---
title: DUPR - API Reference Document

language_tabs: # must be one of https://github.com/rouge-ruby/rouge/wiki/List-of-supported-languages-and-lexers
  - shell

toc_footers:
  - <a href='https://mydupr.com/'>Sign Up for a Developer Key</a>
  - <a href='https://mydupr.com/'>© 2023 DUPR</a>

includes:
  - errors

search: true

code_clipboard: true

meta:
  - name: description
    content: Documentation for the DUPR APIs
  - name: keywords
    content: DUPR,Ratings,API,Documentation

---

# Introduction

Welcome to the DUPR API! You can use our API to upload matches in bulk, search players, get player ratings, fetch DUPR club members ratings, etc. from our database.

You can view code examples in the dark area to the right, and you can switch the programming language of the examples with the tabs in the top right.

# Generate Credentials

To access these APIs you need to request client **secret** and **key** by contacting us via email on [support@mydupr.com](mailto:support@mydupr.com). Please mention your buiness entity name, contact email address and contact person name in request email. Generating client secret and key might take 1 - 3 working days. Once both client key and secret is generated you will receive it on provided offical email address.

Your API keys carry many privileges, so be sure to keep them secure! Do not share your secret API keys in publicly accessible areas such as GitHub, client-side code, and so forth.

### Client Secret

>cs-008a8f63f1fd5433fb2ea1a25fdec9c8

Secret is unique and confidential information for each client. It starts with `cs-` followed by a random hash string. Client secret never changes it remains same for all client keys.

### Client Keys

>ck-6f6e9f3d-ca92-49a5-fced-1bcc36ec127be

Keys are configurable tokens and can be more than one for a client. Authorities could be different for each key, like a key is used to fetch player rating and another key is used to only upload matches. Keys starts with `ck-` followed by 5 hyphen separated hash strings.

### Environments

> test-cs-008a8f63f1fd42e3fb2ea1a25fdec9c8<br>
> test-ck-6f6e9f3d-ca92-49a5-fced-1bcc36ec127be

We have two **Production** and **Test** different environments and we provide two different credentials at the time of registration. You can use test credentials on test environment while you are developing your functionalities. Once you go live you need to replace test credentials with production credentials in your application to access Production data.

Following are base url to access API endpoints:

| Environment | Base URL |
| :-: | :-: |
| Production | - |
| Test | https://uat.mydupr.com/api/ |

# Authentication

> Combine your client key and secret as show below.

```shell
ck-6f6e9f3d-ca92-49a5-fced-1bcc36ec127be:cs-008a8f63f1fd5433fb2ea1a25fdec9c8
```

> When above combination is Base64 encoded it should return following string.

```plaintext
Y2stNmY2ZTlmM2QtY2E5Mi00OWE1LWZjZWQtMWJjYzM2ZWMxMjdiZTpjcy0wMDhhOGY2M2YxZmQ1NDMzZmIyZWExYTI1ZmRlYzljOA==
```

> To authorize, use this code command with your actual credentials:

```shell
# With shell, you can just pass the correct header with each request
curl -X POST "https://<base-url>/auth/v1.0/token" \
   -H "x-authorization: <your-credentials>"
```

> Make sure to replace `<base-url>` with appropriate environment base url and `<your-credentials>` with combination of your API credentials.

> The above command returns response in JSON structured like this:

```json
{
  "status": "SUCCESS",
  "result": {
    "token": "eyJhbGciOiJSUzUxMiJ9.<access-token>",
    "expiry": "2023-04-02T17:21:23Z"
  }
}
```

Authentication API accepts combination of client key and secret colon separated (`:`) encoded in form of Base64 string and returns a short lived temporary access token. 

For example if your client secret is `cs-008a8f63f1fd5433fb2ea1a25fdec9c8` and your key is `ck-6f6e9f3d-ca92-49a5-fced-1bcc36ec127be` then combine it as show below and encode it in Base64 string.

Test credentials have prefix `test-`, for example `test-ck-<hash>` or `test-cs-<hash>`.

Make a request to authenticate using credentials as the special header paramter as shown below:

`x-authorization: <your-credentials>`

<aside class="notice">
You must replace <code>&lt;your-credentials&gt;</code> with your Base64 encoded API credentials.
</aside>


# User

This resource provides APIs to get user ratings and allows you to create an unclaimed DUPR account for the users which are not on the platform.

## Search

Top-level API resource to have support via retrieval via "search" API methods. DUPR search API methods utilize cursor-based pagination via the `offset` parameter. For example you can make a initial request with `offset=0` or exclude `offset` parameter from request and then in subsequent calls keep on incrementing `offset` with `limit` i.e. `offset += limit`.

### HTTP Request

`GET https://<base-url>/user/{version}/search`


### Request Body Paramters

> Example search request to reterive male users between 18 to 25 age lives in Austin, TX, USA with reliable doubles ratings between 2.3 and 3.3.

```json
{
  "filters": {
    "age": {
      "max": 25,
      "min": 18
    },
    "gender": "MALE",
    "location": {
      "address": "Austin, TX, USA",
      "radiusInMeters": 40233.6
    },
    "rating": {
      "max": 3.3,
      "min": 2.3,
      "reliable": true,
      "type": "DOUBLES"
    }
  },
  "limit": 25,
  "offset": 0,
  "query": "*"
}
```

> To search users run this command with your access token.

```shell
curl -X POST "https://<base-url>/user/v1.0/search" \
     -H "accept: application/json" \
     -H "Authorization: Bearer eyJhbGciOiJSUzUxMiJ9." \
     -H "Content-Type: application/json" \
     -d "{ 
          \"query\": \"*\",
          \"limit\": 25,
          \"offset\": 0,
          \"filters\": { 
              \"age\": { 
                  \"max\": 25, 
                  \"min\": 18 
              }, 
              \"gender\": \"MALE\", 
              \"location\": { 
                  \"address\": \"Austin, TX, USA\", 
                  \"radiusInMeters\": 40233.6 
              }, 
              \"rating\": { 
                  \"max\": 3.3, 
                  \"min\": 2.3, 
                  \"reliable\": true, 
                  \"type\": \"DOUBLES\" 
              } 
          }
        }"
```

Parameter | Default | Description
--------- | :-----: | -----------
query     | *       | The search query string, e.g. first or last name of a user.
offset    | 0       | A cursor for pagination across multiple pages of results. Set `0` or don’t include this parameter on the first call.
limit     | 20      | A limit on the number of objects to be returned. Limit can range between 1 and 100, and the default is 10.
filters   | `null`  | Filters applied to this session that restrict the kinds of users to collect.

### Filter Facets

Facet     | Type       | Parameters | Default    | Description
--------- | :--------: | :--------: | :--------: | -----------
age       | object     |            | -          | Object criteria to restrict results based on user's age.
          |            | min        | -          | Minimum user age in years, 0 or more.
          |            | max        | -          | Maximum user age in years, 1 or more (must be greater than minimum age).
gender    | Enum       | -          | -          | Value criteria to restrict results based on gender, value must be either `MALE` or `FEMALE`.
location  | object     |            | -          | Object criteria to restrict results based on user's high level location.
          |            | address    | -          | Street level address in format (&lt;`street`&gt;, &lt;`county / city`&gt;, &lt;`state / region`&gt;, &lt;`country`&gt;). This is an optional parameter in case you do not have lat / long of the user location.
          |            | lat        | -          | The geographic latitude of user's approximate location.
          |            | lng        | -          | The geographic longitude of user's approximate location.
          |            | radiusInMeters | 40233.6 (25 miles)   | Radius in meters to restrict user's from a specific region.
rating    | object     |            | -          | Object criteria to restrict results based on user's rating.
          |            | min        | 2.0        | Minimum value of user ratings can be down to 2.0.
          |            | max        | 8.0        | Maximum value of user ratings can be up to 8.0.
          |            | reliable   | `null`     | User's rating should be reliable or not, by default `null` which means it is not considered while fetch results.
          |            | type       | -          | Type of ratings to apply min and max values, it should be either `SINGLES` or `DOUBLES`.

<aside class="notice">
You can filter results with 4 major facets: age, gender, location and rating. You can use one or any combination of the facets to narrow down the search.
</aside>

### Response Parameters

> The above command returns JSON structured like this:

```json
{
  "status": "SUCCESS",
  "result": {
    "offset": 0,
    "limit": 25,
    "total": 3,
    "totalValueRelation": "EQUAL_TO",
    "hasMore": false,
    "hasPrevious": false,
    "empty": false,
    "hits": [
      {
        "id": "8DJ7YL",
        "fullName": "Kainoa Reponte",
        "firstName": "Kainoa",
        "lastName": "Reponte",
        "gender": "MALE",
        "age": 24,
        "address": "Austin, TX, US",
        "ratings": {
          "singles": null,
          "isSinglesReliable": null,
          "doubles": "2.97",
          "isDoublesReliable": true
        }
      },
      {
        "id": "17LQG0",
        "fullName": "Christian Lee",
        "firstName": "Christian",
        "lastName": "Lee",
        "gender": "MALE",
        "age": 23,
        "address": "Manor, TX, US",
        "ratings": {
          "singles": null,
          "isSinglesReliable": null,
          "doubles": "2.54",
          "isDoublesReliable": true
        }
      },
      {
        "id": "3ZVQJP",
        "fullName": "Lexian Andrushko",
        "firstName": "Lexian",
        "lastName": "Andrushko",
        "gender": "MALE",
        "age": 18,
        "address": "Austin, TX, US",
        "ratings": {
          "singles": null,
          "isSinglesReliable": null,
          "doubles": "3.25",
          "isDoublesReliable": false
        }
      }
    ]
  }
}
```

List of all users matching search request criteria.

**Common pagination parameters:**

Parameter | Type    | Description
--------- | :-----: | -----------
offset    | int     | A cursor for pagination across multiple pages of results sent in request.
limit     | int     | A limit on the number of objects to be returned sent in request.
total     | int     | Total number of results avaiable.
totalValueRelation     | enum     | Relation with total value, `EQUAL_TO` for exact number and `GREATER_THAN_OR_EQUAL_TO` in case total number is greater than or equal to value of actual.
hasMore   | boolean | Are there more results match request criteria.
hasPrevious | boolean | `true` if there are any previous results, always `false` for first request.
empty     | boolean | If hits is an empty array.

**User specific parameters:**

Parameter | Type    | Description
--------- | :-----: | -----------
id        | string  | Unique identifier for the user, generally known as DUPR ID.
fullName  | string  | Person's full name.
firstName | string  | The person’s first name.
lastName  | string  | The person’s last name.
gender    | enum    | The person’s gender (International regulations require either “male” or “female”).
age       | int     | The person’s age in years.
address   | string  | City level address in format (&lt;`county / city`&gt;, &lt;`state / region`&gt;, &lt;`country`&gt;).
ratings   | object  | An object representation of ratings `singles` and `doubles`.
singles   | object  | Singles rating of user. Default is `null`. Float value between 2.0 and 8.0.
isSinglesReliable   | boolean  | Weather singles rating of this person is reliable or not.
doubles   | object  | Doubles rating of user. Default is `null`. Float value between 2.0 and 8.0.
isDoublesReliable   | boolean  | Weather doubles rating of this person is reliable or not.

