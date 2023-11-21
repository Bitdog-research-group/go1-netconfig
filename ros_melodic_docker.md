# Bitdog on ROS Melodic Docker Container

## Introduction

Once the Distro that the robot is using until now is ROS Melodic, this guide shows how to use it and visualize the rostopics on your local machine through docker. 

## Steps

First, execute the following to upload the docker iamge of ros-melodic with RVIZ.

```docker pull tiryoh/ros-melodic-desktop:melodic```

Then, run the following command to run the docker container.

``` 
    docker run -it \
  --user=root \
  --privileged \
  --network=host \
  --privileged \
  --env="DISPLAY=$DISPLAY" \
  --env="QT_X11_NO_MITSHM=1" \
  --volume="/tmp/.X11-unix:/tmp/.X11-unix:rw" \
  --env="XAUTHORITY=$XAUTH" \
  --volume="$XAUTH:$XAUTH" \
  -v "/dev:/dev" \
  tiryoh/ros-melodic-desktop:melodic
  ```



Now you have to export the IP address to ROS, so it can communicate with master in your container.

```
export ROS_MASTER_URI=http://192.168.123.15:11311
export ROS_IP=192.168.12.1
```


Finally, run the following command to run RVIZ.

```rosrun rviz rviz -d ./build_map.rviz```
