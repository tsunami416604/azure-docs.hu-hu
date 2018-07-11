---
title: Site Recovery-tároló törlése
description: Ismerje meg, hogyan lehet törölni az Azure Site Recovery-tároló, a Site Recovery forgatókönyv alapján.
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.date: 07/06/2018
ms.author: rajani-janaki-ram
ms.openlocfilehash: 0ee162e368350ebf140b172ae528e3b76504141c
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2018
ms.locfileid: "37918220"
---
# <a name="delete-a-site-recovery-vault"></a>Site Recovery-tároló törlése

Függőségek megakadályozzák az az Azure Site Recovery-tároló törlése. Milyen lépéseket kell tennie a Site Recovery forgatókönyv függ. Az Azure Backup tároló törlése, lásd: [törlése az Azure Backup-tároló](../backup/backup-azure-delete-vault.md).



## <a name="delete-a-site-recovery-vault"></a>Site Recovery-tároló törlése 
A tároló törléséhez kövesse a javasolt lépéseket a forgatókönyvhöz.

### <a name="vmware-vms-to-azure"></a>VMware virtuális gépek az Azure-ba

1. Törölje az összes védett virtuális gépek a lépéseket követve [tiltsa le a védelmet egy VMware-ről](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure).

2. Minden replikációs házirend törlése a lépéseket követve [replikációs házirend törlése](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy).

3. Törölje a vCenter mutató hivatkozásokat a lépéseket követve [vCenter-kiszolgáló törlése](vmware-azure-manage-vcenter.md#delete-a-vcenter-server).

4. A konfigurációs kiszolgáló törlése a lépéseket követve [szerelje le a konfigurációs kiszolgáló](vmware-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server).

5. Törölje a tárat.


### <a name="hyper-v-vms-with-vmm-to-azure"></a>Hyper-V virtuális gépek (VMM-mel) az Azure-ba
1. Törölje az összes védett virtuális gépek a lépéseket követve[tiltsa le a védelmet egy Hyper-V virtuális gép (a VMM-mel)](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario).

2. Szüntesse meg az & minden replikációs szabályzat törléséhez keresse meg a tároló -> **Site Recovery-infrastruktúra** -> **System Center VMM** -> **replikáció Szabályzatok**

3.  Törli a VMM-kiszolgálóra mutató hivatkozásokat a lépéseket követve [regisztrációjának törlése a VMM-kiszolgálóhoz csatlakoztatott](site-recovery-manage-registration-and-protection.md##unregister-a-vmm-server).

4.  Törölje a tárat.

### <a name="hyper-v-vms-without-virtual-machine-manager-to-azure"></a>A Hyper-V virtuális gépek (nélkül Virtual Machine Manager-) az Azure-bA
1. Törölje az összes védett virtuális gépek a lépéseket követve [tiltsa le a védelmet egy Hyper-V virtuális gép (a Hyper-V – Azure)](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure).

2. Szüntesse meg az & minden replikációs szabályzat törléséhez keresse meg a tároló -> **Site Recovery-infrastruktúra** -> **Hyper-V helyek** -> **replikációs házirendek**

3. Hyper-V kiszolgálók mutató hivatkozások törlése a lépéseket követve [regisztrációjának törlése a Hyper-V-gazdagép](site-recovery-manage-registration-and-protection.md#unregister-a-hyper-v-host-in-a-hyper-v-site).

4. A Hyper-V hely törlése.

5. Törölje a tárat.


## <a name="use-powershell-to-force-delete-the-vault"></a>A PowerShell szolgáltatás használatával kényszerítheti a tároló törlése 

> [!Important]
> A termék tesztel, és nem érintett adatveszteség, ha a kényszerített törlés módszer segítségével eltávolítja a tárolót és minden függőségét.
> A PowerShell-parancsot a tároló teljes tartalmát törli és **nem vonható vissza**.

A Site Recovery-tároló törlése, még akkor is, ha nincsenek védett elemek, a parancsok használatához:

    Connect-AzureRmAccount

    Select-AzureRmSubscription -SubscriptionName "XXXXX"

    $vault = Get-AzureRmRecoveryServicesVault -Name "vaultname"

    Remove-AzureRmRecoveryServicesVault -Vault $vault

Tudjon meg többet [Get-AzureRMRecoveryServicesVault](https://docs.microsoft.com/en-us/powershell/module/azurerm.recoveryservices/get-azurermrecoveryservicesvault?view=azurermps-6.0.0), és [Remove-AzureRMRecoveryServicesVault](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/remove-azurermrecoveryservicesvault?view=azurermps-6.0.0).
