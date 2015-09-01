# API resources

This text aims to set some basic guidelines for the design of REST API resources.

The main goal of these basic guidelines are to help you create beautiful easy to use APIs for developers.

Most inspiration of this writeup is taken from Apigee's pragmatic approach to API design explained by their 2011 book.

## Resource names

Resource naming is part straightforward, part tough.

The simple rules are as follows:
- Use nouns not verbs
- Use plural nouns
- Use concrete names

````
Bad:
- /unicorn
Meh:
- /animals
Good:
- /unicorns
- /horses
````

The hard part is naming. Naming can be surprisingly difficult. Finding the best easy to use self explanatory noun is tough.

Sometimes you have operations that do not involve resources ie language translations or currency conversion. In such scenarios you should use verbs not nouns for the name of your "resource".
````
/convert?text="intergalactic planetary"&from=english&to=norwegian
````

## Resource structure

After you have identified a good resource name it is time to structure the different operations the API offers on the resource.

Structuring the interface of your resource there are some simple to follow conventions that make your API simpler to use for developers.

1. A resource should only have two base urls.
````
/unicorns
/unicorns/12345
````
2. The CRUD (create, read, update and delete) operations on your resource are all implemented with the two base urls using the HTTP methods POST, GET, PUT and DELETE.

Base url | GET | POST | PUT | DELETE
---|---|---|---|---
/unicorns | All unicorns | Create unicorn | Bulk update unicorns | Delete all unicorns
/spaceships/12345 | Unicorn 12345 | - | Update unicorn 12345 | Delete unicorn 12345

## Payload

Given that your API returns JSON as default, your attributes should follow Javascript naming conventions.

- ~~"created_at": value~~
- ~~"CreatedAt": value~~
- "createdAt": value

Responses from APIs tend to contain meta-data as well as the payload itself. Having some general approach to structuring this information across your API makes sense. There are many specifications for this around such as JSON.API, JSend, OData and HAL. Some of these specifications are really complicated. We will show an example using HAL in this text as HAL is both straightforward and powerful.

````json
GET /unicorns  
Host: api.unicorn.space
Content-Type: application/json
Accept: application/hal+json
{
  "_links": {
    "self": {
      "href": "/unicorns"
    }
  },
  "_embedded": {
    "unicorns": [
      {
        "_links": {
          "self": {
            "href": "/unicorns/12345"
          },
          "owner": {
            "href": "/farmers/98765"
          },
          "friends": {
            "href": "/unicorns/12345/friends"
          },
          "image": {
            "href": "https://cdn.unicorns.space/12hu1hd?quality=huge"
          }
        },
        "id": "12345",
        "name": "Herbertpuff Goldenpants",
        "ownerId": "98765"
      }
    ]
  },
  "totalNumberOfUnicorns": 1,
  "unicornsInCurrentFilter": 1
}
````

## Exceptions and errors

An often hastened or neglected part of APIs are the behavior when things go wrong. That goes for both controlled and uncontrolled scenarios. For being pragmatic one should not over engineer how the API behaves in these scenarios early on, but some low hanging fruits should definitely be harvested to help developers along.

1. Standardize on some few HTTP status codes. There are an awful lot of status codes available. There are however more value in returning good error messages than being granular in the use of HTTP status code. To start of the following status codes are often sufficient:
````
200 - OK
400 - Bad Request
500 - Internal Server Error
````
In short 200 is for all successful requests. 400 for all requests where the client sent a bad request such as invalid data, non sufficient authorization or a false url. At last 500s are for any valid client request where the API failed to process the request successfully such as a database timeout or an erroneous program. If you decide to use more status codes than just these few it should be based on a need for more granular control within these general classes of status codes.
2. In addition to use an appropriate status code you should help developers and users to understand what is wrong by beeing as specific as possible in your error messages. Using a uniform error object structure could also be beneficial. If there are more information on the error somewhere, link to it. Remember you might very well be the one to debug these errors down the line.
````
{
    "developerMessage": "Could not connect to database; connection pool exceeded.",
    "userMessage": "Could not process your request at the moment, please try again later.",
    "statusCode": 500,
    "errorCode": 1,
    "moreInfo": "http://url.to.errorcode-info"
}
````

## Complex operations on resources

Simple CRUD operations are rarely sufficient to offer the expected functionality on the resources in your API. Common operations not covered by pure CRUD are filtering, paging, different formats, search and attribute selection.

If your goal is to make a general API such complexity should be hidden behind the ?. Hiding this functionality is opposed to adding specialized endpoints for every single of these functions. Adding special endpoints for every case will leave you with a much larger API surface. You will also have to do a lot more naming as each specialized operation will need another good name; naming is tough!

````
Bad:
/unicorns/youngerThanTwenty
/unicorns/getByOwner/98765

Good:
/unicorns?maxAge=19
/unicorns?ownerId=98765
/unicorns?orderBy=age
/unicorns?limit=10&offset=100
/unicorns?fields=name,age
````

One exception where sweeping complexity behind query parameters might not be the best approach is associated resources. Associated resources can be accessed using the following structure '/resource/identifier/associated-resource'. Depending on the relationship between the resources the associated resource could be a single resource or a set. Note that the associations should never need to be deeper than one level as the first resource is uniquely identified.
````
/unicorns/12345/friends
````
