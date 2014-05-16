cmake_minimum_required(VERSION 2.8.3)
project(glog_catkin)

find_package(catkin_simple REQUIRED)
catkin_simple()

include(ExternalProject)

# On OSX, with gtest installed by homebrew,
# the configure script comes up with the wrong
# answer for the extention of the gtest library.
# Patching the Makefile fixes this.
set(PATCH_MAKEFILE echo "Nothing to patch")
if(APPLE)
  set(PATCH_MAKEFILE sed -i.bu "s/libgtest.la/libgtest.dylib/g" Makefile)
endif()

ExternalProject_Add(glog_src
  URL https://google-glog.googlecode.com/files/glog-0.3.3.tar.gz
  UPDATE_COMMAND ""
  CONFIGURE_COMMAND cd ../glog_src && ./configure --with-pic 
    --with-gflags=${CATKIN_DEVEL_PREFIX} 
    --prefix=${CATKIN_DEVEL_PREFIX} && ${PATCH_MAKEFILE}
  BUILD_COMMAND cd ../glog_src && ${PATCH_MAKEFILE} && make -j8
  INSTALL_COMMAND cd ../glog_src && ${PATCH_MAKEFILE} && make install -j8
)

cs_add_library(${PROJECT_NAME} src/dependency_tracker.cc)
add_dependencies(${PROJECT_NAME} glog_src)
target_link_libraries(${PROJECT_NAME}
  ${CATKIN_DEVEL_PREFIX}/lib/libglog${CMAKE_SHARED_LIBRARY_SUFFIX})

cs_install()

cs_export(INCLUDE_DIRS ${CATKIN_DEVEL_PREFIX}/include
  CFG_EXTRAS glog-extras.cmake)
