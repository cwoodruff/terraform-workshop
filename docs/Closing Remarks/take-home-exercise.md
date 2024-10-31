---
order: -1
icon: rocket
label: Take Home Exercise
meta:
title: "Take Home Exercise"
---
# Bonus Hands-On Exercise: Building a Complete Terraform Project with CI/CD

### Exercise Objective
In this exercise, participants will build a complete infrastructure project using Terraform and integrate it with a **CI/CD pipeline** to enable automatic deployments. By the end, participants will have a fully automated workflow to manage and deploy infrastructure changes.

---

### Steps

#### Step 1: Set Up a Terraform Project Directory Structure

1. **Create the Project Directory**:
    - In your terminal, create a new directory for the project and navigate into it:
      ```bash
      mkdir terraform-cicd-project
      cd terraform-cicd-project
      ```

2. **Create the Basic Directory Structure**:
    - Inside `terraform-cicd-project`, set up the following structure:
      ```
      .
      ├── main.tf
      ├── variables.tf
      ├── outputs.tf
      ├── dev.tfvars
      └── prod.tfvars
      ```

---

#### Step 2: Define the Infrastructure in Terraform

1. **Create the `main.tf` File**:
    - Open or create `main.tf` in your project directory. Add the configuration for an Azure Resource Group and Virtual Machine:
      ```hcl
      provider "azurerm" {
        features {}
      }
 
      resource "azurerm_resource_group" "example" {
        name     = var.resource_group_name
        location = var.location
      }
 
      resource "azurerm_virtual_machine" "example" {
        name                  = "example-vm"
        location              = azurerm_resource_group.example.location
        resource_group_name   = azurerm_resource_group.example.name
        network_interface_ids = [azurerm_network_interface.example.id]
        vm_size               = var.vm_size
 
        storage_image_reference {
          publisher = "Canonical"
          offer     = "UbuntuServer"
          sku       = "18.04-LTS"
          version   = "latest"
        }
 
        os_profile {
          computer_name  = "examplevm"
          admin_username = "adminuser"
          admin_password = "P@ssw0rd123!"
        }
      }
      ```

2. **Define Variables in `variables.tf`**:
    - Create `variables.tf` with the necessary input variables:
      ```hcl
      variable "resource_group_name" {
        description = "The name of the resource group"
        type        = string
      }
 
      variable "location" {
        description = "The Azure location for the resources"
        type        = string
      }
 
      variable "vm_size" {
        description = "The size of the virtual machine"
        type        = string
        default     = "Standard_DS1_v2"
      }
      ```

3. **Define Outputs in `outputs.tf`**:
    - Create `outputs.tf` to output important information about the deployed infrastructure:
      ```hcl
      output "resource_group_name" {
        value = azurerm_resource_group.example.name
      }
 
      output "vm_id" {
        value = azurerm_virtual_machine.example.id
      }
      ```

4. **Create Environment-Specific Variable Files**:
    - Define environment-specific values in `dev.tfvars` and `prod.tfvars`.

    - **`dev.tfvars`**:
      ```hcl
      resource_group_name = "dev-resource-group"
      location            = "East US"
      vm_size             = "Standard_DS1_v2"
      ```

    - **`prod.tfvars`**:
      ```hcl
      resource_group_name = "prod-resource-group"
      location            = "West US"
      vm_size             = "Standard_DS2_v2"
      ```

---

#### Step 3: Initialize the Terraform Project

1. **Initialize the Project**:
    - Run `terraform init` to initialize the project and download necessary provider plugins:
      ```bash
      terraform init
      ```

2. **Run `terraform plan`**:
    - Test the configuration to ensure there are no errors by running:
      ```bash
      terraform plan -var-file="dev.tfvars"
      ```

---

#### Step 4: Set Up a CI/CD Pipeline (e.g., GitHub Actions)

1. **Create a GitHub Repository**:
    - Go to [GitHub](https://github.com/) and create a new repository.
    - Clone the repository to your local machine and add all files from `terraform-cicd-project` to the repository.

2. **Create a GitHub Actions Workflow File**:
    - In your project directory, create a `.github/workflows` directory and add a workflow file for Terraform:
      ```
      .
      └── .github
          └── workflows
              └── terraform.yml
      ```

3. **Define the Workflow in `terraform.yml`**:
    - Open `terraform.yml` and configure it to automatically run `terraform plan` and `terraform apply` on the main branch.
    - Example workflow:
      ```yaml
      name: Terraform CI/CD Pipeline
 
      on:
        push:
          branches:
            - main
 
      jobs:
        terraform:
          runs-on: ubuntu-latest
 
          steps:
            - name: Checkout code
              uses: actions/checkout@v2
 
            - name: Set up Terraform
              uses: hashicorp/setup-terraform@v1
              with:
                terraform_version: 1.0.0
 
            - name: Terraform Init
              run: terraform init
 
            - name: Terraform Plan
              run: terraform plan -var-file="dev.tfvars"
 
            - name: Terraform Apply
              run: terraform apply -auto-approve -var-file="dev.tfvars"
              env:
                ARM_CLIENT_ID: ${{ secrets.ARM_CLIENT_ID }}
                ARM_CLIENT_SECRET: ${{ secrets.ARM_CLIENT_SECRET }}
                ARM_SUBSCRIPTION_ID: ${{ secrets.ARM_SUBSCRIPTION_ID }}
                ARM_TENANT_ID: ${{ secrets.ARM_TENANT_ID }}
      ```

    - **Note**: This workflow uses environment variables from GitHub Secrets (e.g., `ARM_CLIENT_ID`, `ARM_CLIENT_SECRET`). You must set these in the GitHub repository settings.

4. **Set Up GitHub Secrets for Azure Authentication**:
    - In your GitHub repository, navigate to **Settings > Secrets and variables > Actions** and add the following secrets:
        - **ARM_CLIENT_ID**
        - **ARM_CLIENT_SECRET**
        - **ARM_SUBSCRIPTION_ID**
        - **ARM_TENANT_ID**

---

#### Step 5: Test the CI/CD Pipeline

1. **Push Your Code to GitHub**:
    - Commit and push all files to the main branch of your GitHub repository:
      ```bash
      git add .
      git commit -m "Set up Terraform CI/CD pipeline"
      git push origin main
      ```

2. **Monitor the Workflow**:
    - Go to your GitHub repository, navigate to the **Actions** tab, and watch the pipeline run.
    - The workflow should automatically run `terraform init`, `terraform plan`, and `terraform apply`, deploying the infrastructure.

---

#### Step 6: Verify the Deployment in Azure

1. **Log in to the Azure Portal**:
    - Go to the [Azure Portal](https://portal.azure.com/).
    - Verify that the Resource Group and Virtual Machine were created according to the configuration.

2. **Check Output Values**:
    - After the workflow completes, review the GitHub Actions logs for the output values (such as `resource_group_name` and `vm_id`).

---

### Optional: Extend the Pipeline for Multiple Environments

1. **Modify the Workflow for Environment-Specific Files**:
    - Update the GitHub Actions workflow to support both `dev.tfvars` and `prod.tfvars` by using environment branches or workflows that trigger based on specific branches (e.g., `dev` and `prod` branches).

2. **Create Separate Branches**:
    - Use `dev` and `prod` branches in GitHub to separate development and production environments in the pipeline.

---