cmake_minimum_required(VERSION 3.19)
project(mhd_lbm)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

if(DEFINED ENV{Torch_DIR})
    message(STATUS "torch installation prefix=$ENV{Torch_DIR}")
else()
    message(FATAL_ERROR "Torch_DIR not defined")
endif()

if(DEFINED ENV{TOMLCPP_DIR})
    message(STATUS "tomlcplusplus include directory=$ENV{TOMLCPP_DIR}")
else()
    message(FATAL_ERROR "tomlcplusplus not defined")
endif()

include_directories("$ENV{TOMLCPP_DIR}")

find_package(Torch REQUIRED)

# Libraries
add_library(params OBJECT src/params.cpp src/params.hpp)
set_property(TARGET params PROPERTY CXX_STANDARD 17)
target_link_libraries(params "${TORCH_LIBRARIES}")
target_include_directories(params ${warning_guard} PUBLIC
        "\$<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}/src")

add_library(d3q19 OBJECT src/d3q19.cpp src/d3q19.hpp)
set_property(TARGET d3q19 PROPERTY CXX_STANDARD 17)
target_link_libraries(d3q19 "${TORCH_LIBRARIES}")
target_include_directories(d3q19 ${warning_guard} PUBLIC
        "\$<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}/src")

# Executables
add_executable(vortex_flow main.cpp)
set_property(TARGET vortex_flow PROPERTY CXX_STANDARD 17)
target_link_libraries(vortex_flow
        PRIVATE "${TORCH_LIBRARIES}" params d3q19)
