cmake_minimum_required(VERSION 3.15)
project(lodepng
  VERSION 1.0.0
  DESCRIPTION "PNG encoder and decoder in C and C++"
  HOMEPAGE_URL "https://github.com/lvandeve/lodepng"
  LANGUAGES CXX
)

# ------------------------------------------------------------------------------
# Library target
# ------------------------------------------------------------------------------
add_library(lodepng STATIC lodepng.cpp lodepng.h)
add_library(lodepng::lodepng ALIAS lodepng)

target_include_directories(lodepng
  PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
    $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>
)

target_compile_features(lodepng PUBLIC cxx_std_11)

set_target_properties(lodepng PROPERTIES
  POSITION_INDEPENDENT_CODE ON
  PUBLIC_HEADER lodepng.h
  VERSION ${PROJECT_VERSION}
  SOVERSION ${PROJECT_VERSION_MAJOR}
  EXPORT_NAME lodepng
  WINDOWS_EXPORT_ALL_SYMBOLS ON
)

# ------------------------------------------------------------------------------
# Installation
# ------------------------------------------------------------------------------
include(GNUInstallDirs)
include(CMakePackageConfigHelpers)

install(TARGETS lodepng
  EXPORT lodepngTargets
  ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
  LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
  RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
  PUBLIC_HEADER DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
  INCLUDES DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
)

install(EXPORT lodepngTargets
  FILE lodepngTargets.cmake
  NAMESPACE lodepng::
  DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/lodepng
)

# ------------------------------------------------------------------------------
# Package config files
# ------------------------------------------------------------------------------
write_basic_package_version_file(
  "${CMAKE_CURRENT_BINARY_DIR}/lodepngConfigVersion.cmake"
  VERSION ${PROJECT_VERSION}
  COMPATIBILITY SameMajorVersion
)

file(WRITE "${CMAKE_CURRENT_BINARY_DIR}/lodepngConfig.cmake.in" [[
@PACKAGE_INIT@
include("${CMAKE_CURRENT_LIST_DIR}/lodepngTargets.cmake")
check_required_components(lodepng)
]])

configure_package_config_file(
  "${CMAKE_CURRENT_BINARY_DIR}/lodepngConfig.cmake.in"
  "${CMAKE_CURRENT_BINARY_DIR}/lodepngConfig.cmake"
  INSTALL_DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/lodepng
)

install(FILES
  "${CMAKE_CURRENT_BINARY_DIR}/lodepngConfig.cmake"
  "${CMAKE_CURRENT_BINARY_DIR}/lodepngConfigVersion.cmake"
  DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/lodepng
)
