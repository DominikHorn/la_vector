cmake_minimum_required(VERSION 3.20)
project(la_vector)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++17")
set(CMAKE_POLICY_DEFAULT_CMP0048 NEW)

include(${PROJECT_SOURCE_DIR}/thirdparty/sdsl-lite.cmake)

add_library(${PROJECT_NAME} INTERFACE)
target_link_libraries(${PROJECT_NAME} INTERFACE ${SDSL_LIBRARY})
target_include_directories(${PROJECT_NAME} INTERFACE include)

# Enable OpenMP if available
include(CheckCXXCompilerFlag)
check_cxx_compiler_flag(-fopenmp HAS_OPENMP)
if (HAS_OPENMP AND NOT APPLE)
  target_compile_options(${PROJECT_NAME} INTERFACE -fopenmp)
endif()

# Enable march=native if available
check_cxx_compiler_flag(-march=native HAS_MARCH_NATIVE)
if (HAS_MARCH_NATIVE)
  target_compile_options(${PROJECT_NAME} INTERFACE -march=native)
endif()

get_directory_property(hasParent PARENT_DIRECTORY)
if (NOT hasParent)
  add_executable(example example.cpp)
  target_link_libraries(example la_vector)

  enable_testing()
  add_subdirectory(test)
endif()
