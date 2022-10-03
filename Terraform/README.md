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
 
 
## To create your infrastructure
when you're in the Terraform folder, run: 
```bash
# 1: initialises the terraform state. 
terraform init
 
# 2: creates the infrastructure
terraform apply
 
# 3: grab our private key from the terraform output, uses JQ to find the field. 
# We then push this to a private key file, and change the permissions so ansible doesn't give out. 
terraform show -json | \ jq -r '.values.root_module.resources[].values | select(.private_key_pem) |.private_key_pem' \ > ~/.ssh/terraform_private_key.pem && sudo chmod 600 ~/.ssh/terraform_private_key.pem
 
# 4: logs in as root(superadmin), we want to replace or add the new addresses for eoinbrennan.com and the ubuntu box
sudo su
 
# 5: we take the content of /etc/hosts, and ignore eoinbrennan.com and ubuntu. 
cat /etc/hosts | grep -v "eoinbrennan.com" | grep -v ubuntu > /tmp/hosts
 
# We get the ip address from terraform
export AZURE_PUBLIC_IP=$(terraform output public_ip_address | tr -d '"')
 
#We echo the ip address and the url into the tmp file.
echo -e $AZURE_PUBLIC_IP eoinbrennan.com | tee -a /tmp/hosts
 
#We echo the same ip, and ubuntu into the tmp file.
echo -e $AZURE_PUBLIC_IP ubuntu | tee -a /tmp/hosts
 
#finally, we replace the old hosts file. 
cat /tmp/hosts | tee > /etc/hosts
 
``` 
 
To clear down the account, run: 
 
    terraform destroy
