---
title: PDS Change of Address 
keywords:  messaging, bundles
tags: [fhir,messaging]
sidebar: foundations_sidebar
permalink: explore_pds_change_of_address.html
summary: "The FHIR profiles used for the PDS Change of Address event message bundle"
---

## FHIR Profiles ##

The PDS Change of Address Bundle is expected to include a combination of the following resources to support the event header and data item requirements:

| PDS Change of Address Event Message Bundle |
|--------------------------------------------|
| [EMS-Bundle-1](https://fhir.nhs.uk/STU3/StructureDefinition/EMS-Bundle-1)                              |
| [EMS-MessageHeader-1](https://fhir.nhs.uk/STU3/StructureDefinition/EMS-MessageHeader-1) |
| [CareConnect-Organization-1](https://fhir.hl7.org.uk/STU3/StructureDefinition/CareConnect-Organization-1)                |
| [EMS-HealthcareService-1](https://fhir.nhs.uk/STU3/StructureDefinition/EMS-HealthcareService-1)                   |
| [CareConnect-EMS-Patient-1](https://fhir.nhs.uk/STU3/StructureDefinition/CareConnect-EMS-Patient-1)                     |
| [EMS-Communication-1](https://fhir.nhs.uk/STU3/StructureDefinition/EMS-Communication-1)                       |

<img src="images/explore/change_of_address_bundle.png" >

The `CareConnect-EMS-Patient-1` resource within the bundle will contain two addresses, one will represent the patients old address and one will represent the patients new address and will be identifiable by the `use` element within the addresses.


## PDS Change of Address Message Life Cycle ##

The `PDS Change of Address` event message is always a `new` event and there is no concept of `update` or `delete` for the event message.

If a subscriber receive multiple `PDS Change of Address` event messages for the same patient, the latest event message as indicated by the last updated meta data element within the patient resource should be considered the source of truth for the patients correct address.

{% include important.html content="`PDS Change of Address` event messages will only be triggered for updates to the patients home address on the Spine. Changes to a patient temporary address's on Spine will not trigger a `PDS Change of Address` event message to be sent through the EMS." %}



## Resource population requirements and guidance ##

The following requirements and resource population guidance should be followed in addition to the requirements and guidance outlined in the [Events Management Service](https://developer.nhs.uk/apis/ems-beta/explore_event_header_information.html) specification.


### EMS-MessageHeader-1

The messageHeader resource included as part of the event message SHALL conform to the [EMS-MessageHeader-1](https://fhir.nhs.uk/STU3/StructureDefinition/EMS-MessageHeader-1) constrained FHIR profile and the additional population guidance as per the table bellow:

| Element | Cardinality | Additional Guidance |
| --- | --- | --- |
| extension(eventMessageType) | 1..1 | Fixed value: `new` |
| event | 1..1 | Fixed Value: PDS002 (PDS Change of Address) |
| focus | 1..1 | This will reference the "EMS-Communication-1" resource which contains information relating to the event message. |


### EMS-Communication-1

The Communication resource included in the event message SHALL conform to the [EMS-Communication-1](https://fhir.nhs.uk/STU3/StructureDefinition/EMS-Communication-1) constrained FHIR profile and the additional population guidance as per the table below:

| Element | Cardinality | Additional Guidance |
| --- | --- | --- |
| status | 1..1 | Fixed value: `completed` |
| payload | 1..1 | This will reference the patient resource representing the patient who is the focus of this event. |


### CareConnect-EMS-Patient-1

The patient resource included in the event message SHALL conform to the [CareConnect-EMS-Patient-1](https://fhir.nhs.uk/STU3/StructureDefinition/CareConnect-EMS-Patient-1) constrained FHIR profile and the additional population guidance as per the table below:

| Element | Cardinality | Additional Guidance |
| --- | --- | --- |
| identifier | 1..1 | Patient NHS Number SHALL be included within the nhsNumber identifier slice |
| **Current Address** |
| address.use | 1..1 | Fixed value: **home** |
| address.line | 1..* | Current address lines. Note: the address lines SHALL appear in the the resource in order, i.e. Address line 1 first, line 2 second, etc. |
| address.city | 0..1 | If included the city part of the current address. |
| address.district | 0..1 | If included the county element of the current address. |
| address.postalcode | 1..1 | Current address post code |
| address.text | 1..1 | Text representation of the patients current address in full |
| address.period.start | 1..1 | The date from which the patients current address was valid |
| **Previous Address** |
| address.use | 1..1 | Fixed value: **old** |
| address.line | 1..* | Previous address lines. Note: the address lines SHALL appear in the the resource in order, i.e. Address line 1 first, line 2 second, etc. |
| address.city | 0..1 | If included the city part of the previous address. |
| address.district | 0..1 | If included the county element of the previous address. |
| address.postalcode | 1..1 | Previous address post code |
| address.text | 1..1 | Text representation of the patients previous address in full |
| address.period.start | 1..1 | The date from which the patients previous address was valid |
| address.period.end | 0..1 | The date from which the patients previous address was no longer their current address |

