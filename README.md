# DevOps CI/CD Workflows & Pipeline Library

A comprehensive collection of reusable CI/CD workflows and pipeline templates for modern DevOps practices. This library provides standardized, modular components for GitHub Actions and Azure DevOps Pipelines.

## Overview

This repository serves as a centralized library for DevOps automation, containing:

- **GitHub Actions Workflows** - Reusable workflows for GitHub-based CI/CD
- **Azure DevOps Pipeline Templates** - YAML templates for Azure DevOps pipelines
- **Multi-Platform Support** - Consistent tooling across different CI/CD platforms
- **Modular Architecture** - Parameterized, reusable components

## Repository Structure

```
devops-cicd-workflows-pipeline-library/
├── .github/workflows/           # GitHub Actions reusable workflows
│   ├── README.md               # GitHub workflows documentation
│   ├── code_quality.yaml       # Code quality & linting
│   ├── sonarqube.yaml          # Static code analysis
│   ├── retirejs.yaml           # Dependency security scanning
│   ├── npm_build.yaml          # Node.js application build
│   ├── deploy_build.yaml       # Azure Web App deployment
│   ├── terraform_plan.yaml     # Infrastructure planning
│   ├── terraform_apply.yaml    # Infrastructure deployment
│   ├── terraform_destroy.yaml  # Infrastructure cleanup
│   ├── terraform_security.yaml # IaC security checks
│   └── cost_estimation.yaml    # Cost analysis
├── .azure/pipeline/            # Azure DevOps pipeline templates
│   ├── README.md              # Azure pipeline documentation
│   ├── sonarqube.yaml         # SonarQube analysis template
│   ├── todo-be-bandit.yaml    # Python security scanning
│   ├── todo-be-linting.yaml   # Backend code quality
│   ├── todo-fe-build.yaml     # Frontend build template
│   ├── todo-fe-linting.yaml   # Frontend code quality
│   └── todo-fe-retirejs.yaml  # JavaScript dependency scanning
├── LICENSE                    # MIT License
└── README.md                  # This file
```

## Platform Support

### GitHub Actions Workflows

Reusable workflows designed for GitHub's native CI/CD platform:

#### 🔍 Code Quality & Analysis
- **Biome Linting** - Fast JavaScript/TypeScript linting
- **SonarQube Analysis** - Comprehensive static code analysis
- **Super Linter** - Multi-language code quality checks

#### 🔒 Security Scanning
- **RetireJS** - JavaScript dependency vulnerability scanning
- **Terraform Security** - Infrastructure as Code security validation

#### 🏗️ Build & Deployment
- **NPM Build** - Node.js application building and packaging
- **Azure Web App Deploy** - Automated deployment to Azure Web Apps

#### ☁️ Infrastructure as Code
- **Terraform Plan** - Infrastructure change planning
- **Terraform Apply** - Infrastructure deployment
- **Terraform Destroy** - Infrastructure cleanup
- **Cost Estimation** - Infrastructure cost analysis with Infracost

### Azure DevOps Pipeline Templates

YAML templates optimized for Azure DevOps Pipelines:

#### 🔍 Static Analysis
- **SonarQube Integration** - Multi-language code analysis
- **Bandit Security** - Python security vulnerability scanning

#### 🧹 Code Quality
- **Backend Linting** - Python and Markdown linting
- **Frontend Linting** - JavaScript, CSS, and documentation quality

#### 🔒 Security Scanning
- **Dependency Analysis** - JavaScript library vulnerability checks

#### 🏗️ Build & Packaging
- **Frontend Build** - React application build and artifact creation

## Key Features

### 🔄 Reusability
- **Modular Components** - Each workflow/template focuses on a specific concern
- **Parameterized** - Easy customization for different projects
- **Consistent** - Standardized approaches across platforms

### 🔒 Security First
- **Automated Scanning** - Integrated security checks in every pipeline
- **Dependency Analysis** - Vulnerability detection in third-party libraries
- **Infrastructure Security** - IaC security validation with multiple tools

### 📊 Quality Assurance
- **Code Linting** - Automated code quality and style enforcement
- **Static Analysis** - Deep code inspection with SonarQube
- **Multi-Language Support** - JavaScript, Python, Terraform, and more

### ☁️ Cloud-Native
- **Azure Integration** - Native support for Azure services
- **Cost Awareness** - Infrastructure cost estimation and monitoring
- **Scalable Architecture** - Designed for cloud-native applications

## Quick Start

### Using GitHub Actions Workflows

1. **Reference the workflow** in your `.github/workflows/main.yml`:

```yaml
name: CI/CD Pipeline

on:
  push:
    branches: [ main ]

jobs:
  code-quality:
    uses: anurag-devops-principles/devops-cicd-workflows-pipeline-library/.github/workflows/code_quality.yaml@main
    with:
      job_name: 'Code Quality Check'
      node_version: '20'
      working_directory: './src'

  security-scan:
    uses: anurag-devops-principles/devops-cicd-workflows-pipeline-library/.github/workflows/retirejs.yaml@main
    with:
      job_name: 'Security Scan'
      node_version: '20'
      working_directory: './src'
```

### Using Azure DevOps Pipeline Templates

1. **Set up resource reference** in `azure-pipelines.yml`:

```yaml
resources:
  repositories:
    - repository: pipelineTemplates
      type: github
      endpoint: your-github-service-connection
      name: anurag-devops-principles/devops-cicd-workflows-pipeline-library
      ref: refs/heads/main

stages:
  - template: .azure/pipeline/todo-fe-linting.yaml@pipelineTemplates
    parameters:
      jobName: 'Frontend Linting'
      workingDirectory: './frontend'
```

## Prerequisites

### For GitHub Actions
- **GitHub Repository** with Actions enabled
- **Appropriate Secrets** configured (Azure credentials, SonarQube tokens, etc.)
- **Required Permissions** for repository and external services

### For Azure DevOps
- **Azure DevOps Project** with pipeline permissions
- **GitHub Service Connection** for template repository access
- **Azure Service Connections** for deployments

### General Requirements
- **Node.js** projects require `package.json`
- **Python** projects require appropriate file structure
- **Terraform** requires valid `.tf` files and Azure authentication
- **SonarQube** requires server access and project configuration

## Configuration Examples

### Complete CI/CD Pipeline (GitHub Actions)

```yaml
name: Full CI/CD Pipeline

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  # Code Quality
  quality:
    uses: ./.github/workflows/code_quality.yaml
    with:
      job_name: 'Code Quality'
      node_version: '20'
      working_directory: './frontend'

  # Security
  security:
    uses: ./.github/workflows/retirejs.yaml
    with:
      job_name: 'Dependency Security'
      node_version: '20'
      working_directory: './frontend'

  # Build
  build:
    uses: ./.github/workflows/npm_build.yaml
    needs: [quality, security]
    with:
      job_name: 'Build Application'
      node_version: '20'
      artifacts_name: 'app-build'
      working_directory: './frontend'

  # Infrastructure
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

  # Deploy
  deploy:
    uses: ./.github/workflows/deploy_build.yaml
    needs: [build, terraform-plan]
    with:
      job_name: 'Deploy to Azure'
      artifacts_name: 'app-build'
      web_app_name: 'my-web-app'
    secrets:
      AZURE_WEBAPP_PUBLISH_PROFILE: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
```

### Complete CI/CD Pipeline (Azure DevOps)

```yaml
# azure-pipelines.yml
resources:
  repositories:
    - repository: pipelineTemplates
      type: github
      endpoint: github-service-connection
      name: anurag-devops-principles/devops-cicd-workflows-pipeline-library
      ref: refs/heads/main

stages:
  - stage: Quality
    jobs:
      - template: .azure/pipeline/todo-fe-linting.yaml@pipelineTemplates
        parameters:
          jobName: 'Frontend Linting'
          workingDirectory: './frontend'

      - template: .azure/pipeline/todo-fe-retirejs.yaml@pipelineTemplates
        parameters:
          jobName: 'Security Scan'
          workingDirectory: './frontend'

  - stage: Build
    jobs:
      - template: .azure/pipeline/todo-fe-build.yaml@pipelineTemplates
        parameters:
          jobName: 'Build Application'
          workingDirectory: './frontend'

  - stage: Deploy
    jobs:
      - template: .azure/pipeline/deploy-to-azure.yaml@pipelineTemplates
        parameters:
          webAppName: 'my-web-app'
          artifactsName: 'frontend-build'
```

## Tool Versions & Compatibility

### GitHub Actions Environment
- **Ubuntu**: `ubuntu-latest` (primary)
- **Node.js**: 16.x, 18.x, 20.x
- **Python**: 3.8+ (available in runners)
- **Terraform**: Latest (configurable)

### Azure DevOps Environment
- **Ubuntu**: `ubuntu-latest`
- **Node.js**: 16.x, 18.x, 20.x
- **Python**: 3.x (available in agents)
- **Terraform**: Configurable versions

## Security Considerations

### Secrets Management
- Store all sensitive data as repository/platform secrets
- Use service principals for Azure authentication
- Rotate tokens and credentials regularly
- Never commit secrets to version control

### Pipeline Security
- Implement branch protection rules
- Require code reviews for pipeline changes
- Use least-privilege service accounts
- Enable security scanning in all pipelines

### Infrastructure Security
- Use Terraform security tools (TFSec, Checkov)
- Implement proper Azure RBAC
- Enable Azure Security Center
- Regular security assessments

## Contributing

We welcome contributions to improve and extend this library!

### Adding New Workflows/Templates

1. **Choose Platform** - GitHub Actions or Azure DevOps
2. **Follow Patterns** - Use existing templates as reference
3. **Comprehensive Documentation** - Update READMEs with usage examples
4. **Testing** - Test in multiple scenarios
5. **Security Review** - Ensure security best practices

### Guidelines

- **Modular Design** - Single responsibility per workflow/template
- **Parameterization** - Make components reusable and configurable
- **Documentation** - Comprehensive inline and README documentation
- **Error Handling** - Graceful failure handling and clear error messages
- **Versioning** - Use semantic versioning for releases

### Development Workflow

1. Fork the repository
2. Create a feature branch
3. Add your workflow/template
4. Update documentation
5. Test thoroughly
6. Submit a pull request

## Support & Resources

### Documentation
- [GitHub Actions Workflows](./.github/workflows/README.md)
- [Azure DevOps Templates](./.azure/pipeline/README.md)

### Related Projects
- [Azure WebApp IaC Todo Project](https://github.com/anurag-devops-principles/azure-webapp-iac-todo-fullstack-project)
- [Azure Custom Image Packer](https://github.com/anurag-devops-principles/azure-custom-image-packer)
- [Bootstrap Azure Resources](https://github.com/anurag-devops-principles/bootstrap-azure-resources-terraform)

### Getting Help
- 📖 **Documentation** - Check individual workflow/template READMEs
- 🐛 **Issues** - Report bugs and request features
- 💬 **Discussions** - Ask questions and share ideas
- 📧 **Contact** - Reach out to the DevOps team

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Acknowledgments

- **GitHub Actions** - For providing the reusable workflow platform
- **Azure DevOps** - For the robust pipeline capabilities
- **Open Source Community** - For the amazing tools and libraries used

---

**Maintained by**: Anurag Vijay
**Version**: 1.0.0
**Last Updated**: March 2026
