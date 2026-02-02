# devops-cicd-actions-workflows-library
Github Actions Reusable Workflows

## License
MIT

### Example Usage for sonarqube.yaml

```
static_code_analysis:
  uses: anurag-vj/evops-cicd-actions-workflows-library/.github/workflows/sonarqube.yaml@main
  with:
    job_name: 'Static Code Analysis (Sonarqube)'
    runs_on: 'ubuntu-latest'
    SONAR_HOST_URL: http://74.225.207.162:9000
    SONAR_TOKEN: sqp_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
    PROJECT_KEY: frontend-ui-todo
    working_directory: ./frontend

static_code_analysis:
  uses: anurag-vj/evops-cicd-actions-workflows-library/.github/workflows/sonarqube.yaml@main
  with:
    job_name: 'Static Code Analysis (Sonarqube)'
    runs_on: 'ubuntu-latest'
    SONAR_HOST_URL: ${{ secrets.SONAR_HOST_URL }}
    SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }}
    PROJECT_KEY: frontend-ui-todo
    working_directory: ./frontend
```

### Example Usage for cost_estimation.yaml

```
  terraform_plan_and_cost_estimation:
    uses: anurag-vj/actions_reusable_workflows/.github/workflows/cost_estimation.yaml@main
    needs: terraform_security_checks
    secrets:
      INFRACOST_API_KEY: ${{ secrets.INFRACOST_API_KEY }}
      AZURE_CLIENT_ID: ${{ secrets.AZURE_CLIENT_ID }}
      AZURE_TENANT_ID: ${{ secrets.AZURE_TENANT_ID }}
      AZURE_SUBSCRIPTION_ID: ${{ secrets.AZURE_SUBSCRIPTION_ID }}
    with:
      job_name: 'Terraform Plan and Cost Estimation'
      runs_on: 'ubuntu-latest'
      working_directory: environment/${{ github.event_name == 'workflow_dispatch' && inputs.deploy_target || 'dev' }}
```