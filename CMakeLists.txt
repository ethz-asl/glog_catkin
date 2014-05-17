cmake_minimum_required(VERSION 2.8.3)
project(glog_catkin)

find_package(catkin_simple REQUIRED)
catkin_simple()

message("gflags_catkin_PREFIX: ${gflags_catkin_PREFIX}")
message("gflags_catkin_LIBRARIES: ${gflags_catkin_LIBRARIES}")

include(ExternalProject)

# We don't know why catkin doesn't make this directory
file(MAKE_DIRECTORY ${CATKIN_DEVEL_PREFIX}/include)

# On OSX, with gtest installed by homebrew,
# the configure script comes up with the wrong
# answer for the extention of the gtest library.
# Patching the Makefile fixes this.
set(PATCH_MAKEFILE echo "Nothing to patch")
if(APPLE)
  #set(PATCH_MAKEFILE echo "-- Patching for OSX")# && sed -i.bu "s/1.11/1.14/g" Makefile Makefile.am Makefile.in config.log config.status)
endif()

ExternalProject_Add(glog_src
  URL http://robotics.ethz.ch/files/glog-2014-05-16.tgz
  UPDATE_COMMAND ""
  CONFIGURE_COMMAND cd ../glog_src && ./configure --with-pic 
    --with-gflags=${gflags_catkin_PREFIX}
    --prefix=${CATKIN_DEVEL_PREFIX} && ${PATCH_MAKEFILE}
  BUILD_COMMAND cd ../glog_src && ${PATCH_MAKEFILE} && make 
  INSTALL_COMMAND cd ../glog_src && ${PATCH_MAKEFILE} && make install 
)

cs_add_library(${PROJECT_NAME} src/dependency_tracker.cc)
add_dependencies(${PROJECT_NAME} glog_src)
target_link_libraries(${PROJECT_NAME} ${CATKIN_DEVEL_PREFIX}/lib/libglog${CMAKE_SHARED_LIBRARY_SUFFIX})

cs_install()

cs_export(INCLUDE_DIRS ${CATKIN_DEVEL_PREFIX}/include
  CFG_EXTRAS glog-extras.cmake)
