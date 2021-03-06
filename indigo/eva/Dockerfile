#
# Container for the Hanson Robotics Eva head (full version)
# This provides the complete running package for the demo head,
# including UVC webcam vision input, pi_vision to discover faces,
# behavior controled by owyl trees, and the full motor control output.
#
# To build:
# sudo docker build -t opencog/eva .
#
FROM opencog/ros-indigo-blender
MAINTAINER Linas Vepštas "linasvepstas@gmail.com"

# Install required packages
ENV LAST_OS_UPDATE 2015-02-16
RUN apt-get -y update
RUN apt-get -y upgrade
RUN apt-get -y install ros-indigo-tf ros-indigo-driver-common
RUN apt-get -y install ros-indigo-cv-bridge ros-indigo-image-transport
RUN apt-get -y install ros-indigo-openni-camera ros-indigo-mjpeg-server
RUN apt-get -y install ros-indigo-usb-cam
# The below are needed by perception node
RUN apt-get -y install ros-indigo-dynamixel-msgs
RUN apt-get -y install ros-indigo-robot-state-publisher
RUN apt-get -y install ros-indigo-joint-state-publisher

# Owyl setup
WORKDIR /opt/
RUN git clone https://github.com/eykd/owyl.git
WORKDIR /opt/owyl/
RUN python setup.py install

# Install catkinized packages
WORKDIR /catkin_ws/src

RUN git clone https://github.com/hansonrobotics/pi_vision.git
RUN git clone https://github.com/hansonrobotics/perception.git
RUN git clone https://github.com/hansonrobotics/eva_behavior.git
RUN git clone https://github.com/hansonrobotics/blender_api_msgs.git
RUN git clone https://github.com/hansonrobotics/blender_api.git
RUN git clone https://github.com/hansonrobotics/pau2motors.git

# RUN git clone https://github.com/geni-lab/ros_pololu_servo.git
RUN git clone https://github.com/hansonrobotics/ros_pololu_servo.git
RUN git clone https://github.com/hansonrobotics/robots_config.git

# RUN git clone https://github.com/hansonrobotics/ros_motors_webui.git
# # RUN git clone https://github.com/hansonrobotics/chatbot.git
# # RUN git clone https://github.com/hansonrobotics/ros_faceshift.git

# Change line below on rebuild. Will use cache up to this line
ENV LAST_SOFTWARE_UPDATE 2015-02-19

# Git pull for all packages
RUN cd /catkin_ws/src/ && find . -maxdepth 1 -mindepth 1 -type d \
	-execdir git --git-dir=$PWD/{}/.git --work-tree=$PWD/{} pull \;

# The blender API has not been fully catkinized yet.
RUN pip3 install ./blender_api_msgs/

# Some versions of ros_pololu_server will fail during build; the
# work-around for this is to build twice (This because the missing
# files are autogened, and the pololu Cmakefiles fail to list them as
# dependencies.)
WORKDIR /catkin_ws
RUN bash -l -c "/opt/ros/indigo/bin/catkin_make" || true
RUN bash -l -c "/opt/ros/indigo/bin/catkin_make"

RUN echo source /catkin_ws/devel/setup.bash >> ~/.bashrc

COPY /scripts/eva.sh /catkin_ws/eva.sh

# Ports exposed
EXPOSE 9090
EXPOSE 80
EXPOSE 33433

ENTRYPOINT bash -l -c "./eva.sh; bash"
