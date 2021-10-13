cmake_minimum_required(VERSION 2.4.6)
project(ORB_SLAM)

#记得要在电脑里安装opencv2.4.13按本

add_compile_options(-std=c++17)
# 增加这一行，这样在WSL就能舒服的执行了
set(CMAKE_PREFIX_PATH /opt/ros/melodic)
set(ROS_ROOT /opt/ros/melodic/share/ros)
set(CATKIN_WORKSPACES /opt/ros/melodic)

#include(${ROS_ROOT}/core/rosbuild/rosbuild.cmake)
#include($ENV{ROS_ROOT}/core/rosbuild/rosbuild.cmake)

find_package(catkin REQUIRED COMPONENTS
  rosconsole
  roscpp
  rosmsg
  rospy
  tf
  tf2
  image_transport
  cv_bridge
  message_generation
)

find_package(Boost REQUIRED COMPONENTS system)
catkin_package(
#  INCLUDE_DIRS include
#  LIBRARIES abc
#  CATKIN_DEPENDS other_catkin_pkg
#  DEPENDS system_lib
)
IF(NOT ROS_BUILD_TYPE)
  SET(ROS_BUILD_TYPE Release)
ENDIF()

MESSAGE("Build type: " ${ROS_BUILD_TYPE})

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS}  -Wall  -O3 -march=native ")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall  -O3 -march=native")

set(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin)

LIST(APPEND CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/cmake_modules)

find_package(OpenCV 2.4.13 REQUIRED EXACT)
find_package(Eigen3 3.1.1 REQUIRED EXACT)

include_directories(
 include
 ${catkin_INCLUDE_DIRS} #为了include<ros/ros.h>
)

include_directories(
${PROJECT_SOURCE_DIR}
${EIGEN3_INCLUDE_DIR}
)

add_executable(${PROJECT_NAME}
src/main.cc
src/Tracking.cc
src/LocalMapping.cc
src/LoopClosing.cc
src/ORBextractor.cc
src/ORBmatcher.cc
src/FramePublisher.cc
src/Converter.cc
src/MapPoint.cc
src/KeyFrame.cc
src/Map.cc
src/MapPublisher.cc
src/Optimizer.cc
src/PnPsolver.cc
src/Frame.cc
src/KeyFrameDatabase.cc
src/Sim3Solver.cc
src/Initializer.cc
)
#rosbuild_add_boost_directories()
#rosbuild_link_boost(${PROJECT_NAME} thread)
message(${PROJECT_SOURCE_DIR})
target_link_libraries(${PROJECT_NAME}
${catkin_LIBRARIES}
${OpenCV_LIBS}
${EIGEN3_LIBS}
${PROJECT_SOURCE_DIR}/Thirdparty/DBoW2/lib/libDBoW2.so
${PROJECT_SOURCE_DIR}/Thirdparty/g2o/lib/libg2o.so
${BOOST_LIBS}
)

return()
#可以用来debug输出所有的cmake变量
get_cmake_property(_variableNames VARIABLES)
foreach (_variableName ${_variableNames})
    message(STATUS "${_variableName}=${${_variableName}}")
endforeach()
#输出所有环境变量
execute_process(COMMAND "${CMAKE_COMMAND}" "-E" "environment")