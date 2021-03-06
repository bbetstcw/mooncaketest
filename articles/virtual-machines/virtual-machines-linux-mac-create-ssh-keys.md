---
title: Create and use an SSH key pair for Linux VMs in Azure | Azure
description: How to create and use an SSH public and private key pair for Linux VMs in Azure to improve the security of the authentication process.
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: timlt
editor: ''
tags: azure-resource-manager

ms.assetid: 34ae9482-da3e-4b2d-9d0d-9d672aa42498
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/07/2017
wacn.date: ''
ms.author: iainfou
experimental: true
experiment_id: "rasquill-ssh-20170308"

---

# How to create and use an SSH public and private key pair for Linux VMs in Azure
With a secure shell (SSH) key pair, you can create virtual machines (VMs) in Azure that use SSH keys for authentication, eliminating the need for passwords to log in. This article shows you how to quickly generate and use an SSH protocol version 2 RSA public and private key file pair for Linux VMs. For more detailed steps and additional examples, such as for use with the Classic Management Portal, see [detailed steps to create SSH key pairs and certificates](virtual-machines-linux-create-ssh-keys-detailed.md).

## Create an SSH key pair
Use the `ssh-keygen` command to create SSH public and private key files that are by default created in the `~/.ssh` directory, but you can specify a different location and additional passphrase (a password to access the private key file) when prompted. Run the following command from a Bash shell, answering the prompts with your own information.

```bash
ssh-keygen -t rsa -b 2048 
```

## Use the SSH key pair
The public key that you place on your Linux VM in Azure is by default stored in `~/.ssh/id_rsa.pub`, unless you changed the location when you created them. If you use the [Azure CLI 2.0](https://docs.microsoft.com/cli/azure) to create your VM, specify the location of this public key when you use the [az vm create](https://docs.microsoft.com/cli/azure/vm#create) with the `--ssh-key-path` option. If you copy and paste the contents of the public key file to use in the Azure portal preview or a Resource Manager template, make sure you don't copy any additional whitespace. For example, if you use OS X, you can pipe the public key file (by default, **~/.ssh/id_rsa.pub**) to **pbcopy** to copy the contents (there are other Linux programs that do the same thing, such as `xclip`). 

If you're not familiar with SSH public keys, you can see your public key by running `cat` as follows, replacing `~/.ssh/id_rsa.pub` with your own public key file location:

```bash
cat ~/.ssh/id_rsa.pub
```

With the public key on your Azure VM, SSH to your VM using the IP address or DNS name of your VM (remember to replace `azureuser` and `myvm.chinanorth.chinacloudapp.cn` below with the admin username and the fully qualified domain name -- or IP address):

```bash
ssh azureuser@myvm.chinanorth.chinacloudapp.cn
```

If you provided a passphrase when you created your key pair, enter the passphrase when prompted during the login process. (The server is added to your `~/.ssh/known_hosts` folder, and you won't be asked to connect again until the public key on your Azure VM changes or the server name is removed from `~/.ssh/known_hosts`.)

## Next steps

VMs created using SSH keys are by default configured with passwords disabled, to make brute-forced guessing attempts vastly more expensive and therefore difficult. This topic describes creating a simple SSH key pair for quick usage. If you need more assistance in creating your SSH key pair or require additional certificates, such as for use with the Classic Management Portal, see [Detailed steps to create SSH key pairs and certificates](virtual-machines-linux-create-ssh-keys-detailed.md).

You can create VMs that use your SSH key pair using the Azure portal preview, CLI, and templates:

* [Create a secure Linux VM using the Azure portal preview](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Create a secure Linux VM using the Azure CLI 2.0)](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Create a secure Linux VM using an Azure template](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)