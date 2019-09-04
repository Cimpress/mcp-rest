<span id="title-text"> RESTful Best Practices </span>
=============================================================================

Abstract
========

Status

**Version 1.0**

This document recommends best practices for development of REST APIs at
Cimpress. Our goals in offering these best practices are:

-   To build an API platform that is easy for our customers to use —
    recognizing that our customers come first.
-   To foster consistency among independently developed APIs —
    recognizing that our platform is built by many different squads with
    different backgrounds and technology stacks.
-   To help avoid common maintainability and compatibility pitfalls —
    recognizing that our customers should expect long term stability of
    our services and their interfaces.

Scope
=====

All RESTful JSON APIs developed by Cimpress Technology and exposed to
businesses outside of the Cimpress Technology Organization.

All RESTful JSON APIs developed by Cimpress-owned businesses and exposed
to businesses as part of Cimpress Technology managed software products
(such as ABC or MCP).

APIs that are required to comply or match 3rd party API specifications
are exempt from these requirements.

Context
=======

**Why are we doing this?**

-   Interfaces to customers that are not consistent between teams or
    business domains are not easy to use. Customers
    like things that are easy to use.
-   Our customer base will continue to grow.
-   We aspire to take our APIs public and charge for them. People won't
    pay for things that aren't easy to use or consistent. 
-   Designing an API is hard, and we've all messed it up. A set of best
    practices will increase shared knowledge around the subject.

**Why is this important?**

-   As a platform matures, ease of use becomes a bigger
    barrier to adoption. We believe by having base levels of consistency
    across our interfaces, we're able to improve usability and increase
    adoption.
-   Consistency is important for perception. Even if our software is
    great and solves exactly the needs our business needs solved, if the
    interfaces are inconsistent or hard to use the very developers
    charged with developing against it will message that it's difficult
    to use. While we can't remove that pain entirely, increasing
    consistency and providing common patterns will lessen the adoption
    costs.

**Why should I listen?**

-   Customers desire consistency to drive business value. Driving
    business value for our customers is more important than any
    particular design choice in an API.
-   Designing good APIs is hard. We want to provide direction for the
    average developer, and a yardstick to measure against. Designing
    good APIs also takes a lot of time, and debating the finer points of
    RESTful design tradeoffs is time not spent actually implementing
    what our customers need from us. Our desire is to focus the time
    spent on API design around how to model specific domains and
    business concepts.
-   When crafting this document we made no judgement of styles, or base
    our decisions off existing APIs. We believe these patterns are
    generally applicable and appropriate to all REST APIs, and have
    tried to justify our reasoning. The drafters of the document often
    specifically made choices they didn't like at a personal level, but
    would agree it was right for Cimpress as a whole.

**Why did you chose *X* over *Y* when Z/Fielding/HATEOS/W3C/Bill Gates
says...?**

-   We've specifically chosen to be pragmatists given our experience
    with many of our customers. <span style="color: rgb(23,43,77);">Mass
    customization</span> businesses are generally not technology
    organizations, and we have tried to keep that in mind by adopting
    simple and pragmatic choices. In some cases, complexity is required
    to solve a particular problem, but we always should strive to chose
    the most pragmatic choice for our customers. Where we've chosen a
    more complex option, we've typically done it to help avoid common
    maintainability or compatibility concerns. 

**Why an RFC? How should I use this document? How will this be
enforced?**

-   This document should be considered a list of best practices that
    developers can consult.
-   We want to provide [example
    APIs](11-Examples).
-   We want to consult with teams and provide them specific guidance on
    how to make their APIs conform.
-   We would like to provide a swagger validator in the developer portal
    to help validate elements of this RFC, and let you issue a "grade"
    for your API. 
  

Contents
========

### [1 Terminology](1-Terminology)

### [2 API Versioning](2-API-Versioning)

### [3 JSON Conventions](3-JSON-Conventions)

### [4 Resource Names and Locations](4-Resource-Names-and-Locations)

### [5 Resource Relationships](5-Resource-Relationships)

### [6 Resource Operations](6-Resource-Operations)

### [7 Asynchronous Requests](7-Asynchronous-Requests)

### [8 Error Responses](8-Error-Responses)

### [9 Response Status Codes](9-Response-Status-Codes)

### [10 Headers](10-Headers)

### [11 Examples](11-Examples)


References
==========

-   The
    <a href="https://docs.commercetools.com/http-api.html" class="external-link">commercetools API documentation</a>
    is a great example of a well-documented set of APIs that follows
    RESTful practices where possible but also has pragmatic views on
    things that are difficult in REST.
-   The
    <a href="https://cloud.google.com/apis/design/" class="external-link">Google Cloud API Design Guide</a>
    provided inspiration for parts of this document.
-   The
    <a href="https://stripe.com/docs/api" class="external-link">stripe API</a>
    is very consistent and has some great thoughts around how to support
    common features across a varied domain, but has some quirks that
    complicate it's use (Expansion, Idempotency, Versioning). 
-   Apigee's
    <a href="https://pages.apigee.com/rs/apigee/images/api-design-ebook-2012-03.pdf" class="external-link">API Design book</a>
    has some good background on API designs, though it is a bit dated.
-   <a href="https://tools.ietf.org/html/rfc7231" class="external-link">RFC-7231</a>
    provides some clarity around how RESTful APIs should work with HTTP.
