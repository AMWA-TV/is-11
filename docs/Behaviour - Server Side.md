# Behaviour: Server Side

## Active Constraints of Sender

The initial state of Active Constraints of a Sender MUST be an empty `constraint_sets` array. This state indicates a Sender is unconstrained.

Non-empty `constraint_sets` array MUST be treated as unordered unless at least one Constraint Set has `urn:x-nmos:cap:meta:preference` attribute.

Active Constraints are satisfied when any of its Constraint Sets are satisfied in terms of [BCP-004-01][].
Active Constraints are violated when at least one Parameter Constraint of each Constraint Set is not satisfied in terms of [BCP-004-01][].

## Supported Constraints of Sender

A Sender MUST present the list of supported constraint set metadata and parameter constraints in the `/supported` endpoint. This list MUST include at least the constraint set metadata from the following list and at least those parameter constraints from the following list which are applicable to the format of the Flow associated with this Sender:
- `urn:x-nmos:cap:meta:label`
- `urn:x-nmos:cap:meta:preference`
- `urn:x-nmos:cap:meta:enabled`
- `urn:x-nmos:cap:format:media_type`
- `urn:x-nmos:cap:format:grain_rate`
- `urn:x-nmos:cap:format:frame_width`
- `urn:x-nmos:cap:format:frame_height`
- `urn:x-nmos:cap:format:interlace_mode`
- `urn:x-nmos:cap:format:color_sampling`
- `urn:x-nmos:cap:format:component_depth`
- `urn:x-nmos:cap:format:channel_count`
- `urn:x-nmos:cap:format:sample_rate`
- `urn:x-nmos:cap:format:sample_depth`

## Status of Input

An Input has the following states:
- `no_signal` when there is no incoming signal to this Input.
- `awaiting_signal` when the incoming signal is not stable. This is a transitional state until one of the other ones can be established.
- `signal_present` when the incoming signal is present and stable.

Debug information expressed through `debug` property SHOULD assist `no_signal` state and MAY assist the other states.

## Status of Sender

A Sender managed with IS-11 has the following states:
- `unconstrained` when Active Constraints of this Sender is an empty `constraint_sets` array.
- `constrained` when the Active Constraints are satisfied.
- `active_constraints_violation` when Active Constraints are violated.
- `no_essence` when the associated Source is not providing any Essence for this Sender to transmit, which, if the Sender has associated Inputs, can mean that one or more of these Inputs do not have a valid incoming signal.
- `awaiting_essence` when the Essence, which the associated Source is providing, is not stable for this Sender to transmit. This is a transitional state until one of the other ones can be established. It is RECOMMENDED to not update resources such as Flow and Source associated with a Sender while in this state.

`no_essence` and `awaiting_essence` states MUST depend on the state of the Inputs associated with this Sender when the Essence is based exclusively on the signal from these Inputs.

Debug information expressed through `debug` property SHOULD assist `no_essence` and `active_constraints_violation` states and MAY assist the other states.

## Status of Receiver

A Receiver managed with IS-11 has the following states:
- `unknown` when it is not possible to say whether the active stream is compliant with Receiver Capabilities of this Receiver (e.g. it is activated without a `transport_file`).
- `compliant_stream` when the active stream is compliant with Receiver Capabilities of this Receiver (e.g. there is an active `transport_file` and it does not violate the Receiver Capabilities).
- `non_compliant_stream` when the active stream is non compliant with Receiver Capabilities of this Receiver (e.g. there is an active `transport_file` which violates the Receiver Capabilities). If the Receiver becomes inactive this state is preserved until the next activation.

Debug information expressed through `debug` property SHOULD assist `unknown` and `non_compliant_stream` states and MAY assist the other states.

## Status of Output

An Output has the following states that describe the signal at this Output:
- `no_signal` when this Output is not producing any signal to a downstream counterpart (e.g. if the Output has associated Receivers, it may mean that one or more of these Receivers do not have a compliant incoming stream).
- `default_signal` when this Output is producing a default signal (i.e. not acquired from associated Receivers).
- `signal_present` when the signal is present and stable.

Debug information expressed through `debug` property SHOULD assist `no_signal` state and MAY assist the other states.

## Preventing restrictions violation

At any time if State of an active Sender becomes `active_constraints_violation`, the Sender MUST become inactive. An inactive Sender in this state MUST NOT allow activations.

At any time if State of an active Receiver becomes `non_compliant_stream`, the Receiver SHOULD become inactive. An inactive Receiver in this state SHOULD NOT allow activations.

## Signal altering indication

A Device SHOULD provide parent Flows and parent Sources to a Sender's Flow and associated Source if the signal being transmitted from the Sender is not identical to the signal being captured from the Input(s).

## Inputs

### Properties

The `/properties` endpoint shows properties of the Input.

### Base EDID

An Input MAY support Base EDID and MUST indicate it via `base_edid_support` property. The API documentation reflects how responses from `/edid/base` depend on this property.

Inputs that support Base EDID MAY additionally support adjusting Base EDID to internal capabilities (e.g. capabilities of an Input itself and Senders associated with this Input) via `adjust_to_caps` query parameter of a `PUT /edid/base` request. An Input MUST indicate support of `adjust_to_caps` query parameter via `adjust_to_caps` property presence in `GET /properties` responses.

There is no Base EDID at the initial state. If the Base EDID for an Input changes, then all Senders associated with this Input MUST update their versions (in registered mode this MUST update the registered resources).

### Effective EDID

If Active Constraints of all Senders associated with this Input are empty and `adjust_to_caps` is equal to `false` or not supported, the Effective EDID is equal to the Base EDID.

If Active Constraints of all Senders associated with this Input are empty and `adjust_to_caps` is supported and equal to `true`, the Effective EDID is a combination of the Base EDID and the internal capabilities.

If Active Constraints of at least one Sender associated with this Input are not empty, the Effective EDID is a combination of the Base EDID, internal capabilities and Active Constraints of all Senders associated with this Input such that the baseband signal from the Input can be transmitted by the Senders without breaking Active Constraints.

If the Base EDID is not set, the Effective EDID is built on the basis of a default EDID defined for the Input by the manufacturer.

The `/edid/effective` endpoint allows a client to download the Effective EDID if it exists. If the Effective EDID for the Input changes and it is associated with any Senders, then all of the Senders in question MUST update their versions (in registered mode this MUST update the registered resources).

## Outputs

### Properties

The `/properties` endpoint shows properties of the Output.

### EDID

The `/edid` endpoint allows a client to download the binary EDID if it exists. If the EDID information for the Output changes and it is associated with any Receivers, then all of the Receivers in question MUST update their versions (in registered mode this MUST update the registered resources).

[BCP-004-01]: https://specs.amwa.tv/bcp-004-01/
