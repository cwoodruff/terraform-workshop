---
order: -2
icon: rocket
label: Bonus Hands On Exercise
meta:
title: "Bonus Hands On Exercise"
---
# Bonus Hands-On Exercise: Using Variables and Outputs Across Multiple Environments

### Exercise Objective
Participants will create separate variable files for **development** and **production** environments, allowing them to provision the same infrastructure with different parameters and outputs. This exercise will help participants understand how to manage multiple environments with Terraform using variable files.

---

### Steps

#### Step 1: Set Up the Project Directory and Base Configuration

1. **Create a New Project Directory** (if not already created):
    - Open a terminal and create a new directory for the project, then navigate into it:
      ```bash
      mkdir multi-environment-project
      cd multi-environment-project
      ```

2. **Create a `main.tf` File**:
    - In your project directory, create a file named `main.tf`.
    - Define the Azure provider and a Resource Group resource in `main.tf`:
      ```hcl
      provider "azurerm" {
        features {}
      }
 
      resource "azurerm_resource_group" "example" {
        name     = var.resource_group_name
        location = var.location
      }
      ```
    - This configuration uses variables to set the name and location of the Resource Group, allowing the values to be customized by environment.

3. **Save the `main.tf` File**.

---

#### Step 2: Define Variables in `variables.tf`

1. **Create a `variables.tf` File**:
    - In the same project directory, create a new file named `variables.tf`.

2. **Define Variables for Resource Group Name and Location**:
    - In `variables.tf`, add the following code to define the `resource_group_name` and `location` variables:
      ```hcl
      variable "resource_group_name" {
        description = "The name of the resource group"
        type        = string
      }
 
      variable "location" {
        description = "The Azure region to deploy resources"
        type        = string
      }
      ```
    - These variables will be set differently for each environment using separate variable files.

3. **Save the `variables.tf` File**.

---

#### Step 3: Create Separate Variable Files for Development and Production

1. **Create a `dev.tfvars` File**:
    - In your project directory, create a file named `dev.tfvars`.
    - Add the following variable values specific to the development environment:
      ```hcl
      resource_group_name = "dev-resource-group"
      location            = "East US"
      ```

2. **Create a `prod.tfvars` File**:
    - In your project directory, create another file named `prod.tfvars`.
    - Add the following variable values specific to the production environment:
      ```hcl
      resource_group_name = "prod-resource-group"
      location            = "West US"
      ```

3. **Save Both Variable Files**.

---

#### Step 4: Define Outputs in `outputs.tf`

1. **Create an `outputs.tf` File**:
    - In your project directory, create a new file named `outputs.tf`.

2. **Define Outputs for Resource Group Name and Location**:
    - In `outputs.tf`, add the following output definitions to display the Resource Group’s name and location:
      ```hcl
      output "resource_group_name" {
        description = "The name of the resource group"
        value       = azurerm_resource_group.example.name
      }
 
      output "resource_group_location" {
        description = "The location of the resource group"
        value       = azurerm_resource_group.example.location
      }
      ```

3. **Save the `outputs.tf` File**.

---

#### Step 5: Run Terraform Commands for Each Environment

1. **Initialize the Project**:
    - In the terminal, run the following command to initialize the project and download provider plugins:
      ```bash
      terraform init
      ```

2. **Run `terraform plan` for the Development Environment**:
    - Use `terraform plan` with the `-var-file` flag to preview the changes for the development environment:
      ```bash
      terraform plan -var-file="dev.tfvars"
      ```
    - Review the output to ensure that Terraform will create a Resource Group with the name `dev-resource-group` in the "East US" region.

3. **Run `terraform apply` for the Development Environment**:
    - Apply the configuration using the `dev.tfvars` file to create the Resource Group for development:
      ```bash
      terraform apply -var-file="dev.tfvars"
      ```
    - Type `yes` when prompted to confirm the apply operation.
    - After the apply completes, note the output values for `resource_group_name` and `resource_group_location`, which should reflect the development environment settings.

4. **Repeat for the Production Environment**:
    - Run `terraform plan` and `terraform apply` using the `prod.tfvars` file:
      ```bash
      terraform plan -var-file="prod.tfvars"
      terraform apply -var-file="prod.tfvars"
      ```
    - Confirm the creation of the Resource Group for the production environment, which should have the name `prod-resource-group` and location "West US".

---

#### Step 6: Verify Resource Groups in Azure Portal

1. **Log in to the Azure Portal**:
    - Go to the [Azure Portal](https://portal.azure.com/).

2. **Navigate to Resource Groups**:
    - Check that both `dev-resource-group` and `prod-resource-group` have been created in the respective regions specified in `dev.tfvars` and `prod.tfvars`.

---

### Verification

- **Confirm Environment-Specific Resources**:
    - Ensure that the Resource Groups in Azure match the names and locations specified in `dev.tfvars` and `prod.tfvars`.

- **Confirm Output Values**:
    - Verify that the output values displayed in the terminal during `terraform apply` reflect the correct resource group name and location for each environment.

---