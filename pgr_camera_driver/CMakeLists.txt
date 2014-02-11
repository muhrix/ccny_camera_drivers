project(pgr_camera_driver)

find_package(catkin REQUIRED COMPONENTS 
  dynamic_reconfigure message_generation roscpp 
  tf image_transport camera_info_manager polled_camera
)

generate_dynamic_reconfigure_options(cfg/PGRCamera.cfg)

catkin_package(
  INCLUDE_DIRS include
  LIBRARIES fiducials
  DEPENDS OpenCV
)

include_directories(include 
  /usr/include/flycapture
  ${catkin_INCLUDE_DIRS})

add_library(libpgr_camera_driver
  src/libpgrcam/pgr_camera.cpp)

add_executable(pgr_camera_node
  src/nodes/pgr_camera_node.cpp)

target_link_libraries(pgr_camera_node
  libpgr_camera_driver
  flycapture pthread
  ${catkin_LIBRARIES})

add_dependencies(pgr_camera_node
  ${${PROJECT_NAME}_EXPORTED_TARGETS})

install(PROGRAMS
  pgr_camera_node
  DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)
