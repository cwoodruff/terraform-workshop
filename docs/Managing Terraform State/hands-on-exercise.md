---
order: -1
icon: rocket
label: Hands On Exercise
meta:
title: "Hands On Exercise"
---
# Exercise: Configuring and Using Remote State in Azure

### Objective
Participants will configure their Terraform setup to use remote state stored in Azure Blob Storage, which allows for better collaboration and state management in team environments.

---

### Steps

#### Step 1: Create an Azure Storage Account and Blob Container

1. **Open Azure CLI or Azure Portal**:
    - You can use either the Azure CLI (recommended for automation) or the Azure Portal to create the necessary resources.

2. **Create a Resource Group (if not already created)**:
    - Replace `<your-resource-group>` and `<location>` with your desired resource group name and Azure region.
    - Using the Azure CLI:
      ```bash
      az group create --name <your-resource-group> --location <location>
      ```

3. **Create a Storage Account**:
    - Replace `<your-storage-account-name>` with a unique name for the storage account.
    - Using the Azure CLI:
      ```bash
      az storage account create \
        --name <your-storage-account-name> \
        --resource-group <your-resource-group> \
        --location <location> \
        --sku Standard_LRS
      ```
    - **Note**: Storage account names must be globally unique and can only contain lowercase letters and numbers.

4. **Create a Blob Container**:
    - Replace `<your-container-name>` with a name for the container, such as `tfstate`.
    - Using the Azure CLI:
      ```bash
      az storage container create \
        --name <your-container-name> \
        --account-name <your-storage-account-name>
      ```

5. **Get the Storage Account Key**:
    - You will need the storage account key to configure remote state in Terraform.
    - Using the Azure CLI:
      ```bash
      az storage account keys list \
        --resource-group <your-resource-group> \
        --account-name <your-storage-account-name> \
        --query "[0].value" --output tsv
      ```
    - Copy the storage account key for use in the Terraform configuration.

---

#### Step 2: Modify the Terraform Configuration to Add the Remote State Backend Block

1. **Open Your `main.tf` File**:
    - Open your Terraform configuration file, `main.tf`, in your code editor.

2. **Add the Remote State Backend Block**:
    - At the top of `main.tf`, add the `backend` configuration block under `terraform {}` to enable remote state storage in Azure.
    - Replace `<your-storage-account-name>`, `<your-container-name>`, `<your-resource-group>`, and `<your-state-file-name>` as necessary.
    - Example:
      ```hcl
      terraform {
        backend "azurerm" {
          resource_group_name  = "<your-resource-group>"
          storage_account_name = "<your-storage-account-name>"
          container_name       = "<your-container-name>"
          key                  = "<your-state-file-name>.tfstate"
        }
      }
      ```
    - The `key` value specifies the name of the state file in the Blob container (e.g., `terraform.tfstate`).

3. **Configure Environment Variables (Optional)**:
    - You can set the storage account key as an environment variable to avoid hardcoding sensitive information.
    - In your terminal, set the environment variable:
      ```bash
      export ARM_ACCESS_KEY="<your-storage-account-key>"
      ```

---

#### Step 3: Run `terraform init` to Initialize the Backend and Migrate the State

1. **Initialize Terraform**:
    - In the terminal, run the following command to initialize the backend and migrate the local state to Azure Blob Storage:
      ```bash
      terraform init
      ```
    - Terraform will prompt you to confirm the migration of your state file from local to remote storage.

2. **Confirm Migration**:
    - Review the prompt and type `yes` to allow Terraform to move the state file to Azure Blob Storage.
    - Once the initialization completes, you should see a message confirming that the backend has been successfully configured.

---

#### Step 4: Verify the State File in Azure Blob Storage

1. **Log in to the Azure Portal**:
    - Go to the [Azure Portal](https://portal.azure.com/) and navigate to the resource group where you created the storage account.

2. **Navigate to the Storage Account and Blob Container**:
    - Open the storage account you created.
    - In the left-hand menu, select **Containers** and then choose the container (e.g., `tfstate`) where you configured the remote state.

3. **Verify the State File**:
    - You should see a file with the name you specified in the `key` field (e.g., `terraform.tfstate`).
    - This file contains the Terraform state, which is now stored in Azure Blob Storage and can be accessed and locked remotely by Terraform.

---

### Verification

- **Confirm Remote State Initialization**:
    - Verify that `terraform init` completed successfully and that the state file has been moved to the specified Azure Blob Storage container.

- **Check the State File Contents** (optional):
    - You can download the `.tfstate` file from Azure Blob Storage to inspect its contents, ensuring it contains the correct state information.

- **Troubleshoot Issues**:
    - If there are issues with accessing the Blob Storage or state file, verify that you correctly entered the resource names and storage account key in the configuration.

---