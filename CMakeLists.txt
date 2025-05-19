cmake_minimum_required(VERSION 3.10)
cmake_policy(SET CMP0048 NEW)
project(mc_env_description VERSION 1.0.8)

if(NOT DISABLE_ROS)
  if(NOT DEFINED ENV{ROS_VERSION})
    message(FATAL_ERROR "ROS_VERSION is not set in the environment")
  endif()
  if($ENV{ROS_VERSION} EQUAL 1)
    set(ROS_VERSION 1)
    find_package(catkin REQUIRED)
  elseif($ENV{ROS_VERSION} EQUAL 2)
    set(ROS_VERSION 2)
    find_package(ament_cmake REQUIRED)
  else()
    message(FATAL_ERROR "Unknown ROS version: $ENV{ROS_VERSION}")
  endif()
else()
  set(ROS_VERSION 0)
endif()

if(${ROS_VERSION} EQUAL 1)
  catkin_package()
elseif(${ROS_VERSION} EQUAL 2)
  set(CATKIN_PACKAGE_SHARE_DESTINATION share/${PROJECT_NAME})
else()
  set(CATKIN_PACKAGE_SHARE_DESTINATION share/mc_rtc/${PROJECT_NAME})

  include(CMakePackageConfigHelpers)
  set(VERSION_CONFIG "${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}ConfigVersion.cmake")
  set(PROJECT_CONFIG "${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}Config.cmake")
  set(CONFIG_INSTALL_DIR "lib/cmake/${PROJECT_NAME}")

  configure_file("${CMAKE_CURRENT_SOURCE_DIR}/cmake/ConfigVersion.cmake.in" "${VERSION_CONFIG}" @ONLY)
  configure_package_config_file("${CMAKE_CURRENT_SOURCE_DIR}/cmake/Config.cmake.in" "${PROJECT_CONFIG}" INSTALL_DESTINATION "${CONFIG_INSTALL_DIR}")

  install(FILES "${VERSION_CONFIG}" "${PROJECT_CONFIG}" DESTINATION "${CONFIG_INSTALL_DIR}")
  install(FILES package.xml DESTINATION "${CATKIN_PACKAGE_SHARE_DESTINATION}")
endif()

install(DIRECTORY convex/
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}/convex)
install(DIRECTORY meshes/
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}/meshes)
install(DIRECTORY rsdf/
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}/rsdf)
install(DIRECTORY urdf/
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}/urdf)

if(${ROS_VERSION} EQUAL 2)
  ament_package(CONFIG_EXTRAS cmake/mc_env_description_INSTALL_PREFIX.cmake)
endif()
