# DVS Gazebo Plugin

This package provides a DVS simulation implemented as a Gazebo plugin.

## Install

`$ mkdir -p catkin_ws/src`

`$ cd catkin_ws`

First, clone the following ROS DVS package which is supported under ROS Kinetic (Ubuntu 16.04), ROS Melodic (Ubuntu 18.04) and ROS Noetic (Ubuntu 20.04).

`$ git clone https://github.com/uzh-rpg/rpg_dvs_ros `

`$ git clone https://github.com/catkin/catkin_simple.git`

Build the packages to make sure the DVS datatypes are supported now.

`$ git clone https://github.com/Jinczhg/gazebo_event_cam_plugin.git`

Rebuild.

## Usage

This plugin can be used as a drop-in replacement for normal Gazebo camera plugins.
Both, the DVS plugin and the [CameraPlugin](https://bitbucket.org/osrf/gazebo/src/666bf30ad9a3c042955b55f79cf1a5416a70d83d/plugins/CameraPlugin.cc)
use the Gazebo [CameraSensor](https://bitbucket.org/osrf/gazebo/src/666bf30ad9a3c042955b55f79cf1a5416a70d83d/gazebo/sensors/CameraSensor.cc) internally.

The following XACRO snippet shows an example of usage (can also be found in lines 317-374 in _xacro/iris.xacro_):

    <sensor name='event_camera' type='camera'>
        <camera name='dvs_event_cam'>
            <horizontal_fov>2.8</horizontal_fov>
            <image>
                <width>128</width>
                <height>128</height>
            </image>
            <clip>
                <near>0.1</near>
                <far>100</far>
            </clip>
        </camera>
        <always_on>1</always_on>
        <update_rate>120</update_rate>
        <!--<visualize>0</visualize>-->
        <plugin name="event_camera_simulator" filename='libgazebo_dvs_plugin.so'>
            <cameraName>dvs</cameraName>
            <frameName>/event_camera_link</frameName>
            <!--<robotNamespace>AADC_AudiTT</robotNamespace>-->
            <eventThreshold>5</eventThreshold>
            <cameraInfoTopicName>camera_info</cameraInfoTopicName>
            <eventsTopicName>events</eventsTopicName> 
        </plugin>
      </sensor>

The parameters `robotNamespace`, `cameraName` and `eventsTopicName` (default: "events") result in `"$robotNamespace/$cameraName/$eventsTopicName"`
as the identifier of the provided events topic.
In this case, events will be accessible from `"/AADC_AudiTT/camera_front/events"`.

The parameter `eventThreshold` specifies the pixel-wise threshold which has to be exceeded for a event to be emitted for this pixel.

The sensor parameter `update_rate` has only limited effect in Gazebo.
The real rate is determined by the rendering pipeline and can be way lower than the specified rate.
Still, this implementation yields a higher event frequency than similar Python-based implementations as a standalone node.

#### Useful commands to convert a Xacro file to a URDF file or a SDF file:

`$ xacro iris.xacro > model.urdf `

`$ gz sdf -p model.urdf > model.sdf`

# Acknowledgement

If you used this code for your research, please consider citing the paper [Towards a framework for end-to-end control of a simulated vehicle with spiking neural networks](http://ieeexplore.ieee.org/document/7862386/).

```
@INPROCEEDINGS{7862386,
author={J. Kaiser and J. C. V. Tieck and C. Hubschneider and P. Wolf and M. Weber and M. Hoff and A. Friedrich and K. Wojtasik and A. Roennau and R. Kohlhaas and R. Dillmann and J. M. Zöllner},
booktitle={2016 IEEE International Conference on Simulation, Modeling, and Programming for Autonomous Robots (SIMPAR)},
title={Towards a framework for end-to-end control of a simulated vehicle with spiking neural networks},
year={2016},
pages={127-134},
keywords={automobiles;cameras;complex networks;feedforward neural nets;learning (artificial intelligence);mobile robots;DVS;camera images;complex networks;deep learning architectures;end-to-end simulated vehicle control;hand-crafted feature detectors;neural self-driving vehicle applications;neurorobotics applications;rate-based neural networks;silicon retina;spiking neural networks;steering wheel decoder;vehicle end-to-end for lane following behavior;Biological neural networks;Brain modeling;Cameras;Robot sensing systems;Voltage control},
doi={10.1109/SIMPAR.2016.7862386},
month={Dec},}
```
