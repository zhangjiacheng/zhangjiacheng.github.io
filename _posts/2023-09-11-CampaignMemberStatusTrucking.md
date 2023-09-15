---
layout: post
title: "Campaign Member Status Tracking"
date: 2023-09-11
description: "Campaign Member Status Tracking in Salesforce"
tag: Salesforce
mermaid: true
---   

## Data Model In Salesforce Sales Cloud

<div class="mermaid">
graph LR;
    Lead-->|JoinAs|CampaignMember;
    Contact-->|JoinAs|CampaignMember;
    CampaignMember-->|Participate|Campaign;
    Campaign-->|Influence|Opportunity;
</div>

## What is Campaign Member Status
Leads and Contacts can be added as Campaign Members who will participate  the Campaign. And in Salesforce there is a field 'Status' to track the engagement of the Campaign, say the Campaign member status set to 'Accepted' or 'Attended' for a Webinar Campaign.

- The default options of the Campaign Member Status are:
   - Sent
   - Received
   - Responsed
- Salesforce provides the `Campaign Member Statuses` to add or edit the Status options.


## Why tracking Campaign Member Status
Campaign member status is one of most important business metrics to measure the engagment and performance of the Campaign, and is one important factor to evaluate the ROI (Return of Inverstment) of the Campaign.
Salesforce provide the out-of-the-box  `Campaigns with Campaign Members` report type to create the report to gain insight of the Campaign.

## How to tracking Campaign Member Status
Salesforce can let the user to record the Campaign member status individually though User Interface or Manage Campaign Member Statuses in batch though import wizard. But these two ways are done by users in Salesforce Sales Cloud manually, which is not good for the Campaign with huge Campaign Members (for example Email Campaign with lots of senders). In that case, we can automate the tracking process though the integration with other Marketing Automation Platforms.

- Adobe Marketo 
  - Marketo out-of-the-box integration with Salesforce, and can use the `Change Program Status` flow step of Marketo Smart Campaign to update the Campaign Member Status in Salesforce Sales Cloud automatically.
- Salesforce Pardot (Salesforce has renamed it to Account Engagement)
  - Pardot out-of-the-box integration with Salesforce, but need enable `Enable Campaign Member Sync` in Pardot.
- Salesforce Marketing Cloud
  - Write SSJS in Marketing Cloud to push the records from Marketing Cloud to Salesforce Sales Cloud.