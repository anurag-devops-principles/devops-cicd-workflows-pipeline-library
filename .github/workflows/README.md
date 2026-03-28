# GitHub Actions Workflow Library

This directory contains reusable GitHub Actions workflows designed for comprehensive DevOps CI/CD pipelines. These workflows are built as reusable components that can be called from other workflows using the `workflow_call` trigger.

## Overview

The workflow library provides modular, parameterized workflows for:
- **Code Quality & Security** - Linting, static analysis, and security scanning
- **Infrastructure as Code** - Terraform planning, applying, and security checks
- **Application Deployment** - Build, package, and deploy applications
- **Cost Management** - Infrastructure cost estimation and analysis

## Workflows

### 🔍 Code Quality & Analysis

#### `code_quality.yaml`
**Purpose**: Comprehensive code quality checks for JavaScript/TypeScript projects

**Inputs**:
- `job_name` (string): Display name for the job
- `runs_on` (string): Runner environment (default: ubuntu-latest)
- `node_version` (string): Node.js version to use
- `cache` (string): Cache type for dependencies
- `working_directory` (string): Directory containing the code to analyze

**Features**:
- JavaScript/TypeScript linting with Biome
- Super-linter for comprehensive code quality checks
- Continues on error to prevent blocking builds

#### `sonarqube.yaml`
**Purpose**: Static code analysis using SonarQube

**Inputs**:
- `job_name` (string): Display name for the job
- `runs_on` (string): Runner environment (default: ubuntu-latest)
- `SONAR_HOST_URL` (string): SonarQube server URL
- `SONAR_TOKEN` (string): Authentication token for SonarQube
- `PROJECT_KEY` (string): SonarQube project key
- `working_directory` (string): Directory to scan

**Features**:
- SonarQube scanning with quality gate validation
- Supports all languages compatible with SonarQube
- Blocks pipeline if quality gate fails

### 🔒 Security Scanning

#### `retirejs.yaml`
**Purpose**: Dependency vulnerability scanning for JavaScript/Node.js applications

**Inputs**:
- `job_name` (string): Display name for the job
- `runs_on` (string): Runner environment (default: ubuntu-latest)
- `node_version` (string): Node.js version to use
- `cache` (string): Cache type for dependencies
- `working_directory` (string): Directory containing package.json

**Features**:
- Scans for known vulnerable JavaScript libraries
- Uses RetireJS for comprehensive dependency analysis
- Continues on error to allow partial security gate failures

#### `terraform_security.yaml`
**Purpose**: Security and compliance checks for Terraform infrastructure code

**Inputs**:
- `job_name` (string): Display name for the job
- `runs_on` (string): Runner environment (default: ubuntu-latest)
- `working_directory` (string): Directory containing Terraform files

**Features**:
- **TFLint**: Terraform linting and best practices validation
- **TFSec**: Security scanning for Terraform configurations
- **Checkov**: Comprehensive infrastructure as code security analysis
- Soft fail mode to prevent blocking deployments

### 🏗️ Build & Packaging

#### `npm_build.yaml`
**Purpose**: Build and package Node.js applications with artifact generation

**Inputs**:
- `job_name` (string): Display name for the job
- `runs_on` (string): Runner environment (default: ubuntu-latest)
- `node_version` (string): Node.js version to use
- `cache` (string): Cache type for dependencies
- `artifacts_name` (string): Name for the generated artifact
- `artifacts_path` (string): Path where artifacts will be stored
- `working_directory` (string): Directory containing the application

**Features**:
- Dependency installation with `npm ci`
- Production build execution
- Artifact packaging and upload to GitHub Actions

### 🚀 Deployment

#### `deploy_build.yaml`
**Purpose**: Deploy built applications to Azure Web Apps

**Inputs**:
- `job_name` (string): Display name for the job
- `runs_on` (string): Runner environment (default: ubuntu-latest)
- `artifacts_name` (string): Name of the artifact to download
- `artifacts_path` (string): Local path to store downloaded artifacts
- `web_app_name` (string): Azure Web App name for deployment

**Secrets**:
- `AZURE_WEBAPP_PUBLISH_PROFILE`: Azure Web App publish profile

**Features**:
- Downloads build artifacts from previous jobs
- Deploys to Azure Web Apps using publish profile authentication
- Verifies deployment package before upload

### ☁️ Infrastructure as Code (Terraform)

#### `terraform_plan.yaml`
**Purpose**: Plan Terraform infrastructure changes

**Inputs**:
- `job_name` (string): Display name for the job
- `runs_on` (string): Runner environment (default: ubuntu-latest)
- `working_directory` (string): Directory containing Terraform files

**Secrets**:
- `AZURE_CLIENT_ID`: Azure service principal client ID
- `AZURE_TENANT_ID`: Azure tenant ID
- `AZURE_SUBSCRIPTION_ID`: Azure subscription ID
- `AZURE_CLIENT_SECRET`: Azure service principal secret

**Features**:
- Azure CLI authentication with service principal
- Terraform initialization and validation
- Infrastructure planning with detailed output

#### `terraform_apply.yaml`
**Purpose**: Apply Terraform infrastructure changes

**Inputs**: Same as `terraform_plan.yaml`

**Secrets**: Same as `terraform_plan.yaml`

**Features**:
- Applies planned infrastructure changes
- Uses same authentication as plan workflow
- Terraform version 1.13.2 (configurable)

#### `terraform_destroy.yaml`
**Purpose**: Destroy Terraform-managed infrastructure

**Inputs**: Same as `terraform_plan.yaml`

**Secrets**: Same as `terraform_plan.yaml`

**Features**:
- Destroys all resources managed by Terraform
- Useful for cleanup and development environments
- Same authentication pattern as other Terraform workflows

#### `cost_estimation.yaml`
**Purpose**: Estimate infrastructure costs using Infracost

**Inputs**:
- `job_name` (string): Display name for the job
- `runs_on` (string): Runner environment (default: ubuntu-latest)
- `working_directory` (string): Directory containing Terraform files

**Secrets**:
- `AZURE_CLIENT_ID`: Azure service principal client ID
- `AZURE_TENANT_ID`: Azure tenant ID
- `AZURE_SUBSCRIPTION_ID`: Azure subscription ID
- `INFRACOST_API_KEY`: Infracost API key for cost estimation

**Features**:
- Generates cost estimates for Terraform plans
- Integrates with Infracost for cloud cost analysis
- Shows cost breakdown by resource type

## Usage Examples

### Complete CI/CD Pipeline

```yaml
# .github/workflows/main.yml
name: CI/CD Pipeline

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  # Code Quality Checks
  code-quality:
    uses: ./.github/workflows/code_quality.yaml
    with:
      job_name: 'Frontend Code Quality'
      node_version: '20'
      cache: 'npm'
      working_directory: './frontend'

  # Security Scanning
  security-scan:
    uses: ./.github/workflows/retirejs.yaml
    with:
      job_name: 'Dependency Security Scan'
      node_version: '20'
      cache: 'npm'
      working_directory: './frontend'

  # Build Application
  build:
    needs: [code-quality, security-scan]
    uses: ./.github/workflows/npm_build.yaml
    with:
      job_name: 'Build Application'
      node_version: '20'
      cache: 'npm'
      artifacts_name: 'frontend-build'
      artifacts_path: './dist'
      working_directory: './frontend'

  # Infrastructure Planning
  terraform-plan:
    uses: ./.github/workflows/terraform_plan.yaml
    with:
      job_name: 'Infrastructure Plan'
      working_directory: './terraform'
    secrets:
      AZURE_CLIENT_ID: ${{ secrets.AZURE_CLIENT_ID }}
      AZURE_TENANT_ID: ${{ secrets.AZURE_TENANT_ID }}
      AZURE_SUBSCRIPTION_ID: ${{ secrets.AZURE_SUBSCRIPTION_ID }}
      AZURE_CLIENT_SECRET: ${{ secrets.AZURE_CLIENT_SECRET }}

  # Cost Estimation
  cost-estimate:
    needs: [terraform-plan]
    uses: ./.github/workflows/cost_estimation.yaml
    with:
      job_name: 'Cost Analysis'
      working_directory: './terraform'
    secrets:
      AZURE_CLIENT_ID: ${{ secrets.AZURE_CLIENT_ID }}
      AZURE_TENANT_ID: ${{ secrets.AZURE_TENANT_ID }}
      AZURE_SUBSCRIPTION_ID: ${{ secrets.AZURE_SUBSCRIPTION_ID }}
      INFRACOST_API_KEY: ${{ secrets.INFRACOST_API_KEY }}

  # Deploy (only on main branch)
  deploy:
    if: github.ref == 'refs/heads/main'
    needs: [build, terraform-plan, cost-estimate]
    uses: ./.github/workflows/deploy_build.yaml
    with:
      job_name: 'Deploy to Production'
      artifacts_name: 'frontend-build'
      artifacts_path: './dist'
      web_app_name: 'my-web-app'
    secrets:
      AZURE_WEBAPP_PUBLISH_PROFILE: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
```

## Prerequisites

### For JavaScript/Node.js Workflows
- Valid `package.json` in the working directory
- Node.js version compatible with your application
- Proper project structure (src/, dist/, etc.)

### For Terraform Workflows
- Valid Terraform configuration files
- Azure service principal with appropriate permissions
- Remote state configuration (recommended)

### For Azure Deployment Workflows
- Azure Web App created and configured
- Publish profile available as repository secret
- Appropriate Azure RBAC permissions

### For SonarQube Workflows
- SonarQube server or cloud instance
- Project created in SonarQube
- Authentication token with project access

## Security Considerations

- Store all secrets as GitHub repository secrets
- Use service principals for Azure authentication (not user accounts)
- Regularly rotate authentication tokens and secrets
- Review workflow permissions and access controls

## Tool Versions

- **Node.js**: Configurable (16, 18, 20 supported)
- **Terraform**: Latest (workflows) or 1.13.2 (legacy apply/destroy)
- **Biome**: Latest version
- **TFLint**: Latest version
- **TFSec**: Latest version
- **Checkov**: Latest version
- **RetireJS**: Latest version via npm
- **Infracost**: Latest version

## Contributing

When adding new workflows:
1. Use `workflow_call` trigger for reusability
2. Include comprehensive input validation
3. Document all inputs, secrets, and features
4. Use `continue-on-error` appropriately for quality checks
5. Test workflows in a CI environment before committing

## Error Handling

Most quality and security check workflows use `continue-on-error: true` or soft fail modes to:
- Allow gradual improvement of code quality
- Prevent blocking deployments for minor issues
- Enable teams to review and address issues iteratively

## Support

For issues or questions about these workflows:
1. Check the workflow logs for detailed error messages
2. Verify all required inputs and secrets are provided
3. Ensure your project structure matches workflow expectations
4. Review Azure/Terraform permissions if applicable
