cmake_minimum_required(VERSION 3.29)
project(ffmpeg_command)

set(CMAKE_C_STANDARD 11)

set(FFMPEG_HOME /usr/local/ffmpeg-7.1.1)

configure_file(
        ${PROJECT_SOURCE_DIR}/include/config.h.in
        ${PROJECT_BINARY_DIR}/include/config.h
        @ONLY  # 仅替换 @VAR_NAME@ 格式的变量
)

link_directories(
        ${FFMPEG_HOME}/lib
)

file(GLOB FFLIBS_SRC
        src/libffmpeg/*.c
)

file(GLOB FFTOOLS_SRC
        src/fftools/command.c
)

add_library(ffmpeg SHARED
        ${FFLIBS_SRC}
)

add_executable(ffmpeg_command ${FFTOOLS_SRC})

target_include_directories(ffmpeg
        PUBLIC
            ${PROJECT_BINARY_DIR}/include
            ${FFMPEG_HOME}/include
            include
        PRIVATE
            include/fftools
)

target_link_libraries(ffmpeg_command PRIVATE
        avcodec avdevice avfilter avformat avutil swresample swscale
        ffmpeg
)

install(TARGETS ffmpeg_command
        LIBRARY DESTINATION lib
        RUNTIME DESTINATION bin
)

install(DIRECTORY include/
        DESTINATION include
)