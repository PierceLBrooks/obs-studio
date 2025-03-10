cmake_minimum_required(VERSION 3.28...3.30)

find_package(Libx264 REQUIRED)

add_subdirectory(h264bitstream)

if(NOT TARGET OBS::opts-parser)
  add_subdirectory("${CMAKE_SOURCE_DIR}/shared/opts-parser" "${CMAKE_BINARY_DIR}/shared/opts-parser")
endif()

add_library(obs-x264 MODULE)
add_library(OBS::x264 ALIAS obs-x264)

target_sources(obs-x264 PRIVATE obs-x264.c obs-x264-plugin-main.c)
target_link_libraries(obs-x264 PRIVATE OBS::opts-parser Libx264::Libx264 h264bitstream)
target_include_directories(obs-x264 PRIVATE "${CMAKE_CURRENT_LIST_DIR}/h264bitstream")

if(OS_WINDOWS)
  configure_file(cmake/windows/obs-module.rc.in obs-x264.rc)
  target_sources(obs-x264 PRIVATE obs-x264.rc)
endif()

set_target_properties_obs(obs-x264 PROPERTIES FOLDER plugins/obs-x264 PREFIX "")

include(cmake/x264-test.cmake)
