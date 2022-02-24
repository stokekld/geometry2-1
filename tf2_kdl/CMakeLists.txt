cmake_minimum_required(VERSION 3.5)
project(tf2_kdl)

# Default to C++14
if(NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 14)
endif()

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic -Wnon-virtual-dtor -Woverloaded-virtual)
endif()

find_package(ament_cmake REQUIRED)
find_package(builtin_interfaces REQUIRED)
find_package(geometry_msgs REQUIRED)
find_package(orocos_kdl REQUIRED)
find_package(tf2 REQUIRED)
find_package(tf2_ros REQUIRED)

add_library(${PROJECT_NAME} INTERFACE)
target_include_directories(${PROJECT_NAME} INTERFACE
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
  $<INSTALL_INTERFACE:include>
  ${orocos_kdl_INCLUDE_DIRS}
)
target_link_libraries(${PROJECT_NAME} INTERFACE
  orocos-kdl
)
ament_target_dependencies(${PROJECT_NAME} INTERFACE
  builtin_interfaces
  geometry_msgs
  tf2
  tf2_ros
)

install(
  TARGETS ${PROJECT_NAME}
  EXPORT export_${PROJECT_NAME}
)

ament_python_install_package(${PROJECT_NAME}
     PACKAGE_DIR src/${PROJECT_NAME})

install(DIRECTORY include/${PROJECT_NAME}/
    DESTINATION include/${PROJECT_NAME}
)

# TODO(ahcorde): Port python once https://github.com/ros2/orocos_kinematics_dynamics/pull/4 is merged
# install(PROGRAMS scripts/test.py
#     DESTINATION lib/${PROJECT_NAME}
# )

if(BUILD_TESTING)
  find_package(ament_cmake_gtest REQUIRED)
  find_package(rclcpp REQUIRED)
  find_package(tf2_msgs REQUIRED)

  ament_add_gtest(test_kdl test/test_tf2_kdl.cpp)
  target_link_libraries(test_kdl
    ${PROJECT_NAME}
  )
  ament_target_dependencies(test_kdl
    rclcpp
    tf2_msgs
  )
endif()

ament_export_targets(export_${PROJECT_NAME})
ament_export_dependencies(builtin_interfaces geometry_msgs orocos_kdl_vendor tf2 tf2_ros)
ament_package()
