cmake_minimum_required (VERSION 2.8)
project (im2col)

if(MSVC)
set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_SOURCE_DIR}/cmake/")
#set(THREADS_USE_PTHREADS_WIN32 TRUE)

  string(REGEX REPLACE "/Z[iI7]" ""
         CMAKE_CXX_FLAGS_RELEASE
         "${CMAKE_CXX_FLAGS_RELEASE}")
  set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} /Z7")
endif()

find_package (CUDA REQUIRED)
find_package (MATLAB REQUIRED)



if (MSVC)
include_directories (
  ${CUDA_INCLUDE_DIRS}
  ${MATLAB_INCLUDE_DIR}
  ${MATLAB_GPU_INCLUDE_DIR}
  include
)
else()
include_directories (
  ${CUDA_INCLUDE_DIRS}
  ${MATLAB_INCLUDE_DIR}
  ${MATLAB_GPU_INCLUDE_DIR}
  include
)
endif()


CUDA_ADD_LIBRARY (cu_im2col SHARED
  src/cu_im2col/im2col.cu
  src/cu_im2col/matlab.def
)

CUDA_ADD_LIBRARY (cu_col2im SHARED
  src/cu_col2im/col2im.cu
  src/cu_col2im/matlab.def
)

set_target_properties (cu_im2col
  PROPERTIES
  PREFIX ""
  SUFFIX ".mexw64"
)
set_target_properties (cu_col2im
  PROPERTIES
  PREFIX ""
  SUFFIX ".mexw64"
)



TARGET_LINK_LIBRARIES (cu_im2col
  ${MATLAB_LIBRARIES}
  ${CUDA_CUBLAS_LIBRARIES}
  ${MATLAB_GPU_LIBRARY}
)
TARGET_LINK_LIBRARIES (cu_col2im
  ${MATLAB_LIBRARIES}
  ${CUDA_CUBLAS_LIBRARIES}
  ${MATLAB_GPU_LIBRARY}
)


CUDA_BUILD_CLEAN_TARGET()