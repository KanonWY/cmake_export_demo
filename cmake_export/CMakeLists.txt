cmake_minimum_required(VERSION 3.20)

project(cmake_export LANGUAGES CXX C
  VERSION 0.1.1
)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_C_STANDARD 99)
set(CMAKE_CXX_EXTENSIONS OFF)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)
set(CMAKE_BUILD_TYPE Release)

find_package(spdlog REQUIRED)

add_library(aoe SHARED)
target_sources(aoe
    PRIVATE
      src/aoe.cpp
)
target_include_directories(aoe
    PUBLIC
      "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>"
)

add_library(kanon SHARED)
target_sources(kanon
    PRIVATE
      src/kanon.cpp
)
target_include_directories(kanon
    PUBLIC
      "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>"
)
target_link_libraries(kanon
    PUBLIC
      spdlog::spdlog
)

message(STATUS "====> CMAKE_INSTALL_LIBDIR     = ${CMAKE_INSTALL_LIBDIR}")
message(STATUS "====> CMAKE_INSTALL_DIR        = ${CMAKE_INSTALL_DIR}")
message(STATUS "====> CMAKE_INSTALL_PREFIX     = ${CMAKE_INSTALL_PREFIX}")
message(STATUS "====> LIB_SUFFIX               = ${LIB_SUFFIX}")
message(STATUS "====> CMAKE_INSTALL_INCLUDEDIR = ${CMAKE_INSTALL_INCLUDEDIR}")

# install Target
install(TARGETS aoe kanon EXPORT JszrTargets
    RUNTIME DESTINATION bin
    LIBRARY DESTINATION lib
    ARCHIVE DESTINATION lib
    PUBLIC_HEADER DESTINATION include/jszr
    INCLUDES DESTINATION include
)

# 设置配置最终安装路径
set(CONFIG_INSTALL_DIR lib/jszr/cmake)

set(_dependencies "spdlog")
include(CMakePackageConfigHelpers)
configure_package_config_file(
  ${CMAKE_CURRENT_SOURCE_DIR}/cmake/JszrConfig.cmake.in
  "${CMAKE_CURRENT_BINARY_DIR}/JszrConfig.cmake"
  INSTALL_DESTINATION ${CONFIG_INSTALL_DIR}
)
write_basic_package_version_file(
  ${CMAKE_CURRENT_BINARY_DIR}/JszrConfigVersion.cmake
  VERSION ${CMAKE_PROJECT_VERSION}
  COMPATIBILITY SameMajorVersion
)

## export targets for build directory
export(EXPORT JszrTargets
  FILE ${CMAKE_CURRENT_BINARY_DIR}/JszrTargets.cmake
)

## export targets for installation
install(EXPORT JszrTargets
  DESTINATION ${CONFIG_INSTALL_DIR}
  NAMESPACE jszr::
  FILE JszrTargets.cmake
)

## install pckage configuration files
install(FILES
     "${CMAKE_CURRENT_BINARY_DIR}/JszrConfig.cmake"
     "${CMAKE_CURRENT_BINARY_DIR}/JszrConfigVersion.cmake"
     DESTINATION ${CONFIG_INSTALL_DIR}
)
## install header
install(
  DIRECTORY "${CMAKE_CURRENT_SOURCE_DIR}/include/"
  DESTINATION include
  COMPONENT Devel
)

## for cpack
set(CPACK_PACKAGING_INSTALL_PREFIX "/opt/jszr")
set(CPACK_PACKAGE_NAME ${PROJECT_NAME})
set(CPACK_PACKAGE_FILE_NAME ${CPACK_PACKAGE_NAME}_${PROJECT_VERSION}_${CMAKE_HOST_SYSTEM_PROCESSOR}_$ENV{ROS_DISTRO}_${CMAKE_SYSTEM_NAME})
set(CPACK_GENERATOR "DEB")
set(CPACK_DEBIAN_PACKAGE_MAINTAINER "kanonwy")
include(CPack)
