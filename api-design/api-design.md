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

Now let’s see the process we follow to design our RESTful API.

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

### Status Codes: reporting what the request did

