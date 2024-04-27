---
title: Submitty API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - shell

toc_footers:
  - <a href='https://github.com/lord/slate'>Documentation Powered by Slate</a>

includes:

search: true
---

# Introduction

*Note: API is still a work in progress.*

Most of the API is restricted to faculty, however the [Gradeables](#gradeables) section can be used by students or faculty.
API provides an alternative way of interacting with Submitty. It facilitates testing, helps system administrators to modify resources and enables users to create customized frontends.

Note that as we rely on the Authorization header information to authenticate users, please make sure that you have a correct Apache configuration file as specified in [Installation Version Notes: v19.06.02](https://submitty.org/sysadmin/version_notes/v19.06.02).

<aside class="warning">
The API is not formally versioned at this time, and its endpoints and their responses may change
without warning. Use at your own risk!
</aside>

# Requests

Submitty supports requests using the following two `content-types`:

* application/json
* application/x-www-form-urlencoded

while anything involving files should use:

* multipart/form-data

All endpoints use the `/api` path off the base Submitty URL, followed by the specific endpoint
for the API in question.

# Responses

> Example of a `success` JSON response:

```json
{
  "status": "success",
  "data": null
}
```

> Example of a `fail` JSON response:

```json
{
  "status": "fail",
  "message": "Did not specify gradeable"
}
```

All API responses will be returned as `application/json` and conform to a modified
version of the [JSend specification](https://labs.omniti.com/labs/jsend). A response
returned from the server is expected to be one of the following three response types.

| Type    | Description                                                                                         | Required Keys   | Optional Keys |
|---------|-----------------------------------------------------------------------------------------------------|----------------|---------------|
| success | All went well, and (usually) some data was returned                                                 | status, data    |               |
| fail    | There was a problem with the data submitted, or some pre-condition of the API call wasn't satisfied | status, message | data          |
| error   | An error occurred in processing the request, i.e. an exception was thrown                           | status, message | data, code    |

# Authentication

```shell
# With shell, you can just pass the correct header with each request
curl "api_endpoint_here"
  -H "Authorization: my_token"
```

> Make sure to replace `my_token` with your API token, or API will return JSON like this:

```json
{
    "status": "fail",
    "message": "Unauthenticated access. Please log in."
}
```

Submitty uses [JSON Web Tokens](https://jwt.io/) to allow access to the API. You can obtain your API token through the following endpoint.

Kittn expects for the API token to be included in all API requests to the server in a header that looks like the following:

`Authorization: my_token`

<aside class="notice">
You must replace <code>my_token</code> with your personal API token.
</aside>

## Get Token

```shell
curl -X POST \
  <base_url>/api/token \
  -F user_id=instructor \
  -F password=instructor
```

> The above command returns JSON structured like this:

```json
{
    "status": "success",
    "data": {
        "token": "a long string"
    }
}
```

Note that every time you request a new token, the previous token will be invalidated.

### HTTP Request

`POST /api/token`

### Parameters

Parameter | Description
--------- | -----------
user_id | User's unique ID
password | User's password

## Invalidate Token

```shell
curl -X POST \
  <base_url>/api/token/invalidate \
  -F user_id=instructor \
  -F password=instructor
```

The endpoint invalidates the token you requested before.

### HTTP Request

`POST /api/token/invalidate`

### Parameters

Parameter | Description
--------- | -----------
user_id | User's unique ID
password | User's password

# Courses

## Get Courses

```shell
curl --request GET \
  --url <base_url>/api/courses \
  --header 'Authorization: my_token'
```

> The above command returns JSON structured like this:

```json
{
    "status": "success",
    "data": {
        "unarchived_courses": [
            {
                "term": "f19",
                "title": "blank",
                "display_name": "",
                "display_term": "Fall 2019"
            }
        ],
        "archived_courses": [
            {
                "term": "f19",
                "title": "sample",
                "display_name": "",
                "display_term": "Fall 2019"
            }
        ]
    }
}
```

Get all the courses the user is taking or have taken.

### HTTP Request

`GET /api/courses`

## Create Courses

```shell
curl --request POST \
  --url <base_url>/api/courses \
  --form course_term=f19 \
  --form course_title=api \
  --form head_instructor=instructor \
  --form base_course_term=f19 \
  --form base_course_title=sample \
  --header 'Authorization: my_token'
```

This endpoint creates a course based on an existing course.

Note that the endpoint builds a course based on a prior course offering (called base course in API). Instructors and graders with access to the prior course files on GNU/Linux will be able to see the content of the new course as well. Contact the system administrator if this course should not share a GNU/Linux group with any prior course.

### HTTP Request

`POST /api/courses`

### Parameters

Parameter | Description
--------- | -----------
course_term | term of the new course
course_title | Title (or code) of the new course
head_instructor | Head instructor of the new course
base_course_term | term of the base course
base_course_title | Title (or code) of the base course

# Courses.Users

## Get Users

```shell
curl --request GET \
  --url <base_url>/api/<term>/<course>/users \
  --header 'Authorization: my_token'
```

> The above command returns JSON structured like this:

```json
{
    "status": "success",
    "data": [
        {
            "first_name": "Alyssa P",
            "last_name": "Hacker",
            "user_id": "aphacker",
            "email": "alyssaphacker@example.com",
            "reg_section": "1",
            "rot_section": "NULL",
            "group": "Student"
        },
        {
            "first_name": "Delphia",
            "last_name": "Aufderhar",
            "user_id": "aufded",
            "email": "aufded@example.com",
            "reg_section": "1",
            "rot_section": "1",
            "group": "Student"
        },
    ]
}
```

This end point gets all users in a course.

### HTTP Request

`GET /api/<term>/<course>/users`

## Get Graders

```shell
curl --request GET \
  --url <base_url>/api/<term>/<course>/graders \
  --header 'Authorization: my_token'
```

> The above command returns JSON structured like this:

```json
{
    "status": "success",
    "data": [
        {
            "first_name": "Quinn",
            "last_name": "Instructor",
            "user_id": "instructor",
            "email": "instructor@example.com",
            "reg_section": "All",
            "rot_section": "NULL",
            "group": "Instructor"
        },
        {
            "first_name": "Jill",
            "last_name": "TA",
            "user_id": "ta",
            "email": "ta@example.com",
            "reg_section": "",
            "rot_section": "NULL",
            "group": "Full Access Grader (Grad TA)"
        },
    ]
}
```

### HTTP Request

`GET /api/<term>/<course>/graders`

# Courses.Reports

## Generate Grade Summaries

```shell
curl --request POST \
  --url <base_url>/api/<term>/<course>/reports/summaries \
  --header 'Authorization: my_token'
```

This endpoint helps system administrators set up cron jobs for automatic grade summary generation.

### HTTP Request

`POST /api/<term>/<course>/reports/summaries`


# Gradeables
## Get gradeable values

```shell
curl -X GET \
  <base_url>/api/<term>/<course>/gradeable/<gradeable_id>/values?user_id=<user_id>

```
> Possible response examples:

```json
{
    "status": "success",
    "data": {
        "is_queued": false,
        "queue_position": 3,
        "is_grading": false,
        "has_submission": true,
        "autograding_complete": true,
        "has_active_version": true,
        "highest_version": 1,
        "total_points": 0,
        "total_percent": 0
    }
}
```
```json
{
    "status": "fail",
    "message": "Gradeable does not exist"
}
```

The endpoint returns values associated with an autograded gradeable with the given gradeable_id, which allows for determining a score on an assignment, if it has been graded, etc. 

### HTTP Request

`GET /api/<term>/<course>/gradeable/<gradeable_id>/values?user_id=<user_id>`

### Parameters

Parameter | Description
--------- | -----------
user_id | User's unique ID

## Submit VCS Gradeable

```shell
curl -X POST \
  <base_url>/api/<term>/<course>/gradeable/<gradeable_id>/upload\
  -F user_id=student \
  -F vcs_checkout=true \
  -F git_repo_id=true
```
> Possible responses:

```json
{
    "status": "success",
    "data": "Successfully uploaded version {#} for {Gradeable Title}"
}
```
```json
{
    "status": "fail",
    "message": "Invalid gradeable id '{Gradeable ID}'"
}
```
```json
{
    "status": "fail",
    "message": "Student API for upload only supports VCS gradeables"
}
```


The endpoint requests for a VCS gradeable with the given gradeable_id to be submitted.

### HTTP Request

`POST /api/<term>/<course>/gradeable/<gradeable_id>/upload`

### Parameters

Parameter | Description
--------- | -----------
user_id | User's unique ID
vcs_checkout | Required to be `true`
git_repo_id | Required value, however no specific value is checked.
