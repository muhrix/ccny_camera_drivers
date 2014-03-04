cmake_minimum_required(VERSION 2.8.3)

set(TARGET ${CMAKE_HOST_SYSTEM_PROCESSOR})

project(mv_bluefox_driver)
find_package(catkin REQUIRED COMPONENTS roscpp sensor_msgs image_transport camera_info_manager nodelet dynamic_reconfigure driver_base)

find_package(Boost REQUIRED COMPONENTS signals system)

catkin_package(
  INCLUDE_DIRS include
  LIBRARIES mv_bluefox_nodelet
  CATKIN_DEPENDS roscpp sensor_msgs image_transport camera_info_manager nodelet dynamic_reconfigure driver_base
#  DEPENDS system_lib
)

include_directories(
  include 
  ${catkin_INCLUDE_DIRS}
)

add_library(mvDeviceManager SHARED IMPORTED)
set_target_properties(mvDeviceManager PROPERTIES IMPORTED_LOCATION "${PROJECT_SOURCE_DIR}/lib/${TARGET}/libmvDeviceManager.so.2.6.1")

add_library(mv_bluefox_nodelet
  src/mv_bluefox_nodelet.cpp
  src/camera.cpp
)

add_executable(mv_bluefox_node src/mv_bluefox_node.cpp src/camera.cpp)

add_dependencies(mv_bluefox_nodelet ${catkin_EXPORTED_TARGETS} mvDeviceManager)
add_dependencies(mv_bluefox_node ${catkin_EXPORTED_TARGETS} mvDeviceManager)

target_link_libraries(mv_bluefox_nodelet
  mvDeviceManager
  ${catkin_LIBRARIES}
  ${Boost_LIBRARIES}
)

target_link_libraries(mv_bluefox_node
  mvDeviceManager
  ${catkin_LIBRARIES}
  ${Boost_LIBRARIES}
)

## Mark executables and/or libraries for installation
install(TARGETS mv_bluefox_nodelet mv_bluefox_node
  ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)

## Mark cpp header files for installation
install(DIRECTORY include/${PROJECT_NAME}/
  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
  FILES_MATCHING PATTERN "*.h"
)

## Mark other files for installation (e.g. launch and bag files, etc.)
# install(FILES
#   # myfile1
#   # myfile2
#   DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
# )

