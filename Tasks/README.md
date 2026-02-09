# Tasks 

> [!NOTE]
> An RTOS task is, at its core, an infinite function that contains logic to perform a specific part of the application's functionality. Each task is performed **independently** of other tasks, but their execution is coordinated by the RTOS scheduler.

--- 

> Tasks, sometimes called Threads, are the central and most fundamental concept in any Real-Time Operating System (RTOS). These are the basic execution units into which the entire application is divided in an RTOS system. In fact, each task is an independent software entity that performs a certain part of the system's work.

## The main properties of each task:

1. **Priority:** A numeric value indicating the importance of the task. The scheduler will always give preference to the task with a higher priority.
    
2. **Stack (Stack):** Each task has its own memory stack. It is used to store local variables of the task function, return addresses when calling functions, and to save the CPU context when switching tasks.
    
3. **State:** The task is always in one of several predefined states (Running, Ready, Blocked, Suspended).
    
4. **Task Code (Task Function):** The function itself, which implements the logic of the task, usually containing an infinite loop `while(1)'.

---

## Task States

Understanding the task states is critical for debugging and system design.:

- **Running (In progress):** The task that is currently using the CPU. Only one task can be in this state on a single-core system.
    
- **Ready (Ready for execution):** A task that can be completed (it is not waiting for any resource or timeout) and has a high enough priority, but at the moment the CPU is busy with another task (with the same or higher priority).
    
- **Blocked:** A task that is temporarily suspended because it is waiting for some event. It cannot be selected by the scheduler for execution, even if the CPU is free. Examples:
    
    - Waiting for timeout (`vTaskDelay()`).
        
    - Waiting for data from the queue.
        
    - Waiting for the mutex or semaphore to be released.
        
    - Waiting for an external event.
        
- **Suspended:** A task that has been explicitly suspended by another task or by the scheduler itself. It will remain in this state until another task calls a command to "resume" it. Unlike Blocked, Suspended tasks cannot be woken up by timeout or other events.

- <img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/8f349154-1b7d-4303-95a0-2931b797c2e9" />
