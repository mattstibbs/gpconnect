---
title: HTML Implementation Guide
keywords: getcarerecord, development, html, rendering
tags: [development,getcarerecord]
sidebar: accessrecord_sidebar
permalink: accessrecord_development_html_implementation_guide.html
summary: "Overview of the common HTML view rendering guidance in relation to the Access Record capability."
---

## HTML Implementation Guide ##

### Purpose ###

This document is intended for use by software developers looking to build a conformant GP Connect HTML care record viewer application.

### Notational Conventions ###

The keywords "**MUST**", "**MUST NOT**", "**REQUIRED**", "**SHALL**", "**SHALL NOT**", "**SHOULD**", "**SHOULD NOT**", "**RECOMMENDED**", "**MAY**", and "**OPTIONAL**" in this document are to be interpreted as described in [RFC 2119](https://www.ietf.org/rfc/rfc2119.txt).

## Near Real Time View ##

{% include important.html content="The record returned from the Provider system is near real time." %}

- Local pending changes (i.e. within a consultation that is actively ongoing) may not be available.
- The record is machine generated and therefore is not owned or attested by any single clinician.

## Record Locking ##

GP Connect queries/requests may be received while the patient's record is being updated.

Record locking inside a provider system SHALL NOT impact the ability of the system to fulfil read/query requests of the patient's record.

However, it is understood that there are differing approaches to record locking within the GP principal systems which affect when any local/pending changes are actually committed back to the patient's record.

When a consumer system accesses a patient's record, the provider systems SHALL only return data that has been successfully committed back to the patient's record and thus has become available to all users (including users of the provider APIs).

## Common User Interface Guidance ##

Where appropriate the following [Common User Interface (CUI)](https://digital.nhs.uk/common-user-interface) guidance documents should be followed when generating the GP Connect HTML Views.

### NHS Number Format ###

[NHS Number input and display](http://webarchive.nationalarchives.gov.uk/20160921140920/http://systems.digital.nhs.uk/data/cui/uig/inputdisplay.pdf)<br/>
[NHS Number input and display - Quick Implementation Guide](http://webarchive.nationalarchives.gov.uk/20160921140920/http://systems.digital.nhs.uk/data/cui/uig/inputdisplayqig.pdf)

### Patient Details ###

[Patient Name input and display](http://webarchive.nationalarchives.gov.uk/20160921140920/http://systems.digital.nhs.uk/data/cui/uig/patnamedisp.pdf)<br/>
[Patient Name input and display - Quick Implementation Guide](http://webarchive.nationalarchives.gov.uk/20160921140920/http://systems.digital.nhs.uk/data/cui/uig/patnamedispqig.pdf)

[Sex and current Gender input and display](http://webarchive.nationalarchives.gov.uk/20160921140920/http://systems.digital.nhs.uk/data/cui/uig/sex.pdf)<br/>
[Sex and current Gender input and display - Quick Implementation Guide](http://webarchive.nationalarchives.gov.uk/20160921140920/http://systems.digital.nhs.uk/data/cui/uig/sexqig.pdf)

### Date/Time Format ###

[Date display](http://webarchive.nationalarchives.gov.uk/20160921140920/http://systems.digital.nhs.uk/data/cui/uig/datedisplay.pdf)<br/>
[Time display](http://webarchive.nationalarchives.gov.uk/20160921140920/http://systems.digital.nhs.uk/data/cui/uig/timedisplay.pdf)<br/>
[Date Time display - Quick Implementation Guide](http://webarchive.nationalarchives.gov.uk/20160921140920/http://systems.digital.nhs.uk/data/cui/uig/datetimedispqig.pdf)

### GP Details ###

[Address input and display](http://webarchive.nationalarchives.gov.uk/20160921140920/http://systems.digital.nhs.uk/data/cui/uig/address.pdf)<br/>
[Telephone Number input and display](http://webarchive.nationalarchives.gov.uk/20160921140920/http://systems.digital.nhs.uk/data/cui/uig/tele.pdf)

## Patient Banner ##

Consumer systems SHALL present a patient banner above the HTML content returned from the GP Connect APIs in-line with the CUI guidance.

[Patient Banner - Quick Implementation Guide](http://webarchive.nationalarchives.gov.uk/20160921140920/http://systems.digital.nhs.uk/data/cui/uig/patben.pdf)

## Minimum Display Resolution ##

This guidance is applicable to user interfaces displayed on desktop or laptop computers. It is assumed that, at a minimum, these computers are capable of operating at a minimum display resolution of **1024 x 768**, and have a keyboard and pointing device.

## Minimal Structured Data ##

### FHIR Resources ###

Provider systems SHALL return a minimal set of structured data along with the HTML content as follows:

| FHIR Resource(s) | Composition Section  |
|------------------|--------|-------------|
| `Patient`          | Subject              |
| `Practitioner`     |                      |
| `Organization`     | Custodian            |
| `Device`           | Author<sup>1</sup>   |

<sup>1</sup> As the composition is machine generated the concept of a single Author does not make logical sense. It is expected that the Author field will be populated with the details of the software system which generated the composition.

### Demographic Cross Checking ###

Consumer systems SHALL compare the returned structured patient demographic data (supplied by the provider system as structured data) against the demographic data held in the consumer system.

If differences exist then the consumer system SHALL show an alert/warning and provide details of which fields/values are different between the two systems.

The following data SHALL be cross checked between consumer and returned provider data.  Any differences between these fields SHALL be brought to the attention of the user.   

| Item | Resource Field |
| ---- | -------------- | 
| Family Name | patient.name.family |
| Given Name | patient.name.given |
| Gender | patient.gender |
| Birth Date | patient.birthDate |
| GP Practice Code | patient.managingOrganization | 

<sup>1</sup>May be redacted if patient is flagged on Spine as Sensitive demographics.

Additionally the following data MAY be displayed if returned from the provider to assist a visual cross check and for safe identification but should not be part of the automatic comparison.

* Address and Postcode
* Contact (telephone, mobile, email)
* Responsible GP Code and Name
* GP Practice Name and Address

All above may be redacted if patient is flagged on Spine as Sensitive demographics.

## Section Retrieval ##

### Error Handling ###

If a GP principal system can't meaningfully supply content for a requested HTML section (or subset of the Summary View) then the system SHALL return the following HTML fragment in place of the HTML table.

#### Supported But Hasn't Been Recorded ####

- System can store the data.
- BUT no data has been recorded for the patient.

```html
<div>
	<p>No '[section]' data is recorded for this patient.</p>
</div>
```

#### Supported But Can't Be Technically Provided ####

- System can store the data.
- BUT no data is available for the patient via the GP Connect APIs due to a technical limitation.

```html
<div>
	<p>This system does not support retrieval of '[section]' data.</p>
	<p>This data may still exist in the source system.</p>
</div>
```

#### Supported But Is Masked / Access Denied ####

- System can store the data.
- BUT no data is available for the patient via the GP Connect APIs due to a IG/DS rule.

```html
<div>
	<p>Access is denied to '[section]' data for this patient.</p>
	<p>This data may still exist in the source system.</p>
</div>
```

#### Not Supported ####

- System doesn't store the data.

```html
<div>
	<p>'[section]' data is not supported by this system.</p>
</div>
```

### Consumer-Supplied Date Ranges ###

For these, the Provider SHALL supply all matching dates/times, eg the period 2011-05-23 to 2011-05-27 includes all items with times from the start of the 23rd May through to the end of the 27th of May.


### Record In Transit ###

In the scenario where the patient's GP record is not 'fully integrated' into the 'new' GP, following a GP transfer, then only data entered to the new GP's record SHALL be provided. A warning message stating that the record is either not available (no data entered to the new GP record), or incomplete due to the transfer, SHALL be provided and displayed.



### Section Banner ###

#### Applied Date Ranges ####

Consumer Systems SHALL display the date range applied to a section's data, as supplied by the Provider where applicable, beneath the Section Header

```html
<div>
	<p>For the period 'dd-mmm-yyyy' to 'dd-mmm-yyyy'</p>  

</div>
```

#### Default Date Ranges ####

Where the Consumer System has not supplied a date-range, then where applicable and while the default is for ALL items to be provided, the following message SHALL be supplied by the Provider and displayed by the Consumer System beneath the Section Header

```html
<div>
	<p>All relevant items subject to patient preferences and/or RCGP exclusions</p>
</div>
``` 

#### Section Content Message ####

Following the Section Header & Date Range Applied, Consumer Systems SHALL display the Provider-returned message describing the contents of the section and indicating where contents may vary - eg where Historical Allergies are included in the Current Allergies sub-section, or where a particular column is not provided


### Per Section Default Time Frames ###

Section Default Time Frames SHALL be configurable, to be easily amendable if required in response to FoT feedback 

{% include todo.html content="Section default time frames to be reviewed following FoT feedback." %}

| Section Code   | Time Frame | FHIR Resource(s) |
|----------------|------------|------------------|
| ADM  | All |- |                
| ALL  | All Relevant | AllergyIntolerance<sup>1</sup> |
| CLI  | All | Condition, Procedure |
| ENC  | All | Encounter |
| IMM  | All Relevant | Immunization<sup>1</sup> |
| INV  | All | DiagnosticOrder<sup>2</sup> |
| MED  | All Relevant | Medication, MedicationOrder, MedicationDispense, MedicationAdministration |
| OBS  | All Relevant | Observation |
| PAT  | - | Patient<sup>3</sup> |
| PRB  | All Relevant | Problem|
| REF  | All Relevant | Referral |
| SUM  | - | Summary<sup>3</sup> |

<sup>1</sup> An explicit time frame is not allowed to be specified as the system SHALL return 'All Relevant' resources.
<sup>2</sup> Section to be considered as part of future release.
<sup>3</sup> An explicit time frame is not allowed as these are set piece views, the system SHALL return 'All Relevant' resources.

Provider systems SHALL return a HTTP *Bad Request* `400` error response if a date range is specified for a section that does not support filtering by a consumer supplied date range.

# HTML Section Views #

A total of twelve HTML section views are to be provided.

Provider systems SHALL use XHTML constructs as defined in the [FHIR Narrative](https://www.hl7.org/fhir/DSTU2/narrative.html) guidance contained within the FHIR&reg; standard.

### [XHTML Narrative](https://www.hl7.org/fhir/DSTU2/narrative.html) ###

As outlined in the Narrative section of the FHIR&reg; standard.

> The XHTML content SHALL NOT contain a head, a body element, external stylesheet references, deprecated elements, scripts, forms, base/link/xlink, frames, iframes, objects or event related attributes (e.g. onClick). This is to ensure that the content of the narrative is contained within the resource and that there is no active content. Such content would introduce security issues and potentially safety issues with regard to extracting text from the XHTML.
> 
<p />
> Note that the XHTML is contained in general XML so there is no support for HTML entities like ```&nbsp;``` or ```&copy;``` etc. Unicode characters SHALL be used instead. Unicode ```&#160;``` substitutes for ```&nbsp;```.

{%include important.html content="The content SHALL NOT contain any platform-specific escape or formatting characters such as ```\r\n```, as these may cause inconsistent rendering within consumer applications, with potential impacts on clinical safety." %}

### [Styling the XHTML](https://www.hl7.org/fhir/DSTU2/narrative.html#css) ###

As outlined in the 'Styling the XHTML' section of the FHIR&reg; standard.

> In order to minimise manageability and security issues, authoring systems cannot specify the CSS stylesheet to use directly. Instead, the application that displays the resource provides the stylesheets. This means that the rendering system chooses what styles can be used, but the authoring system must use them in advance. Authoring systems can use these classes, which SHALL be supported by all rendering systems.
> 
> Please see the [FHIR Runtime CSS](https://www.hl7.org/fhir/DSTU2/fhir-runtime.css) or [Styling the XHTML](https://www.hl7.org/fhir/DSTU2/narrative.html#css) on the FHIR website for full details.


