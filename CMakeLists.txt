# Set the minimum cmake version
cmake_minimum_required(VERSION 3.10.0)

# set the project name and version
project(Engine3d VERSION 0.1.0)

# set the source directory environment variable
set(SRC_DIR ${PROJECT_SOURCE_DIR}/src)

# set the c++ standard
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED True)

# find all source and header files in the src directory
file(GLOB_RECURSE SOURCE_FILES ${SRC_DIR}/*.cpp ${SRC_DIR}/*.c)
file(GLOB_RECURSE HEADER_FILES ${SRC_DIR}/*.h)

# setup the cmake module path
set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${PROJECT_SOURCE_DIR}/cmake")

# create the CMakeConfig.h file
configure_file(${PROJECT_SOURCE_DIR}/cmake/CMakeConfig.h.in ${SRC_DIR}/CMakeConfig.h)

# find and add the GLFW3 package
find_package(GLFW3 REQUIRED)
set(LIBS ${LIBS} ${GLFW3_LIBRARY})

# Add other platform dependent stuff, mostly libraries
if (WIN32) # if we are on windows
    message("Windows detected")
    set(LIBS ${LIBS} opengl32) # add the needed libraries to the LIBS variable

elseif(UNIX AND NOT APPLE) # if we are on unix
    message("Unix detected")
    set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUF} -Wall")
    
    # Add Opengl to the build
    find_package(OpenGL REQUIRED)
    add_definitions(${OPENGL_DEFINITIONS})
    
    set(LIBS ${LIBS} GL)
else()
    set(LIBS )
endif(WIN32)

# build and add GLAD
add_library(GLAD ${PROJECT_SOURCE_DIR}/ext/glad/glad.c)
target_link_libraries(GLAD ${CMAKE_DL_LIBS})
set(LIBS ${LIBS} GLAD)

# set the include directories
include_directories(${SRC_DIR} ${PROJECT_SOURCE_DIR}/ext ${GLFW3_INCLUDE_DIR})

# generate the executable for the project
add_executable(${PROJECT_NAME} ${HEADER_FILES} ${SOURCE_FILES})

# link the libraries set in the libs variable
target_link_libraries(${PROJECT_NAME} ${LIBS})