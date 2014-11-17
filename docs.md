---
title: Documentation
layout: default
---

This document describes the Open Addresses solution for the *Alpha* stage, completed on 21 November 2014. 

The description follows the flow of the data from its third party Open Data sources through the final Open Addresses product, that is also visualised in [this diagram](/assets/images/docs/data_workflow.png). 

- <a href='#commonetl'>The reference data sources and the Common ETL</a>
- <a href='#companieshouseetl'>The addresses sources and the Companies House ETL</a>
- <a href='#ingester'>The Ingester component</a>
- <a href='#distiller'>The Distiller component</a>
- <a href='#publishingapis'>The publishing APIs</a>
- <a href='#downloadable'>The downloadable Open Addresses database</a>

#<a name='commonetl'>The reference data sources and the Common ETL</a>
Open Addresses uses two types of Open Data sources:

- sources that are suitable to generate **reference data**, such as list of valid post towns or postcodes, and
- sources that are suitable to generate actual **addresses**. 

We use four different Open Data sources of reference data:

- [Ordnance Survey's "OS Locator"](http://www.ordnancesurvey.co.uk/business-and-government/products/os-locator.html), from which we source our reference table of **road names**.
- [Ordnance Survey's "Strategi"](http://www.ordnancesurvey.co.uk/business-and-government/products/strategi.html), from which we source our reference table of **settlements (localities)**.
- [Wikipedia's "List of post towns in the United Kingdom" article](http://en.wikipedia.org/wiki/List_of_post_towns_in_the_United_Kingdom), from which we source our reference table of **post towns**, and
- The [Office for National Statistics' "ONS Postcode Directory (ONSPD)"](http://www.ons.gov.uk/ons/guide-method/geography/products/postcode-directories/-nspp-/index.html), from which we source our reference table of **postcodes**.

Each of these sources are updated regularly in time by their publishers, but it can happen that they do not account for recent changes, such as new streets. For Alpha, we decided to ignore addresses that cannot be matched against the reference tables, in all or in part. This approach will change during the Beta stage, where we will estimate the addresses' formal statistical confidence and this will allow us to include addresses that cannot be matched against our reference tables.

The reference data is extracted by the software component we call *common ETL* (as in "Extract, Transform, Load"). The data goes through minimal transformation and processing before being stored for Open Addresses' use. You are very welcome to study the [source code](https://github.com/OpenAddressesUK/common-ETL) to see the detail. 

#<a name='companieshouseetl'>The addresses sources and the Companies House ETL</a>
The main source of Open Data we decided to use to harvest full addresses is [the UK Companies House's "Free Company Data Product"](http://download.companieshouse.gov.uk/en_output.html). 

Within the limited time available to the Alpha stage, our choice was driven by the need of obtaining the larger sets of addresses while at the same time being absolutely sure of being entitled to reuse them. 

Significant candidate sources of addresses could not be used as they either 

1. **include** third party material that is not openly licensed, or 
2. **were improved** by using third party material that is not openly licensed (e.g. the address validity could have been checked vs Royal Mail's PAF product). 

A great example of this is [Land Registry's "Price Paid" data](https://www.gov.uk/government/statistical-data-sets/price-paid-data-downloads). Price Paid is licensed under the Open Government Licence that does not cover "third party rights the Information Provider is not authorised to license", and we got [confirmation](https://www.whatdotheyknow.com/request/price_paid_dataset_followup/new) through FOI that "Land Registry has been using Royal Mail (PAF) data to validate addresses between 1995 and 2000 and Ordnance Survey data sources since the start of computerised data capture/mapping processes in 2000 to the current day".

Companies House's Free Company Data was not affected by licensing constraints that block us from re-using it and we got confirmation of ****SOME REFERENCE, HOW DO WE KNOW?**** but for a constraint on Northern Ireland addresses that ******** and have been hence removed.

The software component that is responsible of interpreting Companies House's data for use in Open Addresses is the *Companies House ETL*. The addresses are stored as "free text" in the source, so an important responsibility of the ETL is to normalise the addresses in their components and each of the components vs the reference tables, wherever suitable. As described above, addresses that cannot be matched vs the reference tables are discarded. You are very welcome to study the [source code](https://github.com/OpenAddressesUK/common-ETL) to see the detail. 

#<a name='ingester'>The Ingester component</a>
Blah blah

#<a name='distiller'>The Distiller component</a>
Blah blah

#<a name='publishingapis'>The publishing APIs</a>
Blah blah

#<a name='downloadable'>The downloadable Open Addresses database</a>
Blah blah
