[RESTful Best Practices](Home)

7.1 Introduction
----------------

A long-running operation is an API call that is called using an
asynchronous pattern primarily to avoid the technical drawbacks of a
synchronous call. Synchronous calls are susceptible to network
instability or timeouts that would cause the calls to fail. Allowing
asynchronous orchestration of these calls increases their reliability.

In this section, we will use the following terminology definitions:

-   **Long-running operation**: a route on an API that would not be able
    to guarantee a response in a "reasonable" amount of time.
    -   This is subjective, but should be based on common timeouts for
        load balancers and web clients.
    -   As a general rule, a route with a response SLA of 1-5 seconds is
        probably not long-running, while a route with a response SLA of
        over 45 seconds is probably long-running.
-   **Synchronous request**: a POST request made to an API route where
    the response is the complete job output.
-   **Asynchronous request**: a POST request made to an API route where
    the response indicates the job output is in the process of being
    created.
-   **Job output**: the resource representing the output of an
    operation.

7.2 Requirements
----------------

*Long-running operations* **SHOULD** support *asynchronous requests*.

*Asynchronous requests* **MUST** use the pattern described in this
section for returning *job output*.

<span
class="aui-icon aui-icon-small aui-iconfont-info confluence-information-macro-icon"></span>

Orchestrating polling and callbacks for long-running requests requires
significant effort for clients. Sharing the same mechanism for
orchestrating these requests allows clients to reuse code when calling
multiple APIs.

An API call may be called using an asynchronous pattern to avoid the
technical drawbacks of a synchronous call. Synchronous calls are
susceptible to network instability or timeouts that would cause the
calls to fail. Allowing asynchronous orchestration of these potentially
long-running operations calls increases their reliability to the client.

We cannot provide clear guidelines when to use this pattern, but if your
software relies on long-running operations, you may wish to consider
this pattern. Some examples where this pattern would be appropriate:
validating a list of 10000 mailing addresses, batch processing - create
100 products at once, canceling an order, or processing a payment. If
your need requires immediate results back to the customer, this pattern
is probably not appropriate - for instance: creating an order,
retrieving a document, or calculating a price.

7.3 Job Resources
-----------------

The following pattern describes a *long-running operation* that
supports *asynchronous requests*.

The route for the operation **MUST** accept the POST method, for
example: POST /v2/payment/12345:process.

The route for the operation **MAY** allow both synchronous and
asynchronous requests, however the response **MUST **follow the
semantics of asynchronous requests in both cases.

In accordance
with <a href="https://tools.ietf.org/html/rfc7240" class="external-link" title="Follow link">RFC-7240</a>,
the Prefer header **MUST** be used as the indicator for each of the
properties listed here. APIs **MAY** support any or all of the following
values in the Prefer header:

-   respond-async: The client prefers that the server process the
    request asynchronously. When omitted, a preference for synchronous
    processing is assumed.
-   wait=N: The client prefers to receive a response synchronously if
    complete within N seconds.
-   priority=N: The client prefers that the job be processed with
    priority N, which SHOULD be an integer between 1 and 5, 1 being
    highest and 5 being lowest.
-   retries=N: The client prefers that the service try N times to
    complete the request in the event of a failure.  
    retry-delay=N: The client prefers that a static delay is used
    between tries, where N is the number of seconds between tries.
-   retry-progressive: The client prefers that a progressive delay is
    used between tries. The nature of the progressive delays is
    implementation-specific.
-   retry-until=N: The client prefers that the request is retried until
    N seconds have elapsed from the initial request.

<span
class="aui-icon aui-icon-small aui-iconfont-info confluence-information-macro-icon"></span>

<span style="color: rgb(23,43,77);">Having a consistent notion of how to
indicate priority and retry preferences allow clients to maintain
consistent orchestrations of long-running processes across APIs. This
orchestration can be complex to implement, so having consistency makes
integration significantly easier for clients.</span>

<span style="color: rgb(23,43,77);">The Preference-Applied header, as
described in RFC-7240, **SHOULD** be included in the response from the
initial POST request.</span>

Properties of the response from the initial POST are:

-   The status code **MUST** mirror that of the *job output*.
    -   When the operation is not complete, the status code is 202. The
        body **MAY** contain partial contents of the *job output*.
    -   When the operation is complete, the status code **MUST** be 201,
        202, or a 4XX/5XX status. When the result is a 201, the
        **MUST **contain contents of the *job output*.
-   The Location header **MUST** be a fully qualified URL to the *job
    output* when the status code is 201, 202.
-   Standard [response status codes](9-Response-Status-Codes) **MUST** be
    used for any case not listed here.

The response when performing a GET request on the *job output*:

-   **MUST** use status code 202 to indicate that the operation is still
    in progress, and the *job output* is not completely available.
-   **MUST** use status code 200 when the operation is complete and the
    final *job output* is returned.
-   **MUST** use the appropriate 4XX/5XX level status code and the
    Problem Details response when the operation terminally failed and
    no *job output* can be created.  
-   Standard [response status codes](9-Response-Status-Codes) **MUST** be
    used for any case not listed here.

See
also: <a href="http://restalk-patterns.org/long-running-operation-polling.html" class="external-link">http://restalk-patterns.org/long-running-operation-polling.html</a>

  

7.4 Examples
------------

### 7.4.1 Asynchronous request

#### Step 1: Initial request

``` syntaxhighlighter-pre
POST https://orders.cimpress.io/v1/orderRequests
Authorization: Bearer jwt
Prefer: respond-async
{
  "merchant": "merchants-id-abc",
  "items": [ {
    items...
  }]
}
```

Server Response:

``` syntaxhighlighter-pre
HTTP/1.1 202 Accepted
Location: https://orders.cimpress.io/v1/orderRequests/asdfasd-asdfasd-asdfasd
Preference-Applied: respond-async
```

#### Step 2: Poll job resource

``` syntaxhighlighter-pre
GET https://orders.cimpress.io/v1/orderRequests/asdfasd-asdfasd-asdfasd
```

<span class="inline-comment-marker"
data-ref="7b89e10e-30d9-4ef0-b61d-78436a6d0fea">Server Response:</span>

``` syntaxhighlighter-pre
HTTP/1.1 202 Accepted
```

#### Step 3: Poll job resource

``` syntaxhighlighter-pre
GET https://orders.cimpress.io/v1/orderRequests/asdfasd-asdfasd-asdfasd
```

Server Response:

``` syntaxhighlighter-pre
HTTP/1.1 200 OK
{
  "id": "asdfasd-asdfasd-asdfasd",
  "merchant": "merchants-id-abc",
  "items": [ {
    items...
  }]
}
```

### 7.4.2 Synchronous request for long-running operation

#### Step 1: Initial request

``` syntaxhighlighter-pre
POST https://orders.cimpress.io/v1/orderRequests
Authorization: Bearer jwt
{
  "merchant": "merchants-id-abc",
  "items": [ {
    items...
  }]
}
```

Server Response:

``` syntaxhighlighter-pre
HTTP/1.1 201 Created
Location: https://orders.cimpress.io/v1/orderRequests/asdfasd-asdfasd-asdfasd
{
  "id": "asdfasd-asdfasd-asdfasd",
  "merchant": "merchants-id-abc",
  "items": [ {
    items...
  }]
}
```

### 7.4.3 Failed asynchronous request

#### Step 1: Initial request

``` syntaxhighlighter-pre
POST https://orders.cimpress.io/v1/orderRequests
Authorization: Bearer jwt
Prefer: respond-async
{
  "merchant": "merchants-id-abc",
  "items": [ {
    items...
  }]
}
```

Server Response:

``` syntaxhighlighter-pre
HTTP/1.1 202 Accepted
Location: https://orders.cimpress.io/v1/orderRequests/asdfasd-asdfasd-asdfasd
Preference-Applied: respond-async
```

#### Step 2: Poll job resource

``` syntaxhighlighter-pre
GET https://orders.cimpress.io/v1/orderRequests/asdfasd-asdfasd-asdfasd
```

Server Response:

``` syntaxhighlighter-pre
HTTP/1.1 202 Accepted
```

#### Step 3: Poll job resource

``` syntaxhighlighter-pre
GET https://orders.cimpress.io/v1/orderRequests/asdfasd-asdfasd-asdfasd
```

Server Response:

``` syntaxhighlighter-pre
HTTP/1.1 400 Bad Request
Content-Type: application/json
{
  "_links": {
    "self": { "href": "https://orders.cimpress.io/v1/orderRequests/asdfasd-asdfasd-asdfasd" }
  },
  "status": 400,
  "type": "https://orders.cimpress.io/v1/errors/documentInvalid",
  "title": "documentInvalid",
  "detail": "There is a problem validating your document"
  "instance": "https://orders.cimpress.io/v1/errors/uniq-exp-1234"
}
```

### 7.4.4 Failed synchronous request

#### **Step 1:** Initial **request**

``` syntaxhighlighter-pre
POST https://images.cimpress.io/v1:rotate
Authorization: Bearer jwt
Prefer: respond-async
{
  "merchant": "merchants-id-abc",
  "items": [ {
    items...
  }]
}
```

Server response:

``` syntaxhighlighter-pre
HTTP/1.1 400 Bad Request
Content-Type: application/json
{
  "_links": {
    "self": { "href": "https://orders.cimpress.io/v1/orderRequests/asdfasd-asdfasd-asdfasd" }
  },
  "status": 400,
  "type": "https://orders.cimpress.io/v1/errors/documentInvalid",
  "title": "documentInvalid",
  "detail": "There is a problem validating your document"
  "instance": "https://orders.cimpress.io/v1/errors/uniq-exp-1234"
}
```

### 7.4.5 Asynchronous Request with Custom Method

#### Step 1: Initial request

``` syntaxhighlighter-pre
POST https://orders.cimpress.io/v1/orders:search
Authorization: Bearer jwt
Prefer: respond-async
{
  "merchant": "merchants-id-abc",
  "query": [ {
    criteria...
  }]
}
```

Server Response:

``` syntaxhighlighter-pre
HTTP/1.1 202 Accepted
Location: https://orders.cimpress.io/v1/searchResults/123-456
Preference-Applied: respond-async
```

#### Step 2: Poll job resource

``` syntaxhighlighter-pre
GET https://orders.cimpress.io/v1/searchResults/123-456
```

Server Response:

``` syntaxhighlighter-pre
HTTP/1.1 202 Accepted
```

#### Step 3: Poll job resource

``` syntaxhighlighter-pre
GET https://orders.cimpress.io/v1/searchResults/123-456
```

Server Response:

``` syntaxhighlighter-pre
HTTP/1.1 200 OK
{
  "_links": {
    "self": { "href": "https://orders.cimpress.io/v1/searchResults/123-456" }
  },
  "_embedded": {
    "item":  [ {},{},...]
  }
}
```
