# CMakeList.txt : Top-level CMake project file, do global configuration
# and include sub-projects here.
#
cmake_minimum_required (VERSION 3.30)

set(CMAKE_POLICY_VERSION_MINIMUM 3.5)

# NOTE: All VCPKG settings must come before 'project' statement

#Handle windows backslashes
set(VCPKG_ROOT "$ENV{VCPKG_ROOT}")
string(REPLACE "\\" "/" VCPKG_ROOT "${VCPKG_ROOT}")
set(CMAKE_SOURCE_DIR "${CMAKE_SOURCE_DIR}")
string(REPLACE "\\" "/" CMAKE_SOURCE_DIR "${CMAKE_SOURCE_DIR}")

set(VCPKG_OVERLAY_PORTS ${CMAKE_SOURCE_DIR}/overlays)
set(VCPKG_TARGET_TRIPLET "x64-windows-static")
set(VCPKG_INSTALLED_DIR ${CMAKE_SOURCE_DIR}/packages CACHE PATH "Directory for vcpkg installed packages")
set(CMAKE_TOOLCHAIN_FILE ${VCPKG_ROOT}/scripts/buildsystems/vcpkg.cmake CACHE STRING "Vcpkg toolchain file")
set(VCPKG_VERBOSE ON)

project("T-Clock" CXX C)

#Handle windows backslashes
set(PROJECT_SOURCE_DIR "${PROJECT_SOURCE_DIR}")
string(REPLACE "\\" "/" PROJECT_SOURCE_DIR "${PROJECT_SOURCE_DIR}")

# Enable Hot Reload for MSVC compilers if supported.
if (POLICY CMP0141)
  cmake_policy(SET CMP0141 NEW)
  set(CMAKE_MSVC_DEBUG_INFORMATION_FORMAT "$<IF:$<AND:$<C_COMPILER_ID:MSVC>,$<CXX_COMPILER_ID:MSVC>>,$<$<CONFIG:Debug,RelWithDebInfo>:EditAndContinue>,$<$<CONFIG:Debug,RelWithDebInfo>:ProgramDatabase>>")
endif()

#set(Python3_EXECUTABLE "D:/Program Files/Python/Python312/python.exe")
set(CMAKE_Fortran_COMPILER "D:/Program Files/Intel/compiler/latest/windows/bin/ifx.exe")
set(FC "D:/Program Files/Intel/compiler/latest/windows/bin/ifx.exe")

if (MSVC)
  add_definitions(-D_UNICODE -DUNICODE)
  add_compile_options(/std:c++latest)
  set(CMAKE_MSVC_RUNTIME_LIBRARY "MultiThreaded$<$<CONFIG:Debug>:Debug>" CACHE INTERNAL "" FORCE)
endif()

set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${PROJECT_SOURCE_DIR}/${CMAKE_BUILD_TYPE})

find_package(Git QUIET)
if (EXISTS "${PROJECT_SOURCE_DIR}/.git")
  option(GIT_SUBMODULE "Check submodules during build" ON)
  if (GIT_SUBMODULE)
    message(STATUS "Submodule update")
    execute_process(COMMAND ${GIT_EXECUTABLE} submodule update --init --recursive
                    WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}
                    RESULT_VARIABLE GIT_SUBMOD_RESULT)
    if (NOT GIT_SUBMOD_RESULT EQUAL "0")
      message(FATAL_ERROR "git submodule update --init --recusrsive failed with ${}, please checkout submodules.")
    endif()
  endif()
endif()

# Ensure static libraries are used
set(BUILD_SHARED_LIBS OFF)  # Prefer static libraries over shared one

# Include subdirectories
add_subdirectory("src")
