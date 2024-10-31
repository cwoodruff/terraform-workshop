---
order: -2
icon: rocket
label: Bonus Hands On Exercise
meta:
title: "Bonus Hands On Exercise"
---
# Bonus Hands-On Exercise: Creating a Networking Module for Virtual Networks

### Exercise Objective
In this bonus exercise, participants will create a reusable Terraform **module** to manage virtual networks (VNets) in Azure. They will then use this module to deploy virtual networks in different environments, such as **development** and **production**.

---

### Steps

#### Step 1: Create a New Directory for the Networking Module

1. **Create the Module Directory**:
    - Open your terminal and navigate to your project directory.
    - Create a new directory named `modules/network` to hold the networking module:
      ```bash
      mkdir -p modules/network
      ```

2. **Verify Directory Structure**:
    - Your project directory should now contain:
      ```
      .
      ├── main.tf
      └── modules
          └── network
      ```

---

#### Step 2: Create the `main.tf` for the Networking Module

1. **Create a `main.tf` File in the Module Directory**:
    - In `modules/network`, create a file named `main.tf` to hold the virtual network configuration.

2. **Add Virtual Network and Subnet Resource Definitions**:
    - In `modules/network/main.tf`, define resources for the virtual network and a subnet:
      ```hcl
      resource "azurerm_virtual_network" "vnet" {
        name                = var.vnet_name
        location            = var.location
        resource_group_name = var.resource_group_name
        address_space       = var.address_space
      }
 
      resource "azurerm_subnet" "subnet" {
        name                 = var.subnet_name
        resource_group_name  = var.resource_group_name
        virtual_network_name = azurerm_virtual_network.vnet.name
        address_prefixes     = var.subnet_address_prefixes
      }
      ```
    - This configuration defines:
        - **Virtual Network**: Using parameters for the name, location, resource group, and address space.
        - **Subnet**: Using parameters for the subnet name, address prefixes, and associations to the virtual network.

3. **Save the `main.tf` File**.

---

#### Step 3: Define Input Variables in `variables.tf` for the Module

1. **Create a `variables.tf` File in the Module Directory**:
    - In `modules/network`, create a file named `variables.tf`.

2. **Define Variables for VNet and Subnet Properties**:
    - Add the following variable definitions to parameterize the VNet and subnet configuration:
      ```hcl
      variable "vnet_name" {
        description = "The name of the virtual network"
        type        = string
      }
 
      variable "location" {
        description = "The Azure location for the network resources"
        type        = string
      }
 
      variable "resource_group_name" {
        description = "The name of the resource group"
        type        = string
      }
 
      variable "address_space" {
        description = "The address space for the virtual network"
        type        = list(string)
      }
 
      variable "subnet_name" {
        description = "The name of the subnet"
        type        = string
      }
 
      variable "subnet_address_prefixes" {
        description = "The address prefixes for the subnet"
        type        = list(string)
      }
      ```

3. **Save the `variables.tf` File**.

---

#### Step 4: Create Outputs in `outputs.tf` for the Module

1. **Create an `outputs.tf` File in the Module Directory**:
    - In `modules/network`, create a file named `outputs.tf`.

2. **Define Output Values for VNet and Subnet Information**:
    - Add outputs to provide information about the created virtual network and subnet:
      ```hcl
      output "vnet_id" {
        description = "The ID of the virtual network"
        value       = azurerm_virtual_network.vnet.id
      }
 
      output "subnet_id" {
        description = "The ID of the subnet"
        value       = azurerm_subnet.subnet.id
      }
      ```

3. **Save the `outputs.tf` File**.

---

#### Step 5: Call the Networking Module in the Root Configuration for Each Environment

1. **Create a `main.tf` File in the Root Directory**:
    - In the root directory of your project, open or create `main.tf` to use the networking module.

2. **Add Module Calls for Development and Production Environments**:
    - Call the `network` module twice, passing in different parameters for each environment:
      ```hcl
      # Development Environment
      module "dev_network" {
        source              = "./modules/network"
        vnet_name           = "dev-vnet"
        location            = "East US"
        resource_group_name = "dev-resource-group"
        address_space       = ["10.0.0.0/16"]
        subnet_name         = "dev-subnet"
        subnet_address_prefixes = ["10.0.1.0/24"]
      }
 
      # Production Environment
      module "prod_network" {
        source              = "./modules/network"
        vnet_name           = "prod-vnet"
        location            = "West US"
        resource_group_name = "prod-resource-group"
        address_space       = ["10.1.0.0/16"]
        subnet_name         = "prod-subnet"
        subnet_address_prefixes = ["10.1.1.0/24"]
      }
      ```
    - The module is sourced from `./modules/network`, and each environment (dev and prod) has unique values for `vnet_name`, `location`, `resource_group_name`, and `address_space`.

3. **Save the `main.tf` File**.

---

#### Step 6: Run Terraform Commands to Test the Module

1. **Initialize the Project**:
    - In the terminal, run `terraform init` to initialize the project and prepare for deployment:
      ```bash
      terraform init
      ```

2. **Run `terraform plan` to Preview the Changes**:
    - Run `terraform plan` to review the resources that will be created for both the development and production environments:
      ```bash
      terraform plan
      ```
    - Verify that the output includes the virtual network and subnet resources for both `dev` and `prod` environments.

3. **Run `terraform apply` to Deploy the Module**:
    - Apply the configuration to create the virtual networks and subnets in Azure:
      ```bash
      terraform apply
      ```
    - Type `yes` when prompted to confirm the apply operation.

4. **Review the Output Values**:
    - After the apply completes, observe the output values defined in `outputs.tf`, such as `vnet_id` and `subnet_id` for both environments.

---

### Verification

- **Confirm Resource Creation in Azure**:
    - Log in to the [Azure Portal](https://portal.azure.com/) and navigate to the Resource Groups for both `dev-resource-group` and `prod-resource-group`.
    - Verify that the virtual networks (`dev-vnet` and `prod-vnet`) and their respective subnets have been created with the correct configurations.

- **Check Output Values**:
    - Ensure that the output values displayed in the terminal match the expected VNet and subnet information for each environment.

---