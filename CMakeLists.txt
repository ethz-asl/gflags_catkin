cmake_minimum_required(VERSION 2.8.3)
project(gflags_catkin)

find_package(catkin_simple REQUIRED)
catkin_simple()

include(ExternalProject)

file(MAKE_DIRECTORY ${CATKIN_DEVEL_PREFIX}/include)
ExternalProject_Add(gflags_src
  GIT_REPOSITORY https://code.google.com/p/gflags/
  UPDATE_COMMAND ""
  CMAKE_ARGS -DCMAKE_INSTALL_PREFIX:PATH=${CATKIN_DEVEL_PREFIX}
  -DBUILD_SHARED_LIBS:BOOL=true
  -DGFLAGS_NAMESPACE:STRING=google -DCMAKE_BUILD_TYPE:STRING=Release
)


cs_add_library(${PROJECT_NAME} src/dependency_tracker.cc)
add_dependencies(${PROJECT_NAME} gflags_src)
target_link_libraries(${PROJECT_NAME} 
  ${CATKIN_DEVEL_PREFIX}/lib/libgflags${CMAKE_SHARED_LIBRARY_SUFFIX})

cs_install()

cs_export(INCLUDE_DIRS ${CATKIN_DEVEL_PREFIX}/include
  CFG_EXTRAS gflags-extras.cmake)
