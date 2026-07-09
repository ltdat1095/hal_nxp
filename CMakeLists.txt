# MobTurret: hal_nxp module CMakeLists
#
# Restores the top-level CMakeLists.txt that the build expects when a
# module declares `cmake: .` in its module.yml. The upstream
# `zephyrproject-rtos/hal_nxp` repo removed this file during a
# restructuring. Without it, the build cannot find the module's CMake
# integration.
#
# This file:
# 1. Adds the SDK include directories needed for the i.MX93 M33 soc to
#    compile (MIMX9352 + MIMX9301 device families, periph/ for
#    PERI_*.h, common/ for fsl_common.h, plus every peripheral driver
#    subdir under mcux-sdk-ng/drivers/).
# 2. Adds the SDK source files for the i.MX93 M33 minimal build path:
#    fsl_clock.c (clock driver) and fsl_lpuart.c (LPUART peripheral).
#    These provide the symbols (CLOCK_EnableClock, LPUART_Init, etc.)
#    that Zephyr's HAL drivers consume.
# 3. Adds the zephyr/src subdirectory (wireless / RW61x source files
#    preserved from the NXP-downstream era).
#
# Full SDK integration (imx/, s32/, mcux/ add_subdirectory with their
# HAS_* guards, plus per-driver source-file lists) is a separate task.
# For the i.MX93 M33 minimal hello world we just need headers + the
# console + clock + LPUART driver wiring.
#
# Copyright (c) 2021 Linaro, Limited
# Copyright 2024-2026 NXP
# SPDX-License-Identifier: Apache-2.0

set(_mobturret_sdk_root ${CMAKE_CURRENT_LIST_DIR}/mcux/mcux-sdk-ng)

# Auto-discover every peripheral subdir that ships an fsl_*.h header.
# The SDK has 400+ such subdirs under drivers/, each one home to a
# peripheral's public C API (fsl_clock.h, fsl_rgpio.h, fsl_lpuart.h, …).
# The clean way to make all of them includable via the bare `#include
# "fsl_X.h"` form is to glob them and add each as an include dir.
file(GLOB _mobturret_driver_dirs
     LIST_DIRECTORIES true
     "${_mobturret_sdk_root}/drivers/*/")

# Per-device SDK include dirs:
# - MIMX9352 is the SoC variant (MIMX9352DVVXM) used by i.MX93 EVK; the
#   soc.c does `#include <fsl_clock.h>` from MIMX9352/drivers/.
# - MIMX9301 is the broader device family that includes MIMX9352; it
#   has its own fsl_device_registers.h that we keep on the path for any
#   code that includes the bare device header.
# - periph/ holds the PERI_*.h peripheral headers (MIMX9352_cm33.h
#   includes these via `#include "PERI_ADC.h"` etc.).
# - common/ holds fsl_common.h, fsl_tickless.h etc. used by fsl_clock.h
#   and other MIMX9352 drivers.
zephyr_include_directories(
  ${_mobturret_sdk_root}/devices/i.MX/i.MX93/MIMX9352
  ${_mobturret_sdk_root}/devices/i.MX/i.MX93/MIMX9352/drivers
  ${_mobturret_sdk_root}/devices/i.MX/i.MX93/MIMX9301
  ${_mobturret_sdk_root}/devices/i.MX/i.MX93/MIMX9301/drivers
  ${_mobturret_sdk_root}/devices/i.MX/i.MX93/periph
  ${_mobturret_sdk_root}/drivers/common
  ${_mobturret_sdk_root}
  ${_mobturret_driver_dirs}
)

# SDK source files. The SDK ships the C implementations of every
# peripheral function; Zephyr's HAL drivers consume them via header
# declarations and rely on the linker to find them. Without these,
# the link fails with "undefined reference to LPUART_Init" etc.
#
# For the i.MX93 M33 minimal hello world we only need the i.MX93 SoC
# clock driver and the LPUART peripheral. Other peripherals can be
# added later as their use cases arise (matching the CONDITIONs used
# in NXP-downstream's per-SoC CMakeLists trees).
zephyr_sources(
  ${_mobturret_sdk_root}/devices/i.MX/i.MX93/MIMX9352/drivers/fsl_clock.c
  ${_mobturret_sdk_root}/drivers/lpuart/fsl_lpuart.c
)

# Subdirs preserved from the NXP-downstream tree.
add_subdirectory(${ZEPHYR_CURRENT_MODULE_DIR}/zephyr/src hal_nxp)