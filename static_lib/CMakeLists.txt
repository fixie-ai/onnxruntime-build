cmake_minimum_required(VERSION 3.26)
# set(CMAKE_OSX_DEPLOYMENT_TARGET "10.15" CACHE STRING "Minimum OS X deployment version. Used only for macOS")
project(onnxruntime_static_lib)

set(CMAKE_MSVC_RUNTIME_LIBRARY MultiThreaded)
set(ONNX_USE_MSVC_STATIC_RUNTIME ON)
set(protobuf_MSVC_STATIC_RUNTIME ON)
set(onnxruntime_BUILD_SHARED_LIB ON)

if(NOT BUILD_SHARED_LIBS AND MSVC)
  # see https://cmake.org/cmake/help/latest/prop_tgt/MSVC_RUNTIME_LIBRARY.html
  # https://stackoverflow.com/questions/14172856/compile-with-mt-instead-of-md-using-cmake
  if(MSVC)
      add_compile_options(
          $<$<CONFIG:>:/MT> #---------|
          $<$<CONFIG:Debug>:/MTd> #---|-- Statically link the runtime libraries
          $<$<CONFIG:Release>:/MT> #--|
          $<$<CONFIG:RelWithDebInfo>:/MT>
          $<$<CONFIG:MinSizeRel>:/MT>
      )
  endif()
endif()

add_definitions(-D_SILENCE_ALL_CXX23_DEPRECATION_WARNINGS)

add_subdirectory(${ONNXRUNTIME_SOURCE_DIR}/cmake onnxruntime EXCLUDE_FROM_ALL)
message(STATUS "CMAKE_CXX_FLAGS: ${CMAKE_CXX_FLAGS}")

include(bundle_static_library.cmake)
bundle_static_library(${PROJECT_NAME} onnxruntime)
