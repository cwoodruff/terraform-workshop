---
order: -3
icon: rocket
label: Bonus Hands On Exercise
meta:
title: "Bonus Hands On Exercise"
---

# Bonus Exercise: Writing a Basic Terraform Configuration and Running Plan

### Exercise Objective
Participants will write a basic Terraform configuration that defines an Azure resource (e.g., a Resource Group or Virtual Network) and then run `terraform plan` to preview the changes that Terraform would make.

---

### Steps

#### Step 1: Create a New Directory for the Terraform Project
1. **Open a Terminal or Command Prompt**:
    - Ensure that you are in a directory where you want to create your new Terraform project.

2. **Create a New Directory**:
    - Create a folder called `my-terraform-project` and navigate into it:
      ```bash
      mkdir my-terraform-project
      cd my-terraform-project
      ```

---

#### Step 2: Write a Basic `main.tf` File to Define an Azure Resource

1. **Open Your Preferred Text Editor** (VS Code or IntelliJ):
    - Open the `my-terraform-project` directory in your editor.

2. **Create a `main.tf` File**:
    - In the `my-terraform-project` directory, create a new file called `main.tf`.

3. **Add Basic Configuration for Azure Provider**:
    - Add the following configuration in `main.tf` to specify the Azure provider and create a Resource Group:
      ```hcl
      # Configure the Azure provider
      provider "azurerm" {
        features {}
      }
 
      # Create an Azure Resource Group
      resource "azurerm_resource_group" "example" {
        name     = "myResourceGroup"
        location = "East US"
      }
      ```

    - This configuration does the following:
        - Specifies `azurerm` as the provider, which allows Terraform to interact with Azure resources.
        - Defines a `resource_group` in Azure named `myResourceGroup` located in the "East US" region.

---

#### Step 3: Initialize the Terraform Project

1. **Run `terraform init`**:
    - In the terminal, ensure you are in the `my-terraform-project` directory and initialize the Terraform project:
      ```bash
      terraform init
      ```
    - This command downloads the necessary provider plugins (in this case, `azurerm`) and sets up the project.

2. **Confirm Initialization**:
    - After running `terraform init`, you should see a message confirming that Terraform has been successfully initialized.

---

#### Step 4: Run `terraform plan` to Preview Changes

1. **Run the `terraform plan` Command**:
    - In the terminal, run the following command to preview the changes that Terraform will make:
      ```bash
      terraform plan
      ```
    - `terraform plan` compares your configuration with the actual state of resources and shows what actions Terraform will take.

2. **Review the Output**:
    - Look at the output of `terraform plan` to see the planned changes:
        - You should see an output indicating that Terraform will **add** a new resource (indicated by a `+` symbol).
        - The output will display the details of the resource (e.g., the name and location of the Resource Group).
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

3. **Understand the `terraform plan` Output**:
    - The plan output indicates:
        - Terraform will create (`+`) the `azurerm_resource_group` resource.
        - The resource details such as the `id`, `name`, and `location` are shown.
    - This step allows you to verify that the configuration is correct before actually applying changes.

---

### Verification

- **Confirm the Configuration is Valid**:
    - Ensure that the `terraform plan` output confirms Terraform will create the specified Resource Group.
- **Troubleshoot Issues (if any)**:
    - If there are any errors or unexpected output, review the configuration in `main.tf` and ensure `terraform init` was completed successfully.

---
