## Terraform and Azure

> *Prerequisites*
> So before starting this example I created a Microsoft Azure account which can be found [here](https://portal.azure.com/#home) 
>I installed Terraform using home-brew on my Mac which gave me accesses to its resources
>Finally I configured my Azure account with Terraform using the Azure CLI tool. I set my service principles as per my Azure credentials and I was ready to use Terraform to create infrastructure.

Within Azure the Virtual Machine require both storage and networking. The virtual machine these configuration files create runs a simple Ubuntu Image. 

To create the virtual machine we initialise terraform using the command: *terraform init*

We can then run *terraform apply* to create our VM using the configuration files.
