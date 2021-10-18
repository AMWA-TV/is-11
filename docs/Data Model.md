# Data Model: Identifier Mapping

[IS-04][IS-04] uses a logical data model which is extended in this specification with the addition of the Inputs and Outputs resources.
All the language in IS-04 takes precedence.

In order to ensure consistent behaviour in a facility employing this identity, some rules apply when generating and handling identifiers.

## Identity Hierarchy

![Extended IS-04 Model Diagram](../docs/images/is-11-data-model.png)

The IS-04 heirarchy is preserved with the exception of the addition of Sender can identify an association with 0 or more Input resources and Receiver can identify an association with 0 or more Output resources.

## Input Representation

A new Input is created in the following circumstance:

- At the presence of an IS-11 compatible Sender
- At the point of origin for a baseband connection

## Output Representation

A new Output is created in the following circumstance:

- At the presence of a physical display
- At the point of capture for a baseband connection

## Identifier Persistence

As per IS-04,

> Persistent identity helps to ensure that users’ expectations meet with reality. For example, if a piece of equipment is switched off and back on, the user would expect it to return with ... the identifiers used by the Node remain the same.

### Input ID

Owned by:

- Sender ID

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

- Receiver ID

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

[IS-04]: https://specs.amwa.tv/is-04/
