---
title: "Authentication vs. Authorisation"
date: 2019-04-09T12:26:24+01:00
description: ""
categories:
  - development
tags:
  - meta
---

Today I learned more about the distinction between *authentication* and *authorisation*. I have probably thought of those terms as being synonymous with eachother and fulfilling the same needs. But I was wrong.
<!--more-->

I have been writing a personal project that uses an API, accessing methods such as `authenticateUser`, `authenticateToken`, `authoriseRequest` etc. I took a step back and looked at my code. It clicked that when I wanted to *authenticate* something, I needed to prove the information I had been provided was verifiable. Here is some example pseudocode:

### Client Request
```
username = 'bob';
timestamp = generateMicrotime();
sharedAppSecret = 'donttellanyone';
digest = username + timestamp + oneWayHash(sharedAppSecret);

# Send data to API endpoint
send(apiurl, headers[
  authentication: base64Encode(digest),
  username,
  timestamp,
]);
```

### API Endpoint
```
sharedAppSecret = 'donttellanyone';
parseRequest(request);

recreatedDigest(parseRequest.username + parseRequest.timestamp + oneWayHash(sharedAppSecret));

if (parseRequest.authentication === recreatedDigest)
{
  # Request is authenticated
}
```

The general idea is the API Endpoint can *authenticate* the provided information from the client because the information is verifiable programmitically. So that code could mean a client can be *authenticated*. But it does not mean the user is *authorised*.

*Authorisation* would involve the client submitting the users details, which would be looked up in a user table in a database. That users permissions can then be verified and their level of *authorisation* can be determined.

As a front-end developer this can sometimes be taken for granted, as someone else has often developed the API you interact with or you do not control the authentication flow.