cmake_minimum_required(VERSION 2.8)

if (ANDROID AND NOT CRYSTAX)
    message(FATAL_ERROR "This example requires CrystaX NDK for Android build")
endif()

if (ANDROID)
    # Example of filtering of target ABIs
    if (ANDROID_NDK_ABI_NAME STREQUAL "armeabi")
        message(FATAL_ERROR "'${ANDROID_NDK_ABI_NAME}' target is not supported due to lack of the lock-free atomics")
    endif()
    if (NOT ANDROID_NDK_ABI_NAME MATCHES "^(armeabi-v7a|armeabi-v7a-hard|arm64-v8a|x86|x86_64|mips|mips64)$")
        message(FATAL_ERROR "Unsupported ABI: '${ANDROID_NDK_ABI_NAME}'")
    endif()
endif()

set(TARGET hello-world)

add_definitions(-std=c++11 -Wall -Wextra -Werror)
add_executable(${TARGET} test.cpp)

if (ANDROID)
    # Copy additional libraries to the target folder
    foreach(__extLibrary ${ANDROID_LIBCRYSTAX_FILE} ${ANDROID_LIBSTDCXX_FILE})
        add_custom_command(TARGET ${TARGET} PRE_LINK
            COMMAND ${CMAKE_COMMAND} -E copy_if_different ${__extLibrary} $<TARGET_FILE_DIR:${TARGET}>)
    endforeach()
endif()

unset(LAUNCHER)
if (ANDROID AND DEFINED ADBRUNNER)
    set(LAUNCHER ${ADBRUNNER})
    list(APPEND LAUNCHER --log=$<TARGET_FILE_DIR:${TARGET}>/adbrunner.log)
endif()

add_custom_target(run COMMAND ${LAUNCHER} $<TARGET_FILE:${TARGET}> )
add_dependencies(run ${TARGET})
