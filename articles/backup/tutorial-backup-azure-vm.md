---
title: "Készítsen biztonsági másolatot az Azure virtuális gépek léptékű Azure-ban |} Microsoft Docs"
description: "A több Azure virtuális gépek biztonsági mentését a Recovery Services-tároló oktatóanyag adatokat."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: "virtuális gép biztonsági mentése; Készítsen biztonsági másolatot a virtuális gép; biztonsági mentés és katasztrófa utáni helyreállítás"
ms.assetid: 
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/06/2017
ms.author: trinadhk;jimpark;markgal;
ms.custom: mvc
ms.openlocfilehash: 01609c00c6f0585eff4843932b9eb7a090a59c19
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2017
---
# <a name="back-up-azure-virtual-machines-in-azure-at-scale"></a>Készítsen biztonsági másolatot az Azure virtuális gépek Azure léptékű

Ez az oktatóanyag biztonsági mentése Azure virtuális gépeken a Recovery Services-tároló részletezi. A virtuális gépek biztonsági mentését a munka nagyobb része a előkészítése. Előtt biztonsági mentését (vagy védelme) egy virtuális gép, el kell végeznie a [Előfeltételek](backup-azure-arm-vms-prepare.md) készítse fel a környezetet a virtuális gépek védelmére. 

> [!IMPORTANT]
> Ez az oktatóanyag azt feltételezi, hogy a már létrehozott egy erőforráscsoport és az Azure virtuális gépként.

## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

A [Recovery Services-tároló](backup-azure-recovery-services-vault-overview.md) olyan tároló, amely a helyreállítási pontok a fájlok biztonsági mentése folyamatban van. Recovery Services-tároló egy Azure-erőforrás, amelyek telepítése és felügyelete az Azure-erőforrás csoport részeként. Ebben az oktatóanyagban létrehoz egy Recovery Services-tárolónak ugyanabban az erőforráscsoportban védelem alatt álló virtuális gépként.


Azure biztonsági másolat, először regisztrálnia kell az Azure Recovery szolgáltató az előfizetéshez. Ha a szolgáltató már regisztrált az előfizetéshez, nyissa meg a következő lépéssel.

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices
```

Recovery Services-tárolót a **New-AzureRmRecoveryServicesVault** paranccsal hozhat létre. Ne felejtse el az erőforráscsoport-név és a virtuális gépet, készítsen biztonsági másolatot szeretne konfigurálásakor használt helyet adja meg. 

```powershell
New-AzureRmRecoveryServicesVault -Name myRSvault -ResourceGroupName "myResourceGroup" -Location "EastUS"
```

Sok Azure biztonsági mentést készítő parancsmagok bemeneti adatokként a Recovery Services-tároló objektum szükséges. Emiatt célszerű a Recovery Services biztonsági másolat tároló objektum tárolható egy változóban. Ezután **Set-AzureRmRecoveryServicesBackupProperties** beállítása a **- BackupStorageRedundancy** lehetőséggel [Georedundáns tárolás (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage). 

```powershell
$vault1 = Get-AzureRmRecoveryServicesVault –Name myRSVault
Set-AzureRmRecoveryServicesBackupProperties  -vault $vault1 -BackupStorageRedundancy GeoRedundant
```

## <a name="back-up-azure-virtual-machines"></a>Azure-beli virtuális gépek biztonsági mentése

A kezdeti biztonsági mentés futtatása előtt meg kell adni a tárolóban a környezetben. A tároló-környezet a tárolóban lévő védett adatok típusát. Recovery Services-tároló létrehozásakor az alapértelmezett védelem és adatmegőrzési ismét. Az alapértelmezett védelmi házirendet a biztonsági mentési feladatot, a megadott időpontban naponta váltja ki. Az alapértelmezett megőrzési házirend 30 napig őrzi meg a napi helyreállítási pont. Ebben az oktatóanyagban fogadja el az alapértelmezett házirendet. 

Használjon ** [Set-AzureRmRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/set-azurermrecoveryservicesvaultcontext) ** beállítani a tároló a környezetben. A tároló-környezet van beállítva, ha az összes későbbi parancsmag vonatkozik. 

```powershell
Get-AzureRmRecoveryServicesVault -Name myRSVault | Set-AzureRmRecoveryServicesVaultContext
```

Használjon ** [Backup-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/backup-azurermrecoveryservicesbackupitem) ** számára a biztonsági mentési feladatot indít. A biztonsági mentési feladat létrehoz egy helyreállítási pontot. Ha a kezdeti biztonsági másolatot, majd a helyreállítási pont teljes biztonsági mentés. További biztonsági mentések növekményes másolatot készíteni.

```powershell
$namedContainer = Get-AzureRmRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered -FriendlyName "V2VM"
$item = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer -WorkloadType AzureVM
$job = Backup-AzureRmRecoveryServicesBackupItem -Item $item
```

## <a name="delete-the-recovery-services-vault"></a>A Recovery Services-tároló törlése

Recovery Services-tároló törléséhez először törölnie a tárolóban lévő helyreállítási pontot, és majd szüntesse meg a tároló regisztrációját. A következő parancsok ezeket a lépéseket részletesen. 


```powershell
$Cont = Get-AzureRmRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered
$PI = Get-AzureRmRecoveryServicesBackupItem -Container $Cont[0] -WorkloadType AzureVm
Disable-AzureRmRecoveryServicesBackupProtection -RemoveRecoveryPoints $PI[0]
Unregister-AzureRmRecoveryServicesBackupContainer -Container $namedContainer
Remove-AzureRmRecoveryServicesVault -Vault $vault1
```

## <a name="troubleshooting-errors"></a>Kapcsolatos hibák elhárítása
Ha biztonsági során problémákat tapasztal a virtuális gép, tekintse meg a [hibaelhárítási cikk az Azure virtuális gépek biztonsági mentése](backup-azure-vms-troubleshoot.md) segítségét.

## <a name="next-steps"></a>Következő lépések
Most, hogy a virtuális gépeket védetté, tekintse meg a következő cikkekben olvashat a felügyeleti feladatokat, és hogyan lehet visszaállítani a virtuális gépek helyreállítási pontból.

* A biztonsági mentési házirend módosításához lásd [használata AzureRM.RecoveryServices.Backup parancsmagok segítségével készítsen biztonsági másolatot a virtuális gépek](backup-azure-vms-automation.md#create-a-protection-policy).
* [A virtuális gépek kezelése és figyelése](backup-azure-manage-vms.md)
* [Virtuális gépek visszaállítása](backup-azure-arm-restore-vms.md)
