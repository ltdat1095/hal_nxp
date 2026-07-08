# MobTurret: hal_nxp module CMakeLists
#
# Restores the top-level CMakeLists.txt that the build expects when a
# module declares `cmake: .` in its module.yml. The upstream
# `zephyrproject-rtos/hal_nxp` repo removed this file during a
# restructuring. Without it, the build cannot find the module's CMake
# integration.
#
# For now this only adds the zephyr/src subdirectory (which has the
# wireless / RW61x source files preserved from the NXP-downstream era).
# Full SDK integration (imx/, s32/, mcux/ add_subdirectory with their
# respective HAS_* guards) is a separate task — for the i.MX93 M33
# minimal hello world we don't need the SDK sources.

# Copyright (c) 2021 Linaro, Limited
# Copyright 2024-2026 NXP
# SPDX-License-Identifier: Apache-2.0

add_subdirectory(${ZEPHYR_CURRENT_MODULE_DIR}/zephyr/src hal_nxp)
