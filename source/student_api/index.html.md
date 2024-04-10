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

*Note: Student API is still a work in progress.*

API provides an alternative way of interacting with Submitty. This is intended for students to be able to interact with Submitty without using the web UI.

__*Note: All student requests require a parameter of 'user_id' for verification.*__

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

All endpoints use the `/student_api` path off the base Submitty URL, followed by the specific endpoint
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
  "message": "Gradeable does not exist."
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
curl "student_api_endpoint_here"
  -H "Authorization: my_token"
```

> Make sure to replace `my_token` with your API token, or the API will return JSON like this:

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
  <base_url>/student_api/token \
  -F user_id=student \
  -F password=student
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

`POST /student_api/token`

### Parameters

Parameter | Description
--------- | -----------
user_id | User's unique ID
password | User's password

## Invalidate Token

```shell
curl -X POST \
  <base_url>/student_api/token/invalidate \
  -F user_id=student \
  -F password=student
```

The endpoint invalidates the token you requested before.

### HTTP Request

`POST /student_api/token/invalidate`

### Parameters

Parameter | Description
--------- | -----------
user_id | User's unique ID
password | User's password

# Gradeables

## Get score

```shell
curl -X GET \
  <base_url>/student_api/<term>/<course>/gradeable/<gradeable_id>/score?user_id=<user_id>

```
> Possible responses:

```json
{
    "status": "success",
    "data": {
      "score": 0
    }
}
```
```json
{
    "status": "fail",
    "message": "Gradeable does not exist"
}
```

The endpoint returns the score of an autograded gradeable.

### HTTP Request

`GET /student_api/<term>/<course>/gradeable/<gradeable_id>/score?user_id=<user_id>`

### Parameters

Parameter | Description
--------- | -----------
user_id | User's unique ID

## Submit VCS Gradeable

```shell
curl -X POST \
  <base_url>/student_api/<term>/<course>/gradeable/<gradeable_id>/upload\
  -F user_id=student \
  -F vcs_checkout=true
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


The endpoint requests for a VCS gradeable to be submitted.

### HTTP Request

`GET /student_api/<term>/<course>/gradeable/<gradeable_id>/upload`

### Parameters

Parameter | Description
--------- | -----------
user_id | User's unique ID

