# Mutex (Mutex - Mutual Exclusion)

> [!NOTE] 
> Which translates as "mutual exclusion", is one of the most important synchronization mechanisms in Real-Time Operating Systems (RTOS) and any multitasking environment. Its main task is **to provide exclusive access to a shared resource**, preventing simultaneous access by multiple tasks, which could lead to a race condition and data corruption.

---

## 1. The essence and Purpose of a Mutex

Imagine that you have several tasks that need to access the same shared resource, such as writing data to a shared global variable. If two tasks try to update the same variable at the same time, it will result in errors.

The mutex acts as a **lock** on this resource:

- **Critical section protection:** The mutex is used to protect **critical sections** — sections of code that access shared resources (global variables, peripherals, data buffers) that should not be accessed simultaneously.
    
- **Exclusive access:** Only one task can "own" a mutex at any given time. As long as a task owns the mutex, no other task can access it.
    
- **Blocking:** If a task tries to retrieve a mutex that is already being held by another task, the first task ** is blocked** (enters the Blocked state) and waits until the mutex is released.
    
- **Preventing race conditions:** This is the main purpose of the mutex. Without it, simultaneous access to shared data can lead to their inconsistent state.
    

---

## 2. How The Mutex Works

The workflow with a mutex usually looks like this:

1. **Creating a mutex:** A mutex is created during RTOS initialization (or at the beginning of work).
    
2. **Getting a mutex (Take/Lock/Acquire):**
    
    - When task `A` wants to access a protected resource, it calls the RTOS function to "receive" the mutex.
        
    - If the mutex is free, task `A` "takes" it (becomes its owner) and continues execution, gaining exclusive access to the resource.
        
    - If the mutex is already held by task `B`, task `A` ** is blocked** until the mutex is released by task `B`. Task `A` can wait indefinitely or with a timeout.
        
3. **Access to the critical section:** As long as task `A` owns the mutex, it safely executes the critical section code, working with a shared resource.
    
4. **Mutex Release (Give/Unlock/Release):**
    
    - After the work with the shared resource is completed, task `A` calls the RTOS function to "release" the mutex.
        
    - If there are other tasks waiting for this mutex, one of them (usually the highest priority) will be unlocked and will be able to receive it.
        

---

## 3. Problems Solved By Mutexes and New Problems

#### a) Solving The Race Condition Problem

Without mutexes, if multiple tasks are trying to change the same global variable or complex data structure at the same time, the final value may be unpredictable due to alternating CPU reads/writes between tasks. The mutex ensures that only one task modifies the data at any one time.

#### b) The Problem Of Priority Inversion

This is one of the most well-known problems encountered when using mutexes.:

- **Scenario:** The low-priority task `L` hijacks the mutex to gain access to the shared resource.
    
- High priority task `H` interrupts `L` and begins execution.
    
- Task `H` is trying to capture the same mutex, but it is occupied by task `L`. Task `H` ** is blocked**.
    
- Now task `L` holds the mutex that 'H` needs.
    
- If the middle priority task `M` (with priority between `L` and `H`) now enters the Ready state, it can displace `L`.
    
- **Result:** Task `H' (the highest priority) waits for `L` to finish working with the mutex, but `L` cannot work on its own because it is being preempted by `M`. Thus, `H` is blocked because of `M`, even though `M` has a lower priority than `H'. Priorities seemed to be inverted.
    
- **Solution:** Most RTOS (for example, FreeRTOS) offer mutexes with **Priority Inheritance**. When a high-priority task is blocked on a mutex that is being held by a low-priority task, the RTOS temporarily raises the priority of the low-priority task to the priority of the pending high-priority task. As soon as a low-priority task releases the mutex, its priority returns to the original one. This allows it to quickly release a resource without being crowded out by medium-priority tasks.
    

#### c) Deadlock (Mutual Blocking)

This is a situation where two or more tasks are endlessly waiting for resources held by each other.

- **Scenario:**
    
    1. Task `A` captures the mutex `X'.
        
    2. Task `B` captures the mutex `Y'.
        
    3. Task `A` tries to capture mutex `Y`, but `Y` is occupied by `B`. Task `A` is blocked.
        
    4. Task `B` tries to capture the mutex `X`, but `X` is occupied by `A`. Task `B` is blocked.
        
- **Result:** Both tasks are permanently blocked.
    
- **Solution:** Design the system in such a way that mutexes are always captured in the same order (for example, always `X` after `Y`, never `Y` after `X`).
    

---

## 4. What is important to understand

1. **Always use:** If more than one task (or ISR) accesses the resource and at least one of them modifies it, **BE SURE to** use a mutex.
    
2. **Protect only what is necessary:** The critical section should be as short as possible. Capture the mutex immediately before accessing the resource and release it immediately after. A long critical section reduces concurrency and increases the risk of priority inversion.
    
3. **Never forget to release:** If a task captures a mutex but does not release it (for example, due to an error, exception, or an infinite loop inside a critical section), this will permanently block all other tasks waiting for that mutex.
    
4. **Mutex and Semaphore**
    
    - **Mutexes** are designed to **protect resources** and prevent race conditions. They have the concept of "ownership" (only the owner can release) and usually solve the problem of priority inversion.
        
    - **Binary semaphores** are used for **signaling** or simple one-to-one synchronization. They have no concept of ownership.
        
5. **ISRs and mutexes:** **ISRs should not attempt to capture mutexes**, as this may lead to mutual locking or violation of determinism. Instead, ISRs should use **ISR-safe semaphores** or **queues** to signal tasks.
    
6. **Timeouts:** When trying to capture a mutex, always specify a timeout to avoid indefinitely locking the task in case the mutex is never released.
    
7. **Deadlock:** Actively design the system to avoid mutual blockages.
