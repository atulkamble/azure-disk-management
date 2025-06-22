# Azure Disk management

Azure Disk Management is a crucial part of Azure VM administration and storage architecture. Let’s organize this clearly for you. I’ll give you **practical code examples using Azure CLI, PowerShell, ARM Template, Bicep, and Terraform** for common Azure Disk operations.

---

## 📖 Azure Disk Management Scenarios:

1. Create a managed disk
2. Attach a disk to a VM
3. Detach a disk from a VM
4. Resize a disk
5. Delete a disk

---

## 📦 Azure CLI Practice Commands

> 💡 Make sure you’re logged in via `az login` and have set the desired subscription.

### 1️⃣ Create a Managed Disk

```bash
az disk create \
  --resource-group myResourceGroup \
  --name myDataDisk \
  --size-gb 100 \
  --sku Premium_LRS \
  --location eastus
```

---

### 2️⃣ Attach Disk to VM

```bash
az vm disk attach \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name myDataDisk
```

---

### 3️⃣ Detach Disk

```bash
az vm disk detach \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name myDataDisk
```

---

### 4️⃣ Resize Disk

```bash
az disk update \
  --resource-group myResourceGroup \
  --name myDataDisk \
  --size-gb 200
```

---

### 5️⃣ Delete Disk

```bash
az disk delete \
  --resource-group myResourceGroup \
  --name myDataDisk \
  --yes
```

---

## 📦 Azure PowerShell Practice Commands

### 1️⃣ Create a Managed Disk

```powershell
$diskConfig = New-AzDiskConfig -Location "EastUS" -CreateOption Empty -DiskSizeGB 100 -SkuName 'Premium_LRS'
New-AzDisk -ResourceGroupName "myResourceGroup" -DiskName "myDataDisk" -Disk $diskConfig
```

### 2️⃣ Attach Disk

```powershell
$vm = Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
$disk = Get-AzDisk -ResourceGroupName "myResourceGroup" -DiskName "myDataDisk"
$vm = Add-AzVMDataDisk -VM $vm -Name "myDataDisk" -CreateOption Attach -ManagedDiskId $disk.Id -Lun 1
Update-AzVM -ResourceGroupName "myResourceGroup" -VM $vm
```

---

## 📦 ARM Template Snippet for Managed Disk

```json
{
  "type": "Microsoft.Compute/disks",
  "apiVersion": "2023-04-02",
  "name": "myDataDisk",
  "location": "eastus",
  "sku": {
    "name": "Premium_LRS"
  },
  "properties": {
    "diskSizeGB": 100,
    "creationData": {
      "createOption": "Empty"
    }
  }
}
```

---

## 📦 Bicep Template Snippet

```bicep
resource dataDisk 'Microsoft.Compute/disks@2023-04-02' = {
  name: 'myDataDisk'
  location: 'eastus'
  sku: {
    name: 'Premium_LRS'
  }
  properties: {
    diskSizeGB: 100
    creationData: {
      createOption: 'Empty'
    }
  }
}
```

---

## 📦 Terraform Example for Managed Disk

```hcl
resource "azurerm_managed_disk" "data_disk" {
  name                 = "myDataDisk"
  location             = azurerm_resource_group.main.location
  resource_group_name  = azurerm_resource_group.main.name
  storage_account_type = "Premium_LRS"
  disk_size_gb         = 100
  create_option        = "Empty"
}
```

---

## 🎯 Bonus: Check All Disks in a Resource Group (Azure CLI)

```bash
az disk list --resource-group myResourceGroup --output table
```

---

## 📦 Disk Encryption Example (Azure CLI)

```bash
az disk encryption-set create \
  --resource-group myResourceGroup \
  --name myDiskEncryptionSet \
  --location eastus \
  --key-url <keyvault-key-identifier> \
  --source-vault <keyvault-id>
```

---

## ✅ Summary

| Task              | CLI Command         | PowerShell            | IaC (ARM/Bicep/Terraform) |
| :---------------- | :------------------ | :-------------------- | :------------------------ |
| Create Disk       | `az disk create`    | `New-AzDisk`          | ARM/Bicep/Terraform block |
| Attach Disk to VM | `az vm disk attach` | `Add-AzVMDataDisk`    | via VM definition         |
| Detach Disk       | `az vm disk detach` | `Remove-AzVMDataDisk` | via VM definition         |
| Resize Disk       | `az disk update`    | `Update-AzDisk`       | Update IaC block          |
| Delete Disk       | `az disk delete`    | `Remove-AzDisk`       | Remove IaC block          |

---

## 🔥 Pro Practice Suggestion

Build a simple Bash script or Terraform module that:

* Creates a disk
* Attaches to a test VM
* Increases its size
* Detaches it
* Deletes it

Would you like me to help you build that practice script too? ⚡

Excellent — yes, let’s build both:

* ✅ A **Bash script (using Azure CLI)**
* ✅ A **Terraform module**

Both will automate:

> Create Disk → Attach to VM → Resize → Detach → Delete

---

## 📜 1️⃣ Bash Script: `disk_operations.sh`

```bash
#!/bin/bash

# Exit on any error
set -e

# Variables
RESOURCE_GROUP="myResourceGroup"
LOCATION="eastus"
VM_NAME="myVM"
DISK_NAME="myDataDisk"
DISK_SIZE=50
NEW_DISK_SIZE=100

echo "Creating managed disk..."
az disk create --resource-group $RESOURCE_GROUP --name $DISK_NAME --size-gb $DISK_SIZE --location $LOCATION --sku Premium_LRS

echo "Attaching disk to VM..."
az vm disk attach --resource-group $RESOURCE_GROUP --vm-name $VM_NAME --name $DISK_NAME

echo "Increasing disk size to $NEW_DISK_SIZE GB..."
az disk update --resource-group $RESOURCE_GROUP --name $DISK_NAME --size-gb $NEW_DISK_SIZE

echo "Detaching disk from VM..."
az vm disk detach --resource-group $RESOURCE_GROUP --vm-name $VM_NAME --name $DISK_NAME

echo "Deleting the disk..."
az disk delete --resource-group $RESOURCE_GROUP --name $DISK_NAME --yes

echo "✔️ All operations completed successfully."
```

> 🔸 **Usage:**
> `bash disk_operations.sh`

---

## 📦 2️⃣ Terraform Module: `modules/disk/main.tf`

### 📁 Directory Layout

```
azure-disk-iac/
├── main.tf
├── variables.tf
├── outputs.tf
└── modules/
    └── disk/
        ├── main.tf
        ├── variables.tf
        └── outputs.tf
```

---

### 📦 modules/disk/main.tf

```hcl
resource "azurerm_managed_disk" "data_disk" {
  name                 = var.disk_name
  location             = var.location
  resource_group_name  = var.resource_group_name
  storage_account_type = "Premium_LRS"
  disk_size_gb         = var.disk_size
  create_option        = "Empty"
}
```

---

### 📦 modules/disk/variables.tf

```hcl
variable "resource_group_name" {}
variable "location" {}
variable "disk_name" {}
variable "disk_size" {}
```

---

### 📦 modules/disk/outputs.tf

```hcl
output "disk_id" {
  value = azurerm_managed_disk.data_disk.id
}
```

---

### 📦 main.tf (root)

```hcl
provider "azurerm" {
  features {}
}

module "disk" {
  source              = "./modules/disk"
  resource_group_name = "myResourceGroup"
  location            = "eastus"
  disk_name           = "myDataDisk"
  disk_size           = 50
}

resource "azurerm_virtual_machine_data_disk_attachment" "attach_disk" {
  managed_disk_id    = module.disk.disk_id
  virtual_machine_id = azurerm_windows_virtual_machine.vm.id
  lun                = 0
  caching            = "ReadWrite"
}

# Resize Disk
resource "azurerm_managed_disk" "resized_disk" {
  name                 = "myDataDisk"
  location             = "eastus"
  resource_group_name  = "myResourceGroup"
  storage_account_type = "Premium_LRS"
  disk_size_gb         = 100
  create_option        = "Empty"

  lifecycle {
    ignore_changes = [
      create_option
    ]
  }
}

# Detach (simply remove attachment resource or comment it)
```

---

### 📦 variables.tf (root)

```hcl
variable "resource_group_name" {
  default = "myResourceGroup"
}
variable "location" {
  default = "eastus"
}
```

---

### 📦 outputs.tf (root)

```hcl
output "disk_id" {
  value = module.disk.disk_id
}
```

---

## ✅ Summary:

| Task        | Bash Script (CLI)   | Terraform Module                               |
| :---------- | :------------------ | :--------------------------------------------- |
| Create Disk | `az disk create`    | `azurerm_managed_disk` resource                |
| Attach Disk | `az vm disk attach` | `azurerm_virtual_machine_data_disk_attachment` |
| Resize Disk | `az disk update`    | Update `disk_size_gb` in resource block        |
| Detach Disk | `az vm disk detach` | Remove or comment attachment block             |
| Delete Disk | `az disk delete`    | Remove disk resource block                     |

---
Perfect — let’s wrap this up cleanly into a **well-documented, beginner-to-advanced friendly Azure Disk Management practice lab repo plan**, complete with structured steps, a runnable Bash script, and a full Terraform setup.

---

# 📖 Azure Disk Management Practice Lab (Full Documentation)

---

## 📌 Lab Overview

This lab teaches you how to perform **Azure Managed Disk operations** using:

* **Azure CLI**
* **Azure PowerShell**
* **ARM Templates**
* **Bicep Templates**
* **Terraform Modules**
* **Automation scripts (Bash)**

---

## 📦 Lab Scenarios Covered

✅ Create a managed disk
✅ Attach it to an existing VM
✅ Resize the disk
✅ Detach the disk
✅ Delete the disk

---

## 📁 Recommended Repo Structure

```
azure-disk-lab/
├── README.md
├── bash/
│   └── disk_operations.sh
├── terraform/
│   ├── main.tf
│   ├── variables.tf
│   ├── outputs.tf
│   └── modules/
│       └── disk/
│           ├── main.tf
│           ├── variables.tf
│           └── outputs.tf
├── arm-template/
│   └── disk.json
├── bicep/
│   └── disk.bicep
├── powershell/
│   └── disk_operations.ps1
└── docs/
    └── disk-management-guide.md
```

---

## 📜 Bash Automation Script

**File:** `bash/disk_operations.sh`

> Automates disk creation, attachment, resizing, detachment, and deletion.

```bash
#!/bin/bash

set -e

RESOURCE_GROUP="myResourceGroup"
LOCATION="eastus"
VM_NAME="myVM"
DISK_NAME="myDataDisk"
DISK_SIZE=50
NEW_DISK_SIZE=100

az disk create --resource-group $RESOURCE_GROUP --name $DISK_NAME --size-gb $DISK_SIZE --location $LOCATION --sku Premium_LRS
az vm disk attach --resource-group $RESOURCE_GROUP --vm-name $VM_NAME --name $DISK_NAME
az disk update --resource-group $RESOURCE_GROUP --name $DISK_NAME --size-gb $NEW_DISK_SIZE
az vm disk detach --resource-group $RESOURCE_GROUP --vm-name $VM_NAME --name $DISK_NAME
az disk delete --resource-group $RESOURCE_GROUP --name $DISK_NAME --yes

echo "✔️ Disk management operations completed."
```

**Run:**

```bash
bash disk_operations.sh
```

---

## 📦 Terraform Complete Configuration

**1️⃣ modules/disk/main.tf**

```hcl
resource "azurerm_managed_disk" "data_disk" {
  name                 = var.disk_name
  location             = var.location
  resource_group_name  = var.resource_group_name
  storage_account_type = "Premium_LRS"
  disk_size_gb         = var.disk_size
  create_option        = "Empty"
}
```

**2️⃣ modules/disk/variables.tf**

```hcl
variable "resource_group_name" {}
variable "location" {}
variable "disk_name" {}
variable "disk_size" {}
```

**3️⃣ modules/disk/outputs.tf**

```hcl
output "disk_id" {
  value = azurerm_managed_disk.data_disk.id
}
```

**4️⃣ Root main.tf**

```hcl
provider "azurerm" {
  features {}
}

resource "azurerm_resource_group" "main" {
  name     = "myResourceGroup"
  location = "eastus"
}

resource "azurerm_windows_virtual_machine" "vm" {
  name                  = "myVM"
  location              = azurerm_resource_group.main.location
  resource_group_name   = azurerm_resource_group.main.name
  size                  = "Standard_DS1_v2"
  admin_username        = "azureuser"
  admin_password        = "P@ssword1234!"
  network_interface_ids = []

  os_disk {
    caching              = "ReadWrite"
    storage_account_type = "Premium_LRS"
  }
}

module "disk" {
  source              = "./modules/disk"
  resource_group_name = azurerm_resource_group.main.name
  location            = azurerm_resource_group.main.location
  disk_name           = "myDataDisk"
  disk_size           = 50
}

resource "azurerm_virtual_machine_data_disk_attachment" "attach_disk" {
  managed_disk_id    = module.disk.disk_id
  virtual_machine_id = azurerm_windows_virtual_machine.vm.id
  lun                = 0
  caching            = "ReadWrite"
}
```

**5️⃣ Root variables.tf**

```hcl
variable "location" {
  default = "eastus"
}
```

**6️⃣ Root outputs.tf**

```hcl
output "disk_id" {
  value = module.disk.disk_id
}
```

**Run Commands:**

```bash
terraform init
terraform plan
terraform apply
```

To destroy resources:

```bash
terraform destroy
```

---

## 📖 Documentation Steps (docs/disk-management-guide.md)

1️⃣ Create resource group and VM
2️⃣ Create a managed disk
3️⃣ Attach disk to VM
4️⃣ Resize disk
5️⃣ Detach disk
6️⃣ Delete disk

> Include Azure CLI, PowerShell, ARM, Bicep, Terraform, and Bash commands.

---

## ✅ Deliverables Summary

| Item              | Description                         |
| :---------------- | :---------------------------------- |
| Bash Script       | Full automation via Azure CLI       |
| Terraform Module  | Modular and reusable infrastructure |
| ARM Template      | JSON definition for managed disk    |
| Bicep Template    | ARM DSL equivalent                  |
| PowerShell Script | Azure PowerShell automation         |
| Documentation     | Detailed step-by-step usage guide   |



