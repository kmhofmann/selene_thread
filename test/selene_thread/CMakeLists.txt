if(NOT IS_DIRECTORY ${SELENE_THREAD_DIR})
    message(FATAL_ERROR "Please build using the outermost CMakeLists.txt file.")
endif()

#------------------------------------------------------------------------------

if("${CMAKE_CXX_COMPILER_ID}" STREQUAL "GNU" OR "${CMAKE_CXX_COMPILER_ID}" MATCHES "Clang")
    set(SELENE_THREAD_COMPILER_OPTIONS -Wall -Wextra -Wpedantic)
    if("${CMAKE_CXX_COMPILER_ID}" STREQUAL "GNU")
        set(SELENE_THREAD_TEST_COMPILER_OPTIONS -Wno-unused-but-set-variable)
    endif()
    set(SELENE_THREAD_COMPILER_DEFINITIONS "")
elseif("${CMAKE_CXX_COMPILER_ID}" STREQUAL "MSVC")
    set(SELENE_THREAD_COMPILER_OPTIONS /MP /permissive- /wd4324 /wd4611)
    if (NOT "${CMAKE_GENERATOR}" MATCHES "NMake")
        string(REGEX REPLACE " /W[0-4]" "" CMAKE_C_FLAGS "${CMAKE_C_FLAGS}")
        string(REGEX REPLACE " /W[0-4]" "" CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS}")
        set(SELENE_THREAD_COMPILER_OPTIONS ${SELENE_THREAD_COMPILER_OPTIONS} /W4)
    endif()
    set(SELENE_THREAD_COMPILER_DEFINITIONS _CRT_SECURE_NO_WARNINGS _SCL_SECURE_NO_WARNINGS)
endif()

#------------------------------------------------------------------------------

add_executable(selene_thread_tests
        ${CMAKE_CURRENT_LIST_DIR}/Catch.cpp
        ThreadPool.cpp
        )

target_compile_options(selene_thread_tests PRIVATE
        ${SELENE_THREAD_COMPILER_OPTIONS}
        ${SELENE_THREAD_TEST_COMPILER_OPTIONS})

target_compile_definitions(selene_thread_tests PRIVATE
        ${SELENE_THREAD_COMPILER_DEFINITIONS})

target_include_directories(selene_thread_tests PRIVATE
        ${SELENE_THREAD_DIR}
        ${SELENE_THREAD_DIR}/external/Catch2/single_include)

target_link_libraries(selene_thread_tests
        selene_thread
        Threads::Threads)
