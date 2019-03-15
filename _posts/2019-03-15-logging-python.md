---
title:  "Logging in Python"
date:   2018-03-15 +0800
categories: programming
tags: ros logging
layout: single
published: true
---
# Overview
Logging properly is important. Beside from being much easier to
develop, an application with a good logging system is much easier to
maintain. This note briefly describes how one can perform logging in
Python.

To a certain extend, logging is just a upgraded `print` statement. The
application basically "prints" messages as it runs, allowing users and
developers to have some idea about its internal magics and
stupidity. However, different from the simple `print` function,
logging has several advantages. First, it can be done at several
levels, effectively reflecting how urgent messages are. For instance,
debug messages contain non-essential information that is only useful
during debuggin; critical messages, on the other hand, are only
emitted when applications face with unexpected situations and errors.
The second difference is that log messages can be redirected to places
other than screen, such as files in a flexible fashion. You will not
have to worry about having your terminal flooded with non-essential
information any more.

It is worth noting that logging is also useful for debugging. In
multi-threaded applications, debugging with debuggers is usually
tricky. The debugger basically stops threads, causing them to work
differently from the normal working condition. In contrast, logging
are done within the threads without interuptions, reflecting a much
better idea of what has actually happen.

# Logging 101

All that benefits of logging come at literaly no cost: logging is
usually very simple in modern software development. Almost all
languages have builtin, or very matured logging solutions. Generally
speaking, logging consists of two separate stages:

- *the library* initiates loggers, which emit (print) log messages;
- *the library user* configures the loggers to emit messages where she
  wants to.

Both steps are extremely easy in Python. To create logger, do:

``` python
import logging
logger = logging.getLogger('awesome_app.libA')
```

The loggers should be named in a descritive manner. For instance, the
logger for the planning module could be named `awesome_app.planning`;
the logger for the control module could be named `awesome_app.control`
instead. Good naming makes filtering and recording logging much easier.

Logging is done with the logger as below:

```python
logger.debug("Detailed information, typically of interest only when diagnosing problems.")
logger.info("Confirmation that things are working as expected.")
logger.warning("An indication that something unexpected happened.")
logger.error("A more serious problem. Some functions fail.")
logger.critical("Serious error. The application is terminating.")
```

That's it about creating logger and logging in Python.

To configure loggers, there are three ways: programmatically,
configuration file and yaml file. I often use configuration files,
because of legacy reasons. Here is an example configuration file:

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

That's it. Hope this short note helps!
