---
order: -2
icon: rocket
label: Hands On Exercise
meta:
title: "Hands On Exercise"
---
# Exercise: Installing Terraform and Initializing a New Project

### Objective
Ensure that participants have installed Terraform on their machine, set up their development environment, and created a new Terraform project with a basic configuration.

---

### Steps

#### Step 1: Install Terraform on Your Respective OS

1. **Windows**:
    - Download the latest version of Terraform from the [official download page](https://www.terraform.io/downloads).
    - Extract the `.zip` file to a preferred folder (e.g., `C:\Terraform`).
    - Add the folder path (e.g., `C:\Terraform`) to the system PATH:
        - Open the Start Menu and search for **Environment Variables**.
        - Click **Edit the system environment variables**.
        - Under **System variables**, select **Path** and click **Edit**.
        - Click **New** and enter the path where `terraform.exe` is located.
        - Click **OK** to save.
    - Open a new Command Prompt or PowerShell window and run:
      ```bash
      terraform --version
      ```
    - Confirm that Terraform is correctly installed by checking for the version output.

2. **macOS**:
    - Open Terminal and install Terraform using **Homebrew**:
      ```bash
      brew tap hashicorp/tap
      brew install hashicorp/tap/terraform
      ```
    - Confirm the installation by running:
      ```bash
      terraform --version
      ```
    - Alternatively, download the `.zip` file from the Terraform website, extract it, and move `terraform` to `/usr/local/bin`.

3. **Linux**:
    - Download the latest Terraform `.zip` file from the [official download page](https://www.terraform.io/downloads).
    - Open a terminal and use `wget` or `curl` to download the `.zip` file:
      ```bash
      wget https://releases.hashicorp.com/terraform/<VERSION>/terraform_<VERSION>_linux_amd64.zip
      ```
    - Unzip the downloaded file:
      ```bash
      unzip terraform_<VERSION>_linux_amd64.zip
      ```
    - Move `terraform` to `/usr/local/bin`:
      ```bash
      sudo mv terraform /usr/local/bin/
      ```
    - Verify the installation by running:
      ```bash
      terraform --version
      ```

---

#### Step 2: Set Up Your Preferred Development Environment (VS Code or IntelliJ)

1. **Install Visual Studio Code (VS Code)** (if not already installed):
    - Download and install [VS Code](https://code.visualstudio.com/download).
    - Open VS Code and install the **Terraform extension**:
        - Go to the Extensions view (`Ctrl+Shift+X`).
        - Search for “Terraform” and install the HashiCorp Terraform extension.

2. **Install IntelliJ IDEA** (if preferred):
    - Download and install [IntelliJ IDEA](https://www.jetbrains.com/idea/download/).
    - Open IntelliJ and install the **Terraform and HCL Language Support** plugin:
        - Go to **File > Settings > Plugins** (or **Preferences > Plugins** on macOS).
        - Search for “Terraform” and install the plugin.

---

#### Step 3: Create a New Folder for the Terraform Project and Write a Basic `main.tf` File

1. **Create a New Folder for the Project**:
    - Open a terminal or file explorer and create a new directory where the Terraform project will reside.
    - For example, create a folder called `my-first-terraform-project`:
      ```bash
      mkdir my-first-terraform-project
      cd my-first-terraform-project
      ```

2. **Write a Basic `main.tf` File**:
    - Inside the new folder, create a file named `main.tf`.
    - Open `main.tf` in your preferred editor (VS Code or IntelliJ) and add a basic configuration.
    - Example `main.tf` for creating an Azure Resource Group:
      ```hcl
      provider "azurerm" {
        features {}
      }
 
      resource "azurerm_resource_group" "example" {
        name     = "myResourceGroup"
        location = "East US"
      }
      ```

---

#### Step 4: Run `terraform init` to Initialize the Project

1. **Initialize the Project**:
    - Open a terminal and navigate to the project directory (where `main.tf` is located).
    - Run the following command to initialize the project:
      ```bash
      terraform init
      ```
    - `terraform init` will download any necessary provider plugins (e.g., the Azure provider) and initialize the working directory.

2. **Review the Output**:
    - Check the terminal output of `terraform init`. You should see a success message indicating that the initialization is complete.
    - The output should look something like this:
      ```
      Terraform has been successfully initialized!
 
      You may now begin working with Terraform. Try running "terraform plan" to see
      any changes that are required for your infrastructure. All Terraform commands
      should now work.
      ```

---

### **Verification**

- **Confirm Terraform Installation**:
    - Run `terraform --version` to verify Terraform was installed correctly.

- **Confirm Project Initialization**:
    - Ensure that `terraform init` ran without errors and produced a success message.

- **Troubleshoot Issues (if any)**:
    - If any issues arise, such as PATH errors, incorrect installation steps, or problems with `terraform init`, reach out for assistance.

---
