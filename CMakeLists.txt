cmake_minimum_required(VERSION 2.8.3)
project(gflags_catkin)

find_package(catkin_simple REQUIRED)
catkin_simple()

include(ExternalProject)

file(MAKE_DIRECTORY ${CATKIN_DEVEL_PREFIX}/include)

ExternalProject_Add(
  gflags_src
  URL https://github.com/gflags/gflags/archive/v2.1.2.zip
  URL_MD5 5cb0a1b38740ed596edb7f86cd5b3bd8
  UPDATE_COMMAND ""
  CONFIGURE_COMMAND cd ../gflags_src &&
     cmake .
       -DCMAKE_INSTALL_PREFIX:PATH=${CATKIN_DEVEL_PREFIX}
       -DBUILD_SHARED_LIBS:BOOL=true
       -DGFLAGS_NAMESPACE:STRING=google
       -DCMAKE_BUILD_TYPE:STRING=Release
  BUILD_COMMAND cd ../gflags_src && make -j 8
  INSTALL_COMMAND cd ../gflags_src && make install -j 8
)

install(DIRECTORY ${CATKIN_DEVEL_PREFIX}/include/gflags
        DESTINATION ${CATKIN_GLOBAL_INCLUDE_DESTINATION}
        FILES_MATCHING PATTERN "*.h")
install(DIRECTORY ${CATKIN_DEVEL_PREFIX}/lib/
        DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
        FILES_MATCHING PATTERN "libgflags*")
cs_export(INCLUDE_DIRS ${CATKIN_DEVEL_PREFIX}/include
          LIBRARIES gflags)
