---
title: List
keywords: structured design
tags: [design,structured]
sidebar: accessrecord_structured_sidebar
permalink: accessrecord_structured_development_list.html
summary: "Guidance for populating and consuming the List resource"
div: resource-page
---

## Introduction ##

The headings below detail the elements of the `List` resource and describe how to populate and consume them.

{% include important.html content="Any element not specifically detailed below **MUST NOT** be populated or consumed." %}

{% include tip.html content="You'll find it helpful to read it in conjunction with the underlying [List profile definition](https://fhir.nhs.uk/STU3/StructureDefinition/CareConnect-GPC-List-1)." %} 

## Using the List resource ##

The `List` resource in FHIR is used to help manage a collection of resources. In GP Connect it is used to identify the data returns for each query. For each clinical area query, GP Connect will return one or more predefined list that identifies the data returned for that query.

- When multiple queries in the same API call return data in the same profile, the list will identify which data in the profile has been returned for which query.
- Where there are no items returned, the list will be empty.
- Where the return includes warning messages (for example, when clinical data is excluded), those messages will be in the list profile.manage negation where no resources are present in a system to be returned by a query. An attribution that is common to the resources it references will be returned, differentiating between items at different stages of a workflow, providing a mechanism to deal with warnings that can be applied to the group of resources.

{% include todo.html content="A code to populate the code field for Resolved Allergies is yet to be confirmed and will be added to the guidance shortly." %}

### Warning codes

The following table provides details of the warning codes that are to be used in the warningCode extension in GP Connect. More guidance for each code follows in the subsequent sections.

<table class='resource-attributes' border='1'>
  <tr>
    <td>Display</td>
    <td>Code</td>
    <td>Associated text</td>
  </tr>
  <tr>
    <td>Confidential items</td>
    <td>confidential-items</td>
    <td>Items excluded due to confidentiality and/or patient preferences.</td>
  </tr> 
  <tr>
    <td>Data in transit</td>
    <td>data-in-transit</td>
    <td>Patient record transfer from previous GP practice not yet complete; any information recorded before dd-mmm-yyyy has been excluded.</td>
  </tr> 
  <tr>
    <td>Data awaiting filing</td>
    <td>data-awaiting-filing</td>
    <td>Patient data may be incomplete as there is data supplied by a third party awaiting review before becoming available.</td>
  </tr> 
</table>

### Confidential items

Where items have been excluded from the returned resources due to patient consent preferences or as they are part of the exclusion dataset this **MUST** be indicated at the list level. If an item that would have been an entry in a list is excluded the warningCode field **MUST** be populated using the confidential items warning code from the above table. The associated text **MUST** also be added into the note field when the code is used.

### Data in transit

This only refers to data transmitted from GP to GP when a patient moves GP practice. This is where a patient is registered at their new GP practice but their medical records from their previous GP practice has not yet been received and/or incorporated into their new GP practice system. When this takes place all the lists returned **MUST** be populated using the data in transit warning code from the above table. The associated text **MUST** also be added into the note field when the code is used.

### Data awaiting filing

Where data exists in a provider system workflow that would have been included as part of the bundle returned had it been integrated in the system, then this **MUST** be sent in a separate list to the rest of the entries. The list **MUST** be sent using the appropriate list.code relevant to the type of resource that it contains as defined in the guidance for that resource group and **MUST** be populated using the data awaiting filing warning code from the above table. The associated text **MUST** also be added into the note field when the code is used. This will allow consuming systems to be able to display the data and indicate that it has not been reviewed by an appropriate person at the providing practice.

## List elements ##

### id ###

<table class='resource-attributes'>
  <tr>
    <td><b>Data type:</b> <code>Id</code></td>
    <td><b>Optionality:</b> Mandatory</td>
    <td><b>Cardinality:</b> 0..1</td>
  </tr>
</table>

The logical identifier of the List resource.

### meta.profile ###

<table class='resource-attributes'>
  <tr>
    <td><b>Data type:</b> <code>uri</code></td>
    <td><b>Optionality:</b> Mandatory</td>
    <td><b>Cardinality:</b> 0..1</td>
  </tr>
</table>

The List profile URL.

Fixed value [https://fhir.nhs.uk/STU3/StructureDefinition/CareConnect-GPC-List-1](https://fhir.nhs.uk/STU3/StructureDefinition/CareConnect-GPC-List-1)


### extension[warningCode] ###

<table class='resource-attributes'>
  <tr>
    <td><b>Data type:</b> <code>Extension</code></td>
    <td><b>Optionality:</b> Required</td>
    <td><b>Cardinality:</b> 0..1</td>
  </tr>
</table>

A code warning of an issue related to this list.

This extension is used to capture warnings that the list may be incomplete as data has been excluded due to confidentiality or may be missing due to data being in transit. It must be populated using the appropriate code from the table in the warning codes section above.


### extension[clinicalSetting] ###

<table class='resource-attributes'>
  <tr>
    <td><b>Data type:</b> <code>Extension</code></td>
    <td><b>Optionality:</b> Required</td>
    <td><b>Cardinality:</b> 0..1</td>
  </tr>
</table>

For GP Connect this should be set to '1060971000000108 General practice services'.

### status ###

<table class='resource-attributes'>
  <tr>
    <td><b>Data type:</b> <code>Code</code></td>
    <td><b>Optionality:</b> Mandatory</td>
    <td><b>Cardinality:</b> 1..1</td>
  </tr>
</table>

Whether the list is `current`, `retired` or `entered-in-error`.

`current` **MUST** be used for all lists in GP Connect.

### mode ###

<table class='resource-attributes'>
  <tr>
    <td><b>Data type:</b> <code>Code</code></td>
    <td><b>Optionality:</b> Mandatory</td>
    <td><b>Cardinality:</b> 1..1</td>
  </tr>
</table>

Whether the List has a `mode` of `working`, `snapshot`, or `changes`.

`snapshot` **MUST** be used for all lists in GP Connect.

### title ###

<table class='resource-attributes'>
  <tr>
    <td><b>Data type:</b> <code>String</code></td>
    <td><b>Optionality:</b> Mandatory</td>
    <td><b>Cardinality:</b> 1..1</td>
  </tr>
</table>

Descriptive name for the list. This will be taken from the 'display' element from the 'code' field.

### code ###

<table class='resource-attributes'>
  <tr>
    <td><b>Data type:</b> <code>CodeableConcept</code></td>
    <td><b>Optionality:</b> Mandatory</td>
    <td><b>Cardinality:</b> 1..1</td>
  </tr>
</table>

The purpose of the list.

There are currently 3 possible purposes of a list in GP Connect that will be represented by the following SNOMED codes.
1. Medications and medical devices - 933361000000108
2. Allergies and adverse reactions - 886921000000105
3. Resolved allergies - awaiting confirmation of this SNOMED code from the terminology team

### subject ###

<table class='resource-attributes'>
  <tr>
    <td><b>Data type:</b> <code>Reference(Patient)</code></td>
    <td><b>Optionality:</b> Mandatory</td>
    <td><b>Cardinality:</b> 1..1</td>
  </tr>
</table>

Reference to the patient.

### date ###

<table class='resource-attributes'>
  <tr>
    <td><b>Data type:</b> <code>dateTime</code></td>
    <td><b>Optionality:</b> Optional</td>
    <td><b>Cardinality:</b> 0..1</td>
  </tr>
</table>

When the list was created.

### source ###

<table class='resource-attributes'>
  <tr>
    <td><b>Data type:</b> <code>Reference(Practitioner, Patient, Device)</code></td>
    <td><b>Optionality:</b> Optional</td>
    <td><b>Cardinality:</b> 0..1</td>
  </tr>
</table>

Who and/or what defined the list contents (that is, the author).

### orderedBy  ###

<table class='resource-attributes'>
  <tr>
    <td><b>Data type:</b> <code>CodeableConcept</code></td>
    <td><b>Optionality:</b> Optional</td>
    <td><b>Cardinality:</b> 0..1</td>
  </tr>
</table>

What order the list has.

As the data where lists are being used in GP Connect is structured, it is simple for the consumer to put in order.

### note ###

<table class='resource-attributes'>
  <tr>
    <td><b>Data type:</b> <code>Annotation</code></td>
    <td><b>Optionality:</b> Required</td>
    <td><b>Cardinality:</b> 0..1</td>
  </tr>
</table>

Comments about this list.

### entry ###

<table class='resource-attributes'>
  <tr>
    <td><b>Data type:</b> <code>BackboneElement</code></td>
    <td><b>Optionality:</b> Required</td>
    <td><b>Cardinality:</b> 0..*</td>
  </tr>
</table>

Entries in the list.

See below for sub elements.

### entry.date ###

<table class='resource-attributes'>
  <tr>
    <td><b>Data type:</b> <code>Boolean</code></td>
    <td><b>Optionality:</b> Optional</td>
    <td><b>Cardinality:</b> 0..1</td>
  </tr>
</table>

When the item was added to the list.

As GP Connect represents a snapshot at the time the request was made by the consuming system this is not required to be populated.

### entry.item ###

<table class='resource-attributes'>
  <tr>
    <td><b>Data type:</b> <code>Reference(Any)</code></td>
    <td><b>Optionality:</b> Required</td>
    <td><b>Cardinality:</b> 1..1</td>
  </tr>
</table>

Actual entry.

Reference to the item that is part of the list.

### emptyReason ###

<table class='resource-attributes'>
  <tr>
    <td><b>Data type:</b> <code>CodeableConcept</code></td>
    <td><b>Optionality:</b> Required</td>
    <td><b>Cardinality:</b> 0..1</td>
  </tr>
</table>

Why the list is empty.

A FHIR code of `No Content recorded` **SHALL** be used if a query returns no results to enter into a list. In this case the 'note' field **SHALL** be populated with the text 'Information not available'.
