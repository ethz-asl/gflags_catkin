cmake_minimum_required(VERSION 2.8.3)
project(gflags_catkin)

find_package(catkin_simple REQUIRED)
catkin_simple()

include(ExternalProject)

ExternalProject_Add(gflags
  SVN_REPOSITORY http://gflags.googlecode.com/svn/trunk/
  CONFIGURE_COMMAND cd ../gflags && ./autogen.sh && ./configure --with-pic GFLAGS_NAMESPACE=gflags --prefix=${CATKIN_DEVEL_PREFIX}
  BUILD_COMMAND cd ../gflags && make -j8
  INSTALL_COMMAND cd ../gflags && make install -j8
)

cs_add_library(${PROJECT_NAME}_dependency_tracker src/dependency_tracker.cc)
add_dependencies(${PROJECT_NAME}_dependency_tracker gflags)

cs_install()

cs_export(INCLUDE_DIRS ${CATKIN_DEVEL_PREFIX}/include)
