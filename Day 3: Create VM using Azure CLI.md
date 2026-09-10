# Day 3: Create VM Using Azure CLI

## Lab Overview
The **Nautilus DevOps** team is migrating workloads to Microsoft Azure. Since direct access to the Azure Portal is restricted, all infrastructure provisioning must be executed through the Azure CLI via the landing client host (`azure-client`).

---

## Task Requirements
* **VM Name:** `devops-vm`
* **Operating System Image:** `Ubuntu2204` (Ubuntu Server 22.04 LTS)
* **VM Size:** `Standard_B2s` (2 vCPUs, 4 GiB memory)
* **Admin Username:** `azureuser`
* **Authentication:** SSH public key authentication (`--generate-ssh-keys`)
* **OS Disk Type (Storage SKU):** `Standard_LRS`
* **OS Disk Size:** `30 GB`
* **Target State:** Running (`VM running`)

---

## Step 1: Identify the Target Resource Group

Before creating any compute resources, check the existing resource groups provisioned for this environment.

### Command
```bash
az group list -o table
```

### Explanation
* `az`: Invokes the Azure Command-Line Interface.
* `group`: Targets the resource group management module.
* `list`: Queries all resource groups within the active subscription.
* `-o table` (or `--output table`): Formats the output as a human-readable ASCII table instead of raw JSON.

### Sample Output
```text
Name                          Location    Status
----------------------------  ----------  ---------
kml_rg_main-b550cd8a48ca4a97  eastus      Succeeded
```

> **Note:** Take note of the resource group name from the output (e.g., `kml_rg_main-b550cd8a48ca4a97`). You must pass this exact name into the subsequent VM creation command.

---

## Step 2: Create and Provision the Virtual Machine

Run `az vm create` with all parameters required by the team specification.

### Command
```bash
az vm create \
  --resource-group kml_rg_main-b550cd8a48ca4a97 \
  --name devops-vm \
  --image Ubuntu2204 \
  --size Standard_B2s \
  --admin-username azureuser \
  --generate-ssh-keys \
  --storage-sku Standard_LRS \
  --os-disk-size-gb 30
```

### Parameter Breakdown & Explanation

| Parameter | Value | Description |
| :--- | :--- | :--- |
| `--resource-group` | `kml_rg_main-...` | Specifies the resource group identified in Step 1 where all associated resources (NIC, VNet, Public IP, NSG, Disks) will be allocated. |
| `--name` | `devops-vm` | Sets the identifier/hostname of the Virtual Machine. |
| `--image` | `Ubuntu2204` | Utilizes the canonical alias for Ubuntu Server 22.04 LTS Gen2 image from the Azure Marketplace. |
| `--size` | `Standard_B2s` | Configures the burstable B-series VM tier with 2 vCPUs and 4 GiB RAM. |
| `--admin-username` | `azureuser` | Creates the primary administrative user account on the Linux host. |
| `--generate-ssh-keys` | *(Flag)* | Automatically generates a new RSA public/private key pair in `~/.ssh/` if none exists, injecting the public key into `authorized_keys` for secure passwordless login. |
| `--storage-sku` | `Standard_LRS` | Selects Standard HDD locally redundant storage for the managed OS disk to optimize cost. |
| `--os-disk-size-gb` | `30` | Explicitly overrides the default disk size to allocate a 30 GB managed OS disk. |
```
{
  "fqdns": "",
  "id": "/subscriptions/f0c3bcdd-5ce2-4fa0-8cf3-41559747512b/resourceGroups/kml_rg_main-b550cd8a48ca4a97/providers/Microsoft.Compute/virtualMachines/devops-vm",
  "location": "eastus",
  "macAddress": "00-22-48-30-9D-34",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "20.231.19.101",
  "resourceGroup": "kml_rg_main-b550cd8a48ca4a97",
  "zones": ""
}
```
---

## Step 3: Verify VM Provisioning and Power State

Once the command finishes, verify that `devops-vm` is created and actively in the **running** state.

### Command
```bash
az vm show \
  --resource-group kml_rg_main-b550cd8a48ca4a97 \
  --name devops-vm \
  -d \
  --query "{Name:name, State:powerState, PublicIP:publicIps, Size:hardwareProfile.vmSize}" \
  -o table
```

### Expected Output
```text
Name       State       PublicIP        Size
---------  ----------  --------------  ------------
devops-vm  VM running  <PUBLIC_IP>     Standard_B2s
```

* `-d` (or `--show-details`): Fetches runtime instance details, including runtime power status and dynamic public IPs.
* `--query`: Evaluates a JMESPath expression to extract only the key status attributes.
