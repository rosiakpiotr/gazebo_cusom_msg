cmake_minimum_required(VERSION 3.5)
project(gazebo_custom_msg)

# Default to C99
if(NOT CMAKE_C_STANDARD)
  set(CMAKE_C_STANDARD 99)
endif()

# Default to C++14
if(NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 14)
endif()

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

# find dependencies
find_package(ament_cmake REQUIRED)
find_package(gazebo_msgs REQUIRED)
find_package(Protobuf REQUIRED)
pkg_check_modules(PROTOBUF protobuf)

if("${PROTOBUF_VERSION}" VERSION_LESS "2.5.0")
  message(FATAL_ERROR "protobuf version: ${PROTOBUF_VERSION} not compatible, must be >= 2.5.0")
endif()

set(sknr_msgs
  msg/CommandPitchAngle.proto
  msg/VppState.proto
)

PROTOBUF_GENERATE_CPP(SKNR_PROTO_SRCS SKNR_PROTO_HDRS ${sknr_msgs})
add_library(sknr_msgs SHARED ${SKNR_PROTO_SRCS})
target_link_libraries(sknr_msgs ${PROTOBUF_LIBRARY})

ament_export_include_directories(include)

ament_export_dependencies(
  gazebo_msgs
  Protobuf
)

install(FILES
  ${SKNR_PROTO_HDRS}
  DESTINATION
  include/${PROJECT_NAME}
)

install(TARGETS
  sknr_msgs
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION lib
  RUNTIME DESTINATION bin)

ament_package()