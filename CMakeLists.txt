cmake_minimum_required (VERSION 3.5)
project(edf2bin)
set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_FLAGS "-Os -fno-rtti ")

# To build against a library that is not installed, point these at the
# xbtbinfileutils include directory and build directory, e.g.
# cmake -DXBTBINFILEUTILS_INCLUDE_DIR=../xbtbinfileutils/include -DXBTBINFILEUTILS_LIBRARY_DIR=../xbtbinfileutils/build ..
set(XBTBINFILEUTILS_INCLUDE_DIR "" CACHE PATH "xbtbinfileutils include directory")
set(XBTBINFILEUTILS_LIBRARY_DIR "" CACHE PATH "xbtbinfileutils library directory")

#Find library to link against

find_library(XBTLIB
                    NAMES
                         xbtbinfileutils

                    HINTS
                         ${XBTBINFILEUTILS_LIBRARY_DIR}
                         ${CMAKE_INSTALL_PREFIX}
                    PATHS
                         ${CMAKE_INSTALL_PREFIX}
                    PATH_SUFFIXES
                          "lib"
                          "local/lib"
                          "xbtbinfileutils"
                          "local/lib/xbtbinfileutils"
                          "xbtbinfileutils/lib"
                          "xbtbinfileutils/bin"

                         )

# Find header files

find_path(HDRS      BinEncoder.h
                    NAMES
                         BinEncoder.h
                    HINTS
                         ${XBTBINFILEUTILS_INCLUDE_DIR}
                         ${CMAKE_INSTALL_PREFIX}
                         ${CMAKE_SYSTEM_PREFIX_PATH}
                    PATHS
                         ${CMAKE_INSTALL_PREFIX}
                         ${CMAKE_SYSTEM_PREFIX_PATH}
                    PATH_SUFFIXES
                          "include/xbtbinfileutils"
                          "xbtbinfileutils"
                          "xbtbinfileutils/include/xbtbinfileutils"

                         )

include_directories(${HDRS})

#create executable
add_executable(edf2bin src/edf2bin.cpp)
target_link_libraries(edf2bin ${XBTLIB})

install(TARGETS edf2bin DESTINATION bin)

# uninstall target
if(NOT TARGET uninstall)
    configure_file(
        "${CMAKE_CURRENT_SOURCE_DIR}/cmake_uninstall.cmake.in"
        "${CMAKE_CURRENT_BINARY_DIR}/cmake_uninstall.cmake"
        IMMEDIATE @ONLY)

    add_custom_target(uninstall
        COMMAND ${CMAKE_COMMAND} -P ${CMAKE_CURRENT_BINARY_DIR}/cmake_uninstall.cmake)
endif()

message(STATUS "xbtbinfileutils library: ${XBTLIB}")
message(STATUS "xbtbinfileutils headers: ${HDRS}")
