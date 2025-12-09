# CMakeList.txt : src directory cmake file for including all subdirectories.
#
cmake_minimum_required (VERSION 3.30)

# Include subdirectories
add_subdirectory("Calendar")
add_subdirectory("Clock")
add_subdirectory("common")
add_subdirectory("DLL")
add_subdirectory("options")
