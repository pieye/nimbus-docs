==========
Quickstart
==========

In general there are two ways to work with nimbus which are discussed in the following chapters:

1. Working directly on the Raspberry Pi (embedded)
2. Working on a desktop machine or edge computing hardware connected to a Raspberry Pi over the network

Overview about the Nimbus Software
----------------------------------

The following graphic shows the underlying architecture of the Nimbus software.
The imager takes the image and the Linux kernel module developed by us makes it available via Video4Linux.
Now two possibilities are available. 
Either the nimbus-server can be used, which makes the data available in the network via web sockets and lets you change parameters via JSON RPC.
`nimbus-python <https://github.com/pieye/nimbus-python>`_ and `nimbus-web <https://github.com/pieye/nimbus-web>`_ both use this interface.
In this way the data can be used asynchronously locally and in the network and a distributed system can be realized.
The Python interface is particularly suitable for easy use of the data. 
The web interface especially to get a live image and simply adjust the exposure.

Alternatively the local synchronous C-interface can be used, which allows the integration of own calculations into the processing stage.
So you have direct low-level access to the point cloud in C. The ROS (Robot Operating System) driver is based on this interface.


.. figure:: nimbus-processing.png
   :scale: 100 %
   :alt: Nimbus processing topology

 You can use the prepared `Raspberry Pi OS (buster) <https://cloud.pieye.org/index.php/s/XjcEaAw9keRf79S>`_ images with the already installed nimbus software.

nimbus-web
----------

After successful installation and setup of `nimbus-web <https://github.com/pieye/nimbus-web>`_ it can be accessed in the browser via the IP address of the Raspberry Pi.
On the left side the point cloud is visible, in the middle a grey value image and on the right side a depth image.
With a click on the buttons below, the exposure settings and information about the current image can be opened.

.. figure:: nimbus-web.png
   :width: 600px
   :alt: Nimbus-web Overview

nimbus-ros
----------
Robot Operating System (ROS or ros) is robotics middleware. 
Although ROS is not an operating system, it provides services designed for a heterogeneous computer cluster such as hardware abstraction, low-level device control, implementation of commonly used functionality, message-passing between processes, and package management.
Running sets of ROS-based processes are represented in a graph architecture where processing takes place in nodes that may receive, post and multiplex sensor data, control, state, planning, actuator, and other messages. 
Despite the importance of reactivity and low latency in robot control, ROS itself is not a real-time OS.

In order to use the Nimbus in ROS you need the `nimbus-ros <https://github.com/pieye/nimbus-ros>`_ and ROS itself on your Rasperry Pi.

ROS is particularly useful for more extensive projects, such as industrial robotics and autonomous systems, as well as the use of existing algorithms.
The ROS driver provides point cloud, intensity image and depth image which can be visualized with RVIZ or RQT.

.. figure:: rviz_house.png
   :width: 600px
   :alt: House in RVIZ

Furthermore, settings can be easily changed using the ROS parameter server.
Access and location transparency is easily achieved in the local network because ROS uses and abstracts the network interface.
