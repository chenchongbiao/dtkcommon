# SPDX-FileCopyrightText: 2022 Uniontech Software Technology Co.,Ltd.
#
# SPDX-License-Identifier: BSD-3-Clause

cmake_minimum_required(VERSION 3.10)
#set(DVERSION "5.5.22")
set (DVERSION "5.6.2" CACHE STRING "define project version")
project(dtkcommon
  VERSION ${DVERSION}
  DESCRIPTION "DTK COMMON"
  HOMEPAGE_URL "https://github.com/linuxdeepin/dtkcommon"
)
# 在指定安装路径时，应当使用变量而非写死安装目录，
# 以便于在不完全符合 FHS 的系统上安装，提高程序的可移植性。对于使用何种变量，
# GUN 提出了适用于 unix 系统的 GNU标准安装目录，GNU/Linux 上使用的就是这套标准的变体。
# cmake 官方提供了 GNUInstallDirs 模块，定义了一组标准的变量，
# 用于安装不同类型文件到规范指定的目录中。
# 要使用这个模块，在 CMakeLists.txt 添加一行 include(GNUInstallDirs) 即可导入。
# 如果你发现 CMAKE_INSTALL_XXXX 的值为空，大概率是缺少这一行。注意导入模块需要放在使用变量之前。
# https://blog.csdn.net/qq_33831360/article/details/126547238
include(GNUInstallDirs)

if (CMAKE_INSTALL_PREFIX_INITIALIZED_TO_DEFAULT)
# CMAKE_INSTALL_PREFIX（后面简称 PREFIX） 是一个非常特殊的变量，
# 在 CMakeLists.txt 中所有的相对路径都会默认把 PREFIX 作为前缀进行拼接，组成绝对路径。
  set(CMAKE_INSTALL_PREFIX /usr)
endif ()

message(STATUS ${PROJECT_VERSION})

# CMAKE_INSTALL_FULL_LIBDIR=/usr/local/lib
# MKSPECS_INSTALL_DIR=/usr/local/lib/qt5/mkspecs
set (MKSPECS_INSTALL_DIR "${CMAKE_INSTALL_FULL_LIBDIR}/qt5/mkspecs" CACHE STRING "INSTALL DIR FOR qt pri files")

# MODULE_INCLUDES=/usr/local/include/libdtk-5.6.2
set(MODULE_INCLUDES ${CMAKE_INSTALL_FULL_INCLUDEDIR}/libdtk-${PROJECT_VERSION})
# MODULE_TOOLS=/usr/local/lib/libdtk-5.6.2
set(MODULE_TOOLS ${CMAKE_INSTALL_FULL_LIBDIR}/libdtk-${PROJECT_VERSION})
# misc/qt_lib_dtkcommon.pri.in输出为qt_lib_dtkcommon.pri
# @ONLY：限制变量替换，让其只替换被@VAR@引用的变量(那么${VAR}格式的变量将不会被替换)
configure_file(misc/qt_lib_dtkcommon.pri.in qt_lib_dtkcommon.pri @ONLY)

# 安装目标位置/.usr/local/lib/qt5/mkspecs/modules
install(FILES ${CMAKE_CURRENT_BINARY_DIR}/qt_lib_dtkcommon.pri 
  DESTINATION "${MKSPECS_INSTALL_DIR}/modules")
# 安装目标位置/usr/share/glib-2.0/schemas
install(FILES schemas/com.deepin.dtk.gschema.xml 
  DESTINATION "${CMAKE_INSTALL_DATAROOTDIR}/glib-2.0/schemas")
# 将features目录安装到/usr/local/lib/qt5/mkspecs
install(DIRECTORY features 
  DESTINATION "${MKSPECS_INSTALL_DIR}" 
  FILES_MATCHING PATTERN "*" 
)
# 将cmake目录安装到/usr/lib
install(DIRECTORY cmake 
  DESTINATION "${CMAKE_INSTALL_LIBDIR}" 
  FILES_MATCHING  PATTERN "*" 
)

