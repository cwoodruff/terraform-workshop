---
order: -1
icon: rocket
label: Hands On Exercise
meta:
title: "Hands On Exercise"
---
# Exercise: Implementing Provisioners and Functions

### Objective
In this exercise, participants will:
- Implement provisioners to install a web server (Nginx) on an Azure Virtual Machine.
- Use Terraform functions to dynamically adjust the configuration based on the environment.
- Create and use separate **workspaces** for development (dev) and production (prod) environments.

---

### Steps

#### Step 1: Provision a Virtual Machine with Nginx Using a Provisioner

1. **Define a Virtual Machine in `main.tf`**:
    - In your project directory, create or open a `main.tf` file.
    - Add a resource block for an Azure Virtual Machine, configuring basic settings:
      ```hcl
      resource "azurerm_virtual_machine" "example" {
        name                  = "example-vm"
        location              = var.location
        resource_group_name   = var.resource_group_name
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
 
        os_profile_linux_config {
          disable_password_authentication = false
        }
 
        # Provisioner to install Nginx on the VM
        provisioner "remote-exec" {
          connection {
            type        = "ssh"
            host        = self.public_ip_address
            user        = "adminuser"
            password    = "P@ssw0rd123!"
          }
 
          inline = [
            "sudo apt-get update",
            "sudo apt-get install -y nginx"
          ]
        }
      }
      ```

    - This configuration defines a Linux VM running Ubuntu Server 18.04 with an inline remote-exec provisioner to install Nginx after the VM is provisioned.

2. **Define Network Interface and Public IP** (if needed):
    - Ensure the VM has a network interface and public IP so the provisioner can connect and install Nginx.
    - Example:
      ```hcl
      resource "azurerm_network_interface" "example" {
        name                = "example-nic"
        location            = var.location
        resource_group_name = var.resource_group_name
 
        ip_configuration {
          name                          = "internal"
          subnet_id                     = azurerm_subnet.example.id
          private_ip_address_allocation = "Dynamic"
          public_ip_address_id          = azurerm_public_ip.example.id
        }
      }
 
      resource "azurerm_public_ip" "example" {
        name                = "example-pip"
        location            = var.location
        resource_group_name = var.resource_group_name
        allocation_method   = "Static"
      }
      ```

---

#### Step 2: Use Terraform Functions to Adjust Configuration Dynamically

1. **Define the VM Size Based on Environment**:
    - Use the `terraform.workspace` variable and a conditional expression to set the VM size.
    - Add the following code to `variables.tf` or directly in `main.tf`:
      ```hcl
      variable "vm_size" {
        description = "The size of the VM based on the environment"
        type        = string
        default     = terraform.workspace == "prod" ? "Standard_DS2_v2" : "Standard_DS1_v2"
      }
      ```

2. **Use the `length()` Function** (Optional Example):
    - To dynamically calculate values based on lists, you could use the `length()` function in expressions:
      ```hcl
      output "number_of_nics" {
        description = "The number of network interfaces"
        value       = length(azurerm_network_interface.example)
      }
      ```

    - This example calculates the number of network interfaces attached to the VM.

---

#### Step 3: Switch Workspaces for Dev and Prod Environments

1. **Create and Switch to the Development Workspace**:
    - In the terminal, run:
      ```bash
      terraform workspace new dev
      ```
    - Terraform will create a new workspace called `dev` and switch to it.

2. **Create the Production Workspace**:
    - Create another workspace for the production environment:
      ```bash
      terraform workspace new prod
      ```

3. **Verify Workspace**:
    - You can check your current workspace with:
      ```bash
      terraform workspace show
      ```

4. **Set Environment-Specific Values** (Optional):
    - Use separate variable files (e.g., `dev.tfvars` and `prod.tfvars`) to provide different values for `location` or `resource_group_name` based on the workspace.

---

#### Step 4: Run `terraform plan` and `terraform apply` to Verify the Setup

1. **Initialize the Project** (if not already initialized):
    - Run `terraform init` to download necessary provider plugins and prepare the environment:
      ```bash
      terraform init
      ```

2. **Run `terraform plan`**:
    - In the current workspace, run `terraform plan` to verify the configuration:
      ```bash
      terraform plan -var-file="dev.tfvars"  # for dev
      terraform plan -var-file="prod.tfvars" # for prod
      ```
    - Review the output to ensure Terraform will provision a VM with the correct configurations (e.g., VM size based on the workspace).

3. **Run `terraform apply` to Deploy the VM**:
    - Run `terraform apply` to create the VM and install Nginx via the provisioner:
      ```bash
      terraform apply -var-file="dev.tfvars"  # for dev
      terraform apply -var-file="prod.tfvars" # for prod
      ```
    - Type `yes` when prompted to confirm the apply operation.

4. **Verify the Setup in the Azure Portal**:
    - Go to the [Azure Portal](https://portal.azure.com/) and check that the VM has been created in the correct environment with Nginx installed.

---

### Verification

- **Confirm VM Creation**:
    - Verify that a VM has been created in Azure for each workspace and is using the correct VM size based on the environment.

- **Verify Nginx Installation**:
    - Use SSH to connect to the VM and check if Nginx is running:
      ```bash
      ssh adminuser@<VM_PUBLIC_IP>
      sudo systemctl status nginx
      ```

- **Verify Output Values** (if configured):
    - Check any outputs, such as `number_of_nics`, to ensure they reflect the actual setup.

---