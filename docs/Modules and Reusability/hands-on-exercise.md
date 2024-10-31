---
order: -1
icon: rocket
label: Hands On Exercise
meta:
title: "Hands On Exercise"
---
# Exercise: Refactoring Configurations into Reusable Modules

### Objective
Participants will refactor their existing configurations (such as the one for provisioning a web server) into reusable **modules**. This exercise will help participants organize and parameterize their Terraform code, making it easier to reuse across different projects and environments.

---

### Steps

#### Step 1: Create a New Directory for the Module

1. **Create a Module Directory**:
    - Open your terminal and navigate to the project directory.
    - Create a new directory named `modules/webserver` to hold the reusable web server module:
      ```bash
      mkdir -p modules/webserver
      ```

2. **Verify Directory Structure**:
    - Your project directory should now contain the following structure:
      ```
      .
      ├── main.tf
      └── modules
          └── webserver
      ```

---

#### Step 2: Move the Existing Web Server Configuration into the Module

1. **Open Your Web Server Configuration**:
    - Locate your existing web server configuration (typically defined in `main.tf`).

2. **Move the Web Server Configuration to the Module Directory**:
    - Move the resource blocks that define the web server (e.g., Virtual Machine, Network Interface, Public IP) from `main.tf` to a new file named `main.tf` inside `modules/webserver`.

3. **Example `main.tf` for the Module**:
    - In `modules/webserver/main.tf`, the configuration might look like this:
      ```hcl
      resource "azurerm_network_interface" "example" {
        name                = "example-nic"
        location            = var.location
        resource_group_name = var.resource_group_name
        ip_configuration {
          name                          = "internal"
          subnet_id                     = azurerm_subnet.example.id
          private_ip_address_allocation = "Dynamic"
        }
      }
 
      resource "azurerm_virtual_machine" "example" {
        name                  = "example-vm"
        location              = var.location
        resource_group_name   = var.resource_group_name
        network_interface_ids = [azurerm_network_interface.example.id]
        vm_size               = "Standard_DS1_v2"
 
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
 
        os_profile_linux_config {
          disable_password_authentication = false
        }
      }
      ```

4. **Save the Module Configuration**.

---

#### Step 3: Define Input Variables in `variables.tf` for the Module

1. **Create a `variables.tf` File in the Module Directory**:
    - In `modules/webserver`, create a new file named `variables.tf`.

2. **Define Variables for Resource Group Name and Location**:
    - Add variables to parameterize the Resource Group name and location:
      ```hcl
      variable "resource_group_name" {
        description = "The name of the resource group"
        type        = string
      }
 
      variable "location" {
        description = "The location for the web server resources"
        type        = string
      }
      ```

3. **Save the `variables.tf` File**.

---

#### Step 4: Create `outputs.tf` to Capture and Return Useful Information

1. **Create an `outputs.tf` File in the Module Directory**:
    - In `modules/webserver`, create a new file named `outputs.tf`.

2. **Define Outputs for Useful Information**:
    - Capture information such as the public IP address of the Virtual Machine:
      ```hcl
      output "vm_id" {
        description = "The ID of the virtual machine"
        value       = azurerm_virtual_machine.example.id
      }
 
      output "public_ip_address" {
        description = "The public IP address of the virtual machine"
        value       = azurerm_network_interface.example.ip_configuration[0].private_ip_address
      }
      ```

3. **Save the `outputs.tf` File**.

---

#### Step 5: Call the Module in the Root Configuration (`main.tf`)

1. **Open the Root `main.tf` File**:
    - Open the `main.tf` file in the root directory of your project.

2. **Add a Module Block to Call the `webserver` Module**:
    - Call the `webserver` module by specifying its source directory and passing in the necessary variables:
      ```hcl
      module "webserver" {
        source              = "./modules/webserver"
        resource_group_name = "myResourceGroup"
        location            = "East US"
      }
      ```

3. **Save the `main.tf` File**.

---

#### Step 6: Run Terraform Commands to Test the Module

1. **Initialize the Project**:
    - In the terminal, run `terraform init` to ensure that all module dependencies are loaded and the project is ready:
      ```bash
      terraform init
      ```

2. **Run `terraform plan` to Preview the Changes**:
    - Run `terraform plan` to review the resources that will be created by the `webserver` module:
      ```bash
      terraform plan
      ```
    - Ensure that the output includes the expected resources, such as the Virtual Machine and Network Interface.

3. **Run `terraform apply` to Deploy the Module**:
    - Apply the configuration to create the web server resources using the module:
      ```bash
      terraform apply
      ```
    - Type `yes` when prompted to confirm the apply operation.

4. **Review the Output Values**:
    - After the apply completes, observe the output values defined in `outputs.tf`, such as the VM ID and public IP address.

---

### Verification

- **Confirm Resource Creation in Azure**:
    - Log in to the [Azure Portal](https://portal.azure.com/) and check the Resource Group specified in the module call.
    - Verify that the web server resources (e.g., Virtual Machine, Network Interface) have been created with the correct settings.

- **Check Output Values**:
    - Ensure that the output values displayed in the terminal match the expected details for the VM and network configurations.

---