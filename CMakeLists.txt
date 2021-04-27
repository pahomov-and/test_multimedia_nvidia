cmake_minimum_required(VERSION 3.10)
project(13_multi_camera)

set(CMAKE_CXX_STANDARD 14)

#set(/usr/src/linux-headers-4.9.140-tegra-ubuntu18.04_aarch64)
set(TOP_DIR /usr/src/jetson_multimedia_api/)

set(ARGUS_UTILS_DIR ${TOP_DIR}/argus/samples/utils)
set(CLASS_DIR ${TOP_DIR}/samples/common/classes/)
set(ALGO_CUDA_DIR   ${TOP_DIR}/samples/common/algorithm/cuda)
set(ALGO_TRT_DIR    ${TOP_DIR}/samples/common/algorithm/trt)

include_directories(
#        /usr/src/linux-headers-4.9.140-tegra-ubuntu18.04_aarch64/nvidia/include/
        ${TOP_DIR}/include
        ${TOP_DIR}/argus/samples/utils/
        ${TOP_DIR}/include/libjpeg-8b
#        ${TOP_DIR}/include/libjpeg-8b
        /usr/include/libdrm

)



add_library(argus SHARED IMPORTED) # or STATIC instead of SHARED
set_target_properties(argus PROPERTIES
        IMPORTED_LOCATION "/usr/lib/aarch64-linux-gnu/tegra/libnvargus_socketclient.so"
        INTERFACE_INCLUDE_DIRECTORIES "/usr/src/jetson_multimedia_api/include/Argus"
        )

add_library(nvbuf_utils SHARED IMPORTED) # or STATIC instead of SHARED
set_target_properties(nvbuf_utils PROPERTIES
        IMPORTED_LOCATION "/usr/lib/aarch64-linux-gnu/tegra/libnvbuf_utils.so"
        INTERFACE_INCLUDE_DIRECTORIES "/usr/src/jetson_multimedia_api/include"
        )

add_library(nvegl SHARED IMPORTED) # or STATIC instead of SHARED
set_target_properties(nvegl PROPERTIES
        IMPORTED_LOCATION "/usr/lib/aarch64-linux-gnu/tegra/libnveglstream_camconsumer.so"
        INTERFACE_INCLUDE_DIRECTORIES "/usr/src/jetson_multimedia_api/include"
        )


add_library(nvjpeg SHARED IMPORTED) # or STATIC instead of SHARED
set_target_properties(nvjpeg PROPERTIES
        IMPORTED_LOCATION "/usr/lib/aarch64-linux-gnu/tegra/libnvjpeg.so"
        INTERFACE_INCLUDE_DIRECTORIES "/usr/src/jetson_multimedia_api/include"
        )

file(GLOB CLASS_SRC_FILES ${CLASS_DIR}/*.cpp)

add_executable(${PROJECT_NAME}
        main.cpp
        ${ARGUS_UTILS_DIR}/Thread.cpp
        ${CLASS_SRC_FILES}
        )

TARGET_LINK_LIBRARIES( ${PROJECT_NAME}
        argus
        nvbuf_utils
        nvegl
        pthread
        drm
        EGL
        GLESv2
        nvjpeg
        nvinfer
        nvparsers
        v4l2
        X11
        )