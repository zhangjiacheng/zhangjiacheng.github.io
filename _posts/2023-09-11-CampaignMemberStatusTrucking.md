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
    Lead-->CampaignMember;
    Contact-->CampaignMember;
    CampaignMember-->Campaign;
    Campaign-->Opportunity;
</div>

> **How to Track:** Leads and Contacts can be added as Campaign Members who will participate  the Campaign. And in Salesforce there is a field 'Status' to track the engagement of the Campaign, say the Campaign member status set to 'Accepted' or 'Attended' for a Webinar Campaign.