cmake_minimum_required(VERSION 2.8.3)
project(fmt_catkin)

find_package(catkin_simple REQUIRED)
catkin_simple(ALL_DEPS_REQUIRED)

include(ExternalProject)

file(MAKE_DIRECTORY ${CATKIN_DEVEL_PREFIX}/include/fmt)

ExternalProject_Add(fmt_src
	GIT_REPOSITORY https://github.com/fmtlib/fmt.git
	GIT_TAG 7.0.3
	PREFIX ${CMAKE_CURRENT_BINARY_DIR}/fmt
	CMAKE_ARGS -DCMAKE_INSTALL_PREFIX=${CATKIN_DEVEL_PREFIX}
	-DBUILD_SHARED_LIBS=TRUE
)

ExternalProject_Get_Property(fmt_src install_dir)
include_directories(${install_dir}/include)

cs_add_library(${PROJECT_NAME} src/dependency_tracker.cc)
add_dependencies(${PROJECT_NAME} fmt_src)
target_link_libraries(${PROJECT_NAME})
cs_install()

install(DIRECTORY ${CATKIN_DEVEL_PREFIX}/include/fmt
  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
  FILES_MATCHING PATTERN "*.hpp"
  PATTERN "*.h"
  PATTERN ".git" EXCLUDE
)

cs_export(INCLUDE_DIRS ${CATKIN_DEVEL_PREFIX}/include/
	# CFG_EXTRAS g2o-extras.cmake
	LIBRARIES fmt
)
