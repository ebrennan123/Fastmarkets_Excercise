## Terraform and Azure
 
The purpose is to create the following resources 
 - Virtual Network
 - Subnet
 - Public ID Address
 - Network Security Group with SSH inbound rule
 - Virtual Network Interface Card
 -  Storage Account for Boot Diagnostics
 - SSH Key
 - Virtual Machine
 
## How to run 
 
 *Prerequisites*
- Create a Microsoft Azure account which can be found [here](https://portal.azure.com/#home) 
- Install terraform 
	- `brew install terraform`
- Configure the Azure account with Terraform using the Azure CLI tool, this is to create a user that Terraform can run as. 
	- az account set --subscription "**********"
	- az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/<SUBSCRIPTION_ID>"
- Export the necessary environments variables (or save them in your ~/.zshrc || ~/.bashrc
    - export ARM_CLIENT_ID=**********
    - export ARM_CLIENT_SECRET=**********
    - export ARM_SUBSCRIPTION_ID=**********
    - export ARM_TENANT_ID=**********
 
Within Azure the Virtual Machine require both storage and networking. The virtual machine these configuration files create runs a simple Ubuntu Image. 
 
 
To create your infrastructure, run:
 
    terraform init
 
    terraform apply
    
    terraform show -json | \ jq -r '.values.root_module.resources[].values | select(.private_key_pem) |.private_key_pem' \ > ~/.ssh/terraform_private_key.pem
 
To clear down the account, run: 
 
    terraform destroy
