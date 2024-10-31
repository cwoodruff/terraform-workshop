---
order: -2
icon: rocket
label: Bonus Hands On Exercise
meta:
title: "Bonus Hands On Exercise"
---
# Bonus Hands-On Exercise: Using Remote State with Workspaces

### Exercise Objective
Participants will extend their remote state setup by using **Terraform workspaces**. This will allow them to manage different environments (e.g., dev, staging, prod) using the same remote state backend in Azure Blob Storage.

---

### Prerequisites
- **Azure Storage Account and Blob Container**: Ensure that you have already set up Azure Blob Storage for remote state, as outlined in the previous exercise.
- **Terraform Backend Configured**: Make sure that your `main.tf` file includes the backend block pointing to Azure Blob Storage.

---

### Steps

#### Step 1: Modify the Backend Configuration to Use Workspaces

1. **Open Your `main.tf` File**:
    - Open your `main.tf` file where the Azure provider and backend configuration are defined.

2. **Update the Backend Block for Workspace Support**:
    - Modify the `key` parameter in the backend block to use the `terraform.workspace` interpolation syntax, which will dynamically set the state file path based on the current workspace.
    - Example:
      ```hcl
      terraform {
        backend "azurerm" {
          resource_group_name  = "<your-resource-group>"
          storage_account_name = "<your-storage-account-name>"
          container_name       = "<your-container-name>"
          key                  = "${terraform.workspace}/terraform.tfstate"
        }
      }
      ```
    - Here, `${terraform.workspace}` will dynamically create a separate state file for each workspace (e.g., `dev/terraform.tfstate`, `staging/terraform.tfstate`, etc.).

3. **Save the `main.tf` File**:
    - Save your changes in `main.tf`.

---

#### Step 2: Initialize the Backend and Workspaces

1. **Run `terraform init` to Reinitialize the Backend**:
    - In the terminal, run the following command to reinitialize the backend with the updated configuration:
      ```bash
      terraform init
      ```
    - Terraform will detect the change in backend configuration and ask for confirmation to initialize with the new settings.

2. **Confirm Backend Initialization**:
    - Follow the prompts in the terminal and confirm the initialization.

---

#### Step 3: Create and Switch Between Workspaces

1. **Create a New Workspace for the Development Environment**:
    - Use the following command to create a new workspace called `dev`:
      ```bash
      terraform workspace new dev
      ```
    - Terraform will switch to the `dev` workspace after creating it. This workspace will now have its own state file in Azure Blob Storage (`dev/terraform.tfstate`).

2. **Create a Workspace for Staging**:
    - Create another workspace called `staging`:
      ```bash
      terraform workspace new staging
      ```
    - Terraform will automatically switch to the `staging` workspace after creating it. The state file for this workspace will be stored in `staging/terraform.tfstate`.

3. **Create a Workspace for Production**:
    - Similarly, create a `prod` workspace for the production environment:
      ```bash
      terraform workspace new prod
      ```
    - The state file for this workspace will be stored in `prod/terraform.tfstate`.

4. **Switch Between Workspaces**:
    - You can switch between the workspaces using the `select` command:
      ```bash
      terraform workspace select dev
      ```
    - This command switches the current workspace back to `dev`, allowing you to manage resources specific to that environment.

---

#### Step 4: Apply the Configuration in Each Workspace

1. **Run `terraform apply` in the Dev Workspace**:
    - Make sure you are in the `dev` workspace by running:
      ```bash
      terraform workspace select dev
      ```
    - Run `terraform apply` to apply the configuration in the `dev` environment:
      ```bash
      terraform apply
      ```
    - Type `yes` when prompted to confirm.

2. **Repeat for Staging and Production Workspaces**:
    - Switch to the `staging` workspace and apply the configuration:
      ```bash
      terraform workspace select staging
      terraform apply
      ```
    - Switch to the `prod` workspace and apply the configuration:
      ```bash
      terraform workspace select prod
      terraform apply
      ```
    - Each environment will have its own state file stored in Azure Blob Storage under the respective workspace directory (e.g., `dev/terraform.tfstate`, `staging/terraform.tfstate`, `prod/terraform.tfstate`).

---

#### Step 5: Verify State Files in Azure Blob Storage

1. **Log in to the Azure Portal**:
    - Go to the [Azure Portal](https://portal.azure.com/) and navigate to the resource group containing the storage account.

2. **Navigate to the Storage Account and Container**:
    - Open the storage account.
    - In the left-hand menu, select **Containers** and then open the container where you stored the Terraform state files.

3. **Verify the State Files**:
    - You should see separate directories or state files for each workspace (e.g., `dev/terraform.tfstate`, `staging/terraform.tfstate`, and `prod/terraform.tfstate`).
    - Each state file will contain the specific resources applied in that workspace, allowing you to manage separate environments.

---

### Verification

- **Confirm Workspace Creation**:
    - Use `terraform workspace list` to view all created workspaces and verify that `dev`, `staging`, and `prod` are present.

- **Check Azure Blob Storage**:
    - Verify that each workspace has its own state file in Azure Blob Storage, confirming that remote state is correctly isolated by environment.

- **Troubleshoot Issues**:
    - If state files are not appearing as expected, ensure that the `key` parameter in the backend configuration uses `${terraform.workspace}` syntax correctly.

---