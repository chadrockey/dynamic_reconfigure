cmake_minimum_required(VERSION 2.8)
project(dynamic_reconfigure)

find_package(catkin COMPONENTS message_generation roscpp std_msgs)
find_package(Boost REQUIRED COMPONENTS system thread)

include_directories(include ${catkin_INCLUDE_DIRS})
include_directories(SYSTEM ${Boost_INCLUDE_DIRS})

catkin_python_setup()

add_message_files(
  DIRECTORY msg
  FILES 
  BoolParameter.msg      Config.msg           Group.msg       IntParameter.msg      SensorLevels.msg
  ConfigDescription.msg  DoubleParameter.msg  GroupState.msg  ParamDescription.msg  StrParameter.msg)

add_service_files(
  DIRECTORY srv
  FILES Reconfigure.srv)

generate_messages(DEPENDENCIES std_msgs)

catkin_package(LIBRARIES dynamic_reconfigure_config_init_mutex
               INCLUDE_DIRS include
               CATKIN_DEPENDS message_runtime std_msgs
               CFG_EXTRAS extras.cmake
)

add_library(dynamic_reconfigure_config_init_mutex
  src/dynamic_reconfigure_config_init_mutex.cpp)
target_link_libraries(dynamic_reconfigure_config_init_mutex ${Boost_LIBRARIES})

# TODO test does not compile
#add_subdirectory(test)

install(DIRECTORY include/dynamic_reconfigure/
  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
  FILES_MATCHING PATTERN "*.h")
      
install(TARGETS dynamic_reconfigure_config_init_mutex
  ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  RUNTIME DESTINATION ${CATKIN_GLOBAL_BIN_DESTINATION})

install(DIRECTORY cmake
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
  USE_SOURCE_PERMISSIONS
)
install(DIRECTORY msg
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION})
install(PROGRAMS scripts/dynparam
        DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION})
install(PROGRAMS scripts/reconfigure_gui
        DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION})
install(DIRECTORY srv
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION})
install(DIRECTORY templates
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION})
