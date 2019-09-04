[RESTful Best Practices](Home)

4.1 Routes
==========

When versioning a resource, the API routes **MUST** have a prefix
indicating the version number as the base element in the URL:
`service.domain.cimpress.io/v1/orders`

<span
class="aui-icon aui-icon-small aui-iconfont-info confluence-information-macro-icon"></span>

Versioning in the URL is easy for consumers and makes new versions easy
to discover, access, and document. [More detail about versioning
APIs](2-API-Versioning) is available in the [API
Versioning section of this RFC](2-API-Versioning).

API resources **MUST** be camelCase.

<span
class="aui-icon aui-icon-small aui-iconfont-info confluence-information-macro-icon"></span>

This aligns with our recommendation on JSON formatting.

API resources **MUST **be identified by nouns.

<span
class="aui-icon aui-icon-small aui-iconfont-info confluence-information-macro-icon"></span>

In REST, the only verbs are the set of HTTP methods (GET, POST, etc.);
these have standard common behaviors that are important to use (e.g. in
caching proxies such as AWS CloudFront). Introducing routes that are
verbs is a more SOAP / RPC style, and is inconsistent
with <a href="https://tools.ietf.org/html/rfc7232" class="external-link">conditional requests</a>.
If you need to use an RPC-style route, consider using a [custom
method](https://cimpress-support.atlassian.net/wiki/spaces/RFC/pages/260112524/6+Resource+Operations#id-6ResourceOperations-ResourceUpdates(Custommethods)).

4.2 Collection Resources
========================

Collection resources **MUST** be identified by plural nouns.

<span
class="aui-icon aui-icon-small aui-iconfont-info confluence-information-macro-icon"></span>

Instead of `/v1/order`, prefer /`v1/orders/`as that resource is a
collection of multiple orders. `/v1/orders/`*`some-order-id` *is a
single order resource.

Collection resources **SHOULD** be homogeneous; all resources in a
collection should be of the same type (the same OpenAPI schema).

Top-level API resources **SHOULD** generally be collection resources.
One notable exception is `/v1/healthcheck`.

Collection resources **SHOULD** provide a query API using GET. For
example, `GET /v1/orders?sku=CIM-12345&status=shipped`

<span
class="aui-icon aui-icon-small aui-iconfont-info confluence-information-macro-icon"></span>

This provides a common interface to clients for searching and discovery
in a unified format across services. Avoid using POSTs for search unless
it is absolutely required, in which case consider using a [custom method
for `:search`](https://cimpress-support.atlassian.net/wiki/spaces/RFC/pages/260112524/6+Resource+Operations#id-6ResourceOperations-ResourceUpdates(Custommethods)).

4.3 Resource Identifiers
========================

Path elements following a collection resource **SHOULD** identify a
specific instance of that resource. <span
style="color: rgb(0,0,0);">Identifiers</span> **MAY** be a reserved
string like "latest" or "current" to point to a specific resource.

<span
class="aui-icon aui-icon-small aui-iconfont-info confluence-information-macro-icon"></span>

For example, `/v1/orders/cancelRequests/` is a bad route, because
`cancelRequests` does not identify an order resource.

Resource identifiers **MUST** be "simple" identifiers rather than
"compound" or encoded data based values. For example, integer ids and
URL-encoded email addresses are okay, whereas URL-encoded JSON and XML
data are not.

<span
class="aui-icon aui-icon-small aui-iconfont-info confluence-information-macro-icon"></span>

Using complex data-encoded URLs to identify resources makes discovery
hard, readability hard, and caching nearly impossible. Instead, prefer
to use hashed identifiers, or just use a unique Id. Don't make your
clients hold your state!

Resources **MUST** be uniquely returned by identifier, or from a query
on the root object.

APIs **SHOULD** use immutable identifiers in their path; identifiers for
a resource **SHOULD NOT** be changed once the resource is created.

<span
class="aui-icon aui-icon-small aui-iconfont-info confluence-information-macro-icon"></span>

Providing user changeable URIs (via slugs, or kabob converted names) can
break resource URLs that clients store or users bookmark. If you support
mutable identifiers in the path, be sure you provide support for
redirection logic!

  

4.4 RPC-style routes
====================

Routes that are not noun-based resources **SHOULD **be avoided.

Where verb-based routes are required, API routes **SHOULD** use a
[custom
method](https://cimpress-support.atlassian.net/wiki/spaces/RFC/pages/260112524/6+Resource+Operations#id-6ResourceOperations-ResourceUpdates(Custommethods))
on a resource or resource collection by using a `:` in the URI to denote
an action on a resource -
`/v1/images:rotate`, `/v1/orders/someId:cancel`, `/v1/priceModels:execute`, `/v3/surfaceSets:calculate`

  

4.5 Resource Location Examples
==============================

<table style="width:100%;">
<colgroup>
<col style="width: 14%" />
<col style="width: 14%" />
<col style="width: 70%" />
</colgroup>
<thead>
<tr class="header">
<th>Instead of...</th>
<th>Prefer...</th>
<th>Why?</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>GET /foo</td>
<td>GET /v1/foos</td>
<td>Version your API, and make your resource names plural.</td>
</tr>
<tr class="even">
<td>GET /v1/foo?id=7</td>
<td>GET /v1/foos/7</td>
<td>Access your resources by Id, and save query parameters for actual queries. ?id=7 is fine, but that API should return a collection resource, not just the entity 7.</td>
</tr>
<tr class="odd">
<td>POST /v1/foo/search</td>
<td>GET /v1/foos?bar=bin</td>
<td>User query string parameters for their intent - querying a collection of resources.</td>
</tr>
<tr class="even">
<td>POST /v1/foos/bar</td>
<td>POST /v1/foos/{fooId}/bars</td>
<td>Identify your resources uniquely, and treat sub-resources with respect.</td>
</tr>
<tr class="odd">
<td><p>GET /v1/%7B%22foo%22%3A%22bar%22</p>
<p>%2C%20%22bin%3A%22baz%22%7D</p></td>
<td>GET /v1/foos/foo-bar.bin-baz</td>
<td>JSON in a URL is a great way to break things like caching and readability. Use a hash code for small data, or generate an Identifier.</td>
</tr>
<tr class="even">
<td>POST /v1/foo/calculate</td>
<td><p>POST /v1/foos:calculate</p>
<p>POST /v1/foos/7:calculate</p></td>
<td>Prefer using a [custom method](6-Resource-Operations) for action-based URLs instead of trying to shove verbs into resource names.</td>
</tr>
</tbody>
</table>

