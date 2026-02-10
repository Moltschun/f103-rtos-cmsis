# Semaphore

> [!NOTE]
> Is a synchronization mechanism widely used in Real—Time Operating Systems (RTOS) and multitasking environments. It acts as a signaling mechanism or counter, allowing tasks to coordinate their activities, synchronize on events, and manage access to limited resources.

---

## 1. The essence and Purpose of Semaphores

Semaphores can be thought of as "permissions". The task can "take" the permission (token), if it is available, or "give it away". The number of available permissions determines the type of semaphore.

- **Event synchronization:** Main purpose — one task (or ISR) signals another task about an event that has occurred.
    
- **Resource access management (limited):** Control access to resources, the number of which is limited (for example, 3 printers, 5 buffers).
    
- **Have no concept of "ownership":** Unlike mutexes, semaphores are usually not associated with a specific task owner. Any task can "give away" a semaphore, regardless of who "took" it (although in practice it is undesirable to do so).
    

---

## 2. Types of Semaphores

There are two main types of semaphores that a developer should know:

#### a) Binary Semaphore

This is a semaphore that can only take two values: 0 or 1 (free or occupied). It acts as a flag or "key" that is or is not there.
    
- **Operating principle:**
    
    1. **Initialization:** A binary semaphore is created and set to "available" (1).
        
    2. **Receiving (Take/Wait):** The task tries to "take" the semaphore.
        
        - If the semaphore is available (value 1), its value becomes 0, and the task continues execution.
            
        - If the semaphore is unavailable (value 0), the task ** is blocked** until the semaphore becomes available (with timeout or indefinitely).
            
    3. **Giving (Give/Post):** The task (or ISR) "gives away" the semaphore, making it available (the value becomes 1). This unlocks one of the tasks waiting for this semaphore.
        
- **Typical Applications:**
    
    - **Synchronization of the ISR with the task:** The ISR has completed its work and signals to the task that there is data to process.
        
    - **Event alarm:** Task A has completed the operation and signals task B that it can start its work.
        
    - **Simple resource protection:** Can be used as a mutex to protect a resource, but **is NOT recommended**, as it does not solve the problem of priority inversion and has no concept of ownership. **Always use mutexes to protect resources!**
        

#### b) Counting Semaphore

- **What is it:** A semaphore that can take any integer value (counter). It is used to control access to **multiple instances of the same resource**.
    
- **Operating principle:**
    
    1. **Initialization:** A counting semaphore is created with an initial value equal to the maximum number of available resources (for example, 3 if you have 3 printers).
        
    2. **Receiving (Take/Wait):** The task tries to "take" the semaphore.
        
        - If the counter value is > 0, the counter is reduced by 1, and the task gets access to one instance of the resource.
            
        - If the counter value is 0, all resources are occupied, and the task ** is blocked** until any resource is released.
            
    3. **Giving (Give/Post):** The task "returns" the semaphore by incrementing the counter by 1. This indicates that one instance of the resource has become available, and may unblock a pending task.
        
- **Typical Applications:**
    
    - **Buffer Management:** The semaphore can keep track of the number of free buffers. The producer task "gives away" the semaphore when filling the buffer, the consumer task "takes" it when processing the buffer.
        
    - **Restriction of parallel access:** For example, only 3 tasks can work with a specific hardware unit at the same time.
        
    - **Notification of multiple events:** The ISR can "send" the semaphore several times to indicate that several events have occurred.
        

---

## 3. Sending from ISR (Interrupt Service Routine)

As with queues, this is a key scenario.:

- **ISR-safe functions:** Use special "ISR-safe" versions of the functions to "give back" the semaphore (for example, `xSemaphoreGiveFromISR()` in FreeRTOS). These functions are not blocked.
    
- **Switching context:** ISR-safe functions can request a context switch if a "given" semaphore has unblocked a high-priority task. The scheduler can switch to this task immediately after the ISR is completed.
    
- **Important:** ISRs should not attempt to "take" the semaphore, as this may cause the ISR to lock, which is unacceptable.
    
---

**It is also worth telling about possible problems.:**
    
- **Deadlock (Mutual blocking):** As with mutexes, it is possible if multiple semaphores are used incorrectly.
        
- **Starvation (Starvation):** A low-priority task may never receive a resource if it is constantly being hijacked by high-priority ones.
        
- **Skipping events:** If the ISR generates a lot of signals, but the receiving task processes them slowly, and you have a binary semaphore, then some events may be skipped (because the semaphore may be 1, even if many events have occurred). To count events, use a counting semaphore or queue.
