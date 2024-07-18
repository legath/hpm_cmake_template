cmake_minimum_required(VERSION 3.24)
include(conf.cmake)

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

if (USE_DSPLIB)
    set(RV_ARCH "rv32imafc")
    set(RV_ABI "ilp32f")
    set(CONFIG_HPM_MATH 1)
    set(CONFIG_HPM_MATH_DSP 1)
    set(HPM_MATH_DSP_SES_LIB "libdspf")
    message(STATUS "Using DSP Lib")
endif ()


if (USE_MOTORCTL)
    set(CONFIG_MOTORCTRL 1)
    message(STATUS "Using Motor control")
endif ()

if (USE_ADC)
    set(CONFIG_HPM_ADC 1)
    message(STATUS "Using ADC")
endif ()

SET(APP_NAME hpm_playground)
project(hpm_playground)

SET(CMAKE_VERBOSE_MAKEFILE TRUE)

set(ENV{HPM_SDK_BASE} ${PROJECT_SOURCE_DIR}/hpm_sdk/)
set(ENV{HPM_SDK_TOOLCHAIN_VARIANT} nds-gcc)
set(BOARD hpm6200evk)
SET(HPM_BUILD_TYPE flash_xip)


find_package(hpm-sdk REQUIRED HINTS $ENV{HPM_SDK_BASE})


set(CMAKE_C_STANDARD 11)

sdk_app_src(main.c)
if (USE_DSPLIB)
    sdk_ld_options(-L$ENV{HPM_SDK_BASE}/middleware/hpm_math/nds_dsp/gcc/)
endif ()