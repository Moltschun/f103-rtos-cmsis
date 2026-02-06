# STM32F103 RTOS(FreeRTOS) & CMSIS Learning Path

![STM32](https://img.shields.io/badge/STM32-03234B?style=for-the-badge&logo=stmicroelectronics&logoColor=white)
![FreeRTOS](https://img.shields.io/badge/FreeRTOS-darkgreen?style=for-the-badge&logo=freertos&logoColor=white)
![C](https://img.shields.io/badge/C-00599C?style=for-the-badge&logo=c&logoColor=white)
![PlatformIO](https://img.shields.io/badge/PlatformIO-24292E?style=for-the-badge&logo=platformio&logoColor=orange)
![VSCode](https://img.shields.io/badge/VS_Code-007ACC?style=for-the-badge&logo=visual-studio-code&logoColor=white)

This repository documents my learning journey with **FreeRTOS** and **CMSIS** drivers on the **STM32F103** microcontroller.

> [!IMPORTANT]
> I am a student transitioning into the embedded systems field. This project serves as a personal knowledge base and a practical guide for low-level development.


## What is Real-Time Operating System (RTOS)
> [!NOTE]
> What is a Real-Time Operating System (RTOS)? It is an OS specifically engineered for embedded systems where
> predictability and timing precision are critical (a concept known as determinism).

In simpler terms, an RTOS provides a robust framework of services that streamlines the development of complex, multitasking applications by ensuring that high-priority tasks are executed exactly when required.

---

## Why do you need an RTOS
If you've worked on projects that can perform seveal different tasks(e.g., reading sensors, updating a display, and controlling servos), you've likely wanted to make your code more efficient: allocate less screen time to certain tasks or simply reduce the amount of time in the main `while(1)` loop. Trying to do this, you've likely just filled up the `while(1)` loop to such an extent that it becomes unreadable and difficult to maintain. What if one task can take a long time and simply consumes the execution time of another? This makes it difficult to guarantee the execution of critical tasks. 

An RTOS solves all of the above preblems. It provides us with:
> [!TIP]
> 1. **Multitasking** - Running several tasks in "parallel" by fast context switching.
> 2. **Prioritization** - Ensuring that critical tasks always interrupt less important ones.
> 3. **Synchronization and data sharing** - Mechanisms (Mutexes, Semaphores, Queues) for safe data sharing between tasks.
> 4. **Time management** - Time Management: Precise control over task execution and timing.
>
--- 

## Key Concepts in RTOS

# **Task**
> [!NOTE]
> This is the fundamental unit of an RTOS. It's essentially a while(1) loop, but it performs a single task/action.
What's also important to know:
1. Priorities are numerical values ​​that determine the importance of a task. The higher the priority, the sooner the task will be executed.
2. The stack is a separate memory area for local variables and function context.
3. Task states exist in four types: Running (a task that is already running on the CPU), Ready (a task that is ready to be executed as soon as the CPU becomes available), Blocked (a task that is temporarily blocked because an event has not yet occurred), and Suspended (a task that has been explicitly suspended by another task or by the scheduler itself).

---

# **Scheduler**
> [!NOTE]
> This is one of the important components of the OS. It manages the tasks that we set ourselves, determining which of them will receive processor time at the moment.

How does the Scheduler know when it's time to switch tasks? It needs his own metronome.
> [!NOTE]
> **SysTick (System Tick)** is a hardware timer (in the Cortex-M core) that generates an interrupt with a fixed
> frequency (usually 1000 Hz, i.e. once every 1 ms).

It's also worth talking about switching between tasks. 
Each task has its own **Own Stack (Task Stack)**. 
Initially, the scheduler saves/copies the contents of the processor registers to the stack of the current task. Then it takes the data from the stack of the next task and loads it back into the processor registers. If we return to the previous task, the processor will continue to perform the task exactly from the place where it was interrupted.

And the last thing worth talking about: The main points related to priorities.
> [!TIP]
> 1. We assign a number (priority) to each task. The scheduler **always** selects the highest priority task that is in the >**Ready** state. If `Task_A (Prio 2)` is ready and `Task_B (Prio 1)` is ready, the processor will receive `Task_A`.
>
> 2. If a task with a low priority is running, and suddenly a task with a high priority "wakes up", the low task ** is
> immediately** preempted. They don't let her finish her work.
>
> 3. If two tasks have the same priority, the scheduler allows them to work in turn. Each receives one quantum of time
> (usually 1 tick = 1 ms).
>
