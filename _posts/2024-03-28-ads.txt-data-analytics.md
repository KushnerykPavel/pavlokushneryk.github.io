## Ads.txt files analytics

----
### Introduction

The issue of transparency in the programmatic advertising sector has long been a prominent concern. Organizations such 
as IAB, TAG, and the Digital Advertising Alliance have played crucial roles in advancing safeguards for publishers and 
advertisers against unscrupulous suppliers. A pivotal moment in this ongoing effort occurred in 2016 with the 
introduction of ads.txt by IAB, representing a significant initiative to tackle the pervasive issue of ad fraud across
the industry. Despite initial slow uptake, the project has since gained widespread recognition as a standard practice. 
Nowadays, the majority of publishers and advertisers rely on ads.txt to verify that their ad impressions reach genuine
human audiences.

### What is ads.txt?

Ads.txt, short for Authorized Digital Sellers, is a document utilized by publishers to enumerate trusted digital sellers
authorized to vend their ad space. These files are openly accessible to ad exchanges and SSPs, aiming to enhance 
transparency for programmatic buyers and reduce instances of ad fraud.

The initiative caters to both publishers and advertisers, shielding publishers from unauthorized ad sales and enabling
advertisers to verify sellers to prevent dubious transactions. While its use is not obligatory, it has become a widely 
accepted practice within the industry.

Initially confined to web-based platforms, the concept swiftly extended to the realm of OTT advertising, with the 
app-ads.txt file serving as its equivalent on OTT applications.

All requisite data for crafting this document is obtainable through the OpenRTB protocol, offering robust 
targeting capabilities.

This file accommodates three primary types of supplier relationships:

1. Domain owners who peddle inventory on ad exchanges.
2. Ad networks and agencies facilitating programmatic inventory sales.
3. Content syndication partners.

### Ads.txt (app-ads.txt) file example

The ads.txt file usually located at the root of the domain, contains a list of authorized sellers and their IDs.
Here is an example of an ads.txt file:

```txt
33across.com, 0010b00001u7BhSAAU, DIRECT, bbea06d9c4d2853c
acd.op.hicloud.com, PUB_HW_1003, RESELLER
ad-generation.jp, 1234, DIRECT, 7f4ea9029ac04e53OWNERDOMAIN=excite.co.jp
33across.com, 0010b00001u7BhSAAU, DIRECT, bbea06d9c4d2853c
```

where: 
- `33across.com` is the domain of the advertising system.
- `0010b00001u7BhSAAU` is the publisher's account ID.
- `DIRECT` type of account or relationship.
- `bbea06d9c4d2853c` is the certification authority ID.

### Article overview

In this article, we will delve into the process of extracting and analyzing data from ads.txt files. We will explore
about 1.5M websites worldwide. The dataset contains information about the websites and their ads.txt data. Over all 
we found 185,862,247 records which have the following structure:
```json
{
  "seller_domain": "spotxchange.com", 
  "file_type": "ads.txt", 
  "seller_type": "RESELLER", 
  "crawled_domain": "nerds.co", 
  "publisher_id": "84294", 
  "num_links": 2, 
  "uploaded_at": "2023-12-05 16:09:54.840380"
}
```
where:
- `seller_domain` is the domain of the advertising system.
- `file_type` is the type of file (ads.txt or app-ads.txt).
- `seller_type` is the type of account or relationship DIRECT/RESELLER.
- `crawled_domain` is the domain of the website.
- `publisher_id` is the publisher's account ID.
- `num_links` number of back links to current website.
- `uploaded_at` crawling date.

All data for reports available in the [adscrawl-hub.pp.ua](https://adscrawl-hub.pp.ua/).

### Top ssp/ad-exchanges by number of records
Top 10 SSP/Ad-exchanges by the number of records in the dataset over all:

| seller\_domain | records  |
| :--- |:---------|
| pubmatic.com | 15356972 |
| rubiconproject.com | 11423797 |
| appnexus.com | 10163792 |
| google.com | 9754278  |
| openx.com | 8515773  |
| freewheel.tv | 6598848  |
| indexexchange.com | 6556082  |
| smartadserver.com | 6395238  |
| yahoo.com | 4284554  |
| improvedigital.com | 3639971  |

Top 10 SSP/Ad-exchanges with DIRECT integrations:

| seller\_domain | records |
| :--- | :--- |
| google.com | 3494091 |
| lijit.com | 1407607 |
| rubiconproject.com | 1370014 |
| pubmatic.com | 1365567 |
| indexexchange.com | 1351504 |
| appnexus.com | 1143332 |
| taboola.com | 1094012 |
| smartadserver.com | 985610 |
| openx.com | 889408 |
| triplelift.com | 873630 |

Google and lijit (Sovrn) are the top SSP/Ad-exchanges with the most direct integrations. Pubmatic, Rubicon Project, and
Appnexus are also among the top SSP/Ad-exchanges with the most integrations.

### Top 10 direct ads.txt sellers

---
Ads.txt file used by web pulishers. Here are the top 10 direct sellers by the number of records in the dataset:

| seller\_domain | records |
| :--- | :--- |
| google.com | 3275467 |
| lijit.com | 1335695 |
| rubiconproject.com | 1277375 |
| indexexchange.com | 1271600 |
| pubmatic.com | 1258613 |
| appnexus.com | 1072683 |
| smartadserver.com | 935927 |
| openx.com | 824981 |
| triplelift.com | 789589 |
| themediagrid.com | 787313 |

### Top 10 direct app-ads.txt sellers

---
App-ads.txt file used by mobile app publishers. Here are the top 10 direct sellers by the number of records in the dataset:

| seller\_domain | records |
| :--- | :--- |
| facebook.com | 746957 |
| taboola.com | 350075 |
| google.com | 218624 |
| bloxdigital.com | 170302 |
| pubmatic.com | 106954 |
| rubiconproject.com | 92639 |
| triplelift.com | 84041 |
| indexexchange.com | 79904 |
| lijit.com | 71912 |
| appnexus.com | 70649 |

### Conclusion

---
In conclusion, ads.txt files play a crucial role in the digital advertising ecosystem by providing transparency 
and accountability. Through effective data analytics, publishers and advertisers can gain valuable insights into
their ad inventory, identify unauthorized sellers, and optimize their monetization strategies. Volume of data which 
currently available for analysis allows you build solution which can track most of the publishers websites and their 
inventory. Example of such project is [adscrawl-hub.pp.ua](https://adscrawl-hub.pp.ua/) which allow you to check ads.txt
data for any website.