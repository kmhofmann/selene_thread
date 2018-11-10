cmake_minimum_required(VERSION 3.5)
project(selene_thread VERSION 0.1 LANGUAGES CXX)

option(SELENE_THREAD_BUILD_TESTS "Build Selene_thread tests" OFF)

# https://cmake.org/cmake/help/latest/prop_tgt/CXX_STANDARD.html
set(CMAKE_CXX_STANDARD 14 CACHE STRING "The C++ standard whose features are requested to build this target.")
set(CMAKE_CXX_STANDARD_REQUIRED ON CACHE BOOL "Boolean describing whether the value of CXX_STANDARD is a requirement.")
set(CMAKE_CXX_EXTENSIONS OFF CACHE BOOL "Boolean specifying whether compiler specific extensions are requested.")

# Generate compilation database (e.g. for cquery or YouCompleteMe)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

set(SELENE_THREAD_DIR ${CMAKE_CURRENT_SOURCE_DIR})

# Conan support: the cmake_paths generator requires Conan >= 1.4.1
if (EXISTS ${CMAKE_BINARY_DIR}/conan_paths.cmake)
    include(${CMAKE_BINARY_DIR}/conan_paths.cmake)
endif()

add_subdirectory(selene_thread)

if (SELENE_THREAD_BUILD_TESTS)
    message(STATUS "Installing Catch submodule")
    execute_process(COMMAND git submodule update --init -- external/Catch2 WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR})

    find_package(Threads REQUIRED)

    add_subdirectory(test/selene_thread)
endif()
