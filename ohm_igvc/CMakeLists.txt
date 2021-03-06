cmake_minimum_required(VERSION 2.8.3)
project(ohm_igvc)

add_definitions(-std=c++11)

## Find catkin macros and libraries
## if COMPONENTS list like find_package(catkin REQUIRED COMPONENTS xyz)
## is used, also find other catkin packages
find_package(catkin REQUIRED COMPONENTS
  tf
  laser_geometry
  message_generation
  roscpp
  std_msgs
  isc_shared
  serial
)
find_package(OpenCV 3 REQUIRED)

## System dependencies are found with CMake's conventions
#find_package(Boost REQUIRED)


## Uncomment this if the package has a setup.py. This macro ensures
## modules and global scripts declared therein get installed
## See http://ros.org/doc/api/catkin/html/user_guide/setup_dot_py.html
# catkin_python_setup()

################################################
## Declare ROS messages, services and actions ##
################################################

## To declare and build messages, services or actions from within this
## package, follow these steps:
## * Let MSG_DEP_SET be the set of packages whose message types you use in
##   your messages/services/actions (e.g. std_msgs, actionlib_msgs, ...).
## * In the file package.xml:
##   * add a build_depend tag for "message_generation"
##   * add a build_depend and a run_depend tag for each package in MSG_DEP_SET
##   * If MSG_DEP_SET isn't empty the following dependency has been pulled in
##     but can be declared for certainty nonetheless:
##     * add a run_depend tag for "message_runtime"
## * In this file (CMakeLists.txt):
##   * add "message_generation" and every package in MSG_DEP_SET to
##     find_package(catkin REQUIRED COMPONENTS ...)
##   * add "message_runtime" and every package in MSG_DEP_SET to
##     catkin_package(CATKIN_DEPENDS ...)
##   * uncomment the add_*_files sections below as needed
##     and list every .msg/.srv/.action file to be processed
##   * uncomment the generate_messages entry below
##   * add every package in MSG_DEP_SET to generate_messages(DEPENDENCIES ...)

## Generate messages in the 'msg' folder
add_message_files(
  FILES
  drive_mode.msg
  path_debug.msg
  map_cell.msg
  location_point.msg
  pid_feedback.msg
  pixel_locations.msg
  planned_path.msg
  target.msg
  position_update.msg
)

## Generate services in the 'srv' folder
add_service_files(
  FILES
  cell_to_real.srv
  get_successors.srv
  real_to_cell.srv
  robot_position.srv
  coordinate_convert.srv
  waypoint.srv
)

## Generate actions in the 'action' folder
# add_action_files(
#   FILES
#   Action1.action
#   Action2.action
# )

## Generate added messages and services with any dependencies listed here
generate_messages(
  DEPENDENCIES
  std_msgs
  isc_shared
  sensor_msgs
  geometry_msgs
)

################################################
## Declare ROS dynamic reconfigure parameters ##
################################################

## To declare and build dynamic reconfigure parameters within this
## package, follow these steps:
## * In the file package.xml:
##   * add a build_depend and a run_depend tag for "dynamic_reconfigure"
## * In this file (CMakeLists.txt):
##   * add "dynamic_reconfigure" to
##     find_package(catkin REQUIRED COMPONENTS ...)
##   * uncomment the "generate_dynamic_reconfigure_options" section below
##     and list every .cfg file to be processed

## Generate dynamic reconfigure parameters in the 'cfg' folder
# generate_dynamic_reconfigure_options(
#   cfg/DynReconf1.cfg
#   cfg/DynReconf2.cfg
# )

###################################
## catkin specific configuration ##
###################################
## The catkin_package macro generates cmake config files for your package
## Declare things to be passed to dependent projects
## INCLUDE_DIRS: uncomment this if you package contains header files
## LIBRARIES: libraries you create in this project that dependent projects also need
## CATKIN_DEPENDS: catkin_packages dependent projects also need
## DEPENDS: system dependencies of this project that dependent projects also need
catkin_package(
#  INCLUDE_DIRS include
#  LIBRARIES ohm_igvc
  CATKIN_DEPENDS laser_geometry message_runtime roscpp std_msgs isc_shared
#  DEPENDS system_lib
)

###########
## Build ##
###########

## Specify additional locations of header files
## Your package locations should be listed before other locations
include_directories(
  ${catkin_INCLUDE_DIRS}
  ${OpenCV_INCLUDE_DIRS}
)

## Declare a C++ library
# add_library(ohm_igvc
#   src/${PROJECT_NAME}/ohm_igvc.cpp
# )

## Add cmake target dependencies of the library
## as an example, code may need to be generated before libraries
## either from message generation or dynamic reconfigure
# add_dependencies(ohm_igvc ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})

## Declare a C++ executable
# add_executable(ohm_igvc_node src/ohm_igvc_node.cpp)

## Add cmake target dependencies of the executable
## same as for the library above
# add_dependencies(ohm_igvc_node ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})

## Specify libraries to link a library or executable target against
# target_link_libraries(ohm_igvc_node
#   ${catkin_LIBRARIES}
# )

# ADD NEW CPPs HERE
add_executable(auto_control_logic src/auto_control_logic.cpp)
add_dependencies(auto_control_logic ${PROJECT_NAME}_generate_messages_cpp)
target_link_libraries(auto_control_logic ${catkin_LIBRARIES})

add_executable(odometry src/odometry.cpp)
add_dependencies(odometry ${PROJECT_NAME}_generate_messages_cpp)
target_link_libraries(odometry ${catkin_LIBRARIES})

add_executable(ohm_drive_mode_control src/ohm_drive_mode_control.cpp)
add_dependencies(ohm_drive_mode_control ${PROJECT_NAME}_generate_messages_cpp)
target_link_libraries(ohm_drive_mode_control ${catkin_LIBRARIES})

add_executable(ohm_pid src/ohm_pid.cpp)
add_dependencies(ohm_pid ${PROJECT_NAME}_generate_messages_cpp)
target_link_libraries(ohm_pid ${catkin_LIBRARIES})

add_executable(waypoint_selection src/waypoint_selection.cpp)
add_dependencies(waypoint_selection ${PROJECT_NAME}_generate_messages_cpp)
target_link_libraries(waypoint_selection ${catkin_LIBRARIES})

add_executable(white_line_detection src/white_line_detection.cpp)
add_dependencies(white_line_detection ${PROJECT_NAME}_generate_messages_cpp)
target_link_libraries(white_line_detection ${catkin_LIBRARIES} ${OpenCV_LIBRARIES})

add_executable(grid_map src/grid_map_node.cpp)
add_dependencies(grid_map ${PROJECT_NAME}_generate_messages_cpp)
target_link_libraries(grid_map ${catkin_LIBRARIES} ${OpenCV_LIBRARIES})

add_executable(path_planner src/planner_node.cpp)
add_dependencies(path_planner ${PROJECT_NAME}_generate_messages_cpp)
target_link_libraries(path_planner ${catkin_LIBRARIES})

add_executable(obstacle_avoidance src/obstacle_avoidance.cpp src/lidarpoint.h src/obstacle.h)
add_dependencies(obstacle_avoidance ${PROJECT_NAME}_generate_messages_cpp)
target_link_libraries(obstacle_avoidance ${catkin_LIBRARIES})

add_executable(scan_to_xy src/scan_xy_node.cpp)
add_dependencies(scan_to_xy ${PROJECT_NAME}_generate_messages_cpp)
target_link_libraries(scan_to_xy ${catkin_LIBRARIES})

#############
## Install ##
#############

# all install targets should use catkin DESTINATION variables
# See http://ros.org/doc/api/catkin/html/adv_user_guide/variables.html

## Mark executable scripts (Python etc.) for installation
## in contrast to setup.py, you can choose the destination
# install(PROGRAMS
#   scripts/my_python_script
#   DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
# )

## Mark executables and/or libraries for installation
# install(TARGETS ohm_igvc ohm_igvc_node
#   ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
#   LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
#   RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
# )

## Mark cpp header files for installation
# install(DIRECTORY include/${PROJECT_NAME}/
#   DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
#   FILES_MATCHING PATTERN "*.h"
#   PATTERN ".svn" EXCLUDE
# )

## Mark other files for installation (e.g. launch and bag files, etc.)
# install(FILES
#   # myfile1
#   # myfile2
#   DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
# )

#############
## Testing ##
#############

## Add gtest based cpp test target and link libraries
# catkin_add_gtest(${PROJECT_NAME}-test test/test_ohm_igvc.cpp)
# if(TARGET ${PROJECT_NAME}-test)
#   target_link_libraries(${PROJECT_NAME}-test ${PROJECT_NAME})
# endif()

## Add folders to be run by python nosetests
# catkin_add_nosetests(test)
