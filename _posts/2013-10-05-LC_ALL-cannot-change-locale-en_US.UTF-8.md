---
layout: post
title: "LC_ALL: cannot change locale (en_US.UTF-8)"
type: post
tags:
- linux
status: publish
published: true
---

If you get this warning message from bash every time you start a shell

```bash
/bin/bash: warning: setlocale: LC_ALL: cannot change locale (en_US.UTF-8)
```

You can resolve it easily by generation the locale with
```bash
sudo locale-gen en_US.UTF-8
```
.
