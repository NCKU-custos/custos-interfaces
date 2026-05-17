cmake_minimum_required(VERSION 3.16)
project(custos_common_msgs)

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

set(MSG_FILES
  # msg/Example.msg
)
set(SRV_FILES
  # srv/Example.srv
)
set(ACTION_FILES
  # action/Example.action
)

if(MSG_FILES OR SRV_FILES OR ACTION_FILES)
  rosidl_generate_interfaces(${PROJECT_NAME}
    ${MSG_FILES}
    ${SRV_FILES}
    ${ACTION_FILES}
    DEPENDENCIES builtin_interfaces std_msgs
  )
endif()

ament_export_dependencies(rosidl_default_runtime)

if(BUILD_TESTING)
  find_package(ament_lint_auto REQUIRED)
  ament_lint_auto_find_test_dependencies()
endif()

ament_package()
