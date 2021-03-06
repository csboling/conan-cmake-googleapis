project(conan-googleapis)
cmake_minimum_required(VERSION 3.0.1)

include(${CMAKE_BINARY_DIR}/conanbuildinfo.cmake)
conan_basic_setup()

find_package(Protobuf 3 REQUIRED)
list(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_LIST_DIR}/CMake")
find_package(GRPC REQUIRED)
file(GLOB_RECURSE GOOGLEAPI_PROTOFILES RELATIVE ${CMAKE_CURRENT_SOURCE_DIR} *.proto)
set(PROTOBUF_GENERATE_CPP_APPEND_PATH FALSE)

PROTOBUF_GENERATE_CPP(PROTO_SRCS PROTO_HDRS "${GOOGLEAPI_PROTOFILES}")
PROTOBUF_GENERATE_GRPC_CPP(PROTO_GRPC_SRCS PROTO_GRPC_HDRS "${GOOGLEAPI_PROTOFILES}")
set(FINAL_SRCS "${PROTO_SRCS}" "${PROTO_GRPC_SRCS}")
set(FINAL_HDRS "${PROTO_HDRS}" "${PROTO_GRPC_HDRS}")

# set_source_files_properties(${FINAL_SRCS} ${FINAL_HDRS} PROPERTIES GENERATED TRUE)
message(STATUS "to be compiled: ${FINAL_SRCS}")
include_directories("${CMAKE_CURRENT_BINARY_DIR}" "${CMAKE_CURRENT_BINARY_DIR}/googleapis")
add_library(googleapis "${FINAL_SRCS}")
target_link_libraries(googleapis ${PROTOBUF_LIBRARIES} ${GRPC_LIBRARIES} ${CONAN_LIBS})
add_definitions(-D_WIN32_WINNT=0x600)
