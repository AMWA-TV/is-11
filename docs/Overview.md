# AMWA IS-11 NMOS Flow Compatibility Management Specification: Overview \[Work In Progress\]
{:.no_toc}

* A markdown unordered list which will be replaced with the ToC, excluding the "Contents header" from above
{:toc}

<!-- _(c) AMWA 2017, CC Attribution-NoDerivatives 4.0 International (CC BY-ND 4.0)_  -->

## Introduction

The purpose of AMWA IS-11 is to provide a mechanism by which to configure Sources, Flows and Senders using information from Receivers. A Receiver provides Receiver Capabilities and exposes its Outputs, each of which may hold EDID from its downstream counterpart. A Sender has Active Constraints, which restrict media formats allowed for sending, and Inputs, each of which may have an Effective EDID to present to its upstream media producing unit.

The Specification includes:

- RAML and JSON Schema definitions, with supporting JSON examples
- This documentation set, which provides:
  - An overview of the API and how it is used.
  - Normative requirements in addition to those included in the RAML and JSON schemas specifying the API.
  - Additional details and recommendations for implementers of API providers and clients.
  - Information about compatibility between different API versions.

The terms 'Node', 'Device', 'Source', 'Flow', 'Sender' and 'Receiver' are used extensively in this documenation set. The [NMOS Technical Overview](https://specs.amwa.tv/nmos/main/docs/2.0._Technical_Overview.html) provides an outline of these terms, and IS-04 provides corresponding schema definitions.

The terms 'Parameter Constraint' and 'Constraint Set' used in this documentation set are defined in [BCP-004-01][BCP-004-01].

IS-11 is intended to be used in conjunction with an [IS-04][IS-04], [IS-05][IS-05], [BCP-004-01][BCP-004-01] and [BCP-005-01][BCP-005-01] deployment; however it has been written in such a way to provide useful functionality even in the absence of such a system.

## Use of Normative Language

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY",
and "OPTIONAL" in this documentation set are to be interpreted as described in [RFC 2119][RFC-2119].

## Terminology

### Input

An Input is a logical input connector which consumes video, audio and ancillary data for providing it to Sender(s) through a number of Sources and Flows. The precise relationship between the Inputs and such Sources and Flows is not specified and there may be an IS-08 mapping element in the system. The Input may present metadata about media consuming capabilities to its upstream counterpart. Each Input is a resource and based on [Resource Core JSON Schema][Resource-Core-Schema].

### Output

An Output is a logical output connector which produces video, audio and ancillary data provided by Receiver(s). Output may hold metadata about the media consuming capabilities from its downstream counterpart. Each Output is a resource and based on [Resource Core JSON Schema][Resource-Core-Schema].

## API Structure

The API provides a mechanism to get information about Inputs associated with Senders and Outputs associated with Receivers and to constrain a Sender from sending a Flow not supported by its subscribers.

The API is divided into the following parts:

### Senders

Each Sender has an `inputs` resource, which lists identifiers of all Inputs associated with this Sender, a `constraints` base resource which allows controlling the restrictions for the Flow associated with this Sender and a `status` resource showing the Sender's state in terms of IS-11.

#### Constraints

There is an `active` resource which holds Active Constraints and a `supported` resource which lists all the Parameter Constraints supported by this Sender.

### Receivers

Each Receiver has an `outputs` resource, which lists identifiers of all Outputs associated with this Receiver, and a `status` resource showing the Receiver's state in terms of IS-11.

### Inputs

Each Input has a `properties` resource, which has metadata about this Input, and an `edid` resource, which may hold EDIDs associated with this Input.

#### EDID

There is a `base` resource which holds the Base EDID used by the Input as the basis for creating the Effective EDID. The `effective` resource holds the Effective EDID presented to an upstream counterpart of this Input.

### Outputs

Each Output has a `properties` resource, which has metadata about this Output, and an `edid` resource, which may hold the EDID presented to this Output by its downstream counterpart.

[RFC-2119]: https://tools.ietf.org/html/rfc2119 "Key words for use in RFCs"
[IS-04]: https://specs.amwa.tv/is-04 "IS-04 NMOS Discovery & Registration"
[IS-05]: https://specs.amwa.tv/is-05 "IS-05 NMOS Device Connection Management"
[BCP-004-01]: https://specs.amwa.tv/bcp-004-01 "BCP-004-01 NMOS Receiver Capabilities"
[BCP-005-01]: https://specs.amwa.tv/bcp-005-01 "BCP-005-01 NMOS Receiver Capabilities"
[Resource-Core-Schema]: https://github.com/AMWA-TV/nmos-discovery-registration/blob/v1.3.1/APIs/schemas/resource_core.json "AMWA NMOS IS-04 v1.3.x Resource Core JSON schema"
