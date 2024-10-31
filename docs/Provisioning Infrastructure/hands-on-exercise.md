---
order: -1
icon: rocket
label: Hands On Exercise
meta:
title: "Hands On Exercise"
---
# Exercise: Provisioning a Web Server on Azure

### Objective
Provision a web server on Azure using Terraform by following the Terraform workflow (`plan`, `apply`, `destroy`). This exercise will help participants understand how to define resources in a Terraform configuration, preview changes, apply the configuration, and optionally clean up resources.

---

### Steps

#### Step 1: Define an Azure Virtual Machine in the `main.tf` File

1. **Create a New Directory for the Project**:
    - Open a terminal or command prompt.
    - Create a directory for the project and navigate into it:
      ```bash
      mkdir web-server-project
      cd web-server-project
      ```

2. **Create a `main.tf` File**:
    - Open your preferred code editor (e.g., VS Code or IntelliJ).
    - Inside the `web-server-project` directory, create a file named `main.tf`.

3. **Add the Azure Provider Configuration**:
    - In `main.tf`, add the following code to configure the Azure provider:
      ```hcl
      provider "azurerm" {
        features {}
      }
      ```

4. **Define the Azure Resource Group**:
    - Add a resource block for the Resource Group:
      ```hcl
      resource "azurerm_resource_group" "example" {
        name     = "webserver-rg"
        location = "East US"
      }
      ```

5. **Define the Virtual Network and Subnet**:
    - Add a resource block for a Virtual Network and a Subnet:
      ```hcl
      resource "azurerm_virtual_network" "example" {
        name                = "example-vnet"
        location            = azurerm_resource_group.example.location
        resource_group_name = azurerm_resource_group.example.name
        address_space       = ["10.0.0.0/16"]
      }
 
      resource "azurerm_subnet" "example" {
        name                 = "example-subnet"
        resource_group_name  = azurerm_resource_group.example.name
        virtual_network_name = azurerm_virtual_network.example.name
        address_prefixes     = ["10.0.1.0/24"]
      }
      ```

6. **Define the Network Interface**:
    - Add a resource block for a Network Interface that connects to the subnet:
      ```hcl
      resource "azurerm_network_interface" "example" {
        name                = "example-nic"
        location            = azurerm_resource_group.example.location
        resource_group_name = azurerm_resource_group.example.name
        ip_configuration {
          name                          = "internal"
          subnet_id                     = azurerm_subnet.example.id
          private_ip_address_allocation = "Dynamic"
        }
      }
      ```

7. **Define the Virtual Machine**:
    - Add a resource block to create a Virtual Machine with the following configuration:
      ```hcl
      resource "azurerm_virtual_machine" "example" {
        name                  = "example-vm"
        location              = azurerm_resource_group.example.location
        resource_group_name   = azurerm_resource_group.example.name
        network_interface_ids = [azurerm_network_interface.example.id]
        vm_size               = "Standard_DS1_v2"
 
        storage_image_reference {
          publisher = "Canonical"
          offer     = "UbuntuServer"
          sku       = "18.04-LTS"
          version   = "latest"
        }
 
        storage_os_disk {
          name              = "example-osdisk"
          caching           = "ReadWrite"
          create_option     = "FromImage"
          managed_disk_type = "Standard_LRS"
        }
 
        os_profile {
          computer_name  = "examplevm"
          admin_username = "adminuser"
          admin_password = "P@ssw0rd123!"
        }
 
        os_profile_linux_config {
          disable_password_authentication = false
        }
      }
      ```
    - This configuration defines a VM with:
        - **VM Size**: `Standard_DS1_v2`
        - **Image**: Ubuntu Server 18.04 LTS
        - **Admin Username**: `adminuser`
        - **Admin Password**: `P@ssw0rd123!`

8. **Save the `main.tf` File**:
    - Save all changes in `main.tf`.

---

#### Step 2: Run `terraform plan` to Preview the Changes

1. **Initialize the Project**:
    - In the terminal, navigate to the project directory (if not already there) and run:
      ```bash
      terraform init
      ```
    - This will download the necessary provider plugins and prepare the project for deployment.

2. **Run `terraform plan`**:
    - Use the following command to preview the changes Terraform will make:
      ```bash
      terraform plan
      ```
    - The `terraform plan` command shows a detailed list of the resources that will be created, including the Resource Group, Virtual Network, Subnet, Network Interface, and Virtual Machine.

3. **Review the Plan Output**:
    - Verify that the plan output lists all the resources defined in `main.tf` with a `+` symbol, indicating that they will be created.

---

#### Step 3: Run `terraform apply` to Provision the Resources

1. **Apply the Configuration**:
    - Run the following command to provision the resources on Azure:
      ```bash
      terraform apply
      ```
    - When prompted, type `yes` to confirm the apply operation.

2. **Review the Output**:
    - After the apply completes, Terraform will display the resources created and their properties, such as VM ID, IP address, etc.

---

#### Step 4: Verify the VM in the Azure Portal

1. **Log in to the Azure Portal**:
    - Go to the [Azure Portal](https://portal.azure.com/).

2. **Navigate to Resource Groups**:
    - Locate the Resource Group named `webserver-rg`.

3. **Verify the Virtual Machine**:
    - Check that the VM named `example-vm` has been created with the specified configuration.
    - Confirm that the Virtual Network, Subnet, and Network Interface are also present.

---

#### Optional Step 5: Run `terraform destroy` to Clean Up Resources

1. **Destroy the Resources**:
    - To delete all resources created by this configuration, run:
      ```bash
      terraform destroy
      ```
    - Type `yes` when prompted to confirm the destroy operation.

2. **Verify Resource Deletion in the Azure Portal**:
    - After the destroy operation completes, refresh the Azure Portal and confirm that the resources (Resource Group, VM, etc.) have been removed.

---