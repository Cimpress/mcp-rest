[RESTful Best Practices](Home)

Headers make up a fundamental part of the RESTful communication with
services.  As such headers are required in all responses.  F<span
style="color: rgb(9,30,66);">or headers defined in the
<a href="https://www.iana.org/assignments/message-headers/message-headers.xhtml" class="external-link">IANA standard list</a>,
APIs MUST be compliant with the respective public RFCs when using those
headers.</span>

10.1 Required Headers
=====================

Some headers supply critical information to the understanding of a
representation.  These headers have requirements as defined below.

### 10.1.1 Request Headers

-   `Accept`<span class="inline-comment-marker"
    data-ref="96820221-e059-4890-89e5-5a76341d26e7">: When not present
    the payload</span> **<span class="inline-comment-marker"
    data-ref="96820221-e059-4890-89e5-5a76341d26e7">SHOULD</span>**<span
    class="inline-comment-marker"
    data-ref="96820221-e059-4890-89e5-5a76341d26e7"> be interpreted as
    </span>`application/json`<span class="inline-comment-marker"
    data-ref="96820221-e059-4890-89e5-5a76341d26e7">.</span>
-   **`If-Match` / `If-None-Match`:** **MUST** be handled on `PUT` and
    `POST` requests when returning an `ETag` response
    header**.** **MUST** be used for validation if provided.
-   `Prefer`**: MUST** be used for to signal the request for
    asynchronous processing of an otherwise synchronous endpoint
    operation as specified by
    <a href="https://tools.ietf.org/html/rfc7240" class="external-link">RFC 7240</a> and
    vice versa.

### 10.1.2 Response Headers

-   `Cache-Control`: **SHOULD** be provided in all responses.  To
    prevent caching the value **SHOULD** be <span
    style="color: rgb(128,0,128);">"no-cache, no-store,
    must-revalidate"<span style="color: rgb(0,0,0);">. </span></span>
-   **`Content-Type`: MUST** be provided.
-   **<span class="inline-comment-marker"
    data-ref="d769c69b-2754-4546-9adf-c558e6331316">CORS </span>**<span
    class="inline-comment-marker"
    data-ref="d769c69b-2754-4546-9adf-c558e6331316">headers
    </span>**<span class="inline-comment-marker"
    data-ref="d769c69b-2754-4546-9adf-c558e6331316">SHOULD </span>**<span
    class="inline-comment-marker"
    data-ref="d769c69b-2754-4546-9adf-c558e6331316">be provided in
    response to
    </span><a href="https://developer.mozilla.org/en-US/docs/Glossary/Preflight_request" class="external-link"><span class="inline-comment-marker" data-ref="d769c69b-2754-4546-9adf-c558e6331316">preflight OPTIONS requests</span></a><span
    class="inline-comment-marker"
    data-ref="d769c69b-2754-4546-9adf-c558e6331316"> for which CORS is
    enabled</span>. The return should include:
    -   `Access-Control-Allow-Origin` for all methods.  If CORS has been
        enabled, the value **SHOULD **<span
        class="inline-comment-marker"
        data-ref="43909e4a-e600-4a6c-8e47-e229ec9d75f8">be
        the </span>value sent in the request's <span
        style="color: rgb(128,0,128);">**Origin**</span> header field. 
        It **MUST NOT** be a combined wildcard and domain.  When
        returning the **Origin** value in this field,
        the **Vary** header **MUST** contain the value **Origin** in the
        header list.
    -   `Access-Control-Allow-Methods` on OPTIONS method response,
        and **<span class="inline-comment-marker"
        data-ref="9c7df76a-6f35-4a2a-822c-e8cb8e4c9f72">SHOULD</span>**<span
        class="inline-comment-marker"
        data-ref="9c7df76a-6f35-4a2a-822c-e8cb8e4c9f72"> be a list of
        the allowed methods that the service supports, e.g. <span
        style="color: rgb(128,0,128);">"</span></span><span
        class="inline-comment-marker"
        data-ref="9c7df76a-6f35-4a2a-822c-e8cb8e4c9f72"><span
        style="color: rgb(128,0,128);">DELETE,GET,HEAD,OPTIONS,PATCH,POST,PUT</span>"</span>**<span
        class="inline-comment-marker"
        data-ref="9c7df76a-6f35-4a2a-822c-e8cb8e4c9f72">.</span>**

    -   **`Access-Control-Allow-Headers` **on OPTIONS method response,
        and **MUST** include "<span
        style="color: rgb(128,0,128);">Content-Type</span>**" **and for
        responses on endpoints which require authentication **MUST**
        include "<span
        style="color: rgb(128,0,128);">Authorization"<span
        style="color: rgb(0,0,0);"> as well.</span></span>

-   **`ETag`:** **MUST** be used to convey resource changes.  The
    `ETag` header **MAY** be the version.  If the `ETag` is set,
    `Last-Modified` **SHOULD** contain the date and time at which ETag
    was set.
-   **`Location`: SHOULD **be provided when a **201**, **202**, or
    **3XX** status code is returned.  The url provided in the
    `Location` header **MUST** be an absolute url.
-   **`Vary`:** Vary is a list of headers which **MUST** be provided
    when the response payload or response headers would be different if
    the same representation was requested at the same url from a
    different user.  In Vary specify the list of headers which
    contribute to the change.
-   `WWW-Authenticate`: **MUST** be provided when a **401** status
    code is returned.  The value **MUST** be:
    `Bearer realm="Realm", authorization_uri="AuthUrl"` where:
    -   The *`Realm`* **SHOULD **be: **<a href="https://api.cimpress.io/" class="external-link">https://api.cimpress.io/</a>**
    -   The *`AuthUrl`* **SHOULD**
        be: **<a href="https://cimpress.auth0.com/oauth/token" class="external-link">https://oauth.cimpress.io/oauth/token</a>**

10.2 Non-Standard Headers
-------------------------

Headers not defined by the IANA standards **SHOULD** **NOT** be used.
Services **MAY** return non-standard headers if the service deems it
appropriate, but clients of these services **MUST NOT **be required to
read non-standard headers and drive business logic based on them.

10.3 Service Handling Requirements
==================================

Services **MUST** handle and allow headers with all lowercase characters
as well as the documented canonical form.  Services header handling
**<span class="inline-comment-marker"
data-ref="1647005b-6b8f-4f6d-958b-876b50013924">SHOULD</span>** be case
insensitive where possible.  Headers **MUST** be in their standardize
documented casing, however HTTP header fields are case insensitive.

