---
order: -1
icon: rocket
label: Hands On Exercise
meta:
title: "Hands On Exercise"
---
# Exercise: Creating a Configuration with Variables and Outputs

### Objective
Participants will create a Terraform configuration that uses **variables** to define a Resource Group and **outputs** to display its name and location. This exercise will help participants understand how to use variables for flexible configurations and outputs to extract useful information.

---

### Steps

#### Step 1: Create a `variables.tf` File with Input Variables

1. **Create a `variables.tf` File**:
    - Open your code editor (e.g., VS Code or IntelliJ).
    - In your project directory, create a new file named `variables.tf`.

2. **Define Variables for Resource Group Name and Location**:
    - In `variables.tf`, add the following code to define the `resource_group_name` and `location` variables:
      ```hcl
      variable "resource_group_name" {
        description = "The name of the resource group"
        type        = string
        default     = "myResourceGroup"
      }
 
      variable "location" {
        description = "The Azure region to deploy resources"
        type        = string
        default     = "East US"
      }
      ```
    - These variables allow the configuration to dynamically set the Resource Group name and location, making it easy to reuse the code for different projects or environments.

3. **Save the `variables.tf` File**.

---

#### Step 2: Create a `main.tf` File that Uses the Variables

1. **Create a `main.tf` File**:
    - In the same project directory, create a new file named `main.tf`.

2. **Add Resource Group Configuration Using Variables**:
    - In `main.tf`, define a Resource Group resource and use the variables defined in `variables.tf` to set its `name` and `location`:
      ```hcl
      resource "azurerm_resource_group" "example" {
        name     = var.resource_group_name
        location = var.location
      }
      ```
    - Here, `var.resource_group_name` and `var.location` reference the values of the variables, making the configuration flexible.

3. **Save the `main.tf` File**.

---

#### Step 3: Create an `outputs.tf` File to Display Resource Information

1. **Create an `outputs.tf` File**:
    - In your project directory, create a new file named `outputs.tf`.

2. **Define Output Values for Resource Group Name and Location**:
    - In `outputs.tf`, add the following code to define outputs that display the Resource Group’s name and location:
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
    - These output values will provide useful information about the provisioned resources when `terraform apply` completes.

3. **Save the `outputs.tf` File**.

---

#### Step 4: Run `terraform plan` and `terraform apply` to Provision the Resource Group and Display Outputs

1. **Initialize the Terraform Project**:
    - In the terminal, navigate to the project directory and run:
      ```bash
      terraform init
      ```
    - This command will download necessary provider plugins and prepare the project for deployment.

2. **Run `terraform plan` to Preview the Changes**:
    - Use `terraform plan` to verify the configuration and preview the changes Terraform will make:
      ```bash
      terraform plan
      ```
    - Review the output to ensure that Terraform will create a Resource Group with the specified name and location.

3. **Run `terraform apply` to Create the Resource Group**:
    - Apply the configuration to create the Resource Group on Azure:
      ```bash
      terraform apply
      ```
    - Type `yes` when prompted to confirm the apply operation.

4. **Review the Output Values**:
    - After `terraform apply` completes, check the terminal output to see the values for `resource_group_name` and `resource_group_location`.
    - Example output:
      ```
      Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
 
      Outputs:
 
      resource_group_name = "myResourceGroup"
      resource_group_location = "East US"
      ```

---

### Verification

- **Check the Azure Portal**:
    - Go to the [Azure Portal](https://portal.azure.com/) and navigate to **Resource Groups**.
    - Verify that the Resource Group named `myResourceGroup` was created in the specified location (e.g., "East US").

- **Confirm Output Values**:
    - Ensure that the output values for `resource_group_name` and `resource_group_location` displayed in the terminal match the values in the Azure Portal.

---