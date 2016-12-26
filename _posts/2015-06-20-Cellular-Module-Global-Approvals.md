---
layout: post
title: Beyond FCC - Exploring International RF Regulations
---

While hardware which intentionally radiates RF falls under [FCC Part 15](http://www.digikey.com/en/articles/techzone/2011/mar/the-fcc-road-part-15-from-concept-to-approval) in the US, other countries might not be so clear.  This post introduces a few of the regulatory frameworks and bodies operative outside of the US which apply to GSM/cellular communications.

-----

### R&TTE

If you intend to sell a product with radio communication in the EU, [R&TTE](http://www.tuv.com/en/usa/services_usa/product_testing/telecom_it/rtte_directive/rtte_directive.html) compliance will likely be required.  GSM/M2M modules can be [individually certified as compliant with R&TTE](http://www.tuv-sud.co.uk/uploads/images/1365171464129234960088/uk-mkg-guide-to-wireless-m2m-module-integration-en-uk.pdf), which reduces the testing burden.  A number of third party test facilities (TUV, 7-Layers, IB Lenhardt, etc.) exist which can help take a product through the R&TTE certification process.

### GCF

The [GCF](http://www.globalcertificationforum.org/) ("Global Certification Forum") operates under the 'test once, use anywhere' philosophy, and focuses on devices which communicate on the cellular network.  When sourcing a wireless module for a product intended for global use, checking if the module is listed in the GCF ["Certified Devices List"](http://www.globalcertificationforum.org/devices/certified-devices.html) provides some basic assurance that the module will be accepted onto most carrier networks without issue (in the US, however, the GCF equivalent is the "PTCRB", mentioned at the end of this post).  When sourcing a GSM module, ensure it has passed GCF, or be sure that the carriers in the countries of intended operation don't care either way. 

### Type Approvals

Typically, these are approvals that are required to market and/or sell a device within a given country.  For example, the [Pakistan Telecommunications Authority](http://www.pta.gov.pk/index.php?Itemid=180) notes that:

	Under the Telecom Act 1996 (section 29), no terminal equipment can be directly or indirectly connected with PSTN unless it has been type approved by 	PTA.

This includes wireless modules, such as the popular SIM900/908 modules from SIMCOM.  Note on the [2G/3G Approved List](http://www.pta.gov.pk/media/ta/2g_3g_lte_mod_191214.pdf) that the PTA authorizes a specific 'applicant' to sell these modules in country.  For a different approach where the module appears to be listed without an applicant, note the Rwandan [Type Approved Mobile Phones](http://www.rura.rw/fileadmin/docs/TYPE_APPROVED_mobile_handsets_2015.pdf) from January 2015, where only the model name and manufacturer are listed.  Not all countries have a straightforward type-approval requirement:  India, for example, requires no type approval for GSM devices, but China potentially requires [up to three different government-regulated approvals](http://www.7layers.com/#!/type-approval/type-approval-asia/p-r-of-china/). 

### PTCRB

This agency is technically US-only, but it's worth including here to highlight the fact that there are additional regulatory standards outside of the FCC that can apply to GSM-based products.  The organization is a consortium of participating wireless carriers (e.g. AT&T, Verizon) which exists to test expected mobile device performance on a carrier network, using 3GPP and carrier-specific standards.  In general, a carrier participating in PTCRB won't allow a device which has not passed PTCRB certification to connect to it's network at any reasonable scale.  This certification can cost between [$16k-20k for a data-only device](http://m2msupport.net/m2msupport/ptcrb-process-costs-timeline-and-labs/). The interplay between FCC, PTCRB, and additional carrier-specific standards is briefly explored [here by ECNMag](http://www.ecnmag.com/articles/2012/07/cellular-carrier-certification-requirements).  The TL:DR version: **[just use a PTCRB approved module](https://www.ptcrb.com/vendor/complete/complete_request.cfm)**.

