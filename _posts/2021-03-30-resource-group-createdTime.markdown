---
layout: post
title:  "How To: Retrieve Azure Resource Group Created Time"
date:   2021-03-30 07:00:00
categories: azure howto
---
If you’ve ever been in a position where you have hundreds or thousands of resource groups created over a number of years, you may stumble upon the realization that you need to identify a subset of resource groups that pre-date a current convention. While tagging could suffice, sometimes you just want to draw a line in the sand and identify all resource groups that pre- or post-date a certain time.  

At the time of writing, the latest Az PowerShell and Az CLI versions don’t yet return createdTime as a property, but don’t let that stop us. Instead, we’ll hit the ARM API for resource groups directly for this information.  Note the “expand” query string parameter which requests the createdTime property be returned in the response. 

```powershell
# replace with your values
$subscriptionID = "11111-111-1111-111111"
$resourceGroupName = "myResourceGroup"

#assumes you have an active Az CLI context authenticated to target subscription. Can also be done with Az PowerShell, but requires a  different token retrieval method
$accessToken = az account get-access-token --query accessToken

#specify target API endpoint
$uri = "https://management.azure.com/subscriptions/$($subscriptionID)/resourcegroups/$($resourceGroupName)?api-version=2019-08-01&%24expand=createdTime"

$headers = @{
    "Authorization" = "Bearer $($accessToken)"
}

#consume ARM endpoint
$rgInfo = Invoke-RestMethod -Uri $uri -Headers $headers -Method 'GET'

$creationDate = $rgInfo.createdTime
```

I’ve personally used the above to identify resource groups which are “grandfathered” into older compliance rules (I know, I know — not great but change doesn’t always happen overnight) as well as to identify empty, forgotten resource groups which only create liability if left to linger. 
