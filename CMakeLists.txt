cmake_minimum_required(VERSION 3.20)

# Define project name and supported languages
project(mixer-tutorial LANGUAGES C ASM)

# Specify the ARM toolchain
set(CMAKE_SYSTEM_NAME Generic)
set(CMAKE_SYSTEM_PROCESSOR ARM)
set(ARM_TOOLCHAIN_PATH /usr/local/gcc-arm-none-eabi/gcc-arm-none-eabi-9-2020-q2-update)
set(CMAKE_C_COMPILER ${ARM_TOOLCHAIN_PATH}/bin/arm-none-eabi-gcc)
set(CMAKE_CXX_COMPILER ${ARM_TOOLCHAIN_PATH}/bin/arm-none-eabi-g++)
set(CMAKE_ASM_COMPILER ${ARM_TOOLCHAIN_PATH}/bin/arm-none-eabi-gcc)

# Define build type
if(NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE Debug)
endif()

# Compiler flags for Cortex-M4F (nRF52840)
set(CPU_FLAGS "-mcpu=cortex-m4 -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16")
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${CPU_FLAGS} -Wall -Werror -Wno-unused-variable -Wno-unused-function")
set(CMAKE_ASM_FLAGS "${CMAKE_ASM_FLAGS} ${CPU_FLAGS}")

add_compile_definitions(
        NRF52840_XXAA
        __nRF_FAMILY
        ARM_MATH_CM4
        FLASH_PLACEMENT=1
        CONFIG_GPIO_AS_PINRESET
        ASSERT_WARN_CT=0
        MX_CONFIG_FILE=${CMAKE_CURRENT_SOURCE_DIR}/tutorial/nRF52840/mixer_config.h
        GPI_ARCH_PLATFORM=GPI_ARCH_BOARD_TUDNES_SHEPHERD_NRF52840FRAM_V13
        GPI_TRACE_MODE=GPI_TRACE_MODE_NO_TRACE
        GPI_TRACE_BASE_SELECTION=GPI_TRACE_LOG_STANDARD
        GPI_TRACE_BUFFER_ELEMENTS=64
)

# Include directories
include_directories(
        src
        src/mixer
        src/gpi
        tutorial/nRF52840/CMSIS_4/CMSIS/Include
        tutorial/nRF52840/nRF/CMSIS/Device/Include
)

# Source files
file(GLOB_RECURSE SOURCES
        "src/gpi/*.c"
        "src/gpi/arm/nordic/shepherd_nrf52/*.c"
        "src/mixer/*.c"
        "tutorial/nRF52840/main.c"
)

# Add executable target
add_executable(${PROJECT_NAME}.elf ${SOURCES}
        #tutorial/nRF52840/node_utils.h
        #tutorial/nRF52840/leader.h
        #tutorial/nRF52840/leader.c
        #tutorial/nRF52840/follower.h
        #tutorial/nRF52840/follower.c
        src/gpi/arm/nordic/shepherd_nrf52/platform.c
        #src/gpi/arm/nordic/shepherd_nrf52/stdio.c
)

# Post-build commands to generate HEX and BIN files
add_custom_command(TARGET ${PROJECT_NAME}.elf POST_BUILD
        COMMAND arm-none-eabi-objcopy -O ihex ${PROJECT_NAME}.elf ${PROJECT_NAME}.hex
        COMMAND arm-none-eabi-objcopy -O binary ${PROJECT_NAME}.elf ${PROJECT_NAME}.bin
)
