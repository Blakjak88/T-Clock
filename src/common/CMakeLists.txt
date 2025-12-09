# CMakeList.txt : CMake for "common" files
#
cmake_minimum_required (VERSION 3.30)

set (TARGET_NAME "common")

#Handle windows backslashes

# Add source to this project's executable.
set (SOURCE_FILES 
  "clock.c"
  "control_extensions.c"
  "getopt_tools.c"
  "HaveSetTimePerms.c"
  "messagebox_custom.c"
  "newapi.c"
  "utl.c"
  #"win2k_compat.c"
)

add_library (${TARGET_NAME} ${SOURCE_FILES})

add_custom_command(
    TARGET ${TARGET_NAME}
    PRE_BUILD
    COMMAND ${CMAKE_COMMAND} -E rm -f "$<TARGET_FILE:${TARGET_NAME}>"
    COMMENT "Deleting existing .lib file before building"
)

# Setup pre-compiled headers
target_precompile_headers(${TARGET_NAME} PRIVATE
  "$<$<COMPILE_LANGUAGE:CXX>:${CMAKE_CURRENT_SOURCE_DIR}/pre_cpp.h>"
)

# Setup pre-compiled headers
target_precompile_headers(${TARGET_NAME} PRIVATE
  "$<$<COMPILE_LANGUAGE:C>:${CMAKE_CURRENT_SOURCE_DIR}/pre_c.h>"
)

target_compile_options(${TARGET_NAME} 
  PRIVATE /MP16
)

# Include paths (NOTE - not necessary for this folder as all includes are in the same directory.
#target_include_directories(${TARGET_NAME} 
#  PRIVATE ${CMAKE_CURRENT_SOURCE_DIR}
#)

set_property(TARGET ${TARGET_NAME} PROPERTY CXX_STANDARD 26)
