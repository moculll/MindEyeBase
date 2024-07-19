cmake_minimum_required(VERSION 3.12)
set(CMAKE_CXX_STANDARD 14)
project(MindEyeBase)


# MindEyeBase library compile
aux_source_directory(${CMAKE_CURRENT_SOURCE_DIR}/core core)
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/include)
add_library(MindEyeBase STATIC ${core})

# x86/x64 output
if(CMAKE_SIZEOF_VOID_P EQUAL 8)
    set(OUTPUT_DIR ${CMAKE_CURRENT_SOURCE_DIR}/output/x64)
else()
    set(OUTPUT_DIR ${CMAKE_CURRENT_SOURCE_DIR}/output/x86)
endif()
set_target_properties(MindEyeBase PROPERTIES ARCHIVE_OUTPUT_DIRECTORY ${OUTPUT_DIR})

# example executable file compile
add_executable(example example/main.cpp)
target_link_libraries(example MindEyeBase)