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
