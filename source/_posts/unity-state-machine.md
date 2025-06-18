---
title: Unity状态机
date: 2025-06-18 14:14:51
tags: ['Unity', '状态机']
---

[B站视频](https://www.bilibili.com/video/BV1GY41137m8)

单个状态实现IState接口，只需要维护自身逻辑，状态切换通过状态机触发。
```cs
// Scripts/State Machine System/Base/IState.cs
public interface IState
{
    void Enter();
    void Exit();
    void LogicUpdate();
    void PhysicsUpdate();
}
```

状态机继承StateMachine
```cs
// Scripts/State Machine System/Base/StateMachine.cs
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class StateMachine : MonoBehaviour
{
    IState currentState;

    public Dictionary<System.Type, IState> stateTable;

    void Update()
    {
        currentState.LogicUpdate();
    }

    void FixedUpdate()
    {
        currentState.PhysicsUpdate();
    }

    public void SwitchOn(IState newState)
    {
        currentState = newState;
        currentState.Enter();
    }

    public void SwitchOn(System.Type newStateType)
    {
        currentState = stateTable[newStateType];
        currentState.Enter();
    }

    public void SwitchState(IState newState)
    {
        currentState.Exit();
        SwitchOn(newState);
    }

    public void SwitchState(System.Type newStateType)
    {
        currentState.Exit();
        SwitchOn(newStateType);
    }
}
```

每个状态都继承PlayerState，实现自己的逻辑。
```cs
// Scripts/State Machine System/Player States/PlayerState.cs
using UnityEngine;

public class PlayerState : ScriptableObject, IState
{
    [SerializeField]
    string stateName;

    [SerializeField, Range(0f, 1f)]
    float transitionDuration = 0.1f;

    float stateStartTime;

    int stateHash;

    protected float currentSpeed;
    protected Animator animator;
    protected PlayerController player;
    protected PlayerInput input;
    protected PlayerStateMachine stateMachine;

    protected bool IsAnimationFinished =>
        StateDuration >= animator.GetCurrentAnimatorStateInfo(0).length;

    protected float StateDuration => Time.time - stateStartTime;

    public void Initialize(
        Animator animator,
        PlayerController player,
        PlayerInput input,
        PlayerStateMachine stateMachine
    )
    {
        this.animator = animator;
        this.player = player;
        this.input = input;
        this.stateMachine = stateMachine;
    }

    void OnEnable()
    {
        stateHash = Animator.StringToHash(stateName);
    }

    public virtual void Enter()
    {
        animator.CrossFade(stateHash, transitionDuration);
        stateStartTime = Time.time;
    }

    public virtual void Exit() { }

    public virtual void LogicUpdate() { }

    public virtual void PhysicsUpdate() { }
}
```


```cs
// Scripts/State Machine System/Player States/PlayerState_Idle.cs
using UnityEngine;
using UnityEngine.InputSystem;

[CreateAssetMenu(fileName = "PlayerState_Idle", menuName = "Data/StateMachine/PlayerState/Idle")]
public class PlayerState_Idle : PlayerState
{
    [SerializeField]
    float deceleration = 5f;

    public override void Enter()
    {
        base.Enter();

        currentSpeed = player.MoveSpeed;
    }

    public override void LogicUpdate()
    {
        if (input.Move)
        {
            stateMachine.SwitchState(typeof(PlayerState_Run));
        }

        if (input.Jump)
        {
            stateMachine.SwitchState(typeof(PlayerState_Jump));
        }

        if (!player.IsGrounded)
        {
            stateMachine.SwitchState(typeof(PlayerState_Fall));
        }

        currentSpeed = Mathf.MoveTowards(currentSpeed, 0f, deceleration * Time.deltaTime);
    }

    public override void PhysicsUpdate()
    {
        player.SetVelocityX(currentSpeed * player.transform.localScale.x);
    }
}
```

```cs
// Scripts/State Machine System/Player States/PlayerStateMachine.cs
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class PlayerStateMachine : StateMachine
{
    [SerializeField]
    PlayerState[] states;
    protected Animator animator;
    PlayerController player;
    protected PlayerInput input;

    private void Awake()
    {
        animator = GetComponentInChildren<Animator>();

        input = GetComponent<PlayerInput>();

        player = GetComponent<PlayerController>();

        stateTable = new Dictionary<System.Type, IState>(states.Length);

        foreach (PlayerState state in states)
        {
            state.Initialize(animator, player, input, this);
            stateTable.Add(state.GetType(), state);
        }
    }

    void Start()
    {
        SwitchOn(typeof(PlayerState_Idle));
    }
}
```