---
layout: post
title: "Ubuntu 11.04: Rotate only one of two monitors (nvidia, X11, xorg.conf)"
date: 2011-08-01 11:44
comments: true
categories: 
---


Edit: This may not work any more. Last time I tried it I failed. If anyone knows how to do this let me know.

Background:
---

I got a new monitor stand and I wanted to rotate one of my monitors so I can see more code per page. This works easily in Windows, but apparently no one cares enough about Ubuntu. The information I found from a Google search was not very helpful and it took me over an hour to figure out how to do this, so I hope I can help someone with this post.

Disclaimer: There are probably other ways to do this, but this is just the first thing that worked for me. If you know of a better way, please leave a comment. Note that I haven't tested this with 3D or games or anything fancy.

I have an Nvidia video card with two DVI monitor outputs and I've set up my desk with a BENQ monitor on the left rotated vertically (right side up) and an LG monitor on the right of it at a height near the middle of the other one.

Problems
---

It turns out that when I rotate the screen with RandR I can only rotate one device. With TwinView, you have one device for both monitors, so they can't be rotated individually. That leads to rotating being almost useless because one screen is always rotated wrong. It seems to me that it's impossible to have the screens rotated differently with just one device(note: I mean xorg.conf "device", not one video card) and TwinView.

When I tried to make two screens with two separate Devices, I ran into some problems. The Nvidia X Server Settings tool did not present the option to do this, so I had to edit the xorg.conf file.

Solutions
---

1. Create a copy of /etc/X11/xorg.conf by running 

        sudo cp /etc/X11/xorg.conf /etc/X11/xorg.conf.bak

    If anything goes wrong at any time, press CTRL+ALT+F1 (for example, when X11 fails to start) to enter a command line screen. Log in and type this to get your old settings back.

        sudo cp /etc/X11/xorg.conf.bak /etc/X11/xorg.conf

    You will probably just have to restart after this to be able to log in normally again.

    Note: You can always press CTRL+ALT+F7 to go back to the graphical desktop.

2. After you have a back up, go into NVIDIA X Server Settings under System > Administration and open the tab `X Server Display Configuration`
3. Click `Configure...` and select `Separate X screen`
4. Click `Enable Xinerama`
5. Select the first monitor and in the X Screen tab make sure it's positioned at `Absolute +0+0`
6. Select the second monitor and in the X Screen tab position it at `Absolute +(width of your first monitor)+0` to place it to the right of the first one.
7. Click `Save to X Configuration File` - It will complain that the positioning is absolute. Ignore it. It didn't work for me with relative positioning.
8. Save the X Configuration File. This will overwrite the file `/etc/X11/xorg.conf`
9. Log out and log in again and verify that everything works normally with the two screens beside each other.
10. Open `/etc/X11/xorg.conf` to edit it by hand. Nvidia's tool does not support these options. You need to edit it as admin, so the easiest way is to run

        sudo gedit /etc/X11/xorg.conf

    You may see the error

        Xlib:  extension "RANDR" missing on display ":0.0".

    Ignore it. You may also have gotten the error "XServer lacks support to dynamic resizing" when you logged in. Ignore this too. I didn't see a way to make it go away, but it doesn't break anything as far as I know. (maybe it breaks games?)

11. Find the `Device` corresponding to the monitor you want to rotate and add this line to its configuration.

        Option         "Rotate" "right"

    or, of course,

        Option         "Rotate" "left"


12. Now that the screen is rotated, you have to make sure that the screens are positioned beside each other or you won't be able to move your mouse from one to the other. Edit the location of the second `Screen` in Section "ServerLayout". Set the first number (the horizontal offset) to the width of the first monitor and the second number (the vertical offset) can be anything you want. If you want to position the two screens with their bottom edges aligned, try calculating width - height and using that number. It depends on how your monitors are set up physically so just play with it. You'll have to log out and log back in after every change if you want to see the effect.

        Screen      1  "Screen1" 1050 380

13. Save xorg.conf, log out and log back in. This should give you a working setup. I have experienced some glitches such as slow switching between workspaces, redraw bugs and other minor annoyances, but it works and I'm happy. This is what my xorg.conf file looks like now:

        Section "ServerLayout"
            Identifier     "Layout0"
            Screen      0  "Screen0" 0 0
            Screen      1  "Screen1" 1050 380
            Option         "Xinerama" "1"
        EndSection

        Section "Monitor"
            Identifier     "Monitor0"
            VendorName     "Unknown"
            ModelName      "BenQ FP202W"
            HorizSync       30.0 - 84.0
            VertRefresh     56.0 - 76.0
            Option         "DPMS"
        EndSection

        Section "Monitor"
            Identifier     "Monitor1"
            VendorName     "Unknown"
            ModelName      "LG Electronics W2252"
            HorizSync       28.0 - 83.0
            VertRefresh     56.0 - 75.0
        EndSection

        Section "Device"
            Identifier     "Device0"
            Driver         "nvidia"
            VendorName     "NVIDIA Corporation"
            BoardName      "GeForce 9500 GT"
            Option         "Rotate" "right"
            BusID          "PCI:2:0:0"
            Screen          0
        EndSection

        Section "Device"
            Identifier     "Device1"
            Driver         "nvidia"
            VendorName     "NVIDIA Corporation"
            BoardName      "GeForce 9500 GT"
            BusID          "PCI:2:0:0"
            Screen          1
        EndSection

        Section "Screen"

            Identifier     "Screen0"
            Device         "Device0"
            Monitor        "Monitor0"
            DefaultDepth    24
            Option         "TwinView" "0"
            Option         "TwinViewXineramaInfoOrder" "DFP-0"
            Option         "metamodes" "DFP-0: nvidia-auto-select +0+0"
            SubSection     "Display"
            Depth       24
            EndSubSection
        EndSection

        Section "Screen"
            Identifier     "Screen1"
            Device         "Device1"
            Monitor        "Monitor1"
            DefaultDepth    24
            Option         "TwinView" "0"
            Option         "metamodes" "DFP-1: nvidia-auto-select +0+0"
            SubSection     "Display"
            Depth       24
            EndSubSection
        EndSection

Please tell me if you know something I could have done better in my setup. Is there some other way to get this thing working with less glitches? Do you know why the error messages come up? Did anyone try 3D with a setup like this?
