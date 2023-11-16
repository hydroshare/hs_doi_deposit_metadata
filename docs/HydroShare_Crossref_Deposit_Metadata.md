# HydroShare Crossref Deposit Metadata Mapping

## Introduction

HydroShare is a repository within which researchers share and publish data and other research products. These products are referred to in HydroShare as "resources". When a resource has reached maturity and a HydroShare user chooses to permanently publish that resource, HydroShare registers a digital object identifier (DOI) for that resource. HydroShare currently uses [Crossref](https://www.crossref.org/) to register DOIs. When registering a DOI, Crossref users have the option to deposit metadata into Crossref to accompany the DOI registraion. This metadata is then made available by Crossref to other systems.

This document describes how the resource-level metadata from HydroShare's resource data model has been mapped to the Crossref metadata schema for the purpose of depositing metadata for a resource with Crossref when a DOI is registered.

Metadata for the following published HydroShare resource was used in the examples in this document: 
* [https://www.hydroshare.org/resource/6625bdbde41c45c2b906f32be7ea70f0/](https://www.hydroshare.org/resource/6625bdbde41c45c2b906f32be7ea70f0/)
* [https://doi.org/10.4211/hs.6625bdbde41c45c2b906f32be7ea70f0](https://doi.org/10.4211/hs.6625bdbde41c45c2b906f32be7ea70f0)

## HydroShare Resources

HydroShare has two fundamental resource types that are relevant in the context of this document - resource and collection. Although Crossref provides some functionality for a heirarchy of `Database` as a grouping of `Dataset` objects, which might be useful for HydroShare collection resources, for the sake of simplicity, consistency, and ease of implementation, HydroShare will encode deposit metadata for collection and regular resources in the same way as described below. The only real difference is in the setting of the `dataset_type` attribute of the `dataset` element as specified below.

## Crossref Deposit Metadata

There are multiple methods for depositing metadata with Crossref when registering a DOI. HydroShare currently uses the HTTP POST method via Crossref's API to post an XML file with Crossref that contains metadata for the resource for which a DOI is being registered. This deposit XML file must conform to the Crossref metadata schema. The following links are useful:

* Crossref URL for all submissions: [https://doi.crossref.org/servlet/deposit](https://doi.crossref.org/servlet/deposit)
* Crossref URL for testing submissions: [https://doi.crossref.org/servlet/deposit](https://doi.crossref.org/servlet/deposit)
* Crossref XML parser for single file validation: [https://data.crossref.org/reports/parser.html](https://data.crossref.org/reports/parser.html)
* Crossref required, recommended, and optional metadata: [https://www.crossref.org/documentation/schema-library/required-recommended-elements/](https://www.crossref.org/documentation/schema-library/required-recommended-elements/)
* Crossref deposit metadata schema (Version 5.3.1): [https://www.crossref.org/documentation/schema-library/metadata-deposit-schema-5-3-1/](https://www.crossref.org/documentation/schema-library/metadata-deposit-schema-5-3-1/)
* Crossref datasets markup guide: [https://www.crossref.org/documentation/schema-library/markup-guide-record-types/datasets/](https://www.crossref.org/documentation/schema-library/markup-guide-record-types/datasets/)

## Required, Recommended, and Optional Metadata

Crossref supports registration of DOIs for several different types of resources. The most appropriate type from Crossref's list for HydroShare resources is "Database".  A "Database" can contain multiple "Datasets". According to Crossref, The following are the [required, recommended, and optional metadata for "Dataset"](https://www.crossref.org/documentation/schema-library/required-recommended-elements/#005):

**Required**
* Database level: 
  * titles
* Dataset level: 
  * doi_data

**Recommended**
* Database level:
  * contributors
  * description
  * database_date
  * publisher
  * institution
  * doi_data
* Dataset level:
  * contributors
  * titles
  * database_date
  * description
  * format
  * citation_list
  * component_list
  * funding
  * license
  * Crossmark metadata

**Optional**
* publisher_item
* component_list

Based on the above requirements, recommendations from Crossref, availability of HydroShare resource-level metadata elements, and practicality with respect to how often metadata elements would need to be updated as resources are edited within HydroShare, the following Crossref metadata elements have been selected for inclusion in HydroShare's deposit metadata for Crossref:

**Database Level**
* titles - an arbitrary title, but included because it is required
* publisher - optional, but included here because it is only available at the Database level

**Dataset Level**
* titles - the title of the HydroShare resource
* contributors - The names of the people and/or organizations that created the resource
* database_date - Dates associated with creation, update, and publication of the resource
* description - Abstract description of the resource
* funding - Description of any funding agency information in the resource metadata
* license - A link to the license under which the resource is published.
* doi_data - The information to be used by Crossref to register the DOI.

In the sections that follow, each of the elements that will be included in HydroShare's deposit metadata for Crossref DOI registration are described, including additional required Crossref elements that give structure to the deposit metadata but are not specifically included in the list above (e.g., `doi_batch`). Each deposit XML document created by HydroShare for deposit into Crossref when registering a DOI should have the following elements.

### DOI Batch

The `doi_batch` element references the Crossref metadata schema version that is used in the markup. We should be using version 5.3.1 of the schema. Since we will include funding agency information, we also need to include the fundref.xsd schema that is used by Crossref in the schema documentation.

```xml
<doi_batch
	xmlns="http://www.crossref.org/schema/5.3.1"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" version="5.3.1" xsi:schemaLocation="http://www.crossref.org/schema/5.3.1 http://www.crossref.org/schemas/crossref5.3.1.xsd"
	xmlns:fr="http://www.crossref.org/fundref.xsd">
</doi_batch>
```

### Head

The `head` element includes the following sub-elements: 
* `doi_batch_id`: a unique identifier for the batch of DOIs to be registered. HydroShare currently uses the resource ID for this field.
* `timestamp`: The time at which the deposit metadata was created with second resolution. 
* `depositor`: The `depositor_name` and `email_address` of the depositor. I suggest we use "CUAHSI" for `depositor_name` and `help@cuahsi.org` as the `email_address`. This is consistent with what HydroShare is doing now.
*  `registrant`: The name of the registrant - "Consortium of Universities for the Advancement of Hydrologic Science, Inc. (CUAHSI)" consistent with what HydroShare is doing now.

```xml
<head>
  <doi_batch_id>6625bdbde41c45c2b906f32be7ea70f0</doi_batch_id>
  <timestamp>20231114135731</timestamp>
  <depositor>
    <depositor_name>CUAHSI</depositor_name>
    <email_address>help@cuahsi.org</email_address>
  </depositor>
  <registrant>Consortium of Universities for the Advancement of Hydrologic Science, Inc. (CUAHSI)</registrant>
</head>
```

### Body

The `body` element contains the main content of the deposit metadata. It contains several sub-elements, including `database`, which contains database-level metadata and then `dataset`, which contains dataset-level metadata. `database` is a container for all information about a set of datasets. For an individual HydroShare resource, we should nest a single `dataset` element within a `database` element. 

Crossref allows the `dataset_type` attribute of the `dataset` to be set as `record` or `collection` to indicate the type of deposit. The default attribute is `record` and should be used for registering DOIs for all HydroShare resources besides collection resources. For HydroShare collection resources, the value of this attribute should be set to "collection".

For a regular HydroShare resource:
```xml
<body>
  <database>
    <dataset dataset_type="record">...</dataset>
  </database>
</body>
```

For a HydroShare collection resource:

```xml
<body>
  <database>
    <dataset dataset_type="collection">...</dataset>
  </database>
</body>
```

### Database

The `database` element is a container for information about a set of datasets. It can also include some database level metadata elements. Here we will include only those database level elements that are required by Crossref because the rest of the metadata will be specified at the `dataset` level. Required elements include:
* `titles`: Use the string "HydroShare Resource" 
* `publisher`: Use the string "HydroShare"

```xml
<body>
    <database>
      <database_metadata language="en">
        <titles>
          <title>HydroShare Resource</title>
        </titles>
        <publisher>HydroShare</publisher>
      </database_metadata>
    <dataset dataset_type="record">...</dataset>
  </database>
</body>
```

### Dataset

The `dataset` element contains dataset-level metadata about the HydroShare resource. This will include:
* `titles`: The title of the HydroShare resource.
* `contributors`: Information about the authors/creators of the HydroShare resource.
* `database_date`: The dates on which the resource was created, modified, and published.
* `description`: The Abstract of the HydroShare resource.
* `funding`: Information about the funding agency that funded the work that resulted in creation of the resource.
* `license`: Information about the license under which the resource is released.
* * `doi_data`: This is the data that will be used to register the DOI for the resource, including the DOI and the URL to which it should point.

```xml
<dataset dataset_type="record">
  <titles>...</titles>
  <contributors>...</contributors>
  <description>...</description>
  <database_date>...</database_date>
  <funding>...</funding>
  <license>...</license>
  <doi_data>...</doi_data>
</dataset>
```
In the sections below we describe each of the sub-elements of the `Dataset` element.

#### titles

The HydroShare resource title will be contained within the `titles` element. It is encoded as follows:

```xml
<titles>
  <title>Supporting data and tools for "Impact of data temporal resolution on quantifying residential end uses of water"</title>
</titles>
```

#### contributors
The `contributors` element is a container for all of the people who contributed to authoring or editing a resource. For HydroShare resources, we will limit this to the Authors/Creators of the resource. Contributors can be specified using a `person_name` element for each author/creator of the resource. The name of the resource author/creator can also have multiple attributes. We will include `contributor_role`, which is the role that the author/creator played. This comes from an enumeration and should be set to "author". We will also use `sequence`, which is the sequence order of the author/creator. This also comes from an enumeration and should be set to "first" for the first author and "additional" for the other authors. Those are the only two options. Each `person_name` element can include a `given_name` and `surname`. They can also include `affiliations`, `suffix`, `alt-name`, and `ORCID`. Best practice is to provide ORCID if it exists in the author/creator information for the resource. 

I suggest that we do not try to manage `affiliations` for people in the deposit metadata. It adds additional complexity that we probably don't need, and also I don't think there is a place for this in NSF-PAR, so it wouldn't be imported anyway.

```xml
<contributors>
  <person_name contributor_role="author" seqence="first">
    <given_name>Camilo</given_name>
    <surname>Bastidas Pacheco</surname>
  </person_name>
  <person_name contributor_role="author" seqence="additional">
    <given_name>Jeffery</given_name>
    <surname>Horsburgh</surname>
    <ORCID>https://orcid.org/0000-0002-0768-3196</ORCID>
  </person_name>
  <person_name contributor_role="author" seqence="additional">
    <given_name>Arle</given_name>
    <surname>Beckwith</surname>
  </person_name>
</contributors>
```

**NOTE**: If there is an author that is an `organization`, which can be the case for a HydroShare resource, this can be accommodated by Crossref. In this case, an `organization` element is included within `contributors` that encodes the organization name. The `organization` element should have the same `contributor_role` and `sequence` attributes used with `person_name` above:

```xml
<contributors>
  <organization contributor_role="author" seqence="first">Utah Water Research Laboratory</organization>
</contributors>
```

#### description
The `description` element contains a narrative description of the resource. We will use the HydroShare abstract description for this element. The description element can support markup if done correctly. Need to make sure that any special characters are encoded correctly in the abstract.

```xml
<description>The files provided here are the supporting data and code files for the analyses presented in "Impact of data temporal resolution on quantifying residential end uses of water", an article submitted to the Water journal https://www.mdpi.com/journal/water. The journal paper assessed how the temporal resolution at which water use data are collected impacts our ability to identify water end use events, calculate features of individual events, and classify events by end use. Additionally, we also explored implications for data management associated with collecting this type of data as well as methods and tools for analyzing and extracting information from it. The data were collected in the cities of Logan and Providence, Utah, USA in 2022 and are included in this resource. The code and data included in this resource allow replication of the analyses presented in the journal paper, and the raw data included allow for extension of the analyses conducted.</description>
```

#### database_date
The `database_date` element can contain multiple dates. I suggest we use `creation_date` to represent the date on which the resource was originally created, `update_date` to indicate the last time a resource was updated, and `publication_date` to represent the date on which it was published. When first deposited, `update_date` and `publication_date` will be the same. Inclusion of the `update_date` may make it easier in the future for HydroShare to determine when deposit metadata need to be updated because changes to the resource have been made. It looks like NSF-PAR would only import the `publication_date`.

Crossref's schema wants dates separated out into month, day, and year elements, with leading zeros included. I think we should set these dates when creating the DOI and then update them as needed any time the DOI metadata is updated.

```xml
<database_date>
  <creation_date>
    <month>04</month>
    <day>19</day>
    <year>2022</year>
  </creation_date>
  <publication_date>
    <month>07</month>
    <day>08</day>
    <year>2022</year>
  </publication_date>
  <update_date>
    <month>07</month>
    <day>08</day>
    <year>2022</year>
  </update_date>
</database_date>
```

#### funding
An overview of information relevant to encoding funding information is available [here](https://www.crossref.org/documentation/funder-registry/funding-data-overview/). According to Crossref's documentation, funding metadata for Crossref **must** include the name of the funding organization **and** the funder identifier (where the funding organization is listed in the Open Funder Registry), and should include an award/grant number or grant identifier. Funder names should **only** be deposited without the accompanying ID if the funder is not found in the Open Funder Registry. While Crossref members can deposit the funder name without the identifier, those records will not be considered valid until such a time as the funder is added to the databae and they are redeposited (updated) with an ID. What the means is that they will not be found using the filters on funding information that Crossref supports via their REST API, or show up in Crossref's Open Funder Registry search. 

So, HydroShare needs to do the following:
1. Get the name of the funding organization from the resource's metadata if one exists.
2. Make an API call to Crossref's API to see if the funder name exists in their Open Funder Registry. See the Funders endpoint for the Crossref API [here](https://api.crossref.org/swagger-ui/index.html). Alternatively, there are JSON and .CSV formats of the Open Funder Registry available [here](https://www.crossref.org/documentation/funder-registry/accessing-the-funder-registry/) but these are only updated periodically.
3. If the funder exists in the Open Funder Registry, get the Open Funder Registry ID for the funder.
4. Encode the name of the funding agency and the Open Funder Registry ID in the deposit metadata. If the funding agency did not exist in the Open Funder Registry, encode only the name of the funding organization and the award number (if it exists in the HydroShare metadata).

Correct nesting of funder names and Open Funder Registry IDs is essential. Example 1 shows how to specify a single funder like the National Science Foundation. The funder identifier should be nested within the funder name.

**Example 1**. Single funding agency.
```xml
<fr:program name="fundref">
  <fr:assertion name="funder_name">National Science Foundation
    <fr:assertion name="funder_identifier">https://doi.org/10.13039/100000001</fr:assertion>
  </fr:assertion>
  </fr:assertion name="award_number">1552444</fr:assertion>
</fr:program>
```
Where there is more than one funding agency, each funder should be included within a `fundgroup`:

**Example 2**. Multiple funding agencies
```xml
<fr:program name="fundref">
  <fr:assertion name="fundgroup">
    <fr:assertion name="funder_name">National Science Foundation
      <fr:assertion name="funder_identifier">https://doi.org/10.13039/100000001</fr:assertion>
    </fr:assertion>
    </fr:assertion name="award_number">1552444</fr:assertion>
  </fr:assertion>
  <fr:assertion name="fundgroup">
    <fr:assertion name="funder_name">Utah Water Research Laboratory</fr:assertion>
  </fr:assertion>
</fr:program>
```
For additional examples of how to encode funding information, see [this page](https://www.crossref.org/documentation/funder-registry/funding-data-overview/). Some additional notes from that page:
* Items with multiple funder names but no award numbers may be deposited without a fundgroup
* At a minimum, a funding data deposit must contain a `funder_name` and `funder_identifier` assertion. Deposites with just an `award_number` assertion are not allowed. A  `funder_name`,  `funder_identifier`, and  `award_number`  should be included in deposits whenever possible.
* If the funder name cannot be matched in the Open Funder Registry, you may submit  `funder_name`  only, and the funding body will be reviewed and considered for addition to the official Registry. Until it is added to the Registry, the deposit will not be considered a valid funding record and will not appear in funding search or the REST API.
* Items with several award numbers associated with a single funding organization should be grouped together by enclosing the `funder_name`, `funder_identifier`, and `award_number(s)` within a `fundgroup` assertion.

#### license
Including license information is important for prospective users to know how the published resource can be used. In Crossref, a license can be specified for an accepted manuscript (AAM), the version of record (VoR), or a version intended for text and data mining using the `applies_to` attribute - e.g., a resource can have multiple license elements. Given that published HydroShare resources are the version of record (VoR), we will use one license element in the deposit metadata and will specify that it is for the version of record. Each license element can contain a URL to a license description, the article version to which the license applies, and the license start date.

Given that Crossref only allows encoding of a URL for the license, the license element should only be included in the deposit metadata if the published HydroShare resource has a license URL. The URL for license is not required in HydroShare if a user chooses to designate a custom license, but if the user selects one of the standard HydroShare licenses, the URL will be populated. It does not appear that NSF-PAR would import license information.

The `start_date` attribute for the license should be specified as the date on which the resource was published from the resource's metadata.
 
```xml
<license_ref applies_to="vor" start_date="2022-07-08">
    http://creativecommons.org/licenses/by/4.0/
</license_ref>
```

#### doi_data
The `doi_data` element contains the information Crossref requires to register the DOI. The `doi` element should be created by HydroShare with the value of the DOI that will be registered. The `resource` element contains the URI to which the DOI should point. 

```xml
<doi_data>
   <doi>10.4211/hs.6625bdbde41c45c2b906f32be7ea70f0</doi>
   <resource>https://www.hydroshare.org/resource/6625bdbde41c45c2b906f32be7ea70f0/</resource>
</doi_data>
```