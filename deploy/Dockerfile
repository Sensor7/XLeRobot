FROM ros:humble-ros-base

# Install ros dependencies
RUN apt update && apt install -y cmake xdg-utils ros-humble-control-toolbox ros-humble-joint-state-publisher ros-humble-xacro ros-humble-robot-state-publisher ros-humble-joint-state-broadcaster ros-humble-joint-trajectory-controller ros-humble-plotjuggler-ros
RUN apt install -y python3-colcon-common-extensions python3-pip python3-rosdep python3-vcstool libhidapi-hidraw0
RUN apt install -y git git-lfs ffmpeg curl wget

# Intall just
RUN mkdir -p ~/bin && \
    curl --proto '=https' --tlsv1.2 -sSf https://just.systems/install.sh | bash -s -- --to ~/bin
RUN echo "export PATH=\"$PATH:$HOME/bin\"" >> ~/.bashrc
RUN echo "source /ws/install/setup.bash" >> ~/.bashrc

# Install micromamba
RUN curl -Ls https://micro.mamba.pm/api/micromamba/linux-64/latest | tar -xvj bin/micromamba
# Linux ARM64:
# curl -Ls https://micro.mamba.pm/api/micromamba/linux-aarch64/latest | tar -xvj bin/micromamba


COPY deploy/utils/81-vive.rules /etc/udev/rules.d/81-vive.rules
COPY deploy/utils/50-joycon.rules /etc/udev/rules.d/50-nintendo-switch.rules
COPY software/ /ws/software/
SHELL ["/bin/bash", "-c"]

RUN mkdir -p /ws/ && \
    cd /ws/ && \
    micromamba create -y -n lerobot python=3.10 uv && \
    git clone https://github.com/huggingface/lerobot.git

RUN cd /ws && \
    cp software/src/model/SO101Robot.py lerobot/src/lerobot/model/ && \
    cp -r software/src/robots/ lerobot/src/lerobot/ && \
    cp -r software/examples/ lerobot/ && \
    cd lerobot && \
    micromamba run -n lerobot uv pip install -e /ws/lerobot && \
    eval "$(micromamba shell hook --shell bash)"



RUN echo "export HIDAPI_BACKEND=hidraw" >> ~/.bashrc && \
    echo "source /opt/ros/humble/setup.bash" >> ~/.bashrc && \
    echo "micromamba activate lerobot" >> ~/.bashrc


WORKDIR /root/ws

