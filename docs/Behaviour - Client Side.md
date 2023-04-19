# Behaviour: Client Side

## Active Constraints of Sender

Before making an [IS-05][IS-05] connection, NMOS Controller chooses a Sender and a number of Receivers. Then NMOS Controller MUST `GET /constraints/supported` from the Sender, MUST collect Receiver Capabilities from the Receivers and make such processing of them that they can be utilized for building Active Constraints satisfying all (by default) or some (depending on user preferences) of the Receivers and using Parameter Constraint URNs supported by the Sender. If the Sender supports fewer Parameter Constraint URNs than used in the Receiver Capabilities, the NMOS Controller SHOULD inform the user about it. If the processing of the Receiver Capabilities results in an empty `constraint_sets` of Active Constraints, NMOS Controller SHOULD inform the user about it. After that NMOS Controller MUST `PUT /constraints/active` to the Sender and SHOULD make the IS-05 connections if the Constraints have been applied succesfully to the Sender. After breaking these connections via IS-05, NMOS Controller is RECOMMENDED to `DELETE /constraints/active` of the Sender.

Constraint Sets of Active Constraints SHOULD represent a consensus among Receivers. For example, there are Receiver A, Receiver B, Receiver C with equal Receiver Capabilities consisting of Constraint Sets 1, 2, 3, 4, 5 and Receiver D with Constraint Sets 2, 3, 4, 5 and 6 which is incompatible with the previous ones. In this case the consensus is Constraint Sets 2, 3, 4, 5 which are common for all the four Receivers. The user may control how the consensus is obtained, providing preferences to the NMOS Controller among the Receivers and the Constraint Sets of each Receiver and criteria about the size of the consensus.

`urn:x-nmos:cap:meta:preference` property in Constraint Sets of Receiver Capabilities indicates relative preference between Constraint Sets of that Receiver. The NMOS Controller MAY use these values to make a decision about how to process these Constraint Sets and determine preference of Constraint Sets of Active Constraints. It SHOULD NOT propagate values of this property as is from Receiver Capabilities to Active Constraints.

`urn:x-nmos:cap:meta:preference` property in Constraint Sets of Active Constraints indicates relative preference between Constraint Sets of the Active Constraints of the Sender. The NMOS Controller MAY fill in this property in Constraint Sets based on any additional information about Receivers. For example, if the user in any way informs the NMOS Controller that Receiver D has precedence over the consensus of Receivers A, B, C and `urn:x-nmos:cap:meta:preference` values of Receiver D state that Constraint Set 6 has higher preference than Constraints Sets 2, 3, 4, 5, then the Constraint Sets would be 2, 3, 4, 5, 6 where Constraint Set 6 has the highest preference.

Constraint Sets of Receiver Capabilities with `urn:x-nmos:cap:meta:enabled` set to false MUST be ignored completely while making the processing.


## Dynamic Update of Resources

The Controller uses Stream Compatibility API in conjunction with IS-04 APIs to discover and track the state of any relevant resources, including Inputs, Outputs, Senders and Receivers.

* The Controller MUST indicate whether each Sender/Receiver discovered via IS-04 APIs is present in Stream Compatibility API and indicate statuses of such Senders/Receivers
* The Controller MUST report to the User when `state` attribute of the status of a Sender remains `active_constraints_violation` or `no_essence` after a maximum of 30 seconds.
* The Controller SHOULD report to the User when `state` attribute of the status of an active Receiver becomes `non_compliant_stream`.
* The Controller SHOULD be capable of performing `PATCH /staged` of all the connected Receivers with the new `transport_file` requesting immediate activation when the related active Sender updates the transport file.
* The Controller SHOULD indicate statuses of Inputs and Outputs and indicate the Inputs associated to a given Sender and Outputs associated to a given Receiver for Senders/Receivers listed in Stream Compatibility APIs.
* The Controller SHOULD reflect changes in presence/absence of Inputs and Outputs to the User after a maximum of 30 seconds.

[IS-05]: https://specs.amwa.tv/is-05/
