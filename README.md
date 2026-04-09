# AZ AIOps Dashboard

[![Validate](https://github.com/krishna-sunkavalli/az-aiops-dashboard/actions/workflows/validate.yml/badge.svg)](https://github.com/krishna-sunkavalli/az-aiops-dashboard/actions/workflows/validate.yml)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

An [Azure Monitor Workbook](https://learn.microsoft.com/azure/azure-monitor/visualize/workbooks-overview) for day-to-day operational monitoring of Azure AI and Cognitive Services resources. Track request volumes, status codes, availability, model usage, and resource inventory — all in one shareable dashboard with no agents, no code, and no external dependencies.

[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fkrishna-sunkavalli%2Faz-aiops-dashboard%2Fmain%2Fworkbook%2Fazuredeploy.json)

<!-- TODO: Add screenshot — save as docs/screenshot.png -->

## What It Shows

### Overview Tab

| Section | Description |
|---------|-------------|
| **Request Volume by API Type** | Pie chart — Azure OpenAI vs Models API request split |
| **Resources by Region** | Interactive map of Cognitive Services resources by Azure region |
| **Azure OpenAI Requests by Status Code** | Line chart of AOAI requests split by HTTP status code |
| **Models API Requests by Status Code** | Line chart of Models API requests split by HTTP status code |
| **Model Requests by Model Name** | Bar chart of model-level request volumes |
| **Resource Inventory** | Table with Name, Resource Group, Kind, SKU, Location, Public Access, Provisioning State, and Diagnostic Settings status |

For the complete list of every panel, query, and metric across all 9 tabs, see [docs/queries.md](docs/queries.md).

### Parameters

| Parameter | Default | Description |
|-----------|---------|-------------|
| Subscription | All | Filter to one or more Azure subscriptions |
| Resource Group | All | Filter to specific resource groups |
| Cognitive Services Resource | All | Filter to specific AI/Cognitive Services accounts |
| Log Analytics Workspace | All | Workspace for KQL-based queries |
| Time Range | Last 24 hours | Time window for all metric charts |

## Getting Started

### Prerequisites

- An Azure subscription with **Reader** role on Cognitive Services resources
- No additional software required — the workbook runs entirely in the Azure Portal

### Option 1: Deploy to Azure (recommended)

Click the **Deploy to Azure** button above, or use the direct link:

```
https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fkrishna-sunkavalli%2Faz-aiops-dashboard%2Fmain%2Fworkbook%2Fazuredeploy.json
```

### Option 2: Azure CLI

```bash
az group create --name rg-ai-ops-hub --location eastus

az deployment group create \
  --resource-group rg-ai-ops-hub \
  --template-file workbook/azuredeploy.json \
  --parameters workbookDisplayName="AZ AIOps Dashboard"
```

### Option 3: Azure PowerShell

```powershell
New-AzResourceGroup -Name "rg-ai-ops-hub" -Location "eastus"

New-AzResourceGroupDeployment `
  -ResourceGroupName "rg-ai-ops-hub" `
  -TemplateFile "workbook/azuredeploy.json" `
  -workbookDisplayName "AZ AIOps Dashboard"
```

### Option 4: Manual Import

1. Open **Azure Portal** → **Monitor** → **Workbooks**
2. Click **+ New** → **Advanced Editor** (`</>` icon)
3. Paste the contents of [`workbook/az-aiops-dashboard.workbook`](workbook/az-aiops-dashboard.workbook)
4. Click **Apply** → **Done Editing** → **Save**

## Using the Workbook

1. **Set Parameters** — Use the pickers at the top to scope by Subscription, Resource Group, and Resource
2. **Overview** — Review the request volume split and resource map at a glance
3. **Request Charts** — Use the status code line charts to spot errors (5xx) and throttling (429)
4. **Inventory Table** — Check Diagnostic Settings coverage — resources showing **Not Configured** won't surface logs in Log Analytics

## Repository Structure

```
├── workbook/
│   ├── az-aiops-dashboard.workbook   # The Azure Workbook (source of truth)
│   └── azuredeploy.json             # ARM template for one-click deployment
├── docs/
│   ├── OVERVIEW.md                  # Detailed tab and query reference
│   └── queries.md                   # Full inventory of every panel query & metric
├── scripts/
│   └── build-arm-template.ps1       # Regenerate ARM template from workbook
├── .github/
│   ├── workflows/
│   │   └── validate.yml             # CI: JSON validation & ARM sync check
│   ├── ISSUE_TEMPLATE/              # Bug report & feature request templates
│   └── PULL_REQUEST_TEMPLATE/       # PR checklist template
├── CHANGELOG.md
├── CODE_OF_CONDUCT.md
├── CONTRIBUTING.md
├── LICENSE
├── SECURITY.md
└── SUPPORT.md
```

## Roadmap

- [ ] Additional tabs: Token Usage, Latency, Content Safety / RAI
- [ ] Alert rule templates (ARM) for throttling and availability thresholds
- [ ] Contribution to [Azure AI Landing Zone Accelerator](https://github.com/Azure/azure-openai-landing-zone)

## Contributing

This project welcomes contributions and suggestions. Please see [CONTRIBUTING.md](CONTRIBUTING.md) for details.

> **Maintainers:** After creating the repo, [configure branch protection](CONTRIBUTING.md#repository-setup-maintainers) on `main` to require CI checks before merging.

## License

[MIT](LICENSE)
