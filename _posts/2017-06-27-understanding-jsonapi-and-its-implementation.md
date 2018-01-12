---
title: Understanding {json:api} and it's usage in Orga Server
layout: post
categories:
- fossasia
- OAuth
author: poush
date: '2017-06-27 01:31:11'
---

## *What is an API?*
API stands for “application programming interface”. Put briefly, an API consists of a set of rules describing how one application can interact with another and the mechanisms that allow such interaction to happen.

## *and JSON API?*
JSON API is a specification for writing RESTFul APIs ( CRUD interfaces ). This specification basically sets the standard for a client to request the resources and how a server is supposed to response minimizing the redundancy and number of requests.

If we look at the general implementation of RESTful APIs, we see that we are working on creating every endpoint manually, there are no relations. Sometimes different endpoints are being created for some slightly different business logic than other. 

## *Features*

Apart from CRUD interface, JSON-API-Spec provides

* Fetching Resources
* Fetching Relationships
* Inclusion of Related Resources
* Sparse Fieldsets
* Sorting
* Pagination
* Filtering

And what we need at Open Event Orga Server?

* Proper relationship definitions
* Sorting
* Filtering
* Pagination

So JSON-API spec is a good choice for us at Orga Server since it solves our every basic need.

## Overview of Changes

Firstly the main task was shifting to the library [flask-rest-jsonapi](https://github.com/miLibris/flask-rest-jsonapi) because this library stands to our four needs in API. 
The changes included:
* ensuring JSON-API spec in our requests and responses (although the most of the work is done by the library)
* Reusing the current implementation of JWT authorization.
* To locate the new API to `/v1`. Since Orga server is going to be API server with Open Event system following the API-centric approach, therefore, there is no need to have `/api/v1`
* Now out timestamps in response and request will be timezone aware thus following ISO 8601 with timezone information `(Eg. 2017-05-22T09:12:44+00:00)`


*Following some basic rules like
A JSON object MUST be at the root of every JSON API request and response containing data. This object defines a document’s “top level”.
A document MUST contain at least one of the following top-level members:*
```
data: the document’s “primary data”
errors: an array of error objects
meta: a meta object that contains non-standard meta-information. 
```

#### Media type to use: ``` application/vnd.api+json``` 

## An example of new API Server 

#### Resuest

``` JSON
POST /v1/users HTTP/1.1
Content-Type: application/json

POST /v1/users HTTP/1.1
Content-Type: application/vnd.api+json
Host: localhost:5000
Connection: close
User-Agent: Paw/3.1.1 (Macintosh; OS X/10.12.3) GCDHTTPRequest
Content-Length: 165

{
  "data": {
    "attributes": {
      "name": "Open Event User",
      "email": "example@example.com",
      "password": "12345678"
    },
    "type": "user"
  }
}
```

#### Response

```JSON
HTTP/1.1 200 OK
Content-Type: application/vnd.api+json

{
  "data": {
    "attributes": {
      "is-admin": false,
      "last-name": null,
      "instagram-url": null,
      "is-super-admin": false,
      "thumbnail-image-url": null,
      "created-at": "2017-06-27T09:08:59.369531+00:00",
      "last-accessed-at": null,
      "email": "example@example.com",
      "icon-image-url": null,
      "contact": null,
      "deleted-at": null,
      "small-image-url": null,
      "facebook-url": null,
      "details": null,
      "is-verified": false,
      "first-name": null,
      "avatar-url": null,
      "twitter-url": null,
      "google-plus-url": null
    },
    "type": "user",
    "id": "2",
    "links": {
      "self": "/v1/users/2"
    }
  },
  "jsonapi": {
    "version": "1.0"
  },
  "links": {
    "self": "/v1/users/2"
  }
}
```

Looking at the example we can directly see how easy it is for us to manage different needs like pagination, relationships using JSON API. 
Next steps in the implementation is Docs for APIs, permissions implementations to secure the endpoints and setting up unit testing of the endpoints which will be discussed in next posts.

Further read on JSON API Spec -> [http://jsonapi.org/format/](http://jsonapi.org/format/)