# **JOSH** - JSON Over Simple HTTP

JOSH is a simple RPC protocol and
[IDL](https://en.wikipedia.org/wiki/Interface_description_language) with the
following features:

- **Simple**: Methods are invoked by POSTing JSON to a URL.
  [HTTPie](https://httpie.io/) (or [Curl](https://curl.se/) is all you need.
- **Polyglot**: Schema is constrained to satisfy a reasonable intersection
  of popular programming languages.
- **Codegen-ready**: The interface definition language includes inflection
  hints, so that appropriate identifiers can be generated with any casing style.
- **Reflective**: Interfaces and methods can be introspected via HTTP GET,
  which returns type information suitable for both humans and machines.
- **Interoperable**: Easily bridges to JSON-RPC or other JSON-based protocols.
- **Minimal**: The entire system should fit in your head. Clients, servers, and
  code generators should be easy to implement for any toolchain.

**Status:** Extremely Unstable.

EVERYTHING BELOW HERE IS UP IN THE AIR

# Interface Definition Language (IDL)

JOSH IDL files are encoded as JSON5 and are specified by a JSON schema (TBD).
There is one top-level key: `schemas`, is an object mapping names to schema
objects.

## Names

All names are kebab-case. This is URL-friendly, and allows for unamiguous
translation in to any other casing discipline, modulo acronyms.

Names must start with a letter and may only contain letters, numbers or dashes.
They may not end with a dash.

Names beginning with a `$` are especially reserved for meta-syntactic purposes.

## Schema Objects

A schema object has at least one key: `"kind"` with a string value.

Kinds are divided into two sorts: code and data. When schemas are recursive,
code schema objects may not be contained within data schema objects.

In some contexts, the kind of a schema object may be inferred from context.

TODO: Address nullability.

## Interfaces

An interface is a code schema object with `"kind": "interface"`.

Each interface has a `methods` object field, with values of schema-objects
implictly of kind "structure".

### Methods

A method is a code schema object of `"kind": "method"` with the following keys:

- `input`: An inline schema object implicitly of kind "structure".
- `output`: An inline schema object of any non-interface kind.
- `errors`: An inline schema object of kind `error-union`.

### Builtins

A data schema object with one of several predefined kinds:

- `int32`
- `float64`
- `string`
- `boolean`

TODO: How to handle Go's optionality and zero values?

### Array

A data schema object specifying a JSON array with a particular element type.

### Structure

A data schema object specifying a JSON object with a particular set of keys.

TODO: Address required attributes.

### Tagged Union

TBD

### Errors

Special case of tagged unions.

Errors have the following attributes:

- `status`: HTTP status code as an integer.
- `type`: Error Type Tag (TODO: Say more about these)
- `message`: One-line error message string.
- `data`: Additional Key/Value Data - Keys are strings. Values are given schemas.

## HTTP Protocol

An "object" is mounted at some URL. Objects and their methods can be introspected. 
invoke methods on it.

All routes respond with `Content-Type: application/json`.

### Objects: Introspection

A `GET` request to an object's URL returns an introspection file. This is a
service IDL file with an extra top level key: `interface`, which specifies the
interface schema object of this object. All schemas objects in the full service
interface that are not reachable from this interface object are expected to be
culled from the response.

TODO: Consider alowing methods to be invoked dynamically with a JSON-RPC like
request envelope.

### Methods: Invocation & Introspection

An object's URL + '/' + the kebab-case method name specifies a method's URL.

A `POST` request to a method URL invokes it.  The request is expected to have
`Content-Type: application/json`. The body is the arguments for the method as
specified by the input schema structure.  The response is the result for the
method as specified by the output schema.  interface 

A `GET` request to a method's URL returns a method introspection payload. This
is an object-introspection IDL file with an extra top-level key: `method`,
which specifies the method schema object. All interface objects not reachable
from this method are expected to be culled from the response.


### Headers

- TODO: Specify a request-id header.


## Tools, Implementations, Integrations, etc

- TODO: JavaScript/TypeScript Client & Server
- [Go Client & Server](https://github.com/deref/josh-go)
- TODO: JSON-SCHEMA mapping
- TODO: JSON-RPC mapping
- TODO: Embeddable documentation webapp.


## FAQ

### How does JOSH relate to JSON-RPC?

JSON-RPC is great! However, JSON is transport agnostic. That means that
JSON-RPC is useful in more situations than JOSH, but it also means that it
cannot take advantage of HTTP-specifics.

Future work may define a strict bidirectional mapping between JOSH and
JSON-RPC, so that JOSH interface definitions can be used in non-HTTP contexts.
