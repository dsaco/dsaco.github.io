---
title: Unity协同程序
date: 2025-06-18 17:26:17
tags: ['Unity', '协同程序', 'Coroutine', 'IEnumerator']
---

延迟函数

```cs
private void OnTriggerEnter2D(Collider2D other)
{
    if (other.TryGetComponent<PlayerController>(out PlayerController player))
    {
        collider.enabled = false;
        // 延迟3秒
        // Invoke("Reset", time: 3f);
        Invoke(methodName: nameof(Reset), time: 3f);

        // 延迟3秒，每2秒调用一次Reset函数
        // InvokeRepeating(methodName: nameof(Reset), time: 3f, repeatRate: 2f);

        // 取消延迟调用
        // CancelInvoke(nameof(Reset));
    }
}
void Reset()
{
    collider.enabled = true;
}
```

协同程序

```cs
using System.Collections;
using UnityEngine;

public class SomeThing : MonoBehaviour
{
    new Collider2D collider;

    WaitForSeconds waitResetTime;

    private void Awake()
    {
        collider = GetComponent<Collider2D>();

        waitResetTime = new WaitForSeconds(3f);
    }

    private void OnTriggerEnter2D(Collider2D other)
    {
        if (other.TryGetComponent<PlayerController>(out PlayerController player))
        {
            collider.enabled = false;
            StartCoroutine(ResetCoroutine());
        }
    }

    void Reset()
    {
        collider.enabled = true;
    }

    IEnumerator ResetCoroutine()
    {
        yield return waitResetTime;
        Reset();
    }
}
```

可关闭之前的协同程序，重新调用
```cs
public void SetJumpInputBufferTimer()
{
    StopCoroutine(nameof(JumpInputBufferCoroutine));
    StartCoroutine(nameof(JumpInputBufferCoroutine));
}

IEnumerator JumpInputBufferCoroutine()
{
    HasJumpInputBuffer = true;
    yield return waitJumpInputBufferTime;

    HasJumpInputBuffer = false;
}
```