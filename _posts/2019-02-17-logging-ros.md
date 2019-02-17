---
title:  "Logging in ROSS: roscpp and rospy"
date:   2018-02-17 +0800
categories: ros
tags: ros logging
layout: single
published: false
---


- Logging in ROS

- a note on rospy and roscpp
  - each utilizes the logging capability of python and C++ (log4cxx) resp.
  - emphasize: different systems
  - I made the mistake of thinking that both are the same, waste much time.
  
# Section: python logging

- python logging systems overview

- hierachry of loggers

- loghandler

# Section: rospy logging

- rospy loggers 

  - root: parent of all loggers
  - 

- configuration with ROS

  <env name="ROS_PYTHON_LOG_CONFIG_FILE" value="$(find assembly_demo)/python_logging.conf"/>
  
  

