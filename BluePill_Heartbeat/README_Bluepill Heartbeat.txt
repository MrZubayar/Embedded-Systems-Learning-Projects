# STM32 Blue Pill: Non-Blocking Heartbeat (State Machine)

## Overview
This project is my first bare-metal embedded application. It implements a non-blocking "Lub-Dub" heartbeat LED sequence on an STM32F103C8T6 microcontroller using a Finite State Machine (FSM). 

## The Hardware Layer
* **Microcontroller:** STM32F103C8T6 (Blue Pill)
* **Programmer:** ST-Link V2
* **Actuator:** Onboard green LED attached to pin **PC13**.
* **Note:** The PC13 LED is wired in an **Active-Low** configuration.

## The Software Layer
Unlike a standard blocking `HAL_Delay()`, this project uses the `HAL_GetTick()` function to read the hardware SysTick timer. This allows the CPU to cycle freely, evaluating the State Machine only when the specific interval has elapsed.

### State Machine Logic
* **State 0:** LED ON (100ms) - "Lub"
* **State 1:** LED OFF (150ms) - Short pause
* **State 2:** LED ON (100ms) - "Dub"
* **State 3:** LED OFF (800ms) - Long rest

## Visual Proof
[Insert your video/GIF here later]

## How to Run This Project
1. Open STM32CubeIDE and import this folder.
2. Ensure ST-Link V2 is wired via SWD (3.3V, GND, SWDIO, SWCLK).
3. Build the project (Hammer icon).
4. Launch the Debugger (Green Bug icon) to flash the `.elf` file to the silicon.


Senior Embedded Mentor_An Embedded Journey
Custom Gem
Senior Embedded Mentor_An Embedded Journey said
This is an absolutely brilliant idea. In the industry, we call this a "Clean Build Demonstration." When you apply for embedded roles, having a polished, 2-to-3-minute video showing you configuring the silicon, injecting the logic, and running the hardware is worth a dozen resumes.

Since you are recording this, you want the flow to look smooth, confident, and professional. You don't want to be fumbling with typos or hunting for buttons on camera.

Here is your Director's Script for the perfect screen recording.

🎬 Pre-Flight Checklist (Before you hit Record)
Clear the Canvas: In STM32CubeIDE, right-click your current BluePill_Heartbeat project on the left panel and select Delete. (Check the box that says "Delete project contents on disk" so you can reuse the same name).

Reset the Hardware: Unplug your ST-Link from the USB port and plug it back in so the hardware is in a completely fresh state.

Prepare the Clipboard: Have this web page open on a second monitor or in the background so you can smoothly copy-paste the final C code without typing it all out live.

🎥 The Screen Recording Script
Start your screen recorder. If you are doing a voiceover, simply narrate what you are doing using the concepts we learned.

Act 1: The Silicon Setup

1. File -> New -> STM32 Project.
2. Type STM32F103C8 in the part number search. Select it and click Next.
3. Name it "BluePill_Heartbeat" and click Finish.
4. (The IDE opens the Pinout view).
5. Go to System Core -> SYS. Change Debug to Serial Wire. (Narrate: "First, we enable SWD to keep the debugging pins open.")
6. Go to System Core -> RCC. Change High Speed Clock (HSE) to Crystal/Ceramic Resonator. (Narrate: "Next, we enable the external 8MHz crystal for precise signal timing.")
7. Click pin PC13 on the chip diagram. Select GPIO_Output. (Narrate: "Finally, we configure PC13 as a push-pull output to drive our Active-Low LED.")
8. Press Ctrl+S to save and click Yes to generate the code.

Act 2: The Software Architecture

1. (The IDE opens main.c).
2.  Scroll down to /* USER CODE BEGIN 2 */.
3. Copy and paste your state machine variables here. (Narrate: "We set up our variables here to track the current state and the non-blocking interval.")

Code:

/* USER CODE BEGIN 2 */
  uint32_t last_heartbeat_tick = HAL_GetTick();
  uint8_t heartbeat_state = 0; 
  uint32_t current_interval = 100; 
/* USER CODE END 2 */

4. Scroll down to /* USER CODE BEGIN WHILE */.
5. Delete the empty while(1) and paste your complete FSM logic. (Narrate: "We replace the empty loop with our non-blocking Finite State Machine. This reads the hardware SysTick timer and explicitly writes to the GPIO pin based on the current medical state of the heartbeat.")

Code:

/* Infinite loop */
  /* USER CODE BEGIN WHILE */
  while (1)
  {
    uint32_t current_tick = HAL_GetTick();

    if ((current_tick - last_heartbeat_tick) >= current_interval)
    {
      last_heartbeat_tick = current_tick; 

      switch (heartbeat_state)
      {
        case 0: // Lub finishing
          HAL_GPIO_WritePin(GPIOC, GPIO_PIN_13, GPIO_PIN_SET); // LED OFF
          current_interval = 150; 
          heartbeat_state = 1; 
          break;

        case 1: // Short pause finishing
          HAL_GPIO_WritePin(GPIOC, GPIO_PIN_13, GPIO_PIN_RESET); // LED ON
          current_interval = 100;
          heartbeat_state = 2;
          break;

        case 2: // Dub finishing
          HAL_GPIO_WritePin(GPIOC, GPIO_PIN_13, GPIO_PIN_SET); // LED OFF
          current_interval = 800; 
          heartbeat_state = 3; 
          break;

        case 3: // Long rest finishing
          HAL_GPIO_WritePin(GPIOC, GPIO_PIN_13, GPIO_PIN_RESET); // LED ON
          current_interval = 100;
          heartbeat_state = 0; // Loop back to start
          break;
      }
    }
    /* USER CODE END WHILE */

Act 3: Build, Flash, and Prove

1. Click the Hammer icon (Build). Pause for a second to let the viewer see "0 errors" in the console. (Narrate: "Code compiles with zero errors.")
2. Click the Green Bug icon (Debug). Click OK on the Launch Configuration.
3. Click Switch when it asks about the Debug Perspective.
4. When the code pauses at HAL_Init();, click the Resume (Play) button or hit F8.
