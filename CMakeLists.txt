cmake_minimum_required(VERSION 2.8.3)
project(glog_catkin)

find_package(catkin REQUIRED COMPONENTS gflags_catkin)

include(ExternalProject)

file(MAKE_DIRECTORY ${CATKIN_DEVEL_PREFIX}/include)

set(VERSION 0.3.5)
ExternalProject_Add(glog_src
  URL https://github.com/google/glog/archive/v${VERSION}.zip
  UPDATE_COMMAND ""
  PATCH_COMMAND patch -p0 < ${CMAKE_SOURCE_DIR}/fix-unused-typedef-warning.patch
  CONFIGURE_COMMAND cd ../glog_src/ && autoreconf -fi && ./configure --with-pic
    --with-gflags=${gflags_catkin_PREFIX}
    --prefix=${CATKIN_DEVEL_PREFIX}
  BUILD_COMMAND cd ../glog_src/ && make -j 8
  INSTALL_COMMAND cd ../glog_src/ && make install -j 8
)

include_directories(include ${CATKIN_DEVEL_PREFIX}/include ${catkin_INCLUDE_DIRS})
catkin_package(
  INCLUDE_DIRS ${CATKIN_DEVEL_PREFIX}/include ${catkin_INCLUDE_DIRS}
  LIBRARIES glog)
install(DIRECTORY ${CATKIN_DEVEL_PREFIX}/include/glog DESTINATION ${CATKIN_GLOBAL_INCLUDE_DESTINATION}
        FILES_MATCHING PATTERN "*.h")
install(DIRECTORY ${CATKIN_DEVEL_PREFIX}/lib/
        DESTINATION ${CATKIN_GLOBAL_LIB_DESTINATION}
        FILES_MATCHING PATTERN "libglog*")
