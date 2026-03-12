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