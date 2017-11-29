cmake_minimum_required(VERSION 2.8.12)
project(optpp_catkin)

add_compile_options(-std=c++11)

find_package(catkin REQUIRED)

include(ExternalProject)

set(OPTPP_INSTALL ${CMAKE_BINARY_DIR}/optpp_install)
file(MAKE_DIRECTORY ${CATKIN_DEVEL_PREFIX}/include/${PROJECT_NAME})
file(MAKE_DIRECTORY include/${PROJECT_NAME})
file(MAKE_DIRECTORY ${CMAKE_INSTALL_PREFIX}/include/${PROJECT_NAME})

ExternalProject_Add(optpp
  URL http://terminator.robots.inf.ed.ac.uk/apt/optpp-2.4.tar.gz
  CONFIGURE_COMMAND <SOURCE_DIR>/configure --prefix=${OPTPP_INSTALL} --enable-w --enable-static=no --enable-shared=yes
  BUILD_COMMAND ${MAKE}
)

ExternalProject_Add_Step(optpp CopyToDevel
    COMMAND ${CMAKE_COMMAND} -E copy_directory ${OPTPP_INSTALL}/include/ ${CATKIN_DEVEL_PREFIX}/include/${PROJECT_NAME}
    COMMAND ${CMAKE_COMMAND} -E copy ${OPTPP_INSTALL}/../optpp-prefix/src/optpp-build/include/OPT++_config.h ${CATKIN_DEVEL_PREFIX}/include/${PROJECT_NAME}
    COMMAND ${CMAKE_COMMAND} -E copy_directory ${OPTPP_INSTALL}/lib/ ${CATKIN_DEVEL_PREFIX}/lib
    DEPENDEES install
)

catkin_package(
  INCLUDE_DIRS include ${CATKIN_DEVEL_PREFIX}/include include ${CMAKE_INSTALL_PREFIX}/include
  LIBRARIES opt newmat
  CFG_EXTRAS optpp_extra.cmake
)

install(
  DIRECTORY ${CATKIN_DEVEL_PREFIX}/include
  DESTINATION ${CMAKE_INSTALL_PREFIX}
)
install(
  FILES ${CATKIN_DEVEL_PREFIX}/lib/libopt.so
  ${CATKIN_DEVEL_PREFIX}/lib/libnewmat.so
  DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
)
