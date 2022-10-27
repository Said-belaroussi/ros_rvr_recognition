cmake_minimum_required(VERSION 3.0.2)
project(rvr_recognition)

find_package(catkin REQUIRED COMPONENTS
  roscpp
  rospy
  std_msgs
  geometry_msgs
  message_generation
)

add_message_files(
    FILES
    PointArray.msg
)

generate_messages(
  DEPENDENCIES
  std_msgs
  geometry_msgs
)

catkin_package(
   CATKIN_DEPENDS message_runtime roscpp rospy std_msgs geometry_msgs
)

include_directories(
  ${catkin_INCLUDE_DIRS}
)

catkin_install_python(PROGRAMS
   nodes/color_detector/color_detector
   nodes/color_detector/color_translator
   nodes/aruco_detector/aruco_translator
   nodes/nn_detector/nn_detector
   nodes/nn_detector/nn_translator
   nodes/rvr_localisator
   nodes/rvr_localisator_viewer
   DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)

include_directories(include ${catkin_INCLUDE_DIRS})

add_library(${PROJECT_NAME} src/${PROJECT_NAME})
target_link_libraries(${PROJECT_NAME} ${catkin_LIBRARIES})

add_executable(${PROJECT_NAME}_node nodes/aruco_detector/aruco_translator.py)
add_executable(${PROJECT_NAME}_node nodes/color_detector/color_detector.py)
add_executable(${PROJECT_NAME}_node nodes/color_detector/color_translator.py)
add_executable(${PROJECT_NAME}_node nodes/nn_detector/nn_detector.py)
add_executable(${PROJECT_NAME}_node nodes/nn_detector/nn_translator.py)
add_executable(${PROJECT_NAME}_node nodes/rvr_localisator.py)
add_executable(${PROJECT_NAME}_node nodes/rvr_localisator_viewer.py)
target_link_libraries(${PROJECT_NAME}_node ${PROJECT_NAME} ${catkin_LIBRARIES})
set_target_properties(${PROJECT_NAME}_node
  PROPERTIES OUTPUT_NAME rvr_recognition PREFIX "")

install(TARGETS ${PROJECT_NAME} ${PROJECT_NAME}_node
  ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION})

install(DIRECTORY include/${PROJECT_NAME}/
  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION})

install(DIRECTORY launch config
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION})
