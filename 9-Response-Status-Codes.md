[RESTful Best Practices](Home)

Response status codes
=====================

<span
class="aui-icon aui-icon-small aui-iconfont-warning confluence-information-macro-icon"></span>

This section is a reference for all codes - each [resource
operation](6-Resource-Operations) has information about
appropriate response codes. This section also lists generically useful
codes (404, 429, 500...).

  

APIs **MUST** return HTTP status codes that are
<a href="https://httpstatuses.com" class="external-link">most appropriate</a>
for the response.

APIs **MUST** return a
standardized [exception payload](8-Error-Responses) when returning a
4XX-series or 5XX-series response.

APIs **SHOULD** return one of the following response codes:

<table>
<colgroup>
<col style="width: 11%" />
<col style="width: 88%" />
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
<td>Use for any successful synchronous request.</td>
</tr>
<tr class="even">
<td>201 - Created</td>
<td>Use when a new resource is created from either POST or PUT. You should also provide a <code>Location</code> header with the URI to the created resource. The body should include the resource created with a <code>self</code> link.</td>
</tr>
<tr class="odd">
<td>202 - Accepted</td>
<td>Use sparingly, and only when the response cannot be processed synchronously. The body should provide a URL for the client to retrieve any output of the request at in the future.</td>
</tr>
<tr class="even">
<td>204 - No Content</td>
<td>Use when a DELETE request has been invoked successfully.</td>
</tr>
<tr class="odd">
<td>400 - Bad Request</td>
<td>Use when the data submitted to the request cannot be parsed or is syntactically invalid.</td>
</tr>
<tr class="even">
<td>401 - Unauthorized</td>
<td>Use when the user has failed to provide valid credentials.</td>
</tr>
<tr class="odd">
<td>403 - Forbidden</td>
<td>Use when the user has valid credentials, but does not have permissions to perform the desired request. You should inform the user that they don't have permissions. In some cases, in order to not leak information it may be preferred to use a 404 when a user is forbidden from accessing a specific identified resource.</td>
</tr>
<tr class="even">
<td>404 - Not Found</td>
<td>Use when a resource does not exist or cannot be found. Can also be used when a user does not have access to a specific resource, but you do not want to leak its existence to the client.</td>
</tr>
<tr class="odd">
<td>409 - Conflict</td>
<td>When PUTing or PATCHing a resource, and the version you provide is not the most recent version, this indicates a conflict with multiple updates on the same resource</td>
</tr>
<tr class="even">
<td>412 - Precondition Failed</td>
<td>When PUTing or PATCHing a resource, and the If-Match value you provide is not the current ETag, this indicates the client has not seen the most recent version</td>
</tr>
<tr class="odd">
<td><span style="color: rgb(37,37,37);">422 - Unprocessable Entity</span></td>
<td>Use when the data in the request is valid and parsable, but the values contained within are semantically invalid for the data contract. (E.g. required field omitted, invalid value for enumeration, operation invalid at current state).</td>
</tr>
<tr class="even">
<td><span style="color: rgb(37,37,37);">429 - Too Many Requests</span></td>
<td>Use for rate limiting.</td>
</tr>
<tr class="odd">
<td>500 - Internal Server Error</td>
<td>Use when you have an internal issue with your service that prevents the request from being handled, and for default error handling.</td>
</tr>
</tbody>
</table>
