# MobTurret: hal_nxp module CMakeLists
#
# Restores the top-level CMakeLists.txt that the build expects when a
# module declares `cmake: .` in its module.yml. The upstream
# `zephyrproject-rtos/hal_nxp` repo removed this file during a
# restructuring. Without it, the build cannot find the module's CMake
# integration.
#
# For now this:
# 1. Adds the SDK include directories (mcux-sdk-ng/devices/i.MX/i.MX93/MIMX9301/
#    and .../drivers/) so fsl_device_registers.h is findable for the i.MX93
#    soc headers. This is the minimum needed to compile soc.h files.
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

# SDK include paths (MIMX9352 → MIMX9301 device family)
zephyr_include_directories(
  ${CMAKE_CURRENT_LIST_DIR}/mcux/mcux-sdk-ng/devices/i.MX/i.MX93/MIMX9301
  ${CMAKE_CURRENT_LIST_DIR}/mcux/mcux-sdk-ng/devices/i.MX/i.MX93/MIMX9301/drivers
  ${CMAKE_CURRENT_LIST_DIR}/mcux/mcux-sdk-ng
)

# Subdirs preserved from the NXP-downstream tree.
add_subdirectory(${ZEPHYR_CURRENT_MODULE_DIR}/zephyr/src hal_nxp)
