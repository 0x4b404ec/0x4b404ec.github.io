+++
title = 'Unity PlayableDirector正播倒播处理'
date = 2023-12-31T19:57:41+08:00
draft = false

+++

# Unity PlayableDirector(Timeline) 正播倒播处理

> 项目要求控制Timeline的播放状态，官方给出的方案只有正播的处理，并没有倒播的接口。
>
> 而且网上搜索的一些方案都是使用[协程](https://so.csdn.net/so/search?q=协程&spm=1001.2101.3001.7020)，但是协程在处理中途暂停继续播放上比较难处理。
>
> 所以自己随便写了一个。处理上比较傻瓜。



## 下载DoTween插件

略...

## TimelineHelper.cs

主要是提供了两个静态接口，用来实现挂TimelineDirector组件

```c#
using UnityEngine;
using UnityEngine.Playables;

public static class PlayableExpansion
{
    /// <summary>
    /// 为director添加自定义控制器
    /// </summary>
    /// <param name="director"></param>
    /// <returns></returns>
    public static PlayableController AddPlayableController(this PlayableDirector director)
    {
        return director.gameObject.AddComponent<PlayableController>();
    }
 
    /// <summary>
    /// 为obj添加自定义控制器
    /// </summary>
    /// <param name="obj"></param>
    /// <returns></returns>
    public static PlayableController AddPlayableController(this GameObject obj)
    {
        return obj.AddComponent<PlayableController>();
    }
}
	
```

## TimelineDirector.cs

Timeline的相关控制封装，没有用官方播放的API，只用到了PlayableDirector时间和采样(PlayableDirector.time和PlayableDirector.Evaluate())

```c#
using DG.Tweening;
using Sirenix.OdinInspector;
using System;
using UnityEngine;
using UnityEngine.Playables;


[RequireComponent(typeof(PlayableDirector))]
public class PlayableController : MonoBehaviour
{
    #region ENUM

    public enum Status
    {
        NULL,
        PLAYING,
        PAUSED,
        STOPPED,
    }

    public enum Direction
    {
        NULL,
        FORWARD,
        BACKWARD
    }

    #endregion

    [SerializeField] private PlayableDirector m_playableDirector;

    /// <summary>
    /// 当前播放状态(如果用不到可是删除，现在这个字段只是一个状态的记录)
    /// </summary>
    public Status CurrentPlayStatus { get; private set; } = Status.NULL;

    /// <summary>
    /// 当前播放方向(如果用不到可是删除，现在这个字段只是一个状态的记录)
    /// </summary>
    public Direction CurrentPlayDirection { get; private set; } = Direction.NULL;

    /// <summary>
    /// 当前播放进度
    /// </summary>
    public double FullProgress { get; private set; } = 0d;
    
    /// <summary>
    /// 片段进度
    /// </summary>
    //public double ClipProgress { get; private set; } = 0d;

    /// <summary>
    /// 当前播放进度归一化
    /// </summary>
    public double CurrentNormalizedProgress => FullProgress / Duration;
    [Range(0f, 5f)] public float PlaySpeed = 1f;

    /// <summary>
    /// 播放模式
    /// </summary>
    public WrapMode WrapMode = WrapMode.Once;

    /// <summary>
    /// 开始播放事件, 返回时 时间点，和触发时方向
    /// </summary>
    public Action<double, Direction> OnPlay;

    /// <summary>
    /// 暂停播放事件, 返回时 时间点，和触发时方向
    /// </summary>
    public Action<double, Direction> OnPause;

    /// <summary>
    /// 停止播放事件, 返回时 时间点，和触发时方向
    /// </summary>
    public Action<double, Direction> OnStop;

    /// <summary>
    /// 继续播放事件, 返回时 时间点，和触发时方向
    /// </summary>
    public Action<double, Direction> OnContinue;

    /// <summary>
    /// 当播放自动停止，既本次的ProgressExecute播放到指定事件点时触发；
    /// </summary>
    public Action<double> OnAutoStop;

    public Action<double> OnUpdate;

    /// <summary>
    /// Timeline长度
    /// </summary>
    public double Duration { get; private set; } = -1f;

    private Tweener m_timeTween;

    public bool TweenActive => m_timeTween.IsActive();

    private void Awake()
    {
        m_playableDirector = GetComponent<PlayableDirector>();
        m_playableDirector.playOnAwake = false;
        Duration = m_playableDirector.duration;
        CurrentPlayStatus = Status.STOPPED;
    }

    /// <summary>
    /// 继续播放
    /// </summary>
    [Button]
    public void Continue()
    {
        if (m_timeTween.IsActive())
        {
            CurrentPlayStatus = Status.PLAYING;
            m_timeTween.Play();
        }
        OnContinue?.Invoke(FullProgress, CurrentPlayDirection);
    }

    /// <summary>
    /// 从暂停时间点正向播放, 应用在倒播中途暂停后切换为正播
    /// </summary>
    [Button]
    public void ContinuePlayForwardByPausePoint()
    {
        CurrentPlayStatus = Status.PLAYING;
        CurrentPlayDirection = Direction.FORWARD;

        m_timeTween.Kill();
        ProgressExecute(Duration);

        OnContinue?.Invoke(FullProgress, CurrentPlayDirection);
    }


    /// <summary>
    /// 从暂停时间点反向播放, 应用在正播中途暂停后切换为倒播
    /// </summary>
    [Button]
    public void ContinuePlayBackwardByPausePoint()
    {
        CurrentPlayStatus = Status.PLAYING;
        CurrentPlayDirection = Direction.BACKWARD;

        m_timeTween.Kill();
        ProgressExecute(0);

        OnContinue?.Invoke(FullProgress, CurrentPlayDirection);
    }

    public void PlayClip(double start, double stop)
    {
        if (start == stop) return;

        CurrentPlayStatus = Status.PLAYING;
        CurrentPlayDirection = start < stop ? Direction.FORWARD : Direction.BACKWARD;

        m_timeTween.Kill();
        FullProgress = start;
        ProgressExecute(stop);

        OnPlay?.Invoke(start, CurrentPlayDirection);
    }


    /// <summary>
    /// 从开始播放
    /// </summary>
    [Button]
    public void PlayForward()
    {
        CurrentPlayStatus = Status.PLAYING;
        CurrentPlayDirection = Direction.FORWARD;

        m_timeTween.Kill();
        FullProgress = 0d;
        ProgressExecute(Duration);

        OnPlay?.Invoke(FullProgress, CurrentPlayDirection);
    }

    /// <summary>
    /// 从结尾倒放
    /// </summary>
    [Button]
    public void PlayBackward()
    {
        CurrentPlayStatus = Status.PLAYING;
        CurrentPlayDirection = Direction.BACKWARD;

        m_timeTween.Kill();
        FullProgress = Duration;
        ProgressExecute(0);

        OnPlay?.Invoke(FullProgress, CurrentPlayDirection);
    }

    /// <summary>
    /// 暂停播放
    /// </summary>
    [Button]
    public void Pause()
    {
        CurrentPlayStatus = Status.PAUSED;

        m_timeTween.Pause();

        OnPause?.Invoke(FullProgress, CurrentPlayDirection);
    }

    /// <summary>
    /// 停止播放
    /// </summary>
    [Button]
    public void Stop()
    {
        CurrentPlayStatus = Status.STOPPED;

        m_timeTween.Kill();
        FullProgress = 0d;
        m_playableDirector.time = FullProgress;
        m_playableDirector.Evaluate();

        OnStop?.Invoke(FullProgress, CurrentPlayDirection);
    }

    [Button]
    public void JumpTo(double progress)
    {
        // 直接取样
        m_playableDirector.time = progress;
        m_playableDirector.Evaluate();
        FullProgress = progress;

        OnUpdate?.Invoke(progress);
    }

    [Button]
    public void ForceKill()
    {
        m_timeTween.Kill();
        CurrentPlayStatus = Status.PAUSED;
    }

    private void ProgressExecute(double targetProgress)
    {
        // 使用DoTween最当前时间进行线性过渡
        m_timeTween = DOTween.To(() => FullProgress, x => FullProgress = x, targetProgress, PlaySpeed).SetSpeedBased()
            .SetEase(Ease.Linear);
        // 做出限制避免bug
        FullProgress = Clamp(FullProgress, 0d, Duration);
        m_timeTween.OnUpdate(() => { JumpTo(FullProgress); });
        m_timeTween.OnComplete(() =>
        {
            CurrentPlayStatus = FullProgress == Duration ? Status.STOPPED : Status.PAUSED;
            OnAutoStop?.Invoke(FullProgress);
        });
        m_timeTween.Play();
    }

    /// <summary>
    /// 针对Double的Clamp
    /// </summary>
    private static double Clamp(double value, double min, double max)
    {
        if (value < min)
            value = min;
        else if (value > max)
            value = max;
        return value;
    }
}
```

## TimelineTestController

测试类创建一个场景自己制作一个Timeline, 绑定一些按钮。

```c#
using System.Collections;
using System.Collections.Generic;
using Tools;
using UnityEngine;
using UnityEngine.Playables;
using UnityEngine.UI;
 
public class TimelineTestController : MonoBehaviour
{
 
    public PlayableDirector PlayableDirector;
 
    public Button PlayForward;
 
    public Button PlayBackward;
 
    public Button Pause;
 
    public Button Stop;
 
    public Button Continue;
 
    public Button ContinueForward;
 
    public Button ContinueBackward;
 
    public TimelineDirector Director;
 
    public void Start()
    {
        Director = TimelineHelper.CreateTimelineDirector(PlayableDirector);
 
        Director.OnPlay = (t, d) =>
        {
            Debug.Log($"OnPlay time {t} dir {d}");
        };
 
        Director.OnPause = (t, d) =>
        {
            Debug.Log($"OnPause time {t} dir {d}");
        };
 
        Director.OnContinue = (t, d) =>
        {
            Debug.Log($"OnContinue time {t} dir {d}");
        };
 
        Director.OnStop = (t, d) =>
        {
            Debug.Log($"OnStop time {t} dir {d}");
        };
 
        PlayForward.onClick.AddListener(() =>
        {
            Director.PlayForward();
        });
 
        PlayBackward.onClick.AddListener(() =>
        {
            Director.PlayBackward();
        });
 
        Pause.onClick.AddListener(() =>
        {
            Director.Pause();
        });
 
        Stop.onClick.AddListener(() =>
        {
            Director.Stop();
        });
 
        Continue.onClick.AddListener(() =>
        {
            Director.Continue();
        });
 
        ContinueForward.onClick.AddListener(() =>
        {
            Director.ContinuePlayForwardByPausePoint();
        });
 
        ContinueBackward.onClick.AddListener(() =>
        {
            Director.ContinuePlayBackwardByPausePoint();
        });
    }
}
```
