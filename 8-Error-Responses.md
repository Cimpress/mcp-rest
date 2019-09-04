[RESTful Best Practices](Home)

8.1 Error payloads 
===================

8.1.1 All Errors
----------------

All errors **MUST** comply with
<a href="https://tools.ietf.org/html/rfc7807" class="external-link">Problem Details RFC</a> (RFC-7807)
format.

<span class="inline-comment-marker"
data-ref="16d04c2c-26ab-4e42-8a22-61157a8d74f9">An error response
</span>**<span class="inline-comment-marker"
data-ref="16d04c2c-26ab-4e42-8a22-61157a8d74f9">SHOULD</span>**<span
class="inline-comment-marker"
data-ref="16d04c2c-26ab-4e42-8a22-61157a8d74f9"> constrain the
</span>`title`<span class="inline-comment-marker"
data-ref="16d04c2c-26ab-4e42-8a22-61157a8d74f9"> field to be a
lowerCamelCase name of the error or a predefined code part of another
RFC (ie, OAuth2).</span>  The `title` field **SHOULD** be human readable
<span class="inline-comment-marker"
data-ref="bfae3289-9b6d-428a-801c-07230a751060">and</span> easily used
as a key for localization.

<span class="inline-comment-marker"
data-ref="2aedf952-0cfe-4705-9a6f-c658bd3c6533">An error response
</span>**<span class="inline-comment-marker"
data-ref="2aedf952-0cfe-4705-9a6f-c658bd3c6533">MAY </span>**<span
class="inline-comment-marker"
data-ref="2aedf952-0cfe-4705-9a6f-c658bd3c6533">include other fields
specific to the error.</span>

<span style="color: rgb(23,43,77);">The following error information
**MAY** be included. If it is included, it **MUST** use the fields as
described</span>:

-   `causes` - array of error objects (e.g. when multiple things went
    wrong all at once, such as <span class="inline-comment-marker"
    data-ref="1f4d1926-8dba-42af-b013-a90c2b60e5c9">validation
    errors</span>).  Each error should match the format of the Problem
    Details RFC.

The `instance` field **MAY** be a UUID URN, if no dereference-able URL
exists for the instance
(ex: "urn:uuid:b3aa9faf-58fd-47d0-a44c-8d943cda634b" )

### Examples

**Single error** <span class="collapse-source expand-control"
style="display:none;"><span
class="expand-control-icon icon"> </span><span
class="expand-control-text">Expand source</span></span> <span
class="collapse-spinner-wrapper"></span>

``` syntaxhighlighter-pre
{
  "status": 400,
  "type": "https://errors.cimpress.io/invalidItemStatus",
  "title": "invalidItemStatus",
  "detail": "The specified status: 'kaput' is invalid for this item",
  "instance": "https://items.cimpress.io/errors/a3082d39-2b2c-4899-86b8-9f9b1a85d486"
}
```

**Multiple causes for an Error** <span
class="collapse-source expand-control" style="display:none;"><span
class="expand-control-icon icon"> </span><span
class="expand-control-text">Expand source</span></span> <span
class="collapse-spinner-wrapper"></span>

``` syntaxhighlighter-pre
{
  "status": 422,
  "type": "https://errors.cimpress.io/orders/failedValidation",
  "title": "orderFailedValidation",
  "detail": "Multiple errors occured when validating the order. See causes for more information",
  "instance": "urn:uuid:fc12d8b3-ee3c-476d-a395-49e8ead10084"
  "merchantOrderId": "abc-123-xyz",
  "causes": [
    {
      "type": "https://errors.cimpress.io/orders/validation/negativeQuantity",
      "title": "negativeQuantity",
      "detail": "Quantity cannot be a negative number",
      "merchantItemId": "5792381-qmu",
      "quantity": "-100"
    }, {
      "type": "https://errors.cimpress.io/orders/validation/invalidCountryCode",
      "title": "invalidCountryCode",
      "detail": "The Countrycode 'roflcopter' is not valid. Please provide an ISO 3166 alpha-2 country code.",
  }]
}
```

8.1.2 Authentication errors
---------------------------

Authentication errors **SHOULD** return a response in the problem
details format.

<span
class="aui-icon aui-icon-small aui-iconfont-warning confluence-information-macro-icon"></span>

We know we say to use lowerCamelCase for error code fields to match all
of our other API casing standards, but the OAuth2 specification
specifically require snake\_case. Follow the industry standard for the
specification to be compliant with the standard.

8.1.3 412 Precondition Failed
-----------------------------

When returning a 412 Precondition Failed, an API **SHOULD** return an
`etag` field when the update is rejected for a conflict. The `etag`
**MUST** be the current version of the resource had a GET been requested
instead of a PUT or PATCH.

<span
class="aui-icon aui-icon-small aui-iconfont-info confluence-information-macro-icon"></span>

Additional fields are a good idea for specific errors. Returning
the `etag` on a version mismatch allows the caller to perform
investigation about why their update failed.

  

**412 Precondition Failed** <span class="collapse-source expand-control"
style="display:none;"><span
class="expand-control-icon icon"> </span><span
class="expand-control-text">Expand source</span></span> <span
class="collapse-spinner-wrapper"></span>

``` syntaxhighlighter-pre
{
  "status": 412,
  "type": "https://errors.cimpress.io/itemConflict",
  "title": "itemConflict",
  "detail": "The update is unable to be applied to this item.  Get the latest version and resubmit.",
  "errorId": "urn:uuid:b3aa9faf-58fd-47d0-a44c-8d943cda634b",
  "etag":  "8ef4abc"
}
```
