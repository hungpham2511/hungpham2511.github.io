---
title:  "Returning None or raising Exception"
date:   2019-4-28 
tags: programming
layout: single
published: true
category: programming
---

During my development of
[`toppra`](https://github.com/hungpham2511/toppra), a thing bugs me
quite often: how to handle failure? The problem is that `toppra` is a
fairly complicated algorithm with several hotspots for numerical
instabilities. In another words, it fails often and in different
places. For example, consider the below pseudo-code:

```python
def solve_toppra(inputs):
    ret = func1()
    ret = func2(ret)
    ret = func3(ret)
	return ret
```

Now, the algorithm can fail at different points. As a result, there
are a lots of error-checking stuffs in my implementation:

```python
def solve_toppra(inputs):
    ret = func1()
    if ret is None:
        return None
    ret = func2(ret)
    if ret is None:
        return None
    ret = func3(ret)
    return ret
```

Now, the code is rather convoluted. The original version without error
checking looks much cleaner and resemble the pseudo-code
better. Furthermore, it is also very difficult to know why the
algorithm fails, as the result of `solve_toppra` is `None`, conveying
next to nothing information. Indeed, everytime `toppra` fails I have
to look into the logs to figure which part has gone wrong, which is
really tedious.

The solution 
http://www.cauldwell.net/patrick/blog/ThisIBelieveTheDeveloperEdition.aspx
