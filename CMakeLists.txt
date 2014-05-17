cmake_minimum_required(VERSION 2.8.3)
project(glog_catkin)

find_package(catkin_simple REQUIRED)
catkin_simple()

include(ExternalProject)

# We don't know why catkin doesn't make this directory
file(MAKE_DIRECTORY ${CATKIN_DEVEL_PREFIX}/include)

ExternalProject_Add(glog_src
  URL http://robotics.ethz.ch/files/glog-2014-05-16.tgz
  UPDATE_COMMAND ""
  CONFIGURE_COMMAND cd ../glog_src && ./configure --with-pic 
    --with-gflags=${gflags_catkin_PREFIX}
    --prefix=${CATKIN_DEVEL_PREFIX}
  BUILD_COMMAND cd ../glog_src && make -j 8
  INSTALL_COMMAND cd ../glog_src && make install -j 8
)

cs_add_library(${PROJECT_NAME} src/dependency_tracker.cc)
add_dependencies(${PROJECT_NAME} glog_src)
target_link_libraries(${PROJECT_NAME} ${CATKIN_DEVEL_PREFIX}/lib/libglog${CMAKE_SHARED_LIBRARY_SUFFIX})

cs_install()

cs_export(INCLUDE_DIRS ${CATKIN_DEVEL_PREFIX}/include
  CFG_EXTRAS glog-extras.cmake)
