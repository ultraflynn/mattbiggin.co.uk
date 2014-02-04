---
layout: post
title: When is a Boolean not a Boolean?
tags: [Apache Camel, Java]
thumbnail: /images/GeorgeBoole.jpg
synopsis: You think you’re adding a Boolean flag to the header of a message, but are you? Is that flag really a String with the text "true" or "false"? Does is really matter?
---
You have to be careful doing the obvious thing sometimes because it doesn’t always give you the result to want. For instance, I had a requirement to add a header to a message generated in an Apache Camel route. Seemed simple to be so I added this:
 
    <setHeader headerName="NEW_HEADER">
        <constant>true</simple>
    </setHeader>
 
The hidden issue with this is that the header will be a java.lang.String and it will have the value of “true”. Now in many situations that’s not a problem, however the upstream system for me cared about whether this was a java.lang.String or a java.lang.Boolean. The fix is pretty simple though. You just tell Camel what the return should be using a simple expression. Like this:
 
    <setHeader headerName="NEW_HEADER">
        <simple resultType="java.lang.Boolean">true</simple>
    </setHeader>