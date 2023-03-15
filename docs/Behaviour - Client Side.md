# Behaviour: Client Side

## Active Constraints of Sender

An NMOS Controller manages stream compatibility using the Active Constraints of the Sender.
The Active Constraints of the Sender are expressed as [BCP-004-01][] Constraint Sets that restrict the stream the Sender can transmit.

### Building the Constraint Sets

The Controller MAY build these Constraint Sets by processing the Receiver Capabilities of one or more Receivers, or directly to represent User requirements.

The Controller MUST `GET /constraints/supported` from the Sender to determine the supported Parameter Constraints.
If the Sender does not support all the Parameter Constraints needed to express the User requirements or used in the Receiver Capabilities, the Controller SHOULD inform the User about it.

Constraint Sets built from Receiver Capabilities SHOULD represent a consensus among the Receivers, satisfying all (by default) or some (depending on User preferences) of the Receivers.
If the Controller cannot build any Constraint Sets that represent a consensus, the Controller SHOULD inform the User that not all of the chosen Receivers can be satisfied at the same time.

For example, there are Receiver A, Receiver B, Receiver C with equal Receiver Capabilities consisting of Constraint Sets 1, 2, 3, 4, 5 and Receiver D with Constraint Sets 2, 3, 4, 5, and 6 which is incompatible with the previous ones.
In this case the consensus is Constraint Sets 2, 3, 4, 5 which are common for all four Receivers.

The Controller MAY allow the User to control how the consensus is obtained, providing preferences to the Controller among the Receivers and the Constraint Sets of each Receiver and criteria about the size of the consensus.

The `urn:x-nmos:cap:meta:preference` property in Constraint Sets of Receiver Capabilities indicates relative preference between Constraint Sets of that Receiver.
The Controller MAY use these values to make a decision about how to process these Constraint Sets and determine preference of Constraint Sets of Active Constraints.
It SHOULD NOT propagate values of this property as is from Receiver Capabilities to Active Constraints.

The `urn:x-nmos:cap:meta:preference` property in Constraint Sets of Active Constraints indicates relative preference between Constraint Sets of the Active Constraints of the Sender.
The Controller MAY fill in this property in Constraint Sets based on any additional information about Receivers.

For example, if the User in any way informs the Controller that Receiver D has precedence over the consensus of Receivers A, B, C and `urn:x-nmos:cap:meta:preference` values of Receiver D state that Constraint Set 6 has higher preference than Constraints Sets 2, 3, 4, 5, then the Constraint Sets could be 2, 3, 4, 5, 6 where Constraint Set 6 has the highest preference.

Constraint Sets of Receiver Capabilities with `urn:x-nmos:cap:meta:enabled` set to false MUST be ignored completely during this process.

### Using the Constraint Sets

If changing the configuration of the Sender, the Controller MUST `PUT /constraints/active` to the Sender to replace the Active Constraints.
In case of changing the configuration of an active Sender, deactivating the Sender MAY be necessary.
The Sender indicates the Active Constraints cannot currently be updated with the `423` Locked response.

If keeping the configuration of the Sender, the Controller MUST instead `GET /constraints/active` from the Sender and evaluate these Active Constraints against the Constraint Sets it has built.
Sometimes there could be streams compliant with the current Active Constraints that would not be compliant with the Constraint Sets it has built.
In these scenarios, the Controller SHOULD inform the User that compatibility of the Sender's stream with the chosen Receivers cannot be assured without replacing the Active Constraints.

After this the Controller can make connections via [IS-05][].

Subsequently, after breaking these connections via IS-05, the Controller is RECOMMENDED to `DELETE /constraints/active` of the Sender after making it inactive.

The Controller MAY also change the configuration of the Sender without establishing an IS-05 connection.

## Dynamic format changes on Sender

### Changes which break Active Constraints

NMOS Controller MUST track Sender's `subscription`'s `active` property during and after the IS-05 connection. If it became `false` unexpectedly, NMOS Controller SHOULD `GET /status` to figure out the reason of breaking the connection. If the State of the Sender is `active_constraints_violation`, then NMOS Controller SHOULD either disconnect all the Receivers or `PUT /constraints/active` once again and re-enable Sender via IS-05.

### Changes which do not break Active Constraints

Sender's changes which meet the Active Constraints do not affect the `active` property so if the transport file of the Sender has changed, the NMOS Controller SHOULD `PATCH /staged` of all the connected Receivers with the new `transport_file` requesting immediate activation.

[BCP-004-01]: https://specs.amwa.tv/bcp-004-01/
[IS-05]: https://specs.amwa.tv/is-05/
