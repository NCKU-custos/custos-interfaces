cmake_minimum_required(VERSION 3.16)
project(custos_navigation_msgs)

if(NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 20)
endif()
if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

find_package(ament_cmake REQUIRED)
find_package(rosidl_default_generators REQUIRED)
find_package(builtin_interfaces REQUIRED)
find_package(std_msgs REQUIRED)
find_package(geometry_msgs REQUIRED)
find_package(nav_msgs REQUIRED)
find_package(custos_common_msgs REQUIRED)

set(MSG_FILES
)
set(SRV_FILES
)
set(ACTION_FILES
)

if(MSG_FILES OR SRV_FILES OR ACTION_FILES)
  rosidl_generate_interfaces(${PROJECT_NAME}
    ${MSG_FILES}
    ${SRV_FILES}
    ${ACTION_FILES}
    DEPENDENCIES builtin_interfaces std_msgs geometry_msgs nav_msgs custos_common_msgs
  )
endif()

ament_export_dependencies(rosidl_default_runtime custos_common_msgs)

if(BUILD_TESTING)
  find_package(ament_lint_auto REQUIRED)
  ament_lint_auto_find_test_dependencies()
endif()

ament_package()
