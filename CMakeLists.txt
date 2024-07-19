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
aux_source_directory(${CMAKE_CURRENT_SOURCE_DIR}/example example_srcs)
add_executable(example ${example_srcs} ${common_srcs})
if(MSVC)
    add_definitions(-DNOMINMAX)
    set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} /SAFESEH:NO")
endif()

target_include_directories(example PRIVATE example/common)

target_link_libraries(MindEyeBase sndfile OpenAL32)
target_link_libraries(example MindEyeBase)