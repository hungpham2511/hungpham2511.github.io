---
title:  "Logging in ROS: roscpp and rospy"
date:   2019-02-17
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
  
```
[loggers]
keys=root, rosout

[handlers]
keys=fileHandler,streamHandler

[formatters]
keys=defaultFormatter

[logger_root]
level=INFO
handlers=fileHandler

[logger_rosout]
level=DEBUG
handlers=streamHandler
propagate=1
qualname=rosout

[handler_fileHandler]
class=handlers.RotatingFileHandler
level=DEBUG
formatter=defaultFormatter
# log filename, mode, maxBytes, backupCount
args=(os.environ['ROS_LOG_FILENAME'],'a', 50000000, 4)

[handler_streamHandler]
class=rosgraph.roslogging.RosStreamHandler
level=DEBUG
formatter=defaultFormatter
# colorize output flag
args=(True,)

[formatter_defaultFormatter]
format=[%(name)s][%(levelname)s] %(asctime)s: %(message)s
```

