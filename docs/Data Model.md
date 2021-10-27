# Data Model: Identifier Mapping

All the language in IS-04 takes precedence.

In order to ensure consistent behaviour in a facility employing this identity, some rules apply when generating and handling identifiers.

## Identity Hierarchy

![Extended IS-04 Model Diagram](../docs/images/is-11-data-model.png)

The IS-04 hierarchy is preserved. Additionally, Senders managed with IS-11 SHOULD be associated with 1 or more Input resources and Receivers managed with IS-11 SHOULD be associated with 1 or more Output resources. Senders and Receivers not managed with IS-11 MAY be associated with Inputs and Outputs respectively.

## Input Representation

A new Input is created in the following circumstance:

- At the presence of a physical source device which reads EDID

## Output Representation

A new Output is created in the following circumstance:

- At the presence of a physical sink device which provides EDID

## Identifier Persistence

As per IS-04,

> Persistent identity helps to ensure that users’ expectations meet with reality. For example, if a piece of equipment is switched off and back on, the user would expect it to return with ... the identifiers used by the Node remain the same.

### Input ID

Owned by:

- Device ID

SHOULD change if:

- Sender ID changes
- A different physical interface (such as an HDMI output) is mapped to originate the essence
- A different physical source is connect to the same interface (using the manufacturer information, serial number, or unique identifier)

SHOULD NOT change if:

- A Sender is re-configured, adding or removing some Inputs, but keeping this one

It is suggested that Input IDs be generated using the combination of:

- The parent Sender ID
- A connection identifier such as a channel name, index or similar

### Output ID

Owned by:

- Device ID

SHOULD change if:

- Receiver ID changes
- A different physical interface (such as an HDMI input) is mapped to consume the essence
- A different physical display is connect to the same interface (using the manufacturer information, serial number, or unique identifier)

SHOULD NOT change if:

- Configuration parameters associated with the Flow are changed, such as its operating resolution or bitrate
- A Receiver is re-configured, adding or removing some Outputs, but keeping this one

It is suggested that Output IDs be generated using the combination of:

- The parent Receiver ID
- A connection identifier such as a channel name, index or similar
