cmake_minimum_required(VERSION 3.14)
#FreeRTOS from https://github.com/raspberrypi/FreeRTOS-kernel

set(CMAKE_C_STANDARD 11)
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)


# Initialise pico_sdk from installed location
# (note this can come from environment, CMake cache etc)

# == DO NEVER EDIT THE NEXT LINES for Raspberry Pi Pico VS Code Extension to work ==
if(WIN32)
    set(USERHOME $ENV{USERPROFILE})
else()
    set(USERHOME $ENV{HOME})
endif()
set(sdkVersion 2.0.0)
set(toolchainVersion 13_2_Rel1)
set(picotoolVersion 2.0.0)
set(picoVscode ${USERHOME}/.pico-sdk/cmake/pico-vscode.cmake)
if (EXISTS ${picoVscode})
    include(${picoVscode})
endif()
# ====================================================================================
set(PICO_BOARD pico CACHE STRING "Board type")
set(PICO_PLATFORM rp2040)

# Set default build type to Debug if not specified
set(CMAKE_BUILD_TYPE Debug CACHE STRING "Choose the type of build (Debug or Release)" FORCE)

# Pull in Raspberry Pi Pico SDK (must be before project)
include(pico_sdk_import.cmake)

# Name the project
project(TemplateApp
    LANGUAGES C CXX ASM
    DESCRIPTION "TemplateApp (FreeRTOS runtime)"
)

# Initialise the Raspberry Pi Pico SDK
pico_sdk_init()


#### FreeRTOS #####
set(FREERTOS_KERNEL_PATH "${CMAKE_SOURCE_DIR}/external/FreeRTOS-Kernel")

#set(FREERTOS_PORT GCC_ARM_CM4F CACHE STRING "")
set(FREERTOS_PORT GCC_ARM_CM0 CACHE STRING "")


add_library(freertos_config INTERFACE)

# The config file directory
target_include_directories(freertos_config SYSTEM INTERFACE 
    include 
    ${FREERTOS_KERNEL_PATH}/portable/ThirdParty/GCC/RP2040  # Add this line
)

if ("${PICO_PLATFORM}" STREQUAL "rp2040")
    include(${FREERTOS_KERNEL_PATH}/portable/ThirdParty/GCC/RP2040/FreeRTOS_Kernel_import.cmake)
elseif("${PICO_PLATFORM}" STREQUAL "rp2350-arm-s")
    include(${FREERTOS_KERNEL_PATH}/portable/ThirdParty/GCC/RP2350_ARM_NTZ/FreeRTOS_Kernel_import.cmake)
elseif("${PICO_PLATFORM}" STREQUAL "rp2350-riscv")
    include(${FREERTOS_KERNEL_PATH}/portable/ThirdParty/GCC/RP2350_RISC-V/FreeRTOS_Kernel_import.cmake)
endif()


# Add FreeRTOS Kernel
add_subdirectory(${FREERTOS_KERNEL_PATH} FreeRTOS-Kernel)

# Define the target using the ProjectName variable
add_executable(TemplateApp src/main.c)  # Replace src/main.cpp with your main source file

target_include_directories(TemplateApp PRIVATE
    ${FREERTOS_KERNEL_PATH}/include
    #${FREERTOS_KERNEL_PATH}/portable/GCC/ARM_CM0
    ${FREERTOS_KERNEL_PATH}/portable/ThirdParty/GCC/RP2040  
    ../include  # Path to your FreeRTOSConfig.h file
)


# Link to built libraries
target_link_libraries(TemplateApp LINK_PUBLIC
    pico_stdlib    
    hardware_clocks      # Hardware Clocks Library
    hardware_pio         # Hardware PIO
    freertos_config
    FreeRTOS-Kernel
    FreeRTOS-Kernel-Heap4 
    )
    

#FreeRTOS heap choices
# https://www.freertos.org/Documentation/02-Kernel/02-Kernel-features/09-Memory-management/01-Memory-management
#
# heap_1 - the very simplest, does not permit memory to be freed.
# heap_2 - permits memory to be freed, but does not coalescence adjacent free blocks.
# heap_3 - simply wraps the standard malloc() and free() for thread safety.
# heap_4 - coalescences adjacent free blocks to avoid fragmentation. Includes absolute address placement option.
# heap_5 - as per heap_4, with the ability to span the heap across multiple non-adjacent memory areas.
#
#Notes:
#  heap_1 is less useful since FreeRTOS added support for static allocation.
#  heap_2 is now considered legacy as the newer heap_4 implementation is preferred.

# Enable/disable STDIO via USB and UART
pico_enable_stdio_usb(TemplateApp  0)
pico_enable_stdio_uart(TemplateApp 1)

# Enable extra build products
pico_add_extra_outputs(TemplateApp)



