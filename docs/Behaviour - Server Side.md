# Behaviour: Server Side

## Active Constraints of Sender

The initial state of Active Constraints of a Sender MUST be an empty `constraint_sets` array. This state indicates a Sender is unconstrained.

Non-empty `constraint_sets` array MUST be treated as unordered unless at least one Constraint Set has `urn:x-nmos:cap:meta:preference` attribute.

Once a Sender accepts proposed Active Constraints, this Sender, the Flow and the Connection API `/transportfile` resource (if used) associated with this Sender and the Source associated with this Flow MUST satisfy the Active Constraints when the Sender is active. At any time if the Source, the Flow, the `/transportfile` (if used) and the active Sender cannot satisfy the Active Constraints, the Sender MUST NOT transmit the Flow over the network. The Sender is REQUIRED to take the following actions:

- MUST set the [IS-05][IS-05] `/active`'s `master_enable` property to `false`.
- MUST update the [IS-04][IS-04] `subscription`'s `active` property to `false` (regardless of unicast or multicast).

Regardless whether a constrained Sender is active, if this Sender, the Flow or the `/transportfile` (if used) associated with this Sender or the Source associated with this Flow does not satisfy the Active Constraints, Sink Metadata Processing API MUST respond with `205 Reset Content` to `GET /constraints` requests. This response SHOULD persist until the Source, the Flow, the `/transportfile` (if used) and the Sender satisfy the Active Constraints.

When a Sender is in this state, it MUST NOT allow [IS-05][IS-05] activations.

### Request Methods

- `GET` request returns the last successfully applied Active Constraints.
- `PUT` request MUST be validated and if the Sender supports Parameter Constraints used in the proposed Active Constraints and is capable to adhere at least one of the Constraint Sets, then the associated Inputs may be reconfigured, which may eventually cause the update of the corresponding Flows, Sources, `/transportfile` and Sender. If successful, the operation MUST return the accepted Active Constraints, otherwise an error MUST be returned and nothing changed.
- `DELETE` request MUST clear Active Constraints. It turns the Sender to the initial state which excludes IS-11 out of the process.

## Receivers

If an active Receiver becomes unable to handle Flow, it is REQUIRED to take the following actions:
- MUST set the [IS-05][IS-05] `/active`'s `master_enable` property to `false`.
- MUST update the [IS-04][IS-04] `subscription`'s `active` property to `false` (regardless of unicast or multicast).

## Inputs

### Properties

The `/properties` endpoint shows properties of the Input. In particular, it MAY contain information parsed from Base EDID and Effective EDID if the Input supports EDID and the Node is capable to parse EDID binaries.

### Base EDID

The initial state of Base EDID is such an EDID which this Input presents to the upstream counterpart if Active Constraints are empty.

`GET /edid/base` and `PUT /edid/base` operations allow a client to download and replace the Base EDID respectively if it exists. `DELETE /edid/base` resets Base EDID to the initial state. If the Base EDID for the Input changes and it is associated with any Senders, then all of the Senders in question MUST update their versions (in registered mode this MUST update the registered resources).

### Effective EDID

Effective EDID is such combination of Base EDID and Active Constraints of all Senders associated with this Input that the baseband signal from the Input can be transmitted by the Senders without breaking Active Constraints.

If Active Constraints are empty, then Effective EDID is the same as Base EDID.

The `/edid/effective` endpoint allows a client to download the Effective EDID if it exists. If the Effective EDID for the Input changes and it is associated with any Senders, then all of the Senders in question MUST update their versions (in registered mode this MUST update the registered resources).

## Outputs

### Properties

The `/properties` endpoint shows properties of the Output. In particular, it MAY contain information parsed from EDID if it is present and the Node is capable to parse EDID binaries.

### EDID

The `/edid` endpoint allows a client to download the binary EDID if it exists. If the EDID information for the Output changes and it is associated with any Receivers, then all of the Receivers in question MUST update their versions (in registered mode this MUST update the registered resources).

[IS-04]: https://specs.amwa.tv/is-04/
[IS-05]: https://specs.amwa.tv/is-05/
