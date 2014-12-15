---
title: Technology
layout: default
---

This document describes the Open Addresses solution for the *Alpha* stage. It was last updated on 12 December 2014. 

The description follows the flow of data processing from its third party Open Data or public data sources down through the final Open Addresses database. 

<a href='/assets/images/docs/data_workflow.png'><img src="/assets/images/docs/data_workflow_preview.png"></a>

Click on the diagram to see the full version.

##Table of Contents

- <a href='#sources'>Our sources of data</a>
- <a href='#etls'>The ETL software modules ("Extract, Transform, Load")</a>
- <a href='#ingester'>The Ingester software module</a>
- <a href='#distiller'>The Distiller software module</a>
- <a href='#publishingapis'>The publishing APIs</a>
- <a href='#downloadable'>The downloadable Open Addresses database</a>
- <a href='#provenance'>How provenance is documented</a>

#<a name='sources'>Our sources of data</a>

Open Addresses uses two types of data:

- **reference data**, such as list of valid post towns or postcodes, and
- **addresses**. 

All of the data that we use is either free of third-party Intellectual Property (IP) or is fully openly licenced. All provenance is tracked.

##Reference data

For the Alpha phase We used four reference datasets. All are open data released by the public sector:

- [Ordnance Survey's "OS Locator"](http://www.ordnancesurvey.co.uk/business-and-government/products/os-locator.html), from which we source our reference table of **road names**.
- [Ordnance Survey's "Strategi"](http://www.ordnancesurvey.co.uk/business-and-government/products/strategi.html), from which we source our reference table of **settlements (localities)**.
- [Wikipedia's "List of post towns in the United Kingdom" article](http://en.wikipedia.org/wiki/List_of_post_towns_in_the_United_Kingdom), from which we source our reference table of **post towns**, and
- The [Office for National Statistics' "ONS Postcode Directory (ONSPD)"](http://www.ons.gov.uk/ons/guide-method/geography/products/postcode-directories/-nspp-/index.html), from which we source our reference table of **postcodes**. Note that Northern Ireland's postcodes are dropped from the reference data as ["a separate licence for commercial use is required, direct from the Irish Land and Property Services"](http://www.ons.gov.uk/ons/guide-method/geography/beginner-s-guide/licences/index.html). 

##Address data
The current platform uses a single address dataset [Companies House's "Free Company Data Product"](http://download.companieshouse.gov.uk/en_output.html). 

Companies House's Free Company Data Product [does not include third party IP](https://www.whatdotheyknow.com/request/free_company_data_third_party_in).

Some other candidate sources of addresses could not be used as they included third party IP that is not openly licensed.

Find out more about these challenges and how you can help add more data on our [Get Involved](/about/involved/) page.

##Updating the data
Each of the sources described above are currently maintained and updated regularly by their respective publishers. It can happen, though, that they do not account for recent changes, such as new streets. During Beta we will be updating the data on a daily basis.

It is important to note that, for Open Addresses' Alpha stage, we also decided to ignore addresses that cannot be matched against any of the reference tables. This approach will change during the Beta stage, when we will calculate mathematically the statistical confidence that can be placed in each  address and address component.

#<a name='etls'>The ETL software modules</a>

##The Common ETL
The reference data created from its sources described above by the software component we call *common ETL* (ETL as in "Extract, Transform, Load"). We consider the data highly reliable, hence it goes through minimal processing before being published by the platform.. 

You are welcome to review or contribute to the current version of the Distiller on GitHub [here](https://github.com/OpenAddressesUK/common-ETL).

##The Companies House ETL
The software component that is responsible for interpreting Companies House Free Company Data Product for use in Open Addresses is the *Companies House ETL*. The addresses are stored as "free text" in the source, so an important responsibility of the ETL is to decompose the addresses in their components and normalise each of the components vs the reference tables, wherever suitable. As described above, addresses that cannot be matched vs the reference tables are discarded. 

You are welcome to review or contribute to the current version of the Distiller on GitHub [here](https://github.com/OpenAddressesUK/common-ETL).

#<a name='ingester'>The Ingester software module</a>
The Open Addresses solution is designed to support a distributed set of ETL modules in the future, many of which may not necessarily be owned directly by Open Addresses but contributed and operated by other individuals or organisations.

The *Ingester* module - called internally "Ernest" in honour of Ernest Marples who was [postmaster general at the time of the introduction of postcodes in the UK](http://en.wikipedia.org/wiki/Ernest_Marples) - is the interface between Open Addresses and this *ecosystem* of contributing ETLs. It provides a (soon to be) public API through which registered ETL modules will be able to submit addresses programmatically to our database of address submissions. Our own Companies House ETL module is run as if it was one of these third parties ETLs. 

During Beta we will publish instructions on how to run your own ETL and interface the Ingester. During Alpha, the Companies House ETL and the Open Addresses website itself (through the form on the homepage) are the only ETLs enabled to submit addresses to the Ingester. 

Data submitted through the Ingester is stored in what we call the "raw database". It's *raw* as we cannot make (yet) any general assumption on the quality of the addresses that are submitted to us, but just store them for later processing. The Ingester's database need to be effective at dealing with large volumes of loosely structured, incomplete and duplicate addresses submissions. 

It is also important for the Ingester to manage different submissions as if they were 'transactions'. This is useful, for example, in case we had concerns around any of the submission - e.g. their intellectual property - so that we can 'quarantine' that data while any necessary due diligence is performed.

You are welcome to review or contribute to the current version of the Ingester on GitHub [here](https://github.com/OpenAddressesUK/ernest).

#<a name='distiller'>The Distiller software module</a>
The Distiller module 'distills' the raw database into the best possible consistent set of addresses we can produce at the moment of execution. 

For Alpha, this mainly means identifying duplicates. For example data from Companies House can include more than one company are registered at the same addresses. 

In Beta the Distiller's role will become much more substantial. It will detect misspellings and alternative spellings; manage contradicting sources for the same addresses; calculate rigorous statistical confidence in each address; define the provenance record for the derived address data accordingly; and possibly even do cross-ETL inference. For example, can we infer the existence of an address from the presumed existence of other addresses submitted by different ETL modules and what level of confidence should be placed on it?

The Distiller is also responsible for creating unique identifiers for each unique component of all known addresses so that they can be referenced in a linked data fashion and published accordingly.

You are welcome to review or contribute to the current version of the Distiller on GitHub [here](https://github.com/OpenAddressesUK/distiller).

#<a name='publishingapis'>The publishing APIs</a>
Open Addresses offers several APIs (Application Programming Interfaces) to integrate our live data into your applications. During Alpha cover the following functionality:

- We publish each address component (street, locality, post town and postcode) and each address as URIs (unique resource identifiers) so that they can be easily referenced from third party applications. The URIs have the format *http://alpha.openaddressesuk.org/[***TO BE COMPLETED***]*. Browsing any of these URIs using a conventional Web browser will offer you a human-readable Web page of the data describing that address or address component.
- You can search the Open Addresses database by querying *http://alpha.openaddressesuk.org/[***TO BE COMPLETED***]*.

#<a name='downloadable'>The downloadable Open Addresses database</a>
Downloadable versions of the Open Addresses database are available, too. See the [download page](/download) for more detail. 

The software module responsible for producing the downloadable versions has been named "Hippodamus" after [Hippodamus of Miletus](https://en.wikipedia.org/wiki/Hippodamus_of_Miletus): an ancient Greek architect, urban planner, physician, mathematician, meteorologist and philosopher commonly considered to be the 'father of urban planning'. 

You are welcome to review or contribute to the current version of the Hippodamus on GitHub [here](https://github.com/OpenAddressesUK/hippodamus/).

#<a name='provenance'>How provenance is documented</a>
Given the sensitiveness of the intellectual property matters surrounding address data in the UK, it is critical for Open Addresses to work fully in the open and to document in detail how our data is created or derived from its sources. 

[All of our source code is published on GitHub](https://github.com/openaddressesuk) and the data we publish is available for distribution in formats that include sufficient provenance information to fully assess its origin. It is our ambition to give anybody the means to reproduce all of our work.

The typical provenance information associated to each address can be seen in the JSON code below [****NOT FINAL FORMAT****]. It references the exact source code that was run and the data sources that were used, and when the data processing and the download took place. Whenever technically and legally feasible [we also store unmodified copies of the source data for reference and due diligence](http://download.openaddressesuk.org.s3-website-eu-west-1.amazonaws.com/?prefix=sources_archive/), e.g. the five files listed in the example.

<pre><code><font size="-2">{
	(...)
	"provenance": {
		"activity":{
			"processing_scripts":"[LINK TO THE DISTILLER REPOSITORY **TREE** used for distillation]",
			"executed_at":"2014-11-19T09:53:14.000Z",
			"derived_from": [
				{
					"type":"Source",
					"description_url":"http://download.companieshouse.gov.uk/en_output.html",
					"url":[
						"http://download.companieshouse.gov.uk/BasicCompanyData-2014-11-01-part1_5.zip",
						"http://download.companieshouse.gov.uk/BasicCompanyData-2014-11-01-part2_5.zip",
						"http://download.companieshouse.gov.uk/BasicCompanyData-2014-11-01-part3_5.zip",
						"http://download.companieshouse.gov.uk/BasicCompanyData-2014-11-01-part4_5.zip",
						"http://download.companieshouse.gov.uk/BasicCompanyData-2014-11-01-part5_5.zip"
					],
					"name":"Companies House's \"Free Company Data Product\"",
					"downloaded_at":"2014-11-21T12:21:49.859Z",
					"processing_scripts":"https://github.com/OpenAddressesUK/common-ETL/tree/efcd9970fc63c12b2f1aef410f87c2dcb4849aa3"
				},
				(...)
			]
		}
	}
}</font></code></pre>

We are evaluating adopting a recognised standard for documenting provenance in Beta, such as W3C's [PROV](http://www.w3.org/TR/prov-dm/).
