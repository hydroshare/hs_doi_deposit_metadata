# HydroShare DOI Deposit Metadata

This repository provides documentation for HydroShare's implementation of deposit metadata for registration of digital object identifiers (DOIs) for published resources. The HydroShare production system is at [https://www.hydroshare.org](https://www.hydroshare.org).

## Introduction

HydroShare currently uses [Crossref](https://www.crossref.org/) to register DOIs for published resources. When a DOI is registered, the DOI registration system allows metadata to be deposited which can then be queried by anyone who has the DOI. In this repository we describe how HydroShare's resource metadata is mapped to Crossref's deposit metadata schema. 

This repository was created to document HydroShare's deposit metadata implementation, and may be changed depending on HydroShare's use of DOI registration system.

## Driving Use Case

The use case that drives HydroShare's practice and efforts to accurately map HydroShare's metadata to the Crossref deposit metadata schema is related to other systems accessing the deposit metadata for a registered DOI. For example, many invetigators with grants from the National Science Foundation (NSF) wish to register published datasets with NSF's Public Access Repository (NSF-PAR) so that those resources can be included in the investigator's annual or final report to NSF. NSF-PAR has the capability to automatically retrieve metadata for a given a DOI, but only if deposit metadata for that DOI exists within the system in which the DOI is registered. 

## Repository Contents

This repository contains:

* [Docs](https://github.com/hydroshare/hs_doi_deposit_metadata/tree/main/docs): A folder containing documentation of HydroShare's deposit metadata implementation
* [Examples](https://github.com/hydroshare/hs_doi_deposit_metadata/tree/main/examples): A folder containing example deposit metadata encodings

## Sponsors and Credits


[![NSF-2012748](https://img.shields.io/badge/NSF-2012748-blue.svg)](https://nsf.gov/awardsearch/showAward?AWD_ID=2012748)

This material is based upon work supported by the National Science Foundation (NSF) under award 2012748. Any opinions, findings, conclusions, or recommendations expressed in this material are those of the authors and do not necessarily reflect the views of the NSF.
