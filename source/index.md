---
title: API Reference

language_tabs:
  - shell
  - ruby
  - python

toc_footers:
  - <a href='http://info.xen.do/developers/api'>Sign Up for a Developer Key</a>
  - <a href='http://help.xen.do'>Xendo Help Center</a>

includes:
  - errors

search: true
---

# Introduction

Xendo's a hosted Enterprise Search service that provides unified search across 30+ cloud apps like Google Apps, Salesforce, Box, Dropbox, Asana, Microsoft Exchange and more (see the full list here https://xen.do/integrations).

The Xendo API enables you to build full-text search into your applications and workflows so you can provide contextual information to your users such a list of apps that contain the search term they're looking for or providing auto-complete as they're typing a name.

# Authentication

Xendo uses API keys to allow access to the API.  To get started, [request access to our developer portal](http://info.xen.do/developers/api).  After you've been setup as a Xendo developer, you can [create an OAuth2 client](https://xen.do/developer/oauth2apps/) which which enables you to securely access Xendo's API endpoints.

You'll need to take a note of the `CLIENT ID` and `CLIENT SECRET` associated with your OAuth2 client.

## Step 1 - Authorization

Your authenticating web application should redirect to the following URL:

### Redirect URL

`https://xen.do/oauth2/authorize`

### GET Parameters

Parameter | Default | Description
--------- | ------- | -----------
response_type | code | Return an authorization code.
client_id | None | <Your Client ID>
redirect_uri | None | <Your Redirect URI>

<aside class="success">
You application will redirect to the `redirect_uri` with the authorization code. 
</aside>

## Step 2 - Obtain Access Token

Your application needs to submit a request to the following URL:

### HTTP Request

`POST https://xen.do/oauth2/access_token` 

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
grant_type | authorization_code | Request an authorization code.
client_id | None | <Your Client ID>
client_secret | None | <Your Client ID>
code | None | <Your Authorization Code from Step 1>
redirect_uri | None | <Your Redirect URI>


## Step 3 - Refreshing an Access Token

```shell
# The Authorization Bearer header must be passed with every API request.  For example:
curl -X GET "https://xen.do/api/v1/search/" 
    -H "Authorization: Bearer 17126fbae658371b7c0eeda04b2cfb3b57f4cb60" 
```

> Make sure to replace the Bearer token with your access token.


The Access Token is short lived and will expire. You'll need to use the Refresh Token to obtain a new Access Token:

### HTTP Request

`POST https://xen.do/oauth2/access_token` 

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
grant_type | refresh_token | Request an authorization code.
client_id | None | <Your Client ID>
client_secret | None | <Your Client ID>
refresh_token | None | <Your Refresh Token from Step 2>

<aside class="success">
The response will include a new access token and refresh token. 
</aside>

Once you have an access token, you can call Xendo's API endpoints (same as before) with an Authorization Bearer header.

# Search

## Search

> For example, searching for 'Winston Churchill' would look like this:

```shell
curl -X GET "https://xen.do/api/v1/search/?q=winston%20churchill"
    -H "Authorization: Bearer 17126fbae733871b7c0eeda04b2cfb3b57f4cb60" 
```

> The above command returns JSON structured like this:

```json
{
    "response": {
        "start": 0, 
        "maxScore": 12.350116, 
        "numFound": 373, 
        "docs": [
            {
                "utc_xendo_sort_timestamp": "2015-07-02T20:58:34Z", 
                "description": "Winston Churchill and DevOps help scale enterprise security", 
                "author": "mike d. kail", 
                "url": "http://blog.conjur.net/winston-churchill-security-and-devops", 
                "imageUrl": "https://media.licdn.com/mpr/mprx/0_Z3_74LxSHKBPY8MGTlchRvjp5f", 
                "title": "Winston Churchill, Security, and DevOps", 
                "profileUrl": "https://www.linkedin.com/profile/view?id=13156", 
                "source": "linkedin", 
                "last_modified": "1435870714153", 
                "content_type": "message", 
                "score": 12.350116, 
                "source_content_type": "share", 
                "storm_user_id": "8", 
                "id": "12944-s6022478279817060352"
            }, 
            {
                "utc_xendo_sort_timestamp": "2015-08-06T04:56:47Z", 
                "score": 1.326375, 
                "author": "SeanEllis", 
                "url": "https://twitter.com/SeanEllis/status/629154126358667264", 
                "imageUrl": "https://pbs.twimg.com/profile_images/601169cxPsS_normal.png", 
                "profileUrl": "https://twitter.com/SeanEllis", 
                "source": "twitter", 
                "last_modified": "Thu Aug 06 04:56:47 UTC 2015", 
                "content_type": "message", 
                "message": "If you're going through hell, keep going. - Winston Churchill", 
                "source_content_type": "tweet", 
                "storm_user_id": "8", 
                "id": "350-629154126358667264"
            }, 
            {
                "utc_xendo_sort_timestamp": "2015-08-03T19:05:08Z", 
                "score": 1.3218337, 
                "author": "briancarter", 
                "url": "https://twitter.com/briancarter/status/628280455117316096", 
                "imageUrl": "https://pbs.twimg.com/profile_images/KvxdX8oX_normal.jpeg", 
                "profileUrl": "https://twitter.com/briancarter", 
                "source": "twitter", 
                "last_modified": "Mon Aug 03 19:05:08 UTC 2015", 
                "content_type": "message", 
                "message": "Success is not final, failure is not fatal: it is the courage to continue that counts. - Winston Churchill", 
                "source_content_type": "tweet", 
                "storm_user_id": "8", 
                "id": "350-628280455117316096"
            }, 
        ]
    }
}
```

This endpoint enables an authenticated user to search across their content.

### HTTP Request

`GET https://xen.do/api/v1/search/`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
q | None | Search query.
sources | None | Comma-separated list of sources.  Supported types include `salesforce`, `gmail`, `box`, `dropbox`.
content_types | None | Comma-separated list of content types.  Supported types include `document`, `spreadsheet`, `contact`.
dates | None | Restrict the results to items within the specified date range expressed as `dates=<date_from>,<date_to>`.  Dates should be formatted `YYYY-MM-DDThh:mm:ssZ`.  Searching between a date range would look this `/api/v1/search/?q=*&dates=2015-10-20T23:00:00Z,2015-11-11T23:00:00Z`. Alternatively, the 'dates' parameter can accept a descriptive value: `NOW`, `MINUTES`, `HOURS`, `DAYS`, `MONTHS`, `YEARS` which can be used `/api/v1/search/?q=*&dates=n<date unit>`.  As an example, searching for content updates within the past 3 hours would be `/api/v1/search/?q=*&dates=3HOURS`. 
sort | By relevance | Override the default sort order.  Valid sort orders include: `date`, `author`, `name`, `title`.`subject`.  Ascending `:asc` or Descending `:desc` must be specified, for example: `sort=date:asc`.
page_size | 20 | Number of results to return per page.
p | 0 | Page of results.


## Facets

> For example, finding the Services and Content types that contain the term 'Winston Churchill' would look like this:

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get()
```

```shell
curl -X GET "https://xen.do/api/v1/facets/?q=winston%20churchill"
  -H "Authorization: Bearer 17126fbae733871b7c0eeda04b2cfb3b57f4cb60" 
```

> The above command returns JSON structured like this:

```json
{
  "response":
    {"numFound":6,"start":0,"docs":[]},
    "facet_counts": {
      "facet_queries":{},
      "facet_fields":{
        "source":["twitter",4,"googlecontacts",2],
        "content_type":["message",4,"contact",2]
      },
      "facet_dates":{},
      "facet_ranges":{},
      "facet_intervals":{},
      "facet_heatmaps":{}
    }
}
```

This endpoint enables search across all connected service accounts.

### HTTP Request

`GET https://xen.do/api/v1/facets/`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
q | None | Search query.
facets | None | Comma-separated list of facets.  Supported types include `source`, `content_type`.
sources | None | Comma-separated list of sources.  Supported types include `salesforce`, `gmail`, `box`, `dropbox`.
content_types | None | Comma-separated list of content types.  Supported types include `document`, `spreadsheet`, `contact`.
dates | None | Restrict the results to items within the specified date range expressed as `dates=<date_from>,<date_to>`.  Dates should be formatted `YYYY-MM-DDThh:mm:ssZ`.  Searching between a date range would look this `/api/v1/search/?q=*&dates=2015-10-20T23:00:00Z,2015-11-11T23:00:00Z`. Alternatively, the 'dates' parameter can accept a descriptive value: `NOW`, `MINUTES`, `HOURS`, `DAYS`, `MONTHS`, `YEARS` which can be used `/api/v1/search/?q=*&dates=n<date unit>`.  As an example, searching for content updates within the past 3 hours would be `/api/v1/search/?q=*&dates=3HOURS`. 
facet_dates | None | If this parameter is specified, matching counds for the following date ranges will be returned: `1DAY`, `7DAYS`, `14DAYS`, `1MONTH`, `3MONTHS`, `6MONTHS`.


## Autosuggest

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get()
```

```shell
curl -H "Authorization: Bearer 17126fbae733871b7c0eeda04b2cfb3b57f4cb60" -X GET https://xen.do/api/v1/autosuggest/?q=winst
```

> The above command returns JSON structured like this:

```json
{
  "response": {
    "numFound":45,"start":0,"docs":[]
  },
  "facet_counts":{
    "facet_queries":{},
    "facet_fields":{
      "content_type":["message",23,"document",14,"contact",8],
      "source":["twitter",23,"github",11,"googlecontacts",8,"dropbox",3]
    },
    "facet_dates":{},
    "facet_ranges":{},
    "facet_intervals":{},
    "facet_heatmaps":{}
  }
}
```

This endpoint enables search across all connected service accounts.

### HTTP Request

`GET https://xen.do/api/v1/autosuggest/`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
q | None | Search query.
sources | None | Comma-separated list of sources.  Supported types include `salesforce`, `gmail`, `box`, `dropbox`.
content_types | None | Comma-separated list of content types.  Supported types include `document`, `spreadsheet`, `contact`.



# Provisioning

## Users

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get()
```

```shell
curl -H "Authorization: Bearer 17126fbae733871b7c0eeda04b2cfb3b57f4cb60" -X GET https://xen.do/api/v1/search/?q=winston%20churchill
```

> The above command returns JSON structured like this:

```json
{
    "response": {
        "start": 0, 
        "maxScore": 12.350116, 
        "numFound": 373, 
        "docs": [
            {
                "utc_xendo_sort_timestamp": "2015-07-02T20:58:34Z", 
                "description": "Winston Churchill and DevOps help scale enterprise security", 
                "author": "mike d. kail", 
                "url": "http://blog.conjur.net/winston-churchill-security-and-devops", 
                "imageUrl": "https://media.licdn.com/mpr/mprx/0_Z3_74LxSHKBPY8MGTlchRvjp5f", 
                "title": "Winston Churchill, Security, and DevOps", 
                "profileUrl": "https://www.linkedin.com/profile/view?id=13156", 
                "source": "linkedin", 
                "last_modified": "1435870714153", 
                "content_type": "message", 
                "score": 12.350116, 
                "source_content_type": "share", 
                "storm_user_id": "8", 
                "id": "12944-s6022478279817060352"
            }, 
            {
                "utc_xendo_sort_timestamp": "2015-08-06T04:56:47Z", 
                "score": 1.326375, 
                "author": "SeanEllis", 
                "url": "https://twitter.com/SeanEllis/status/629154126358667264", 
                "imageUrl": "https://pbs.twimg.com/profile_images/601169cxPsS_normal.png", 
                "profileUrl": "https://twitter.com/SeanEllis", 
                "source": "twitter", 
                "last_modified": "Thu Aug 06 04:56:47 UTC 2015", 
                "content_type": "message", 
                "message": "If you're going through hell, keep going. - Winston Churchill", 
                "source_content_type": "tweet", 
                "storm_user_id": "8", 
                "id": "350-629154126358667264"
            }, 
            {
                "utc_xendo_sort_timestamp": "2015-08-03T19:05:08Z", 
                "score": 1.3218337, 
                "author": "briancarter", 
                "url": "https://twitter.com/briancarter/status/628280455117316096", 
                "imageUrl": "https://pbs.twimg.com/profile_images/KvxdX8oX_normal.jpeg", 
                "profileUrl": "https://twitter.com/briancarter", 
                "source": "twitter", 
                "last_modified": "Mon Aug 03 19:05:08 UTC 2015", 
                "content_type": "message", 
                "message": "Success is not final, failure is not fatal: it is the courage to continue that counts. - Winston Churchill", 
                "source_content_type": "tweet", 
                "storm_user_id": "8", 
                "id": "350-628280455117316096"
            }, 
        ]
    }
}
```

This endpoint enables search across all connected service accounts.

### HTTP Request

`GET https://xen.do/api/v1/search/`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
q | None | Search query.
sources | None | Comma-separated list of source services eg. salesforce,box,gmail,googledrive.

<aside class="success">
Remember — a happy kitten is an authenticated kitten!
</aside>




# Kittens

## Get All Kittens

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get()
```

```shell
curl "http://example.com/api/kittens"
  -H "Authorization: meowmeowmeow"
```

> The above command returns JSON structured like this:

```json
[
  {
    "id": 1,
    "name": "Fluffums",
    "breed": "calico",
    "fluffiness": 6,
    "cuteness": 7
  },
  {
    "id": 2,
    "name": "Max",
    "breed": "unknown",
    "fluffiness": 5,
    "cuteness": 10
  }
]
```

This endpoint retrieves all kittens.

### HTTP Request

`GET http://example.com/api/kittens`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
include_cats | false | If set to true, the result will also include cats.
available | true | If set to false, the result will include kittens that have already been adopted.

<aside class="success">
Remember — a happy kitten is an authenticated kitten!
</aside>

## Get a Specific Kitten

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get(2)
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get(2)
```

```shell
curl "http://example.com/api/kittens/2"
  -H "Authorization: meowmeowmeow"
```

> The above command returns JSON structured like this:

```json
{
  "id": 2,
  "name": "Max",
  "breed": "unknown",
  "fluffiness": 5,
  "cuteness": 10
}
```

This endpoint retrieves a specific kitten.

<aside class="warning">If you're not using an administrator API key, note that some kittens will return 403 Forbidden if they are hidden for admins only.</aside>

### HTTP Request

`GET http://example.com/kittens/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the kitten to retrieve

