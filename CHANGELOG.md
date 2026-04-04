# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

## [1.0.0] - 2026-04-04

### Added

- **Overview Tab** — Request Volume by API Type pie chart (Azure OpenAI vs Models API)
- **Resources by Region** — Interactive map using Azure Resource Graph
- **Azure OpenAI Requests by Status Code** — Line chart via Azure Monitor Metrics, split by `StatusCode`
- **Models API Requests by Status Code** — Line chart via Azure Monitor Metrics, split by `StatusCode`
- **Model Requests by Model Name** — Bar chart via Azure Monitor Metrics, split by `ModelName`
- **Resource Inventory Table** — Cognitive Services resources with Name, Resource Group, Kind, SKU, Location, Public Access, Provisioning State, and Diagnostic Settings status (ARG join)
- **Parameters** — Subscription, Resource Group, Cognitive Services Resource, Log Analytics Workspace, Time Range — all defaulting to "All"
- ARM template (`azuredeploy.json`) for one-click Deploy to Azure
- GitHub Actions CI for JSON validation and ARM/workbook sync check
