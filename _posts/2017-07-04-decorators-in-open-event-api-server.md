---
title: Decorators in Open Event API Server
layout: post
---

One of the interesting features of Python is the decorator. Decorators dynamically alter the functionality of a function, method, or class without having to directly use subclasses or change the source code of the function being decorated.
[Open Event API Server](http://github.com/fossasia/open-event-orga-server) makes use of decorator in various ways. The ability to wrap a function and run the decorator(s) before executing that function solves various purpose in Python. Earlier before decoupling of Orga Server into API Server and Frontend, decorators were being used for routes, permissions, validations and more. 

Now, The API Server mainly uses decorators for:
* Permissions
* Filtering on the basis of view_kwargs or injecting something into view_kwargs
* Validations

We will take here about first two because validations are simple and we are using them out of the box from [marshmall-api](http://marshmallow-jsonapi.readthedocs.io/en/latest/)
The second one is custom implementation made to ensure no separate generic helpers are called which can add additional database queries and call overheads in some scenarios.

**Permissions Using Decorators**

Flask-rest-jsonapi provides an easy way to add decorators to `Resources`. This is as easy as defining this into Resource class
```python

decorators = (some_decorator, )
    
```
On working to event role decorators to use here, I need to follow only these 3 rules

* If the user is admin or super admin, he/she has full access to all event roles
* Then check the user's role for the  given event 
* Returns the requested resource's view if authorized unless returns Forbidden Error response.

One of the example is:

```python
def is_organizer(view, view_args, view_kwargs, *args, **kwargs):
    user = current_identity

    if user.is_staff:
        return view(*view_args, **view_kwargs)

    if not user.is_organizer(kwargs['event_id']):
        return ForbiddenError({'source': ''}, 'Organizer access is required').respond()

    return view(*view_args, **view_kwargs)
```

From above example, it is clear that it is following those three guidelines 
 

**Filtering on the basis of view_kwargs or injecting something into view_kwargs**

This is the main point to discuss, starting from a simple scenario where we have to show different events list for different users. Before decoupling API server, we had two different routes, one served the public events listing on the basis of event identifier and other to show events to the event admins and managers, listing only their own events to their panel.

In API server there are no two different routes for this. We manage this with single route and served both cases using decorator. This below is the magic decorator function for this purpose
```python
def accessible_role_based_events(view, view_args, view_kwargs, *args, **kwargs):
    if 'POST' in request.method or 'withRole' in request.args:
        _jwt_required(app.config['JWT_DEFAULT_REALM'])
        user = current_identity

        if 'GET' in request.method and user.is_staff:
            return view(*view_args, **view_kwargs)
        view_kwargs['user_id'] = user.id

    return view(*view_args, **view_kwargs)
```

It works simply by looking for 'withRole' in requests and make a decision to include `user_id` into kwargs as per these rules

1. If the request is POST then it has to be associated with some user so add the `user_id`
2. If the request is GET and 'withRole' GET parameter is present in URL then yes add the `user_id`. This way user is asking to list the events in which I have some admin or manager role
3. If the request is GET and 'withRole' is defined but the logged in user is `admin` or `super_admin` then there is no need add `user_id` since staff can see all events in admin panel
4. The last one is GET and no 'withRole' parameter is defined therefore ignores and continues the same request to list all events.

The next work is of `query` method of `EventList` Resource
```python
if view_kwargs.get('user_id'):
            if 'GET' in request.method:
                query_ = query_.join(Event.roles).filter_by(user_id=view_kwargs['user_id']) \
                    .join(UsersEventsRoles.role).filter(Role.name != ATTENDEE)
```

This query joins the UsersEventsRoles model whenever `user_id` is defined. Thus giving role-based events only.

The next interesting part is the Implementation of permission manager to ensure it doesn't break at any point. We will see it in next post.