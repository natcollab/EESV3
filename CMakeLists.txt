#[===============================================================================[.rst
# EESV3/CMakeLists.txt
#
# EESV3 library.
# ^^^^^^^^^^^^
#
# Imported targets in global scope:
#
#   EESV3::Trader
#   EESV3::Quote
#
#]===============================================================================]

# Setup minimum required version of cmake, policy
# This command should be the first command in the project
cmake_minimum_required(VERSION 3.13...3.16)

# Setup Project name and version
# CMakeTest_VERSION and PROJECT_VERSION variables are set
project(EESV3 VERSION 1.0
  DESCRIPTION "Interface to EESV3 library"
  LANGUAGES CXX
  )

#=================================================================================

# Target: Trader
add_library(EESV3.Trader INTERFACE)
add_library(EESV3::Trader ALIAS EESV3.Trader)

set(EESV3_INCLUDE_INSTALL_DIR include/EESV3)
set(EESV3_LIB_INSTALL_DIR lib/EESV3)
set(EESV3_CMAKE_INSTALL_DIR lib/cmake/EESV3)

target_include_directories(EESV3.Trader
  INTERFACE  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
             $<INSTALL_INTERFACE:${EESV3_INCLUDE_INSTALL_DIR}>
  )

target_link_libraries(EESV3.Trader
  INTERFACE  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/lib/libEESTraderApi.so>
             $<INSTALL_INTERFACE:$<INSTALL_PREFIX>/${EESV3_LIB_INSTALL_DIR}/libEESTraderApi.so>
  )

# Target: Quote
add_library(EESV3.Quote INTERFACE)
add_library(EESV3::Quote ALIAS EESV3.Quote)

target_include_directories(EESV3.Quote
  INTERFACE  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
             $<INSTALL_INTERFACE:${EESV3_INCLUDE_INSTALL_DIR}>
  )

target_link_libraries(EESV3.Quote
  INTERFACE  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/lib/libEESQuoteApi.so>
             $<INSTALL_INTERFACE:$<INSTALL_PREFIX>/${EESV3_LIB_INSTALL_DIR}/libEESQuoteApi.so>
  )

#=================================================================================

# Install : Trader
install(DIRECTORY include/
  DESTINATION ${EESV3_INCLUDE_INSTALL_DIR}
  FILES_MATCHING PATTERN "EesTrader*.h"
  )

install(FILES lib/libEESTraderApi.so
  DESTINATION ${EESV3_LIB_INSTALL_DIR}
  )

# Install : Quote
install(DIRECTORY include/
  DESTINATION ${EESV3_INCLUDE_INSTALL_DIR}
  FILES_MATCHING PATTERN "EESQuote*.h"
  )

install(FILES lib/libEESQuoteApi.so
  DESTINATION ${EESV3_LIB_INSTALL_DIR}
  )

#=================================================================================

# Install export targets

install(TARGETS EESV3.Trader EXPORT EESV3_export)
set_target_properties(EESV3.Trader PROPERTIES EXPORT_NAME Trader)

install(TARGETS EESV3.Quote EXPORT EESV3_export)
set_target_properties(EESV3.Quote PROPERTIES EXPORT_NAME Quote)

install(EXPORT EESV3_export
  NAMESPACE EESV3::
  FILE EESV3Targets.cmake
  DESTINATION ${EESV3_CMAKE_INSTALL_DIR}
  )

#=================================================================================

# Install package config and version files

include(CMakePackageConfigHelpers)

# Package version file
write_basic_package_version_file(
  ${CMAKE_CURRENT_BINARY_DIR}/EESV3ConfigVersion.cmake
  VERSION ${PROJECT_VERSION}
  COMPATIBILITY SameMajorVersion
  )

# Package config file
configure_package_config_file(
  ${CMAKE_CURRENT_LIST_DIR}/cmake/EESV3Config.cmake.in
  ${CMAKE_CURRENT_BINARY_DIR}/EESV3Config.cmake
  INSTALL_DESTINATION ${EESV3_CMAKE_INSTALL_DIR}
  PATH_VARS
    EESV3_INCLUDE_INSTALL_DIR
    EESV3_LIB_INSTALL_DIR
  )

# Install the config and version files
install(FILES
  ${CMAKE_CURRENT_BINARY_DIR}/EESV3Config.cmake
  ${CMAKE_CURRENT_BINARY_DIR}/EESV3ConfigVersion.cmake
  DESTINATION ${EESV3_CMAKE_INSTALL_DIR}
  )
