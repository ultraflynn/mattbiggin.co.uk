---
layout: post
title: SEDA queue sizes and importance of reading API docs
tags: [Apache Camel, Java, Seda]
synopsis: TODO
---
One of the key benefits of a SEDA queue is the ability to buffer requests and tasks within an Apache Camel route. You split up your processing into discrete tasks and then wire these tasks together using SEDA queues. You can then tune the number of consumers you have running against the queues to get the throughput that you need. To be able to do this tuning you need to know the back pressure on the consumers, i.e. the size of the SEDA queues. A simple way to do this is to use JMX to connect via JConsole and inspect the size of the SEDA queues but that involves starting a process and drilling down through a graphical UI. It’s a lot easier to have the queues depths output to the log file regularly so you can review the sizes over time.
 
The API for doing this is fairly simple:
 
    SedaEndpoint sedaEndpoint = camelContext.getEndpoint(sedaEndpointName,  SedaEndpoint.class);
    LOGGER.info("queue size: " + sedaEndpoint.queueSize());
 
In my production code I had a little more checking around ensuring that the endpoint was found but what I noticed though was that even if the endpoint didn’t exist the call to getEndpoint() always worked and returned a valid endpoint. A quick look at the API docs revealed this:
 
    /**
    * Resolves the given name to an {@link Endpoint} of the specified type.
    * If the name has a singleton endpoint registered, then the singleton is returned.
    * Otherwise, a new {@link Endpoint} is created and registered.
    *
    * @param name         the name of the endpoint
    * @param endpointType the expected type
    * @return the endpoint
    */
    <T extends Endpoint> T getEndpoint(String name, Class<T> endpointType);
 
The critical thing here is that if the endpoint doesn’t exist Apache Camel actually creates the endpoint dynamically. I checked my production instance using JConsole  and confirmed that this in fact happening. I knew I had an invalid endpoint specified in the configuration file and it transpires that Apache Camel had dynamically created this endpoint. That’s not what I intended at all.
 
To fix this and stop the endpoint from being created dynamically the code to look up the endpoint was  changed to this:
 
    Map<String, Endpoint> endpointMap = camelContext.getEndpointMap();
    Endpoint endpoint = endpointMap.get(endpointName);
    if (endpoint instanceof SedaEndpoint) {
        SedaEndpoint sedaEndpoint = (SedaEndpoint) endpoint;
        LOGGER.info("queue size: " + sedaEndpoint.queueSize());
    }
 
Key takeaway here is to always read the API docs of the methods you use just in case they have strange side-effects.