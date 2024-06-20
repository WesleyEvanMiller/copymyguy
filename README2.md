```
# Install curl if it is not already installed
apk add --no-cache curl ca-certificates

# Download the glibc APK packages from the sgerrand repository with curl and -k to skip certificate validation
curl -k -o /etc/apk/keys/sgerrand.rsa.pub https://alpine-pkgs.sgerrand.com/sgerrand.rsa.pub

# Download the specific version of glibc (for example, 2.28-r0) with curl and -k
curl -k -L -o glibc-2.28-r0.apk https://github.com/sgerrand/alpine-pkg-glibc/releases/download/2.28-r0/glibc-2.28-r0.apk
curl -k -L -o glibc-bin-2.28-r0.apk https://github.com/sgerrand/alpine-pkg-glibc/releases/download/2.28-r0/glibc-bin-2.28-r0.apk

# Install the downloaded packages
apk add --no-cache glibc-2.28-r0.apk glibc-bin-2.28-r0.apk

# Clean up the downloaded packages
rm -f glibc-2.28-r0.apk glibc-bin-2.28-r0.apk

# Verify the installation
/usr/glibc-compat/bin/ldd --version

```
```
$newModulePath = "D:\PowerShellModules"
if (-not (Test-Path -Path $newModulePath)) {
    New-Item -Path $newModulePath -ItemType Directory
}
```
```
$currentPSModulePath = [Environment]::GetEnvironmentVariable("PSModulePath", [EnvironmentVariableTarget]::Machine)
$newPSModulePath = "$currentPSModulePath;$newModulePath"
[Environment]::SetEnvironmentVariable("PSModulePath", $newPSModulePath, [EnvironmentVariableTarget]::Machine)
```

```
[Environment]::GetEnvironmentVariable("PSModulePath", [EnvironmentVariableTarget]::Machine)
```
```
Install-Module -Name Az.Automanage -Scope AllUsers
```
```
# Install Azure CLI using PowerShell
Invoke-WebRequest -Uri https://aka.ms/installazurecliwindows -OutFile .\AzureCLI.msi; Start-Process msiexec.exe -ArgumentList '/i AzureCLI.msi /quiet' -Wait; Remove-Item .\AzureCLI.msi
```
```
$acrName = "your_acr_name"
$resourceGroupName = "your_resource_group_name"
$acr = Get-AzContainerRegistry -ResourceGroupName $resourceGroupName -Name $acrName
$acrCredentials = Get-AzContainerRegistryCredential -ResourceGroupName $resourceGroupName -Name $acrName
```
```
$username = $acrCredentials.Username
$password = ($acrCredentials.Password | ConvertTo-SecureString -AsPlainText -Force)
docker login $acr.LoginServer -u $username -p $password
```

```
Certainly! Below is an updated version of the script that specifically compares the inbound and outbound security rules of two NSGs.

```python
import os
from azure.identity import DefaultAzureCredential
from azure.mgmt.network import NetworkManagementClient
from deepdiff import DeepDiff

# Function to get the NSG
def get_nsg(client, resource_group, nsg_name):
    return client.network_security_groups.get(resource_group_name=resource_group, network_security_group_name=nsg_name)

# Function to compare security rules
def compare_security_rules(rules1, rules2):
    diff = DeepDiff(rules1, rules2, ignore_order=True)
    return diff

def main():
    # Azure authentication
    credential = DefaultAzureCredential()
    subscription_id = os.environ["AZURE_SUBSCRIPTION_ID"]

    # Initialize NetworkManagementClient
    network_client = NetworkManagementClient(credential, subscription_id)

    # NSG details
    resource_group_1 = 'your_resource_group_1'
    nsg_name_1 = 'your_nsg_name_1'
    
    resource_group_2 = 'your_resource_group_2'
    nsg_name_2 = 'your_nsg_name_2'

    # Get NSGs
    nsg1 = get_nsg(network_client, resource_group_1, nsg_name_1)
    nsg2 = get_nsg(network_client, resource_group_2, nsg_name_2)

    # Extract inbound and outbound security rules
    inbound_rules_1 = [rule.as_dict() for rule in nsg1.security_rules if rule.direction == 'Inbound']
    outbound_rules_1 = [rule.as_dict() for rule in nsg1.security_rules if rule.direction == 'Outbound']
    inbound_rules_2 = [rule.as_dict() for rule in nsg2.security_rules if rule.direction == 'Inbound']
    outbound_rules_2 = [rule.as_dict() for rule in nsg2.security_rules if rule.direction == 'Outbound']

    # Compare inbound and outbound rules
    inbound_differences = compare_security_rules(inbound_rules_1, inbound_rules_2)
    outbound_differences = compare_security_rules(outbound_rules_1, outbound_rules_2)

    # Print the differences
    if inbound_differences:
        print("Differences in Inbound Security Rules:")
        for key, value in inbound_differences.items():
            print(f"{key}: {value}")
    else:
        print("The Inbound Security Rules are identical")

    if outbound_differences:
        print("Differences in Outbound Security Rules:")
        for key, value in outbound_differences.items():
            print(f"{key}: {value}")
    else:
        print("The Outbound Security Rules are identical")

if __name__ == "__main__":
    main()
```

### How to Use

1. **Set up your Azure environment:**
   Ensure you have set the `AZURE_SUBSCRIPTION_ID` environment variable with your Azure subscription ID.

   ```sh
   export AZURE_SUBSCRIPTION_ID="your_subscription_id"
   ```

2. **Provide your resource group and NSG names:**
   Replace `'your_resource_group_1'`, `'your_nsg_name_1'`, `'your_resource_group_2'`, and `'your_nsg_name_2'` with the actual names of your resource groups and Network Security Groups.

3. **Run the script:**
   Execute the script using Python.

   ```sh
   python compare_nsgs.py
   ```

This script focuses on comparing only the inbound and outbound security rules of the specified NSGs and prints out the differences between them.

```
pip install azure-identity==1.7.1 azure-mgmt-network==18.0.0 azure-core==1.23.1
```
```
pip uninstall azure-identity azure-mgmt-network azure-core
```
```
