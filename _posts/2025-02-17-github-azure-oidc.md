---
title: "Use OIDC in Github Actions with Azure"
date: 2025-02-17
categories: github
---

Github documents refers to Azure, and Azure refers to Github, so both are shit. That makes me creating this post.

# Useless Docs

[Github](https://docs.github.com/en/actions/security-for-github-actions/security-hardening-your-deployments/configuring-openid-connect-in-azure)

[Azure](https://learn.microsoft.com/en-us/azure/developer/github/connect-from-azure-openid-connect)

# Concept

This method uses a service principle in Azure Entra ID representing a Github repo. Federated credential will be used for trusting tokens. 

# How To

## Azure 

1.  Create a service principle in Entra ID

    Microsoft Entra ID -> App Registrations -> New registation 

    Give a name e.g. "github-action-admin" and leave other settings as default. (The name is only a description but its generated client ID is global unique)

2.  Assgin RBAC role to your subscription / resource group.

    It is common to assgin Contributor role to the whole subscription when doing platform engineering or IaC works. 

3.  Created federated credential
    
    Go to the newly created App Registration -> Cetificates & Secrets -> Federated credentials -> add credential

    | Config    | Value |
    | -------- | ------- |
    | Federated credential | scenario GitHub Actions deploying Azure resources |
    |Issuer | default|
    |Organization | < your Github org / personal account name > |
    |Repository | < your Github repo name > |
    | Entity type | Environment |
    | Based on selection | < Environment name in your repo. E.g. "prd" > |
    | Subject identifier | default |

    Credential details: any name you like. Normally use the repo name.

## Github

4.  Create an environment in repo (repo needs to be public or has orgnisation as owner)

    Settings -> Environments -> New Environment

    name: prd (must match the "Based on selection" part)

    Then add environment secret:

    | Name    | Value |
    | -------- | ------- |
    |AZURE_CLIENT_ID | |
    |AZURE_SUBSCRIPTION_ID | |
    |AZURE_TENANT_ID| |

    You can also add environment variables here for your workflow.

5.  Workflow: Az cli login

    ```
    jobs:
        build:
            environment: prd
            steps:
            - name: 'Az CLI login'
            uses: azure/login@v2
            with:
                client-id: ${{ secrets.AZURE_CLIENT_ID }}
                tenant-id: ${{ secrets.AZURE_TENANT_ID }}
                subscription-id: ${{ secrets.AZURE_SUBSCRIPTION_ID }}
            
    ```

6.  Good luck