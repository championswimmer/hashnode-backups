---
title: "MX Master on Mac OS - Jittery Cursor Position and Jumping Pointer"
datePublished: Tue Jan 02 2024 20:23:12 GMT+0000 (Coordinated Universal Time)
cuid: clqwsqzf3000008jq2ezdfzlh
slug: mx-master-on-mac-os-jittery-cursor-position-and-jumping-pointer
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/UihqZIiVcxY/upload/37e91e1f33228701264bac33aa7e4aa7.jpeg
tags: mouse, macos, mx-master

---

I have been a longtime MX Master user. 5+ years with the MX Master 2S and not 1+ with MX Master 3. (I do not use the 3S because it has a different kind of receiver that doesn't let me use the same receiver for MX Keys)

One issue that has been plaguing me recently was that the cursor position was jittery, and when moving the mouse, the cursor was jumping around a bit.

I noticed that it happend a lot more if I was having an additional external monitor attached.

Just to note it down properly, here are the things I am using

* Macbook Pro 16" - 2021 Model (M1 Max)
    
* MacOS 12.1
    
* MX Master 3 Mouse
    
* Logi Options+ 1.60.xxx
    

So what solved the problem was a weird set of steps (I do not know why this solved the problem).

1. I turned down the Mouse tracking speed in system settings to really low
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1704226746151/bed023c9-25c4-4fa2-8a68-c49955161944.png align="center")
    
2. Inside Logi Options+ I increased the pointer speed to much higher to compensate for that.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1704226833338/dcb24476-e7b4-4a75-b72a-5c95dcd09fbf.png align="center")
    

If I have to hazard a guess, this way the actual hardware interface tracks the mouse much slower (hence less jumps) and the Logi Options+ driver translates that to bigger movements at the software layer. I do not know this for sure, and thus I do not know exactly why this set of steps fixes this problem, but that it does.

Anyway documenting this mainly for myself to find this solution back again few years later when I update my Macbook to M4 Max or something and face this problem again I guess. If you faced the same and this solved for you, do leave a comment. Thanks :)