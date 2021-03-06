
cmake_minimum_required(VERSION 2.6)
project(snappy)

if(POLICY CMP0042)
  cmake_policy(SET CMP0042 NEW)
endif()

set(SNAPPY_MAJOR 1)
set(SNAPPY_MINOR 1)
set(SNAPPY_PATCHLEVEL 4)

INCLUDE (CheckIncludeFiles)

CHECK_INCLUDE_FILES(stdint.h HAVE_STDINT_H)
CHECK_INCLUDE_FILES(stddef.h HAVE_STDDEF_H)
CHECK_INCLUDE_FILES(byteswap.h HAVE_BYTESWAP_H)
CHECK_INCLUDE_FILES(dlfcn.h HAVE_DLFCN_H)
CHECK_INCLUDE_FILES(inttypes.h HAVE_INTTYPES_H)
CHECK_INCLUDE_FILES(memory.h HAVE_MEMORY_H)
CHECK_INCLUDE_FILES(stdlib.h HAVE_STDLIB_H)
CHECK_INCLUDE_FILES(strings.h HAVE_STRINGS_H)
CHECK_INCLUDE_FILES(string.h HAVE_STRING_H)
CHECK_INCLUDE_FILES(sys/byteswap.h HAVE_SYS_BYTESWAP_H)
CHECK_INCLUDE_FILES(sys/endian.h HAVE_SYS_ENDIAN_H)
CHECK_INCLUDE_FILES(sys/mman.h HAVE_SYS_MMAN_H)
CHECK_INCLUDE_FILES(sys/mman.h HAVE_FUNC_MMAP)
CHECK_INCLUDE_FILES(sys/resource.h HAVE_SYS_RESOURCE_H)
CHECK_INCLUDE_FILES(sys/stat.h HAVE_SYS_STAT_H)
CHECK_INCLUDE_FILES(sys/time.h HAVE_SYS_TIME_H)
CHECK_INCLUDE_FILES(sys/types.h HAVE_SYS_TYPES_H)
CHECK_INCLUDE_FILES(unistd.h HAVE_UNISTD_H)
CHECK_INCLUDE_FILES(windows.h HAVE_WINDOWS_H)
CHECK_INCLUDE_FILES(sys/uio.h HAVE_SYS_UIO_H)

if(WIN32)
  if (HAVE_STDINT_H)
    set(ac_cv_have_stdint_h 1)
    add_definitions(-DHAVE_STDINT_H)
  else ()
    set(ac_cv_have_stdint_h 0)
  endif()

  if (HAVE_STDDEF_H)
    set(ac_cv_have_stddef_h 1)
    add_definitions(-DHAVE_STDDEF_H)
  else ()
    set(ac_cv_have_stddef_h 0)
  endif()

  if (HAVE_SYS_UIO_H)
    set(ac_cv_have_sys_uio_h 1)
    add_definitions(-DHAVE_SYS_UIO_H)
  else ()
     set(ac_cv_have_sys_uio_h 0)
  endif()

  if (HAVE_BYTESWAP_H)
    add_definitions(-DHAVE_BYTESWAP_H)
  endif()
  if (HAVE_DLFCN_H)
    add_definitions(-DHAVE_DLFCN_H)
  endif()
  if (HAVE_INTTYPES_H)
    add_definitions(-DHAVE_INTTYPES_H)
  endif()
  if (HAVE_MEMORY_H)
    add_definitions(-DHAVE_MEMORY_H)
  endif()
  if (HAVE_STDLIB_H)
    add_definitions(-DHAVE_STDLIB_H)
  endif()
  if (HAVE_STRINGS_H)
    add_definitions(-DHAVE_STRINGS_H)
  endif()
  if (HAVE_STRING_H)
    add_definitions(-DHAVE_STRING_H)
  endif()
  if (HAVE_SYS_BYTESWAP_H)
    add_definitions(-DHAVE_SYS_BYTESWAP_H)
  endif()
  if (HAVE_SYS_ENDIAN_H)
    add_definitions(-DHAVE_SYS_ENDIAN_H)
  endif()
  if (HAVE_SYS_MMAN_H)
    add_definitions(-DHAVE_SYS_MMAN_H)
  endif()
  if (HAVE_FUNC_MMAP)
    add_definitions(-DHAVE_FUNC_MMAP)
  endif()
  if (HAVE_SYS_RESOURCE_H)
  add_definitions(-DHAVE_SYS_RESOURCE_H)
  endif()
  if (HAVE_SYS_STAT_H)
    add_definitions(-DHAVE_SYS_STAT_H)
  endif()
  if (HAVE_SYS_TIME_H)
    add_definitions(-DHAVE_SYS_TIME_H)
  endif()
  if (HAVE_SYS_TYPES_H)
    add_definitions(-DHAVE_SYS_TYPES_H)
  endif()
  if (HAVE_UNISTD_H)
    add_definitions(-DHAVE_UNISTD_H)
  endif()
  if (HAVE_STDINT_H)
    add_definitions(-DHAVE_STDINT_H)
  endif()
  if (HAVE_WINDOWS_H)
    add_definitions(-DHAVE_WINDOWS_H)
  endif()
endif()

configure_file(snappy-stubs-public.h.in ${PROJECT_SOURCE_DIR}/snappy-stubs-public.h @ONLY)

if(CMAKE_SYSTEM_NAME MATCHES "Windows")
  add_definitions(-DWIN32 -DOS_WIN -D_MBCS -DWIN64)
endif()

include_directories(${PROJECT_SOURCE_DIR})
include_directories(${CMAKE_BINARY_DIR})

# Main library source code

set(SOURCES
        snappy.cc
        snappy-sinksource.cc
        snappy-stubs-internal.cc
        snappy-c.cc)

if(WIN32)
  set(SNAPPY_STATIC_LIB snappylib${ARTIFACT_SUFFIX})
endif()

add_library(${SNAPPY_STATIC_LIB} STATIC ${SOURCES})

if(WIN32)
  if(MSVC)
    set_target_properties(${SNAPPY_STATIC_LIB} PROPERTIES
      COMPILE_FLAGS "/Fd${CMAKE_CFG_INTDIR}/${SNAPPY_STATIC_LIB}.pdb"
      COMPILE_DEFINITIONS "SNAPPY_STATIC_LIB")
  endif()
endif()

set(TESTS
        snappy-test.cc
        snappy_unittest.cc)

add_executable(snappy_test${ARTIFACT_SUFFIX} ${TESTS})
set_target_properties(snappy_test${ARTIFACT_SUFFIX}
      PROPERTIES EXCLUDE_FROM_ALL 1
                 EXCLUDE_FROM_DEFAULT_BUILD 1)
target_link_libraries(snappy_test${ARTIFACT_SUFFIX} snappylib${ARTIFACT_SUFFIX})
