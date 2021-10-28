# Behaviour: Server Side

## Constraints of Sender

The initial state of Constraints of a Sender MUST be an empty `constraint_sets` array. This state indicates a Sender is unconstrained. Creating a connection with such a Sender via [IS-05][IS-05] is not impacted by IS-11.

Non-empty `constraint_sets` array MUST be treated as unordered unless at least one Constraint Set has `urn:x-nmos:cap:meta:preference` attribute.

Once Constraints are accepted, a Flow and a Source of the specific Sender MUST NOT break the Constraints. This commitment MUST be adhered to when an external change (i.e. not from the `/constraints` endpoint) modifies any aspect of the stream. If the change does not satisfy the Constraints, the Sender MUST NOT transmit the signal over the network. The Sender is REQUIRED to take the following actions:

- MUST set the [IS-05][IS-05] `/active`'s `master_enable` property to `false`.
- MUST update the [IS-04][IS-04] `subscription`'s `active` property to `false` (regardless of unicast or multicast).
- MUST respond with `205 Reset Content` when answering `GET /constraints` to indicate it is no longer following or adhering to the Constraints. This response SHOULD persist until the next valid `PUT` or `DELETE /constraints` request or an external change which leads to the Sender adheres the Constraints.

When a Sender is in this state, it MUST NOT allow [IS-05][IS-05] activations.

## Request Methods

- `GET` request returns the last successfully applied Constraints.
- `PUT` request MUST be validated and if the Sender is capable to adhere the proposed Constraints, the associated Inputs may be reconfigured, which may eventually cause the update of the corresponding Flows, Sources, and Sender. If successful, the operation MUST return the accepted Constraints, otherwise an error MUST be returned and nothing changed.
- `DELETE` request MUST clear Constraints. It turns the Sender to the initial state which excludes IS-11 out of the process.

## Receivers

If an active Receiver becomes unable to handle Flow, it is REQUIRED to take the following actions:
- MUST set the [IS-05][IS-05] `/active`'s `master_enable` property to `false`.
- MUST update the [IS-04][IS-04] `subscription`'s `active` property to `false` (regardless of unicast or multicast).

## Inputs

The `/edid` endpoint allows a client to download the effective EDID if it exists. If the EDID information for the Input changes and it is associated with any Senders, then all of the Senders in question MUST update their versions (in registered mode this MUST update the registered resources).

## Outputs

The `/edid` endpoint allows a client to download the binary EDID if it exists. If the EDID information for the Output changes and it is associated with any Receivers, then all of the Receivers in question MUST update their versions (in registered mode this MUST update the registered resources).

[IS-04]: https://specs.amwa.tv/is-04/
[IS-05]: https://specs.amwa.tv/is-05/
