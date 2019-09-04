[RESTful Best Practices](Home)

Related resources **SHOULD **be specified by links according to the
<a href="https://tools.ietf.org/html/draft-kelly-json-hal-08" class="external-link">HAL specification</a>.

<span
class="aui-icon aui-icon-small aui-iconfont-info confluence-information-macro-icon"></span>

Ids may be shorter but compression minimizes issues with the length of
URLs.  Ids lack the context of the resource and requires the client to
maintain configuration for resource APIs possibly for multiple
environments.

Links **MUST** be absolute URLs.

<span
class="aui-icon aui-icon-small aui-iconfont-info confluence-information-macro-icon"></span>

Relative URLs are discouraged because they require other information
that the client must interpret or derive.  When the payload is passed
around in client code, there may be no context of the request that can
be used to expand relative URLs.

Embedded subresources **SHOULD** use the `_embedded` field and may also
contain `_links` on each embedded resource.

Links **MAY** also exist on nested objects that are not embedded
subresources.  

<span
class="aui-icon aui-icon-small aui-iconfont-warning confluence-information-macro-icon"></span>

URLs may be unstable over time, so they may not be suitable for
persistence.

### Examples

**Relationship via HAL Example**

``` syntaxhighlighter-pre
{
  "_links": {
    "self": { "href": "https://item-service.commerce.cimpress.io/v1/items/4990a90a-acb5-416b-b3a3-69a71fb4c138" }, 
    "productConfiguration": { "href": "https://configuration.products.cimpress.io/v1/configurations/bbf83dd31baec56a52047e3433f84bc751820acb"},
    "orderedConfiguration": { "href": "https://configuration.products.cimpress.io/v1/configurations/abf83dd31baec56a52047e3433f84bc751820acb"},
    "legacyProduct": { "href": "https://cdn.products.cimpress.io/v3/mcpsku/VIP-44517" },
    "product": { "href": "https://cdn.products.cimpress.io/v2/mcpsku/VIP-44517" }
  },
  "id": "4990a90a-acb5-416b-b3a3-69a71fb4c138",
  "quantity": 10,
  ...
}
```

  

**Subresource Links Example**

``` syntaxhighlighter-pre
{
  "_links": {
    "self": { "href": "https://fulfillers.cimpress.io/v1/fulfillers/1324abef-acb5-416b-b3a3-69a71fb4c138" },
  },
  "id": "1324abef-acb5-416b-b3a3-69a71fb4c138",
  "_embedded": {
    "configurations": [
      {
        "_links": {
          "self": { "href": "https://fulfillers.cimpress.io/v1/configurations/4990a90a-acb5-416b-b3a3-69a71fb4c138" },
          "shipmentPlans": { "href": "https://logistics.cimpress.io/v1/shipmentPlans/4990a90a-acb5-416b-b3a3-69a71fb4c138/statuses"},
          "fulfiller": { "https://fulfillers.cimpress.io/v1/fulfillers/1324abef-acb5-416b-b3a3-69a71fb4c138" }
        },
        "id": "4990a90a-acb5-416b-b3a3-69a71fb4c138",
      },
      {
        "_links": {
          "self": { "href": "https://fulfillers.cimpress.io/v1/configurations/5990a90a-acb5-416b-b3a3-69a71fb4c138"},
          "shipmentPlans": { "href": "https://logistics.cimpress.io/v1/shipmentPlans/5990a90a-acb5-416b-b3a3-69a71fb4c138/statuses"},
          "fulfiller": { "href": "https://fulfillers.cimpress.io/v1/fulfillers/1324abef-acb5-416b-b3a3-69a71fb4c138"}
        },
        "id": "5990a90a-acb5-416b-b3a3-69a71fb4c138",
      }
    ]
  }
  ...

}
```

**Pagination Links Example**

``` syntaxhighlighter-pre
{
  "_links": {
    "self": { "href": "https://products.cimpress.io/v1/products?q=blah&limit=25&offset=1231fc23d2ddqewdq23d2dascx23d2edaasdcawewer"},
    "first": { "href": "https://products.cimpress.io/v1/products?q=blah&limit=25"},
    "prev": { "href": "https://products.cimpress.io/v1/products?q=blah&limit=25"},
    "next": { "href": "https://products.cimpress.io/v1/products?q=blah&limit=25&offset=asdcawewer1231fc23d2ddqewdq23d2dascx23d2eda"},
    "last": null
  },
  "count": "25",
  "total": "null",
  "offset": "asdcawewer1231fc23d2ddqewdq23d2dascx23d2eda",
  "_embedded": {
    "item": [
      {
        "_links": {
          "detail": { "href": "https://products.cimpress.io/v1/products/4990a90a-acb5-416b-b3a3-69a71fb4c138"}
        },
        "id": "4990a90a-acb5-416b-b3a3-69a71fb4c138",
        ...
      },
      {
        "_links": {
          "detail": { "href": "https://products.cimpress.io/v1/products/5990a90a-acb5-416b-b3a3-69a71fb4c138"}
        },
        "id": "5990a90a-acb5-416b-b3a3-69a71fb4c138",
        ...
      }
    ]
  },
  ...
}
```
