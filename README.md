# FreeRTOS-Pico-Template

Getting [FreeRTOS](https://freertos.org/) running on my Pi Pico took a few days.
Hopefully this can save some time in my later projects.

## Project Structure

```text
/
|
|___/src                    // Main src code directoru
|   
|___/include
|   |___FreeRTOSConfig.h    // FreeRTOS project config file
|
|___/external
|       |
|       \ FreeRTOS-Kernel   // FreeRTOS kernel files, included as a submodule (source: https://github.com/raspberrypi/FreeRTOS-kernel )
|
|
|___.gitmodules             // Definition for Submodules (FreeRTOS)
|___install.txt             // Description on how to manually add submodule
|___CMakeLists.txt          // Top-level project CMake config file
|___pico_sdk_import.cmake   // Raspberry Pi Pico SDK CMake import script
|
|___README.md               // This file
|___LICENSE.md              // MIT License
```

## Usage

Only testet for Windows.

1. Clone the repo https://github.com/RonnyA/FreeRTOS-Pico-Template.git
2. Enter repo: `cd FreeRTOS-Pico-Template`
3. Prepare the submodules: `git submodule update --init --recursive`
4. Start Visual Studio code: `code .`
5. Install the official [Raspberry Pi Pico extension](https://marketplace.visualstudio.com/items?itemName=raspberry-pi.raspberry-pi-pico)
6. In Visual Studio Code press "F1", then choose "Cmake: Delete Cache and Reconfigure"
7. In Visual Studio Code press "F1", then choose "Cmake: Clean rebuild"
8. Deploy code using UF2 file or "Flash project" using PI Pico Extension.

## Changes for your new copy

In CmakeLists.txt rename all places where "TemplateApp" is found to "YourNewShinyApp" (or whatever)

I would have prefered to set the ${NAME} variable and reference it, but sedly then FLASH function fails. (Bug/Cant handle variables)

## Output

Running the program should give you this output

```text

Starting main_blinky.
FreeRTOS version: 11.1.0
Free Heap: 0 bytes
Minimum Ever Free Heap: 0 bytes
Task Name    State    Priority    Stack    Task ID
----------------------------------------------------
BlinkyTask   0        2          118      1
```
