# CMakeList.txt : CMake for "common" files
#
cmake_minimum_required (VERSION 3.30)

set (TARGET_NAME "Clock")

#Handle windows backslashes

# Add source to this project's executable.
set (SOURCE_FILES 
  "alarm.c"
  "alarmday.c"
  "BounceWind.c"
  "ExitWindows.c"
  "main.c"
  "menu.c"
  "mouse.c"
  "pageabout.c"
  "pagealarm.c"
  "pagecolor.c"
  "pageformat.c"
  "PageHotKey.c"
  "PageMisc.c"
  "PageMouse.c"
  "PageQuicky.c"
  "PageQuickyMenu.c"
  "propsheet.c"
  "settings.c"
  "SNTP.c"
  "soundselect.c"
  "StopWatch.c"
  "timer.c"
)

add_executable (${TARGET_NAME} WIN32 ${SOURCE_FILES})

add_custom_command(
    TARGET ${TARGET_NAME}
    PRE_BUILD
    COMMAND ${CMAKE_COMMAND} -E rm -f "$<TARGET_FILE:${TARGET_NAME}>"
    COMMENT "Deleting existing .exe file before building"
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

set_property(TARGET ${TARGET_NAME} PROPERTY CXX_STANDARD 26)

# Include paths (NOTE - not necessary for this folder as all includes are in the same directory.
target_include_directories(${TARGET_NAME} 
  PRIVATE ${CMAKE_CURRENT_SOURCE_DIR}/../common
)

target_link_libraries(${TARGET_NAME} 
  PRIVATE common
  PRIVATE comctl32 dsound mpr msimg32 shlwapi winmm ws2_32 wtsapi32  # Windows libraries
)
