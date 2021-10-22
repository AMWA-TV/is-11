# API

The Sink Metadata Processing API provides a mechanism to change the settings associated with logical Inputs, Sources, Flows and Senders. Another use case is obtaining information about Outputs associated with Receivers.

## Senders

The `/inputs` endpoint returns a list of IDs corresponding to Inputs associated to the specific Sender. If this list changes then the associated [IS-04][IS-04] Sender MUST update its version (in registered mode this MUST update the registered resource).

The `/media-profiles` endpoint allows the client to pass an array of [Media Profiles](Overview.md#media-profile) to a Sender. The sender can then make these restrictions visible to its associated Inputs. The Inputs let the Source and Flow to choose exactly which format from the set they will use. A client can pass a single Media Profile to specify the exact format.

### Role of Media Profiles

The initial state of Media Profiles of a Sender MUST be an empty JSON array `[]`. This state indicated a Sender is unbound by a Media Profile. Creating a connection with such a Sender via [IS-05][IS-05] is not impacted by the Sink Metadata Processing API.

Non-empty array of Media Profiles MUST be treated as unordered unless `preference` property exists.

Once an array of Media Profiles is accepted, a Sender MUST have a Flow and Source which meet at least one Profile. This constraint MUST be adhered to when an external change (i.e. not from the `/media-profiles` endpoint) modifies any aspect of the stream. If the change does not satisfy its Media Profiles, the Sender MUST NOT transmit the signal over the network. The Sender is REQUIRED to take the following actions:

- MUST set the [IS-05][IS-05] `/active`'s `master_enable` property to `false`.
- MUST update the [IS-04][IS-04] `subscription`'s `active` property to `false` (regardless of unicast or multicast).
- MUST respond with `205 Reset Content` when answering `GET /media-profiles` to indicate it is no longer following or adhering to a set. This response SHOULD persist until the next valid `PUT /media-profiles` request.

When a Sender is in this state, ie. responding with a `205 Reset Content`, this indicates a Sender is unbound by a Media Profile and is similar to the initial state.

### Request Methods

- A `GET` request returns the last successfully applied Media Profiles array.
- A `PUT` request MUST be validated and if the Sender supports the proposed Media Profiles, the associated Inputs may be reconfigured to adhere to the Media Profiles constraints, which may eventually cause the update of the corresponding Flows, Sources, and Sender. If successful, the operation MUST return the accepted Media Profiles array, otherwise an error MUST be returned and nothing changed.
- A `DELETE` request MUST clear the last successfully applied Media Profiles array. It turns Media Profiles to the initial state which exclude Sink Metadata Processing out of the process. It is RECOMMENDED to be performed by a client after disconnecting all Receivers from an according Sender.

When the `/media-profiles` structure changes as a consequence of PUT or DELETE (request is validated and accepted by the API server), then the associated [IS-04][IS-04] Sender MUST have its version updated (in registered mode this MUST update the registered resource).

### Integration with IS-04

If the new `/media-profiles` structure causes the Sender or underlying Flow, Source or Inputs to change, then their versions MUST be updated (in registered mode this MUST update the registered resources).

## Receivers

The `/outputs` endpoint returns a list of IDs corresponding to Outputs associated to the specific Receiver. If this list changes then the underlying [IS-04][IS-04] Receiver MUST update its version (in registered mode this MUST update the registered resource).

If Receiver becomes unable to handle Flow, it is REQUIRED to take the following actions:
- MUST set the [IS-05][IS-05] `/active`'s `master_enable` property to `false`.
- MUST update the [IS-04][IS-04] `subscription`'s `active` property to `false` (regardless of unicast or multicast).

## Inputs

The `/edid` endpoint allows a client to download the effective EDID if it exists. If the EDID information for the Input changes and it is associated with any Senders, then all of the Senders in question MUST update their versions (in registered mode this MUST update the registered resources).

## Outputs

The `/edid` endpoint allows a client to download the binary EDID if it exists. If the EDID information for the Output changes and it is associated with any Receivers, then all of the Receivers in question MUST update their versions (in registered mode this MUST update the registered resources).

[IS-04]: https://specs.amwa.tv/is-04/
[IS-05]: https://specs.amwa.tv/is-05/
