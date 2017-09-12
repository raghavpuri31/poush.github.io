---
title: A guide to use Permission Manager in Open Event API Server
layout: post
author: poush
permalink: /a-guide-to-use-permission-manager-in-open-event-api-server/
tags:
- gsoc, python, open event, api server, permissions, usage guide, protecting endpoints, python-flask
source-id: 1L-EsfeOWFNJQoGg_xdoiY-DYoKb-aD5wPCM0LBFfT8E
published: true
---
![image alt text]({{ site.url }}/public/ZnPzuLKvhpUpxOTCk05cg_img_0.png)A guide to use Permission Manager in Open Event API Server

This article provides a simple guide to use permission manager in [Open Event API Server](http://github.com/fossasia/open-event-orga-server). Permission manager is constantly being improved and new features are being added into it. To ensure that all co-developers get to know about it and make use of them, this blog posts describes every part of permission manager.

### Bootstrapping

Permission manager as a part of flask-rest-jsonapi works as a decorator for different resources of the API. There are two ways to provide the permission decorator to any view

* First one is to provide it in the list of decorators

    * decorators = (api.has_permission('is_coorganizer', fetch="event_id",

    *                                 fetch_as="event_id", model=StripeAuthorization),)

* Second way is to explicitly provide it as a decorator to any view 

    * **@api.has_permission**('custom_arg', custom_kwargs='custom_kwargs')    **def** get(*args, **kwargs):        **return** 'Hello world !'

In the process of booting up, we first need to understand the flow of Resources in API. All resources even before doing any schema check, call the decorators. So this way you will not get any request data in the permission methods. All you will receive is a **dict** of the URL parameters but again it will not include the filter parameters.

Permission Manager receives five parameters as:

**def ****permission_manager**(view, view_args, view_kwargs, *args, **kwargs):

First three are provided into it implicitly by [flask-rest-jsonapi](https://flask-rest-jsonapi.readthedocs.io/) module

* **v****iew: **This is the resource's view method which is called through the API. For example if I go to */events *then the **get** method of **ResourceList** will be called.

* **v****iew_args: **These are args associated with that view

* **v****iew_kwargs: **These are kwargs associated with that resource view. It includes all your URL parameters as well

* **a****rgs: **These are the custom args which are provided when calling the permission manager. Here at permission manager is it expected that the first index of **args** will be the name of permission to check for.

* **k****wargs: **This is the custom dict which is provided on calling the permission manager. The main pillar of the permission manager. Described below in usage.

### Using Permission Manager

Using permission manager is basically understanding the different options you can send through the **kwargs** so here is the list of the things you can send to permission manager

These are all described in the order of priority in permission manager

1. **m****ethod (string)**: You can provide a string containing the methods where permission needs to be checked as comma separated values of different methods in a string.For example: **_method=_***"GET,POST”*

2. **l****eave_if (lambda): **This receives a lambda function which should return boolean values. Based on returned value if is true then **it will skip the permission check. **The provided lambda function receives only parameter, *"view_kwargs" *Example use case can be the situation where you can leave the permission for any specific related endpoint to some resource and would like to do a manual check in the method itself.

3. **c****heck (lambda): **Opposite to leave_if. It receives a lambda function that will return boolean values. Based on returned value, If it is true then only it will go further and check the request for permissions else **will throw forbidden error.**

4. **f****etch (string): **This is the string containing the name of the key which has to be fetched for **fetch_as **key (described below). Permission manager will first look for this value in **view_kwargs** dict object. If it is not there then it will make the query to get one(described below at **model )**

5. **f****etch_as (string): **This is the string containing the name of a key. The value of **fetch** key will be sent to the permission functions by this name.

6. **m****odel (string): **This is one most interesting concept here. To get the value of **fetch **key. Permission manager first looks into **view_kwargs **and if there no such value then you can still get one through the model. The model attribute here receives the class of the database model which will be used to get the value of **fetch** key.It makes the query to get the single resource from this model and look for the value of **fetch** key and then pass it to the permission functions/methods.The interesting part is that by default it uses <*id> *from view_kwargs to get the resource from the model but in any case if there is no specific ID with name <id> on the view_kwargs. You can use these two options as:

    1. **f****etch_key_url (string): **This is the name of the key whose value will be fetched from view_kwargs and will be used to match through the records in database model to get the resource.

    2. **f****etch_key_model (string):** This is the name of the match column in the database model for the **fetch_key_url**, The value of it will be matched with column named as the value of **fetch_key_model.**

**In case there is no record found in the model then permission manager will throw NotFound 404 Error.**

### A helper for permissions

The next big thing in permission manager is the addition of new helper function "has_access"

**def ****has_access**(access_level, **kwargs):

   **if **access_level **in **permissions:

       auth = permissions[access_level](**lambda ***a, **b: True, (), {}, (), **kwargs)

       **if **type(auth) **is **bool **and **auth **is **True:

           **return **True

   **return **False

This method allows you to check the permission at the mid of any method of any view and of any resource. Just provide the name of permission in the first parameter and then the additional options needed by the permission function as the **kwargs **values.

**This does not throw any exception**. Just returns the boolean value so take care of throwing any exception by yourselves.

### Anything to improve on?

I will not say this exactly as improvement but I would really like to make it more meaningful and interesting to add permission. May be something like this below:

permission = "Must be co_organizer OR track_organizer, fetch event_id as event_id, use model Event"

This clearly needs time to make it. But I see this as an interesting way to add permission. Just provide meaningful text and rest leave it to the permission manager.

### Resources:

* Permission manager - Flask-rest-jsonapi module[http://flask-rest-jsonapi.readthedocs.io/en/latest/permission.html](http://flask-rest-jsonapi.readthedocs.io/en/latest/permission.html)

