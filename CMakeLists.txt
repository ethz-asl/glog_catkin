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
  SVN_REPOSITORY http://google-glog.googlecode.com/svn/trunk/
  UPDATE_COMMAND ""
  CONFIGURE_COMMAND cd ../glog_src && ./configure --with-pic 
    --with-gflags=${CATKIN_DEVEL_PREFIX} 
    --prefix=${CATKIN_DEVEL_PREFIX} && ${PATCH_MAKEFILE}
  BUILD_COMMAND cd ../glog_src && make -j8
  INSTALL_COMMAND cd ../glog_src && make install -j8
)

link_directories( ${CATKIN_DEVEL_PREFIX}/lib )
cs_add_library(${PROJECT_NAME}_dependency_tracker src/dependency_tracker.cc)
add_dependencies(${PROJECT_NAME}_dependency_tracker glog_src)
target_link_libraries(${PROJECT_NAME}_dependency_tracker glog)

cs_install()

cs_export(INCLUDE_DIRS ${CATKIN_DEVEL_PREFIX}/include
  CFG_EXTRAS glog-extras.cmake)
