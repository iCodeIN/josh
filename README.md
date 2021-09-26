# **JOSH** - JSON Over Simple HTTP

JOSH is a simple RPC protocol and
[IDL](https://en.wikipedia.org/wiki/Interface_description_language) with the
following features:

- **Simple**: Methods are invoked by POSTing JSON to a URL.
  [HTTPie](https://httpie.io/) (or [Curl](https://curl.se/)) is all you need.
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

Work in progress on a specification is here:
[./specificaton.md](./specification.md).

## Tools, Implementations, Integrations, etc

- [JavaScript/TypeScript Implementation](https://github.com/deref/josh-js)
- [Go Implementation](https://github.com/deref/josh-go)
- TODO: Mapping from JOSH-IDL to JSON-SCHEMA.
- TODO: Embedding of JOSH in JSON-RPC.
- TODO: Middleware for HTML-based introspection (ie. documentation webapp).


## FAQ

### How does JOSH relate to JSON-RPC?

JSON-RPC is great! However, JSON is transport-agnostic and lacks an interface
definition language (IDL). Being transport-agnostic means that JSON-RPC is
useful in more situations than JOSH, but it also means that it cannot take
advantage of HTTP-specifics such as URL-based method routing. Lacking an IDL
means that you need an additional tool to define types.

Future work may define a strict bidirectional mapping between JOSH and
JSON-RPC, so that JOSH interface definitions can be used in non-HTTP contexts.
