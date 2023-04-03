---
title: DUPR - API Reference Document (BETA)

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

Welcome to the **DUPR API v1.0 (BETA)**! You can use our API to upload matches in bulk, search players, get player ratings, fetch DUPR club members ratings, etc. from our database.

You can view code examples in the dark area to the right, and you can switch the programming language of the examples with the tabs in the top right.

# Credentials

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
| Test | https://test.mydupr.com/api/ |

# Authentication

> Combine your client key and secret as show below.

```shell
ck-6f6e9f3d-ca92-49a5-fced-1bcc36ec127be:cs-008a8f63f1fd5433fb2ea1a25fdec9c8
```

> When above combination is encoded with Base64, it should return exact same string.

```plaintext
Y2stNmY2ZTlmM2QtY2E5Mi00OWE1LWZjZWQtMWJjYzM2ZWMxMjdiZTpjcy0wMDhhOGY2M2YxZmQ1NDMzZmIyZWExYTI1ZmRlYzljOA==
```

> To authorize, use this code command with your actual credentials:

```shell
# With shell, you can just pass the correct header with each request
curl -X POST "https://test.mydupr.com/api/auth/v1.0/token" \
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

`ck-6f6e9f3d-ca92-49a5-fced-1bcc36ec127be:cs-008a8f63f1fd5433fb2ea1a25fdec9c8`

Test credentials have prefix `test-`, for example `test-ck-<hash>` or `test-cs-<hash>`.

Make a request to authenticate using credentials as the special header paramter as shown below:

`x-authorization: <your-credentials>`

<aside class="notice">
You must replace <code>&lt;your-credentials&gt;</code> with your Base64 encoded API credentials.
</aside>


# User

This resource provides APIs to get user ratings and allows you to create an unclaimed DUPR account for the users which are not on the platform.

## Search Users

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

Top-level API resource to have support via retrieval via "search" API methods. DUPR search API methods utilize cursor-based pagination via the `offset` parameter. For example you can make a initial request with `offset=0` or exclude `offset` parameter from request and then in subsequent calls keep on incrementing `offset` with `limit` i.e. `offset += limit`.

### HTTP Request

`POST https://<base-url>/user/{version}/search`


### Request Body Paramters

> To search users run this command with your access token.

```shell
curl -X POST "https://test.mydupr.com/api/user/v1.0/search" \
     -H "Authorization: Bearer <your-access-token>" \
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

### HTTP Response

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
gender    | enum    | The person’s gender (International regulations require either “MALE” or “FEMALE”).
age       | int     | The person’s age in years.
address   | string  | City level address in format (&lt;`county / city`&gt;, &lt;`state / region`&gt;, &lt;`country`&gt;).
ratings   | object  | An object representation of ratings `singles` and `doubles`.
singles   | double  | Singles rating of user. Default is `null`. Floating value between 2.0 and 8.0.
isSinglesReliable   | boolean  | Weather singles rating of this person is reliable or not.
doubles   | double  | Doubles rating of user. Default is `null`. Floating value between 2.0 and 8.0.
isDoublesReliable   | boolean  | Weather doubles rating of this person is reliable or not.

## Get User Rating

```shell
curl -X GET "https://test.mydupr.com/api/user/v1.0/V8DYL8" \
     -H "Authorization: Bearer <your-access-token>"
```

This API gives most recent rating of DUPR user. You need to provide DUPR id which can be obtained via search API or user can find it in their application user profile.

### HTTP Request

`GET https://<base-url>/user/{version}/{id}`

### Path Paramters

| Parameter | Description |
| :-------: | :---------: |
| `id`      | DUPR ID user obtained by search or user can get it from user profile in app. |

### HTTP Response

```json
{
  "status": "SUCCESS",
  "result": {
    "id": "V8DYL8",
    "fullName": "Ben Johns",
    "firstName": "Ben",
    "lastName": "Johns",
    "ratings": {
      "singles": "7.19",
      "isSinglesReliable": true,
      "doubles": "6.92",
      "isDoublesReliable": true
    }
  }
}
```

Parameter | Type    | Description
--------- | :-----: | -----------
id        | string  | Unique identifier for the user, generally known as DUPR ID.
fullName  | string  | Person's full name.
firstName | string  | The person’s first name.
lastName  | string  | The person’s last name.
gender    | enum    | The person’s gender (International regulations require either “MALE” or “FEMALE”).
age       | int     | The person’s age in years.
address   | string  | City level address in format (&lt;`county / city`&gt;, &lt;`state / region`&gt;, &lt;`country`&gt;).
ratings   | object  | An object representation of ratings `singles` and `doubles`.
singles   | object  | Singles rating of user. Default is `null`. Float value between 2.0 and 8.0.
isSinglesReliable   | boolean  | Weather singles rating of this person is reliable or not.
doubles   | object  | Doubles rating of user. Default is `null`. Float value between 2.0 and 8.0.
isDoublesReliable   | boolean  | Weather doubles rating of this person is reliable or not.


## Create a User

> All of the parameters are recommended to create a user. The detail helps us assign ratings to the correct user.

```json
{
  "firstName": "John",
  "lastName": "Doe",
  "emailAddress": "john.doe@mydupr.com",
  "isoAlpha2Code": "US",
  "phoneNumber": "+12481234701",
  "birthdate": "1973-09-25",
  "age": 50,
  "gender": "MALE",
  "address": "Dripping Springs, Hays County, TX, USA",
  "identifier": "unique-identifier"
}
```

You can create a new user in case the user account is not present on DUPR platform. These created accounts will remain in unclaimed state until the actual user goes through the sign up flow and claims the account. It is recommended to provide birthdate or age, gender and location which helps us assign ratings to the correct user.

### HTTP Request

> To create a user run this command with your access token.

```shell
curl -X POST "https://test.mydupr.com/api/user/v1.0/create" \
     -H "Authorization: Bearer <your-access-token>" \
     -H "Content-Type: application/json" \
     -d "{
          \"firstName\": \"John\",
          \"lastName\": \"Doe\",
          \"emailAddress\": \"john.doe@mydupr.com\",
          \"isoAlpha2Code\": \"US\",
          \"phoneNumber\": \"+12481234701\",
          \"birthdate\": \"1973-09-25\",
          \"gender\": \"MALE\",
          \"address\": \"Dripping Springs, Hays County, TX, USA\",
          \"identifier\": \"unique-identifier\"
        }"
```

`POST https://<base-url>/user/v1.0/create`

### Request Body Paramters

Parameter     | Description
---------     | -----------
firstName     | The person’s first name.
lastName      | The person’s last name.
emailAddress  | Email address using which the person will be registered and get notified.
isoAlpha2Code | Standard 2 letters ISO code of a country in which phone number belongs.
phoneNumber   | Phone number including country code.
birthdate     | Birthdate of the person in ISO 8601 date format i.e. `yyyy-MM-dd`
age           | Age of the person in years.
gender        | The person’s gender (International regulations require either “MALE” or “FEMALE”).
address       | Street level address in format (&lt;`street`&gt;, &lt;`county / city`&gt;, &lt;`state / region`&gt;, &lt;`country`&gt;).
identifier    | A unique identifier from your end, it will help you identify user later.


### HTTP Response

> Once user is created successfully you should get alpha-numeric DUPR id in return.

```json
{
  "status": "SUCCESS",
  "message": "User created successfully",
  "result": {
    "id": "XOME32"
  }
}
```

Parameter | Type    | Description
--------- | :-----: | -----------
id        | string  | Unique identifier for the user, generally known as DUPR ID.

# Match

This resources provides APIs to upload or delete matches for your users.

## Create Match

> Example doubles match happened between Robert Lees (3LWVQ6), Adam Mitzel (3PZ5OZ) and Dave Conley (8JP24O), Matthew Fisher (30PDQ2) on 05th August 2022 at Newport Beach, CA, USA in The Newport Beach DUPR Waterfall Presented by Takeya tournament, Men's Doubles Flight C - 11AM bracket 

```json
{
  "identifier": "99ec7e067b864df3998646f5a6a2800a23ba9a64",
  "format": "DOUBLES",
  "matchDate": "2022-08-05",
  "location": "Newport Beach, CA, USA",
  "matchSource": "TOURNAMENT",
  "matchType": "SIDEOUT",
  "event": "The Newport Beach DUPR Waterfall Presented by Takeya",
  "bracket": "Men's Doubles Flight C - 11AM",
  "clubId": 6224372156,
  "teamA": {
    "game1": 11,
    "game2": 11,
    "game3": null,
    "game4": null,
    "game5": null,
    "player1": "3LWVQ6",
    "player2": "3PZ5OZ"
  },
  "teamB": {
    "game1": 3,
    "game2": 3,
    "game3": null,
    "game4": null,
    "game5": null,
    "player1": "8JP24O",
    "player2": "30PDQ2"
  },
  "extras": {
    "key1": "value1"
  }
}
```

This API allows you to create a match on DUPR platform for your users, these matches will be consider while we process ratings for the users.

### HTTP Request

`POST https://<base-url>/match/{version}/create`

### Request Body Paramters

> To submit a match run this command with your access token.

```shell
curl -X POST "https://test.mydupr.com/api/match/v1.0/create" \
     -H "Authorization: Bearer <your-access-token>" \
     -H "Content-Type: application/json" \
     -d "{ 
        \"identifier\": \"99ec7e067b864df3998646f5a6a2800a23ba9a64\", 
        \"format\": \"DOUBLES\", 
        \"matchDate\": \"2022-08-05\", 
        \"location\": \"Newport Beach, CA, USA\", 
        \"matchSource\": \"TOURNAMENT\", 
        \"matchType\": \"SIDEOUT\", 
        \"event\": \"The Newport Beach DUPR Waterfall Presented by Takeya\", 
        \"bracket\": \"Men's Doubles Flight C - 11AM\", 
        \"clubId\": 6224372156,
        \"teamA\": { 
          \"game1\": 11, 
          \"game2\": 11, 
          \"game3\": null, 
          \"game4\": null, 
          \"game5\": null, 
          \"player1\": \"3LWVQ6\", 
          \"player2\": \"3PZ5OZ\" 
        }, 
        \"teamB\": { 
          \"game1\": 3, 
          \"game2\": 3, 
          \"game3\": null, 
          \"game4\": null, 
          \"game5\": null, 
          \"player1\": \"8JP24O\", 
          \"player2\": \"30PDQ2\" 
        }, 
        \"extras\": { 
          \"key1\": \"value1\" 
        }
    }"
```

Parameter | Default | Description
--------- | :-----: | -----------
identifier|         | An unique identifier per match from your end.
format    |         | A match format either `DOUBLES` or `SINGLES`.
matchDate |         | Date when this match played.
location  |         | Street level address in format (&lt;`street`&gt;, &lt;`county / city`&gt;, &lt;`state / region`&gt;, &lt;`country`&gt;) where this match played.
matchSource | `SELF`| Match source determins in what type of event this match was played. Match Sources: [`SELF`, `CLUB`, `TOURNAMENT`].
matchType |`SIDEOUT`| Match type determins what type of match was played either `SIDEOUT` or `RALLY`.
event     |         | In which event, tourament or league name this match was played.
bracket   |         | In which bracket of tourament or league this match was played.
clubId    |         | DUPR Club id of which this match was played.
teamA     |         | Match team A object.
teamB     |         | Match team B object.
extras    |         | If there are any extra parameters you wish to attach in form of key-value pairs along with this match for your future reference.

### Team Object

Parameter | Default | Description
--------- | :-----: | -----------
player1   |         | DUPR id of player1, if not available create account using [Create a User](#create-a-user) API.
player2   |         | DUPR id of player2, in case it's a `DOUBLES` match; if not available you can create account using [Create a User](#create-a-user) API.
game1     |         | Score of this team in first game of match.
game2     |         | Score of this team in second game of match, can be `null` if not played.
game3     |         | Score of this team in third game of match, can be `null` if not played.
game4     |         | Score of this team in forth game of match, can be `null` if not played
game5     |         | Score of this team in fifth game of match, can be `null` if not played.

### HTTP Response

> The above command returns JSON structured like this:

```json
{
  "status": "SUCCESS",
  "message": "Match submitted successfully",
  "result": {
    "id": "XOME32"
  }
}
```

Parameter | Type    | Description
--------- | :-----: | -----------
id        | string  | Unique identifier for this match, save it at your end to reterive this match in future.

## Bulk Create Match

> Array of create a match requests.

```json
[
  {
    "identifier": "99ec7e067b864df3998646f5a6a2800a23ba9a64",
    "format": "DOUBLES",
    "matchDate": "2022-08-05",
    "location": "Newport Beach, CA, USA",
    "matchSource": "TOURNAMENT",
    "matchType": "SIDEOUT",
    "event": "The Newport Beach DUPR Waterfall Presented by Takeya",
    "bracket": "Men's Doubles Flight C - 11AM",
    "clubId": 6224372156,
    "teamA": {
      "game1": 11,
      "game2": 11,
      "game3": null,
      "game4": null,
      "game5": null,
      "player1": "3LWVQ6",
      "player2": "3PZ5OZ"
    },
    "teamB": {
      "game1": 3,
      "game2": 3,
      "game3": null,
      "game4": null,
      "game5": null,
      "player1": "8JP24O",
      "player2": "30PDQ2"
    },
    "extras": {
      "key1": "value1"
    }
  }
]
```

This resource help create matches in bulk for detail info check [Create Match](#create-match). At a time maximum 100 matches are accepted by this API.

### HTTP Request

`POST https://<base-url>/match/{version}/batch`

<aside class="notice">
Request body will contain create match request objects in array.
</aside>

> To submit a matches in bulk run this command with your access token.

```shell
curl -X POST "https://test.mydupr.com/api/match/v1.0/batch" \
     -H "Authorization: Bearer <your-access-token>" \
     -H "Content-Type: application/json" \
     -d "[
          { 
            \"identifier\": \"99ec7e067b864df3998646f5a6a2800a23ba9a64\", 
            \"format\": \"DOUBLES\", 
            \"matchDate\": \"2022-08-05\", 
            \"location\": \"Newport Beach, CA, USA\", 
            \"matchSource\": \"TOURNAMENT\", 
            \"matchType\": \"SIDEOUT\", 
            \"event\": \"The Newport Beach DUPR Waterfall Presented by Takeya\", 
            \"bracket\": \"Men's Doubles Flight C - 11AM\", 
            \"clubId\": 6224372156,
            \"teamA\": { 
              \"game1\": 11, 
              \"game2\": 11, 
              \"game3\": null, 
              \"game4\": null, 
              \"game5\": null, 
              \"player1\": \"3LWVQ6\", 
              \"player2\": \"3PZ5OZ\" 
            }, 
            \"teamB\": { 
              \"game1\": 3, 
              \"game2\": 3, 
              \"game3\": null, 
              \"game4\": null, 
              \"game5\": null, 
              \"player1\": \"8JP24O\", 
              \"player2\": \"30PDQ2\" 
            }, 
            \"extras\": { 
              \"key1\": \"value1\" 
            }
        }
     ]"
```

### HTTP Response

<aside class="notice">
Order of responses are exactly as provided in request. <code>null</code> element in <code>matchCodes</code> array represents some error occurred while submitting the match and detail present in errors array.
</aside>

> The above command returns JSON structured like this:

```json
{
  "status": "SUCCESS",
  "message": "Matches submitted successfully",
  "result": {
    "errors" : [
      {
        //... match request key-value pairs.
        "errors": {
          "identifier": "Provide a unique identifier for this match.",
          "matchDate": "Match date must be in ISO 8061 format date i.e. yyyy-MM-dd"
        }
      }
    ],
    "matchCodes": [null, "XOME32"]
  }
}
```

## Delete a Match

> Example to delete doubles match happened between Robert Lees (3LWVQ6), Adam Mitzel (3PZ5OZ) and Dave Conley (8JP24O), Matthew Fisher (30PDQ2) with identifier <code>99ec7e067b864df3998646f5a6a2800a23ba9a64</code> and match code <code>XOME32</code>

```json
{
  "identifier": "99ec7e067b864df3998646f5a6a2800a23ba9a64",
  "matchCode": "XOME32"
}
```

This API allows you to delete matches from DUPR platform, only which were added by you.

### HTTP Request

`DELETE https://<base-url>/match/{version}/delete`

### Request Body Paramters

Parameter | Default | Description
--------- | :-----: | -----------
identifier|         | An unique identifier of the match from your end.
matchCode |         | DUPR id of the match received at the time of match creation.


> To submit a match run this command with your access token.

```shell
curl -X DELETE "https://test.mydupr.com/api/match/v1.0/delete" \
     -H "Authorization: Bearer <your-access-token>" \
     -H "Content-Type: application/json" \
     -d "{ 
        \"identifier\": \"99ec7e067b864df3998646f5a6a2800a23ba9a64\", 
        \"matchCode\": \"XOME32\"
      }"
```

> The above command returns JSON structured like this:

```json
{
  "status": "SUCCESS",
  "message": "Match deleted successfully"
}
```

# Club

This resources provides APIs to reterive club members ratings information.

## Club Member Ratings

> Example of reteriving ratings of club members of [Texas DUPR Flex Leagues](https://mydupr.com/dashboard/club/7614955351/info) club.

```json
{
  "clubId": 7614955351
}
```

This API allows you to reterive ratings of all the club members of a particular club by it's id.

### HTTP Request

`POST https://<base-url>/club/{version}/members`

### Request Body Parameters

> To reterive all club members rating run this command with your access token.

```shell
curl -X POST "https://test.mydupr.com/api/club/v1.0/members" \
     -H "Authorization: Bearer <your-access-token>" \
     -H "Content-Type: application/json" \
     -d "{ 
          \"clubId\": 7614955351
        }"
```

Parameter | Type    | Description
--------- | :-----: | -----------
clubId    | int     | Unique DUPR club identifier.

### HTTP Response

```json
{
  "status": "SUCCESS",
  "results": [
    {
      "id": "3L2961",
      "fullName": "Austin Dempsey",
      "firstName": "Austin",
      "lastName": "Dempsey",
      "ratings": {
        "singles": "3.85",
        "isSinglesReliable": false,
        "doubles": "4.00",
        "isDoublesReliable": true
      }
    },
    {
      "id": "89XGK3",
      "fullName": "Joshua Arment",
      "firstName": "Joshua",
      "lastName": "Arment",
      "ratings": {
        "singles": "4.77",
        "isSinglesReliable": false,
        "doubles": "4.38",
        "isDoublesReliable": true
      }
    },
    {
      "id": "8G4PG1",
      "fullName": "Tomas Chase",
      "firstName": "Tomas",
      "lastName": "Chase",
      "ratings": {
        "singles": "3.39",
        "isSinglesReliable": false,
        "doubles": "3.55",
        "isDoublesReliable": false
      }
    },
    {
      "id": "8KWNO8",
      "fullName": "Ian Schumann",
      "firstName": "Ian",
      "lastName": "Schumann",
      "ratings": {
        "singles": "NR",
        "isSinglesReliable": true,
        "doubles": "4.09",
        "isDoublesReliable": true
      }
    }
  ]
}
```

Parameter | Type    | Description
--------- | :-----: | -----------
id        | string  | Unique identifier for the user, generally known as DUPR ID.
fullName  | string  | Person's full name.
firstName | string  | The person’s first name.
lastName  | string  | The person’s last name.
ratings   | object  | An object representation of ratings `singles` and `doubles`.
singles   | double  | Singles rating of user. Default is `null`. Floating value between 2.0 and 8.0.
isSinglesReliable   | boolean  | Weather singles rating of this person is reliable or not.
doubles   | double  | Doubles rating of user. Default is `null`. Floating value between 2.0 and 8.0.
isDoublesReliable   | boolean  | Weather doubles rating of this person is reliable or not.
