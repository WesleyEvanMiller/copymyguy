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


```
import os
from jinja2 import Environment, FileSystemLoader

def render_templates(template_dir, output_dir, context):
    env = Environment(loader=FileSystemLoader(template_dir))
    
    for template_name in os.listdir(template_dir):
        if template_name.endswith('.j2'):
            template = env.get_template(template_name)
            rendered_content = template.render(context)
            
            output_file_path = os.path.join(output_dir, template_name[:-3])  # Remove .j2 extension
            with open(output_file_path, 'w') as output_file:
                output_file.write(rendered_content)
            print(f"Rendered {template_name} to {output_file_path}")

if __name__ == "__main__":
    import sys
    if len(sys.argv) < 3:
        print("Usage: python render_templates.py <template-dir> <output-dir>")
        sys.exit(1)

    template_dir = sys.argv[1]
    output_dir = sys.argv[2]
    context = {}  # Add any necessary context variables here
    
    render_templates(template_dir, output_dir, context)
```


To render all Jinja2 (`.j2`) files in a directory, you'll need a Python script that uses the Jinja2 library. Here's a script that will iterate through all `.j2` files in a specified directory, render them, and save the rendered output to a new file.

First, ensure you have the Jinja2 library installed:
```sh
pip install jinja2
```

Here's the Python script:

```python
import os
from jinja2 import Environment, FileSystemLoader

def render_templates(directory):
    # Set up the Jinja2 environment
    env = Environment(loader=FileSystemLoader(directory))
    
    # Iterate through all .j2 files in the directory
    for filename in os.listdir(directory):
        if filename.endswith('.j2'):
            # Get the template
            template = env.get_template(filename)
            
            # Render the template
            rendered_content = template.render()
            
            # Save the rendered content to a new file (without the .j2 extension)
            output_filename = os.path.join(directory, filename[:-3])
            with open(output_filename, 'w') as output_file:
                output_file.write(rendered_content)
                
            print(f'Rendered {filename} to {output_filename}')

# Specify the directory containing the .j2 files
template_directory = 'path/to/your/templates'
render_templates(template_directory)
```

Replace `'path/to/your/templates'` with the path to the directory containing your `.j2` files.

This script does the following:
1. Sets up the Jinja2 environment with the specified directory as the template loader.
2. Iterates through all files in the directory.
3. Checks if a file ends with `.j2`.
4. Renders the template.
5. Saves the rendered content to a new file with the same name but without the `.j2` extension.
6. Prints a message indicating the rendering of each file.

Feel free to modify the script according to your specific needs, such as passing variables to the templates if needed.


------------------------------------------

The error `no filter named 'to_json'` indicates that Jinja2 is trying to use a filter called `to_json`, but it is not available by default. You need to add custom filters to your Jinja2 environment to support this functionality.

To resolve this, you can define a custom `to_json` filter in your Python script. Here's how you can modify the `render_jinja2.py` script to include a custom `to_json` filter:

### Updated Python Script: `render_jinja2.py`

```python
import sys
from jinja2 import Environment, FileSystemLoader
import os
import json

def to_json(value):
    return json.dumps(value)

def render_template(template_dir, template_file):
    env = Environment(loader=FileSystemLoader(template_dir))
    env.filters['to_json'] = to_json  # Add the custom to_json filter
    template = env.get_template(template_file)
    rendered_content = template.render()
    return rendered_content

if __name__ == "__main__":
    if len(sys.argv) != 3:
        print("Usage: python render_jinja2.py <template_directory> <template_file>")
        sys.exit(1)

    template_directory = sys.argv[1]
    template_file = sys.argv[2]

    rendered = render_template(template_directory, template_file)
    output_file_path = os.path.join(template_directory, template_file[:-3])
    
    with open(output_file_path, 'w') as output_file:
        output_file.write(rendered)

    print(f'Rendered {template_file} to {output_file_path}')
```

### Bash Script: `render_all_jinja2.sh`

The Bash script remains the same:

```bash
#!/bin/bash

# Directory containing the .j2 files
TEMPLATE_DIR="path/to/your/templates"

# Iterate through all .j2 files in the directory
for template_file in "$TEMPLATE_DIR"/*.j2; do
    # Extract the base name of the file
    base_name=$(basename "$template_file")
    
    # Render the template using the Python script
    python render_jinja2.py "$TEMPLATE_DIR" "$base_name"
done
```

### Steps to Execute

1. Save the updated Python script (`render_jinja2.py`) in your working directory.
2. Save the Bash script (`render_all_jinja2.sh`) in your working directory.
3. Make the Bash script executable if not already done:

```sh
chmod +x render_all_jinja2.sh
```

4. Run the Bash script:

```sh
./render_all_jinja2.sh
```

This modification ensures that the `to_json` filter is available in the Jinja2 environment, allowing you to use it in your templates.


To include `tfvars` (Terraform variables) in the Jinja2 render script, you'll need to parse the `tfvars` file and pass the variables to the template rendering function. Here's how you can modify the Python script to include this functionality.

### Updated Python Script: `render_jinja2.py`

```python
import sys
import os
import json
from jinja2 import Environment, FileSystemLoader

def to_json(value):
    return json.dumps(value)

def parse_tfvars(tfvars_file):
    variables = {}
    with open(tfvars_file) as f:
        for line in f:
            if '=' in line:
                key, value = line.split('=', 1)
                key = key.strip()
                value = value.strip().strip('"')
                variables[key] = value
    return variables

def render_template(template_dir, template_file, tfvars_file):
    env = Environment(loader=FileSystemLoader(template_dir))
    env.filters['to_json'] = to_json  # Add the custom to_json filter

    variables = parse_tfvars(tfvars_file)
    template = env.get_template(template_file)
    rendered_content = template.render(variables)
    return rendered_content

if __name__ == "__main__":
    if len(sys.argv) != 4:
        print("Usage: python render_jinja2.py <template_directory> <template_file> <tfvars_file>")
        sys.exit(1)

    template_directory = sys.argv[1]
    template_file = sys.argv[2]
    tfvars_file = sys.argv[3]

    rendered = render_template(template_directory, template_file, tfvars_file)
    output_file_path = os.path.join(template_directory, template_file[:-3])
    
    with open(output_file_path, 'w') as output_file:
        output_file.write(rendered)

    print(f'Rendered {template_file} to {output_file_path}')
```

### Updated Bash Script: `render_all_jinja2.sh`

Update the Bash script to include the `tfvars` file as an argument:

```bash
#!/bin/bash

# Directory containing the .j2 files
TEMPLATE_DIR="path/to/your/templates"
# Path to the tfvars file
TFVARS_FILE="path/to/your/variables.tfvars"

# Iterate through all .j2 files in the directory
for template_file in "$TEMPLATE_DIR"/*.j2; do
    # Extract the base name of the file
    base_name=$(basename "$template_file")
    
    # Render the template using the Python script
    python render_jinja2.py "$TEMPLATE_DIR" "$base_name" "$TFVARS_FILE"
done
```

### Steps to Execute

1. Save the updated Python script (`render_jinja2.py`) in your working directory.
2. Save the updated Bash script (`render_all_jinja2.sh`) in your working directory.
3. Make the Bash script executable if not already done:

```sh
chmod +x render_all_jinja2.sh
```

4. Run the Bash script:

```sh
./render_all_jinja2.sh
```

In this update, the Python script parses the `tfvars` file to extract the variables and pass them to the Jinja2 template during rendering. The Bash script calls the Python script with the appropriate arguments, including the path to the `tfvars` file.
