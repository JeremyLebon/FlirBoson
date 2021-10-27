# Flir Boson (USB)

[Image Color Map](/Pic/Image_ColorMap.png)


## Info
### Get info of camera
Search for video objects

```bash
ls /dev/video*
sudo apt install v4l-utils
v4l2-ctl --device /dev/video0 --all
v4l2-ctl --device /dev/video0 --list-formats-ext
```

## OpenCV
### Read thermal camera (Python)

To get the correct image of the Flir Boson camera the `YU12` has to be selected. 
Otherwise you get a low intensity image.

Other codecs can be found with the following [link](https://www.fourcc.org/codecs.php)

In the example below you get the thermal image and a color image with the cv colormap feature.
````python
# import the opencv library
import cv2
import numpy as np
  
# define a video capture objec
vid = cv2.VideoCapture(0 + cv2.CAP_V4L2)

# Get info of camera


print('WIDTH:', vid.get(cv2.CAP_PROP_FRAME_WIDTH))
print('HEIGHT:',vid.get(cv2.CAP_PROP_FRAME_HEIGHT))
print('FPS:',vid.get(cv2.CAP_PROP_FPS))
print('INIT VALUE FOURCC:',vid.get(cv2.CAP_PROP_FOURCC))
#Format needed to get to correct video feed of the Flir BOSON
vid.set(cv2.CAP_PROP_FOURCC,cv2.VideoWriter_fourcc(*"YU12"))
vid.set(cv2.CAP_PROP_CONVERT_RGB,0)

while(True):
    # Get frame
    ret, frame = vid.read()
    # Display the resulting frame
    cv2.imshow('frame', frame)
    # Get color map of frame
    im_color_normed = cv2.applyColorMap(frame,cv2.COLORMAP_JET)
    cv2.imshow('color map normed', im_color_normed)
    
    # the 'q' button is set as the
    # quitting button you may use any
    # desired button of your choice
    if cv2.waitKey(1) & 0xFF == ord('q'):
        break
  
# After the loop release the cap object
vid.release()
# Destroy all the windows
cv2.destroyAllWindows()

````

### Read Thermal camera (C++)
See Github of Teledyne Flir - [BosonUSB](https://github.com/FLIR/BosonUSB)


----
##ROS
The thermal camera can be read out with to pa 

###Own ROS package with cv_bridge

Used packages:
*   cv_bridge package + tutorial: [Wiki](http://wiki.ros.org/cv_bridge/Tutorials/ConvertingBetweenROSImagesAndOpenCVImagesPython#Converting_OpenCV_images_to_ROS_image_messages)


**Make catkin workspace**

```bash
mkdir -p ~/catkin_ws/src
$ cd ~/catkin_ws/
$ catkin_make
```

**Source your setup.bash file**

```bash
sudo nano ~/.bashrc
```

Add the following below in th .bashrc file

```bash
source ~/catkin_ws/devel/setup.bash
```

Restart your terminal!!

**Make a own rospackage**

Go the src directory in your catkin workspace
```bash
cd ~/catkin_ws/src
```
Create package with the command below
```bash
catkin_create_pkg flir_camera std_msgs sensor_msgs rospy roscpp
```
Go out the src directory and build your package with `catkin_make`
```bash
cd ~/catkin_ws
$ catkin_make
```

Add a script directory in your package diretory


```bash
cd ~/catkin_ws/src/flir_camera
mkdir scripts
cd scripts
```
Make a python script 

```bash
touch read_flir_camera.py
```
Make it executable
```bash
chmod +x read_flir_camera.py
```

Add the following code to the python script

```python
#!/usr/bin/env python
from __future__ import print_function

import roslib
#roslib.load_manifest('my_package')
import sys
import rospy
import cv2
from std_msgs.msg import String
from sensor_msgs.msg import Image
from cv_bridge import CvBridge, CvBridgeError

class image_converter:

  def __init__(self):
    self.image_pub_yu12 = rospy.Publisher("image_topic_yu12",Image,queue_size=1)
    self.image_pub_color = rospy.Publisher("image_topic_color",Image,queue_size=1)
         
    self.bridge = CvBridge()
    #self.image_sub = rospy.Subscriber("image_topic",Image,self.callback)

    self.vid = cv2.VideoCapture(0 + cv2.CAP_V4L2)
    self.vid.set(cv2.CAP_PROP_FOURCC,cv2.VideoWriter_fourcc(*"YU12"))  #YU12
    self.vid.set(cv2.CAP_PROP_CONVERT_RGB,0)
  def read(self):
    try:

      ret, frame = self.vid.read()
    except CvBridgeError as e:
      print(e)
    try:
      self.image_pub_yu12.publish(self.bridge.cv2_to_imgmsg(frame, "mono8"))
      im_color_normed = cv2.applyColorMap(frame,cv2.COLORMAP_JET)
      self.image_pub_color.publish(self.bridge.cv2_to_imgmsg(im_color_normed, "bgr8")) 
    except CvBridgeError as e:
      print(e)

def main(args):
  ic = image_converter()
  rospy.init_node('image_converter', anonymous=True)
  r=rospy.Rate(10)
  while not rospy.is_shutdown():
        ic.read()
        r.sleep()
  
  cv2.destroyAllWindows()

if __name__ == '__main__':
    main(sys.argv)







```


----
### usb_cam ros-package (Doesn't work properly)

#### Installation of usb_cam package
```bash
sudo apt install ros-melodic-usb-cam
```
#### Launch usb_cam 
```bash
roslaunch usb_cam usb_cam-test.launch 
```
The launch file can be adjusted with gedit

Go to package and adjust the 
```bash
roscd usb_cam
cd launch/
gedit usb_cam-test.launch 
```

----

### camera_cv package (Doesn't work properly)
#### Installation
sudo apt install ros-melodic-cv-camera 
####  Starting ROS node
rosrun cv_camera cv_camera_node

#### Make and adjusting the yaml-file
Go to the following directory
/home/'user'/.ros/camera_info/camera.yaml

````
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
````



Get temperature of  pixel
https://github.com/LJMUAstroecology/flirpy/blob/master/flirpy/util/raw.py 



Make own script with python to read in de FLIR camera




Make ROS package for FLIR camera





#Links 
* [flirpy](https://github.com/LJMUAstroecology/flirpy/blob/master/flirpy/util/raw.py)
* [Teledyne Flir](https://github.com/FLIR)
