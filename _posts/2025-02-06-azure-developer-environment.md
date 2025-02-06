---
title: "Azure Developer Environment"
date: 2025-02-06
categories: azure
---

# Official Doc and Portal
[Azure Depolyment Environment](https://learn.microsoft.com/en-us/azure/deployment-environments/)

[Dev Portal](https://devportal.microsoft.com/)

# Use Case

##TODO

# Limits
1.  The resource group name follows the pattern {projectName}-           {environmentName} 

    [reference](https://learn.microsoft.com/en-us/azure/deployment-environments/quickstart-create-access-environments?tabs=no-existing-environments#:~:text=The%20resource%20group%20name%20follows%20the%20pattern%20%7BprojectName%7D%2D%7BenvironmentName%7D)

    ##TODO: create resource group in IaC templates with your name.????

2. The 30 minutes interval cannot be changed in automatic sync of environment defination (yaml file) 

    [reference](https://learn.microsoft.com/en-us/azure/deployment-environments/best-practice-catalog-structure#update--environment-definitions-and-sync-changes)

    ADE actually syncs and cach all files in the yaml's folder and its sub folders. This means, if platform engineer changed yaml or templates (bicep, TF) in repo, you must either manually sync, or wait 30 minutes.

    ##TODO: automatically run a sync Azure CLI command with Github actions?

3.  No way to enforce a lifespane? Tag?

4.  Scheduled deployment?

5.  Terraform needs customized container 
    [reference](https://learn.microsoft.com/en-us/azure/deployment-environments/configure-environment-definition#:~:text=ADE%20supports%20custom%20container%20images%20for%20environment%20deployments%2C%20which%20can%20help%20deploy%20IaC%20frameworks%20such%20as%20Pulumi%20and%20Terraform.)

    ![image](/assets/images/2025-02-06-azure-developer-environment/tf_custom_container.png) 


