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

if (MSVC)
  set(CMAKE_MSVC_RUNTIME_LIBRARY "MultiThreaded$<$<CONFIG:Debug>:Debug>" CACHE INTERNAL "" FORCE)
endif()

set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${PROJECT_SOURCE_DIR}/${CMAKE_BUILD_TYPE})

# Ensure static libraries are used
set(BUILD_SHARED_LIBS OFF)  # Prefer static libraries over shared one

set(CMAKE_C_STANDARD 17)
set(CMAKE_C_STANDARD_REQUIRED ON)

# Define the installation directory
set(CMAKE_INSTALL_PREFIX "${CMAKE_BINARY_DIR}/bin/T-Clock")  # Install to a directory named "T-Clock" inside the build directory

add_compile_definitions(
  _WIN32_WINNT=0x0A00
  WINVER=0x0A00
  UNICODE
  LOGGING
)

# Add the src subdirectory
add_subdirectory(src)

# Install the executables and DLL
install(TARGETS Clock DESTINATION .)
install(TARGETS common DESTINATION .)
install(TARGETS Options DESTINATION misc)
install(TARGETS T-Clock DESTINATION misc)
install(TARGETS XPCalendar DESTINATION misc)

# Install the common library
install(TARGETS common DESTINATION ${CMAKE_INSTALL_PREFIX}/lib)