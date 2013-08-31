---
layout: post
title: "Network Code Module"
date: 2013-08-30 21:38
comments: true
categories: 
---

In the Winter term of 2013, [Francis Williams](http://fwilliams.org) and I worked on an interesting project with one of our professors, [Sebastian Fischmeister](https://uwaterloo.ca/embedded-software-group/people-profiles/sebastian-fischmeister). The goal was to implement, in software, a language for real time communication schedules that he invented. The [paper](https://uwaterloo.ca/embedded-software-group/sites/ca.embedded-software-group/files/uploads/files/NC-TC_0.pdf) is available. Our [final result](https://bitbucket.org/fwilliams/ncm) is also available. It was based on a slightly newer version of the language that was already implemented in hardware in an FPGA.

The project required us to learn how to write a Linux kernel module for RTLinux. We had to send and receive packets in real time. Ultimately, the network code module modified the state of variables that we made accessible to userspace. We chose to use /sys as the interface because it's a very clean way of communicating small amounts of data between kernelspace and userspace. We also tried /dev, but we ran into issues reading from the device. We had a collection of command line tools to convert network code machine instructions between different formats. In addition to the kernel module, we wrote a tool for visualizing network schedules. It was web based and it read input from a file generated using a python script.

The list of technologies we had to use to accomplish this is:

* **ArchLinux** because it's awesome
* **VirtualBox** because we blew up the kernel over 9000 times
* **Linux kernel source code reading** like a baws
* **C** with no chaser
* **Make** because Linux kernel :/
* **Bash** scripts because why not?
* **Python 2 and 3** - yay!
* **Bootstrap, JavaScript, CSS, HTML**, etc. because who doesn't love a pretty UI!
