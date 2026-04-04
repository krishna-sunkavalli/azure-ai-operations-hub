## Description

<!-- Describe what this PR changes and why -->

## Type of change

- [ ] Bug fix
- [ ] New chart / visualization
- [ ] New tab or section
- [ ] Documentation update
- [ ] Other (describe):

## Checklist

- [ ] Workbook JSON is valid: `python -c "import json; json.load(open('workbook/azure-ai-monitor.workbook'))"`
- [ ] ARM template regenerated: `.\scripts\build-arm-template.ps1`
- [ ] ARM template is valid JSON: `python -c "import json; json.load(open('workbook/azuredeploy.json'))"`
- [ ] Tested in Azure Portal with at least one real Cognitive Services resource
- [ ] `docs/OVERVIEW.md` updated if a new section was added
- [ ] `CHANGELOG.md` entry added

## Screenshots

<!-- Before / after screenshots of the workbook are appreciated -->
