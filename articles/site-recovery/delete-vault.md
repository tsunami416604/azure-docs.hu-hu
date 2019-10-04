---
title: A Azure Site Recovery szolgáltatáshoz konfigurált Recovery Services-tároló törlése
description: Megtudhatja, hogyan törölhet Azure Site Recoveryhoz konfigurált Recovery Services-tárolót
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: a13dee2010688b02fd86fb05900826470a7d7a08
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876044"
---
# <a name="delete-a-site-recovery-services-vault"></a>Site Recovery Services-tároló törlése

A függőségek megakadályozhatják Azure Site Recovery tároló törlését. Az Site Recovery forgatókönyvtől függően változhatnak a szükséges műveletek. A Azure Backupban használt tár törléséhez lásd: [Backup-tároló törlése az Azure-ban](../backup/backup-azure-delete-vault.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="delete-a-site-recovery-vault"></a>Site Recovery-tároló törlése 
A tár törléséhez kövesse az ajánlott lépéseket a forgatókönyvhöz.
### <a name="azure-vms-to-azure"></a>Azure-beli virtuális gépek az Azure-ba

1. Az összes védett virtuális gép törléséhez kövesse a [VMware-védelem letiltása](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-azure-vm-azure-to-azure)című témakör lépéseit.
2. Törölje a tárolót.

### <a name="vmware-vms-to-azure"></a>VMware virtuális gépek az Azure-ba

1. Az összes védett virtuális gép törléséhez kövesse a [VMware-védelem letiltása](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure)című témakör lépéseit.

2. Törölje az összes replikációs házirendet a [replikációs házirend törlése](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy)című témakör lépéseit követve.

3. Törölje a vCenter mutató hivatkozásokat a vCenter- [kiszolgáló törlése](vmware-azure-manage-vcenter.md#delete-a-vcenter-server)című rész lépéseit követve.

4. A konfigurációs kiszolgáló leszerelésének lépéseit követve törölje a konfigurációs kiszolgálót. [](vmware-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server)

5. Törölje a tárolót.


### <a name="hyper-v-vms-with-vmm-to-azure"></a>Hyper-V virtuális gépek (VMM-mel) az Azure-ba
1. Az összes védett virtuális gép törléséhez kövesse a[Hyper-V virtuális gép védelmének letiltása (VMM)](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario)című témakör lépéseit.

2. Az összes replikációs szabályzat törlésének megszüntetése & a tár-> **site Recovery infrastruktúra** -> **a System Center VMM** -> **replikációs házirendjeihez** való tallózással

3.  Törölje a VMM-kiszolgálókra mutató hivatkozásokat a [csatlakoztatott VMM-kiszolgáló regisztrációjának](site-recovery-manage-registration-and-protection.md##unregister-a-vmm-server)törlése című rész lépéseit követve.

4.  Törölje a tárolót.

### <a name="hyper-v-vms-without-virtual-machine-manager-to-azure"></a>Hyper-V virtuális gépek (Virtual Machine Manager nélkül) az Azure-ba
1. Törölje az összes védett virtuális [gépet a Hyper-v rendszerű virtuális gép védelmének letiltása (Hyper-v – Azure)](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure)című témakör lépéseit követve.

2. Az összes replikációs szabályzat törlésének megszüntetése & a tár-> **site Recovery infrastruktúra** > **a Hyper-V-helyek** -> **replikációs házirendjeihez** tallózással

3. Törölje a Hyper-V kiszolgálókra mutató hivatkozásokat a [Hyper-v-gazdagép regisztrációjának megszüntetése](site-recovery-manage-registration-and-protection.md#unregister-a-hyper-v-host-in-a-hyper-v-site)című témakörben leírtak szerint.

4. Törölje a Hyper-V-helyet.

5. Törölje a tárolót.


## <a name="use-powershell-to-force-delete-the-vault"></a>A tároló törlésének kényszerítése a PowerShell használatával 

> [!Important]
> Ha teszteli a terméket, és nem aggódik az adatvesztéssel kapcsolatban, akkor a kényszerített törlési módszer használatával gyorsan eltávolíthatja a tárolót és annak összes függőségét.
> A PowerShell-parancs törli a tár összes tartalmát, és **nem vonható**vissza.

Ha védett elemek is vannak, akkor a Site Recovery-tároló törléséhez használja a következő parancsokat:

    Connect-AzAccount

    Select-AzSubscription -SubscriptionName "XXXXX"

    $vault = Get-AzRecoveryServicesVault -Name "vaultname"

    Remove-AzRecoveryServicesVault -Vault $vault

További információ a [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault)és a [Remove-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/remove-azrecoveryservicesvault).
