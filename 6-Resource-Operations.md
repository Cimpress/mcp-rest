[RESTful Best Practices](Home)

- [6.1 Resource Creation (POST)](#61-resource-creation-post)
  - [6.1.1 POST Requests](#611-post-requests)
      - [Examples](#examples)
  - [6.1.2 POST Responses](#612-post-responses)
      - [Response Status Codes](#response-status-codes)
      - [POST Example](#post-example)
      - [Subsequent POST with the same Key](#subsequent-post-with-the-same-key)
- [6.2 Resource Updates (PUT, PATCH, Custom method)](#62-resource-updates-put-patch-custom-method)
  - [6.2.1 Optimistic Concurrency](#621-optimistic-concurrency)
  - [6.2.2 Partial Resource Updates (PATCH)](#622-partial-resource-updates-patch)
      - [Examples](#examples-1)
  - [6.2.3 Complete Resource Updates (PUT)](#623-complete-resource-updates-put)
      - [Example:](#example)
  - [6.2.4 Complex Resource Updates (Custom methods)](#624-complex-resource-updates-custom-methods)
      - [Examples](#examples-2)
      - [Response status codes](#response-status-codes)
- [6.3 Resource Deletes (DELETE)](#63-resource-deletes-delete)
      - [Response status codes](#response-status-codes-1)
- [6.4 Resource Retrieval (GET, OPTIONS)](#64-resource-retrieval-get-options)
      - [Response status codes](#response-status-codes-2)
- [6.5 Collection Resource Retrieval (GET, OPTIONS)](#65-collection-resource-retrieval-get-options)
      - [Examples](#examples-3)
      - [Response status codes](#response-status-codes-3)
  - [6.5.1 Pagination](#651-pagination)
      - [Examples](#examples-4)
  - [6.5.2 Sorting](#652-sorting)
      - [Examples](#examples-5)


6.1 Resource Creation (POST)
============================

6.1.1 POST Requests
-------------------

`POST` requests **MUST NOT** include the entity Id to create, either in
the URI or body.

`POST` requests **MUST** be made to collection resource URIs.

<span
class="aui-icon aui-icon-small aui-iconfont-info confluence-information-macro-icon"></span>

For Example: `POST /v1/orders/{orderId}` **is bad**. Instead,
**prefer**: `POST /v1/orders`

If a client-provided unique identifier is needed, a `key` field **MAY**
be provided for querying. 

<span
class="aui-icon aui-icon-small aui-iconfont-info confluence-information-macro-icon"></span>

A POST creates a new entity within a collection resource. If the client
creating the resource knows the ID, use a PUT instead.

The contract specified in an API for a POST request **SHOULD** be
complete representations that mirror the response of a GET to the same
resource minus the id field and fields for auditing, links, etc.

If a client-provided unique identifier is needed on a resource,
a key field MAY be accepted by the API on POST that can be used for
querying by the client.

<span
class="aui-icon aui-icon-small aui-iconfont-info confluence-information-macro-icon"></span>

A `POST` creates a new entity within a collection resource. If the
client creating the resource knows the ID, use a `PUT` instead.

If the key field is accepted by an API, POST requests containing
a key field **MUST** prevent duplicate resources created with the
same key field value.

POST requests **MAY** use fields other than key to constrain resource
creation.

####  Examples

Request:

**POST /v1/items** <span class="collapse-source expand-control"
style="display:none;"><span
class="expand-control-icon icon"> </span><span
class="expand-control-text">Expand source</span></span> <span
class="collapse-spinner-wrapper"></span>

``` syntaxhighlighter-pre
{
  "_links": {
    "productConfiguration": { "href": "https://configuration.products.cimpress.io/configurations/asdasdafasdfa" }
   },
  "key": "{merchantItemId}"
  "sku": "VIP-44517",
  "quantity": "10",
  .....
}
```

Response:

**201 Created** <span class="collapse-source expand-control"
style="display:none;"><span
class="expand-control-icon icon"> </span><span
class="expand-control-text">Expand source</span></span> <span
class="collapse-spinner-wrapper"></span>

``` syntaxhighlighter-pre
Location: https://items.commerce.cimpress.io/v1/items/4990a90a-acb5-416b-b3a3-69a71fb4c138
{
  "_links": {
    "productConfiguration": { "href": "https://configuration.products.cimpress.io/configurations/asdfasdf132asdfa"}
   },
  "id": "4990a90a-acb5-416b-b3a3-69a71fb4c138",
  "key": "{merchantItemId}"
  "createdBy": "asdafsdf",
  "createdAt": "{ISO-8601}",
  "modifiedBy": "asdafsdf",
  "modifiedAt": "{ISO-8601}",
  "sku": "VIP-44517",
  "quantity": "10",
  .....
}
```

6.1.2 POST Responses
--------------------

`POST` responses **SHOULD **return an HTTP 201 Created or an HTTP 202
Accepted response code, with a `Location` header providing the URI for
the new entity.

If an operation can be completed synchronously, an HTTP 201
**SHOULD **be returned, the response body **SHOULD** contain the
entirety of the created entity.

If the operation cannot be completed synchronously, an HTTP 202
**SHOULD** be returned, the response body **SHOULD** contain a link to
the created entity.

<span
class="aui-icon aui-icon-small aui-iconfont-info confluence-information-macro-icon"></span>

<a href="https://tools.ietf.org/html/rfc7231#section-4.3.3" class="external-link">RFC-7231</a>
explicitly specifies the result of a POST SHOULD be an HTTP 201 Created
with the Location header. Returning the created entity makes life much
easier for your calling client if they need any information off of the
entity, or if they want to provide the new entity to a user. It also
reduces round-trip time from the API which is a significant concern for
a global company.

#### Response Status Codes

APIs creating resources **MUST** return HTTP status codes that are
<a href="https://httpstatuses.com" class="external-link">most appropriate</a>
for the response.

APIs creating resources **MUST** return a standardized [exception
payload](8-Error-Responses) when returning a 4XX-series
or 5XX-series response.

The most common status codes for this operation are below:

| Status Code                                                                                              | When to use                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
|----------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span class="inline-comment-marker" data-ref="43a21ccf-5d88-4074-ac22-eac44e0d64bd">201 - Created</span> | <span class="inline-comment-marker" data-ref="43a21ccf-5d88-4074-ac22-eac44e0d64bd">Use when a new resource is created from either POST or PUT. You should also provide a </span>`Location`<span class="inline-comment-marker" data-ref="43a21ccf-5d88-4074-ac22-eac44e0d64bd"> header with the URI to the created resource. The body should include the resource created with a </span>`self`<span class="inline-comment-marker" data-ref="43a21ccf-5d88-4074-ac22-eac44e0d64bd"> link.</span> |
| 202 - Accepted                                                                                           | Use when the response cannot be processed synchronously. The body should provide a URL for the client to retrieve any output of the request at in the future.                                                                                                                                                                                                                                                                                                                                   |
| 400 - Invalid Request                                                                                    | Use when the content of the request cannot be parsed or are syntactically invalid.                                                                                                                                                                                                                                                                                                                                                                                                              |
| <span style="color: rgb(37,37,37);">422 - Unprocessable Entity</span>                                    | Use when the contract in the request is valid and parsable, but the values contained within are semantically invalid.                                                                                                                                                                                                                                                                                                                                                                           |

#### POST Example

Request:

**POST /v1/items** <span class="collapse-source expand-control"
style="display:none;"><span
class="expand-control-icon icon"> </span><span
class="expand-control-text">Expand source</span></span> <span
class="collapse-spinner-wrapper"></span>

``` syntaxhighlighter-pre
{
  "_links": {
    "productConfiguration": { "href": "https://configuration.products.cimpress.io/configurations/asdasdafasdfa" }
   },
  "key": "fooBarBaz"
  "sku": "VIP-44517",
  "quantity": "10",
  .....
}
```

Response:

**201 Created** <span class="collapse-source expand-control"
style="display:none;"><span
class="expand-control-icon icon"> </span><span
class="expand-control-text">Expand source</span></span> <span
class="collapse-spinner-wrapper"></span>

``` syntaxhighlighter-pre
Location: https://items.commerce.cimpress.io/v1/items/4990a90a-acb5-416b-b3a3-69a71fb4c138
{
  "_links": {
    "productConfiguration": { "href": "https://configuration.products.cimpress.io/configurations/asdfasdf132asdfa"}
   },
  "id": "4990a90a-acb5-416b-b3a3-69a71fb4c138",
  "key": "fooBarBaz"
  "createdBy": "asdafsdf",
  "createdAt": "{ISO-8601}",
  "modifiedBy": "asdafsdf",
  "modifiedAt": "{ISO-8601}",
  "sku": "VIP-44517",
  "quantity": "10",
  .....
}
```

#### Subsequent POST with the same Key

**POST /v1/items** <span class="collapse-source expand-control"
style="display:none;"><span
class="expand-control-icon icon"> </span><span
class="expand-control-text">Expand source</span></span> <span
class="collapse-spinner-wrapper"></span>

``` syntaxhighlighter-pre
{
  "_links": {
    "productConfiguration": { "href": "https://configuration.products.cimpress.io/configurations/qazwsxedcedcrfv" }
   },
  "key": "fooBarBaz"
  "sku": "VIP-65782",
  "quantity": "83"
}
```

Response

**409 Precondition Failed** <span class="collapse-source expand-control"
style="display:none;"><span
class="expand-control-icon icon"> </span><span
class="expand-control-text">Expand source</span></span> <span
class="collapse-spinner-wrapper"></span>

``` syntaxhighlighter-pre
{
  "status": 409,
  "type": "https://errors.cimpress.io/items/duplicateItem",
  "title": "duplicateItem",
  "detail": "The item with key 'fooBarBaz' already exists.",
  "instance": "urn:a8ebded3-3050-4e35-b439-71a6f01cee46"
  "key": "fooBarBaz",
}
```

------------------------------------------------------------------------

6.2 Resource Updates (PUT, PATCH, Custom method)
================================================

6.2.1 Optimistic Concurrency
----------------------------

Resource updates **MUST** be idempotent. That is, identical requests
sent multiple times **MUST** result in identical resource state.  This
property ensures deterministic behavior under at-least-once guarantees
of request processing. 

APIs for mutable resources **MUST** employ optimistic concurrency
control to prevent lost updates when making changes to existing data
concurrently.

APIs for mutable resources **MUST** support conditional requests as
defined in
<a href="https://tools.ietf.org/html/rfc7232" class="external-link">RFC 7232</a>. 
These API's **MUST** use `ETag` and `If-Match` as the primary method of
concurrency control. The server **MUST** respond with 412 when the
`ETag` from the request doesn't match the current one for that resource.

------------------------------------------------------------------------

6.2.2 Partial Resource Updates (PATCH)
--------------------------------------

APIs **SHOULD** use `PATCH` for partial updates to resources.

`PATCH` request **MUST** support the conditional requests and
the `If-Match` header as described in section 6.2.1.

`PATCH` **SHOULD** replace collection properties with entire contents
from request.  Use a custom method to update a collection in a more
sophisticated way.

#### Examples

The most simple PATCH operation is any subset of fields of the entity
being updated.

**PATCH /v1/items/{id}** <span class="collapse-source expand-control"
style="display:none;"><span
class="expand-control-icon icon"> </span><span
class="expand-control-text">Expand source</span></span> <span
class="collapse-spinner-wrapper"></span>

``` syntaxhighlighter-pre
If-Match: {ETag value}
{
  "status": "shipped",
  "quantity": "10",
  "price": "4.25"
}
```

------------------------------------------------------------------------

6.2.3 Complete Resource Updates (PUT)
-------------------------------------

<span
class="aui-icon aui-icon-small aui-iconfont-error confluence-information-macro-icon"></span>

As the API evolves in a backwards compatible way, new fields may be
added that existing clients are not aware of. If a new client gives a
new field a value, existing clients may be at risk of setting it to null
just by not sending it in their requests. In general prefer PATCH over
PUT.

  

A `PUT` request **MAY **be used when updating an entire resource at once
from the body of the request.

`PUT` request URLs **MUST** uniquely identify a single resource that is
being updated.

`PUT` requests **SHOULD** return an HTTP 200 OK response with the the
entire updated entity in the response body. If it is not possible to
return the entire resource in a timely fashion, an HTTP 202 Accepted
**SHOULD** be returned.

`PUT` requests for mutable resources **MUST** send an `If-Match` header
as described in section 6.2.1.

`PUT` requests **MUST** be idempotent.  That is, identical requests sent
multiple times **MUST** result in identical resource state.  This
property ensures deterministic behavior under at-least-once guarantees
of request processing. 

#### Example:

**PUT /v1/items/{id}** <span class="collapse-source expand-control"
style="display:none;"><span
class="expand-control-icon icon"> </span><span
class="expand-control-text">Expand source</span></span> <span
class="collapse-spinner-wrapper"></span>

``` syntaxhighlighter-pre
If-Match: {ETag value}
{
  "_links": {
    "productConfiguration": { "href": "https://configuration.products.cimpress.io/configurations/asdasd-afasdfa"}
   },
  "id": "4990a90a-acb5-416b-b3a3-69a71fb4c138"
  "sku": "VIP-44517",
  "quantity": "10",
  .....
}
```

------------------------------------------------------------------------

  

6.2.4 Complex Resource Updates (Custom methods)
-----------------------------------------------

APIs **MAY** use `POST` with a custom method for partial updates to
resources. 

Custom update methods **SHOULD **be exposed by POST operations using a
"`:"` and the name of the update. For example, an update to an item
status which requires a custom update format should be a
to `POST /v1/items/{itemId}:updateItemStatus`

A custom method `POST` request **MUST** support the conditional requests
and the `If-Match` header described in section 6.2.1.

Updates to a collection resource **MUST** be performed in a similar
manner to a single resource.  

#### Examples

**POST /v1/items/{itemId}:updateItemStatus** <span
class="collapse-source expand-control" style="display:none;"><span
class="expand-control-icon icon"> </span><span
class="expand-control-text">Expand source</span></span> <span
class="collapse-spinner-wrapper"></span>

``` syntaxhighlighter-pre
If-Match: {ETag value}
{
  "statuses": {
    "shipped": {
      "quantity": "10"
    },
    "production": {
      "quantity": "10"
    }
  }
}
```

**POST Resource Collection
/v1/fulfiller/{id}/locations:addRemoveLocations** <span
class="collapse-source expand-control" style="display:none;"><span
class="expand-control-icon icon"> </span><span
class="expand-control-text">Expand source</span></span> <span
class="collapse-spinner-wrapper"></span>

``` syntaxhighlighter-pre
If-Match: {ETag value}
{ 
   "addLocations": [ 
          { "key": "foo" },
          { "key": "bar" }
        
      ],
   "removeLocations": [ 
          { "key": "bazz" },
          { "key": "boo" }
        
   ]
}
```

#### Response status codes

APIs updating resources **MUST** return HTTP status codes that are
<a href="https://httpstatuses.com" class="external-link">most appropriate</a>
for the response.

APIs updating resources **MUST** return a standardized [exception
payload](8-Error-Responses) when returning a 4XX-series
or 5XX-series response.

The most common status codes for this operation are below:

| Status Code                                                           | When to use                                                                                                                                                                       |
|-----------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 200 - OK                                                              | Use for any successful synchronous request.                                                                                                                                       |
| 202 - Accepted                                                        | Use sparingly, and only when the response cannot be processed synchronously. The body should provide a URL for the client to retrieve any output of the request at in the future. |
| 400 - Invalid Request                                                 | Use when the content of the request cannot be parsed or are syntactically invalid.                                                                                                |
| 404 - Not Found                                                       | Use when the targeted resource could not be found.                                                                                                                                |
| 412 - Precondition Failed                                             | When PUTing or PATCHing a resource, and the If-Match value you provide is not the most recent Etag, this indicates a conflict with multiple updates on the same resource          |
| <span style="color: rgb(37,37,37);">422 - Unprocessable Entity</span> | Use when the contract in the request is valid and parsable, but the values contained within are semantically invalid.                                                             |

------------------------------------------------------------------------

6.3 Resource Deletes (DELETE)
=============================

APIs **SHOULD **use the `DELETE` verb to remove individual resources,
and follow
the <a href="https://tools.ietf.org/html/rfc7231#section-4.3.5" class="external-link">RFC7231</a> guidelines
on return values:

  

``` newpage
   If a DELETE method is successfully applied, the origin server SHOULD
   send a 202 (Accepted) status code if the action will likely succeed
   but has not yet been enacted, a 204 (No Content) status code if the
   action has been enacted and no further information is to be supplied,
   or a 200 (OK) status code if the action has been enacted and the
   response message includes a representation describing the status.
```

  

APIs **SHOULD NOT** return the data that was deleted.

<span
class="aui-icon aui-icon-small aui-iconfont-info confluence-information-macro-icon"></span>

If you’re returning the resource in the state it was before it was
deleted, you’re explicitly returning an out of date response. Instead,
prefer to return either nothing (204 No Content) or a data model
indicating that the deletion was successful if the client would require
additional information.

APIs for resources which can be deleted **MUST **support conditional
requests as defined
in <a href="https://tools.ietf.org/html/rfc7232" class="external-link">RFC 7232</a>. 
These API's **MUST **use `ETag` and `If-Match `as the primary method of
concurrency control as described in section 6.2.1.

#### Response status codes

APIs deleting resources **MUST** return HTTP status codes that are
<a href="https://httpstatuses.com" class="external-link">most appropriate</a>
for the response.

APIs deleting resources **MUST** return a standardized [exception
payload](8-Error-Responses) when returning a 4XX-series
or 5XX-series response.

The most common status codes for this operation are below:

<table>
<colgroup>
<col style="width: 23%" />
<col style="width: 76%" />
</colgroup>
<thead>
<tr class="header">
<th>Status Code</th>
<th>When to use</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>200 - OK</td>
<td>Use when a <code>DELETE</code> request was successful and the client may require further information.</td>
</tr>
<tr class="even">
<td>202 - Accepted</td>
<td>Use when a <code>DELETE</code> request will be performed asynchronously. </td>
</tr>
<tr class="odd">
<td>204 - No Content</td>
<td>Use when a <code>DELETE</code> request was successfully applied and no further information is available or useful to caller.</td>
</tr>
<tr class="even">
<td>404 - Not Found</td>
<td>Use when the targeted resource could not be found.</td>
</tr>
<tr class="odd">
<td>412 - Precondition Failed</td>
<td>If an If-Match is sent in the request, returned if the current Etag doesn't match.</td>
</tr>
</tbody>
</table>

------------------------------------------------------------------------

  

6.4 Resource Retrieval (GET, OPTIONS)
=====================================

APIs **SHOULD** use `GET` to retrieve or search for resources.

`GET` requests **MUST NOT** mutate resources.

APIs **MUST** support `OPTIONS` HTTP requests so that you can be called
from UIs that use
<a href="https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS" class="external-link">CORS</a>.

<span
class="aui-icon aui-icon-small aui-iconfont-info confluence-information-macro-icon"></span>

UIs use a pattern called
<a href="https://developer.mozilla.org/en-US/docs/Glossary/Preflight_request" class="external-link">preflight requests</a>
to determine if they can access particular information for a user.
Preflight requests rely upon the `OPTIONS` HTTP method being supported.
If you don't support it, no UI will ever be able to call your API.

#### Response status codes

APIs retrieving resources **MUST** return HTTP status codes that are
<a href="https://httpstatuses.com" class="external-link">most appropriate</a>
for the response.

APIs retrieving resources **MUST** return a standardized [exception
payload](8-Error-Responses) when returning a 4XX-series
or 5XX-series response.

The most common status codes for this operation are below:

| Status Code     | When to use                                 |
|-----------------|---------------------------------------------|
| 200 - OK        | Use for any successful synchronous request. |
| 404 - Not Found | The resource could not be found.            |

  

------------------------------------------------------------------------

6.5 Collection Resource Retrieval (GET, OPTIONS)
================================================

Collection resources **MUST** be not be returned as a raw array.  The
response format should be an object as defined by the HAL+JSON
specification.

The following common top-level fields **MUST** be returned on a
collection APIs:

-   `_embedded` - Object containing at least one relation called `item`
    <span class="inline-comment-marker"
    data-ref="a3108420-5c75-4510-9863-3dbdfad4788f">as</span> dictated
    by HAL+JSON and <span
    style="color: rgb(23,43,77);"><a href="https://tools.ietf.org/html/rfc6573" class="external-link">RFC-6573</a></span>

<span style="color: rgb(23,43,77);">Responses containing no
results </span>**MUST**<span style="color: rgb(23,43,77);"> return
a </span>**200 - OK**<span style="color: rgb(23,43,77);"> result
with the `_embedded` `item` relation </span><span
style="color: rgb(23,43,77);">set to an empty array in favor of
a </span>**404 - Not Found**

Collection resources **SHOULD** support filtering of the resources using
query parameters.

<span
class="aui-icon aui-icon-small aui-iconfont-info confluence-information-macro-icon"></span>

For developers unfamiliar with the HAL+JSON specification, its handling
of arrays is likely to be surprising. Here is a modified example from
the specification.

Because a HAL+JSON response hangs on the keys "\_links" and
"\_embedded", the top-level value cannot be an array. (Where would the
keys and values go?) For that reason, arrays are considered to be
embedding themselves.

This practice is to provide consistency and flexibility for service
evolution.  The wrapping object allows fields to be added in the future
as needed (paging, etc...). 

#### Examples

**GET /v1/pets?ownerId=c040ec5f-8d83-4b14-9c15-62e873b20bba** <span
class="collapse-source expand-control" style="display:none;"><span
class="expand-control-icon icon"> </span><span
class="expand-control-text">Expand source</span></span> <span
class="collapse-spinner-wrapper"></span>

``` syntaxhighlighter-pre
GET /v1/pets?ownerId=465dde9d-39cf-4c5b-8008-bf62b0b45b95
{
  "_links": {
    "self": { "href": "https://pets.doghouse.cimpress.io/v1/pets?ownerId=c040ec5f-8d83-4b14-9c15-62e873b20bba"}
   },
  "_embedded": {
    "item": [
      {
        "_links": {
          "self": { "href": "https://pets.doghouse.cimpress.io/v1/pets/4990a90a-acb5-416b-b3a3-69a71fb4c138" },
          "vaccinations": { "href": "https://pets.doghouse.cimpress.io/v1/pets/4990a90a-acb5-416b-b3a3-69a71fb4c138/vaccinations" },
          "owner": { "href": "https://people.house.cimpress.io/v1/people/c040ec5f-8d83-4b14-9c15-62e873b20bba" }
        },
        "id": "4990a90a-acb5-416b-b3a3-69a71fb4c138",
        "type": "cat",
        "name": "Mr. Fuzzles"
      },
      {
        "_links": {
          "self": { "href": "https://pets.doghouse.cimpress.io/v1/pets/5990a90a-acb5-416b-b3a3-69a71fb4c138" },
          "vaccinations": { "href": "https://pets.doghouse.cimpress.io/v1/pets/5990a90a-acb5-416b-b3a3-69a71fb4c138/vaccinations" },
          "owner": { "href": "https://people.house.cimpress.io/v1/people/c040ec5f-8d83-4b14-9c15-62e873b20bba" }
        },
        "id": "5990a90a-acb5-416b-b3a3-69a71fb4c138",
        "type": "dog",
        "name": "Smokey"
      }
    ]
  }
 
}
```

#### Response status codes

APIs retrieving resources **MUST** return HTTP status codes that are
<a href="https://httpstatuses.com" class="external-link">most appropriate</a>
for the response.

APIs retrieving resources **MUST** return a standardized [exception
payload](8-Error-Responses) when returning a 4XX-series
or 5XX-series response.

The most common status codes for this operation are below:

| Status Code     | When to use                                                                                                |
|-----------------|------------------------------------------------------------------------------------------------------------|
| 200 - OK        | Use for any successful synchronous request.  Empty collections should use this status instead of 404.      |
| 404 - Not Found | The resource could not be found.  There is no collection resource versus the collection resource is empty. |

6.5.1 Pagination
----------------

Unbounded collection resources, such as search results, **MUST** be
paged via `limit` and `offset` query string parameters.

The following common top-level fields **MUST** be returned on a
paginated API:

-   `offset` - String, reference pointer to the end of this page in the
    search results. Should be passed as `offset=<last offset>` as a
    query parameter of the request to get the next set of results. May
    be a number, but can also be a hash or b64 value.
-   `_embedded` - Object containing at least one relation called `item`
    as dictated by HAL+JSON

Additionally, paginated APIs **MAY** return:

-   `count` - Number. The actual number of results returned
    in `results`.
-   `total` - Number. The total number of results matching the query.

  

If a collection resource is not paginated, then responses **MUST** be
bounded.

<span
class="aui-icon aui-icon-small aui-iconfont-info confluence-information-macro-icon"></span>

If you have no limits on your response, it opens you up to accidental
(and intended) denial-of-service attacks, as well as the possibility of
accidentally sending huge result sets to your clients and causing
extreme load on any downstream systems.

  

#### Examples

<span
class="aui-icon aui-icon-small aui-iconfont-warning confluence-information-macro-icon"></span>

Null fields are included below for completeness, but are not required in
the API response for efficiency.

**GET /v1/pets?type=cat** <span class="collapse-source expand-control"
style="display:none;"><span
class="expand-control-icon icon"> </span><span
class="expand-control-text">Expand source</span></span> <span
class="collapse-spinner-wrapper"></span>

``` syntaxhighlighter-pre
GET /v1/pets?type=cat
{
  "_links": {
    "self": { "href": "https://pets.doghouse.cimpress.io/v1/pets?type=cat"},
    "first": { "href": "https://pets.doghouse.cimpress.io/v1/pets?type=cat"},
    "prev": null,
    "next": { "href": "https://pets.doghouse.cimpress.io/v1/pets?type=cat &offset=asdfavaweawe"},
    "last": null
   },
  "offset": "asdfavaweawe",
  "count": 10,
  "total": null,
  "_embedded": {
    "item": [      
      {
        "_links": {
          "self": { "href": "https://pets.doghouse.cimpress.io/v1/pets/4990a90a-acb5-416b-b3a3-69a71fb4c138" },
          "vaccinations": { "href": "https://pets.doghouse.cimpress.io/v1/pets/4990a90a-acb5-416b-b3a3-69a71fb4c138/vaccinations" },
          "owner": { "href": "https://people.house.cimpress.io/v1/people/c040ec5f-8d83-4b14-9c15-62e873b20bba" }
        },
        "id": "4990a90a-acb5-416b-b3a3-69a71fb4c138",
        "type": "cat",
        "name": "Mr. Fuzzles"
      },
      {
        "_links": {
          "self": { "href": "https://pets.doghouse.cimpress.io/v1/pets/2d696aa4-683a-419a-9329-21245380f848" },
          "vaccinations": { "href": "https://pets.doghouse.cimpress.io/v1/pets/2d696aa4-683a-419a-9329-21245380f848/vaccinations" },
          "owner": { "href": "https://people.house.cimpress.io/v1/people/ed822444-2978-4d7a-b4ea-8e863e520df0" }
        },
        "id": "2d696aa4-683a-419a-9329-21245380f848",
        "type": "cat",
        "name": "Felix"
      },
      ...
    ]
  },
  ...
}
```

6.5.2 Sorting
-------------

Collection resources which support sorting **MUST** be done using the
`sort` query string parameter.  
Users **SHOULD** be able to specify ascending or descending order by
adding `:asc` or `:desc` to one or multiple fields.

#### Examples

Example with implicit `asc` sorting order documented by the API for the
field `createdAt`:

<a href="https://orders.commerce.cimpress.io/v1/orders?merchantId=vistaprint&amp;sort=createdAt" class="external-link">https://orders.commerce.cimpress.io/v1/orders?merchantId=vistaprint&amp;sort=createdAt</a>

Example with explicit sorting order:  
<a href="https://orders.commerce.cimpress.io/v1/orders?merchantId=vistaprint&amp;sort=createdAt:desc" class="external-link">https://orders.commerce.cimpress.io/v1/orders?merchantId=vistaprint&amp;sort=createdAt:desc</a>  
  

Example with explicit sorting order for multiple fields:  
<a href="https://orders.commerce.cimpress.io/v1/orders?merchantId=vistaprint&amp;sort=createdAt:desc&amp;sort=createdBy:asc" class="external-link">https://orders.commerce.cimpress.io/v1/orders?merchantId=vistaprint&amp;sort=createdAt:desc&amp;sort=createdBy:asc</a>  
  

<span
class="aui-icon aui-icon-small aui-iconfont-warning confluence-information-macro-icon"></span>

The API **MUST** support specifying a query parameter multiple times if
it supports sorting by multiple fields (such as
`sort=category&sort=merchant`)

  
