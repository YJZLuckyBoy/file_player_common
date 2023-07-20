cmake_minimum_required(VERSION 2.8.3)
project(file_player_common)

add_definitions(-std=c++11)

if (NOT CMAKE_BUILD_TYPE)
  set (CMAKE_BUILD_TYPE release)
endif ()

option(COMPILE_LIVOX "compile with livox driver" OFF)
if (COMPILE_LIVOX)
  message(STATUS "compile with livox driver : ${COMPILE_LIVOX}")
  add_definitions(-DCOMPILE_LIVOX)
endif()

find_package(catkin REQUIRED cmake_modules
  COMPONENTS
  roscpp
  rospy
  std_msgs
  geometry_msgs
  message_generation
  rosbag
  image_transport
  cv_bridge
  dynamic_reconfigure
  pcl_ros
  pcl_conversions
  pcl_msgs
#  irp_sen_msgs
  camera_info_manager
  tf
  eigen_conversions
)
set(CMAKE_AUTOMOC ON)

find_package(Eigen REQUIRED)

#set (CMAKE_PREFIX_PATH /opt/Qt5.6.1/5.6/gcc_64/lib/cmake)

#find_package(Qt5Core)
find_package(Qt5Widgets)
find_package(Qt5Gui)
find_package(Qt5OpenGL)

#add_message_files(
#  DIRECTORY msg
#)

generate_messages(
   DEPENDENCIES
    std_msgs
    geometry_msgs
)

generate_dynamic_reconfigure_options(
  cfg/file_player.cfg
  #...
)

catkin_package(
  INCLUDE_DIRS include
  LIBRARIES 
    file_player_common
  CATKIN_DEPENDS 
    roscpp rospy 
    std_msgs 
    geometry_msgs 
    message_runtime 
    image_transport 
    cv_bridge 
    dynamic_reconfigure 
    pcl_ros
    pcl_conversions  
    pcl_msgs
    eigen_conversions
#    irp_sen_msgs
    camera_info_manager
    tf

  DEPENDS 
    Eigen
)

set (SRC_DIR ${CMAKE_CURRENT_SOURCE_DIR}/src)

set (File_Player_QTLib_src ${SRC_DIR}/mainwindow.cpp ${SRC_DIR}/ROSThread.cpp)
set (File_Player_QTLib_hdr ${SRC_DIR}/mainwindow.h ${SRC_DIR}/ROSThread.h)
set (File_Player_QTLib_ui  ${SRC_DIR}/mainwindow.ui)
set (File_Player_QTBin_src ${SRC_DIR}/main.cpp)

#find_package(GLEW REQUIRED)
#find_package(GLUT REQUIRED)

include_directories(
  ${catkin_INCLUDE_DIRS}
  ${file_player_common_INCLUDE_DIRS}
  ${SRC_DIR}
  ${Qt5Widgets_INCLUDE_DIRS}
  ${PROJECT_BINARY_DIR}
  include
  ${Eigen_INCLUDE_DIRS}
)


#qt5_wrap_cpp(File_Player_QTLib_hdr_moc ${File_Player_QTLib_hdr})
qt5_wrap_ui (File_Player_QTLib_ui_moc  ${File_Player_QTLib_ui})
qt5_add_resources(SHADER_RSC_ADDED ${SRC_DIR}/resources.qrc)


###########
## Build ##
###########
add_executable(file_player_common ${File_Player_QTLib_src} ${File_Player_QTLib_hdr} ${File_Player_QTBin_src} ${SHADER_RSC_ADDED} ${File_Player_QTLib_ui_moc})     

add_dependencies(file_player_common file_player_common_msgs_generate_messages_cpp ${PROJECT_NAME}_gencfg)
add_dependencies(file_player_common ${catkin_EXPORTED_TARGETS})

target_link_libraries(file_player_common
  ${catkin_LIBRARIES}
  ${QT_LIBRARIES} 
  ${OPENGL_LIBRARIES}
  Qt5::Widgets 
  Qt5::Gui 
  Qt5::OpenGL
  GL
  ${BOOST_CUSTOM_LIBS}
  ${Eigen_LIBRARIES}
)



