cmake_minimum_required(VERSION 3.25)
project(mltoy LANGUAGES CXX)

if (NOT DEFINED CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 23)
endif()


set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)
set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_SHARED_LINKER_FLAGS} -fuse-ld=lld")
set(CMAKE_PREFIX_PATH "${CMAKE_PREFIX_PATH};/lib/llvm-19/lib/cmake/")
# set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -stdlib=libc++")

if(NOT DEFINED LLVM_VERSION)
  message("Setting LLVM_VERSION to 19")
  set(LLVM_VERSION 19 CACHE STRING "LLVM and MLIR version to be used")
endif()


message("Finding LLVM_VERSION / MLIR_VERSION ${LLVM_VERSION}")
find_package(MLIR REQUIRED CONFIG)
message(STATUS "Found LLVM ${LLVM_PACKAGE_VERSION}")
message(STATUS "Using LLVMConfig.cmake in: ${LLVM_DIR}")

include_directories(${LLVM_INCLUDE_DIRS})
separate_arguments(LLVM_DEFINITIONS_LIST NATIVE_COMMAND ${LLVM_DEFINITIONS})
add_definitions(${LLVM_DEFINITIONS_LIST})

include_directories(${PROJECT_SOURCE_DIR}/include)

add_executable(${PROJECT_NAME} main.cpp parser/AST.cpp)

llvm_map_components_to_libnames(llvm_libs support core irreader)
target_link_libraries(${PROJECT_NAME} ${llvm_libs} )
