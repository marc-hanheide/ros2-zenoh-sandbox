# Playground to test ROS1, ROS2 and Zenoh bridges

The main documentation is the actual [`docker-compose.yml`](./docker-compose.yml) file. It sets up the following:

* Several *replicas" of ROS2 containers (named `ros_publisher`) that uses Cyclone DDS and publish on the topic `/foo`. This is connected to the network `ros_network_1`. Think of it as running on multiple robots or ROS nodes running in a connected ROS2 network
* Also in the `ros_network_1` runs a [Zenoh-ros2dds bridge](https://github.com/eclipse-zenoh/zenoh-plugin-ros2dds) (container named `zenoh`). It bridges the Cyclone DDS ROS2 with Zenoh. This acts as a server that *listens* on port 7447 for other Zenoh peers and on 8888 it exposes its [HTTP REST API](https://zenoh.io/docs/apis/rest/), both on a `zenoh_network`, i.e. this is the network (e.g. Wifi, 4G) that connects different robots or a central controller to robots etc.
* In another `ros_network_2` (think of it as a robot coordinator, or another robot, which is not directly using DDS to talk to the first network), there are a ROS2 subscriber (`ros2_subscriber`) on the `/foo` topic. And on this second network there runs another [Zenoh-ros2dds bridge](https://github.com/eclipse-zenoh/zenoh-plugin-ros2dds) (container named `zenoh_conect`), but here it *connects* (it initiates the connection, i.e., it conects to the other bridge on its port 7447) to the the previous Zenoh bridge via the `zenoh_network`.
* Also on the `ros_network_2` runs a [ROS2-ROS1 bridge](https://github.com/ros2/ros1_bridge) (container `ros_bridge`), and a ROS1 subscriber (container `ros1_subscriber`), to test the ability to bridge to ROS1 containers. The `roscore` is being run as part of the `ros_bridge`


## Overview of the network setup and the protocols between different containers

[![](http://interactive.blockdiag.com/nwdiag/image?compression=deflate&encoding=base64&src=eJyNkcsKwjAQRff9iiFrXVQERek3CLoUKU0ytMWSlEmKL_rvxjTFovWRVcjce-fMRJ1kmeVwiwAU2pOmI5A2abinsa8AXFHpAvaZlITGJExcRKUVTqU0bAKmyGpMSDdKouT6fFh7kwuapXXDq9IUSC5r3O_V7QDAN-sRfgOsFvP5goWenVdopVDYb_qP1F2Cc9nUNNwIKjnSIKmwtl4t3XkHH25uFsD9EkaD3nbwF30vzR14HXoAVBnHChJgDwROpcyRhdLzZRC43eziCfz1i12C0PTq70nafs54fM6nto2i9g5uC85n)](http://interactive.blockdiag.com/nwdiag/?compression=deflate&src=eJyNkcsKwjAQRff9iiFrXVQERek3CLoUKU0ytMWSlEmKL_rvxjTFovWRVcjce-fMRJ1kmeVwiwAU2pOmI5A2abinsa8AXFHpAvaZlITGJExcRKUVTqU0bAKmyGpMSDdKouT6fFh7kwuapXXDq9IUSC5r3O_V7QDAN-sRfgOsFvP5goWenVdopVDYb_qP1F2Cc9nUNNwIKjnSIKmwtl4t3XkHH25uFsD9EkaD3nbwF30vzR14HXoAVBnHChJgDwROpcyRhdLzZRC43eziCfz1i12C0PTq70nafs54fM6nto2i9g5uC85n)

