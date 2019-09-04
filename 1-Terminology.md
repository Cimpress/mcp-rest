[RESTful Best Practices](Home)

Although it may be possible that no two developers have ever had exact
agreement on what
a <a href="https://en.wikipedia.org/wiki/Representational_state_transfer" class="external-link">REST API</a>
is, or what it truly means to be RESTful, we would like to introduce
some common vocabulary for this document:

**<a href="https://en.wikipedia.org/wiki/Web_resource" class="external-link">Resource</a>:** an
abstract entity that can be addressed with
a <a href="https://en.wikipedia.org/wiki/Uniform_Resource_Identifier" class="external-link">URI</a> and
has a unique identifier. For example, the URI <span
class="underline">`https://api.example.io/v1/widgets/123`</span> refers
to a `widget` resource.

**Collection Resource:** a resource that is (or contains) a set of other
resources. For example, the URI <span
class="underline">`https://api.example.io`/v1/widgets</span> refers to a
collection resource, and we can expect that appending another path
component (e.g. `/123`) to the URI would identify a single element of
the collection.

**Sub-resource:** a resource that is scoped within another resource. For
example, the URI <span
class="underline">`https://api.example.io/v1/widgets/123/components`</span> refers
to the components of a widget.

**Representation:** a concrete serialization of a resource, that can be
distinguished by possible other representations of the same resource by
its <a href="https://en.wikipedia.org/wiki/Media_type" class="external-link">media type</a>.
For example, a JSON representation for our hypothetical widget resource
would have the media type `application/json`.
