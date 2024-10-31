---
order: -1
icon: rocket
label: Hands On Exercise
meta:
title: "Hands On Exercise"
---
# Exercise: Writing a Basic Terraform Configuration

### Objective
Participants will write a basic Terraform configuration to provision a simple resource (e.g., a Resource Group) in Azure, using variables to make the configuration more dynamic.

---

### Steps

#### Step 1: Define the Azure Provider in `main.tf`

1. **Create a New Project Directory**:
    - Open a terminal or command prompt.
    - Create a directory for the Terraform project and navigate into it:
      ```bash
      mkdir my-terraform-project
      cd my-terraform-project
      ```

2. **Create a `main.tf` File**:
    - Open your preferred code editor (e.g., VS Code or IntelliJ).
    - Inside the `my-terraform-project` directory, create a file named `main.tf`.

3. **Add the Azure Provider to `main.tf`**:
    - In the `main.tf` file, add the following configuration to specify the Azure provider:
      ```hcl
      # Configure the Azure provider
      provider "azurerm" {
        features {}
      }
      ```
    - This configuration specifies the `azurerm` provider, which allows Terraform to manage Azure resources. The `features` block is required by the provider but can be left empty.

---

#### Step 2: Add a Resource Block to Create a Resource Group

1. **Define an Azure Resource Group in `main.tf`**:
    - Below the provider block in `main.tf`, add a resource block to create an Azure Resource Group.
    - Example:
      ```hcl
      # Create an Azure Resource Group
      resource "azurerm_resource_group" "example" {
        name     = "myResourceGroup"
        location = "East US"
      }
      ```
    - This configuration creates a new Azure Resource Group with:
        - `name`: The name of the Resource Group (set to "myResourceGroup" in this example).
        - `location`: The Azure region where the Resource Group will be created (set to "East US" here).

---

#### Step 3: Use Variables for Dynamic Configuration

1. **Create a `variables.tf` File**:
    - In the same project directory, create a new file named `variables.tf`.
    - This file will hold the variable definitions for the Resource Group name and location, making the configuration more flexible.

2. **Define Variables in `variables.tf`**:
    - Add the following variable definitions to `variables.tf`:
      ```hcl
      # Define a variable for the Resource Group name
      variable "resource_group_name" {
        description = "The name of the Azure Resource Group"
        type        = string
        default     = "myResourceGroup"
      }
 
      # Define a variable for the Azure region (location)
      variable "location" {
        description = "The Azure region where the Resource Group will be created"
        type        = string
        default     = "East US"
      }
      ```

3. **Modify `main.tf` to Use Variables**:
    - Update the resource block in `main.tf` to use the variables defined in `variables.tf`:
      ```hcl
      # Create an Azure Resource Group using variables
      resource "azurerm_resource_group" "example" {
        name     = var.resource_group_name
        location = var.location
      }
      ```
    - Here, `var.resource_group_name` and `var.location` reference the values of the variables defined in `variables.tf`.

---

#### Step 4: Run `terraform plan` to Preview Changes and Ensure the Configuration is Valid

1. **Initialize the Project**:
    - In the terminal, navigate to the `my-terraform-project` directory if you’re not already there.
    - Run `terraform init` to initialize the project and download the required provider plugin:
      ```bash
      terraform init
      ```

2. **Run `terraform plan`**:
    - After initializing, run the following command to preview the changes Terraform will make:
      ```bash
      terraform plan
      ```
    - This command checks the configuration, evaluates the values of variables, and generates a plan showing the actions Terraform will take to create the resource.

3. **Review the Output of `terraform plan`**:
    - The output should display a summary indicating that Terraform will add a new resource, along with the details of the Resource Group.
    - Example output:
      ```
      Terraform will perform the following actions:
 
        # azurerm_resource_group.example will be created
        + resource "azurerm_resource_group" "example" {
            + id       = (known after apply)
            + location = "East US"
            + name     = "myResourceGroup"
        }
 
      Plan: 1 to add, 0 to change, 0 to destroy.
      ```
    - Ensure that the output matches your expectations and that the Resource Group will be created with the specified name and location.

---

### Verification

- **Confirm the Initialization**:
    - Ensure that `terraform init` ran without errors and successfully initialized the project.

- **Verify the Plan Output**:
    - Confirm that the output of `terraform plan` shows Terraform will create a new Resource Group with the specified details.

- **Troubleshoot Issues**:
    - If there are errors in the configuration, review `main.tf` and `variables.tf` to ensure all syntax and variable references are correct.

---