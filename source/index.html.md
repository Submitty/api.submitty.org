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
  http://192.168.56.111/api/token \
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
