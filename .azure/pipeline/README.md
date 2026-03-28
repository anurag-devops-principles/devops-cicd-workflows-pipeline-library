# Azure DevOps Pipeline Templates

This directory contains reusable Azure DevOps pipeline templates for CI/CD workflows, specifically designed for a Todo application with React frontend and Python backend components.

## Overview

These pipeline templates provide comprehensive DevOps capabilities including:
- **Static Code Analysis** - Code quality and security scanning
- **Security Scanning** - Vulnerability detection in dependencies
- **Code Quality Checks** - Linting and formatting validation
- **Build & Packaging** - Application building and artifact publishing

## Pipeline Templates

### 🔍 Static Code Analysis

#### `sonarqube.yaml`
**Purpose**: Performs comprehensive static code analysis using SonarQube

**Parameters**:
- `sonarHost` (string): SonarQube server URL
- `sonarToken` (string): Authentication token for SonarQube
- `sonarProject` (string): Project key in SonarQube
- `workingDirectory` (string): Directory to scan
- `jobName` (string): Display name for the pipeline job

**Features**:
- Runs SonarQube scanner with quality gate validation
- Supports any programming language supported by SonarQube
- Blocks pipeline if quality gate fails

### 🔒 Security Scanning

#### `todo-be-bandit.yaml`
**Purpose**: Security scanning for Python backend code using Bandit

**Parameters**:
- `jobName` (string): Display name for the pipeline job
- `workingDirectory` (string): Directory containing Python code

**Features**:
- Creates isolated Python virtual environment
- Installs and runs Bandit security scanner
- Scans `app.py` for common security vulnerabilities
- Reports high, medium, and low severity issues

#### `todo-fe-retirejs.yaml`
**Purpose**: Dependency vulnerability scanning for JavaScript/Node.js applications

**Parameters**:
- `jobName` (string): Display name for the pipeline job
- `workingDirectory` (string): Directory containing package.json

**Features**:
- Installs project dependencies with `npm ci`
- Runs RetireJS to scan for known vulnerable JavaScript libraries
- Continues on error to prevent blocking builds (configurable)

### 🧹 Code Quality Checks

#### `todo-be-linting.yaml`
**Purpose**: Code quality validation for backend Python code and documentation

**Parameters**:
- `jobName` (string): Display name for the pipeline job
- `workingDirectory` (string): Directory to lint

**Features**:
- **Markdown Linting**: Uses markdownlint-cli for README and documentation files
- **Python Linting**: Uses Ruff for fast Python code analysis
- Continues on error to allow partial quality gate failures

#### `todo-fe-linting.yaml`
**Purpose**: Comprehensive code quality validation for React frontend applications

**Parameters**:
- `jobName` (string): Display name for the pipeline job
- `workingDirectory` (string): Directory containing the React application

**Features**:
- **JavaScript Linting**: Uses Biome for fast, modern JS/TS/React linting
- **CSS Linting**: Uses Stylelint with standard configuration for CSS files
- **Markdown Linting**: Uses markdownlint-cli for documentation
- Installs dependencies with `npm ci` for accurate linting
- Continues on error for non-blocking quality checks

### 🏗️ Build & Packaging

#### `todo-fe-build.yaml`
**Purpose**: Build and package React frontend applications for deployment

**Parameters**:
- `jobName` (string): Display name for the pipeline job
- `workingDirectory` (string): Directory containing the React application

**Features**:
- Uses Node.js 16.x environment
- Installs dependencies with npm
- Runs production build (`npm run build`)
- Publishes build artifacts to Azure DevOps pipeline artifacts
- Artifact name: `todo-ui-build`

## Usage Examples

### Using in Azure DevOps Pipeline

```yaml
# azure-pipelines.yml
stages:
  - template: .azure/pipeline/todo-fe-linting.yaml
    parameters:
      jobName: 'Frontend Linting'
      workingDirectory: 'frontend'

  - template: .azure/pipeline/todo-fe-build.yaml
    parameters:
      jobName: 'Frontend Build'
      workingDirectory: 'frontend'

  - template: .azure/pipeline/todo-be-linting.yaml
    parameters:
      jobName: 'Backend Linting'
      workingDirectory: 'backend'

  - template: .azure/pipeline/todo-be-bandit.yaml
    parameters:
      jobName: 'Backend Security Scan'
      workingDirectory: 'backend'

  - template: .azure/pipeline/sonarqube.yaml
    parameters:
      sonarHost: 'https://sonarcloud.io'
      sonarToken: $(sonarToken)
      sonarProject: 'my-project'
      workingDirectory: '.'
      jobName: 'SonarQube Analysis'
```

## Resources

To use these pipeline templates in your Azure DevOps pipelines, add this repository as a resource:

```yaml
resources:
  repositories:
    - repository: githubTemplate
      type: github
      endpoint: anurag-devops-principles
      name: anurag-devops-principles/devops-cicd-workflows-pipeline-library
      ref: refs/heads/main
```

## Prerequisites

### For Frontend Pipelines
- Node.js environment (version specified in each template)
- `package.json` with required dependencies and scripts
- Proper project structure with `src/` directory

### For Backend Pipelines
- Python 3.x environment
- `app.py` file in the working directory
- Proper Python project structure

### For SonarQube Pipeline
- SonarQube server or cloud instance
- Valid authentication token with project access
- SonarQube project key configured

## Tool Versions

- **Node.js**: 16.x (build), 20.x (linting/security)
- **Python**: 3.x (latest available in pipeline agent)
- **Bandit**: Latest version installed via pip
- **Ruff**: Latest version installed via pip
- **Biome**: Latest version installed via npm
- **Stylelint**: Latest with standard config
- **Markdownlint**: Latest CLI version
- **RetireJS**: Latest version installed via npm

## Error Handling

Most quality check templates use `continueOnError: true` to prevent pipeline failures while still providing feedback. This allows teams to:

- Gradually improve code quality
- Not block deployments for minor issues
- Review and address issues in subsequent iterations

## Customization

These templates are designed to be:
- **Modular**: Each template focuses on a specific concern
- **Parameterized**: Easy to customize for different projects
- **Reusable**: Can be used across multiple repositories
- **Maintainable**: Clear separation of concerns

## Contributing

When adding new pipeline templates:
1. Follow the existing parameter naming conventions
2. Include comprehensive documentation
3. Use `continueOnError` appropriately for quality checks
4. Test templates in a CI environment before committing

## Security Notes

- Store sensitive tokens (like SonarQube tokens) as Azure DevOps pipeline variables
- Use secret variables for authentication credentials
- Regularly update tool versions for latest security patches
- Review pipeline permissions and access controls
