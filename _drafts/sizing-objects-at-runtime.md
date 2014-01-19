---
layout: post
title: Sizing objects at runtime
tags: [JVM, Java]
synopsis: Sizing objects at runtime
---
Sizing a JVM so that it can work without running out of memory is inherently hard but there are some tools and techniques to finding out how much memory your process needs.

// TODO Rework this  
There are two aspects to an application which have a compound affect on how much memory it uses. Firstly, what is the process doing? You need to understand where data is created, destroyed and thrown away. Next, how many times is it doing it?

No of consumers
Batch size
Thread count

Pretty much everything that could affect memory size without changing the processes themselves. These should all be able to be changed at runtime. i.e. properties used which can be overridden at runtime on the command line.

Primarily these are used during tuning on a production-like box but they then allow changes to the production environment to be made without a code release or hacking configuration files in situ.

Finding out how much memory your process is going to need at runtime, under load is surprisingly hard. There are a variety of tools and techniques available to help with this and I'm going to try and touch on a few of them. Sizing the JVM is not the end of the story though, probably more important is knowledge about what is using memory in your process and how it's requirements can be manipulated.
