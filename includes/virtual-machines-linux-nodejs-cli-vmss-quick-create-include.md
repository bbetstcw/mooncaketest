## Prerequisites

If you haven't already, you can get an [Azure subscription trial](https://www.azure.cn/pricing/1rmb-trial/) and the [Azure CLI 1.0](../articles/cli-install-nodejs.md) [connected to your Azure account](../articles/xplat-cli-connect.md). Make sure the Azure CLI is in Resource Manager mode as follows:

```azurecli
azure config mode arm
```

## Create the scale set

Now create your scale set using the `azure vmss quick-create` command. The following example creates a Linux scale set named `myVMSS` with five VM instances in the resource group named `myResourceGroup`:

```azurecli
azure vmss quick-create -n myVMSS -g myResourceGroup -l chinanorth \
    -u ops -p P@ssw0rd! \
    -C 5 -Q Canonical:UbuntuServer:16.04-LTS:latest
```

The following example creates a Windows scale set with the same configuration:

```azurecli
azure vmss quick-create -n myVMSS -g myResourceGroup -l chinanorth \
    -u ops -p P@ssw0rd! \
    -C 5 -Q MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest
```

If you want to customize the location or image-urn, look into the commands `azure location list` and `azure vm image {list-publishers|list-offers|list-skus|list|show}`.

This command returns once the scale set is done deploying. At this point, the scale set has a load balancer with NAT rules mapping port 50,000+i on the load balancer to port 22 on VM i. Thus, once we figure out the FQDN of the load balancer, we can connect via ssh to our VMs:

```bash
# (if you decide to run this as a script, please invoke using bash)

# list load balancers in the resource group we created
#
# generic syntax:
# azure network lb list -g RESOURCE-GROUP-NAME
#
# example with some quick-and-dirty grep-fu to store the result in a variable:
line=$(azure network lb list -g negatvmssrg | grep negatvmssrg)
split_line=( $line )
lb_name=${split_line[1]}

# now that we have the name of the load balancer, we can show the details to find which Public IP (PIP) is 
# associated to it
#
# generic syntax:
# azure network lb show -g RESOURCE-GROUP-NAME -n LOAD-BALANCER-NAME
#
# example with some quick-and-dirty grep-fu to store the result in a variable:
line=$(azure network lb show -g negatvmssrg -n $lb_name | grep loadBalancerFrontEnd)
split_line=( $line )
pip_name=${split_line[4]}

# now that we have the name of the public IP address, we can show the details to find the FQDN
#
# generic syntax:
# azure network public-ip show -g RESOURCE-GROUP-NAME -n PIP-NAME
#
# example with some quick-and-dirty grep-fu to store the result in a variable:
line=$(azure network public-ip show -g negatvmssrg -n $pip_name | grep FQDN)
split_line=( $line )
FQDN=${split_line[3]}

# now that we have the FQDN, we can use ssh on port 50,000+i to connect to VM i (where i is 0-indexed)
#
# example to connct via ssh into VM "0":
ssh -p 50000 negat@$FQDN
```