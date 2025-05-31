cmake_minimum_required(VERSION 3.16)


# set the project name
project(codec-alac)

# lots of warnings and all warnings as errors
## add_compile_options(-Wall -Wextra )
set(CMAKE_CXX_STANDARD 17)

# define source code
file(GLOB_RECURSE SRC_LIST_C CONFIGURE_DEPENDS  "${PROJECT_SOURCE_DIR}/src/*.c" )
file(GLOB_RECURSE SRC_LIST_CPP CONFIGURE_DEPENDS  "${PROJECT_SOURCE_DIR}/src/*.cpp" )

# define as library
add_library (codec-alac ${SRC_LIST_C} ${SRC_LIST_CPP})

# define location for header files
target_include_directories(codec-alac PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/src ${CMAKE_CURRENT_SOURCE_DIR}/src/alac/codec )

# disable multi-character character constant warning
target_compile_options(codec-alac PUBLiC -Wno-multichar)