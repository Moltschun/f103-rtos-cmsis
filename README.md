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
