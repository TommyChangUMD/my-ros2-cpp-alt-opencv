cmake_minimum_required(VERSION 3.8)
project(my-ros2-cpp-alt-opencv)

# clangd support
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)
execute_process(COMMAND "rm" "-f" "${CMAKE_SOURCE_DIR}/compile_commands.json")
execute_process(COMMAND "ln" "-s" "-r" "${CMAKE_BINARY_DIR}/compile_commands.json" "${CMAKE_SOURCE_DIR}/")

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

# find dependencies
find_package(ament_cmake REQUIRED)
find_package(rclpy REQUIRED)
find_package(image_transport REQUIRED)
find_package(cv_bridge REQUIRED)
find_package(sensor_msgs REQUIRED)
find_package(std_msgs REQUIRED)

# Find OpenCV dependencies:
if (USE_ALT_OPENCV)
# option2: use specific OpencCV 3.4.0 installed in /home/tchang/opencv3/install
  set(opencv3_install_dir "/home/tchang/opencv3/install/")
  find_package(OpenCV 3.4.0 EXACT REQUIRED PATHS "${opencv3_install_dir}/share/OpenCV/")
  set(ENV{PKG_CONFIG_PATH} "${opencv3_install_dir}/lib/pkgconfig/")
  pkg_search_module(OpenCV REQUIRED opencv=3.4.0)
  set(OpenCV_LIBRARIES ${OpenCV_LINK_LIBRARIES})
else()
# option1: default to use system OpenCV
  find_package(OpenCV)
endif()

message(STATUS "Found OpenCV version ${OpenCV_VERSION}")

if(BUILD_TESTING)
  find_package(ament_lint_auto REQUIRED)
  # the following line skips the linter which checks for copyrights
  # comment the line when a copyright and license is added to all source files
  set(ament_cmake_copyright_FOUND TRUE)
  # the following line skips cpplint (only works in a git repo)
  # comment the line when this package is in a git repo and when
  # a copyright and license is added to all source files
  set(ament_cmake_cpplint_FOUND TRUE)
  ament_lint_auto_find_test_dependencies()
endif()

# Define executables:
add_executable(my_opencv src/main.cpp)
target_link_libraries(my_opencv
  ${OpenCV_LIBRARIES}
  )


include_directories(
    ${CMAKE_SOURCE_DIR}/include
    ${OpenCV_INCLUDE_DIRS}
)


# Install executables
install(
  TARGETS my_opencv
  RUNTIME DESTINATION lib/${PROJECT_NAME}
)


ament_package()
