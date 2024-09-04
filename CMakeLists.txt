cmake_minimum_required(VERSION 3.24)


if (DEFINED ENV{GNURISCV_TOOLCHAIN_PATH})
    SET(TOOLCHAIN_DIR $ENV{GNURISCV_TOOLCHAIN_PATH})
else ()
    message(FATAL_ERROR "Please set GNURISCV_TOOLCHAIN_PATH environment value")
endif ()

SET(TOOLCHAIN_BIN_DIR ${TOOLCHAIN_DIR}/bin)
SET(CMAKE_C_COMPILER "${TOOLCHAIN_BIN_DIR}/riscv32-elf-gcc" CACHE INTERNAL "")
SET(CMAKE_CXX_COMPILER "${TOOLCHAIN_BIN_DIR}/riscv32-elf-g++" CACHE INTERNAL "")
SET(CMAKE_ASM_COMPILER "${TOOLCHAIN_BIN_DIR}/riscv32-elf-gcc" CACHE INTERNAL "")
SET(ENV{VERBOSE} 1)

SET(APP_NAME hpm_playground)
project(hpm_playground)
set(PROJECT_ROOT                ${CMAKE_SOURCE_DIR})
list(APPEND CMAKE_MODULE_PATH   ${PROJECT_ROOT}/cmake)

include(kconfig)

if (CONFIG_DSPLIB)
    set(RV_ARCH "rv32imafc")
    set(RV_ABI "ilp32f")
    set(CONFIG_HPM_MATH 1)
    set(CONFIG_HPM_MATH_DSP 1)
    set(HPM_MATH_DSP_SES_LIB "libdspf")
    message(STATUS "Using DSP Lib")
endif ()


if (CONFIG_MOTORCTL)
    set(CONFIG_MOTORCTRL_V2 1)
    message(STATUS "Using Motor control")
endif ()

if (CONFIG_ADC)
    set(CONFIG_HPM_ADC 1)
    message(STATUS "Using ADC")
endif ()

if (CONFIG_USB)
    set(CONFIG_CHERRYUSB 1)
    message(STATUS "Using usb (CherryUSB)")
endif ()

if (CONFIG_SHELL)
    set(CONFIG_CHERRYSH 1)
    if(CONFIG_SHELL_INTERFACE_UART)
        set(CONFIG_CHERRYSH_INTERFACE uart)
    endif ()
    if(CONFIG_SHELL_INTERFACE_USB)
        set(CONFIG_CHERRYSH_INTERFACE usb)
    endif ()
    message(STATUS "Using shell (CherrySH) with interface ${CONFIG_CHERRYSH_INTERFACE}")
endif ()

if (CONFIG_FREERTOS_ENABLE)
    set(CONFIG_FREERTOS 1)
    message(STATUS "Using FreeRTOS")
endif ()

if (CFG_RTT)
    set(CONFIG_SEGGER_RTT 1)
    message(STATUS "Using Segger RTT")
endif ()



#SET(CMAKE_VERBOSE_MAKEFILE TRUE)

set(ENV{HPM_SDK_BASE} ${PROJECT_SOURCE_DIR}/hpm_sdk/)
set(ENV{HPM_SDK_TOOLCHAIN_VARIANT} nds-gcc)

if(CONFIG_BOARD_HPM5300EVK)
    set(BOARD hpm5300evk)
endif ()

if(CONFIG_BOARD_HPM6200EVK)
    set(BOARD hpm6200evk)
endif ()

if(CONFIG_BOARD_HPM6E00EVK)
    set(BOARD hpm6e00evk)
endif ()

SET(HPM_BUILD_TYPE flash_xip)


find_package(hpm-sdk REQUIRED HINTS $ENV{HPM_SDK_BASE})


set(CMAKE_C_STANDARD 11)

sdk_app_src(main.c)
if (CONFIG_DSPLIB)
    sdk_ld_options(-L$ENV{HPM_SDK_BASE}/middleware/hpm_math/nds_dsp/gcc/)
endif ()

#if (CFG_GEN_IDE)
#generate_ide_projects()
#endif ()