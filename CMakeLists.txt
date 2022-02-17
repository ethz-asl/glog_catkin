cmake_minimum_required(VERSION 2.8.3)
project(glog_catkin)

find_package(catkin_simple REQUIRED)
catkin_simple()

include(ExternalProject)

file(MAKE_DIRECTORY ${CATKIN_DEVEL_PREFIX}/include)

set(VERSION 0.5.0)
ExternalProject_Add(glog_src
  URL https://github.com/google/glog/archive/v${VERSION}.zip
  UPDATE_COMMAND ""
  CONFIGURE_COMMAND cd ../glog_src/ && cmake -S . -B build -G "Unix Makefiles" -DCMAKE_INSTALL_PREFIX=${CATKIN_DEVEL_PREFIX} 
  BUILD_COMMAND cd ../glog_src/ && cmake --build build -j 8
  INSTALL_COMMAND cd ../glog_src/ && cmake --build build --target install
)

install(DIRECTORY ${CATKIN_DEVEL_PREFIX}/include/glog
        DESTINATION ${CATKIN_GLOBAL_INCLUDE_DESTINATION}
        FILES_MATCHING PATTERN "*.h")
install(DIRECTORY ${CATKIN_DEVEL_PREFIX}/lib/
        DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
        FILES_MATCHING PATTERN "libglog*")
cs_export(INCLUDE_DIRS ${CATKIN_DEVEL_PREFIX}/include
          LIBRARIES glog)
