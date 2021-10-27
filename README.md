Flir Boson (USB)
-----

# Read thermal camera with OpenCV (Python)






# Get video with usc

The thermal camera can be read out with to pa 

# usb_cam package

##Installation of usb_cam package
sudo apt install ros-melodic-usb-cam

##Launch usb_cam 

roslaunch usb_cam usb_cam-test.launch 

The launch file can be adjusted with gedit

Go to package and adjust the 

roscd usb_cam
cd launch/
gedit usb_cam-test.launch 



#camera_cv package
## Installation
sudo apt install ros-melodic-cv-camera 
##  Starting ROS node
rosrun cv_camera cv_camera_node

## Make and adjusting the yaml-file
Go to the following directory
/home/'user'/.ros/camera_info/camera.yaml

image_width: 640
image_height: 512
camera_name: camera
cv_cap_prop_contrast: 0.75 
cv_cap_prop_brightness: 0.25
cv_cap_prop_saturation: 0.25 
cv_cap_prop_hue: 0.25
cv_cap_prop_gain: 0.25
camera_matrix:
  rows: 3
  cols: 3
  data: [767.942728, 0.000000, 317.244746, 0.000000, 767.068789, 231.644928, 0.000000, 0.000000, 1.000000]
distortion_model: plumb_bob
distortion_coefficients:
  rows: 1
  cols: 5
  data: [-0.504042, 0.268940, 0.003454, -0.000748, 0.000000]
rectification_matrix:
  rows: 3
  cols: 3
  data: [1.000000, 0.000000, 0.000000, 0.000000, 1.000000, 0.000000, 0.000000, 0.000000, 1.000000]
projection_matrix:
  rows: 3
  cols: 4
  data: [694.958557, 0.000000, 316.182977, 0.000000, 0.000000, 720.968750, 229.582940, 0.000000, 0.000000, 0.000000, 1.000000, 0.000000]



search for video objects
ls /dev/video*
sudo apt install v4l-utils
v4l2-ctl --device /dev/video0 --all
v4l2-ctl --device /dev/video0 --list-formats-ext

Make own script with python to read in de FLIR camera




Make ROS package for FLIR camera






