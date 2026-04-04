# Contributing to Azure AI Operations Hub

This project welcomes contributions and suggestions. Most contributions require you to agree to a Contributor License Agreement (CLA) declaring that you have the right to, and actually do, grant us the rights to use your contribution. For details, visit https://cla.opensource.microsoft.com.

This project has adopted the [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/). For more information see the [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) or contact [opencode@microsoft.com](mailto:opencode@microsoft.com) with any additional questions or comments.

## How to Contribute

### Reporting Issues

- Search [existing issues](https://github.com/krishna-sunkavalli/azure-ai-operations-hub/issues) first
- Use the appropriate issue template (bug report or feature request)
- Include as much detail as possible

### Submitting Changes

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/my-change`)
3. Make your changes
4. Validate the workbook JSON is valid:
   ```bash
   python -c "import json; json.load(open('workbook/azure-ai-monitor.workbook'))"
   ```
5. If you modified the workbook, regenerate `azuredeploy.json`:
   ```powershell
   .\scripts\build-arm-template.ps1
   ```
6. Commit your changes (`git commit -m 'Add new check for ...'`)
7. Push to your fork (`git push origin feature/my-change`)
8. Open a Pull Request

### Adding New Charts or Queries

To add a new visualization to the workbook:

1. Edit the workbook in **Azure Portal** → Monitor → Workbooks (edit mode)
2. Export the updated workbook JSON via Advanced Editor (`</>`)
3. Replace the contents of `workbook/azure-ai-monitor.workbook` with the exported JSON
4. Regenerate the ARM template: `.\scripts\build-arm-template.ps1`
5. Update `docs/OVERVIEW.md` with the new section description

### Query Guidelines

- Prefer **MetricsItem** tiles for time-series metric data (Azure Monitor Metrics API)
- Use **KQL on `AzureMetrics`** only for non-dimension aggregations — `AzureMetrics` does not store per-dimension rows
- Use lowercase resource type names (e.g., `microsoft.cognitiveservices/accounts`)
- Include `subscriptionId` in ARG projections for multi-subscription support
- Add `noDataMessage` to all grid items

## Repository Setup (Maintainers)

After creating the repo on GitHub, configure the following **branch protection rules** on `main`:

1. **Settings → Branches → Add rule** for `main`
2. Enable:
   - ✅ Require a pull request before merging (1+ approving review)
   - ✅ Dismiss stale pull request approvals when new commits are pushed
   - ✅ Require status checks to pass before merging
     - Add: `Validate JSON & Structure`
   - ✅ Require conversation resolution before merging
   - ✅ Do not allow bypassing the above settings

## Development Setup

### Prerequisites

- PowerShell 7.0+
- Python 3.x (for JSON validation)
- An Azure subscription with **Reader** role on Cognitive Services resources

### Local Testing

```powershell
# Validate workbook JSON
python -c "import json; json.load(open('workbook/azure-ai-monitor.workbook')); print('OK')"

# Rebuild ARM template
.\scripts\build-arm-template.ps1

# Validate ARM template
python -c "import json; json.load(open('workbook/azuredeploy.json')); print('OK')"
```
