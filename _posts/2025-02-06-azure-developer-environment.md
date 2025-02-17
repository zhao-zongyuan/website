---
title: "Azure Developer Environment"
date: 2025-02-06
categories: azure
---

# Official Doc and Portal
[Azure Depolyment Environment](https://learn.microsoft.com/en-us/azure/deployment-environments/)

[Dev Portal](https://devportal.microsoft.com/)

# Use Case

1.  Azure engineers who needs to do a PoC / demo / test in a shared subscription
    
    Azure consultants can safely create a sandbox resource group with owner permission, and create other resources in it temporarily. 


# Pros

1.  Cost Saving: All deployed resources can have a schedule to destory for cost saving, especially for POC resources.

2.  Security: The Service Principle for CI/CD (the App Service in Entra ID for OIDC Github Action) only requires "Reader" on project and "Deployment Environments User" on environment type in ADE catalog. Without ADE, it normally needs Contributor role on subscription.

    This might be even more secure if the deployment needs to assign other roles, e.g. create Managed Identity in IaC. The SP for OIDC does not need permission in Entra.

    It is worth noticing that the ADE Dev Center still needs to enable its System Managed Identity (or create a user defined identity) and assign roles (owner or contributor) on subscription level. However, System Managed Identity is normally considered to be more secure than App Registry for its limited use scope and self destory. 

3.  Forced Tagging: All deployment can be added with a tag automatically from environment settings.

4.  Least Privilige & Highest Privilige: Dev user can have owner permission on the sandbox resource group, but will be restricted to that resource group only. By test, the infrastucture defination cannot add new resource group (bicep scope is locked on resource group)

# Limits
1.  The resource group name follows the pattern {projectName}-           {environmentName} 

    [reference](https://learn.microsoft.com/en-us/azure/deployment-environments/quickstart-create-access-environments?tabs=no-existing-environments#:~:text=The%20resource%20group%20name%20follows%20the%20pattern%20%7BprojectName%7D%2D%7BenvironmentName%7D)

2. The 30 minutes interval cannot be changed in automatic sync of environment defination (yaml file) 

    [reference](https://learn.microsoft.com/en-us/azure/deployment-environments/best-practice-catalog-structure#update--environment-definitions-and-sync-changes)

    ADE actually syncs and cach all files in the yaml's folder and its sub folders. This means, if platform engineer changed yaml or templates (bicep, TF) in repo, you must either manually sync, or wait 30 minutes.

    Solution: run a sync Azure CLI command with Github action.

3.  No way to enforce a lifespane by administrator. But this can be done by adding a time with creation date and automation jobs.

4.  After an environment is created, if the infrastructure definiation is changed, the environment cannot be deleted from developer portal.


5.  Terraform needs customized container 
    [reference](https://learn.microsoft.com/en-us/azure/deployment-environments/configure-environment-definition#:~:text=ADE%20supports%20custom%20container%20images%20for%20environment%20deployments%2C%20which%20can%20help%20deploy%20IaC%20frameworks%20such%20as%20Pulumi%20and%20Terraform.)

    ![image](../../../../assets/tf_custom_container.jpg) 


# Terraform

ADE only supports standard runner for bicep and ARM. To use Terraform in infrastructure defination, you must configure a self defined runner as described in [reference](https://learn.microsoft.com/en-us/azure/deployment-environments/how-to-configure-extensibility-model-custom-image?tabs=custom-script%2Cterraform-script%2Cprivate-registry&pivots=terraform)

Following the above reference is difficult. Here is the full story:

## Background

Follow [this document](https://learn.microsoft.com/en-us/azure/deployment-environments/how-to-configure-extensibility-model-custom-image?tabs=custom-script%2Cgithub-workflow%2Cprivate-registry&pivots=terraform) 

Under [create a custom container image](https://learn.microsoft.com/en-us/azure/deployment-environments/how-to-configure-extensibility-model-custom-image?tabs=custom-script%2Cgithub-workflow%2Cprivate-registry&pivots=terraform#create-a-custom-container-image-1) , select ``using a Github workflow``

Under [Make the custom image available to ADE](https://learn.microsoft.com/en-us/azure/deployment-environments/how-to-configure-extensibility-model-custom-image?tabs=custom-script%2Cgithub-workflow%2Cprivate-registry&pivots=terraform#make-the-custom-image-available-to-ade), select ``private registry``

For PoC, use Azure Container Registry Basic tier to save cost (does not support network firewall and anonymous pull).

All above references can be ignored if you want to follow me.

## Azure
1.  Create a ``Container registry`` (ACR) resource with Basic tier to save cost.

2.  Assign the AcrPull role to the ADE project's environment.

    Go to the Access Control tab of the ACR resource, add role assignment. 

    |Setting|	Value|
    | -------- | ------- |
    |Role|	Select AcrPull.|
    |Assign access to|	Select User, group, or service principal.|
    |Members	|Enter the name of the project, e.g. ``terraform/environmentTypes/dev-env ``|

## Github
3.  Fork the [Microsoft ADE repo](https://github.com/Azure/ade-extensibility-model-terraform) 

4.  Setup OIDC for this repo. For details, please check my other post [Use OIDC in Github Actions with Azure](https://zhao-zongyuan.github.io/website/github/2025/02/217/github-azure-oidc.html)

5. Add 3 variables in the newly created environment

    |name|value|
    | -------- | ------- |
    |REGISTRY_NAME|name of the ACR, e.g. "johnzhaoade"|
    |REPOSITORY_NAME|name of the repository in ACR, e.g."ade-terraform"|
    |TAG|Add a tag for the image|

6. Add repo environment defination in the workflow.

    In the workflow file "build-and-push-image.yml", add 

    ```
    environment: prd
    ```

7.  Run the workflow.

    The workflow will push an image into the created ACR.

## Infrastructure Defination (ADE catalog repo)

8.  Use the ACR as runner.

    In ``environment.yaml``, change runner to 

    ```
    runner: "{YOUR_REGISTRY}.azurecr.io/{YOUR_REPOSITORY}:{YOUR_TAG}"
    ```

