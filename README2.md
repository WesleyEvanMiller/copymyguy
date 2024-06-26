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

# Function to print differences in a readable format
def print_differences(diff, direction):
    if diff:
        print(f"Differences in {direction} Security Rules:")
        for change_type, changes in diff.items():
            print(f"  {change_type}:")
            for change, details in changes.items():
                print(f"    {change}: {details}")
    else:
        print(f"The {direction} Security Rules are identical")

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
    print_differences(inbound_differences, "Inbound")
    print_differences(outbound_differences, "Outbound")

if __name__ == "__main__":
    main()

```

Understood. We need to ensure that the `generate_variables.py` script is correctly resolving variables and then use those resolved variables to render the Jinja2 template into the final `.tfvars` file.

Let's fix the scripts to ensure that the final `.tfvars` file is generated correctly:

### Python Script: `generate_variables.py`

This script will resolve derived variables using Jinja2 and save the resolved variables to a JSON file.

```python
import sys
import os
import yaml
import json
from jinja2 import Template

def parse_yaml_files(yaml_files):
    variables = {}
    for filepath in yaml_files:
        if filepath.endswith('.yaml') or filepath.endswith('.yml'):
            with open(filepath, 'r') as file:
                yaml_content = yaml.safe_load(file)
                if isinstance(yaml_content, dict):
                    for key, value in yaml_content.items():
                        if key in variables:
                            print(f"Warning: Duplicate key {key} found in {filepath}. Using the latest value.")
                        variables[key] = value
    return variables

def resolve_derived_variables(variables):
    resolved_variables = {}
    for key, value in variables.items():
        if isinstance(value, str):
            try:
                template = Template(value)
                resolved_value = template.render(variables)
                resolved_variables[key] = resolved_value
            except Exception as e:
                print(f"Error resolving variable {key}: {e}")
                resolved_variables[key] = value  # Keep the unresolved value for now
        else:
            resolved_variables[key] = value
    return resolved_variables

def generate_variables(variables, output_file):
    with open(output_file, 'w') as f:
        json.dump(variables, f, indent=4)

if __name__ == "__main__":
    if len(sys.argv) < 3:
        print("Usage: python generate_variables.py <output_json_file> <yaml_file_1> <yaml_file_2> ...")
        sys.exit(1)

    output_json_file = sys.argv[1]
    yaml_files = sys.argv[2:]

    variables = parse_yaml_files(yaml_files)
    resolved_variables = resolve_derived_variables(variables)
    generate_variables(resolved_variables, output_json_file)
    print(f"Generated {output_json_file} with variables from {yaml_files}")
```

### Python Script: `render_jinja2.py`

This script will render Jinja2 templates using the variables from the JSON file and write the output to the correct file names, including rendering `vars.tfvars.j2` to `vars.tfvars`.

```python
import sys
import os
import json
from jinja2 import Environment, FileSystemLoader

def to_json(value):
    return json.dumps(value)

def render_template(template_dir, template_file, variables):
    env = Environment(loader=FileSystemLoader(template_dir))
    env.filters['to_json'] = to_json  # Add the custom to_json filter
    template = env.get_template(template_file)
    rendered_content = template.render(variables)
    return rendered_content

def load_variables(json_file):
    with open(json_file, 'r') as f:
        try:
            variables = json.load(f)
        except json.JSONDecodeError as e:
            print(f"Error loading JSON file {json_file}: {e}")
            variables = {}
    return variables

if __name__ == "__main__":
    if len(sys.argv) != 4:
        print("Usage: python render_jinja2.py <template_directory> <template_file> <variables_json_file>")
        sys.exit(1)

    template_directory = sys.argv[1]
    template_file = sys.argv[2]
    json_file = sys.argv[3]

    variables = load_variables(json_file)
    rendered = render_template(template_directory, template_file, variables)
    
    # Correctly name the output file
    if template_file.endswith('.tfvars.j2'):
        output_file_name = template_file.replace('.tfvars.j2', '.tfvars')
    else:
        output_file_name = template_file[:-3]  # Remove the .j2 extension
    
    output_file_path = os.path.join(template_directory, output_file_name)
    
    with open(output_file_path, 'w') as output_file:
        output_file.write(rendered)

    print(f'Rendered {template_file} to {output_file_path}')
```

### Updated Bash Script: `render_all_templates.sh`

Ensure the Bash script correctly orchestrates the process:

```bash
#!/bin/bash

# Directory containing the Jinja2 templates
TEMPLATE_DIR="path/to/your/templates"
# Path to the variables JSON file to be generated
VARIABLES_FILE="path/to/your/variables.json"
# List of YAML files
YAML_FILES=("path/to/your/yaml/file1.yaml" "path/to/your/yaml/file2.yaml")

# Generate the variables file from the YAML files
python generate_variables.py "$VARIABLES_FILE" "${YAML_FILES[@]}"

# Iterate through all .tf.j2 and .tfvars.j2 files in the directory
for template_file in "$TEMPLATE_DIR"/*.{tf.j2,tfvars.j2}; do
    # Check if the file exists (in case no files match the pattern)
    if [[ -e "$template_file" ]]; then
        # Extract the base name of the file
        base_name=$(basename "$template_file")
        
        # Render the template using the Python script
        python render_jinja2.py "$TEMPLATE_DIR" "$base_name" "$VARIABLES_FILE"
    fi
done
```

### Steps to Execute

1. Save the updated Python scripts (`generate_variables.py` and `render_jinja2.py`) in your working directory.
2. Save the updated Bash script (`render_all_templates.sh`) in your working directory.
3. Modify the paths in the Bash script to match your actual template directory and YAML files.
4. Make the Bash script executable if not already done:

```sh
chmod +x render_all_templates.sh
```

5. Run the Bash script:

```sh
./render_all_templates.sh
```

### Explanation

1. **Python Script (`generate_variables.py`)**:
    - Reads the specified YAML files.
    - Extracts key-value pairs from the YAML files.
    - Resolves derived variables using Jinja2 while handling exceptions gracefully.
    - Writes the resolved key-value pairs to a JSON file.
    
2. **Python Script (`render_jinja2.py`)**:
    - Loads the variables from the generated JSON file.
    - Renders the Jinja2 template using the variables from the JSON file.
    - Writes the output to the correct file by removing the `.j2` extension from the template file name and ensuring `vars.tfvars.j2` is rendered to `vars.tfvars`.
    
3. **Bash Script (`render_all_templates.sh`)**:
    - First calls the `generate_variables.py` script to generate the JSON file with variables using the specified YAML files.
    - Then iterates through all `.tf.j2` and `.tfvars.j2` files in the template directory.
    - Calls the `render_jinja2.py` script to render each template using the generated JSON file with variables.

This setup ensures that the variables from the specified YAML files, including derived variables, are used to render both the `.tf` and `.tfvars` Jinja2 templates, and writes the rendered content to the appropriate files, specifically ensuring `vars.tfvars.j2` is rendered to `vars.tfvars`.
---------
To perform a manual update of the `capacity_reservation_group_id` for an AKS cluster without replacing the cluster, you can follow these steps:

1. **Manually Update the AKS Cluster**:
   - Use the Azure portal, Azure CLI, or Azure PowerShell to manually update the `capacity_reservation_group_id` for your AKS cluster.
   
2. **Import the Updated Resource into Terraform**:
   - After making the manual update, you need to import the updated AKS cluster configuration back into your Terraform state.

### Step-by-Step Guide

#### Step 1: Manually Update the AKS Cluster

**Using Azure CLI:**

1. Update the `capacity_reservation_group_id` for your AKS cluster:
   ```sh
   az aks update \
     --resource-group <your_resource_group> \
     --name <your_aks_cluster_name> \
     --capacity-reservation-group-id <new_capacity_reservation_group_id>
   ```

**Using Azure Portal:**

1. Navigate to your AKS cluster in the Azure portal.
2. Go to the "Configuration" or similar section where you can update the capacity reservation group.
3. Update the `capacity_reservation_group_id` with the new value.

#### Step 2: Import the Updated Resource into Terraform

1. **Get the Resource ID**:
   - You need the full resource ID of your AKS cluster to import it into Terraform. You can find this in the Azure portal or by using the Azure CLI:
     ```sh
     az aks show --resource-group <your_resource_group> --name <your_aks_cluster_name> --query id
     ```

2. **Import the AKS Cluster Resource**:
   - Use the `terraform import` command to import the AKS cluster into your Terraform state:
     ```sh
     terraform import azurerm_kubernetes_cluster.example <aks_cluster_resource_id>
     ```

3. **Update the Terraform Configuration**:
   - Ensure your Terraform configuration file (`main.tf` or similar) includes the `capacity_reservation_group_id` attribute with the new value:
     ```hcl
     resource "azurerm_kubernetes_cluster" "example" {
       name                = "example-aks"
       location            = azurerm_resource_group.example.location
       resource_group_name = azurerm_resource_group.example.name
       dns_prefix          = "exampleaks"
       
       default_node_pool {
         name       = "default"
         node_count = 1
         vm_size    = "Standard_DS2_v2"
       }
       
       identity {
         type = "SystemAssigned"
       }
       
       capacity_reservation_group_id = "<new_capacity_reservation_group_id>"
     }
     ```

4. **Run Terraform Plan and Apply**:
   - Run `terraform plan` to ensure that Terraform recognizes the manually updated state and does not attempt to replace the resource.
   - If the plan looks good, run `terraform apply` to apply any additional changes or just confirm the import.

```sh
terraform plan
terraform apply
```

By following these steps, you can manually update the `capacity_reservation_group_id` for your AKS cluster and import the updated configuration into Terraform without triggering a resource replacement. This approach ensures that the cluster remains intact and operational throughout the process.
