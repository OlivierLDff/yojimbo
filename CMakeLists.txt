#
#   yojimbo CMake build script
#
#   Copyright Olivier Le Doeuff 2019
#

# ┌──────────────────────────────────────────────────────────────────┐
# │                       CMAKE PROPERTIES                           │
# └──────────────────────────────────────────────────────────────────┘

CMAKE_MINIMUM_REQUIRED( VERSION 3.11 )

# DEPENDANCIES

SET( MBEDCRYPTO_REPOSITORY "https://github.com/OlivierLDff/mbed-crypto" CACHE STRING "Repository of mbed-crypto" )
SET( MBEDCRYPTO_TAG "mbedcrypto-1.1.1-Superbuild" CACHE STRING "Git tag/branch of mbed-crypto" )

SET( MBEDTLS_REPOSITORY "https://github.com/OlivierLDff/mbedtls" CACHE STRING "Repository of mbedtls" )
SET( MBEDTLS_TAG "mbedtls-2.18.1-Superbuild" CACHE STRING "Git tag/branch of mbedtls" )

SET( SODIUM_REPOSITORY "https://github.com/OlivierLDff/libsodium" CACHE STRING "Repository of libsodium" )
SET( SODIUM_TAG "stable" CACHE STRING "Git Tag of libsodium" )

SET( NETCODE_REPOSITORY "https://github.com/OlivierLDff/netcode.io" CACHE STRING "Repository of netcode.io" )
SET( NETCODE_TAG "master" CACHE STRING "Git Tag of netcode.io" )

SET( RELIABLE_REPOSITORY "https://github.com/OlivierLDff/reliable.io" CACHE STRING "Repository of reliable.io" )
SET( RELIABLE_TAG "master" CACHE STRING "Git Tag of reliable.io" )

# ┌──────────────────────────────────────────────────────────────────┐
# │                       PROJECT SETTINGS                           │
# └──────────────────────────────────────────────────────────────────┘

# OPTIONS

# General
SET( YOJIMBO_PROJECT "yojimbo" CACHE STRING "Project Name")
SET( YOJIMBO_TARGET ${YOJIMBO_PROJECT} CACHE STRING "yojimbo library name" )
SET( YOJIMBO_VERSION "1.2.1" CACHE STRING "yojimbo current version, this is only decorative and will not configure any files" FORCE )
SET( YOJIMBO_BUILD_SHARED OFF CACHE BOOL "Build as a shared library (ON) or as static (OFF)" )
SET( YOJIMBO_FOLDER_PREFIX ${YOJIMBO_PROJECT} CACHE STRING "Prefix folder for all yojimbo generated targets in generated project (only decorative)" )

# Tests
SET( YOJIMBO_ENABLE_TESTS OFF CACHE BOOL "Create or not a target for test (compatible with CTests)" )
SET( YOJIMBO_TESTS_PREFIX ${YOJIMBO_PROJECT} CACHE STRING "Prefix for all yojimbo tests" )

#netcode.io and reliable.io test are required for yojimno tests
IF(YOJIMBO_ENABLE_TESTS)
    SET( NETCODE_ENABLE_TESTS ON CACHE BOOL "Create or not a target for test (compatible with CTests)" FORCE )
    SET( RELIABLE_ENABLE_TESTS ON CACHE BOOL "Create or not a target for test (compatible with CTests)" FORCE )
ENDIF(YOJIMBO_ENABLE_TESTS)

# Examples
SET( YOJIMBO_ENABLE_EXAMPLES OFF CACHE BOOL "Create or not a target for examples" )
SET( YOJIMBO_EXAMPLES_PREFIX ${YOJIMBO_PROJECT} CACHE STRING "Prefix for all yojimbo tests" )

# Install
SET( YOJIMBO_ENABLE_INSTALL OFF CACHE BOOL "Create or not a target for install" )
SET( YOJIMBO_INSTALL_PREFIX ${YOJIMBO_PROJECT} CACHE STRING "Prefix for all yojimbo headers in the install folder" )

# CREATE PROJECT

PROJECT( ${YOJIMBO_PROJECT} VERSION ${YOJIMBO_VERSION} LANGUAGES C CXX )
SET_PROPERTY(GLOBAL PROPERTY USE_FOLDERS ON)
SET(CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/cmake")

# LOG OPTIONS

MESSAGE("---------------- YOJIMBO OPTIONS. ----------------")

#General
MESSAGE(STATUS "YOJIMBO_PROJECT          : " ${YOJIMBO_PROJECT})
MESSAGE(STATUS "YOJIMBO_TARGET           : " ${YOJIMBO_TARGET})
MESSAGE(STATUS "YOJIMBO_VERSION          : " ${YOJIMBO_VERSION})
MESSAGE(STATUS "YOJIMBO_BUILD_SHARED     : " ${YOJIMBO_BUILD_SHARED})
MESSAGE(STATUS "YOJIMBO_FOLDER_PREFIX    : " ${YOJIMBO_FOLDER_PREFIX})

# Tests
MESSAGE(STATUS "YOJIMBO_ENABLE_TESTS     : " ${YOJIMBO_ENABLE_TESTS})
IF(YOJIMBO_ENABLE_TESTS)
MESSAGE(STATUS "YOJIMBO_TESTS_PREFIX     : " ${YOJIMBO_TESTS_PREFIX})
ENDIF(YOJIMBO_ENABLE_TESTS)

# Examples
MESSAGE(STATUS "YOJIMBO_ENABLE_EXAMPLES  : " ${YOJIMBO_ENABLE_EXAMPLES})
IF(YOJIMBO_ENABLE_EXAMPLES)
MESSAGE(STATUS "YOJIMBO_EXAMPLES_PREFIX  : " ${YOJIMBO_EXAMPLES_PREFIX})
ENDIF(YOJIMBO_ENABLE_EXAMPLES)

# Install
MESSAGE(STATUS "YOJIMBO_ENABLE_INSTALL   : " ${YOJIMBO_ENABLE_INSTALL})
IF(YOJIMBO_ENABLE_INSTALL)
MESSAGE(STATUS "YOJIMBO_INSTALL_PREFIX   : " ${YOJIMBO_INSTALL_PREFIX})
ENDIF(YOJIMBO_ENABLE_INSTALL)

MESSAGE("---------------- DONE WITH OPTIONS. -----------------")
MESSAGE("                                                     ")

# ┌──────────────────────────────────────────────────────────────────┐
# │                       DEPENDENCIES                               │
# └──────────────────────────────────────────────────────────────────┘

include(FetchContent)

# mbedtls
FetchContent_Declare(
    mbedtls
    GIT_REPOSITORY ${MBEDTLS_REPOSITORY}
    GIT_TAG        ${MBEDTLS_TAG}
)

SET(MBEDCRYPTO_FOLDER_PREFIX "Dependencies/mbedcrypto" CACHE STRING "")
SET(MBEDTLS_FOLDER_PREFIX "Dependencies/mbedtls" CACHE STRING "")

# libsodium
FetchContent_Declare(
    libsodium
    GIT_REPOSITORY ${SODIUM_REPOSITORY}
    GIT_TAG        ${SODIUM_TAG}
)

SET(SODIUM_FOLDER_PREFIX "Dependencies/libsodium" CACHE STRING "")

# netcode.io
FetchContent_Declare(
    netcode
    GIT_REPOSITORY ${NETCODE_REPOSITORY}
    GIT_TAG        ${NETCODE_TAG}
)

SET(NETCODE_FOLDER_PREFIX "Dependencies/netcode" CACHE STRING "")

# reliable.io
FetchContent_Declare(
    reliable
    GIT_REPOSITORY ${RELIABLE_REPOSITORY}
    GIT_TAG        ${RELIABLE_TAG}
)

SET(RELIABLE_FOLDER_PREFIX "Dependencies/reliable" CACHE STRING "")

SET(FETCHCONTENT_UPDATES_DISCONNECTED ON)
SET(FETCHCONTENT_QUIET OFF)

# Make all dependencies avaiables
FetchContent_MakeAvailable(mbedtls libsodium netcode reliable)

# ┌──────────────────────────────────────────────────────────────────┐
# │                          YOJIMBO                              │
# └──────────────────────────────────────────────────────────────────┘

# YOJIMBO SOURCES
SET(YOJIMBO_SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/yojimbo.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/tlsf/tlsf.c
    )
SET(YOJIMBO_HEADERS ${CMAKE_CURRENT_SOURCE_DIR}/yojimbo.h
    ${CMAKE_CURRENT_SOURCE_DIR}/tlsf/tlsf.h
    )

# YOJIMBO TARGET
IF(YOJIMBO_BUILD_SHARED)

    ADD_LIBRARY(${YOJIMBO_TARGET} SHARED ${YOJIMBO_SOURCES} ${YOJIMBO_HEADERS})
    TARGET_COMPILE_DEFINITIONS(${YOJIMBO_TARGET} PRIVATE "-DYOJIMBO_DLL_EXPORT")

ELSE(YOJIMBO_BUILD_SHARED)

    ADD_LIBRARY(${YOJIMBO_TARGET} STATIC  ${YOJIMBO_SOURCES} ${YOJIMBO_HEADERS})
    TARGET_COMPILE_DEFINITIONS(${YOJIMBO_TARGET} PUBLIC "-DYOJIMBO_STATIC")

ENDIF(YOJIMBO_BUILD_SHARED)

# Set include directory
TARGET_INCLUDE_DIRECTORIES(${YOJIMBO_TARGET} PUBLIC $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}> $<INSTALL_INTERFACE:${YOJIMBO_INSTALL_PREFIX}>)

# Avoid libnetcode.io on darwin for example.
SET_TARGET_PROPERTIES(${YOJIMBO_TARGET} PROPERTIES PREFIX "")

# Link to sodium library
TARGET_LINK_LIBRARIES(${YOJIMBO_TARGET} ${MBEDTLS_TARGET} ${SODIUM_TARGET} ${RELIABLE_TARGET} ${NETCODE_TARGET})

SET_TARGET_PROPERTIES(${YOJIMBO_TARGET} PROPERTIES FOLDER ${YOJIMBO_FOLDER_PREFIX})

# Link to math library when not using msvc
IF(NOT MSVC)
    TARGET_LINK_LIBRARIES(${YOJIMBO_TARGET} m)
ENDIF(NOT MSVC)

INCLUDE(CheckCCompilerFlag)

IF(MSVC)

    CHECK_C_COMPILER_FLAG("/fp:fast" IS_FFAST_MATH_AVAILABLE)
    IF(IS_FFAST_MATH_AVAILABLE)
        MESSAGE(STATUS "/fp:fast is available")
        SET(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} /fp:fast")
    ELSE(IS_FFAST_MATH_AVAILABLE)
        MESSAGE(STATUS "/fp:fast isn't available")
    ENDIF(IS_FFAST_MATH_AVAILABLE)

    # Disable warning about vsprintf that is unsafe
    SET(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} /wd4996")
ELSE(MSVC)

    CHECK_C_COMPILER_FLAG("ffast-math" IS_FFAST_MATH_AVAILABLE)
    IF(IS_FFAST_MATH_AVAILABLE)
        MESSAGE(STATUS "ffast-math is available")
        SET(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -ffast-math")
    ELSE(IS_FFAST_MATH_AVAILABLE)
        MESSAGE(STATUS "ffast-math isn't available")
    ENDIF(IS_FFAST_MATH_AVAILABLE)

    CHECK_C_COMPILER_FLAG("msse2" IS_SSE2_AVAILABLE)
    IF(IS_SSE2_AVAILABLE)
        MESSAGE(STATUS "sse2 is available")
        SET(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -msse2")
    ELSE(IS_SSE2_AVAILABLE)
        MESSAGE(STATUS "sse2 isn't available")
    ENDIF(IS_SSE2_AVAILABLE)

ENDIF(MSVC)

# ┌──────────────────────────────────────────────────────────────────┐
# │                           TESTS                                  │
# └──────────────────────────────────────────────────────────────────┘

IF(YOJIMBO_ENABLE_TESTS)
    INCLUDE(${CMAKE_CURRENT_SOURCE_DIR}/CTestLists.txt)
ENDIF(YOJIMBO_ENABLE_TESTS)

# ┌──────────────────────────────────────────────────────────────────┐
# │                         EXAMPLES                                 │
# └──────────────────────────────────────────────────────────────────┘

IF(YOJIMBO_ENABLE_EXAMPLES)

    MACRO(YOJIMBO_ADD_EXAMPLE EXAMPLE_NAME)

        SET(EXAMPLE_FULL_NAME ${YOJIMBO_EXAMPLES_PREFIX}_${EXAMPLE_NAME})

        MESSAGE(STATUS "Add Example: ${EXAMPLE_FULL_NAME}")

        ADD_EXECUTABLE       (${EXAMPLE_FULL_NAME} ${EXAMPLE_NAME}.cpp "shared.h")
        TARGET_LINK_LIBRARIES(${EXAMPLE_FULL_NAME} ${YOJIMBO_TARGET})
        SET_TARGET_PROPERTIES(${EXAMPLE_FULL_NAME} PROPERTIES FOLDER ${YOJIMBO_FOLDER_PREFIX}/Examples)

    ENDMACRO()

    YOJIMBO_ADD_EXAMPLE(client)
    YOJIMBO_ADD_EXAMPLE(server)
    YOJIMBO_ADD_EXAMPLE(secure_client)
    YOJIMBO_ADD_EXAMPLE(secure_server)
    YOJIMBO_ADD_EXAMPLE(client_server)
    YOJIMBO_ADD_EXAMPLE(loopback)
    YOJIMBO_ADD_EXAMPLE(soak)

ENDIF(YOJIMBO_ENABLE_EXAMPLES)

# ┌──────────────────────────────────────────────────────────────────┐
# │                         INSTALL                                  │
# └──────────────────────────────────────────────────────────────────┘

IF(YOJIMBO_ENABLE_INSTALL)
    INCLUDE(${CMAKE_CURRENT_SOURCE_DIR}/CInstallLists.txt)
ENDIF(YOJIMBO_ENABLE_INSTALL)