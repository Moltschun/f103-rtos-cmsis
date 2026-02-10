# Scheduler

In classical microcontroller programming (Bare Metal) You managed the execution flows yourself. You wrote functions, called them sequentially inside `while(1)`and we hoped that the interruptions didn't ruin the timing. In RTOS, you will give this control to the "Scheduler".

---

## 1. Paradigm Shift: Super Loop vs Scheduler

You're used to linear thinking. The scheduler introduces the concept of **pseudo-parallelism**.

> [!NOTE]
> Let's discuss the main principle of RTOS.
>
> **Determinism**. The system ensures that a high-priority task is managed at a strictly defined time, regardless of what the processor is currently doing.

---

## 2. The mechanics of time

How does the Scheduler know when it's time to switch tasks? He needs a metronome.

**SysTick (System Tick)** is a hardware timer (in the Cortex-M core) that generates an interrupt with a fixed frequency (usually 1000 Hz, i.e. 1 every 1 ms).

1. **Event:** 1 ms passes.
    
2. **Interruption:** The processor stops executing the current instruction.
    
3. **Log in to the Scheduler:** The FreeRTOS kernel code ('xPortSysTickHandler') is running.
    
4. **Solution:** The planner looks at: "Is there a task more important than the current one? Or has the current task expired?".
    
5. **Action:** If yes, a context switch occurs.
    

---

## 3. Context Switch

This is the "magic" that allows one task to disappear and another to appear as if nothing had happened.

Each task has its own **Own Stack (Task Stack)**. It's her personal memory.

**It's worth talking about the switching algorithm:**

1. **Save (Save):** The scheduler copies the contents of the processor registers (R0-R15, xPSR — the "brain state" of the CPU) to the stack of the current task.
    
2. **Download (Restore):** The scheduler takes data from the stack of the next task and loads it back into the processor registers.
    
3. **Refund:** The processor continues to perform a new task exactly from the place where it was interrupted.
    

> [!WARNING]
> Switching price
> Context Switch is not free. It takes CPU time (about 100-300 clock cycles per Cortex-M3). If you switch too often (for example, a tick every 10 microseconds), the processor will only do the switching, not the work.

---

## 4. The selection algorithm

FreeRTOS uses the **Fixed Priority Preemptive Scheduling with Time Slicing** strategy.

Let's analyze this term in parts.:

### A. Fixed Priority

You assign a number (priority) to each task. The scheduler **always** selects the highest priority task that is in the **Ready** state.

- If `Task_A (Prio 2)` is ready and `Task_B (Prio 1)` is ready, the processor will receive `Task_A`.
    

### B. Preemptive (Displacing)

If a task with a low priority is running, and suddenly "wakes up" (for example, an interrupt has arrived or the timer has expired) a task with a high priority, the low task **is immediately** ousted. They don't let her finish her work. Power changes instantly.

### C. Time Slicing (Time Quantization / Round Robin)

What if two tasks have the same priority?

The scheduler lets them take turns working. Each receives one quantum of time (usually 1 tick = 1 ms).

- `Task_A` is running for 1 ms.
    
- Switching.
    
- `Task_B` is running for 1 ms.
    
- Switching.
