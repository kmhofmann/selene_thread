if(NOT IS_DIRECTORY ${SELENE_THREAD_DIR})
    message(FATAL_ERROR "Please build using the outermost CMakeLists.txt file.")
endif()

add_library(selene_thread INTERFACE)
add_library(selene_thread::selene_thread ALIAS selene_thread)

target_sources(selene_thread INTERFACE
        $<BUILD_INTERFACE:${CMAKE_CURRENT_LIST_DIR}/ThreadPool.hpp>
        $<BUILD_INTERFACE:${CMAKE_CURRENT_LIST_DIR}/impl/Callable.hpp>
        $<BUILD_INTERFACE:${CMAKE_CURRENT_LIST_DIR}/impl/TaskQueue.hpp>
        )

target_include_directories(selene_thread INTERFACE
        $<BUILD_INTERFACE:${SELENE_THREAD_DIR}>
        $<INSTALL_INTERFACE:include>)

# Installation

include(GNUInstallDirs)
include(CMakePackageConfigHelpers)

set(SELENE_THREAD_INSTALL_CONFIGDIR ${CMAKE_INSTALL_LIBDIR}/cmake/selene_thread)

# Create selene-config-version.cmake
write_basic_package_version_file(
        ${CMAKE_CURRENT_BINARY_DIR}/selene_thread-config-version.cmake
        VERSION ${PROJECT_VERSION}
        COMPATIBILITY AnyNewerVersion)

# Create selene-config.cmake
configure_package_config_file(
        ${SELENE_THREAD_DIR}/cmake/selene_thread-config.cmake.in
        ${CMAKE_CURRENT_BINARY_DIR}/selene_thread-config.cmake
        INSTALL_DESTINATION ${SELENE_THREAD_INSTALL_CONFIGDIR})

# Install library files
install(TARGETS selene_thread
        EXPORT selene_thread-targets
        LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
        ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
        RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
        PUBLIC_HEADER DESTINATION ${CMAKE_INSTALL_INCLUDEDIR})

# Install headers
install(DIRECTORY ${CMAKE_CURRENT_LIST_DIR}
        DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
        FILES_MATCHING PATTERN "*.hpp")

# Install exports
install(EXPORT selene_thread-targets
        FILE selene_thread-targets.cmake
        NAMESPACE selene_thread::
        DESTINATION ${SELENE_THREAD_INSTALL_CONFIGDIR})

# Install config and config-version files
install(FILES
        ${CMAKE_CURRENT_BINARY_DIR}/selene_thread-config.cmake
        ${CMAKE_CURRENT_BINARY_DIR}/selene_thread-config-version.cmake
        DESTINATION ${SELENE_THREAD_INSTALL_CONFIGDIR})

#------------------------------------------------------------------------------

# Exporting from the build tree

export(EXPORT selene_thread-targets
        FILE ${CMAKE_CURRENT_BINARY_DIR}/selene_thread-targets.cmake
        NAMESPACE selene_thread::)

export(PACKAGE selene_thread)
