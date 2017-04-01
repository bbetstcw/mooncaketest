---
title: Get Azure tools (macOS 10.10) | Azure
description: Install Python and Azure command-line interface (Azure CLI) on macOS.
services: iot-hub
documentationcenter: ''
author: shizn
manager: timtl
tags: ''
keywords: iot cloud service, azure cli

ms.assetid: f2d7d584-7734-401c-976c-81788a7282a3
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2016
wacn.date: ''
ms.author: xshi
---

# Get Azure tools (macOS 10.10)
>[!div class="op_single_selector"]
[Windows 7 and later](./iot-hub-raspberry-pi-kit-c-lesson2-get-azure-tools-win32.md)
[Ubuntu 16.04](./iot-hub-raspberry-pi-kit-c-lesson2-get-azure-tools-ubuntu.md)
[macOS 10.10](./iot-hub-raspberry-pi-kit-c-lesson2-get-azure-tools-mac.md)

## What you will do
Install the Azure command-line interface (Azure CLI). If you have any problems, look for solutions on the [troubleshooting page](./iot-hub-raspberry-pi-kit-c-troubleshooting.md).

## What you will learn
In this article, you will learn:
* How to install Azure CLI.
* How to add an IoT subgroup of the Azure CLI.

## What you need
* A Mac with an Internet connection.
* An active Azure subscription. If you don't have an Azure account, you can create a [Azure trial account](https://www.azure.cn/pricing/1rmb-trial/) in just a few minutes.

## Install Python
Although macOS comes with Python 2.7 out of the box, we recommend that you install Python through Homebrew. See [Installing Python on macOS](http://docs.python-guide.org/en/latest/starting/install/osx/).

Install Python and pip by running the following command:

```bash
brew install python
```

## Install the Azure CLI
The Azure CLI provides a multiplatform command-line experience for Azure. You work directly from your command line to provision and manage resources. 

To install the latest Azure CLI, follow these steps:

1. Run the following commands in a terminal window. It might take five minutes to install the Azure CLI.

   ```bash
   pip install --upgrade azure-cli
   pip install --upgrade azure-cli-iot
   ```
2. Verify the installation by running the following command:

   ```bash
   az iot -h
   ```

You should see the following output if the installation is successful.

![Output that indicates success](./media/iot-hub-raspberry-pi-lessons/lesson2/az_iot_help_osx.png)

## Summary
You've installed the Azure CLI. Your next task is to create your Azure IoT hub and device identity by using the Azure CLI.

## Next steps
[Create your IoT hub and register Raspberry Pi 3](./iot-hub-raspberry-pi-kit-c-lesson2-prepare-azure-iot-hub.md)