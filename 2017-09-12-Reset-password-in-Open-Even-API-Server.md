---
title: Reset password in Open Even API Server
layout: post
author: poush
permalink: /reset-password-in-open-even-api-server/
tags:
- python, flask, gsoc, open-event, api-server, reset-password, forgot password,  new password, reset token
source-id: 1HF0_EbNpL2JL1DwE_CRYF2jI2N6sMChh7l8YCppqWvo
published: true
---
![image alt text]({{ site.url }}/public/AWUmd00BBnleDnbcf1Cfw_img_0.png)

Reset password in Open Even API Server

The addition of reset password API in the [Open Event API Server](https://github.com/fossasia/open-event-orga-server) enables the user to send a forgot password request to the server so that user can reset the password.

Reset Password API is a two step process. The first endpoint allows you to request a token to reset the password and this token is sent to the user via email. The second process is making a PATCH request with the token and new password to set the new password on user's account.

### Creating a Reset token

This endpoint is non JSON spec based API. A reset token is simply a hash of random bits which is stored in a specific column of user's table.

hash_ = random.getrandbits(128)

self.reset_password = str(hash_)

Once the user completed the resetting of the password using the specific token, the old token is flushed and the new token is generated. These tokens are all one time use only.

### Requesting a token

A token can be request on a specific endpoint  **_POST /v1/auth/reset-password_**

The token with the direct link will be sent to registered email.

link = make_frontend_url('/reset-password', {'token': user.reset_password})

send_email_with_action(user, PASSWORD_RESET,     

                                        app_name=get_settings()['app_name'], link=link)

### Flow with frontend

The flow is broken into 2 steps with front end is serving to the backend. The user when click on forget password will be redirected to reset password page in front end which will call the API endpoint in the backend with an email to send the token.

The email received will contain the link for the front end URL which when clicked will redirect the user to the front end page of providing the new password. The new password entered with the token will be sent to API server by front end and reset password will complete.

### Updating Password

Once clicked on the link in the email, the user will be asked to provide the new password. This password will be sent to the endpoint **_PATCH /v1/auth/reset-password_**

The body will receive the token and the new password to update. The user will be identified using the token and password is updated for the respective user.

try:

   user = User.query.filter_by(reset_password=token).one()

except NoResultFound:

   return abort(

       make_response(jsonify(error="User not found"), 404)

   )

else:

   user.password = password

   save_to_db(user)

### References

1. Understand Self-service reset password[https://en.wikipedia.org/wiki/Self-service_password_reset](https://en.wikipedia.org/wiki/Self-service_password_reset)

2. Python - getrandbits()[https://docs.python.org/2/library/random.html](https://docs.python.org/2/library/random.html)

