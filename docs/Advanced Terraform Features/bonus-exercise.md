---
order: -2
icon: rocket
label: Bonus Hands On Exercise
meta:
title: "Bonus Hands On Exercise"
---
# Bonus Hands-On Exercise: Dynamic Configuration with Workspaces and Provisioners

### Exercise Objective
In this exercise, participants will create dynamic infrastructure that adjusts based on the **current workspace**. They will also use **provisioners** to automatically install different software on virtual machines based on the environment (e.g., installing Nginx in development and Apache in production).

---

### Steps

#### Step 1: Set Up Workspace-Based Variables

1. **Create Separate Variable Files for Dev and Prod**:
    - In your project directory, create two separate variable files: `dev.tfvars` and `prod.tfvars`.

2. **Define Environment-Specific Variables in Each File**:
    - Add environment-specific values for `resource_group_name`, `location`, and `vm_software` in each file.

    - **`dev.tfvars`**:
      ```hcl
      resource_group_name = "dev-resource-group"
      location            = "East US"
      vm_software         = "nginx"
      ```

    - **`prod.tfvars`**:
      ```hcl
      resource_group_name = "prod-resource-group"
      location            = "West US"
      vm_software         = "apache2"
      ```

3. **Create `variables.tf`**:
    - Define the variables for `resource_group_name`, `location`, and `vm_software` in `variables.tf`:
      ```hcl
      variable "resource_group_name" {
        description = "The name of the resource group"
        type        = string
      }
 
      variable "location" {
        description = "The location for the resources"
        type        = string
      }
 
      variable "vm_software" {
        description = "The software to install on the VM"
        type        = string
      }
      ```

4. **Save All Variable Files**.

---

#### Step 2: Define the Virtual Machine Configuration in `main.tf`

1. **Open or Create `main.tf`**:
    - In your project directory, create or open a `main.tf` file to define the VM configuration.

2. **Add a Virtual Machine Resource with Provisioners**:
    - Define the Azure VM resource, using a `remote-exec` provisioner to install software based on the environment.
    - Example configuration:
      ```hcl
      resource "azurerm_virtual_machine" "example" {
        name                  = "example-vm"
        location              = var.location
        resource_group_name   = var.resource_group_name
        network_interface_ids = [azurerm_network_interface.example.id]
        vm_size               = terraform.workspace == "prod" ? "Standard_DS2_v2" : "Standard_DS1_v2"
 
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
 
        # Provisioner to install different software based on environment
        provisioner "remote-exec" {
          connection {
            type        = "ssh"
            host        = self.public_ip_address
            user        = "adminuser"
            password    = "P@ssw0rd123!"
          }
 
          inline = [
            "sudo apt-get update",
            "sudo apt-get install -y ${var.vm_software}"
          ]
        }
      }
      ```
    - The `vm_size` parameter dynamically changes based on the workspace, and the `vm_software` variable installs different software based on the environment.

3. **Save `main.tf`**.

---

#### Step 3: Set Up Networking Resources (if needed)

1. **Define Network Interface and Public IP**:
    - Ensure that the VM has a network interface and a public IP to allow SSH access for the provisioner.
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

#### Step 4: Initialize and Configure Workspaces for Dev and Prod

1. **Initialize the Project**:
    - Run `terraform init` to download provider plugins and prepare the environment:
      ```bash
      terraform init
      ```

2. **Create and Switch to the Development Workspace**:
    - In the terminal, create the dev workspace and switch to it:
      ```bash
      terraform workspace new dev
      ```

3. **Create the Production Workspace**:
    - Create another workspace for production:
      ```bash
      terraform workspace new prod
      ```

4. **Verify Current Workspace**:
    - Run `terraform workspace show` to confirm your current workspace.

---

#### Step 5: Run Terraform Commands for Each Environment

1. **Run `terraform plan` for Development Environment**:
    - In the dev workspace, use `terraform plan` with `dev.tfvars` to preview the changes:
      ```bash
      terraform plan -var-file="dev.tfvars"
      ```

2. **Run `terraform apply` for Development Environment**:
    - Apply the configuration to deploy the VM with Nginx in the development environment:
      ```bash
      terraform apply -var-file="dev.tfvars"
      ```
    - Type `yes` to confirm.

3. **Switch to Production Workspace**:
    - Switch to the production workspace:
      ```bash
      terraform workspace select prod
      ```

4. **Run `terraform plan` and `terraform apply` for Production Environment**:
    - Run `terraform plan` and `terraform apply` with `prod.tfvars` to deploy the VM with Apache in the production environment:
      ```bash
      terraform plan -var-file="prod.tfvars"
      terraform apply -var-file="prod.tfvars"
      ```
    - Confirm the deployment by typing `yes` when prompted.

---

### Verification

1. **Check the Azure Portal**:
    - Go to the [Azure Portal](https://portal.azure.com/) and navigate to the resource groups for each environment (`dev-resource-group` and `prod-resource-group`).
    - Verify that VMs have been created in each environment with the correct size (based on workspace).

2. **Verify Software Installation**:
    - Connect to each VM via SSH and confirm the installed software:
        - For dev, verify Nginx:
          ```bash
          ssh adminuser@<DEV_VM_PUBLIC_IP>
          systemctl status nginx
          ```
        - For prod, verify Apache:
          ```bash
          ssh adminuser@<PROD_VM_PUBLIC_IP>
          systemctl status apache2
          ```

3. **Confirm Dynamic Configuration**:
    - Verify that the VM size and software installation vary based on the workspace (dev or prod).

---