---
layout: post
title:  "Using Cloud storage for event exports"
date:   2017-05-09 13:26:44 +0530
categories: fossasia
layout: post
tag:
- fossasia
- tech
- open-event
star: true
author: poush
---
Open-event orga server provides ability to organizer to create complete export of the event they created. Currently when an organizer triggers the export in orga server, A celery job is set to complete the export task resulting asynchronous completion of the job. Organizer gets the download button enabled once export is ready.

Till now the main issue was related with storage of those export zip files. All exported zip files were stored directly in local storage and that even not by using storage module created under orga server.

![local storage path](/Screen%20Shot%202017-05-10%20at%207.11.36%20PM.png)

On a mission to solve this, I made three simple steps that I followed to solve this issue.

These three steps were: 
> 1. Wait for **shutil.make_archive** to complete archive and store it in local storage.
> 2. Copy the created archive to storage ( specified by user )
> 3. Delete local archive created.

The easiest part here was to make these files upload to different storage ( s3, gs, local) as we already have ```storage``` helper

```
def upload(uploaded_file, key, **kwargs):
    """
    Upload handler
    """
```

The most important logic of this issue resides to this code snippet.
```
    dir_path = dir_path + ".zip"
 
     storage_path = UPLOAD_PATHS['exports']['zip'].format(
         event_id = event_id
     )
     uploaded_file = UploadedFile(dir_path, dir_path.rsplit('/', 1)[1])
     storage_url = upload(uploaded_file, storage_path)
 
    if get_settings()['storage_place'] != "s3" or get_settings()['storage_place'] != 'gs':
        storage_url = app.config['BASE_DIR'] + storage_url.replace("/serve_","/")
    return storage_url
```

From above snippet it is clear that we are extending the process of creating zip. Once the zip is created we will make storage path for cloud storage and upload it. Only one thing will take time to understand here is the last second and third line of above snippet.

```
if get_settings()['storage_place'] != "s3" or get_settings()['storage_place'] != 'gs':
        storage_url = app.config['BASE_DIR'] + storage_url.replace("/serve_","/")
```

Initial the plan was simple to serve the files through **```serve_static ```** but then the test cases were expecting a file at this location thus I had to remove "serve_" part for local storage and then it works fine on those three steps.

Next thing on this storage process need to be discussed is feature to delete old exports. I believe one reason to keep them would be a old backup of your event will be always there with us at our cloud storage.