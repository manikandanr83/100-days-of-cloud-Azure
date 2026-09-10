# Day 3: Create VM using Azure CLI

The Nautilus DevOps team is in the process of migrating some of their workloads to Azure. One of the tasks involves creating a new Virtual Machine (VM) using the Azure CLI. The team does not have access to the Azure portal but can manage Azure resources via the azure-client host (the landing host for this lab).

1) Create a new Azure Virtual Machine named `devops-vm` using the Azure CLI.

2) Use the `Ubuntu2204` image and set the VM size to `Standard_B2s`.

3) Make sure the admin username is set to `azureuser` and SSH keys are generated for secure access.

4) Use `Standard_LRS` storage account, disk size must be `30GB` and ensure the VM `devops-vm` is in the running state after creation.


Step 1 — Check existing Resource Groups

```
az group list -o table
```
az group list -o table is an Azure CLI command used to display all resource groups in your active Azure subscription formatted as a readable text table.

az: Calls the Azure Command-Line Interface.
group: Specifies the resource group management module.
list: Instructs the CLI to retrieve every resource group in the currently active subscription.
-o table: Short for --output table. Formats the returned data as a clean ASCII table rather than the default, verbose JSON format.

Running this command typically outputs a table with:
Name: The identifier of each resource group.
Location: The Azure region hosting the group (e.g., eastus, westeurope).
Status: Provisioning state (typically Succeeded).

Output Columns
Name                          Location    Status
----------------------------  ----------  ---------
kml_rg_main-b550cd8a48ca4a97  eastus      Succeeded

Step 1 — Create the Virtual Machine

```
az vm create \
  --resource-group devops-rg \  # Resource group must be changed based on step 1 result
  --name devops-vm \
  --image Ubuntu2204 \
  --size Standard_B2s \
  --admin-username azureuser \
  --generate-ssh-keys \
  --storage-sku Standard_LRS \
  --os-disk-size-gb 30
```
