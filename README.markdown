# The ShowClix API and Client Library #

This is the ShowClix API Client Library intended to facilitate access to the ShowClix RESTful API.  Documentation can be found at [The ShowClix Developer Site](http://developer.showclix.com) and below.

For private access, you must contact ShowClix to be setup with a Partner Account and have your CSR signed.  Certain data is provided to the public (e.g. basic event and venue information) and does not require authentication.  See [The ShowClix Developer Site](http://developer.showclix.com) for more.

# The ShowClix API #

## Introduction ##
The ShowClix API is intended to allow third parties developers to integrate with the ShowClix platform.  With the API, partners can fetch information about upcoming events and even add events, sellers, and venues to the system without ever opening a browser.  The API is intended to be easy to use and secure.  For these reasons, the API was built in a RESTful style and uses SSL Certificates for authentication.

The ShowClix API adheres closely to the principles behind the REST architecture style.  The API uses HTTP as its "uniform interface" and follows the HTTP 1.1 Standard, in particular when it comes to methods, status codes, and headers that promote caching and usability. 


> ## A Brief, High-Level REST Introduction ##
> A RESTful Web Service can be thought of as just Nouns and Verbs, or in more RESTful terms, Resources and Operations.  A RESTful Service has a group of things (aka Nouns, aka Resources) that we can do stuff to (aka we can apply Operations to these things/Resources).  Pretty simple idea.  These Resources and Operations have a few general principles: 
> 
> Resources:
> Universal Resource Identifiers (URIs)
> Resources must be universally identifiable through what is typically called a URI.  If the Resources/Nouns we were talking about were people, we could consider Social Security Number as the URI for our Resources (assuming the people were US Citizens and SSNs were actually unique).  Given a URI, I should be able to determine exactly what Resource is being referred to.  When talking about RESTful Web Services, URIs are almost always in the form of the familiar URL.  The ShowClix API follows this convention.  An example URI in the ShowClix API, https://www.showclix.com/rest.api/Event/1234, would identify a single event in the ShowClix system.
> 
> Representations
> Resources in RESTful Web Services are exchanged as representations.  When requesting a Resource in a RESTful Service, the client does not get a physical copy of that Resource (e.g. when requesting a venue, obviously ShowClix doesn't physically send you that venue).  Instead, the client would receive a representation describing that Resource.  In RESTful Web Services, this representation is text describing the resource, structured in the form of XML, JSON, YAML, RSS, Atom, etc..  The ShowClix API uses JSON formatted data for its representations.
> 
> Operations
> Uniform Interface
> Resources don't do us any good unless we can do things with them.  In the RESTful style of things, what we can do is limited to a defined set of operations that can be performed to all Resources.  This is commonly referred to as the uniform interface.  Going back to the Noun/Verb analogy, our vocabulary only contains a certain amount of verbs that were applicable to all Nouns.  In RESTful Web Services, this uniform interface is almost always HTTP.  HTTP defines four main operations: GET (retrieving data), PUT (editing data), POST (creating data), DELETE (removing data).  These are the only operations that can be applied to a Resource.  The ShowClix API uses HTTP 1.1 as its uniform interface.
> 
> RESTful Architectures follow the Client/Server design pattern.  This means that the client machine initiates all requests to a server machine that handles client's request.  The server then sends a response to the client once it has processed the request.  In standard HTTP, the server does not initiate any requests.
> 
> RESTful Architectures have a few other principles and perks (cachability, hyperlinking, statelessness, inherent scalability...) but this covers the main ideas.  To recap, in a RESTful Web Service, a client makes a request to a server to perform an operation on resource that is identified by a URI.  
> 
> If you're really interested in learning REST the right way (REST is a very abused term), check out the following:
> 
> * [Roy Fielding's Dissertation](http://www.ics.uci.edu/~fielding/pubs/dissertation/rest_arch_style.htm) that started the whole RESTful idea
> * [InfoQ](http://www.infoq.com) Excellent articles and lectures on RESTful Architectures
> * [RESTful Web Services by Sam Ruby](http://oreilly.com/catalog/9780596529260)



## Important Terms for the ShowClix API and This Documentation ##

### Class ###
Classes in the API correspond to a type of entity in the ShowClix domain.  Examples of Classes in the ShowClix API would be events, venues, sellers, and even less concrete things, such as a ticket sale.  The terms Class and "Resource Type" are used interchangeably in this API.

### Instance ###
Instance are a single instance of a particular class.  For example, when someone adds an event to the ShowClix system, they have just added an instance of the Event class.  In RESTful terms, an Instance is an example of a Resource.

### Client ###
The client in this documentation refers to the person/developer using the API.

### Partner ###
A partner in this documentation refers to a person/organization that has partnership with ShowClix to use the API.

### Seller ###
A seller in this documentation refers to the person/band/promoter that is selling tickets to a particular event thru ShowClix.

## URI Structure ##
URIs give us a way to identify Resources and a handle for interacting with data  from the API.  Notice that these identifiers (URIs) are also web accessible URLs (in fact the Web is really just a RESTful API of sorts itself... but that's for another lecture).  All URIs in the API are prefixed with "https://www.showclix.com/rest.api/".  There are several types of URIs: Class URIs, Instance URIs, Relationship URIs, Attribute URIs, and Search URIs.

### Instance URIs ###
e.g. https://www.showclix.com/rest.api/Event/1234
An instance URI corresponds to a single Instance in the ShowClix system.  The example URI would correspond to a single particular event.  Using this URI and HTTP (see HTTP section below) the client would be able to retrieve data about this particular event, edit details about this event, and in some cases remove this event altogether.

### Class URIs ###
e.g. https://www.showclix.com/rest.api/Event
A class URI corresponds to a single Class in the ShowClix domain.  Most commonly these URIs are used for creating new Resources.  The example URI above could be used to create a new Event.

### Relationship URIs ###
e.g. https://www.showclix.com/rest.api/Seller/123/events
It's typically good REST practice to expose relationships in your API.  One of the ways the ShowClix API does this is through relationship URIs (see Hyperlinking below for more).  The example URI above could be used to retrieve a list of all the events that belong to a single seller (identified by https://www.showclix.com/rest.api/Seller/123).  To promote what is known as hyperlinking and make API users aware of what relationships are available to them for a particular resource, representations will often include Relationship URIs in them (e.g. the representation for the Seller identified by https://www.showclix.com/rest.api/Seller/123 would include the https://www.showclix.com/rest.api/Seller/123/events Relationship URI along with several other Relationship URIs).

### Attribute URIs ###
e.g. https://www.showclix.com/rest.api/Event/1234/tickets_remaining
Attribute URIs expose additional information about a Resource.  Unlike the previous URIs, these URIs do not necessarily expose a Resource (or a collection of Resources like some Relationship URIs).  Instead they can expose scalar values or some other structured data.  The example URI above would return a single integer value determining how many tickets were still available for a particular event (identified by https://www.showclix.com/rest.api/Event/1234).

### Search URIs ###
e.g. https://www.showclix.com/rest.api/-/Event/event_start/GE/2009-10-20%2000%3A00%3A00
*Coming Soon*.  Search URIs provide a nice way to get filtered results.  They may look a little complex but are very easy to construct. The example URI would give a list of events that had an event start after October 29th, 2009 (notice all dates follow the SQL convention YYYY-MM-DD HH:MM:SS and criteria is URL encoded).
In general these URLs follow the following format:

    https://www/showclix.com/rest.api/-/<CLASS>/<ATTRIBUTE>/<OPERATOR>/<CRITERIA>

Supported Operators are:
EQ:  Equals
GT:  Greater than
GE:  Greater than or equal to
LT:  Less than
LE:  Less than or equal to

Additional Notes:
Criteria must be URL encoded


## HTTP ##
HTTP is what drives the ShowClix REST API (and the web in general).  The ShowClix API adheres to the HTTP 1.1 Standard.  Visit http://www.w3.org/Protocols/rfc2616/rfc2616.html for more information on HTTP from the guys that created it.

### Methods ###
The API currently supports the following HTTP methods GET, PUT, POST, DELETE, OPTIONS, HEAD.  Below describes the methods' purposes very briefly and give examples (using the CLI version of cURL for making the HTTP/HTTPS requests).  The key and cert parameters are used only for private access to the API for client authentication.  See the Public vs. Private and Authentication sections below for more details on this topic.

### GET ###
GET is by far the most common HTTP operation.  It tells the API to fetch a representation from the URI requested and return it to the client.  For example, sending a GET request to the URI https://www.showclix.com/rest.api/Event/1234 (see Resource URIs above), would return a JSON formatted representation to the API user.

Example:
Get information about a particular event in the system.  This is a request to a Resource URI (define above), http://www.showclix.com/rest.api/Event/4444.

cURL Example:
    curl -v http://www.showclix.com/rest.api/Event/6454
    
    GET /rest.api/Event/6454 HTTP/1.1
    User-Agent: curl/7.19.5 (i386-apple-darwin8.11.1) libcurl/7.19.5 OpenSSL/0.9.7l zlib/1.2.3 libidn/1.15
    Host: www.showclix.com
    Accept: */*
    
    HTTP/1.1 200 OK
    Date: Mon, 16 Nov 2009 15:23:34 GMT
    Server: Apache/2.0.63 (Unix) PHP/5.2.10 DAV/2 mod_ssl/2.0.63 OpenSSL/0.9.7l
    X-Powered-By: PHP/5.2.10
    Set-Cookie: PHPSESSID=703774fbda6aac85c3e624d3a198e938; path=/
    Expires: Thu, 19 Nov 1981 08:52:00 GMT
    Cache-Control: no-store, no-cache, must-revalidate, post-check=0, pre-check=0
    Pragma: no-cache
    ETag: f536d3bf14fa84e1fd9446f25172da44
    Content-Length: 441
    Content-Type: text/javascript
    
    
    {"event_id":"6454","event":"Event Title","ages":"0","genre":"Alternative","date_added":"2009-09-19 14:39:00","date_edited":null,"sales_open":"2009-09-19 14:39:00","event_start":"2009-11-08 18:30:00","event_end":"0000-00-00 00:00:00","price_levels":"https:\/\/www.showclix.com\/rest.api\/Event\/6454\/price_levels","venue":"https:\/\/www.showclix.com\/rest.api\/Event\/6454\/venue","seller":"https:\/\/www.showclix.com\/rest.api\/Event\/6454\/seller"}


### PUT ###
PUT requests allow the client to edit existing Resources.  A PUT request to update a Resource should contain the modified version of the Resource in the body of the request (currently on JSON formatted request bodies are supported) directed to the URI of the resource the client wishes to update.  As a side note, most calls in the ShowClix API that are "not safe" (e.g. effect the state of a resource) do require authentication.  See the Authentication and Public vs. Private Sections below for more details.

Example:
Let's go ahead an change the time of our event that we just pulled (this assumes we are the owner of this event and have done the authentication steps provided in the Authentication Section).

cURL Example:

    curl -v -s -k --key private.key --cert certificate.crt -X PUT -d '{"event_id":"6454","seller_id":"678","venue_id":"842","event":"My New Event Title","description":"event desc","inventory":"600","private_event":"0","price":"18.00","price_label":"General Admission","price_limit":"1","ticket_purchase_timelimit":null,"ticket_purchase_limit":null,"will_call_ticketing":null,"ages":"0","image":"20091252630916.jpg","url":"http:\/\/www.jokerprod.com","event_type":"3","ticket_note":null,"genre":"Alternative","status":"5","scheme_id":null,"keywords":null,"sales_open":"2009-09-19 14:39:00","event_start":"2009-11-08 18:30:00","event_end":"0000-00-00 00:00:00","short_name":"","parent":null,"display_image":"1"}' https://www.showclix.com/rest.api/Event/6454
    
    PUT /rest.api/Event/6454 HTTP/1.1
    User-Agent: curl/7.19.5 (i386-apple-darwin8.11.1) libcurl/7.19.5 OpenSSL/0.9.7l zlib/1.2.3 libidn/1.15
    Host: www.showclix.com
    Accept: */*
    Content-Length: 639
    Content-Type: text/javascript
    
    
    HTTP/1.1 200 OK
    Date: Mon, 16 Nov 2009 15:27:48 GMT
    Server: Apache/2.0.63 (Unix) PHP/5.2.10 DAV/2 mod_ssl/2.0.63 OpenSSL/0.9.7l
    X-Powered-By: PHP/5.2.10
    Expires: Thu, 19 Nov 1981 08:52:00 GMT
    Cache-Control: no-store, no-cache, must-revalidate, post-check=0, pre-check=0
    Pragma: no-cache
    ETag: 51a9f2bd1c7aa0e29f9680774888a4ab
    Content-Length: 737
    Content-Type: text/javascript
    
    
    {"event_id":"6454","seller_id":"678","venue_id":"842","event":"My New Event Title","description":"event desc","inventory":"600","private_event":"0","price":"18.00","price_label":"General Admission","price_limit":"1","ticket_purchase_timelimit":null,"ticket_purchase_limit":null,"will_call_ticketing":null,"ages":"0","url":"http:\/\/www.jokerprod.com","event_type":"3","ticket_note":null,"views":"0","genre":"Alternative","status":"5","newsletter_sent":"0","donation_live":"n","donation_name":"","date_added":"2009-09-19 14:39:00","date_edited":null,"scheme_id":null,"keywords":null,"sales_open":"2009-09-19 14:39:00","event_start":"2009-11-08 18:30:00","event_end":"0000-00-00 00:00:00","short_name":"","parent":null,"display_image":"1"}


### POST ###
POST requests allow the client to create new Resources.  A POST request to create a new Instance should contain the representation of the Instance the client wishes to create (currently JSON formatted only) POSTed to the Class URI (e.g. https://www.showclix.com/rest.api/Event).  On successful POSTs, the client should receive a 201 Created response and the Location header will be set with the full URI of the newly created Resource.

cURL Example:

    curl -v -s -k --key private.key --cert certificate.crt -X POST -d '{"seller_id":"678","venue_id":"842","event":"Event Title","description":"event desc","inventory":"600","private_event":"0","price":"18.00","price_label":"General Admission","price_limit":"1","ticket_purchase_timelimit":null,"ticket_purchase_limit":null,"will_call_ticketing":null,"ages":"0","image":"20091252630916.jpg","url":"http:\/\/www.jokerprod.com","event_type":"3","ticket_note":null,"genre":"Alternative","status":"5","scheme_id":null,"keywords":null,"sales_open":"2009-09-19 14:39:00","event_start":"2009-11-08 18:30:00","event_end":"0000-00-00 00:00:00","short_name":"","parent":null,"display_image":"1"}' https://www.showclix.com/rest.api/Event
    
    POST /rest.api/Event HTTP/1.1
    User-Agent: curl/7.19.5 (i386-apple-darwin8.11.1) libcurl/7.19.5 OpenSSL/0.9.7l zlib/1.2.3 libidn/1.15
    Host: www.showclix.com
    Accept: */*
    Content-Length: 614
    Content-Type: text/javascript
    
    
    HTTP/1.1 201 Created
    Date: Mon, 16 Nov 2009 15:20:17 GMT
    Server: Apache/2.0.63 (Unix) PHP/5.2.10 DAV/2 mod_ssl/2.0.63 OpenSSL/0.9.7l
    X-Powered-By: PHP/5.2.10
    Expires: Thu, 19 Nov 1981 08:52:00 GMT
    Cache-Control: no-store, no-cache, must-revalidate, post-check=0, pre-check=0
    Pragma: no-cache
    ETag: dcca48101505dd86b703689a604fe3c4
    Location: https://www.showclix.com/rest.api/Event/6454/
    Content-Length: 0
    Content-Type: text/javascript



### DELETE ###
DELETE requests allow the client to remove Resources.  A DELETE request to a Resource URI will remove that resource from the system and return the representation of that resource that was deleted.

cURL Example:

    curl -v -s -k --key private.key --cert certificate.crt -X DELETE https://www.showclix.com/rest.api/Event/6454
    
    DELETE /rest.api/Event/6454 HTTP/1.1
    User-Agent: curl/7.19.5 (i386-apple-darwin8.11.1) libcurl/7.19.5 OpenSSL/0.9.7l zlib/1.2.3 libidn/1.15
    Host: www.showclix.com
    Accept: */*
    
    
    HTTP/1.1 200 OK
    Date: Mon, 16 Nov 2009 15:35:53 GMT
    Server: Apache/2.0.63 (Unix) PHP/5.2.10 DAV/2 mod_ssl/2.0.63 OpenSSL/0.9.7l
    X-Powered-By: PHP/5.2.10
    Expires: Thu, 19 Nov 1981 08:52:00 GMT
    Cache-Control: no-store, no-cache, must-revalidate, post-check=0, pre-check=0
    Pragma: no-cache
    ETag: 51a9f2bd1c7aa0e29f9680774888a4ab
    Content-Length: 737
    Content-Type: text/javascript
    
    
    {"event_id":"6454","seller_id":"678","venue_id":"842","event":"My New Event Title","description":"event desc","inventory":"600","private_event":"0","price":"18.00","price_label":"General Admission","price_limit":"1","ticket_purchase_timelimit":null,"ticket_purchase_limit":null,"will_call_ticketing":null,"ages":"0","url":"http:\/\/www.jokerprod.com","event_type":"3","ticket_note":null,"views":"0","genre":"Alternative","status":"5","newsletter_sent":"0","donation_live":"n","donation_name":"","date_added":"2009-09-19 14:39:00","date_edited":null,"scheme_id":null,"keywords":null,"sales_open":"2009-09-19 14:39:00","event_start":"2009-11-08 18:30:00","event_end":"0000-00-00 00:00:00","short_name":"","parent":null,"display_image":"1"}


### HEAD ###
The HEAD request is almost identical to the GET request, with the exception that it only returns the headers for that HTTP request rather than the headers and representation.

cURL Example:

    curl -v -X HEAD http://www.showclix.com/rest.api/Event/6456
    
    HEAD /rest.api/Event/6456 HTTP/1.1
    User-Agent: curl/7.19.5 (i386-apple-darwin8.11.1) libcurl/7.19.5 OpenSSL/0.9.7l zlib/1.2.3 libidn/1.15
    Host: www.showclix.com
    Accept: */*
    
    
    HTTP/1.1 200 OK
    Date: Mon, 16 Nov 2009 15:39:42 GMT
    Server: Apache/2.0.63 (Unix) PHP/5.2.10 DAV/2 mod_ssl/2.0.63 OpenSSL/0.9.7l
    X-Powered-By: PHP/5.2.10
    Expires: Thu, 19 Nov 1981 08:52:00 GMT
    Cache-Control: no-store, no-cache, must-revalidate, post-check=0, pre-check=0
    Pragma: no-cache
    Content-Length: 0
    Content-Type: text/plain



### OPTIONS ###
The OPTIONS request lets a client know what they are permitted to do.  It sets this via the "Allow" header set with a list of allowed methods.

cURL Example:

    curl -v -s -k --key private.key --cert certificate.crt -X OPTIONS http://www.showclix.com/rest.api/Event/6456
    
    OPTIONS /rest.api/Event/6456 HTTP/1.1
    User-Agent: curl/7.19.5 (i386-apple-darwin8.11.1) libcurl/7.19.5 OpenSSL/0.9.7l zlib/1.2.3 libidn/1.15
    Host: www.showclix.com
    Accept: */*
    
    
    HTTP/1.1 200 OK
    Date: Mon, 16 Nov 2009 15:41:18 GMT
    Server: Apache/2.0.63 (Unix) PHP/5.2.10 DAV/2 mod_ssl/2.0.63 OpenSSL/0.9.7l
    X-Powered-By: PHP/5.2.10
    Expires: Thu, 19 Nov 1981 08:52:00 GMT
    Cache-Control: no-store, no-cache, must-revalidate, post-check=0, pre-check=0
    Pragma: no-cache
    Allow: GET,PUT,DELETE
    Content-Length: 0
    Content-Type: text/plain



## Status Codes ##
ShowClix uses HTTP standards for reporting request status and making the client aware of errors.

### 200 "HTTP/1.1 200 OK" ###
Probably the most common response code.  Lets the client know that the request went as expected.

### 201 "HTTP/1.1 201 Created" ###
Lets the client know a resource was create.  Only seen after a successful POST to create a new resource and typically accompanied with the "Location" header to let the client know where the newly created resource is located.

### 204 "HTTP/1.1 204 No Content" ###
Client's request has no body.  Sent with HEAD responses and any other requests that contain no content.

### 304 "HTTP/1.1 304 Not Modified" ###
Sent to let the client know that the resource their requesting has not been modified since their last request allowing for caching.

### 400 "HTTP/1.1 400 Bad Request" ###
The client's request was invalid.  Most likely the server couldn't understand the client's request as a result of a malformed URI or request body.

### 401 "HTTP/1.1 401 Unauthorized" ###
The client is not authorized to access the requested resource.

### 403 "HTTP/1.1 403 Forbidden" ###
The requested resource is forbidden through the API.

### 404 "HTTP/1.1 404 Not Found" ###
Probably the second most famous Status Code.  Lets the client know that the resource requested does not exist.

### 405 "HTTP/1.1 405 Method Not Allowed" ###
The client attempted a method that is not supported in the API for their request.

### 500 "HTTP/1.1 500 Internal Server Error" ###
There was an error on the server.  If you receive this error frequently, contact ShowClix.


## Errors ##
In addition to sending the appropriate status code response, the API will also send a brief error JSON response with a bit more details.  For example, the following could be a response when attempting to create a resource and forgetting required attributes.

    {"status":"400","response":"HTTP/1.1 400 Bad Request","details":"Phone is required, Email is required"}



## Hyperlinking ##
The ShowClix REST API promotes hyperlinking in resources representations.  This helps naturally expose the API and relationships among Resources.  When you find another URI in a representation, this typically means that it is hyperlinking to another resource in ShowClix.  Consider the Seller representation below.  Notice that the last attribute, event, has a URI for its value.  This actually links to another representation, a list of all events that belong to this seller.

    {"seller_id":"6","first_name":"Joe","organization":"Showclix","last_name":"Schmoe","events":"https:\/\/www.showclix.com\/rest.api\/Seller\/6/\events"}


## Public vs. Private ##
The ShowClix REST API supports two access levels, Public and Private.  Public access to the API does not require SSL authentication and does not require that the client accessing the data is the owner of the API.  As a result, the public access to the API is very limited and can only perform safe operations (e.g. GET, OPTIONS, HEAD) at the moment.  For partners that are interested in just retrieving basic event and venue data from the system, public access to the API works just fine.  Private access to the API allows for tighter integration (e.g. adding sellers and events to the system).  Private access requires that you use a signed SSL Certificate (provided by ShowClix) for authentication.  With this SSL certificate, the client can now add new Resources to the system and manipulate those Resources that the client owns.  A client can only manipulate resources that they directly own and can create relationships between resources that they own.  For example, a client cannot add an event to the system under a seller that they don't own.


## Authentication ##
The API uses SSL Client Certificates for authentication and private access to the API.  ShowClix can provide clients with a private key and signed certificate or partners can create a Certificate Signing Request for ShowClix to sign if that is preferred.  Here is an example using the CLI for the popular cURL library.

    curl -i -X GET --key private.key --cert public.crt https://www.showclix.com/rest.api/Event/1234


## Abuse ##
ShowClix tracks both public and private use of the API.  If we suspect a client is abusing the the API, ShowClix has the right to throttle or entirely revoke a clients access to the API.


## Additional Features ##

The "follows" Parameter
The API allows for a follows parameter to be passed in as a query string value to follow relationships and embed relationships representations right within a representation.  By default, relationships are included in representations are URIs themselves.  For example an Event JSON representation defined by the URI http://www.showclix.com/rest.api/Event/6456 would typically look like...

    {"event_id":"6456","event":"Event Title","ages":"0","genre":"Alternative","date_added":"2009-11-16 10:38:35","date_edited":null,"sales_open":"2009-09-19 14:39:00","event_start":"2009-11-08 18:30:00","event_end":"0000-00-00 00:00:00","price_levels":"https:\/\/www.showclix\/rest.api\/Event\/6456\/price_levels","venue":"https:\/\/www.showclix\/rest.api\/Event\/6456\/venue","seller":"https:\/\/www.showclix\/rest.api\/Event\/6456\/seller"}

Notice that the values for the relationships in this representation (e.g. price_levels, venue, and seller) are set as URIs, more specifically what the ShowClix API refers to as Relationship URIs.  GETting any of these URIs will return a representation of the Resource identified by that relationship.  In the case of price levels (a relationships that would be considered a "One To Many" relationship) the returned representation would be a JSON formatted array/list of JSON encoded PriceLevel Instance representations. In the case of the venue relationships (a relationship that would be considered a "Many To One" relationship) the Relationships URI, http://www.showclix/rest.api/Event/6456/venue,  would return the JSON representation of that Venue Instance (the same representation available at the URI, http://www.showclix/rest.api/Venue/842). 

The client may desire to actually have these representations embedded right in place for these relationship values, rather than having to perform an additional lookup.  To do this, the client can specify the relationship as a value for the query parameter follows.  For example, if the client wanted to include details about the venue in this event representation, the client would GET http://www.showclix/rest.api/Event/6456?follow[]=venue, returning a new representation with a venue representation embedded right in it...

    {"event_id":"6456","event":"Event Title","ages":"0","genre":"Alternative","date_added":"2009-11-16 10:38:35","date_edited":null,"sales_open":"2009-09-19 14:39:00","event_start":"2009-11-08 18:30:00","event_end":"0000-00-00 00:00:00","price_levels":"https:\/\/www.showclix\/rest.api\/Event\/6456\/price_levels","venue":{"venue_name":"The REST Venue","capacity":"999","description":"Test...","image":null,"seating_chart":null,"address":"123 Seasame St.","city":"Pittsburgh","state":"PA","zip":"12345","country":"USA","lat":"-73.9403000","lng":"42.8145000"},"seller":"https:\/\/www.showclix\/rest.api\/Event\/6456\/seller"}

Extensions
The API does allow for extensions to be placed in the URIs.  This will dictate which format your representation will be in.  For example http://www.showclix.com/rest.api/Event/1234.json would return a JSON formatted representation of this Event Instance, whereas http://www.showclix.com/rest.api/Event/1234.xml would return the same Event Instance formatted in XML.  At this time, only JSON is supported, however XML and YAML support is expected soon.  Presently when an extension is left of the default format (JSON) is assumed.


## Client Library ##

ShowClix provides developers with a one-file library, written in php, in order to ease development of web applications based on the ShowClix RESTful API. It is contained in the rest.php file found in the developer package, and consists of a single class, Server, which has the following useful methods:

Constructor:
The constructor takes a single associative array as its parameter. Possible keys are:

* 'protocol' -- Either 'http' or 'https'
* 'base_url' -- If you don't have explicit directions on how to change this, don't
* 'clientcert' -- full path to the client certificate that ShowClix signed. This doesn't have to be present if the 'protocol' is 'http'
* 'clientkey' -- full path to the client key used to create the client certificate signing request you sent to ShowClix. The key should be in PEM format, and should not have a password.
* 'verifypeer' -- usually used in conjunction with 'base_url' -- if you weren't directed to set it, then you shouldn't need to touch it.


build_url($rest)
Generates a url for use with the rest of the framework with the protocl and base_url appropriately prepended to $rest. Example:

* $server->build_url('/Event/1234'); // By default: 'http://www.showclix.com/rest.api/Event/1234'


build_uri($info)
Takes the same thing as the first argument to get_resource, and turns it into a URI, if neccessary.

extract_from_uri($info)
Returns an array where the first element is an Resource name, and the second element is an Resource ID that correspond to the given URI

NOTE: For the purposes of get_resource, modify_resource and delete_resource, the first argument is either a URI obtained from some server output, or it's an associative array with the keys:

* 'entity' -- the name of the Resource with which you wish to deal (i.e. 'Event', 'Seller', etc.)
* 'id' -- the id of the Resource you're accessing.


get_resource($entityid_or_uri, $verbose=false)
Retrieves the given resource, returning it as an object.

modify_resource($entityid_or_uri, $modifications, $verbose=false)
Modifies the specified resource, only updating the keys mentioned in $modifications with the corresponding values.

delete_resource($entityid_or_uri, $verbose=false)
Deletes the given resource.

create_resource($entity, $initial, $verbose=false)
Creates an Entity of the given type using initial values drawn from $initial