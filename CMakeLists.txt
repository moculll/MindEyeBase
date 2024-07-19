cmake_minimum_required(VERSION 3.12)
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED True)
project(MindEyeBase)


# MindEyeBase library compile
aux_source_directory(${CMAKE_CURRENT_SOURCE_DIR}/core core)
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/include)
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/include/common)
add_library(MindEyeBase STATIC ${core})

# x86/x64 library link and output
if(CMAKE_SIZEOF_VOID_P EQUAL 8)
    link_directories(${CMAKE_CURRENT_SOURCE_DIR}/lib/x64)
    set(OUTPUT_DIR ${CMAKE_CURRENT_SOURCE_DIR}/output/x64)
else()
    link_directories(${CMAKE_CURRENT_SOURCE_DIR}/lib/x86)
    set(OUTPUT_DIR ${CMAKE_CURRENT_SOURCE_DIR}/output/x86)
endif()
set_target_properties(MindEyeBase PROPERTIES ARCHIVE_OUTPUT_DIRECTORY ${OUTPUT_DIR})

# example executable file compile
aux_source_directory(${CMAKE_CURRENT_SOURCE_DIR}/example/common common_srcs)

# set one example you'd like to compile
set(example_srcs ${CMAKE_CURRENT_SOURCE_DIR}/example/almultireverb.c)
#set(example_srcs ${CMAKE_CURRENT_SOURCE_DIR}/example/alconvolve.c)
#set(example_srcs ${CMAKE_CURRENT_SOURCE_DIR}/example/aldirect.cpp)
#set(example_srcs ${CMAKE_CURRENT_SOURCE_DIR}/example/alffplay.cpp)
#set(example_srcs ${CMAKE_CURRENT_SOURCE_DIR}/example/alhrtf.c)
#set(example_srcs ${CMAKE_CURRENT_SOURCE_DIR}/example/allatency.c)
#set(example_srcs ${CMAKE_CURRENT_SOURCE_DIR}/example/alloopback.c)
#set(example_srcs ${CMAKE_CURRENT_SOURCE_DIR}/example/alplay.c)
#set(example_srcs ${CMAKE_CURRENT_SOURCE_DIR}/example/alrecord.c)
#set(example_srcs ${CMAKE_CURRENT_SOURCE_DIR}/example/alreverb.c)
#set(example_srcs ${CMAKE_CURRENT_SOURCE_DIR}/example/alstream.c)
#set(example_srcs ${CMAKE_CURRENT_SOURCE_DIR}/example/alstreamcb.cpp)
#set(example_srcs ${CMAKE_CURRENT_SOURCE_DIR}/example/altonegen.c)

add_executable(example ${common_srcs} ${example_srcs})
if(MSVC)
    # disable windows default limits
    add_definitions(-DNOMINMAX)
    # fix errors
    target_compile_definitions(example PRIVATE strcasecmp=_stricmp AL_API_NOEXCEPT17=)
    
    set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} /SAFESEH:NO")
endif()

target_include_directories(example PRIVATE example/common)

# for sample alffplay linking necessary libs
list(FIND example_srcs ${CMAKE_CURRENT_SOURCE_DIR}/example/alffplay.cpp index)
if (index GREATER_EQUAL 0)
    target_link_libraries(MindEyeBase SDL2 swresample swscale postproc avutil avformat avfilter avdevice avcodec)
endif()
# for sample alloopback linking necessary libs
list(FIND example_srcs ${CMAKE_CURRENT_SOURCE_DIR}/example/alloopback.c index)
if (index GREATER_EQUAL 0)
    target_link_libraries(MindEyeBase SDL2)
endif()

target_link_libraries(MindEyeBase sndfile OpenAL32)
target_link_libraries(example MindEyeBase)