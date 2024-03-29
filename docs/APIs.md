# APIs
{:.no_toc}

* A markdown unordered list which will be replaced with the ToC, excluding the "Contents header" from above
{:toc}

_(c) AMWA 2023, CC Attribution-NoDerivatives 4.0 International (CC BY-ND 4.0)_

## API Specifications

The Stream Compatibility Management API is specified using:

- The following sub-sections describing common NMOS API properties.
- [RAML](http://raml.org/) documents and [JSON schemas](http://tools.ietf.org/html/draft-zyp-json-schema-04) in the [APIs](../APIs/) folder.
- Normative text in files in the [docs](../docs) directory.

Examples of JSON format output are provided in the [examples](../examples/) folder.

## API Validation

JSON schemas are included with the RAML API definitions. These include validation for values used within the APIs. It is RECOMMENDED that implementers of a Stream Compatibility Management API use these JSON schemas as part of a validation stage when receiving requests from clients. Invalid requests SHOULD cause a `400` (Bad Request) HTTP error to be returned to the client.

### Content Types

All APIs MUST provide a JSON representation signalled via `Content-Type: application/json` headers. This SHOULD be the default content type in the absence of any requested alternative by clients. Other content types (such as HTML) are permitted if they are explicitly requested via `Accept` headers.

### API Paths

All NMOS APIs MUST use a path in the following format. Other HTTP resources MAY be presented on the same port by the Node, provided all NMOS resources are available from the `/x-nmos/` path as follows:

```
http(s)://<ip address or hostname>:<port>/x-nmos/<api type>/<api version>/
```

At each level of the API from the base resource upwards, the response SHOULD include a JSON format array of the child resources available from that point.

### Versioning

All public APIs are versioned as follows:

- Requesting the API base resource (such as `http(s)://<ip address or hostname>:<port>/x-nmos/streamcompatibility/`) will provide a list containing the versions of the API present on the node.
- A versioned API response MUST include only resources which match the schema for that API version.
- Data which is held for mismatched minor API versions MAY be returned if it can be conformed to the correct schema (see example below). Data MUST NOT be conformed between major API versions.

**Example**

A v1.1 API response can include:

- v1.1 data without modification.
- Data conforming to schemas less than v2.0 but greater than v1.1, with any non-v1.1 keys removed.

### Common API Base Resource

```json
[
  "v1.0/",
  "v2.0/",
  "v3.0/"
]
```

- Appending `/v1.0/` to the API base resource will request the version v1.0 of the API if available.
- The versioning format is `v<MAJOR>.<MINOR>`
- `MINOR` increments will be performed for non-breaking changes (such as the addition of attributes in a response)
- `MAJOR` increments will be performed for breaking changes (such as the renaming of a resource or attribute)
- Versions MUST be represented as complete strings. Parsing MUST proceed as follows: separate into two strings, using the point (`.`) as a delimiter. Compare integer representations of `MAJOR`, `MINOR` version (such that v1.12 is greater than v1.5).
- Clients are responsible for identifying the correct API version they require.

### URLs: Approach to Trailing Slashes

For consistency and in order to adhere to how these APIs are specified in RAML, the 'primary' path for every resource has the trailing slash omitted.

In order to overcome shortcomings in some common libraries, the following requirements are imposed for the support of URL paths with or without trailing slashes.

#### `GET` and `HEAD` Requests

- Clients performing requests using these methods SHOULD correctly handle a `301` (Moved Permanently) response.
- When a `301` is supported, the client MUST follow the redirect in order to retrieve the response payload.
- Servers implementing the APIs MUST support requests using these methods to both the trailing slash and non-trailing slash path to each resource. One of these MAY produce a `301` response causing a redirect to the other if preferred.

#### All Other Requests (`PUT`, `POST`, `DELETE`, `OPTIONS`, etc.)

- Clients performing requests using these methods MUST use URLs with no trailing slash present.
- Servers implementing the APIs MUST correctly interpret requests using these methods to paths without trailing slashes present.
- Servers implementing the APIs MAY correctly interpret requests using these methods to paths with trailing slashes present for backward compatibility.
- Servers SHOULD NOT respond with `3xx` codes for these request types.

#### Client Behaviour

When a server implementation needs to indicate an API URL via an `href` or similar attribute, it is valid to either include a trailing slash or not provided that the listed path is accessible and follows the above rules. Clients appending paths to `href` type attributes MUST support both cases, avoiding doubled or missing slashes.

### Error Codes & Responses

The NMOS APIs use HTTP status codes to indicate success, failure and other cases to clients as per [RFC 7231](https://tools.ietf.org/html/rfc7231) and related standards. Where the RAML specification of an API specifies explicit response codes it is expected that a client will handle these cases in a particular way. Explicit handling of every possible HTTP response code is not expected, but clients are expected to implement generic handling for ranges of response codes (`1xx`, `2xx`, `3xx`, `4xx` and `5xx`).

For HTTP codes `400` and upwards, a JSON format response MUST be returned as follows:

**Example Error Response**

```json
{
  "code": 400,
  "error": "Human readable message which is suitable for user interface display, and helpful to the user",
  "debug": "Programmer / debugging detail or traceback"
}
```

`code` SHOULD match the HTTP status code. `error` MUST always be present and in string format. `debug` MAY be `null` if no further debug information is available.

Further details on when APIs will respond with particular codes is covered in the [Behaviour](Behaviour.md) section.
