# Resumable (chunked) uploads in Rails 4.2 using Paperclip and jQuery File Upload

## Description
This is an example implementation of [jQuery File Upload](https://github.com/blueimp/jQuery-File-Upload) in Rails 4.2 using [Paperclip](https://github.com/thoughtbot/paperclip). The app uses the gem [jquery-fileupload-rails](https://github.com/tors/jquery-fileupload-rails) to integrate the relevant libraries into the Rails Asset Pipeline.

## Installation
```
bundle install
rake db:create && rake db:migrate
```

## Features
The app has one model `Course`, which `has_attached_file :upload`. Once a `Course` has been created, the user is redirected to the `upload` action of `CoursesController`. The user can choose a local file to upload. 

When the user initiates the upload, jQuery File Upload does an AJAX request to the `resume_upload` action in order to establish whether there is an unfinished/interrupted upload. A JSON contianing information about the already uploaded bytes is returned. jQuery File Upload will then continue the upload from the last uploaded chunk (or from the beginning).

The PATCH request containing the first or following chunk of the uploaded file is processed by the `do_upload` action. This action checks again whether the file exists in the system.
* If a new file: `Course.status` is updated from 'new' to 'uploading' and the first chunk is saved.
* If an unfinished upload: by taking the current file size as a point of reference, the controller action makes sure that the `Content-Range` from the request headers corresponds to the following chunk that needs to be uploaded. If true, the chunk is written to the existing file.

Once the file upload has been completed, the callback of jQuery File Upload triggers an AJAX request to the `update_status` action, which make sure that `Course.status` is set from 'uploading' to 'uploaded'.

## Some remarks
* The app accepts only pdf files. Validations and checks have been added on the frontend as well as on the backend
* Currently, the app supports only single uploads (one file per `Course`). The backend can be easily modified to work with multiple uploads
* The `GET upload` action contains a link to remove any unfinished uploads, just in case the user expieriences unexpected errors when trying to resume
* Sharing is caring, so feel free to contribute!
