# Interoperability

The Sink Metadata Processing Specification was designed to be used alongside the following NMOS specifications:

* IS-04 Discovery & Registration
* IS-05 Connection Management
* BCP-004-01 NMOS Receiver Capabilities
* BCP-005-01 NMOS EDID to Receiver Capabilities Mapping

IS-05 dictates that IS-04 APIs SHOULD be operating at version 1.2 or greater in order to ensure full interoperability and Sink Metadata Processing Specification inherits this requirement.

Receivers listed under IS-11 `/receivers` endpoint MUST use BCP-005-01 to express their Receiver Capabilities.

## Discovery

The Sink Metadata Processing API should be advertised as a ‘control’ endpoint under an IS-04 NMOS Device in the controls array using the `urn:x-nmos:control:sink-mp` type. For consistency the href URL offered should always end with a trailing slash.

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
            "href": "http://hostname/x-nmos/connection/v1.1/"
        },
        {
            "type": "urn:x-nmos:control:sink-mp/v1.0",
            "href": "http://hostname/x-nmos/sink-mp/v1.0/"
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

The UUIDs used to advertise Senders and Receivers in the Sink Metadata Processing API must match those used in a corresponding IS-04 implementation.

## Version Increments

In order to prevent unnecessary polling of the Sink Metadata Processing API, changes to Inputs and Outputs are signalled via the IS-04 versioning mechanism. When the Input/Output parameters of a Sender/Receiver are modified, or when set of Inputs/Outputs associated with the Sender/Receiver is changed, the `version` attribute of the relevant IS-04 Sender/Receiver MUST be incremented.

If `/media-profiles` PUT or DELETE operation applied to a Sender causes a change of Inputs, Source or Flow, their versions MUST be incremented as well. If such a change causes subsequent changes to the Connection API `transport_file`, version of the relevant IS-04 Sender MUST be incremented as IS-05 dictates.