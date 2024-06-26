# Playground to test ROS1, ROS2 and Zenoh bridges

The main documentation is the actual [`docker-compose.yml`](./docker-compose.yml) file. It sets up the following:

* Several *replicas* of ROS2 containers (named `ros_publisher`) that uses Cyclone DDS and publish on the topic `/foo`. This is connected to the network `ros_network_1`. Think of it as running on multiple robots or ROS nodes running in a connected ROS2 network
* Also in the `ros_network_1` runs a [Zenoh-ros2dds bridge](https://github.com/eclipse-zenoh/zenoh-plugin-ros2dds) (container named `zenoh`). It bridges the Cyclone DDS ROS2 with Zenoh. This acts as a server that *listens* on port 7447 for other Zenoh peers and on 8888 it exposes its [HTTP REST API](https://zenoh.io/docs/apis/rest/), both on a `zenoh_network`, i.e. this is the network (e.g. Wifi, 4G) that connects different robots or a central controller to robots etc.
* In another `ros_network_2` (think of it as a robot coordinator, or another robot, which is not directly using DDS to talk to the first network), there are a ROS2 subscriber (`ros2_subscriber`) on the `/foo` topic. And on this second network there runs another [Zenoh-ros2dds bridge](https://github.com/eclipse-zenoh/zenoh-plugin-ros2dds) (container named `zenoh_conect`), but here it *connects* (it initiates the connection, i.e., it conects to the other bridge on its port 7447) to the the previous Zenoh bridge via the `zenoh_network`.
* Also on the `ros_network_2` runs a [ROS2-ROS1 bridge](https://github.com/ros2/ros1_bridge) (container `ros_bridge`), and a ROS1 subscriber (container `ros1_subscriber`), to test the ability to bridge to ROS1 containers. The `roscore` is being run as part of the `ros_bridge`
* It also features a [Zenoh MQTT bridge](https://lib.rs/crates/zenoh-plugin-mqtt) (named: `zenoh_mqtt`), that connects to the `zenoh` container and acts as an MQTT broker, allowing MQTT clients to receive/publish any ROS topics

## Overview of the network setup and the protocols between different containers

[![](http://interactive.blockdiag.com/nwdiag/image?compression=deflate&encoding=base64&src=eJyNks1qhDAUhfc-xSVru3A6MGLxGQrT5VCCSS4qtYlNItMffPdmkojSiVBXau4557tH5VX0TQs_GYBEe1X6DbQyNN7Twp8AfKNUHVwaITQaUxP-xQcl8UEIQ3IwXTNirdUkBQqmPl-fvMgZHeg4saE3HWrnldb76XkD4MMWhB0A_1ydjsdTDp21Y1W6i8TgYMCVlMhtUrQPHbRu3lIzMcN1z1BvPHbC3j-spUz3okW4BGuyeru4JPlNVRVl-XhfwvYrHGIJvtAk1V2f-yUkRltHOsYMgKFhOEAN5IYQViLxaH2zMTw_vxQ5_OuPCA5c6b_6hWRe9izSe66zc5bNvxAi5rA)](http://interactive.blockdiag.com/nwdiag/?compression=deflate&src=eJyNks1qhDAUhfc-xSVru3A6MGLxGQrT5VCCSS4qtYlNItMffPdmkojSiVBXau4557tH5VX0TQs_GYBEe1X6DbQyNN7Twp8AfKNUHVwaITQaUxP-xQcl8UEIQ3IwXTNirdUkBQqmPl-fvMgZHeg4saE3HWrnldb76XkD4MMWhB0A_1ydjsdTDp21Y1W6i8TgYMCVlMhtUrQPHbRu3lIzMcN1z1BvPHbC3j-spUz3okW4BGuyeru4JPlNVRVl-XhfwvYrHGIJvtAk1V2f-yUkRltHOsYMgKFhOEAN5IYQViLxaH2zMTw_vxQ5_OuPCA5c6b_6hWRe9izSe66zc5bNvxAi5rA)

## Images used:
```
eclipse/zenoh-bridge-mqtt:latest
lcas.lincoln.ac.uk/lcas/ros1_bridge:latest
lcas.lincoln.ac.uk/lcas/ros2_test:latest
lcas.lincoln.ac.uk/lcas/zenoh_ros2dds_bridge:latest
ros:noetic
```