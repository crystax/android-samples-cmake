cmake_minimum_required(VERSION 2.8)

if (ANDROID AND NOT CRYSTAX)
    message(FATAL_ERROR "You should use CrystaX-enabled CMake toolchain")
endif()

if (ANDROID)
    if (ANDROID_NDK_ABI_NAME STREQUAL "armeabi")
        message(FATAL_ERROR "'${ANDROID_NDK_ABI_NAME}' target is not supported due to lack of the lock-free atomics")
    endif()
    if (NOT ANDROID_NDK_ABI_NAME MATCHES "^(armeabi-v7a|armeabi-v7a-hard|arm64-v8a|x86|x86_64|mips|mips64)$")
        message(FATAL_ERROR "Unsupported ABI: '${ANDROID_NDK_ABI_NAME}'")
    endif()
endif()

set(TARGET cmake-test)

add_definitions(-std=c++11 -Wall -Wextra -Werror)
add_executable(${TARGET} test.cpp)

if (ANDROID)
    foreach(__extLibrary ${STDCXX_LIBRARY} ${LIBCRYSTAX_LIBRARY})
        add_custom_command(TARGET ${TARGET} PRE_LINK
            COMMAND ${CMAKE_COMMAND} -E copy_if_different ${__extLibrary} $<TARGET_FILE_DIR:${TARGET}>)
    endforeach()
endif()

unset(LAUNCHER)
if (ANDROID)
    list(APPEND LAUNCHER ${ANDROID_NDK}/tools/adbrunner)
    list(APPEND LAUNCHER --abi=${ANDROID_NDK_ABI_NAME})
    list(APPEND LAUNCHER --ld-library-path=$<TARGET_FILE_DIR:${TARGET}>)
    list(APPEND LAUNCHER --log=$<TARGET_FILE_DIR:${TARGET}>/adbrunner.log)
endif()

add_custom_target(run COMMAND ${LAUNCHER} $<TARGET_FILE:${TARGET}> )
add_dependencies(run ${TARGET})
