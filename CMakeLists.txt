# MobTurret: hal_nxp module CMakeLists
#
# Restores the top-level CMakeLists.txt that the build expects when a
# module declares `cmake: .` in its module.yml. The upstream
# `zephyrproject-rtos/hal_nxp` repo removed this file during a
# restructuring. Without it, the build cannot find the module's CMake
# integration.
#
# For now this:
# 1. Adds the SDK include directories (MIMX9352 + MIMX9301 device
#    families, with their drivers/ subdirs) so fsl_device_registers.h
#    and fsl_clock.h are findable for the i.MX93 soc.h files.
# 2. Adds the zephyr/src subdirectory (wireless / RW61x source files
#    preserved from the NXP-downstream era).
#
# Full SDK integration (imx/, s32/, mcux/ add_subdirectory with their
# HAS_* guards, plus the per-driver source lists) is a separate task.
# For the i.MX93 M33 minimal hello world we just need headers + the
# console driver wiring.
#
# Copyright (c) 2021 Linaro, Limited
# Copyright 2024-2026 NXP
# SPDX-License-Identifier: Apache-2.0

# SDK include paths.
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
  ${CMAKE_CURRENT_LIST_DIR}/mcux/mcux-sdk-ng/devices/i.MX/i.MX93/MIMX9352
  ${CMAKE_CURRENT_LIST_DIR}/mcux/mcux-sdk-ng/devices/i.MX/i.MX93/MIMX9352/drivers
  ${CMAKE_CURRENT_LIST_DIR}/mcux/mcux-sdk-ng/devices/i.MX/i.MX93/MIMX9301
  ${CMAKE_CURRENT_LIST_DIR}/mcux/mcux-sdk-ng/devices/i.MX/i.MX93/MIMX9301/drivers
  ${CMAKE_CURRENT_LIST_DIR}/mcux/mcux-sdk-ng/devices/i.MX/i.MX93/periph
  ${CMAKE_CURRENT_LIST_DIR}/mcux/mcux-sdk-ng/drivers/common
  ${CMAKE_CURRENT_LIST_DIR}/mcux/mcux-sdk-ng
)

# Subdirs preserved from the NXP-downstream tree.
add_subdirectory(${ZEPHYR_CURRENT_MODULE_DIR}/zephyr/src hal_nxp)

