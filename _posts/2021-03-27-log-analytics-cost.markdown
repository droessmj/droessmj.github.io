---
layout: post
title:  "How to Lower Your Azure Bill - Log Analytics Workspace"
date:   2021-03-27 15:00:00
categories: azure cost
---

If you’ve been using Azure for anything beyond POC work, you’ve probably  discovered that Log Analytics is a great way to collect logs from Azure Monitor. Scattered through Azure documentation you’ll find numerous links and articles describing how to capture platform logs to target sinks. Log Analytics, Event Hubs, and Storage Accounts are the three most common, and of these three, Log Analytics is by far the easiest to immediately query and answer questions. 

Once you’ve turned on platform logs for multiple (tens, hundreds) of resources and begun to collect (and retain) logs, you’re eventually going to notice an uptick in your costs. Depending on what you’re collecting for various resources and whether you do so across the board or only in select environments, this bill could work its way towards the top of your billing statement. This post won’t help you architect how you manage workspaces in your Azure environment, but hopefully you learn of a few gotchas to help lower your monthly bill. 

Note: The following links are all to the same article on Microsoft’s documentation site. If you find this post valuable, you probably should consider reading this linked article in its entirety. 

#### [Where to Collect Logs?](https://docs.microsoft.com/en-us/azure/azure-monitor/essentials/diagnostic-settings#destinations) 
First off, ask yourself if the logs being collected in your workspace are being used frequently, or are just a safety net. It’s going to be much cheaper to store the same logs in a Storage Account if you need them “just in case”. Storing these logs in a workspace is super convenient for immediate querying and access, while additional processes need to be built out to effectively consume these same logs as a developer from Event Hubs or Storage Account sinks. If you need to collect logs for compliance reasons and have no immediate plans to consume them for troubleshooting, Log Analytics is probably the worst sink to default to from a cost perspective. 

#### [How Long Should I Hold Them?](https://docs.microsoft.com/en-us/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period)
Next, know that your first 31 days of retention are free in Log Analytics. After 31 days, you pay per GB for additional retention. Note that you pay a decent price to initially ingest so I’d highly encourage you to take full advantage of your 31 free days. However, you should take a moment to think about whether you want to keep data beyond that point as this retention fee can add up quickly should you look to retain data for 90, 180, or even 365 days.  

#### [Can I Cap My Costs?](https://docs.microsoft.com/en-us/azure/azure-monitor/logs/manage-cost-storage#manage-your-maximum-daily-data-volume)
For anyone who’s ever woken up to a surprise cloud bill due to runaway cost accumulation (and for those of you that have been lucky to this point), know that Log Analytics Workspaces allow you to cap the amount a given workspace will ingest in a given day. I would highly encourage anyone whose knees shake at the thought of a 3x-5x daily workspace bill to put a cap in place at a number which is well over your peak ingestion with room to grow. Don’t forget to setup Alerts to trigger and notify you when this cap is hit!


#### [Why am I Seeing So Much Data Ingestion?](https://docs.microsoft.com/en-us/azure/azure-monitor/logs/manage-cost-storage#understanding-ingested-data-volume)
Now that you have your retention timeline under control and have capped your ingestion rate, the following link can be used to asses where exactly all the logs are coming from. Don’t be afraid to go back to point one and ask yourself whether you should be collecting these logs within a workspace, and if you must, at least understand the relative volumes of various log types. 

As a quick aside, we discovered the AKS clusters have diagnostic logs for both “kube-admin” and kube-admin-audit”.  A bit of digging showed that over 96% of our AzureDiagnostic logs were coming from “kube-admin” — which contains more or less all CRUD activities performed for cluster management. For our needs, we only required the Create, Update, and Delete portions, which seemed to overlap extremely well with the “kube-admin-audit” diagnostic type. Updating this collection setting reduced the amount of logs being collected by over 75%, ultimately saving tens of thousands of dollars a year at our scale. 

---

<br/>
The content above is the first of hopefully a few hard won lessons on how to lower a large Azure bill by looking into the nooks and crannies where costs silently accumulate. Feel free to drop me a note if you’ve found this helpful or have suggestions I should add!


