---
order: -2
icon: rocket
label: Bonus Hands On Exercise
meta:
title: "Bonus Hands On Exercise"
---
# Bonus Hands-On Exercise: Creating a Configuration with Variables and Outputs

### Exercise Objective
Participants will enhance their Terraform configuration by adding **variables** to make resource definitions more dynamic and **outputs** to capture key information about the resources provisioned (e.g., the name of the Resource Group or its location).

---

### Steps

#### Step 1: Define Variables for Resource Configuration

1. **Create a `variables.tf` File**:
    - If you haven’t already, create a `variables.tf` file in your Terraform project directory.

2. **Define Variables in `variables.tf`**:
    - Add variable definitions for the Resource Group name and location. This will allow you to change these values dynamically without modifying the resource configuration directly.
    - Example:
      ```hcl
      # Define a variable for the Resource Group name
      variable "resource_group_name" {
        description = "The name of the Azure Resource Group"
        type        = string
        default     = "myDynamicResourceGroup"
      }
 
      # Define a variable for the Azure region (location)
      variable "location" {
        description = "The Azure region where the Resource Group will be created"
        type        = string
        default     = "East US"
      }
      ```

3. **Save the `variables.tf` File**:
    - Save the file to apply the variable definitions to your Terraform configuration.

---

#### Step 2: Update `main.tf` to Use Variables

1. **Open the `main.tf` File**:
    - Open your existing `main.tf` file where the Azure provider and Resource Group resource are defined.

2. **Modify the Resource Group to Use Variables**:
    - Update the `azurerm_resource_group` resource block to use the variables defined in `variables.tf`.
    - Example:
      ```hcl
      # Configure the Azure provider
      provider "azurerm" {
        features {}
      }
 
      # Create an Azure Resource Group using variables
      resource "azurerm_resource_group" "example" {
        name     = var.resource_group_name
        location = var.location
      }
      ```
    - Here, `var.resource_group_name` and `var.location` reference the values of the variables defined in `variables.tf`, making the configuration more flexible.

3. **Save the `main.tf` File**:
    - Save the changes in `main.tf`.

---

#### Step 3: Define Outputs to Capture Resource Information

1. **Create an `outputs.tf` File**:
    - In your project directory, create a new file named `outputs.tf`. This file will contain the output definitions.

2. **Define Outputs in `outputs.tf`**:
    - Add output definitions to capture information about the provisioned Resource Group, such as its name and location.
    - Example:
      ```hcl
      # Output the Resource Group name
      output "resource_group_name" {
        description = "The name of the resource group"
        value       = azurerm_resource_group.example.name
      }
 
      # Output the Resource Group location
      output "resource_group_location" {
        description = "The location of the resource group"
        value       = azurerm_resource_group.example.location
      }
      ```

3. **Save the `outputs.tf` File**:
    - Save the `outputs.tf` file to apply the output definitions to your Terraform configuration.

---

#### Step 4: Initialize and Run `terraform plan` to Preview Changes

1. **Initialize the Project** (if not already initialized):
    - In the terminal, navigate to the project directory and run:
      ```bash
      terraform init
      ```
    - This command downloads necessary provider plugins and prepares the working directory for Terraform operations.

2. **Run `terraform plan`**:
    - Run the following command to preview the changes Terraform will make:
      ```bash
      terraform plan
      ```
    - The plan output should show that Terraform will create a Resource Group with the specified name and location.

3. **Review the Plan Output**:
    - Verify that the plan output confirms Terraform will create the specified Resource Group.
    - The output should include information about the planned creation of the Resource Group.

---

#### Step 5: Apply the Configuration to Provision the Resource Group

1. **Run `terraform apply`**:
    - Run the following command to apply the configuration and create the Resource Group:
      ```bash
      terraform apply
      ```
    - When prompted, type "yes" to confirm the apply operation.

2. **Review the Output Values**:
    - After Terraform completes the apply, check the terminal output for the values of `resource_group_name` and `resource_group_location`.
    - Example output:
      ```
      Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
 
      Outputs:
 
      resource_group_name = "myDynamicResourceGroup"
      resource_group_location = "East US"
      ```

3. **Verify the Resource in the Azure Portal** (optional):
    - Go to the Azure Portal and navigate to **Resource Groups** to confirm that the Resource Group has been created with the correct name and location.

---

### Verification

- **Confirm Variable Use**:
    - Check that the Resource Group was created with the name and location specified in the variables.

- **Confirm Outputs**:
    - Verify that the output values for `resource_group_name` and `resource_group_location` are displayed after running `terraform apply`.

- **Troubleshoot Issues (if any)**:
    - If any errors occur, check that the variables are correctly referenced in `main.tf` and that `outputs.tf` is properly defined.

---