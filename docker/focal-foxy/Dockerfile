# Image: introlab3it/rtabmap:focal-foxy

FROM ubuntu:20.04

RUN apt update && \
    apt install software-properties-common -y && \
    add-apt-repository universe && \
    apt update && \
    apt install curl -y && \
    curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.key -o /usr/share/keyrings/ros-archive-keyring.gpg && \
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/ros-archive-keyring.gpg] http://packages.ros.org/ros2/ubuntu $(. /etc/os-release && echo $UBUNTU_CODENAME) main" | tee /etc/apt/sources.list.d/ros2.list > /dev/null && \
    apt-get clean && rm -rf /var/lib/apt/lists/

ENV DEBIAN_FRONTEND=noninteractive

# Install build dependencies
RUN apt-get update && \
    apt upgrade -y && \
    apt-get install -y git ros-foxy-ros-base python3-argcomplete ros-dev-tools ros-foxy-rtabmap-ros && \
    apt-get remove -y ros-foxy-rtabmap && \
    apt-get clean && rm -rf /var/lib/apt/lists/

WORKDIR /root/

# GTSAM
RUN add-apt-repository ppa:borglab/gtsam-release-4.0 -y
RUN apt-get update && apt install libgtsam-dev libgtsam-unstable-dev -y && \
    apt-get clean && rm -rf /var/lib/apt/lists/

# MRPT
RUN add-apt-repository ppa:joseluisblancoc/mrpt-stable -y
RUN apt-get update && apt install libmrpt-poses-dev -y && \
    apt-get clean && rm -rf /var/lib/apt/lists/
    
# PDAL
RUN apt-get update && apt-get install -y libpdal-dev && \
    apt-get clean && rm -rf /var/lib/apt/lists/

# RealSense2
RUN apt-get update && apt-get install -y ros-foxy-librealsense2 && \
    apt-get clean && rm -rf /var/lib/apt/lists/

ARG TARGETPLATFORM
ENV TARGETPLATFORM=${TARGETPLATFORM:-linux/amd64}
RUN echo "I am building for $TARGETPLATFORM"

# Azure Kinect DK
# Taken from https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1190#issuecomment-822772494
# K4A binaries on 20.04 not released yet, we should take those from 18.04
RUN if [ "$TARGETPLATFORM" = "linux/amd64" ]; then echo "Installing k4a..." && \
    apt-get update && apt-get install -y curl && \
    echo "Download libk4a1.3_1.3.0_amd64.deb..." && \
    curl -sSL https://packages.microsoft.com/ubuntu/18.04/prod/pool/main/libk/libk4a1.3/libk4a1.3_1.3.0_amd64.deb > /tmp/libk4a1.3_1.3.0_amd64.deb && \
    echo "Download libk4a1.3-dev_1.3.0_amd64.deb..." && \
    curl -sSL https://packages.microsoft.com/ubuntu/18.04/prod/pool/main/libk/libk4a1.3-dev/libk4a1.3-dev_1.3.0_amd64.deb > /tmp/libk4a1.3-dev_1.3.0_amd64.deb && \
    echo "Download libk4abt1.0_1.0.0_amd64.deb..." && \
    curl -sSL https://packages.microsoft.com/ubuntu/18.04/prod/pool/main/libk/libk4abt1.0/libk4abt1.0_1.0.0_amd64.deb > /tmp/libk4abt1.0_1.0.0_amd64.deb && \
    echo "Download libk4abt1.0-dev_1.0.0_amd64.deb..." && \
    curl -sSL https://packages.microsoft.com/ubuntu/18.04/prod/pool/main/libk/libk4abt1.0-dev/libk4abt1.0-dev_1.0.0_amd64.deb > /tmp/libk4abt1.0-dev_1.0.0_amd64.deb && \
    echo "Download k4a-tools_1.3.0_amd64.deb..." && \
    curl -sSL https://packages.microsoft.com/ubuntu/18.04/prod/pool/main/k/k4a-tools/k4a-tools_1.3.0_amd64.deb > /tmp/k4a-tools_1.3.0_amd64.deb && \
    echo "Accept license..." && \
    echo 'libk4a1.3 libk4a1.3/accepted-eula-hash string 0f5d5c5de396e4fee4c0753a21fee0c1ed726cf0316204edda484f08cb266d76' | debconf-set-selections && \
    echo 'libk4abt1.0	libk4abt1.0/accepted-eula-hash	string	03a13b63730639eeb6626d24fd45cf25131ee8e8e0df3f1b63f552269b176e38' | debconf-set-selections && \
    dpkg -i /tmp/libk4a1.3_1.3.0_amd64.deb && \
    dpkg -i /tmp/libk4a1.3-dev_1.3.0_amd64.deb && \
    dpkg -i /tmp/libk4abt1.0_1.0.0_amd64.deb && \
    dpkg -i /tmp/libk4abt1.0-dev_1.0.0_amd64.deb && \
    apt-get install -y libsoundio1 && \
    dpkg -i /tmp/k4a-tools_1.3.0_amd64.deb && \
    rm /tmp/libk4a* /tmp/k4a* && \
    apt-get clean && rm -rf /var/lib/apt/lists/; fi

# libfreenect2 
RUN if [ "$TARGETPLATFORM" = "linux/amd64" ]; then echo "Installing libfreenect2..." && \
    apt-get update && apt-get install -y mesa-utils xserver-xorg-video-all libusb-1.0-0-dev libturbojpeg0-dev libglfw3-dev && \
    apt-get clean && rm -rf /var/lib/apt/lists/ && \
    git clone https://github.com/OpenKinect/libfreenect2 && \
    cd libfreenect2 && \
    mkdir build && \
    cd build && \
    cmake .. && \
    make -j$(nproc) && \
    make install && \
    cd && \
    rm -r libfreenect2; fi

# zed open capture
RUN if [ "$TARGETPLATFORM" = "linux/amd64" ]; then echo "Installing zed-open-capture..." && \
    apt-get update && apt install -y libusb-1.0-0-dev libhidapi-libusb0 libhidapi-dev wget && \
    apt-get clean && rm -rf /var/lib/apt/lists/ && \
    git clone https://github.com/stereolabs/zed-open-capture.git && \
    cd zed-open-capture && \
    mkdir build && \
    cd build && \
    cmake .. && \
    make -j$(nproc) && \
    make install && \
    cd && \
    rm -r zed-open-capture; fi

# AliceVision v2.4.0 modified (Sept 13 2021)
RUN if [ "$TARGETPLATFORM" = "linux/amd64" ]; then echo "Installing AliceVision..." && \
      apt-get update && DEBIAN_FRONTEND=noninteractive apt-get install -y \
      libsuitesparse-dev \
      libceres-dev \
      xorg-dev \
      libglu1-mesa-dev \
      wget && apt-get clean && rm -rf /var/lib/apt/lists/; fi
RUN if [ "$TARGETPLATFORM" = "linux/amd64" ]; then git clone https://github.com/OpenImageIO/oiio.git && \
    cd oiio && \
    git checkout Release-2.0.12 && \
    mkdir build && \
    cd build && \
    cmake -DUSE_PYTHON=OFF -DOIIO_BUILD_TESTS=OFF -DOIIO_BUILD_TOOLS=OFF .. && \
    make -j$(nproc) && \
    make install && \
    cd && \
    rm -r oiio; fi
RUN if [ "$TARGETPLATFORM" = "linux/amd64" ]; then git clone https://github.com/assimp/assimp.git && \
    cd assimp && \
    git checkout 71a87b653cd4b5671104fe49e2e38cf5dd4d8675 && \
    mkdir build && \
    cd build && \
    cmake .. && \
    make -j$(nproc) && \
    make install && \
    cd && \
    rm -r assimp; fi
# Ignore assimp installed from ros, as it has invalid "/usr/lib/include" in its target, making rtabmap's cmake fails
RUN if [ "$TARGETPLATFORM" = "linux/amd64" ]; then rm -rf /usr/lib/x86_64-linux-gnu/cmake/assimp*; fi
RUN if [ "$TARGETPLATFORM" = "linux/amd64" ]; then git clone https://github.com/alicevision/geogram.git && \
    cd geogram && \
    git checkout v1.7.6 && \
    wget https://gist.githubusercontent.com/matlabbe/1df724465106c056ca4cc195c81d8cf0/raw/b3ed4cb8f9b270833a40d57d870a259eabfa4415/geogram_8b2ae61.patch && \
    git apply geogram_8b2ae61.patch && \
    ./configure.sh && \
    cd build/Linux64-gcc-dynamic-Release && \
    make -j$(nproc) && \
    make install && \
    cd && \
    rm -r geogram; fi
RUN if [ "$TARGETPLATFORM" = "linux/amd64" ]; then git clone https://github.com/alicevision/AliceVision.git --recursive && \
    cd AliceVision && \
    git checkout 0f6115b6af6183c524aa7fcf26141337c1cf3872 && \
    git submodule update -i && \
    wget https://gist.githubusercontent.com/matlabbe/1df724465106c056ca4cc195c81d8cf0/raw/b3ed4cb8f9b270833a40d57d870a259eabfa4415/alicevision_0f6115b.patch && \
    git apply alicevision_0f6115b.patch && \
    mkdir build && \
    cd build && \
    cmake -DALICEVISION_USE_CUDA=OFF -DALICEVISION_USE_APRILTAG=OFF -DALICEVISION_BUILD_SOFTWARE=OFF .. && \
    make -j$(nproc) && \
    make install && \
    cd && \
    rm -r AliceVision; fi
    
#commit Aug 6 2020
RUN apt-get update && apt install -y wget && \
    apt-get clean && rm -rf /var/lib/apt/lists/
RUN git clone https://github.com/laurentkneip/opengv.git && \
    cd opengv && \
    git checkout 91f4b19c73450833a40e463ad3648aae80b3a7f3 && \
    wget https://gist.githubusercontent.com/matlabbe/a412cf7c4627253874f81a00745a7fbb/raw/accc3acf465d1ffd0304a46b17741f62d4d354ef/opengv_disable_march_native.patch && \
    git apply opengv_disable_march_native.patch && \
    mkdir build && \
    cd build && \
    cmake -DCMAKE_BUILD_TYPE=Release .. && \
    make -j$(nproc) && \
    make install && \
    cd && \
    rm -r opengv

#RUN git clone --branch 4.2.0 https://github.com/opencv/opencv.git && \
#    git clone --branch 4.2.0 https://github.com/opencv/opencv_contrib.git && \
#    cd opencv && \
#    mkdir build && \
#    cd build && \
#    cmake -DBUILD_opencv_python3=OFF -DBUILD_opencv_python_bindings_generator=OFF -DBUILD_opencv_python_tests=OFF -DBUILD_PERF_TESTS=OFF -DBUILD_TESTS=OFF -DOPENCV_ENABLE_NONFREE=ON -DOPENCV_EXTRA_MODULES_PATH=/root/opencv_contrib/modules .. && \
#    make -j$(nproc) && \
#    make install && \
#    cd ../.. && \
#    rm -rf opencv opencv_contrib

# Will be used to read/store databases on host
RUN mkdir -p /root/Documents/RTAB-Map && chmod 777 /root/Documents/RTAB-Map

RUN rm /bin/sh && ln -s /bin/bash /bin/sh

# Copy current source code
COPY . /root/rtabmap

RUN cp /root/rtabmap/docker/focal-foxy/ros_entrypoint.sh /ros_entrypoint.sh
ENTRYPOINT [ "/ros_entrypoint.sh" ]

# Build RTAB-Map project
RUN source /ros_entrypoint.sh && \
    cd rtabmap/build && \
    cmake -DWITH_ALICE_VISION=ON -DWITH_OPENGV=ON .. && \
    make -j$(nproc) && \
    make install && \
    cd ../.. && \
    rm -rf rtabmap && \
    ldconfig

