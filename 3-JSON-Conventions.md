[RESTful Best Practices](Home)

3.1 Casing
==========

APIs **MUST** use `lowerCamelCase` for all field names in the request,
response, and documentation.  All field values that are enumerations
**MUST** also be `lowerCamelCase`.

  

<span
class="aui-icon aui-icon-small aui-iconfont-info confluence-information-macro-icon"></span>

The vast majority of javascript developers use camelCase as their API
standard, as do many other languages. Since JSON is serialized
javascript, we prefer to use the common convention in that language.
Additionally, we should design our APIs to be used directly from UIs -
using the same casing standards as most javascript UI frameworks use
eases that adoption.

  

<span
class="expand-control-icon"><img src="assets/images/icons/grey_arrow_down.png" class="expand-control-image" /></span><span
class="expand-control-text">Casing Examples</span>

<table>
<colgroup>
<col style="width: 54%" />
<col style="width: 45%" />
</colgroup>
<thead>
<tr class="header">
<th>Instead of...</th>
<th>Use lowerCamelCase!</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p><strong>alllowercase:</strong> foofieldname</p>
<p><strong>UpperCamelCase:</strong> FooFieldName</p>
<p><strong>snake_case:</strong> foo_field_name</p>
<p><strong>SCREAMING_SNAKE:</strong> FOO_FIELD_NAME</p>
<p><strong>kebab-case:</strong> foo-field-name</p></td>
<td><strong>lowerCamelCase:</strong> fooFieldName</td>
</tr>
<tr class="even">
<td>orderID</td>
<td>orderId</td>
</tr>
<tr class="odd">
<td>pricePaidUSD</td>
<td>pricePaidUsd</td>
</tr>
<tr class="even">
<td>HTMLButton</td>
<td>htmlButton</td>
</tr>
</tbody>
</table>

3.2 Data Types
==============

3.2.1 Date and Times
--------------------

APIs **<span class="inline-comment-marker"
data-ref="61c12eb1-684d-4a46-b696-7ab71ef7fe03">MUST</span>**<span
class="inline-comment-marker"
data-ref="61c12eb1-684d-4a46-b696-7ab71ef7fe03"> use ISO-8601 for dates,
dates without times, and date with times</span> per
<a href="https://www.ietf.org/rfc/rfc3339.txt" class="external-link">RFC3339</a>,
**UNLESS** otherwise specified by a public RFC that an alternate
standard must be used for a particular HTTP header value. For
example: <a href="https://tools.ietf.org/html/rfc7232#section-3.4" class="external-link">If-Unmodified-Since</a> requires
<a href="https://www.w3.org/Protocols/rfc822/#z28" class="external-link">RFC822</a>,
so that format should be used for that header.

-   Some HTTP headers (ex:
    <a href="https://tools.ietf.org/html/rfc7232#section-3.4" class="external-link">If-Unmodified-Since</a>)
    may require the use of a specific format (ex. RFC822 Specification
    for ARPA Internet Text Messages §5 Date and Time). The requirement
    to use ISO-8601 and comply with RFC3339 is nullified in cases where
    a conflicting ratified public RFC standard exists.

  

<span
class="aui-icon aui-icon-small aui-iconfont-info confluence-information-macro-icon"></span>

Timestamps and timezones are complicated. Use
<a href="https://en.wikipedia.org/wiki/ISO_8601" class="external-link">international</a>
<a href="https://www.ietf.org/rfc/rfc3339.txt" class="external-link">standards</a>
and
<a href="http://apiux.com/2013/03/20/5-laws-api-dates-and-times/" class="external-link">best practices</a>. The
ISO-8601 standard has broad library support and history.

  

APIs **SHOULD** use and store UTC representations of date-times and
timestamps.

-   When working with date-times, prefer UTC representations in your API
    over local timezones, and convert to user-preferred timezones
    separately.

  

<span
class="aui-icon aui-icon-small aui-iconfont-info confluence-information-macro-icon"></span>

Timezones are complex, and using local times for a global company is
tricky. Where representing a fixed instant in time, prefer using a UTC
representation.  

  

APIs **SHOULD **use ISO-8601 for
<a href="https://en.wikipedia.org/wiki/ISO_8601#Time_intervals" class="external-link">time durations and intervals</a>.

<span
class="aui-icon aui-icon-small aui-iconfont-error confluence-information-macro-icon"></span>

Many ISO‌-8601 parsing libraries will treat 24H and 1D as the same. Do
not rely on the specific duration string provided as a means to convey
further semantic information.

If your logic requires treating 24 hours differently then 1 day, it is
unlikely you are modeling an interval and should use a different data
structure to convey the differences in those values to your API
consumer.

  

<span
class="expand-control-icon"><img src="assets/images/icons/grey_arrow_down.png" class="expand-control-image" /></span><span
class="expand-control-text">Date and Time Examples</span>

<table>
<colgroup>
<col style="width: 57%" />
<col style="width: 42%" />
</colgroup>
<thead>
<tr class="header">
<th>Instead of these timestamps:</th>
<th>Prefer ISO-8601 UTC:</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p><strong>Unix Timestamp</strong>: 1529941649</p>
<p><strong>.NET Ticks:</strong> 636655384490000000</p>
<p><span style="color: rgb(0,0,0);"><strong>RFC1123</strong>: Mon, 25 Jun 2018 15:47:29 GMT</span></p>
<p><strong><span style="color: rgb(0,0,0);">Or  any other way to represent a date-time.</span></strong></p></td>
<td>2018-06-25T15:47:29Z</td>
</tr>
</tbody>
</table>

3.2.2 Identifiers
-----------------

Identifiers **MUST **always be strings.

<span
class="aui-icon aui-icon-small aui-iconfont-info confluence-information-macro-icon"></span>

You don't know what your ID space will be in the future, or how big it
should be. Use a string.

Identifiers **SHOULD NOT** be sequential integers.

<span
class="expand-control-icon"><img src="assets/images/icons/grey_arrow_down.png" class="expand-control-image" /></span><span
class="expand-control-text">Identifier Examples</span>

<table>
<colgroup>
<col style="width: 35%" />
<col style="width: 64%" />
</colgroup>
<thead>
<tr class="header">
<th>Instead of integer Ids:</th>
<th>Use strings or UUIDs:</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p><code>"id": 1234</code></p>
<p><code>"id": 5</code></p>
<p><code>"id": 68583854</code></p></td>
<td><p><code>"id": "1234"</code></p>
<p>or better, <strong>use a UUID or other random string</strong>:</p>
<p><code>"id": "26yv0qzk7mv7vs5tq6qjy040af"</code></p></td>
</tr>
</tbody>
</table>

3.2.3 Numbers
-------------

JSON numbers **SHOULD** **NOT** be used except when representing small
integer values.

<span
class="aui-icon aui-icon-small aui-iconfont-info confluence-information-macro-icon"></span>

Due to differences in serializers in many languages, there are often
interoperability issues between serializers. Using strings for numbers
also allows for significantly more precision when using decimal values,
as JSON numbers are floating-point based, not decimals.

3.2.4 Enumerations
------------------

Enumeration values **MUST** be `lowerCamelCase`.

3.3 Reserved Field Names
========================

APIs **MAY** include the below fields where appropriate. When an API
provides these fields, they **MUST** be used consistently with the
following semantics: 

-   `id` - the unique server created id of the resource. **MUST** be a
    string.
-   `key` - the client provided unique identifier for the
    resource. **MUST** be a string.
-   `createdBy` - the principal that created the resource. **MUST** be a
    string.
-   `createdAt` - the timestamp the resource was created. **MUST** be
    an <a href="https://en.wikipedia.org/wiki/ISO_8601" class="external-link">ISO-8601</a><a href="https://en.wikipedia.org/wiki/ISO_8601" class="external-link"> </a>timestamp.
-   `modifiedBy` - the last principal to modify the resource. if no
    modifications have been made, this should be the createdBy
    principal. **MUST** be a string.
-   `modifiedAt` - the timestamp the resource was last modified. if no
    modifications have been made, this should be the createdAt
    timestamp. **MUST** be
    an <a href="https://en.wikipedia.org/wiki/ISO_8601" class="external-link">ISO-8601</a><a href="https://en.wikipedia.org/wiki/ISO_8601" class="external-link"> </a>timestamp.

  

<span
class="aui-icon aui-icon-small aui-iconfont-info confluence-information-macro-icon"></span>

Common field naming promotes consistency in common fields that are
commonly present across APIs.

<span
class="aui-icon aui-icon-small aui-iconfont-warning confluence-information-macro-icon"></span>

We're not saying you need to have these fields, but they're a good idea
in many cases. If you **do** have these fields, you **MUST** follow the
above guidance.

### Examples

**Reserved field names**

``` syntaxhighlighter-pre
{
  "id": "4990a90a-acb5-416b-b3a3-69a71fb4c138",
  "key": "my-identifier",
  "createdBy": "asdafsdf",
  "createdAt": "{ISO-8601}",
  "modifiedBy": "asdafsdf",
  "modifiedAt": "{ISO-8601}",
  ...
}
```

