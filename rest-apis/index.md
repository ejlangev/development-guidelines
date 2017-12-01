# REST API Design

REST APIs are the basic unit of developing accessible backend software.
They are easy to standardize and can be a helpful development tool even
when you control all the clients that access your routes.

## Designing endpoints and routes

The purpose of an endpoint is to coordinate the validation of input
information, decide whether an action is allowed based on inputs
and the requesting user, pass that information off to code to
perform the requested operation, and then coordinate generating a
response.  These tasks should often be delegated to other objects e.g. [Form objects](../code-organization-patterns/index.md#form-objects)
for input validation, [Service objects](../code-organization-patterns/index.md#service-objects)/[Resource objects](../code-organization-patterns/index.md#resource-objects)
for performing tasks, [Policy objects](../code-organization-patterns/index.md#policy-objects) for determining permissions,
and [Presenter objects](../code-organization-patterns/index.md#presenter-objects)/[Serializer objects](../code-organization-patterns/index.md#serializer-objects) for making responses.

Endpoints should generally follow the setup of resources which come in two forms: singular and plural resources.

### Plural resources

A plural resource is something that you have multiple instances of at
any time.  The canonical example of this is posts on a forum.  Endpoints
designed around a post should take the following form:

* `GET /posts` - This returns a list of all posts, if needed you can use
  query params to provide filters e.g. `GET /posts?author_id=:id`.
* `POST /posts` - This creates a new post where the data is included in
  the body of the request.
* `PUT /posts/:id` - This edits a specific post data comes from the body
  of the request in the same format as it would for creating a new post.
* `DELETE /posts/:id` - This removes a specific post by id.

### Singular resources

A singular resource is something that you can only have a single
instance of at any time.  A simple example is a profile resource that
corresponds one to one with a given user.  Endpoints designed for this
should take the following form:

* `GET /profile` - Returns the single profile
* `POST /profile` - Creates a new profile
* `PUT /profile` - Updates the existing profile
* `DELETE /profile` - Removes the existing profile

### Visibility and identifiers

Often times data has a sequential numeric column as the primary key and
we generally use that for accessing it.  However, this opens up a
possibility of a user checking for other records by incrementing the
number e.g. in the url `GET /posts/5` it is fairly clear that `GET
/posts/6` could also be valid.  To prevent this it is good practice to
use non-guessable identifiers for your API that you associate with your
records for this purpose.  Often these take the form of UUIDs.  Using a
non guessable identifier externally could prevent someone from
incrementing a numeric id to access a disallowed resource though it is
important to note it is not a substitute for explicitly preventing this
case in code.

### Nested resources

Sometimes certain resources only really make sense in the context of
another resource which provides some critical information.  Examples of
this include posts or a profile nested underneath a user.  In those
cases it might make sense to express that dependency through the URL
e.g. as `/users/:id/posts` or `/users/:id/profile`.

## Response formats

Consistent response formats and status codes are critical for making
your API easy to use both internally and externally. JSON typically uses
snake case e.g. `some_resource` for field names.  Examples:

### Bulk load of posts

```js
// GET /posts

{
  "posts": [
    {
      id: 1,
      body: "Hello, world",
    },
    {
      id: 2,
      body: "Goodbye, world",
    }
  ]
}
```

### Single load of a profile

```js
// GET /profile
{
  "profile": {
    "id": 1,
    "first_name": "Hello",
    "last_name": "World"
  }
}
```

## Status codes

### 200 - Ok

Used for basic GET requests that complete successfully and are returning
data.

### 201 - Created

Used for POST requests that result in the creation of a new object.

### 202 - Accepted

Used for PUT requests that edit an existing object.

### 204 - No content

Used for DELETE requests that have no content to return.

### 400 - Bad request

Use this when the structure of the request does not match what is
expected either because parameters are missing or in the wrong format or
extra parameters are specified.  Ideally the error response contains the
details of which fields are missing or extraneous.

### 401 - Unauthorized

Use this when the request comes from a user who is not logged in.  This
is distinct for the question of if the user has access to the operation
in question.

### 403 - Access denied

Use this when the user is logged in but they do not have access to do an
operation on the resource in question.

### 404 - Not found

Use this when attempting to read or modify some resource that cannot be
found.

### 500 - Server error

Use this when something unexpected happens that you cannot anticipate or
react to.

## Versioning

Versioning is useful when you do not control all the clients to your API.  This always happens with public APIs but can also happen when you ship clients into the world that you cannot update e.g. phone apps. There are three main ways to version an API, generally should prefer using headers to using the URL but any should serve.

### Links

- [Stripe API versioning](https://stripe.com/blog/api-versioning)

### URL versioning

This is the most obvious option which would mean maintaining URLs like
`https://mysite.com/api/v1/some-resources` and
`https://mysite.com/api/v2/some-resources`.  The downside of this is
that you're using the location of the resource to encode the version
which is not strictly related to the purpose of the URL.  For this
reason using headers is generally preferred.

### Custom header versioning

Another strategy is to include a custom header e.g. `X-Api-Version` that
enocdes which version of the API the request should use, generally
defaulting to the latest version.  The tricky part about a custom header
is that it isn't standardized and part of any spec so it is possible to
end up with different names in different places.  Overall this method is
a solid approach to take.

### Accept header versioning

A third strategy is to encode the version you want into the Accept
header making it look something like: `Accept: application/your-app.v2+json`.
The advantage here is that the Accept header already has a semantic
meaning that you don't have to override.

## Documenting

Documenting your API is important especially if it is used externally
but can also be much quicker than using the code for internal users as
well.  Some potential tools:

* [Open API](https://github.com/OAI/OpenAPI-Specification)
* [Slate](https://github.com/lord/slate)

Some tools can allow for generating API clients automatically targeting
different languages as well which can be useful.
