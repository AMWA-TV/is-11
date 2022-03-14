# Behaviour: Client Side

## Active Constraints of Sender

Before making an [IS-05][IS-05] connection, NMOS Controller chooses a Sender and Receivers. Then NMOS Controller MUST `GET /constraints/supported` from the Sender, MUST collect Receiver Capabilities from the Receivers and make such intersection of them that they can be utilized for building Active Constraints satisfying all the Receivers and using Parameter Constraint URNs supported by the Sender. If the Sender supports fewer Parameter Constraint URNs than used in the Receiver Capabilities, the NMOS Controller SHOULD inform the user about it. After that NMOS Controller MUST `PUT /constraints/active` and SHOULD make the IS-05 connection if the Constraints have been applied succesfully. After breaking this connection via IS-05, NMOS Controller is RECOMMENDED to `DELETE /constraints/active`.

Constraint Sets of Active Constraints SHOULD be the largest consensus among Receivers. For example, there are Receiver A, Receiver B, Receiver C with equal Receiver Capabilities consisting of Constraint Sets 1, 2, 3, 4, 5 and Receiver D with Constraint Sets 2, 3, 4, 5 and 6 which is incompatible with the previous ones. In this case the largest consensus is Constraint Sets 2, 3, 4, 5 which are common for all the four Receivers.

`urn:x-nmos:cap:meta:preference` property in Constraint Sets of Receiver Capabilities indicates relative preference between Constraint Sets of that Receiver. The NMOS Controller MAY use these values to make a decision about the intersection and determine preference of Constraint Sets of Active Constraints. It SHOULD NOT propagate values of this property as is from Receiver Capabilities to Active Constraints.

`urn:x-nmos:cap:meta:preference` property in Constraint Sets of Active Constraints indicates relative preference between Constraints Sets of the Active Constraints of the Sender. The NMOS Controller MAY fill in this property in Constraint Sets based on any additional information about Receivers. For example, if the user in any way informs the NMOS Controller that Receiver D is more important than Receivers A, B, C and `urn:x-nmos:cap:meta:preference` of Receiver D states that Constraint Set 6 has higher preference than Constraints Sets 2, 3, 4, 5, then the consensus would be Constraint Sets 2, 3, 4, 5, 6 where Constraint Set 6 has the highest preference.

Constraint Sets of Receiver Capabilities with `urn:x-nmos:cap:meta:enabled` set to false MUST be ignored completely while making the intersection.


## Dynamic format changes on Sender

### Changes which break Active Constraints

NMOS Controller MUST track Sender's `subscription`'s `active` property during the IS-05 connection. If it became `false` unexpectedly, NMOS Controller SHOULD `GET /status` to figure out the reason of breaking the connection. If the State of the Sender is `active_constraints_violation`, then NMOS Controller SHOULD either disconnect all the Receivers or `PUT /constraints/active` once again and re-enable Sender via IS-05.

### Changes which do not break Active Constraints

Sender's changes which follow the Active Constraints do not effect the `active` property so NMOS Controller MUST track version of Inputs, the Source, the Flow and the Sender. If any of them has changed, NMOS Controller SHOULD `PATCH /staged` of all the Receivers with new `transport_file` (if it's used for the IS-05 connection) and immediate activation.

[IS-05]: https://specs.amwa.tv/is-05/
