cmake_minimum_required(VERSION 3.20)
project(irena LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

if(NOT CMAKE_BUILD_TYPE AND NOT CMAKE_CONFIGURATION_TYPES)
  set(CMAKE_BUILD_TYPE Release CACHE STRING "" FORCE)
endif()

add_executable(irena
  src/main.cpp
  src/cl_backend.cpp
  src/vk_interop.cpp
  src/scene.cpp
  src/bvh.cpp
  src/renderer.cpp
  src/image.cpp
)

target_include_directories(irena PRIVATE src)

if(NOT APPLE AND NOT CMAKE_SYSTEM_NAME STREQUAL "Linux")
  message(FATAL_ERROR "irena supports macOS and Linux only")
endif()

target_compile_options(irena PRIVATE -Wall -Wextra)
target_link_libraries(irena PRIVATE ${CMAKE_DL_LIBS})

if(APPLE)
  target_compile_definitions(irena PRIVATE CL_SILENCE_DEPRECATION GL_SILENCE_DEPRECATION)
endif()

add_custom_command(TARGET irena POST_BUILD
  COMMAND ${CMAKE_COMMAND} -E copy_directory
          ${CMAKE_SOURCE_DIR}/kernels $<TARGET_FILE_DIR:irena>/kernels)
