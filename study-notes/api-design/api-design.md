# Bike Rental App API design

To rent a bike, the user follows these steps:

- Before going to a station, the user sees on the app the list of all stations. Each station has a name, a latitude and
  longitude location, and a numeric identifier. Also, the app shows the quantity of available bikes on each station.
  This is useful because some stations may not have bikes available for rent, i.e., all have been rented.
- The user goes to one of the stations with available bikes.
- Once at the station, the user decides which bike they want. Like the stations, each bike has a number.
- The user rents a bike by inputting on the app the station number, the available bike number, and the destination
  station number.
- The station releases the chosen bike.
- The user rides the bike.
- After the ride, the user returns the bike to the destination station they specified.

Besides the bike rental itself, the service has other additional constraints and features:

- Each user can only rent one bike at a time.
- The user can see the history of their rentals, including the current active rental (if one exists). The history of
  rentals includes rental start dates and end dates.
- After a rental, the user can change the destination station through the app. However, this change is allowed only
  during the first 10 minutes after the rent.
- The user can also cancel the current active rental within 10 minutes. After canceling, the user must return the bike
  to the same station it was taken from.

Remember: we’ll just design the API for the mobile app of this bike rental service. We won’t worry about the
communication between the server and the stations. Also, we’ll assume all users already have accounts and subscriptions
for the service. In other words, we won’t care about user management or payment.

Now let’s see the pro cess we follow to design our RESTful API.

## From Spec to RESTful API

### Step 1: Identifying Nouns and Verbs

We’ve seen that REST is made of resources, representations, and actions. We’ve also seen that resources are like nouns
and actions are like verbs. So, identifying the nouns and verbs available at a system spec is a good way to start our
spec to API process. By reading the spec, we can get the following list of nouns and verbs related to the system use
cases:

Nouns:

- Station
- Bike
- User
- City

Verbs:

- View (user views the list of stations)
- Show (app shows the quantity of available bikes at a station)
- Rent (user rents a bike)
- Release (station releases a bike)
- Ride (user ride the bike)
- Return (user returns a bike)
- view (user views the history of rents)
- change (user changes destination station)
- cancel (user cancels the active rent)

But we won’t need REST resources for all of those nouns, nor REST actions for all of those verbs. We can remove the
following:

- City noun. We can assume each user is interested only in the city they’re currently located in.
- User noun. We already said we can suppose all users have accounts.
- Bike noun. The service doesn’t offer any additional info about bikes, except if they’re available or not.
- Release verb. We’re designing the API for the mobile app, not for the station.
- Return verb. Same as above.
- Ride verb. The service doesn’t care how the user rides the bike.

After removing those, we’ve ended up with this new list:

Nouns:

- Station

Verbs:

- View (user views the list of stations)
- Rent (user rents a bike)
- Show (app shows the quantity of available bikes at a station)
- View (user views the history of rents)
- Change (user changes destination station)
- Cancel (user cancels the active rent)

The next step is to convert the nouns to HTTP resources with URLs and the verbs to HTTP methods.

It’s easy to convert nouns into HTTP resources with URLs. For example, the noun “station” can become a resource located
by the URL path `/stations/` in our API. But there are some gotchas in the conversion of verbs to HTTP methods. Let’s
see why.

A word on HTTP “verbs”:

One limitation we have while mapping a spec to a RESTful API is the restricted set of verbs HTTP provides. We can only
use the documented HTTP methods: GET, POST, PUT, PATCH, DELETE, etc. As you can see, we don’t have a “RENT” verb, even
though at first glance our application requires it.

Instead of custom verbs, we must define custom nouns for our RESTful API. Anything can be a noun. Therefore anything can
be a resource that’s mapped by a URL. Even if it doesn’t look like a noun at first sight. “RENT” fits more into a verb,
but you can noun-ify it to make it a resource. Think about the “Rent” noun, not the “RENT” verb. Then you’ll see that
you can “add a Rent”. And HTTP fits here because it has the POST verb for adding things. See? Just by changing the way
we think about noun and verbs we can adapt our application to HTTP. The correct way to rent something via HTTP is to
POST a Rent.

Thus, to properly design our RESTful API we need to know really well the meaning of each HTTP method. What are the
semantics behind each method? How should we use them? Let’s see:

- **GET** – Should be used to retrieve data from the server. Means “give me a representation of this resource identified
  by this URL”. It’s worth noting that we must never use GET to change resources. For this, we have the following other
  methods.
- **POST** – Should be used to provide data to the server. Means “process the resource representation as a new
  subordinate of this URL”. In practice, POST is mostly used for creating resources. The client specifies the resource
  representation in the request body and makes a POST to the URL that holds the collection of resources. 3
- **PUT** – Should be used to update existing server resources by replacing their old state with a new one. Means “use
  this representation to replace the resource identified by this URL”.
- **DELETE** – Should be used to remove server resources. Means “delete the resource identified by this URL”.

### Step 2: Extracting URLs and their methods from nouns and verbs

We’ve seen that mapping nouns to resources is as easy as Station => /stations/. But verbs are different. Some verbs are
easily mapped to a HTTP method, because sometimes the semantics match. Other verbs will need to be noun-fied to become
resources, as we’ve seen in the last section with “Rent”.

Besides that, we also need to care about which data from resources will be included in request and response bodies. For
example, on a POST request for creation, the body needs to include the resource representation to be created. Another
example, a GET response body needs to contain a resource representation, but a DELETE response body does not.

Summarizing, here’s what we need to answer here to design our API:

- Which HTTP methods map to our verbs?
- Which resources and corresponding URLs should we create for our nouns and noun-fied verbs?
- For each URL + method, which resource data should be included in the request body?
- For each URL + method, which resource data should be included in the response body?

The result of answering those questions is the basis of our RESTful API design: combinations of URLs and the HTTP
methods they respond to, along with what’s included in the request and response bodies.

To achieve this, let’s analyze each of our verbs and think about the nouns they interact with:

**1. View (user views the list of stations)**

_Which HTTP method maps to “View”?_ -> “View the list” means the API should show the list. Which HTTP method fits that?
The GET method.

_What’s the noun here, what’s the resource? Which URL fits this resource?_ -> We need a GET request to the
list/collection of stations. “Collection of stations” is the resource here. “Station” is already a defined noun. Thus,
we should implement a /stations/ URL that accepts GETs

_What should be passed in the request body?_ -> Since we’re only GETting, we don’t need to pass anything.

_What should be included in the response body?_ -> GET /stations/ needs to return a representation of the collection of
stations. This representation could be a JSON list, for example. Each station has a name, a location, and identifier, so
we need to include these in the representation. To represent the location, we’ll use a JSON list with the latitude and
longitude as floats.

This means we need to have something like:

```shell
Request: GET /stations/
Request body: (empty)
Response body:
[{
	"id": 1,
	"name": "John St",
	"location": [40.7, -74]
},
{
	"id": 2,
	"name": "Brooklyn Bridge",
	"location": [40.7, -73]
}]
```

**2. Rent (user rents a bike)**

_Which HTTP method maps to “View”?_ -> POST

_What’s the noun here, what’s the resource? Which URL fits this resource?_ -> rent is noun and /rents

_What should be passed in the request body?_ -> The POST request needs to include the station number, the bike number to
be rented, and the destination station. These are the parts of the representation of a rent.

_What should be included in the response body?_ -> Nothing. If the POST succeeds, the API client will know the rent has
been made

```shell
Request: POST /rents/
Request body:
{
	"origin_station_id": 1,
	"bike_number": 10,
	"destination_station_id": 2
}
Response body: (empty)
```

**3. Show (app shows the quantity of available bikes at a station)**

_Which HTTP method maps to “View”?_  -> GET
_What’s the noun here, what’s the resource? Which URL fits this resource?_ -> /stations/{stationId}/bikes

**4. View (user views the history of rents)**

```shell
Request: GET /rents/
Request body: (empty)
Response body:
[{
	"id": 1,
	"origin_station_id": 1,
	"bike_number": 10,
	"destination_station_id": 2,
	"start_date": "2017-06-08T19:30:39+00:00",
	"end_date": null,
	"is_active": true
}]
```

**5. Change (user changes destination station)**

PUT /rents/{id}

```shell
Request: PUT /rents/321914/
Request body:
{
	"destination_station_id": 2
}
Response body: (empty)
```

**5. Cancel (user cancels the active rent)**

DELETE

/rents/{id}

```shell
Request: DELETE /rents/321914/
Request body: (empty)
Response body: (empty)
```

---

# Error handling

Requests can be wrong or can fail for various reasons. We need to answer questions like:

- How can the API tell clients the request worked as expected?
- How can the API say the request was wrong?
- Depending on the error the API returned, what clients can do to fix the request?

### Understanding HTTP status codes

HTTP status codes are divided into classes grouped by the first digit of the number. The available classes are:

- 1xx – Informational responses
- 2xx – Success
- 3xx – Redirection
- 4xx – Client errors
- 5xx – Server errors

2xx – Success class:

These status codes are returned when the server succeeded in processing the request.

Commonly used status codes here are **200 OK** for regular successful requests with a response body, **201 Created** for
successful requests that created some resource, and **204 No Content** for successful requests without a response body.

3xx – Redirection class:

These are used when the server found the requested resource somewhere else. To find the resource, the client should make
another request to the new URL. In APIs, it’s common to return **301 Moved Permanently** for resources that moved to
another URL. There’s an important status code in this class that’s not related to redirection: **304 Not Modified**.
We’ll discuss it in the Caching section of this article.

4xx – Client error class:

These are used when the server received a wrong request. This means the client structured the request incorrectly. We
always have to anticipate that clients can do something wrong (or even act maliciously). That’s the nature of
client-server apps. Because the client doesn’t hold all server state, the client can make wrong requests for various
reasons:

- Unauthenticated user.
- Bad user input that can’t be validated client-side, like a wrong password.
- Bad user input that the client-side didn’t validate.
- Out-of-sync errors, like updating a resource that is already deleted.
- Users trying to access resources without permission.
- etc.

Common status code here are **400 Bad Request** for requests with invalid or malformed data, **401 Unauthorized** for
requests with wrong authentication credentials, **403 Forbidden** for requests where authenticated users have
insufficient permission to access the resource, and **404 Not Found** for requests to unavailable or hidden resources (
more on that later).

5xx – Server error class:

When something wrong happens on the server, not because of client request data, but because of server state itself, the
error codes will be 5xx. Those should be unanticipated errors, like a broken database connection. Your API should never
throw 5xx errors because of wrongdoings by clients. Leave 5xx errors for exceptional and unpredicted cases. Monitor your
API responses for 5xx errors, because this usually means your code isn’t properly handling exceptions while processing
requests. Or worse: your server machine is facing problems. Commonly, APIs return **500 Internal Server Error** when
those things happen.

### Defining success conditions for our defined APIS

GET /stations/ (User sees the list of stations)

```shell
HTTP/1.1 200 OK
Content-Type: application/json

[{
    "id": 1,
    "name": "John St",
    "location": [40.7, -74],
    "available_bikes_quantity": 10
},
{
    "id": 2,
    "name": "Brooklyn Bridge",
    "location": [40.7, -73],
    "available_bikes_quantity": 2
}]
```

GET /rents/ (User sees the history of rents)

```shell
HTTP/1.1 200 OK
Content-Type: application/json

[]
```

POST /rents/ (User rents a bike)

```shell
REQUEST:
POST /rents/ HTTP/1.1
Content-Type: application/json

{
    "origin_station_id": 1,
    "bike_number": 10,
    "destination_station_id": 2
}

RESPONSE:
HTTP/1.1 201 Created
Location: /rents/321914/
```

PUT /rents/{id}/ (User changes destination station)

```shell
REQUEST:
POST /rents/321914/ HTTP/1.1
Content-Type: application/json

{
   "destination_station_id": 3
}

RESPONSE:
HTTP/1.1 200 OK
Content-Type: application/json

{
    "id": 321914,
    "origin_station_id": 1,
    "bike_number": 10,
    "destination_station_id": 3,
    "start_date": "2017-06-08T19:30:39+00:00",
    "end_date": null,
    "is_active": true
}
```

DELETE /rents/{id}/ (User cancels the active rent)

```shell
HTTP/1.1 204 No Content
```

### Defining error conditions

Under what conditions can our bike rental API operations fail? Status codes here will be always 4xx, because these are
errors we can anticipate. As we’ve seen in the description of the 4xx class, anticipated errors include bad input,
access to nonexistent resources, out-of-sync state, unauthenticated users, etc. Those are generic problems that can
happen in any API.

ex:

```shell
HTTP/1.1 400 Bad Request
Content-Type: application/json

{
    "errors": {
      "bike_number": ["This bike is already rented"]
    }
}
```

### Defining fault tolerance

What should a client do when it never gets a response? In that case, the client doesn’t know the current server state,
so is it fine to just try again? The answer for the these questions rely on two well-defined concepts: **safety and
idempotency.**

In the context of HTTP, safety means that a given request will not produce any changes in the server state. GET is the
main safe method in HTTP. Correct implementations of GET requests, as in our bike rental API, don’t have side effects.
Secondary effects like logging or throttling are fine, but in general the server shouldn’t change any resources after
handling a GET request. Therefore, it’s perfectly safe to retry GET requests if the client somehow lost track of the
server state.

On the other hand, idempotency means the resource state doesn’t change after subsequent requests, i.e., a first request
can change something, but the following requests will change nothing. This means the request can be replayed any number
of times without introducing new changes in the resource. DELETE is the easiest method to understand when we think about
idempotency. The first time you make a DELETE /rents/123/ request, you change the rent state by cancelling it. But
subsequent DELETE requests to the same /rents/123/ resource won’t change it, since it was already cancelled1. It’s like
multiplying by 0. Everything that’s safe is also idempotent, but opposite isn’t true.

A counterexample of safety and idempotency is the POST method for creating resources. If you make repeated POST requests
to the same URL, you’ll create multiple resources. You change the server state after every request, so there’s no safety
nor idempotency here. If the client connection with the API server is interrupted in the middle of a POST request, it’s
always good to check the server state with a GET before making another POST.

### Authentication

Let’s talk about some of these stateless authentication standards for HTTP APIs.

**Basic Authentication:**

Basic Authentication is the simplest stateless authentication scheme that can be implemented in HTTP. The client
authenticates users by including an Authorization header with credentials on requests. The credentials are computed by
joining the username and password with a colon (:) and encoding the result with base64. The result will be something
like: Authorization: Basic dXNlcm5hbWU6cGFzc3dvcmQ=.

Due to statelessness, if the client wants to make successive authenticated requests, it needs to include the
Authorization header in each of them.

Using Basic Auth with HTTPS is fine for simple or private APIs. However, other authentication methods are preferable for
public APIs, like our bike rental API example.

**Token Authentication:**

Usually, APIs that support Token Authentication have a “sign in” URL that accepts a POST request with username and
password and responds with a token. A token is simply a string, usually randomly or cryptographically generated.
Subsequent requests should use the token instead of the username and password, typically in the form of a header like
Authorization: Token t$IKL&qNhTfx0pr$. The token is mapped to the user in the server, generally in a database table.

The advantage of Token Authentication over Basic Authentication is that it decouples the authorization from the
password, thereby supporting the following functionalities:

- No password: if the API client is somehow compromised, for example, a thief that stole the phone of a bike rental
  service user, the attacker won’t have access to the user password.
- Invalidation: again, if compromised, the token can be invalidated without changing the password. There are other
  purposes for invalidation, e.g., to revoke access after a password change.
- Multiple tokens per user: if necessary, a single user can have multiple tokens. A use for this is different tokens for
  different API clients: one for the mobile app, another for the desktop app, etc.
- Rotation: tokens can expire after some time (or even after each request) and be renewed, thereby reducing the attack
  window.No password: if the API client is somehow compromised, for example, a thief that stole the phone of a bike
  rental service user, the attacker won’t have access to the user password.
- Invalidation: again, if compromised, the token can be invalidated without changing the password. There are other
  purposes for invalidation, e.g., to revoke access after a password change.
- Multiple tokens per user: if necessary, a single user can have multiple tokens. A use for this is different tokens for
  different API clients: one for the mobile app, another for the desktop app, etc. Rotation: tokens can expire after
  some time (or even after each request) and be renewed, thereby reducing the attack window.

**JSON Web Token (JWT):**

In simplified terms, a JWT is a standard, special type of token that holds a JSON payload with the data for
authentication.

You may ask: is this really safe? Can’t the client side change the payload and authenticate as another user or become an
admin?. Yes it’s safe, and no, the client can’t change the payload. If they change the payload, the token can be
recognized on the server side as false. The reason is that the JWT payload is cryptographically signed by the server,
meaning it can’t be tampered with.

Another great feature of JWT is that it supports authentication distributed in a multiple-server environment, like a
microservices architecture. Since the payload can carry information about the user, a node does not needs to communicate
with a central point to validate or retrieve information about the user on each request. From the token itself, servers
can extract the user ID, profile info, permissions, etc. With this capability, even if the authentication service node
goes down, other services can still operate normally. Relating this to bike rental, you can imagine that stations don’t
need to make a request to authenticate users!

**OAuth:**

If you have logged into some service using a social network profile like Google, Facebook, or Twitter, you were
authenticated via OAuth. It makes sense to use OAuth when you’re a service provider like Facebook and you want to give
other applications access to your users’ data. OAuth makes this possible by exchanging user credentials for an access
token.

### A note About Same-Origin Policy and CORS

On browsers, there’s something called same-origin policy that restricts how a document or script loaded from one origin
can interact with a resource from another origin.Origin here means the protocol, port, and host of a URL.

If there were no same-origin policy, when you visited hack-me.com, it could make an AJAX request from your browser to
your-bank.com and impersonate you using your cookies, since the request is coming from your browser! Same-origin also
protects against dangerous DOM access

However, the stateless authentication methods we’ve discussed don’t use cookies. If no cookies are involved in the
authentication, you can safely enable cross-origin requests in your API using CORS (Cross-Origin Resource Sharing).

Public APIs are made to allow integration with external services, so enabling all origins access is part of their
nature. Check for your web framework documentation on how to enable CORS. But remember: don’t enable CORS for all
origins if you’re using any cookie-based authentication in your website. Also, if you have both a website version and a
API-based version of a service, a good idea is to host them in different subdomains or domains. The website should keep
same-origin policy active, while the API can have CORS enabled.

---

# Resource representations, Media Types, HATEOAS, and versioning.

In our previous examples, our bike rental API sent and received data formatted as vanilla JSON. By “vanilla” we mean
JSON as defined by the JSON standard: a simple format that supports collections of name/value pairs and arrays of
strings, numbers, booleans and nulls. But RESTful APIs can send and receive any data type chosen by the developer. REST
is not a protocol, it’s an architectural style. As such, it doesn’t limit the format of client-server interaction, i.e.,
the Media Type1. However, this flexibility is a double-edged sword. On the bright side, it gives applications liberty to
define the format that best suits them. On the other side, this results in a lack of standardization. Working with a new
RESTful API usually requires a bit of reading to understand how it accepts input data (the request, especially the query
parameters and the body) and returns output data (the response, especially the body).

For example, the bike sharing application we’ve seen in our previous articles has a stations resource. There are many
reasonable Media Types we can use to represent a list of stations.We’ve used vanilla JSON.But nothing prevents us from
using another Media Type, such as XML.

Additionally, there are multiple ways of representing data in a single Media Type. For example, latitude and longitude
can be shown as decimal degrees like [40.7, -74] or degrees, minutes and seconds like ["40 42 0", "-74 0 0"].

Earlier, we considered which data the API clients needed in request and responses. Now we’ll discuss how to represent
that data.

### User centered design for representations

When deciding the way to represent API resources, try to imagine how clients will consume them. There are two important
questions:

- Are the representation data fields in a format that’s easily processable by clients?
- Do these data fields precisely represent the resource state from the server?

### Make the representation easy to process

Remember the GET /rents/ request from the first article? Its response includes each active rent start_date in a format
like this:

`"2017-06-08T19:30:39+00:00"`

This is the ISO 8601 format for datetimes, which is a popular standardized format for representing dates as strings.
It’s used by GitHub API, which is considered a well implemented API. Since the JSON spec is silent about how datetimes
should be specified, web developers commonly use ISO 8601 for representing datetimes in JSON. Because it is a popular
standard, most frameworks and languages have built-in functions to parse ISO 8601 and get datetime objects, thereby
facilitating the work of API client. As always, there’s a relevant XKCD.

Now imagine if instead we represented start_date like this:

`"08/06/2017 19:30:39"`

This is a human readable format, but it isn’t standardized for machines to process. The API client programmer needs to
check the programming language docs to know which format specifiers it must use to process this format.

Another important example in GET /rents/ is how we represent rents that have no end_date:

`end_date: null`

Some APIs do this differently. They hide the empty field entirely, removing the end_date key from the JSON response
body. This is a bad practice. It makes the API less explorable, less self-descriptive, less predictable, and more
error-prone for clients.

### Make the representation precise

There are other problems with start_date: "08/06/2017 19:30:39". First, it is ambiguous. 08/06/2017 is August 6th in the
US. However, it’s June 8th in the UK and other countries. A badly implemented datetime parser might accept this format
and interpret it wrongly. Second, the format lacks potentially crucial data: there’s no time zone info. The API client
don’t know whether this date and time is in UTC or in the time zone where the bike rental service is operating. This
imprecision could be remediated with ISO 8601. Similar to the above problem is representing floating-point numbers. In
our bike rental API, we represent the latitude and longitude of a location like this:

`"location": [40.7, -74]`

However, it’s important to note that floating point arithmetic is not exact. When a processor performs the
floating-point addition 0.04 + 0.07, the result may be something like 0.11000000000000001. Such imprecise approximations
are fine for latitude and longitude calculations, but they’re definitely insufficient for financial data. In JSON, the
popular solution is to use strings of decimal numbers to represent exact currency values, for example:

`"price_in_usd": "19.90"`

Another solution is to use the smallest currency unit as an integer, which in dollars is cents. That’s what Stripe does.
The previous example would then look like:

`"price_in_usd_cents": 1990`

In JSON, besides floats versus integers, a common mistake is not using Booleans to represent true or false values. Don’t
use integers 1 or 0, or even worse, strings “1” or “0” to represent true or false. The string “0” is interpreted as
truthy in a lot of programming languages. If your API client uses it in a “if” statement, it might introduce a bug
that’s hard to catch. A bug like this might even happen on the server, if this bad data comes back to it via a request
body. Just use JSON Booleans, as we’ve did on the first article with the is_active field in GET /rents/.

### Sorting, filtering, paginating, linking with Media Types

Defining a Media Type for a RESTful API is more than choosing a data format like JSON or XML. It’s defining the whole
format of interactions between clients and servers, i.e., the structure of requests and responses. As Roy Fielding, REST
creator says, “a REST API should spend almost all of its descriptive effort in defining the Media Type(s) used for
representing resources and driving application state”. In practice this means we must define how our API Media Type
relates to features like sorting, filtering, paginating, and linking.

### Sorting and filtering

The most RESTful way to support sorting and filtering is using HTTP query parameters on each resource. The
sortable/filterable fields must match the names used in resource representations. This is how the resource
representation Media Type is related to sorting and filtering. So, when documenting your API Media Type, you must
document how it supports sorting and filtering.

In our Bike rental API, we can implement sorting and filtering for /stations/ as following:

Get the stations with the most available bikes first.

`GET /stations/?sort=-available_bikes_quantity`

Get the stations whose names contain Brooklyn.

`GET /stations/?name_contains=Brooklyn`

Get the stations with the most available bikes first, closest to the user and with names that do not contain the word
“Park”.

`GET /stations/?sort=-available_bikes_quantity,distance&name_not_contains=Park`

### Pagination and HATEOAS

What if our bike service had thousands of stations? We could return them all in a huge JSON list, but is a good idea?
Probably not. This would have a negative impact in the performance of our backend server and API clients. The solution
is pagination.

The most straightforward way to paginate API responses is to return the page count and leave it up to the API client to
specify the page through a query parameter. The response for a request like GET /stations/?page=2 looks like this:

```shell
{
    "page_number": 2,
    "total_pages": 102,
    "page_count": 10,
    "total_count": 1013,
    "results": [{
        "id": 1,
        "name": "John St",
        "location": [40.7, -74],
        "available_bikes_quantity": 10
    },
    {
        "id": 2,
        "name": "Brooklyn Bridge",
        "location": [40.7, -73],
        "available_bikes_quantity": 2
    }]
}
```

Note that the results are now enveloped in the JSON results key. Everything we had before in our response body is there,
but each request will have a part of the results since they’re paginated. To get the next page, the client would make
the request GET /stations/?page=3. And the last page of results is at the URL /stations/?page=102.

To update our example to use **HATEOAS**, we can send the following:

```shell
{
    "page_number": 2,
    "total_pages": 102,
    "total_count": 1013,
    "previous_page": "/stations/?page=1",
    "next_page": "/stations/?page=3",
    "last_page": "/stations/?page=102",
    "results": [
       …
    ]
}
```

Now the client doesn’t need to build URLs to get the previous, next, or last pages! HATEOAS makes our API easier to
consume, more explorable, and less error-prone. However, there’s a more RESTful alternative. As someone once said, HTTP
is your envelope already. You don’t need to add pagination metadata in the response body, you can use response headers!
That’s what the GitHub API does on its paginated responses. It uses the Link header to hold the pagination links.
Updating our example to be like GitHub API responses, we get this:

```shell
Status: 200 OK
Link:   </stations/?page=3>; rel="next",
        </stations/?page=1>; rel="previous",
        </stations?page=102>; rel="last"

[
    … results here … 
]
```

### Common Media Types for RESTful APIs

n the HTTP protocol, Media Types are specified with identifiers like `text/html, application/json, and application/xml,`
which correspond to HTML, JSON, and XML respectively, the most common web formats. There are other more API-specific
Media Types as well, such as `application/vnd.api+json`. This represents is JSON API, which we’ll cover later.

But web pages and APIs can render or respond much more than text. The contemporary web has images, audio, video, etc.
There are many Media Types for each of those things too, like `image/gif, audio/vorbis, video/mpeg`, etc.

### JSON API

JSON API defines conventions about how JSON should be structured in APIs. The GET /stations/ response would be formatted
like this in JSON API:

```json
{
  "links": {
    "prev": "/stations/?page=1",
    "self": "/stations/?page=2",
    "next": "/stations/?page=3",
    "last": "/stations/?page=102"
  },
  "meta": {
    "count": 1013
  },
  "data": [
    {
      "type": "station",
      "id": 1,
      "attributes": {
        "name": "John St",
        "location": [
          40.7,
          -74
        ],
        "available_bikes_quantity": 10
      },
      "relationships": {
        "bikes": {
          "links": {
            "related": "/stations/1/bikes/"
          }
        }
      },
      "links": {
        "self": "/stations/1/"
      }
    },
    {
      "type": "station",
      "id": 2,
      "attributes": {
        "name": "Brooklyn Bridge",
        "location": [
          40.7,
          -73
        ],
        "available_bikes_quantity": 2
      },
      "relationships": {
        "bikes": {
          "links": {
            "related": "/stations/2/bikes/"
          }
        }
      },
      "links": {
        "self": "/stations/2/"
      }
    }
  ]
}
```

As you can see, this Media Type is more verbose than vanilla JSON, but it’s also more organized. Links, identifiers,
data fields (attributes), and relationships are all properly separated, each of them in a standardized place. This
facilitates the implementation of generic tools to deal with APIs that use JSON API as Media Type. In fact, there are
many JSON API client and server libraries for most of the popular programming langagues.

Another great feature of JSON API is support for metadata on the top-level object, embedded resources or even links. The
available_bikes_quantity can be thought as metadata of the station bikes. Thus, instead of putting the
available_bikes_quantity inside attributes, we can move it next to the related bikes link:

```json
{
  "attributes": "...",
  "relationships": {
    "bikes": {
      "links": {
        "related": {
          "href": "/stations/2/bikes/",
          "meta": {
            "available_bikes_quantity": 2
          }
        }
      }
    }
  }
}
```

---

Summary:

- Noun
- Verb
- Form URLs
- Req res
- HttpStatus codes
- sorting and filtering
- pagination
- Json API













