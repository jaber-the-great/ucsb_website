# HTTP
.fx: title

__CS291A__

Dr. Bryce Boe

September 27, 2016

---

# In the News

![The need for mobile speed](img/2016_mobile_speed.png)

[Source](https://www.doubleclickbygoogle.com/articles/mobile-speed-matters/)

---

# Today's Agenda

* TODO
* The Life Cycle of a Web Request
* HTTP Requests and Responses
* HTTP Performance (HPBN, chapters 9-11)

---

# TO-DO

## Should be done

* Join the class on [Piazza](https://piazza.com/class/idgkoaxbvg14lx)
* Complete the form:
  [https://goo.gl/forms/CF1CIeTSymIuMbO73](https://goo.gl/forms/CF1CIeTSymIuMbO73)
  (people will be dropped who haven't completed it)
* Read chapters 1 and 2 in
  [High Performance Browser Networking](https://hpbn.co/primer-on-latency-and-bandwidth/)


## Before Tomorrow

* Read the list of project ideas: [http://cs290.com/project_ideas/](http://cs290.com/project_ideas/)
* Post or comment on at least one idea on Piazza under the `project_idea`
  "folder"
* Complete the [Ruby Code Academy](https://www.codecademy.com/tracks/ruby)
* Complete chapter 1 in the
  [Ruby on Rails Tutorial](https://www.railstutorial.org/book/beginning)
* Begin chapter 2 in the
  [Ruby on Rails Tutorial](https://www.railstutorial.org/book/toy_app)

---

# The Life Cycle of a Web Request

---

# Review: The Two Endpoint Basics

A web browser is a process (at least one) that runs on an operating system. It:

* responds to user input
* renders the display
* utilizes the network

A web server is a process (at least one) that runs on an operating system. It:

* responds to network requests
* loads resources that may come from file system, database, other servers

---

# Core Components of a Web request

* Web server: Opens a TCP socket to listen for requests
* Browser: Makes a DNS query to obtain an IP address for www.reddit.com
* Browser: Establishes a TCP connection to the IP address
* Web server: Accepts the TCP connection
* Web server: Adds a TLS context to the TCP connection
* Browser: Wraps a TLS session on-top of the TCP connection
* Browser: Sends an HTTP request over the TLS session
* Web server: Parses the request, fetches and sends the requested resources

---

# HTTP

---

# Chrome Network Table

By the end of today's lecture, you should understand this output:

![Chrome Network Table](img/chrome_network_table.png)

---

# History

The year is 1990. The Internet has existed for ~20 years, email for ~8.

Tim Berners-Lee has the idea to combine __HyperText__ and the __Internet__. He
creates the first version of __HTTP__ and __HTML__.

__HyperText__: Links documents together via __HyperLinks__

* 1993: Mosaic, the first web browser, is created at UIUC
* 1994: Marc Andreessen leaves UIUC and founds Netscape with Jim Clark

---

# The First Web Server

![The First Web Server](img/first_web_server.png)

---

# HTTP Explained

It its original, simplest version:

* Open a TCP socket (standard port is 80)
* Send an ASCII request for a resource (`GET mypage.html`)
* Response comes back containing only the content of `mypage.html`
* Close the TCP socket

HTTP was originally designed to:

* Send HTML (was later extended to send anything)
* To facilitate interaction between a web browser and a web server (now also
  used heavily for server-to-server communication)

---

# HTTP Request/Response Components

## HTTP Request

* __Verb__: What do you want to do (e.g., __GET__, __POST__)
* __Resource__: What is the logical path of the resource you want
* __HTTP Version__: What version of HTTP are you using?
* __Headers__: Standard way to specify or request optional behavior
* __Body__: Content that is being sent to the server (e.g., a file upload)

## HTTP Response

* __HTTP Version__: Indicates the HTTP version the server is "speaking"
* __Status Code__: Indicates success, failure, or other possible response
  states
* __Headers__: Provides meta data about the response.
* __Body__: The primary payload of the response (for a successful _GET_
request, this contains the resource content)

---

# HTTP Components Example

![Components of HTTP request and response](img/http_components.png)

---

# HTTP Verbs GET and POST

## GET

* Request a copy of a _resource_
* The request should have no side-effects (i.e., doesn't change server state)
* __GOOD__: `GET /fluffy_kitty.jpg`
* __BAD__: `GET /users/sign_out`

## POST

* Sends data to the server
* Generally used to create a _resource_
* Has side-effects (e.g., creates a resource)
* Not idempotent (i.e., making the same request twice creates two
  separate, but similar resources)
    * "Do you want to submit your form again?"

---

# HTTP Verbs PUT and DELETE

## PUT

* Sends data to the server
* Often used to update an existing resource
* Has side-effects
* Should be idempotent (e.g., updating a resource twice should result in the
  same effect to the resource)

## DELETE

* Destroys a resource
* Has side effects
* Should be idempotent
* __GOOD__: `DELETE /session/<id>` (for log out)

---

# HTTP Verb HEAD

## HEAD

* Exactly like GET but excludes the body in the response

> What can HTTP HEAD be used for?

---

# HTTP Verb Misuse

Correct HTTP verb usage is not enforced, and is often misused.

> What do you think the following is supposed to do?

`GET /post/5?action=hide`

> What other problem do you think can occur with the misuse of HTTP verbs?

---

# Other HTTP Verbs

* TRACE
* OPTIONS
* CONNECT

---

# HTTP Resource

Specifies a logical hierarchy to access a resource:

* __GOOD__: /gp/product/1565925092/
* __BAD__: /index.jsp?page_id=4251

## Query String

* The portion of the resource after the _question mark_
* Used to assist in locating the resource
* Values are assigned using the equal token (e.g., `id=15`)
* Multiple values can be concatenated via ampersand (`&`)

Example:

http://www.reddit.com/r/ucsantabarbara/?__sort=top&t=all__

---

# HTTP Version

Version strings are often used in protocols to make it easy to evolve the
protocol.

With HTTP different versions have different behavior:

* (1991) HTTP 0.9 (retroactively versioned): Single line protocol with no
  headers. Only GET supported: `GET index.html`
* (1996) HTTP 1.0: Added headers, and a version string
* (1999) HTTP 1.1: Connection keep-alive by default, additional caching
  mechanisms. __Primary HTTP version used today__
* (2015) HTTP 2.0: Binary framing, header compression, many other
  optimizations. Discussed in more detail in a future lecture

---

# HTTP Headers

Provides metadata for the request and response.

> What HTTP headers do you know of?


# HTTP Headers: Accept

Indicates the format of the resource

`Accept: text/html`

I desire the resource in html format.

`Accept: application/json`

I desire the resource as a JSON document.

`Accept application/json,application/xml`

I prefer a JSON document, but if you cannot do that then an XML document will
do.

---

# HTTP Headers: Accept-*

## Accept-Encoding

Indicates preferred encoding for the response body.

`Accept-Encoding: bzip2,gzip`

I prefer the data compressed via bzip2. If that cannot be done, please gzip the
response.

## Accept-Language

Indicates the preferred language for the resource

`Accept-Language: es,en-US`

I prefer Spanish, but will accept US-English.

---

# HTTP Headers: Host, User-Agent

## Host (required in HTTP 1.1 request)

Indicates the DNS hostname associated with the desired resource.

> Why is the HOST header required?

## User-Agent

Indicates information about the client (web browser, crawler, tool) to the web
server.

`User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_10_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/45.0.2454.99 Safari/537.36`

Can be used to serve different content to different clients. However, try to
avoid doing so.

---

# HTTP Headers: Set-Cookie, Cookie

## Set-Cookie

A response header informing the browser to use the provided cookies in
subsequent requests to the server.

## Cookie

A request header containing the data previously set by the server via a
`Set-Cookie` header.

> What kind of information might one put in a cookie?

> What security concerns may exist with cookies?


---

# Other HTTP Headers

## Caching Related Headers

* ETag
* Date
* Last-Modified
* Cache-Control
* Age

## Security Related Headers

* Content-Security-Policy
* Strict-Transport-Security
* X-Frame-Options

`X-` prefixed headers are not part of the official specification and may later
become _standardized_.


---

# HTTP Statuses

The HTTP response status indicates the outcome of the request. Status codes
fall into one of five categories:

* 1XX - Informational
* 2XX - Successful
* 3XX - Redirection
* 4XX - Client Error
* 5XX - Server Error

Ref: [HTTP Status Code Definitions](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)


---

# Common HTTP Statuses

* __200 OK__: The requested resource is being returned.
* __301 Moved Permanently__: The resource has been moved and the browser should
  always use the new URL provided via the `Location: http://...` header.
* __302 Found__: The resource can be found at another location.
* __304 Not Modified__: Useful with `HEAD` requests containing a cache header
  (e.g., `If-Modified-Since`) to see if a `GET` request is needed.
* __403 Forbidden__: The request is not authorized to access the resource.
* __404 Not Found__: The resource does not exist.
* __500 Internal Server Error__: Something crashed on the server.
* __503 Service Unavailable__: Temporary failure on the server-side.

---

# HTTP Request Body

HTTP PUT and POST requests typically have a body associated with them. HTML
_form_ elements usually result in a POST request with a `x-www-form-urlencoded`
type.

Example:

`curl http://httpbin.org/post --data 'username=bboe&comment=Hi There' -v`

    !http
    POST /post HTTP/1.1
    Host: httpbin.org
    User-Agent: curl/7.43.0
    Accept: */*
    Content-Length: 30
    Content-Type: application/x-www-form-urlencoded

    username=bboe&comment=Hi There

---

# HTTP Response Body

The body of a response usually contains the requested resource content.

    !http
    HTTP/1.1 200 OK
    Content-Type: text/html; charset=utf-8
    Content-Length: 9973

    <!DOCTYPE html>
    <html lang="en">
    ...
    </html>

---

# Try it! Direct Input Server

## Listen for a TCP connection

`nc -l localhost 4000`

## Browse to: [http://localhost:4000](http://localhost:4000)

## Paste the following into your terminal:


    !http
    HTTP/1.1 200 OK
    Content-Type: text/html; charset=utf-8
    Content-Length: 106

    <!DOCTYPE html>
    <html lang="en">
    <h1>HTML is easy!</h1>
    <img src="http://i.imgur.com/oXxTj5g.gif">
    </html>

---

# HTTP Performance

Prior to HTTP 1.1, one TCP connection was used for a single HTTP session. Thus
1 request per connection.

> What performance implications does having 1 request per session have?

---

# TCP Connection Delay

Establishing a TCP connection requires 1 round-trip.

![TCP round trip visualized](img/tcp_round_trip.png)

_Image Source: “High Performance Browser Networking,” by Ilya Grigorik_

---

# Slow Start and Congestion Avoidance

The early phases of a TCP connection are bandwidth constrained.

![TCP congestion mechanisms](img/tcp_congestion.png)

_Image Source: “High Performance Browser Networking,” by Ilya Grigorik_

---

# TCP Congestion Window Size (cwnd)

It takes a fair amount of time to get up-to-speed. Making new connections per
HTTP request is not terribly efficient.

![TCP connection window size](img/tcp_cwnd.png)

_Image Source: “High Performance Browser Networking,” by Ilya Grigorik_

---

# HTTP Keep-Alive

HTTP 1.1 officially added support for the `Connection` header most commonly
used as:

`Connection: keep-alive`

In fact, with HTTP 1.1, the default is `keep-alive`. The alternative, and the
way to signal the end of the HTTP session is:

`Connection: close`

With a keep-alive HTTP session, the server waits some amount of time for
an additional request after processing the most recent request.

---

# HTTP Keep-Alive Session

![HTTP Session with Two Requests](img/http_session.png)

_Image Source: “High Performance Browser Networking,” by Ilya Grigorik_

---

# HTTP Pipelining

With HTTP keep-alive, we can make multiple requests on a single TCP connection. Success!

But, we're still waiting for the current response before we can issue the next
request. Why wait?

---

# HTTP Pipelining Session

![HTTP Session with Two Requests](img/http_pipelining.png)

_Image Source: “High Performance Browser Networking,” by Ilya Grigorik_

---

# Think About it

> What sort of issues might occur with HTTP pipelining?

---

# Pipelining Issues

## Head of Line Blocking

Head of line blocking means that a request that takes a long time is blocking
another request from occurring.

## Extra work for the server

Pipeling may require the server to buffer future responses while blocked on the
head of the line. These extra resources can exhaust the server.

Furthermore, if an error occurs the server may end up doing the same _work_
twice.

## Adoption

Many intermediaries (proxies, caches) simply do not support HTTP pipelining thus
making the feature less appealing.

---

# More Speed

A single web page may have tens of resources. In practice obtaining each
resource serially over the same TCP connection is too slow.

> What can be done to get more speed?

---

# Concurrent HTTP Sessions

Most browsers will open up to __six__ concurrent connections to the same
server.

![Concurrent HTTP Sessions](img/concurrent_http.png)

_Image Source: “High Performance Browser Networking,” by Ilya Grigorik_

---

# Even more speed

According to HTTP Archive the average number of resources for websites they
crawled is ~100. With up to six connections each HTTP session must fetch
approximately 16 resources. With head-of-line blocking this may still be too
slow.

> What can we do?

---

# Domain Sharding

_Domain sharding_ is the process of separating resources to different
domains, e.g., i.ytimg.com, s.ytimg.com.

The web browser will make up to 6 connections for each domain.

Reduces page load time for some work-loads (test to see if it's right for you).

---

# Hacks that work

Concurrent sessions and domain sharding are hacks to get more performance out
of an existing system. Using these hacks makes deployment more complicated.

## Other Performance Related Hacks

* CSS/JS concatenation and minimization
* Image spriting

## The Future is Here!

In a future lecture we'll talk about how HTTP 2.0 obviates all of these hacks.

---

# Wednesday's Lab

Bring your laptop, with the battery fully charged.

## Reminder: Before Tomorrow

* Read the list of project ideas: [http://cs290.com/project_ideas/](http://cs290.com/project_ideas/)
* Post or comment on at least one idea on Piazza under the `project_idea`
  "folder"
* Complete the [Ruby Code Academy](https://www.codecademy.com/tracks/ruby)
* Complete chapter 1 in the
  [Ruby on Rails Tutorial](https://www.railstutorial.org/book/beginning)
* Begin chapter 2 in the
  [Ruby on Rails Tutorial](https://www.railstutorial.org/book/toy_app)