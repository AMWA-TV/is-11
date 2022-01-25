# Behaviour: Server Side

## Active Constraints of Sender

The initial state of Active Constraints of a Sender MUST be an empty `constraint_sets` array. This state indicates a Sender is unconstrained.

Non-empty `constraint_sets` array MUST be treated as unordered unless at least one Constraint Set has `urn:x-nmos:cap:meta:preference` attribute.

Active Constraints are satisfied when any of its Constraint Sets are satisfied in terms of BCP-004-01.

## State of Sender

A Sender managed with IS-11 has the following states:
- `Unconstrained` when Active Constraints of this Sender is an empty `constraint_sets` array.
- `Constrained` when the Active Constraints are satisfied.
- `Active Constraints Violation` when at least one Parameter Constraint of each Constraint Set is not satisfied.
- `No Signal` when there is no signal from Inputs associated with this Sender.
- `Awaiting Signal` when Inputs associated with this Sender were reconfigured recently and the signal is not stable yet. This is a transitional state until one of the previous ones can be established. It is RECOMMENDED to not update such associated resources as Flow and Source while this Sender is in this state.

## State of Receiver

A Receiver managed with IS-11 has the following states:
- `No Transport File` when there's no active transport file.
- `OK` when this Receiver's active transport file does not violate Receiver Capabilities.
- `Receiver Capabilities Violation` when this Receiver's active transport file violates Receiver Capabilities.

## Preventing restrictions violation

At any time if State of an active Sender becomes `Active Constraints Violation`, the Sender MUST NOT transmit the Flow over the network. The Sender is REQUIRED to take the following actions:
- MUST set the [IS-05][IS-05] `/active`'s `master_enable` property to `false`.
- MUST update the [IS-04][IS-04] `subscription`'s `active` property to `false` (regardless of unicast or multicast).

At any time if State of an active Receiver becomes `Receiver Capabilities Violation`, the Receiver MUST stop receiving the Flow by taking the following actions:
- MUST set the [IS-05][IS-05] `/active`'s `master_enable` property to `false`.
- MUST update the [IS-04][IS-04] `subscription`'s `active` property to `false` (regardless of unicast or multicast).

## Signal altering indication

A Device SHOULD provide parent Flows and parent Sources to a Sender's Flow and associated Source if the signal being transmitted from the Sender is not identical to the signal being captured from the Input(s).

## Inputs

### Properties

The `/properties` endpoint shows properties of the Input. In particular, it MAY contain information parsed from Base EDID (if set) and Effective EDID if the Input supports EDID and the Node is capable to parse EDID binaries.

### Base EDID

There is no Base EDID at the initial state. If Base EDID for an Input changes, then all Senders associated with this Input MUST update their versions (in registered mode this MUST update the registered resources).

### Effective EDID

Effective EDID is such combination of Base EDID and Active Constraints of all Senders associated with this Input that the baseband signal from the Input can be transmitted by the Senders without breaking Active Constraints.

If Base EDID is not set, Effective EDID is built on basis of a default Effective EDID defined for the Input by manufacturer.

The `/edid/effective` endpoint allows a client to download the Effective EDID if it exists. If the Effective EDID for the Input changes and it is associated with any Senders, then all of the Senders in question MUST update their versions (in registered mode this MUST update the registered resources).

## Outputs

### Properties

The `/properties` endpoint shows properties of the Output. In particular, it MAY contain information parsed from EDID if it is present and the Node is capable to parse EDID binaries.

### EDID

The `/edid` endpoint allows a client to download the binary EDID if it exists. If the EDID information for the Output changes and it is associated with any Receivers, then all of the Receivers in question MUST update their versions (in registered mode this MUST update the registered resources).

[IS-04]: https://specs.amwa.tv/is-04/
[IS-05]: https://specs.amwa.tv/is-05/
