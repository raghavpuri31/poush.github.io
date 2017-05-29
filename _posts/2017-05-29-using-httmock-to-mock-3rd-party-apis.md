---
title: Using HTTMock to mock 3rd Party APIs
layout: post
author: poush
tag:
- fossasia
- HTTMock
- tech
- API-server
- open-event
---

In the process of implementing the connected social media in API server. There was a situation where we need to mock the 3rd party API services like Google OAuth, Facebook Graph API.

The idea was simple, as suggested by  [@hongquan](https://github.com/hongquan),  when ```requests``` make a __HTTP__ request to _https://api.google.com/profile_, for example, [httmock](https://github.com/patrys/httmock) will 

- stand in the middle, 
- stop request from going to the Internet, 
- and returns a JSON response as if the response is from Google. 


The content of this response is written by us in the test case. We have to read Google documentation to write a correct fake response.

__*Library used*__

For this purpose, I used the [httmock](https://github.com/patrys/httmock) library for Python.

### Steps to follow

1. Look for response object on two requests (OAuth and profile details).
2. Create the dummy response using the sample response object.
3. Creating endpoints using the httpmock library.
4. During test run, calling the specific method ```with HTTMock```

Sample object of OAuth Response from Google is:
```
    {
        "access_token":"2YotnFZFEjr1zCsicMWpAA",
        "token_type":"Bearer",
        "expires_in":3600,
        "refresh_token":"tGzv3JOkF0XG5Qx2TlKWIA",
        "example_parameter":"example_value"
    }
```

and from the sample object of Google Profile API we needed the link of profile for our API-server:
```JSON
{'link':'http://google.com/some_id'}
```

__Creating the dummy response__

Creating dummy response was easy. All I had to do is provide proper header and content in response and use ```urlmatch``` decorator

```python
# response for getting userinfo from google
@urlmatch(netloc='https://www.googleapis.com/userinfo/v2/me')
def google_profile_mock(url, request):
    headers = {'content-type': 'application/json'}
    content = {'link':'http://google.com/some_id'}
    return response(200, content, headers, None, 5, request)

@urlmatch(netloc=r'(.*\.)?google\.com$')
def google_auth_mock(url, request):
    headers = {'content-type': 'application/json'}
    content = {
        "access_token":"2YotnFZFEjr1zCsicMWpAA",
        "token_type":"Bearer",
        "expires_in":3600,
        "refresh_token":"tGzv3JOkF0XG5Qx2TlKWIA",
        "example_parameter":"example_value"
    }
    return response(200, content, headers, None, 5, request)
```

So now we have the end points to mock the response. All we need to do is to use HTTMock inside the test case.

To use this setup all we need to do is:
```python
 with HTTMock(google_auth_mock, google_profile_mock):
                 self.assertTrue('Open Event' in self.app.get('/gCallback/?state=dummy_state&code=dummy_code',
                                                           follow_redirects=True).data) 
             self.assertEqual(self.app.get('/gCallback/?state=dummy_state&code=dummy_code').status_code, 302)
             self.assertEqual(self.app.get('/gCallback/?state=dummy_state&code=dummy_code').status_code, 302)
```

And we were able to mock the Google APIs in our test case. Complete implementation in Fossasia API-Server can be seen [Here](https://github.com/fossasia/open-event-orga-server/pull/3629/files)