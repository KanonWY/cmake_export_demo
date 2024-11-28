cmake_minimum_required(VERSION 3.20)

project(cmake_export_use LANGUAGES CXX C)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_C_STANDARD 99)
set(CMAKE_CXX_EXTENSIONS OFF)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

find_package(Jszr REQUIRED)
message(STATUS "==> ${Jszr_VERSION}")
message(STATUS "==> ${CMAKE_PREFIX_PATH}")


add_executable(main main.cpp)
target_link_libraries(main jszr::kanon)
