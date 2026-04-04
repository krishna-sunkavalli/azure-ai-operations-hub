# Workbook Overview

Detailed reference for each section of the Azure AI Operations Hub workbook.

## Overview Tab

### Request Volume by API Type

**Type:** Pie chart (KQL on Log Analytics)  
**Data source:** `AzureMetrics` table, `ResourceProvider = 'MICROSOFT.COGNITIVESERVICES'`  
**Metrics:** `AzureOpenAIRequests`, `ModelRequests`  
**Groups:**

| Slice | MetricName |
|-------|------------|
| Azure OpenAI | `AzureOpenAIRequests` |
| Models API | `ModelRequests` |

**Note:** This chart requires Log Analytics Workspace to be set. Resources must have Diagnostic Settings configured to send metrics to a workspace.

---

### Resources by Region

**Type:** Map  
**Data source:** Azure Resource Graph  
**Query:**
```kql
Resources
| where type =~ 'microsoft.cognitiveservices/accounts'
| project location, ResourceName = name, Kind = tostring(kind), ResourceGroup = resourceGroup
| summarize Count = count() by location, ResourceGroup
```

---

### Azure OpenAI Requests by Status Code

**Type:** Line chart (MetricsItem)  
**Metric:** `AzureOpenAIRequests`  
**Split by:** `StatusCode`  
**Common status codes:**

| Code | Meaning |
|------|---------|
| 200 | Success |
| 429 | Throttled (rate limit) |
| 5xx | Service error |

---

### Models API Requests by Status Code

**Type:** Line chart (MetricsItem)  
**Metric:** `ModelRequests`  
**Split by:** `StatusCode`  
Same status code meanings as above, for Models API traffic specifically.

---

### Model Requests by Model Name

**Type:** Bar chart (MetricsItem)  
**Metric:** `ModelRequests`  
**Split by:** `ModelName`  
Shows relative request volume per deployed model (e.g., `gpt-4o`, `gpt-4o-mini`, `text-embedding-ada-002`).

---

### Cognitive Services Resource Inventory

**Type:** Grid table (Azure Resource Graph)  
**Query:**
```kql
Resources
| where type =~ 'microsoft.cognitiveservices/accounts'
| extend ResourceId = tolower(id)
| join kind=leftouter (
    Resources
    | where type =~ 'microsoft.insights/diagnosticsettings'
    | extend parentId = tolower(tostring(split(id, '/providers/microsoft.insights/diagnosticsettings/')[0]))
    | summarize DiagCount = count() by parentId
) on $left.ResourceId == $right.parentId
| extend DiagnosticSettings = iff(isnotnull(DiagCount), 'Configured', 'Not Configured')
| project ResourceName = name, ResourceGroup = resourceGroup, Kind = tostring(kind),
          SKU = tostring(sku.name), Location = location,
          PublicAccess = tostring(properties.publicNetworkAccess),
          ProvisioningState = tostring(properties.provisioningState),
          DiagnosticSettings
```

**Columns:**

| Column | Description |
|--------|-------------|
| ResourceName | Cognitive Services account name |
| ResourceGroup | Containing resource group |
| Kind | Account kind (e.g., `OpenAI`, `CognitiveServices`) |
| SKU | Pricing tier (e.g., `S0`) |
| Location | Azure region |
| PublicAccess | `Enabled` or `Disabled` |
| ProvisioningState | `Succeeded`, `Creating`, etc. |
| DiagnosticSettings | `Configured` (green) or `Not Configured` (red) |

> Resources showing **Not Configured** for Diagnostic Settings will not send logs or metrics to Log Analytics — the Request Volume pie chart and other KQL-based queries will return no data for those resources.

## Parameters

| Parameter | Type | Source | Notes |
|-----------|------|--------|-------|
| Subscription | Multi-select dropdown | ARM | Defaults to All |
| ResourceGroup | Multi-select dropdown | ARM, filtered by Subscription | Defaults to All |
| CogServicesResource | Multi-select dropdown | ARG `microsoft.cognitiveservices/accounts` | Defaults to All |
| LogAnalyticsWorkspace | Multi-select dropdown | ARM `microsoft.operationalinsights/workspaces` | Defaults to All |
| TimeRange | Time range picker | — | Applied to all metric charts |
