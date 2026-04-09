# Query & Metric Inventory

Complete inventory of every panel in the AZ AIOps Dashboard workbook, organized by tab.

> **Legend — Query Type:**
> | Abbreviation | Meaning |
> |---|---|
> | KQL | Kusto Query Language — runs against a Log Analytics workspace (`AzureMetrics` table) |
> | ARG | Azure Resource Graph — cross-subscription resource query |
> | Metrics | Azure Monitor Metrics — native platform metric charts |

---

## Overview

| Panel Name | Title | Type | Query / Metrics |
|---|---|---|---|
| kpi-requests-piechart | Request Volume by API Type | KQL | `let aoaiTotal = toscalar(AzureMetrics \| where ResourceProvider =~ 'MICROSOFT.COGNITIVESERVICES' \| where MetricName == 'AzureOpenAIRequests' \| summarize sum(Total)); let modelsTotal = toscalar(AzureMetrics \| where ResourceProvider =~ 'MICROSOFT.COGNITIVESERVICES' \| where MetricName == 'ModelRequests' \| summarize sum(Total)); print Category = 'Azure OpenAI', Total = coalesce(aoaiTotal, 0.0) \| union (print Category = 'Models API', Total = coalesce(modelsTotal, 0.0) - coalesce(aoaiTotal, 0.0)) \| where Total > 0` |
| kpi-resource-map | Resources by Region | ARG | `Resources \| where type =~ 'microsoft.cognitiveservices/accounts' \| project location, ResourceName = name, Kind = tostring(kind), ResourceGroup = resourceGroup \| summarize Count = count() by location, ResourceGroup` |
| chart-aoai-requests-status | Azure OpenAI Requests by Status Code | Metrics | **AzureOpenAIRequests** · Sum · Split by StatusCode |
| chart-aoai-availability-deployment | Models API Requests by Status Code | Metrics | **ModelRequests** · Sum · Split by StatusCode |
| chart-model-requests-model | Model Requests by Model Name | Metrics | **ModelRequests** · Sum · Split by ModelName |
| table-resource-inventory | Cognitive Services Resource Inventory | ARG | `Resources \| where type =~ 'microsoft.cognitiveservices/accounts' \| extend ResourceId = tolower(id) \| join kind=leftouter (Resources \| where type =~ 'microsoft.insights/diagnosticsettings' \| extend parentId = tolower(tostring(split(id, '/providers/microsoft.insights/diagnosticsettings/')[0])) \| summarize DiagCount = count() by parentId) on $left.ResourceId == $right.parentId \| extend DiagnosticSettings = iff(isnotnull(DiagCount), 'Configured', 'Not Configured') \| project ResourceName = name, ResourceGroup = resourceGroup, Kind = tostring(kind), SKU = tostring(sku.name), Location = location, PublicAccess = tostring(properties.publicNetworkAccess), ProvisioningState = tostring(properties.provisioningState), DiagnosticSettings \| order by ResourceName asc` |

## Token Usage

| Panel Name | Title | Type | Query / Metrics |
|---|---|---|---|
| chart-prompt-vs-completion | Prompt vs. Completion Tokens by Deployment | Metrics | **ProcessedPromptTokens** · Sum · Split by ModelDeploymentName, **GeneratedTokens** · Sum · Split by ModelDeploymentName |
| chart-total-inference-tokens | Total Inference Tokens (Prompt + Completion) by Deployment | Metrics | **TokenTransaction** · Sum · Split by ModelDeploymentName |
| chart-active-tokens | Active Tokens — PTU / PTU-Managed (Excludes Cache Hits) | Metrics | **ActiveTokens** · Max · Split by ModelDeploymentName |
| chart-cache-match-rate | Prompt Token Cache Match Rate (%) by Deployment | Metrics | **AzureOpenAIContextTokensCacheMatchRate** · Max · Split by ModelDeploymentName |
| chart-audio-tokens | Audio Input + Output Tokens by Deployment | Metrics | **AudioPromptTokens** · Sum · Split by ModelDeploymentName, **AudioCompletionTokens** · Sum · Split by ModelDeploymentName |
| chart-realtime-usage | Realtime API Seconds Used by Deployment | Metrics | **RealtimeUsageTime** · Sum · Split by ModelDeploymentName |
| chart-tps-aoai | Tokens Per Second (Generation Speed) by Deployment | Metrics | **AzureOpenAITokenPerSecond** · Max · Split by ModelDeploymentName |

## Latency

| Panel Name | Title | Type | Query / Metrics |
|---|---|---|---|
| chart-ttr-aoai | Time to Response — Azure OpenAI (Max + Avg ms) | Metrics | **AzureOpenAITimeToResponse** · Max + Avg · Split by ModelDeploymentName |
| chart-ttr-models | Time to Response — Models API (Max + Avg ms) | Metrics | **TimeToResponse** · Max + Avg · Split by ModelDeploymentName |
| chart-ttfb-aoai | Normalized TTFB — Azure OpenAI (Max + Avg ms/token) | Metrics | **AzureOpenAINormalizedTTFTInMS** · Max + Avg · Split by ModelDeploymentName |
| chart-ttfb-models | Normalized TTFB — Models API (Max + Avg ms/token) | Metrics | **NormalizedTimeToFirstToken** · Max + Avg · Split by ModelDeploymentName |
| chart-tbt | Time Between Tokens — Azure OpenAI (Max ms) | Metrics | **AzureOpenAINormalizedTBTInMS** · Max · Split by ModelDeploymentName |
| chart-tbt-models | Time Between Tokens — Models API (Max ms) | Metrics | **NormalizedTimeBetweenTokens** · Max · Split by ModelDeploymentName |
| chart-ttlb | Time to Last Byte — Azure OpenAI (Max ms) | Metrics | **AzureOpenAITTLTInMS** · Max · Split by ModelDeploymentName |
| chart-ttlb-models | Time to Last Byte — Models API (Max ms) | Metrics | **TimeToLastByte** · Max · Split by ModelDeploymentName |
| chart-tps-models | Tokens Per Second — Models API (Max + Avg) | Metrics | **TokensPerSecond** · Max + Avg · Split by ModelDeploymentName |

## PTU Utilization

| Panel Name | Title | Type | Query / Metrics |
|---|---|---|---|
| chart-ptu-utilization | PTU Utilization V2 (%) — Max + Avg by Deployment | Metrics | **AzureOpenAIProvisionedManagedUtilizationV2** · Max + Avg · Split by ModelDeploymentName |
| chart-ptu-streamtype | PTU Utilization V2 (%) — Streaming vs. Non-Streaming | Metrics | **AzureOpenAIProvisionedManagedUtilizationV2** · Max · Split by StreamType |
| chart-ptu-active-tokens | Active Tokens — PTU Consumption Proxy (Max + Avg) | Metrics | **ActiveTokens** · Max + Avg · Split by ModelDeploymentName |
| chart-ptu-spillover | Spillover Requests — PTU Overflow to Spillover Pool | Metrics | **AzureOpenAIRequests** · Sum · Split by IsSpillover |
| chart-ptu-429 | Requests by Status Code (429 Throttling) | Metrics | **AzureOpenAIRequests** · Sum · Split by StatusCode |

## Content Safety

| Panel Name | Title | Type | Query / Metrics |
|---|---|---|---|
| kpi-rai-piechart | Content Safety Breakdown | KQL | `AzureMetrics \| where MetricName in ('RAITotalRequests','RAIHarmfulRequests','RAIRejectedRequests','RAIAbusiveUsersCount') \| summarize Total = sum(Total) by MetricName \| project Category = case(MetricName == 'RAITotalRequests', 'Total Volume', MetricName == 'RAIHarmfulRequests', 'Harmful Volume', MetricName == 'RAIRejectedRequests', 'Blocked Volume', MetricName == 'RAIAbusiveUsersCount', 'Abusive Users', MetricName), Total` |
| chart-rai-harmful-category | Harmful Volume by Category | Metrics | **RAIHarmfulRequests** · Sum · Split by Category |
| chart-rai-harmful-severity | Harmful Volume by Severity | Metrics | **RAIHarmfulRequests** · Sum · Split by Severity |
| chart-rai-blocked-category | Blocked Volume by Category | Metrics | **RAIRejectedRequests** · Sum · Split by Category |
| chart-rai-system-events | RAI System Events by EventType | Metrics | **RAISystemEvent** · Sum · Split by EventType |
| chart-content-safety-moderation | Content Safety — Image + Text Moderation Call Volume | Metrics | **ContentSafetyImageAnalyzeRequestCount** · Sum, **ContentSafetyTextAnalyzeRequestCount** · Sum |

## AI Agents

| Panel Name | Title | Type | Query / Metrics |
|---|---|---|---|
| chart-agent-runs-status | Agent Runs by Status | Metrics | **AgentRuns** · Sum · Split by RunStatus |
| chart-agent-responses-status | Agent Responses by Response Status | Metrics | **AgentResponses** · Sum · Split by ResponseStatus |
| chart-agent-tokens | Agent Input + Output Tokens by Model | Metrics | **AgentInputTokens** · Sum · Split by ModelName, **AgentOutputTokens** · Sum · Split by ModelName |
| chart-agent-tool-calls | Agent Tool Calls by Tool Name | Metrics | **AgentToolCalls** · Sum · Split by ToolName |
| chart-agent-threads | Agent Threads by EventType | Metrics | **AgentThreads** · Sum · Split by EventType |
| chart-agent-messages | Agent User Messages by EventType | Metrics | **AgentMessages** · Sum · Split by EventType |
| chart-agent-runs-model | Agent Runs by Model Name | Metrics | **AgentRuns** · Sum · Split by ModelName |
| chart-agent-indexed-files | Agent Indexed Files (Vector Store) by Status | Metrics | **AgentUsageIndexedFiles** · Sum · Split by Status |

## Logs & Diagnostics

| Panel Name | Title | Type | Query / Metrics |
|---|---|---|---|
| kql-request-volume | Request Volume Over Time by Metric | KQL | `AzureMetrics \| where ResourceProvider =~ 'MICROSOFT.COGNITIVESERVICES' \| where MetricName in ('AzureOpenAIRequests','ModelRequests') \| summarize Total = sum(Total) by bin(TimeGenerated, 15m), MetricName \| evaluate pivot(MetricName, take_any(Total)) \| extend ['Azure OpenAI'] = coalesce(AzureOpenAIRequests, 0.0), ['Models API'] = coalesce(ModelRequests, 0.0) - coalesce(AzureOpenAIRequests, 0.0) \| project-away AzureOpenAIRequests, ModelRequests \| render timechart` |
| kql-throttled-requests | Token Volume Over Time (Prompt + Completion) | KQL | `AzureMetrics \| where ResourceProvider =~ 'MICROSOFT.COGNITIVESERVICES' \| where MetricName in ('ProcessedPromptTokens','GeneratedTokens') \| summarize Total = sum(Total) by bin(TimeGenerated, 15m), MetricName \| render timechart` |
| kql-latency-percentiles | Availability Rate Over Time (%) | KQL | `AzureMetrics \| where ResourceProvider =~ 'MICROSOFT.COGNITIVESERVICES' \| where MetricName in ('AzureOpenAIAvailabilityRate','ModelAvailabilityRate') \| summarize AvgRate = avg(Average) by bin(TimeGenerated, 15m), MetricName, Resource \| render timechart` |
| kql-top-errors | Normalized Time to First Byte — Avg + Max (ms) | KQL | `AzureMetrics \| where ResourceProvider =~ 'MICROSOFT.COGNITIVESERVICES' \| where MetricName in ('AzureOpenAINormalizedTTFTInMS','NormalizedTimeToFirstToken') \| extend Source = case(MetricName == 'AzureOpenAINormalizedTTFTInMS', 'Azure OpenAI', 'Models API') \| summarize AvgLatencyMs = avg(Average), MaxLatencyMs = max(Maximum) by bin(TimeGenerated, 15m), Source \| render timechart` |
| kql-token-by-operation | Top Resources by Token Consumption | KQL | `AzureMetrics \| where ResourceProvider =~ 'MICROSOFT.COGNITIVESERVICES' \| where MetricName == 'TokenTransaction' \| summarize TotalTokens = sum(Total) by Resource, ResourceGroup \| top 20 by TotalTokens desc` |

## Capacity

| Panel Name | Title | Type | Query / Metrics |
|---|---|---|---|
| table-capacity-summary | Deployment Capacity Summary | ARG | `Resources \| where type =~ 'microsoft.cognitiveservices/accounts/deployments' \| extend ParentAccount = tostring(split(id, '/')[8]) \| project DeploymentName = name, ParentAccount, ResourceGroup = resourceGroup, Model = tostring(properties.model.name), ModelVersion = tostring(properties.model.version), SKU = tostring(sku.name), ['Capacity (K TPM)'] = toint(sku.capacity), ScaleType = tostring(properties.scaleSettings.scaleType), ProvisioningState = tostring(properties.provisioningState) \| order by ParentAccount asc, DeploymentName asc` |
| chart-capacity-by-model | Capacity Allocation by Model (K TPM) | ARG | `Resources \| where type =~ 'microsoft.cognitiveservices/accounts/deployments' \| project Model = tostring(properties.model.name), Capacity = toint(sku.capacity) \| summarize ['Total K TPM'] = sum(Capacity) by Model \| order by ['Total K TPM'] desc` |
| chart-capacity-by-sku | Capacity Allocation by SKU Type | ARG | `Resources \| where type =~ 'microsoft.cognitiveservices/accounts/deployments' \| project SKU = tostring(sku.name), Capacity = toint(sku.capacity) \| summarize ['Total K TPM'] = sum(Capacity) by SKU` |
| chart-capacity-token-throughput | Token Throughput by Deployment (Actual Usage) | Metrics | **TokenTransaction** · Sum · Split by ModelDeploymentName |
| chart-capacity-throttled | Throttled vs Total Requests by Deployment | Metrics | **AzureOpenAIRequests** · Sum · Split by ModelDeploymentName ("Total Requests"), **RatelimitedCalls** · Sum · Split by ModelDeploymentName ("Rate-Limited 429") |
| table-capacity-by-account | Capacity by Account (Aggregate) | ARG | `Resources \| where type =~ 'microsoft.cognitiveservices/accounts/deployments' \| extend ParentAccount = tostring(split(id, '/')[8]) \| project ParentAccount, DeploymentName = name, Model = tostring(properties.model.name), SKU = tostring(sku.name), ['Capacity (K TPM)'] = toint(sku.capacity) \| summarize Deployments = count(), ['Total K TPM'] = sum(['Capacity (K TPM)']) by ParentAccount \| order by ['Total K TPM'] desc` |
| table-capacity-model-sku | Capacity by Model + SKU Breakdown | ARG | `Resources \| where type =~ 'microsoft.cognitiveservices/accounts/deployments' \| project Model = tostring(properties.model.name), SKU = tostring(sku.name), ['Capacity (K TPM)'] = toint(sku.capacity), ScaleType = tostring(properties.scaleSettings.scaleType) \| summarize Deployments = count(), ['Total K TPM'] = sum(['Capacity (K TPM)']) by Model, SKU, ScaleType \| order by ['Total K TPM'] desc` |

## Inventory

| Panel Name | Title | Type | Query / Metrics |
|---|---|---|---|
| table-cog-resources | Cognitive Services Resources | ARG | `Resources \| where type =~ 'microsoft.cognitiveservices/accounts' \| project ResourceName = name, ResourceGroup = resourceGroup, Kind = tostring(kind), SKU = tostring(sku.name), Location = location, Endpoint = tostring(properties.endpoint), CustomDomain = tostring(properties.customSubDomainName), PublicAccess = tostring(properties.publicNetworkAccess), ProvisioningState = tostring(properties.provisioningState), SubscriptionId = subscriptionId \| order by ResourceName asc` |
| table-model-deployments | Model Deployments | ARG | `Resources \| where type =~ 'microsoft.cognitiveservices/accounts/deployments' \| project DeploymentName = name, ParentResource = tostring(split(id, '/')[8]), ResourceGroup = resourceGroup, Model = tostring(properties.model.name), Version = tostring(properties.model.version), Format = tostring(properties.model.format), SKU = tostring(sku.name), Capacity = tostring(sku.capacity), ScaleType = tostring(properties.scaleSettings.scaleType), ProvisioningState = tostring(properties.provisioningState) \| order by ParentResource asc, DeploymentName asc` |
| table-private-endpoints | Private Endpoints — AI / Cognitive Services | ARG | `Resources \| where type =~ 'microsoft.network/privateendpoints' \| mv-expand connection = properties.privateLinkServiceConnections \| where tostring(connection.properties.privateLinkServiceId) contains 'cognitiveservices' \| project PrivateEndpoint = name, ResourceGroup = resourceGroup, Location = location, LinkedResource = tostring(split(tostring(connection.properties.privateLinkServiceId), '/')[8]), ConnectionState = tostring(connection.properties.privateLinkServiceConnectionState.status) \| order by LinkedResource asc` |
| table-diagnostic-settings | Model Deployments — Full Detail | ARG | `Resources \| where type =~ 'microsoft.cognitiveservices/accounts/deployments' \| project DeploymentName = name, ParentAccount = tostring(split(id, '/')[8]), ResourceGroup = resourceGroup, Model = tostring(properties.model.name), Version = tostring(properties.model.version), Format = tostring(properties.model.format), SKU = tostring(sku.name), Capacity = tostring(sku.capacity), ScaleType = tostring(properties.scaleSettings.scaleType), ProvisioningState = tostring(properties.provisioningState) \| order by ParentAccount asc, DeploymentName asc` |

---

**Totals:** 64 panels — 13 KQL · 14 ARG · 37 Azure Monitor Metrics
