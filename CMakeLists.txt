cmake_minimum_required(VERSION 2.8.3)
project(gflags_catkin)

find_package(catkin_simple REQUIRED)
catkin_simple()

include(ExternalProject)

# We can't check version numbers easily as the Gflags script doesn't export them.
# For reference we use the script that comes with ROS's cmake_modules:
# https://github.com/ros/cmake_modules/blob/0.5-devel/cmake/Modules/FindGflags.cmake

# If set to OFF, will compile the given version of Gflags.
# If set to ON, will use the system version of Gflags.
# If set to AUTO, will try to use the syste version if there is one, and build otherwise.
set(USE_SYSTEM_GFLAGS "AUTO" CACHE INTERNAL "Whether to use the system version of Gflags.")

# Check if the version is suitable, otherwise force downloading the newer one.
if(USE_SYSTEM_GFLAGS STREQUAL "AUTO")
  find_package(Gflags QUIET)
  if(Gflags_FOUND)
    set(USE_SYSTEM_GFLAGS "ON")
    message(STATUS "Suitable Gflags version found.")
  else()
    set(USE_SYSTEM_GFLAGS "OFF")
    message(STATUS "No suitable Gflags version found.")
  endif()
endif()


if(USE_SYSTEM_GFLAGS STREQUAL "OFF")
  message(STATUS "Installing Gflags from source!")

  file(MAKE_DIRECTORY ${CATKIN_DEVEL_PREFIX}/include)

  ExternalProject_Add(
    gflags_src
    URL https://github.com/gflags/gflags/archive/v2.2.1.zip
    URL_MD5 2d988ef0b50939fb50ada965dafce96b
    UPDATE_COMMAND ""
    CONFIGURE_COMMAND cd ../gflags_src &&
       cmake .
         -DCMAKE_INSTALL_PREFIX:PATH=${CATKIN_DEVEL_PREFIX}
         -DBUILD_SHARED_LIBS:BOOL=true
         -DGFLAGS_NAMESPACE:STRING=google
         -DCMAKE_BUILD_TYPE:STRING=Release
         -DCMAKE_TOOLCHAIN_FILE:STRING=${CMAKE_TOOLCHAIN_FILE}
    BUILD_COMMAND cd ../gflags_src && make -j 8
    INSTALL_COMMAND cd ../gflags_src && make install -j 8
  )

  install(DIRECTORY ${CATKIN_DEVEL_PREFIX}/include/gflags
          DESTINATION ${CATKIN_GLOBAL_INCLUDE_DESTINATION}
          FILES_MATCHING PATTERN "*.h")
  install(DIRECTORY ${CATKIN_DEVEL_PREFIX}/lib/
          DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
          FILES_MATCHING PATTERN "libgflags*")
  install(FILES ${CATKIN_DEVEL_PREFIX}/bin/gflags_completions.sh
          PERMISSIONS OWNER_READ OWNER_WRITE OWNER_EXECUTE GROUP_READ GROUP_EXECUTE WORLD_READ WORLD_EXECUTE
          DESTINATION ${CATKIN_GLOBAL_BIN_DESTINATION})
  cs_export(INCLUDE_DIRS ${CATKIN_DEVEL_PREFIX}/include
            LIBRARIES gflags)
else()
  message(STATUS "Using system Gflags.")
  find_package(Gflags REQUIRED)
  cs_install()
  cs_export(INCLUDE_DIRS  ${Gflags_INCLUDE_DIRS}
            LIBRARIES     ${Gflags_LIBRARIES})
endif()
