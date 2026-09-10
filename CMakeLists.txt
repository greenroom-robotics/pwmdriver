cmake_minimum_required(VERSION 3.8)
project(pwmdriver)

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

# find dependencies
find_package(ament_cmake REQUIRED)
find_package(rclcpp REQUIRED)
find_package(std_msgs REQUIRED)

find_package(fmt REQUIRED)

add_library(${PROJECT_NAME} SHARED src/pwmlib.cpp src/param_pwmport.cpp)
add_executable(pwm_driver src/pwm_driver.cpp)

target_include_directories(${PROJECT_NAME} PUBLIC
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
  $<INSTALL_INTERFACE:include>)

target_compile_features(${PROJECT_NAME} PUBLIC c_std_99 cxx_std_23)  # Require C99 and C++23
ament_target_dependencies(${PROJECT_NAME}
  rclcpp
)

ament_target_dependencies(pwm_driver
  rclcpp
  std_msgs
)

target_link_libraries(${PROJECT_NAME}
  fmt
)

target_link_libraries(pwm_driver
  ${PROJECT_NAME}
  fmt
)

install(TARGETS ${PROJECT_NAME} EXPORT ${PROJECT_NAME}
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION lib
  RUNTIME DESTINATION bin)

install(TARGETS pwm_driver
  DESTINATION lib/${PROJECT_NAME})

install(DIRECTORY include/ DESTINATION include)

if(BUILD_TESTING)
  # Lint is off: with package_dir `.`, colcon writes install/ and build/ inside
  # the package source dir and the linters lint colcon's own generated output.
  # find_package(ament_lint_auto REQUIRED)
  # ament_lint_auto_find_test_dependencies()
endif()

ament_export_include_directories(include)
ament_export_targets(${PROJECT_NAME})

ament_package()
