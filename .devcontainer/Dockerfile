FROM ros:foxy-ros1-bridge

ENV ROS_DISTRO=foxy
ENV AMENT_PREFIX_PATH=/opt/ros/foxy
ENV COLCON_PREFIX_PATH=/opt/ros/foxy
ENV LD_LIBRARY_PATH=/opt/ros/foxy/lib
ENV PATH=/opt/ros/foxy/bin:$PATH
ENV PYTHONPATH=/opt/ros/foxy/lib/python3.8/site-packages
ENV ROS_PYTHON_VERSION=3
ENV ROS_VERSION=2
ENV DEBIAN_FRONTEND=

# Install language
RUN apt-get update && apt-get install -y \
  locales \
  && locale-gen en_US.UTF-8 \
  && update-locale LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8 \
  && rm -rf /var/lib/apt/lists/*
ENV LANG en_US.UTF-8

# Install timezone
RUN ln -fs /usr/share/zoneinfo/UTC /etc/localtime \
  && export DEBIAN_FRONTEND=noninteractive \
  && apt-get update \
  && apt-get install -y tzdata \
  && dpkg-reconfigure --frontend noninteractive tzdata \
  && rm -rf /var/lib/apt/lists/*

ARG USERNAME=ros
ARG USER_UID=1000
ARG USER_GID=$USER_UID

# Create a non-root user
RUN groupadd --gid $USER_GID $USERNAME \
  && useradd -s /bin/bash --uid $USER_UID --gid $USER_GID -m $USERNAME \
  # [Optional] Add sudo support for the non-root user
  && apt-get update \
  && apt-get install -y sudo \
  && echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME\
  && chmod 0440 /etc/sudoers.d/$USERNAME \
  # Cleanup
  && rm -rf /var/lib/apt/lists/* \
  && echo "source /usr/share/bash-completion/completions/git" >> /home/$USERNAME/.bashrc \
  && echo "if [ -f /opt/ros/${ROS_DISTRO}/setup.bash ]; then source /opt/ros/${ROS_DISTRO}/setup.bash; fi" >> /home/$USERNAME/.bashrc
ENV DEBIAN_FRONTEND=


ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update \ 
        && apt-get -y install --no-install-recommends nano ros-foxy-rviz2 terminator gdb

# play ros1 bags with ros2 bag play
RUN apt-get -y install ros-foxy-rosbag2-bag-v2-plugins
RUN apt-get -y install --no-install-recommends ~nros-foxy-rqt*

# build tools ros1 catkin
RUN apt-get -y install python3-rosdep python3-rosinstall python3-rosinstall-generator python3-wstool build-essential python3-catkin-tools python3-osrf-pycommon

# navigation stack dependencies Huy
# RUN apt-get -y install autoconf ros-noetic-dynamic-reconfigure ros-noetic-mavros ros-noetic-cmake-modules libyaml-cpp-dev ros-noetic-tf2-geometry-msgs

#-> Linear algebra library
RUN echo "Installing Eigen 3 last version ..." && \
    apt-get install -y libeigen3-dev

#-[] Install OpenCV last version
#-? From : http://techawarey.com/programming/install-opencv-c-c-in-ubuntu-18-04-lts-step-by-step-guide/
#-? Another RUN command in order to free memory
#-? Usual computer vision library
# RUN mkdir /dpds/
# RUN echo "Installing OpenCV last version ..." && \
#     cd /dpds/ && \
#     git clone https://github.com/Itseez/opencv.git opencv && \
#     git clone https://github.com/Itseez/opencv_contrib.git opencv_contrib && \
#     cd opencv/ && \
#     mkdir build && \
#     cd build/ && \
#     cmake -D CMAKE_BUILD_TYPE=RELEASE \
#     -D BUILD_TIFF=ON \
#     -D WITH_CUDA=OFF \
#     -D ENABLE_AVX=OFF \
#     -D WITH_OPENGL=OFF \
#     -D WITH_OPENCL=OFF \
#     -D WITH_IPP=OFF \
#     -D WITH_TBB=ON \
#     -D BUILD_TBB=ON \
#     -D WITH_EIGEN=ON \
#     -D WITH_V4L=OFF \
#     -D WITH_VTK=OFF \
#     -D BUILD_TESTS=OFF \
#     -D BUILD_PERF_TESTS=OFF \
#     -D OPENCV_GENERATE_PKGCONFIG=ON \
#     -D OPENCV_EXTRA_MODULES_PATH=/dpds/opencv_contrib/modules \
#     /dpds/opencv/ && \
#     make -j4 && \
#     make install && \
#     ldconfig

# RUN apt-get update \
#    && apt-get -y install --no-install-recommends <your-package-list-here> \
#    #
#    # Clean up
#    && apt-get autoremove -y \
#    && apt-get clean -y \
#    && rm -rf /var/lib/apt/lists/*
# ENV DEBIAN_FRONTEND=dialog

# Set up auto-source of workspace for ros user
ARG WORKSPACE

# shortcuts for convenience ;)
# please customize to your liking
RUN echo "alias sn='source /opt/ros/noetic/setup.bash'" >> /home/ros/.bashrc
RUN echo "alias sf='source /opt/ros/foxy/setup.bash'"  >> /home/ros/.bashrc
RUN echo "alias s='source install/setup.bash'" >> /home/ros/.bashrc
RUN echo "alias m='bash build.sh'" >> /home/ros/.bashrc

# source ros2 workspace at startup of container
RUN echo "if [ -f ${WORKSPACE}/ros2/install/setup.bash ]; then source ${WORKSPACE}/ros2/install/setup.bash; fi" >> /home/ros/.bashrc