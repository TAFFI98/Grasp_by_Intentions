FROM osrf/ros:noetic-desktop-full


# Environment variable -> set language to C (computer) UTF-8 (8 bit unicode transformation format).
ENV LANG C.UTF-8

# Debconf is used to perform system-wide configutarions.
# Noninteractive -> use default settings -> put in debconf db.
RUN echo 'debconf debconf/frontend select Noninteractive' | debconf-set-selections

# Set the nvidia container runtime.
ENV NVIDIA_VISIBLE_DEVICES \
    ${NVIDIA_VISIBLE_DEVICES:-all}
ENV NVIDIA_DRIVER_CAPABILITIES \
    ${NVIDIA_DRIVER_CAPABILITIES:+$NVIDIA_DRIVER_CAPABILITIES,}graphics

# Environment variable -> see output in real time.
ENV PYTHONUNBUFFERED 1

# Install some handy tools.
RUN set -x \
        && apt-get update \
        && apt-get upgrade -y \
        && apt-get install -y  terminator  python3-catkin-tools python3-rosdep python3-rosinstall python3-rosinstall-generator python3-wstool build-essential ros-noetic-ros-control ros-noetic-ros-controllers libglfw3 libglfw3-dev git-all \
	&& apt install snapd libosmesa6-dev libgl1-mesa-glx libglfw3 -y
RUN apt-get install -y \
  software-properties-common \
  ca-certificates \
  wget\
  build-essential \
   xauth \
  apt-utils \
  unzip \
  git\
  vim \
  curl \
  locate \
  sudo
# The OSRF container didn't link python3 to python, causing ROS scripts to fail.

#Install miniconda
ENV MINICONDA_VERSION latest
ENV CONDA_DIR $HOME/miniconda3
RUN wget --quiet https://repo.anaconda.com/miniconda/Miniconda3-$MINICONDA_VERSION-Linux-x86_64.sh -O ~/miniconda.sh && \
    chmod +x ~/miniconda.sh && \
    ~/miniconda.sh -b -p $CONDA_DIR && \
    rm ~/miniconda.sh
    
# ========  make non-activate conda commands available ========
ENV PATH=$CONDA_DIR/bin:$PATH
# make conda activate command available from /bin/bash --login shells
RUN echo ". $CONDA_DIR/etc/profile.d/conda.sh" >> ~/.profile
# make conda activate command available from /bin/bash --interative shells
RUN conda init bash



# ======== create a project directory inside user home ========
ENV PROJECT_DIR $HOME/Project
RUN mkdir -p $PROJECT_DIR
COPY ../Project/ $PROJECT_DIR/
WORKDIR $PROJECT_DIR

# ======== build the conda environment ========
ENV ENV_PREFIX $PROJECT_DIR/env
RUN conda  create -n grcnn python numpy=1.19 pytorch torchvision opencv scikit-image tensorboardx matplotlib pillow
RUN conda init bash \
    && . ~/.bashrc \
    && conda activate grcnn \
    && pip install torchsummary

