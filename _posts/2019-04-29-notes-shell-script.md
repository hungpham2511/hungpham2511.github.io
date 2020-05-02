---
title:  "Some notes on shell scripts commands"
date:   2019-4-28 
tags: programming
layout: single
published: false
---


```bash
set -e
```

This command makes a shell script terminate as soon as it hits an
error.

```bash
set -o pipefail
```

With this command set at the beginning, a shell script has exit code 0
if and only if all commands exit successfully.


```bash
set -x
```
This command makes the script prints each command before execution.
