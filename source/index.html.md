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

API provides an alternative way of interacting with Submitty. It facilitates testing, helps system administrators to modify resources and enables users to create customized frontends.

Note that as we rely on the Authorization header information to authenticate users, please make sure that you have a correct Apache configuration file as specified in [Installation Version Notes: v19.06.02](https://submitty.org/sysadmin/version_notes/v19.06.02).

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

All API responses conform to a modified version of [JSend specification](https://labs.omniti.com/labs/jsend). A response returned from the server is expected to be one of the following three response types.

| Type    | Description                                                                                         | Required Keys   | Optional Keys |
|---------|-----------------------------------------------------------------------------------------------------|-----------------|---------------|
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

Submitty uses [JSON Web Tokens](https://jwt.io/) to allow access to the API. You can obtain your API key through the following endpoint.

Kittn expects for the API key to be included in all API requests to the server in a header that looks like the following:

`Authorization: my_token`

<aside class="notice">
You must replace <code>my_token</code> with your personal API key.
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

### HTTP Request

`POST /api/token`

### Parameters

Parameter | Description
--------- | -----------
user_id | User's unique ID
password | User's password

# Courses.Users

## Get Users

```shell
curl --request GET \
  --url <base_url>/api/<semester>/<course>/users \
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

### HTTP Request

`GET /api/<semester>/<course>/users`

## Get Graders

```shell
curl --request GET \
  --url <base_url>/api/<semester>/<course>/graders \
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

`GET /api/<semester>/<course>/graders`

# Courses.Reports

## Generate Grade Summaries

```shell
curl --request POST \
  --url <base_url>/api/<semester>/<course>/reports/summaries \
  --header 'Authorization: my_token'
```

This endpoint helps system administrators set up cron jobs for automatic grade summary generation.

### HTTP Request

`POST /api/<semester>/<course>/reports/summaries`
