# Rviz Visual Tools

Helper functions for displaying and debugging data in Rviz via published markers.

This package includes:

 - Basic geometric markers for Rviz
 - A tf publishing helper class

Developed by [Dave Coleman](http://dav.ee) at the Correll Robotics Lab, University of Colorado Boulder with help from Andy McEvoy and others.

 * [![Build Status](https://travis-ci.org/davetcoleman/rviz_visual_tools.svg)](https://travis-ci.org/davetcoleman/rviz_visual_tools) Travis CI
 * [![Build Status](http://build.ros.org/buildStatus/icon?job=Jsrc_uT__rviz_visual_tools__ubuntu_trusty__source)](http://build.ros.org/view/Jsrc_uT/job/Jsrc_uT__rviz_visual_tools__ubuntu_trusty__source/) ROS Buildfarm - Trusty Devel Source Build
 * [![Build Status](http://build.ros.org/buildStatus/icon?job=Jbin_uT64__rviz_visual_tools__ubuntu_trusty_amd64__binary)](http://build.ros.org/view/Jbin_uT64/job/Jbin_uT64__rviz_visual_tools__ubuntu_trusty_amd64__binary/) ROS Buildfarm - AMD64 Trusty Debian Build

![](resources/screenshot1.png)

## Install

### Ubuntu Debian

```
sudo apt-get install ros-indigo-rviz-visual-tools
```

### Build from Source

Clone this repository into a catkin workspace, then use the rosdep install tool to automatically download its dependencies. Depending on your current version of ROS, use:
```
rosdep install --from-paths src --ignore-src --rosdistro indigo
```

## Quick Start Demo

To see random shapes generated in Rviz, first launch Rviz:

    roslaunch rviz_visual_tools demo_rviz.launch

Then start demo:

    roslaunch rviz_visual_tools demo.launch

## Code API

See [VisualTools Class Reference](http://docs.ros.org/indigo/api/rviz_visual_tools/html/classrviz__visual__tools_1_1VisualTools.html)

## Usage

We'll assume you will be using these helper functions within a class. Almost all of the functions assume you are publishing transforms in the world frame (whatever you call that e.g. /odom).

### Initialize

Add to your includes:
```
#include <rviz_visual_tools/visual_tools.h>
```

Add to your class's member variables:
```
// For visualizing things in rviz
rviz_visual_tools::VisualToolsPtr visual_tools_;
```

In your class' constructor add:
```
visual_tools_.reset(new rviz_visual_tools::VisualTools("base_frame","/rviz_visual_markers"));
```

Change the first parameter to the name of your robot's base frame, and the second parameter to whatever name you'd like to use for the corresponding Rviz marker ROS topic.

### Tools

Now in your code you can easily debug your code using visual markers in Rviz

Start rviz and create a new marker using the 'Add' button at the bottom right. Choose the marker topic to be the same as the topic you specified in the constructor.

### Example Code

In the following snippet we create a pose at xyz (0.1, 0.1, 0.1) and rotate the pose down 45 degrees along the Y axis. Then we publish the pose as a arrow for visualziation in Rviz. Make sure your Rviz fixed frame is the same as the one chosen in the code.

    // Create pose
    Eigen::Affine3d pose;
    pose = Eigen::AngleAxisd(M_PI/4, Eigen::Vector3d::UnitY()); // rotate along X axis by 45 degrees
    pose.translation() = Eigen::Vector3d( 0.1, 0.1, 0.1 ); // translate x,y,z

    // Publish arrow vector of pose
    ROS_INFO_STREAM_NAMED("test","Publishing Arrow");
    visual_tools_->publishArrow(pose, rviz_visual_tools::RED, rviz_visual_tools::LARGE);


### Basic Publishing Functions

See ``visual_tools.h`` for more details and documentation on the following functions:

 - publishSphere
 - publishSpheres
 - publishArrow/publishXArrow
 - publishYArrow
 - publishZArrow
 - publishCuboid
 - publishCone
 - publishXYPlane
 - publishXZPlane
 - publishYZPlane
 - publishLine
 - publishPath
 - publishPolygon
 - publishBlock
 - publishWireframeCuboid
 - publishWireframeRectangle
 - publishAxis
 - publishAxisLabeled
 - publishCylinder
 - publishMesh
 - publishMesh
 - publishText
 - publishTest

And more...

### Helper Functions

Reset function

 - ``deleteAllMarkers`` - tells Rviz to clear out all current markers from being displayed.

Batch publish - useful for when many markers need to be published at once to prevent buffer overflow of ROS messages.

 - enableBatchPublishing()
 - triggerBatchPublish()
 - triggerBatchPublishAndDisable()

Conversion functions

 - convertPose
 - convertPoint32ToPose
 - convertPoseToPoint
 - convertPoint
 - convertPoint32
 - convertFromXYZRPY
 - convertToXYZRPY

Convenience functions

 - generateRandomPose
 - generateEmptyPose
 - dRand
 - fRand
 - iRand
 - getCenterPoint
 - getVectorBetweenPoints

### Available Colors

This package helps you quickly choose colors - feel free to send PRs with more colors as needed

    BLACK,
	BLUE,
    BROWN,
    CYAN,
    DARK_GREY,
    GREEN,
    GREY,
    LIME_GREEN,
    MAGENTA,
    ORANGE,
    PINK,
    PURPLE,
    RED,
    WHITE,
    YELLOW,
    TRANSLUCENT_LIGHT,
    TRANSLUCENT,
    TRANSLUCENT_DARK,
    RAND,
	CLEAR,
    DEFAULT // i.e. 'do not change default color'

### Available Marker Sizes

    XXSMALL,
    XSMALL,
    SMALL,
    REGULAR,
    LARGE,
    xLARGE,
    xxLARGE,
    xxxLARGE,
    XLARGE,
    XXLARGE

## Batch Publishing

There is a new feature that allows markers to be saved up in an array until a trigger is recieved to send all markers to Rviz for visualization. This is useful when many markers need to be published at once that can overflow the Rviz message buffers. To enable, use ``enableBatchPublishing(true)`` and to trigger use ``triggerBatchPublish()`.

## TF Visual Tools

This tool lets you easily debug Eigen transforms in Rviz. Demo use:

    rviz_visual_tools::TFVisualTools tf_visualizer;
    Eigen::Affine3d world_to_shelf_transform = Eigen::Affine3d::Identity(); // or whatever value
    tf_visualizer.publishTransform(world_to_shelf_transform, "world", "shelf");

## Testing and Linting

To run [roslint](http://wiki.ros.org/roslint), use the following command with [catkin-tools](https://catkin-tools.readthedocs.org/):

    catkin build --no-status --no-deps --this --make-args roslint

To run [catkin lint](https://pypi.python.org/pypi/catkin_lint), use the following command with [catkin-tools](https://catkin-tools.readthedocs.org/):

    catkin lint -W2

Use the following command with [catkin-tools](https://catkin-tools.readthedocs.org/) to run the small amount of available tests:

    catkin run_tests --no-deps --this -i

## Contribute

Please send PRs for new helper functions, fixes, etc!
