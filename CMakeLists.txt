cmake_minimum_required(VERSION 3.29)
project(ffmpeg_command)

set(CMAKE_CXX_STANDARD 20)

file(GLOB FFLIBS_SRC
        src/libffmpeg/*.c
)

file(GLOB FFTOOLS_SRC
        src/fftools/command.c
)

include_directories(
        /usr/local/ffmpeg-7.1/include
        include/fftools
        include
)

link_directories(
        /usr/local/ffmpeg-7.1/lib
)

add_library(ffmpeg SHARED
        ${FFLIBS_SRC}
)

add_executable(ffmpeg_command ${FFTOOLS_SRC})

target_link_libraries(ffmpeg_command PRIVATE
        avcodec avdevice avfilter avformat avutil swresample swscale
        ffmpeg
)