cmake_minimum_required(VERSION 3.11)

list(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/cmake")

include (get_version)

message(STATUS " --pthreads_win32-- ")
message(STATUS "Generator ......... ${CMAKE_GENERATOR}")
message(STATUS "Build Type ........ ${CMAKE_BUILD_TYPE}")
message(STATUS "Version ........... ${PTHREADS4W_VERSION}")

project(pthreads4w VERSION ${PTHREADS4W_VERSION} LANGUAGES C CXX)

# set(PTW32_VER ${PROJECT_VERSION_MAJOR}${EXTRAVERSION})
# if(NOT CMAKE_DEBUG_POSTFIX)
#     set(CMAKE_DEBUG_POSTFIX d)
# endif(NOT CMAKE_DEBUG_POSTFIX)

# Uncomment this if config.h defines RETAIN_WSALASTERROR
#set(XLIBS wsock32.lib)

# include_directories(.)

#################################
# Target Arch                   #
#################################
include (target_arch)

get_target_arch(TARGET_ARCH)

if(TARGET_ARCH STREQUAL "ARM")
    add_definitions(-DPTW32_ARCHARM -D_ARM_WINAPI_PARTITION_DESKTOP_SDK_AVAILABLE=1)
elseif(TARGET_ARCH STREQUAL "ARM64")
    add_definitions(-DPTW32_ARCHARM64 -D_ARM_WINAPI_PARTITION_DESKTOP_SDK_AVAILABLE=1)
elseif(TARGET_ARCH STREQUAL "x86_64")
    add_definitions(-DPTW32_ARCHAMD64)
elseif(TARGET_ARCH STREQUAL "x86")
    add_definitions(-DPTW32_ARCHX86)
elseif(TARGET_ARCH STREQUAL "x64")
    add_definitions(-DPTW32_ARCHX64)
else()
    MESSAGE(FATAL_ERROR "\"${TARGET_ARCH}\" not supported in version.rc")
endif()
message(STATUS "Target ............ ${TARGET_ARCH}")

if(MSVC)
    message(STATUS "MSVC Version ...... ${MSVC_VERSION}")
endif()

#################################
# Defs                          #
#################################
# if(MSVC)
#     # C++ Exceptions
#     # (Note: If you are using Microsoft VC++6.0, the library needs to be built
#     # with /EHa instead of /EHs or else cancellation won't work properly.)
#     if(MSVC_VERSION EQUAL 1200)
#         set(VCEFLAGS  "/EHa /errorReport:none /nologo ")
#     else()
#         set(VCEFLAGS  "/EHs /errorReport:none /nologo ")
#     endif()
#
# endif()

# Update filename with proper version info
configure_file(${CMAKE_CURRENT_SOURCE_DIR}/cmake/version.rc.in ${CMAKE_CURRENT_BINARY_DIR}/version.rc @ONLY)

#################################
# Libraries                     #
#################################

# macro(shared_lib type def)
#     set(targ pthread${type})
#     if(${type} STREQUAL "VCE" OR ${type} STREQUAL "VSE")
# 		add_library(${targ} SHARED pthread-EH.cpp ${CMAKE_CURRENT_BINARY_DIR}/version.rc)
# 		#set_target_properties(${targ} PROPERTIES LINKER_LANGUAGE CXX)
# 	else()
# 	    add_library(${targ} SHARED pthread-JMP.c ${CMAKE_CURRENT_BINARY_DIR}/version.rc)
# 	endif()
#     message(STATUS ${targ})
#     target_compile_definitions(${targ} PUBLIC "-D${def}")
#     target_compile_definitions(${targ} PUBLIC -D_WINDLL)
#     target_compile_definitions(${targ} PUBLIC -DPTW32_BUILD_INLINED)
#     if(MSVC)
#         target_compile_definitions(${targ} PUBLIC -DHAVE_CONFIG_H -DPTW32_RC_MSC)
#     endif()
#     target_include_directories(${targ} PRIVATE ".")
#     if(${type} STREQUAL "VCE" OR ${type} STREQUAL "VSE")
#         set_target_properties(${targ} PROPERTIES COMPILE_FLAGS ${VCEFLAGS})
#     endif()
# endmacro()

# macro(static_lib type def)
#     set(targ libpthread${type})
#     if(${type} STREQUAL "VCE" OR ${type} STREQUAL "VSE")
# 		add_library(${targ} STATIC pthread-EH.cpp)
# 		#set_target_properties(${targ} PROPERTIES LINKER_LANGUAGE CXX)
# 	else()
# 	    add_library(${targ} STATIC pthread-JMP.c)
# 	endif()
#     message(STATUS ${targ})
#     target_compile_definitions(${targ} PUBLIC "-D${def}" -DPTW32_STATIC_LIB)
#     target_compile_definitions(${targ} PUBLIC -DPTW32_BUILD_INLINED)
#     if(MSVC)
#         target_compile_definitions(${targ} PUBLIC -DHAVE_CONFIG_H -DPTW32_RC_MSC)
#     endif()
#     target_include_directories(${targ} PRIVATE ".")
#     if(${type} STREQUAL "VCE" OR ${type} STREQUAL "VSE")
#         set_target_properties(${targ} PROPERTIES COMPILE_FLAGS ${VCEFLAGS})
#     endif()
# endmacro()

# shared_lib ( VCE PTW32_CLEANUP_CXX )
# shared_lib ( VSE PTW32_CLEANUP_SEH )
# shared_lib ( VC  PTW32_CLEANUP_C )
#
# static_lib ( VCE PTW32_CLEANUP_CXX )
# static_lib ( VSE PTW32_CLEANUP_SEH )
# static_lib ( VC  PTW32_CLEANUP_C )

set(type "VC")
set(targ libpthread${type})
add_library(${targ} STATIC pthread-JMP.c)
message(STATUS ${targ})
target_compile_definitions(${targ} PUBLIC "-D${def}" -DPTW32_STATIC_LIB)
target_compile_definitions(${targ} PUBLIC -DPTW32_BUILD_INLINED)
if(MSVC)
    target_compile_definitions(${targ} PUBLIC -DHAVE_CONFIG_H -DPTW32_RC_MSC)
endif()
target_include_directories(${targ} PUBLIC ".")
