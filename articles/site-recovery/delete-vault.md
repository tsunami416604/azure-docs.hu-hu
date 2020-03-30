---
title: Azure-webhely-helyreállítási tároló törlése
description: Megtudhatja, hogyan törölheti az Azure Site Recovery szolgáltatáshoz konfigurált helyreállítási szolgáltatások trezorát
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/05/2019
ms.author: rajanaki
ms.openlocfilehash: 0e409ffdedbac822aedf48833f2dd85f8e04afa2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894984"
---
# <a name="delete-a-site-recovery-services-vault"></a>Site Recovery Services-tár törlése

Ez a cikk a Helyreállítási szolgáltatások webhely-helyreállítási tárolójának törlését ismerteti. Az Azure Backup ban használt tároló törléséről az Azure biztonsági [mentési tárolójának törlése.](../backup/backup-azure-delete-vault.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


## <a name="before-you-start"></a>Előkészületek

A tároló törlése előtt el kell távolítania a regisztrált kiszolgálókat és a tárolóban lévő elemeket. Az eltávolítandó műveletek a telepített replikációs forgatókönyvektől függnek. 


## <a name="delete-a-vault-azure-vm-to-azure"></a>Vault-Azure virtuális gép törlése az Azure-ba

1. Kövesse [ezeket](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-azure-vm-azure-to-azure) az utasításokat az összes védett virtuális gép törléséhez.
2. Ezután törölje a trezort.

## <a name="delete-a-vault-vmware-vm-to-azure"></a>Vault-VMware vm törlése az Azure-ba

1. Kövesse [ezeket](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) az utasításokat az összes védett virtuális gép törléséhez.
2. Az összes replikációs házirend törléséhez kövesse [az alábbi lépéseket.](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy)
3. A vCenterre mutató hivatkozások törlése [az alábbi lépésekkel.](vmware-azure-manage-vcenter.md#delete-a-vcenter-server)
4. A konfigurációs kiszolgáló leszereléséhez kövesse [az alábbi utasításokat.](vmware-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server)
5. Ezután törölje a trezort.


## <a name="delete-a-vault-hyper-v-vm-with-vmm-to-azure"></a>Vault-Hyper-V VM törlése (VMM-mel) az Azure-ba

1. Az [alábbi lépésekkel](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario) törölheti a System Center VMM által kezelt Hyper-V virtuális gépeket.
2. Az összes replikációs házirend társításának és törlésének leválasztani és törölni. Ehhez a tárolóban >**System Center VMM** > **replikációs házirendjei** **hely-helyreállítási infrastruktúráját.** > 
3. A csatlakoztatott VMM-kiszolgáló regisztrációjának megszüntetése érdekében kövesse [az alábbi lépéseket.](site-recovery-manage-registration-and-protection.md#unregister-a-vmm-server)
4. Ezután törölje a trezort.

## <a name="delete-a-vault-hyper-v-vm-to-azure"></a>Vault-Hyper-V virtuális gép törlése az Azure-ba

1. Kövesse [az alábbi lépéseket](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure) az összes védett virtuális gép törléséhez.
2. Az összes replikációs házirend társításának és törlésének leválasztani és törölni. Ehhez a tárolóban > **a hely-helyreállítási infrastruktúra** > **a Hyper-V helyek** > **replikációs házirendjeihez.**
3. A Hyper-V állomás regisztrációjának megszüntetése érdekében kövesse [az alábbi utasításokat.](site-recovery-manage-registration-and-protection.md#unregister-a-hyper-v-host-in-a-hyper-v-site)
4. Törölje a Hyper-V webhelyet.
5. Ezután törölje a trezort.


## <a name="use-powershell-to-force-delete-the-vault"></a>A PowerShell használata a tároló törlésének kényszerítéséhez 

> [!Important]
> Ha teszteli a terméket, és nem aggódik az adatvesztés miatt, használja a force delete módszert a tároló és az összes függőség gyors eltávolításához.
> A PowerShell parancs törli a tároló teljes tartalmát, és **nem visszafordítható.**

Ha védett elemek esetén is törölni szeretné a Site Recovery tárolót, használja az alábbi parancsokat:

    Connect-AzAccount

    Select-AzSubscription -SubscriptionName "XXXXX"

    $vault = Get-AzRecoveryServicesVault -Name "vaultname"

    Remove-AzRecoveryServicesVault -Vault $vault

További információ a [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault)és az [Remove-AzRecoveryServicesVault szolgáltatásról.](https://docs.microsoft.com/powershell/module/az.recoveryservices/remove-azrecoveryservicesvault)
