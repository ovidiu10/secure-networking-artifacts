# Secure Networking OpenHack Artifacts

You can find in this repository assets required for the Secure Networking [OpenHack](https://openhack.microsoft.com) to deploy the application required (you can find the source code for the application [here](https://github.com/Microsoft/YADA)).

## OpenHack Network Diagnostics (OHND) application

The Secure Networking OpenHack uses the OpenHack Network Diagnostics (OHND) as application throughout all of the exercises. The application's tier components can be deployed via ARM templates to Azure virtual machines.

For the application tier, the template needs the following parameters:

- VNet and subnet name
- SQL FQDN and credentials
- Optionally an Availability Zone

Here an example of how to deploy the application tier to an Azure Virtual machine:

```bash
# Bash script

# Variables
rg='your_resource_group'
location='your_azure_region'

vnet_name='your_vnet_name'
api_subnet_name='your_app_subnet'
vm_username='demouser'
vm_password='your_vm_password'
sql_server_fqdn='fully_qualified_domain_name_of_a_SQL_server'
sql_username='your_sql_server_username'
sql_password='your_sql_server_password'
api_template_uri='https://raw.githubusercontent.com/Microsoft-OpenHack/secure-networking-artifacts/main/deploy_api_to_vm.json'

# Create VM for API tier
echo "Creating API VM..."
az deployment group create -n appvm -g $rg --template-uri $api_template_uri \
    --parameters vmName=api \
                 adminUsername=$vm_username \
                 adminPassword=$vm_password \
                 virtualNetworkName=$vnet_name \
                 sqlServerFQDN=$sql_server_fqdn \
                 sqlServerUser=$sql_username \
                 "sqlServerPassword=$sql_password" \
                 subnetName=$api_subnet_name \
                 availabilityZone=1
```

Similarly, for the web tier you will need to provide the following parameters:

- VNet and subnet name
- URL to access the API
- Optionally an Availability Zone

Here an example of how to deploy the web tier to an Azure Virtual Machine:

```bash
# Variables
rg='your_resource_group'
location='your_azure_region'
vnet_name='your_vnet_name'
web_subnet_name='your_web_subnet'
vm_username='demouser'
vm_password='your_vm_password'
api_ip_address='1.2.3.4'
web_template_uri='https://raw.githubusercontent.com/Microsoft-OpenHack/secure-networking-artifacts/main/deploy_web_to_vm.json'

# Create VM for web tier
echo "Creating web VM..."
az deployment group create -n webvm -g $rg --template-uri $web_template_uri \
    --parameters vmName=web \
                 adminUsername=$vm_username \
                 adminPassword=$vm_password \
                 virtualNetworkName=$vnet_name \
                 "apiUrl=http://${api_ip_address}:8080" \
                 subnetName=$web_subnet_name \
                 availabilityZone=1
```
