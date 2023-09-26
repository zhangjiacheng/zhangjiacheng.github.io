---
layout: post
title: "Salesforce Sales Cloud Forecasting"
date: 2023-09-25
description: "Sales Forecasting in Salesforce"
tag: Salesforce
mermaid: true
---   

## Data Model In Salesforce Sales Cloud

<img src="https://architect.salesforce.com/1/asset/immutable/s/a86a100/assets/images/data-models/sales-cloud-opportunity-forecasting-data-model.png" alt="Data Model of Salesforce Forecast"/>

## The supported Data Source Objects which Sales Cloud Forecasting is based on
- Opportunity (API Name: Opportunity)
- Opportunity Product (API Name: OpportunityLineItem)
- Opportunity Split (API Name: OpportunityLineItemSplit)
- Line Item Schedule (API Name: OpportunityLineItemSchedule)

> **Notes:** The forecasting is centered on Opportunity (The source data the forecasting based on is either Opportunity or Opportunity related data ) , so creating a data culture is the critical important  for a Company to successfully rolling out the Sales Cloud forecast.

## Two types of hierarchy that Sales Cloud Forecasting supports to group 
- User role (role-based forecasts hierarchy is automatically generated based on your `Role Hierarchy`)
- Territory (territory-based forecasts hierarchy is relied on the Enterprise `Territory Models` , need enable Enterprise Territory Management first ).

> **Notes:** For role-based forecasts hierarchy,  users must be in the role-based forecasts hierarchy within their respective roles and also a user in the role-based forecasts hierarchy must be selected  to act as the forecast manager to be able to view subordinates’ forecasts.

## Forecast categories

**Notes:** Based on forecast period (monthly or quarterly) and Opportunity Stage, Sales Cloud accumulates the forcasts data into Categories: `Pipeline`, `Best Case`, `Commit`, `Closed`, `Omitted`. 

  <table>
    <tbody>
      <tr>
        <th>Forecast Category</th>
        <th>Opptunity Stage</th>
        <th>Probablity</th>
      </tr>
      <tr>
        <td>Pipeline</td>
        <td>Prospecting</td>
        <td>10%</td>
      </tr>
      <tr>
        <td>Pipeline</td>
        <td>Qualification</td>
        <td>10%</td>
      </tr>
      <tr>
        <td>Pipeline</td>
        <td>Needs Analysis</td>
        <td>20%</td>
      </tr>
      <tr>
        <td>Pipeline</td>
        <td>Value Proposition</td>
        <td>50%</td>
      </tr>
      <tr>
        <td>Pipeline</td>
        <td>Id. Decision Makers</td>
        <td>60%</td>
      </tr>
        <tr>
        <td>Best Case</td>
        <td>Perception Analysis</td>
        <td>70%</td>
      </tr>
      <tr>
        <td>Best Case</td>
        <td>Proposal/Price Quote</td>
        <td>75%</td>
      </tr>
      <tr>
        <td>Commit</td>
        <td>Negotiation/Review</td>
        <td>90%</td>
      </tr>
      <tr>
        <td>Closed</td>
        <td>Closed Won</td>
        <td>100%</td>
      </tr>
      <tr>
        <td>Omitted</td>
        <td>Closed Lost</td>
        <td>0%</td>
      </tr>
    </tbody>
  </table>


## Related Forecasts Settings

- Forecast Types 
   - Object (to select the source data Object which the forecasts are based on )
   - Measure (Options: `Amount`, `Quantity`, the measure factor of the forecasts )
   - Date (Fields like CloseDate of Opportunity and Date of OpportunityLineItem, to determine the records that should included based on the Forecasts Date range)
   - Hierarchy (`User role` or `Territory`)
- Enable Adjustments
   - Manager Adjustments
   - Owner Adjustments
- Manage Forecast Rollups
   - Single category rollups - Show one forecast category per column (Closed, Commit, Best Case, and Pipeline)
   - Cumulative category rollups - Show multiple related forecast categories per column
- Choose a Default Date Range
   - Forecast Period (`Monthly` or `Quarterly`)
   - Starting on 
   - Extending for
- Show Quotas (whether show quotas in the Forecasts page)

## Forecasts Report

> **Notes:** Following report is created from the Report type `Forecasting Items with Historical Trending` Salesforce provided.

  <table>
    <tbody>
      <tr>
        <th>Forecasting Type</th>
        <th>Forecast Category</th>
        <th>Start Date</th>
        <th>End Date</th>
        <th>Forecast Amount</th>
        <th>Amount Without Adjustments</th>
        <th>Amount Without Manager Adjustment</th>
      </tr>
      <tr>
        <td>OpportunityRevenue</td>
        <td>Pipeline</td>
        <td>2023/9/1</td>
        <td>2023/9/30</td>
        <td>$1,269,390</td>
        <td>$1,269,390</td>
        <td>$1,269,390</td>
      </tr>
    </tbody>
  </table>


## How to use Forecast Process for the Sales team
<div class="mermaid">
sequenceDiagram;
  SalesRep->>SalesManager: Identify and Create Opportunity in timely manner;
  SalesManager-->>SalesRep: Inspecting and Mornitoring pipeline;
  SalesManager->>CSuite: Adding judgment and commits;
  CSuite-->>SalesManager: Investigate trend and redirect resource;
  
</div>