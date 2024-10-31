---
order: -2
icon: rocket
label: Bonus Hands On Exercise
meta:
title: "Bonus Hands On Exercise"
---
# Bonus Hands-On Exercise: Creating an Azure Load Balancer for High Availability

### Exercise Objective
In this bonus exercise, participants will create an **Azure Load Balancer** and configure it to distribute traffic across two web servers, ensuring high availability and redundancy.

---

### Steps

#### Step 1: Define the Azure Provider and Resource Group in `main.tf`

1. **Create a New Project Directory**:
    - Open a terminal or command prompt.
    - Create a new directory for the project and navigate into it:
      ```bash
      mkdir load-balancer-project
      cd load-balancer-project
      ```

2. **Create a `main.tf` File**:
    - Open your preferred code editor and create a file named `main.tf` in the `load-balancer-project` directory.

3. **Add the Azure Provider Configuration**:
    - Add the following code to configure the Azure provider:
      ```hcl
      provider "azurerm" {
        features {}
      }
      ```

4. **Define a Resource Group**:
    - Add a resource block for the Resource Group that will contain all resources for the load balancer setup:
      ```hcl
      resource "azurerm_resource_group" "example" {
        name     = "loadbalancer-rg"
        location = "East US"
      }
      ```

---

#### Step 2: Define a Virtual Network and Subnet

1. **Add a Virtual Network and Subnet**:
    - In `main.tf`, define a Virtual Network and a Subnet for the load balancer and VMs to reside in:
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

---

#### Step 3: Define Network Interfaces and Virtual Machines

1. **Define Network Interfaces for Each VM**:
    - Create two network interfaces, one for each VM, and associate them with the subnet:
      ```hcl
      resource "azurerm_network_interface" "vm1_nic" {
        name                = "vm1-nic"
        location            = azurerm_resource_group.example.location
        resource_group_name = azurerm_resource_group.example.name
 
        ip_configuration {
          name                          = "internal"
          subnet_id                     = azurerm_subnet.example.id
          private_ip_address_allocation = "Dynamic"
        }
      }
 
      resource "azurerm_network_interface" "vm2_nic" {
        name                = "vm2-nic"
        location            = azurerm_resource_group.example.location
        resource_group_name = azurerm_resource_group.example.name
 
        ip_configuration {
          name                          = "internal"
          subnet_id                     = azurerm_subnet.example.id
          private_ip_address_allocation = "Dynamic"
        }
      }
      ```

2. **Define Two Virtual Machines**:
    - Create two virtual machines, `vm1` and `vm2`, that will serve as backend web servers.
    - Example:
      ```hcl
      resource "azurerm_virtual_machine" "vm1" {
        name                  = "vm1"
        location              = azurerm_resource_group.example.location
        resource_group_name   = azurerm_resource_group.example.name
        network_interface_ids = [azurerm_network_interface.vm1_nic.id]
        vm_size               = "Standard_DS1_v2"
 
        storage_image_reference {
          publisher = "Canonical"
          offer     = "UbuntuServer"
          sku       = "18.04-LTS"
          version   = "latest"
        }
 
        os_profile {
          computer_name  = "vm1"
          admin_username = "adminuser"
          admin_password = "P@ssw0rd123!"
        }
 
        os_profile_linux_config {
          disable_password_authentication = false
        }
      }
 
      resource "azurerm_virtual_machine" "vm2" {
        name                  = "vm2"
        location              = azurerm_resource_group.example.location
        resource_group_name   = azurerm_resource_group.example.name
        network_interface_ids = [azurerm_network_interface.vm2_nic.id]
        vm_size               = "Standard_DS1_v2"
 
        storage_image_reference {
          publisher = "Canonical"
          offer     = "UbuntuServer"
          sku       = "18.04-LTS"
          version   = "latest"
        }
 
        os_profile {
          computer_name  = "vm2"
          admin_username = "adminuser"
          admin_password = "P@ssw0rd123!"
        }
 
        os_profile_linux_config {
          disable_password_authentication = false
        }
      }
      ```

---

#### Step 4: Create an Azure Load Balancer and Backend Pool

1. **Define a Public IP Address for the Load Balancer**:
    - Add a public IP resource for the load balancer:
      ```hcl
      resource "azurerm_public_ip" "lb_public_ip" {
        name                = "lb-public-ip"
        location            = azurerm_resource_group.example.location
        resource_group_name = azurerm_resource_group.example.name
        allocation_method   = "Static"
      }
      ```

2. **Create the Load Balancer**:
    - Define the Azure Load Balancer, associating it with the public IP:
      ```hcl
      resource "azurerm_lb" "example" {
        name                = "example-lb"
        location            = azurerm_resource_group.example.location
        resource_group_name = azurerm_resource_group.example.name
 
        frontend_ip_configuration {
          name                 = "lb-frontend"
          public_ip_address_id = azurerm_public_ip.lb_public_ip.id
        }
      }
      ```

3. **Define the Backend Pool for the VMs**:
    - Associate the backend VMs with the load balancer using a backend address pool:
      ```hcl
      resource "azurerm_lb_backend_address_pool" "example" {
        loadbalancer_id = azurerm_lb.example.id
        name            = "backend-pool"
      }
      ```

---

#### Step 5: Add Load Balancer Rules and Health Probes

1. **Create a Health Probe**:
    - Define a health probe to check the status of the VMs:
      ```hcl
      resource "azurerm_lb_probe" "example" {
        loadbalancer_id = azurerm_lb.example.id
        name            = "http-probe"
        protocol        = "Tcp"
        port            = 80
      }
      ```

2. **Add a Load Balancer Rule**:
    - Define a rule to distribute traffic on port 80 across the backend VMs:
      ```hcl
      resource "azurerm_lb_rule" "example" {
        resource_group_name            = azurerm_resource_group.example.name
        loadbalancer_id                = azurerm_lb.example.id
        name                           = "http-rule"
        protocol                       = "Tcp"
        frontend_port                  = 80
        backend_port                   = 80
        frontend_ip_configuration_name = "lb-frontend"
        backend_address_pool_id        = azurerm_lb_backend_address_pool.example.id
        probe_id                       = azurerm_lb_probe.example.id
      }
      ```

---

#### Step 6: Run Terraform Commands

1. **Initialize the Project**:
    - In the terminal, navigate to the project directory and run:
      ```bash
      terraform init
      ```

2. **Run `terraform plan`**:
    - Use `terraform plan` to preview the resources to be created:
      ```bash
      terraform plan
      ```

3. **Run `terraform apply`**:
    - Apply the configuration to provision the resources:
      ```bash
      terraform apply
      ```
    - Type `yes` when prompted.

---

#### Step 7: Verify the Load Balancer and VMs in Azure Portal

1. **Log in to the Azure Portal**:
    - Go to the [Azure Portal](https://portal.azure.com/).

2. **Navigate to the Resource Group**:
    - Locate the Resource Group `loadbalancer-rg`.

3. **Verify the Load Balancer and Public IP**:
    - Check that the Load Balancer and its associated public IP are created.
    - You can test the setup by accessing the public IP in a web browser or using a tool like `curl`.

4. **Verify the VMs in the Backend Pool**:
    - Ensure that both VMs are associated with the load balancer's backend pool for high availability.

---