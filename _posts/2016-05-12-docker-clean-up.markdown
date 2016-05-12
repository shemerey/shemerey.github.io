---
layout: post
title: Docker clean up
date: '2016-05-12 10:18'
---

    Warning: This will destroy all your images and containers. It will not be possible to restore them!
    
Problem: You use Docker, but working with it created lots of images and
containers. You want to remove all of them to save disk space. Some times I go
deep in cowboy style development where all I want to do is do things fast. After
this jam session I usually find my place ruined with tons of images and
containers. It's nice to start all things from scratch, and what you can do if
you want to.


You can easily remove all images and containers by executing following lines.


```bash
# Delete all containers
$ docker rm $(docker ps -a -q)
```

```bash
# Delete all images
$ docker rmi $(docker images -q)
```
