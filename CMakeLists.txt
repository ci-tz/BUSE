# Minimum CMake version required
cmake_minimum_required(VERSION 3.10)

# Project name and C standard
project(BuseExample C)
set(CMAKE_C_STANDARD 99)
set(CMAKE_C_STANDARD_REQUIRED ON)

# Compiler options
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -g -pedantic -Wall -Wextra")

# Source files
set(TARGETS busexmp loopback)
set(LIBRARY_SOURCES buse.c)

# Build static library
add_library(buse STATIC ${LIBRARY_SOURCES})
set_target_properties(buse PROPERTIES OUTPUT_NAME "buse")

# Build executables
foreach(target ${TARGETS})
    add_executable(${target} ${target}.c)
    target_include_directories(${target} PRIVATE ${CMAKE_SOURCE_DIR})
    target_link_libraries(${target} PRIVATE buse)
endforeach()

# Add custom target for testing
add_custom_target(test
    COMMAND PATH=${CMAKE_BINARY_DIR}:$ENV{PATH} sudo ${CMAKE_SOURCE_DIR}/test/busexmp.sh
    COMMAND PATH=${CMAKE_BINARY_DIR}:$ENV{PATH} sudo ${CMAKE_SOURCE_DIR}/test/signal_termination.sh
    DEPENDS ${TARGETS}
    COMMENT "Running tests..."
)