cmake_minimum_required(VERSION 2.8.3)
project(glog_catkin)

find_package(catkin_simple REQUIRED)
catkin_simple()

include(ExternalProject)

ExternalProject_Add(glog
  SVN_REPOSITORY http://google-glog.googlecode.com/svn/trunk/
  UPDATE_COMMAND ""
  CONFIGURE_COMMAND cd ../glog && ./configure --with-pic --with-gflags=${CATKIN_DEVEL_PREFIX} --prefix=${CATKIN_DEVEL_PREFIX}
  BUILD_COMMAND cd ../glog && make -j8
  INSTALL_COMMAND cd ../glog && make install -j8
)

cs_add_library(${PROJECT_NAME}_dependency_tracker src/dependency_tracker.cc)
add_dependencies(${PROJECT_NAME}_dependency_tracker glog)
target_link_libraries(${PROJECT_NAME}_dependency_tracker 
  ${CATKIN_DEVEL_PREFIX}/lib/libglog.so)

cs_install()

cs_export(INCLUDE_DIRS ${CATKIN_DEVEL_PREFIX}/include)
