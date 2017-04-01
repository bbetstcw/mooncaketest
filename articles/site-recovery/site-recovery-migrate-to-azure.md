---
title: Migrate to Azure with Site Recovery | Azure
description: This article provides an overview of migrating VMs and physical servers to Azure with Azure Site Recovery
services: site-recovery
documentationcenter: ''
author: rayne-wiselman
manager: jwhit
editor: ''

ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/04/2017
wacn.date: ''
ms.author: raynew
---

# Migrate to Azure with Site Recovery?

Read this article for an overview of using the Azure Site Recovery service for migration of virtual machines and physical servers.

Organizations need a BCDR strategy that determines how apps, workloads, and data stay running and available during planned and unplanned downtime, and recover to normal working conditions as soon as possible. Your BCDR strategy should keep business data safe and recoverable, and ensure that workloads remain continuously available when disaster occurs.

Site Recovery is an Azure service that contributes to your BCDR strategy, by orchestrating replication of on-premises physical servers and virtual machines to the cloud (Azure), or to a secondary datacenter. When outages occur in your primary location, you fail over to the secondary location to keep apps and workloads available. You fail back to your primary location when it returns to normal operations. Learn more in [What is Site Recovery?](./site-recovery-overview.md)

This article describes deployment in the [Azure portal](https://portal.azure.cn). The [Azure classic portal](https://manage.windowsazure.cn/) can be used to maintain existing Site Recovery vaults, but you can't create new vaults.

## What do we mean by migration?

You can deploy Site Recovery for full replication of on-premises VMs and physical servers, to Azure or to a secondary site.You replicate machines, fail them over from the primary site when outages occur, and fail them back to the primary site when it recovers. In addition to full replication, you can use Site Recovery to migrate VMs and physical servers to Azure, so that users can access the machine workload from Azure VMs. Migration entails replication, and failover from the primary site to Azure. However, unlike full replication, it does not include a failback mechanism.

## What can Site Recovery migrate?

You can:

- Migrate workloads running on on-premises Hyper-V VMs, VMware VMs, and physical servers, to run on Azure VMs. You can also do full replication and failback in this scenario.
- Migrate [Azure IaaS VMs](./site-recovery-migrate-azure-to-azure.md) between Azure regions. Currently only migration is supported in this scenario, which means failback isn't supported.
- Migrate [AWS Windows instances](./site-recovery-migrate-aws-to-azure.md) to Azure IaaS VMs. Currently only migration is supported in this scenario, which means failback isn't supported.

## Migrate on-premises VMs and physical servers

To migrate on-premises Hyper-V VMs, VMware VMs, and physical servers, you follow almody the same steps as those used for regular replication. You set up a Recovery Services vault, configure the required management servers (depending on what you want to migrate), add them to the vault, and specify replication settings. You enable replication for the machines you want to migrate, and run a quick test failover to ensure that everything's working as it should.

After you verify that your replication environment is working, you use a planned or unplanned failover depending on [what's supported](./site-recovery-failover.md#failover-and-failback) for your scenario. For migration, you don't need to commit a failover or delete anything. Instead, you select the **Complete Migration** option for each machine you want to migrate. The **Complete Migration** action finishes up the migration process, removes replication for the machine, and stops Site Recovery billing for the machine.

## Migrate between Azure regions

You can migrate Azure VMs between regions using Site Recovery. In this scenario only migration is supported. In other words, you can replicate the Azure VMs and fail them over to anothe region, but you can't fail back. In this scenario you set up a Recovery Services vault, deploy an on-premises configuration server to manage replication, add it to the vault, and specify replication settings. You enable replication for the machines you want to migrate, and run a quick test failover. Then you run an unplanned failover with the **Complete Migration** option.

## Migrate AWS to Azure

You can migrate AWS instances to Azure VMs. In this scenario only migration is supported. In other words, you can replicate the AWS instances and fail them over to Azure, but you can't fail back. AWS instances are handled in the same way as physical servers for migration purposes. You set up a Recovery Services vault, deploy an on-premises configuration server to manage replication, add it to the vault, and specify replication settings. You enable replication for the machines you want to migrate, and run a quick test failover. Then you run an unplanned failover with the **Complete Migration** option.

## Next steps

- [Migrate VMware VMs to Azure](./site-recovery-vmware-to-azure.md)
- [Migrate physical servers to Azure](./site-recovery-vmware-to-azure.md)
- [Migrate Hyper-V VMs in VMM clouds to Azure](./site-recovery-vmm-to-azure.md)
- [Migrate Hyper-V VMs without VMM to Azure](./site-recovery-hyper-v-site-to-azure.md)
- [Migrate Azure VMs between Azure regions](./site-recovery-migrate-azure-to-azure.md)
- [Migrate AWS instances to Azure](./site-recovery-migrate-aws-to-azure.md)