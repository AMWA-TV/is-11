# Data Model: Identifier Mapping

All the language in IS-04 takes precedence.

In order to ensure consistent behaviour in a facility employing this identity, some rules apply when generating and handling identifiers.

## Identity Hierarchy

![Extended IS-04 Model Diagram](../docs/images/is-11-data-model.png)

The IS-04 hierarchy is preserved. Additionally, Senders managed with IS-11 SHOULD be associated with 1 or more Input resources and Receivers managed with IS-11 SHOULD be associated with 1 or more Output resources.

Inputs/Outputs carrying multiple media formats at a time (e.g. video and audio) MAY be associated with multiple Senders/Receivers of given formats.

## Input Representation

A new Input is created in the following circumstance:

- At the presence of a physical interface for connection to an upstream media producing unit

## Output Representation

A new Output is created in the following circumstance:

- At the presence of a physical interface for connection to a downstream media consuming unit

## Identifier Persistence

As per IS-04,

> Persistent identity helps to ensure that users’ expectations meet with reality. For example, if a piece of equipment is switched off and back on, the user would expect it to return with ... the identifiers used by the Node remain the same.

### Input ID

Owned by:

- Device ID

SHOULD change if:

- Sender ID changes
- A different physical interface (such as an HDMI output) is mapped to originate the essence

SHOULD NOT change if:

- A Sender is re-configured, adding or removing some Inputs, but keeping this one
- A different upstream counterpart is connected to the same interface

It is suggested that Input IDs be generated using the combination of:

- The parent Sender ID
- A physical interface identifier such as its name, index or similar

### Output ID

Owned by:

- Device ID

SHOULD change if:

- Receiver ID changes
- A different physical interface (such as an HDMI input) is mapped to consume the essence

SHOULD NOT change if:

- A Receiver is re-configured, adding or removing some Outputs, but keeping this one
- A different upstream counterpart is connected to the same interface

It is suggested that Output IDs be generated using the combination of:

- The parent Receiver ID
- A physical interface identifier such as its name, index or similar
