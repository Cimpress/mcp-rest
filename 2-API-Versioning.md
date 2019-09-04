[RESTful Best Practices](Home)

<span id="id-2APIVersioning-apiversioning" class="confluence-anchor-link"></span>
=================================================================================

  

<span class="inline-comment-marker" data-ref="3cab394b-2eba-4d4b-86af-abec27826ead">2.1 Versioning</span>
=========================================================================================================

<span class="inline-comment-marker"
data-ref="3cab394b-2eba-4d4b-86af-abec27826ead">API Versioning is
critical to ensure that clients are not broken by changes. At the same
time, versioning your API with a breaking change requires development
work by your consumers.</span>

<span class="inline-comment-marker"
data-ref="3cab394b-2eba-4d4b-86af-abec27826ead">Developers choosing to
version their API </span>**<span class="inline-comment-marker"
data-ref="3cab394b-2eba-4d4b-86af-abec27826ead">MUST</span>**<span
class="inline-comment-marker"
data-ref="3cab394b-2eba-4d4b-86af-abec27826ead"> evaluate if the
breaking changes are worth the business value to the consumer for the
time required to make the change.</span>

<span
class="aui-icon aui-icon-small aui-iconfont-info confluence-information-macro-icon"></span>

Be mindful that customers are not interested in "better" versions of the
API unless it provides a concrete business value for them to move to. 

2.2 <span style="color: rgb(23,43,77);">Sandbox/Alpha/</span>Beta Routes
========================================================================

Individual API routes **MAY **be signaled as unreliable to clients by
clearly marking the API documentation with the phrase "**ALPHA-** "
or "**BETA -** ".  

Such routes **MUST** match the route prefix of **/v0**.

API routes prefixed by **/v0 MAY** be changed or removed without warning
or notification.

<span
class="aui-icon aui-icon-small aui-iconfont-info confluence-information-macro-icon"></span>

This lets you mark something as "We're working on this" without needing
to hide it from your clients. This is useful for getting early feedback
from your consumers before the interface is finalized.There is no
requirement that APIs support a deprecation cycle for these routes.

2.3 Production Routes
=====================

API routes not prefixed by **/v0 MUST NOT** introduce breaking changes.

<span style="color: rgb(9,30,66);">Breaking changes in the request or
response model </span>**MUST**<span style="color: rgb(9,30,66);"> create
a new version of the API. A change is breaking when it is backwards
incompatible and results in negative client impact.  Backwards
incompatible changes include but are not limited to:</span>

-   Renaming of a field
-   Removal of a field
-   Changing the type of a field

  

Fields **MAY** be added to APIs without creation of a new version.

<span
class="aui-icon aui-icon-small aui-iconfont-info confluence-information-macro-icon"></span>

Why? Adding fields is already backwards compatible.

  

Required fields **MAY** be changed to be no longer required in an API
without creation of a new version.

<span
class="aui-icon aui-icon-small aui-iconfont-info confluence-information-macro-icon"></span>

Often, deprecating a field (but not removing it) and working with your
clients to stop its use is often an easier conversation than forcing
them to a new API version entirely.

  

  

------------------------------------------------------------------------

Examples
--------

Imagine in API that takes:

**POST /v1/items**

``` syntaxhighlighter-pre
{
  "foo": "string",
  "qaz": "qux",
  "myData": "some:magical,string:2parse",
  .....
}
```

  

...and you want to change it:

| Instead of...                                                                  | Prefer...                                                                                                                                                                                                                        |
|--------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Removing *foo*                                                                 | Marking *foo* deprecated, but continue to populate it. Removing fields may break your clients!                                                                                                                                   |
| Renaming *foo* to *bar*                                                        | Marking *foo* as deprecated, and adding *bar* as a new field. Consider populating *bar* with the contents of *foo* when *bar *is not set by the client                                                                           |
| Changing *myData *from a string to an object.                                  | Mark *myData* as deprecated, and create a differently named object. Copy the data from the old field to a value in the new field if appropriate.                                                                                 |
| Adding field *bar,* and making it required, returning a 422 when not provided. | Add *bar* as optional. If your API truly cannot live without it, first ask why it is now required. If it really is required, you **MUST** version your API to prevent breaking changes. Your old API must also continue to work. |

