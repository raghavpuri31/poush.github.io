---
title: Connecting Social Apps with Orga API Server
layout: post
categories:
- fossasia
- oauth
tag:
- fossasia
- api-server
- oauth
- social-apps
author: poush
star: 'true'
date: '2017-05-23 04:11:24'
---

## Addition of a feature to Orga API Server
### *Ability to connect your social apps with orga server*

Options to connect your social 

__*What's going to add?*__

A feature which will allow us to provide Organizer options to connect with their social media audience directly with API server and Users to share their experience for different events on their social media platforms.

__*Platforms added*__
* **F**aceBook
* **T**witter
* **I**nstagram 
* **G**oogle+

### The Auth Tool - Oauth 2.0
Without going on introductory introduction to Oauth2.0 Let's focus on it's implementation in Orga API Server

__OAuth Roles__

*OAuth defines four roles:*

* Resource Owner
* Client
* Resource Server
* Authorization Server

Let's look on the responsibility of these roles when you conenct your social apps with API server

__Resource Owner: User/Organizer ( You )__

You as User or Organizer connection your accounts with API server are the resource owners who authorizes API server to access your account. During authorization you provide us access to read your account details like Name, Email, Profile photo, etc.

__Resource / Authorization Server: Social Apps__

The resource server here are your social platforms/apps where you have your accont registered. These Apps provide us limited access to fetch details of your account once you authorize our applciation to do so.
They make sure the token we provides match with the authoriztion provided before through your account.

__Client: Orga API Server__

Orga API Server act as client to access your account details. Before it may do so, it must be authorized by the user, and the authorization must be validated by the API.

### The process to add
A simple workplan to follow:

1. Understanding how Oauth is implemented.
2. Test Oauth implementation on all 4 social medias.
3. After Neccessary correction. Make sure we have all views(routes) to connect these 4 social medias.
4. Implementing the same feature on template file.
5. Make sure these connect buttons are shown only when Admin has registered it's client credentials in Settings.
6. Creating a view to unlink your social media account.

__Understanding how Oauth is implemented.__

Current Implementation of Oauth is very simple and interesting on API server. We have Oauth helper classes which provides all necessary end points and differnet methods to get job done.
![Oauth](/Screen%20Shot%202017-05-23%20at%205.17.52%20PM.png)

__Test Oauth implementation on all 4 social medias.__

Now we can work on testing on the callbacks of all 4 social apps. We have have callback defined in ```views/util_routes.py```
For this I picked up the auth Oauth Urls and called them directly on my browsers and testing their callback. Now on callback those methods required some change to save user data on database thus connecting their accounts with API server. This lead to changes in ```update_user_details``` and on callback methods.
```
def update_user_details(first_name=None,
                        last_name=None,
                        facebook_link=None,
                        twitter_link=None,
                        file_url=None,
                        instagram=None,
                        google=None):
```

__Make sure we have all views(routes) to connect these 4 social medias__

This has to be done on ```views/users/profile.py```
Addition of one method 
```
@profile.route('/google_connect/', methods=('GET', 'POST'))
def google_connect():
		....
		....
    return redirect(gp_auth_url)
```
and testing, correction on other 3 methods

__Implementing the same feature on template file.__

Updating ```gentelella/users/settings/pages/applications.html```  to add changes required to add this feature. This included ability to show urls of connected accounts and functioning connect and disconnect button

__Make sure these connect buttons are shown only when Admin has registered it's client credentials in Settings.__

```
	 fb = get_settings()['fb_client_id'] != None and get_settings()['fb_client_secret'] != None
		....
		....
		...
```

Addition of such snipper provide data to template to decided whether to show those fields or not. It will not make any sense if there is no applcation created to connect those accounts by Admin.

__Creating a view to unlink your social media account.__

```
utils_routes.route('/unlink-social/<social>')
def unlink_social(social):
    if login.current_user is not None and login.current_user.is_authenticated:
		...
		...
```
A method is created to unlink the connected accounts so that users can anytime disconnect their accounts from API server.

**Where to connect?**

```
Settings > Applications
```

*How it Works*
