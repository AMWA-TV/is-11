# Interoperability

The Stream Compatibility Management Specification was designed to be used alongside the following NMOS specifications:

* IS-04 Discovery & Registration
* IS-05 Connection Management
* BCP-004-01 NMOS Receiver Capabilities
* BCP-005-01 NMOS EDID to Receiver Capabilities Mapping

IS-05 dictates that IS-04 APIs SHOULD be operating at version 1.2 or greater in order to ensure full interoperability and the Stream Compatibility Management Specification inherits this requirement.

Any references to terms and JSON Schemas defined in BCP-004-01 relate to version 1.0.

Receivers, managed with IS-11 and associated with Outputs which support EDID, SHOULD use BCP-005-01 to express Receiver Capabilities that came from the EDID(s) and BCP-004-01 to express their own Receiver Capabilities. The inherent Receiver Capabilities prevail over the EDID associated Capabilities. An EDID can further restrict inherent Receiver Capabilities but cannot expand them.

## Discovery

The Stream Compatibility Management API SHOULD be advertised as a `control` endpoint under an IS-04 NMOS Device in the controls array using the `urn:x-nmos:control:stream-compat` type. For consistency the href URL offered SHOULD always end with a trailing slash.

Example:

```json
{
    "senders": [
        "79ceab1f-a0e7-42e9-967e-7d766c009d77",
        "e2495aa8-a828-4c7d-ad68-32a0db6c908b"
    ],
    "receivers": [
        "e0f6e7d3-4d8f-40b7-9ff4-c15993bad770",
        "575ae98f-77c6-4a6a-a6ee-592e6be1bb4b"
    ],
    "controls": [
        {
            "type": "urn:x-nmos:control:sr-ctrl/v1.1",
            "href": "http://api.example.com/x-nmos/connection/v1.1/"
        },
        {
            "type": "urn:x-nmos:control:stream-compat/v1.0",
            "href": "http://api.example.com/x-nmos/streamcompatibility/v1.0/"
        }
    ],
    "tags": {},
    "type": "urn:x-nmos:device:generic",
    "label": "Example Device",
    "version": "1529676926:000000000",
    "node_id": "3309f357-9676-439a-bb64-8106f934bd08",
    "id": "ab79e404-1bca-4557-8afc-0ddf28b1e1e7",
    "description": "Example Device"
}
```

## Sender and Receiver IDs

The UUIDs used to advertise Senders and Receivers in the Stream Compatibility Management API must match those used in a corresponding IS-04 implementation.

## Version Increments

In order to prevent unnecessary polling of the Stream Compatibility Management API, changes are signaled via the IS-04 versioning mechanism of the associated Sender and Receiver resources. E.g. when properties of any Input/Output associated with the Sender/Receiver are modified, when the set of Inputs/Outputs associated with the Sender/Receiver is changed, or when the Status of a Sender/Receiver is updated, then the `version` attribute of the relevant IS-04 Sender/Receiver MUST be incremented.

If properties of any resource such as Source, Flow, Sender, Receiver, etc. change over its lifetime, then its `version` attribute MUST be incremented as well.
