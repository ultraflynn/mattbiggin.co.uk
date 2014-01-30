---
layout: post
title: Multiplexer EIP
tags: [Apache Camel, Java]
synopsis: How do I describe this?
---
This would be a blog post about how to take a single input and turn it into multiple outputs to an established process. Then take the results from each of those executions and aggregate it into a single response to the client. Making sure that the published result reflects the appropriate status. The focus here will be on receiving a JSON message, marshalling, unmarshalling and then returning the result. This should be configured as a plug-in process on top of an existing route.