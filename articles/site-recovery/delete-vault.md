---
title: "A Site Recovery-tároló törlése"
description: "Megtudhatja, hogyan törölhető egy Azure Site Recovery-tárolóban, a Site Recovery forgatókönyv alapján."
service: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: rajani-janaki-ram
ms.openlocfilehash: 89ab1e7c8b2fa0f4014ecfa0e677b398e601e6fa
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="delete-a-site-recovery-vault"></a>A Site Recovery-tároló törlése
Függőségek megakadályozzák az az Azure Site Recovery-tároló törlése. A Site Recovery forgatókönyvön függően változnak, milyen lépéseket kell tennie: az Azure-ba, a Hyper-V (a és a System Center Virtual Machine Manager nélkül) Azure és az Azure Backup VMware. Törli a tárolót, az Azure Backup szolgáltatáshoz használt, lásd: [az Azure biztonsági mentési tároló törlése](../backup/backup-azure-delete-vault.md).



## <a name="delete-a-site-recovery-vault"></a>A Site Recovery-tároló törlése 
Törli a tárolót, hajtsa végre a javasolt lépéseket a forgatókönyvéhez.

### <a name="vmware-vms-to-azure"></a>VMware virtuális gépek az Azure-ba

1. Töröljön minden védett virtuális gépek a megfelelő lépéseket követve [tiltsa le a védelmet a VMware](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure).

2. Minden replikációs házirendek törlése lépéseit követve [törölheti a replikációs házirendet](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy).

3. Törli a vCenter mutató hivatkozások lépéseit követve [törli a vCenter-kiszolgáló](vmware-azure-manage-vcenter.md#delete-a-vcenter-server).

4. A konfigurációs kiszolgáló törlése a lépések [konfigurációs kiszolgáló leszerelése](vmware-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server).

5. Törli a tárolót.


### <a name="hyper-v-vms-with-virtual-machine-manager-to-azure"></a>A Hyper-V virtuális gépek (a Virtual Machine Managerrel) az Azure-bA
1. Töröljön minden védett virtuális gépek a megfelelő lépéseket követve[letilthatja a védelmet a Hyper-V virtuális gépek replikálása Azure-bA a System Center VMM-forgatókönyvet az Azure használatával](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario).

2. Szüntesse meg, és törölje az összes replikációs házirendek keresse meg azt a tárolóba -> **Site Recovery-infrastruktúra** -> **System Center VMM** -> **replikáció Házirendek**

3.  A Virtual Machine Manager-kiszolgálók hivatkoznak törlése lépéseit követve [csatlakoztatott VMM-kiszolgáló regisztrációját](site-recovery-manage-registration-and-protection.md##unregister-a-vmm-server).

4.  Törli a tárolót.

### <a name="hyper-v-vms-without-virtual-machine-manager-to-azure"></a>A Hyper-V virtuális gépek (nélkül Virtuálisgép-kezelő) az Azure-bA
1. Töröljön minden védett virtuális gépek a megfelelő lépéseket követve [tiltsa le a védelmet a Hyper-V virtuális gép (Hyper-V az Azure-bA)](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure).

2. Szüntesse meg, és törölje az összes replikációs házirendek keresse meg azt a tárolóba -> **Site Recovery-infrastruktúra** -> **a Hyper-V helyek** -> **replikációs házirendhez**

3. Hyper-V kiszolgálók mutató hivatkozások törlése a lépések [regisztrációjának törlése a Hyper-V gazdagépek](/site-recovery-manage-registration-and-protection.md##unregister-a-hyper-v-host-in-a-hyper-v-site).

4. A Hyper-V hely törlése.

5. Törli a tárolót.


## <a name="use-powershell-to-force-delete-the-vault"></a>A PowerShell szolgáltatás használatával kényszerítheti a tároló törlése 

> [!Important]
> Ha a termék tesztel, és nem részletesebben szeretnének foglalkozni az adatvesztés, a kényszerített delete metódus segítségével gyorsan távolítsa el a tárolóhoz és annak függőségeit.
> A PowerShell-parancsot a tároló tartalmának törlése és **nem visszafejthető**.

A Site Recovery-tároló törlése, még akkor is, ha nincsenek védett elemek, ezen parancsok használatával:

    Login-AzureRmAccount

    Select-AzureRmSubscription -SubscriptionName "XXXXX"

    $vault = Get-AzureRmSiteRecoveryVault -Name "vaultname"

    Remove-AzureRmSiteRecoveryVault -Vault $vault
