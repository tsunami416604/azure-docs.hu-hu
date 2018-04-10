---
title: Azure-beli virtuális gépek nagy léptékű biztonsági mentése | Microsoft Docs
description: Ez az oktatóanyag részletesen ismerteti a több Azure-beli virtuális gép helyreállítási tárba történő biztonsági mentését.
services: backup
documentationcenter: ''
author: markgalioto
manager: carmonm
editor: ''
keywords: virtuális gép biztonsági mentése; biztonsági mentés; virtuális gép; biztonsági mentés és vészhelyreállítás
ms.assetid: ''
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/06/2017
ms.author: trinadhk;jimpark;markgal;
ms.custom: mvc
ms.openlocfilehash: 62cc623dc3130119c5ec803933012c5545d703e5
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
# <a name="back-up-azure-virtual-machines-in-azure-at-scale"></a>Azure-beli virtuális gépek nagy léptékű biztonsági mentése

Ez az oktatóanyag részletesen ismerteti az Azure-beli virtuális gépek helyreállítási tárba történő biztonsági mentését. A virtuális gépek biztonsági mentésének legnagyobb része az előkészítés. Egy virtuális gép biztonsági mentése (vagy védelme) előtt meg kell felelnie azoknak az [előfeltételeknek](backup-azure-arm-vms-prepare.md), amelyek felkészítik a környezetet a virtuális gépek védelmére. 

> [!IMPORTANT]
> Ez az oktatóanyag feltételezi, hogy már létrehozott egy erőforráscsoportot és egy Azure-beli virtuális gépet.

## <a name="create-a-recovery-services-vault"></a>Helyreállítási tár létrehozása

A [helyreállítási tár](backup-azure-recovery-services-vault-overview.md) egy olyan tároló, amely a biztonsági mentésre kijelölt elemek helyreállítási pontjait tartalmazza. A helyreállítási tár egy Azure-erőforráscsoport részeként telepíthető és kezelhető Azure-erőforrás. Ebben az oktatóanyagban ugyanabban az erőforráscsoportban fog helyreállítási tárat létrehozni, mint amelyik a virtuális gép védelmét végzi.


Amikor első alkalommal használja az Azure Backup szolgáltatást, regisztrálnia kell az Azure Recovery Services-szolgáltatót az előfizetésével. Ha már regisztrálta a szolgáltatót az előfizetésével, folytassa a következő lépéssel.

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices
```

Recovery Services-tárolót a **New-AzureRmRecoveryServicesVault** paranccsal hozhat létre. Ne feledje, hogy a biztonsági mentésre szánt virtuális gép konfigurálásakor használt erőforráscsoport nevét és helyét kell megadnia. 

```powershell
New-AzureRmRecoveryServicesVault -Name myRSvault -ResourceGroupName "myResourceGroup" -Location "EastUS"
```

Számos Azure Backup-parancsmaghoz szükséges bemenetként a helyreállítási tár objektum. Ebből az okból célszerű egy változóban tárolni a helyreállítási tár objektumot. Ezután a **Set-AzureRmRecoveryServicesBackupProperties** segítségével állítsa a **-BackupStorageRedundancy** beállítást [Georedundáns tárolás (GRS)](../storage/common/storage-redundancy-grs.md) értékre. 

```powershell
$vault1 = Get-AzureRmRecoveryServicesVault –Name myRSVault
Set-AzureRmRecoveryServicesBackupProperties  -vault $vault1 -BackupStorageRedundancy GeoRedundant
```

## <a name="back-up-azure-virtual-machines"></a>Azure-beli virtuális gépek biztonsági mentése

A kezdeti biztonsági mentés futtatása előtt be kell állítani a tárolási környezetet. A tárolási környezet a tár által védett adatok típusa. Helyreállítási tár létrehozásakor a tár alapértelmezett védelmi és megőrzési szabályzatokkal rendelkezik. Az alapértelmezett védelmi szabályzat naponta egyszer, adott időben aktivál egy biztonsági mentési feladatot. Az alapértelmezett megőrzési szabályzat 30 napig őrzi meg a napi helyreállítási pontokat. A jelen oktatóanyag esetében fogadja el az alapértelmezett szabályzatokat. 

A tárolási környezet beállításához használja a **[Set-AzureRmRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/set-azurermrecoveryservicesvaultcontext)** parancsot. A tárolási környezet beállítását követően az minden további parancsmagra érvényes lesz. 

```powershell
Get-AzureRmRecoveryServicesVault -Name myRSVault | Set-AzureRmRecoveryServicesVaultContext
```

A biztonsági mentési feladat aktiválásához használja a **[Backup-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/backup-azurermrecoveryservicesbackupitem)** parancsot. A biztonsági mentési feladat létrehoz egy helyreállítási pontot. Ha ez a kezdeti biztonsági mentés, akkor a helyreállítási pont teljes biztonsági mentés lesz. A további biztonsági mentésekkel növekményes másolatok jönnek létre.

```powershell
$namedContainer = Get-AzureRmRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered -FriendlyName "V2VM"
$item = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer -WorkloadType AzureVM
$job = Backup-AzureRmRecoveryServicesBackupItem -Item $item
```

## <a name="delete-the-recovery-services-vault"></a>A helyreállítási tár törlése

A helyreállítási tár törléséhez először a tárban lévő helyreállítási pontokat, majd a tár regisztrációját kell törölni. Ezekhez a lépésekhez az alábbi parancsok nyújtanak segítséget. 


```powershell
$Cont = Get-AzureRmRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered
$PI = Get-AzureRmRecoveryServicesBackupItem -Container $Cont[0] -WorkloadType AzureVm
Disable-AzureRmRecoveryServicesBackupProtection -RemoveRecoveryPoints $PI[0]
Unregister-AzureRmRecoveryServicesBackupContainer -Container $namedContainer
Remove-AzureRmRecoveryServicesVault -Vault $vault1
```

## <a name="troubleshooting-errors"></a>Hibaelhárítás
Ha probléma merül fel a virtuális gép biztonsági mentése közben, az [Azure-beli virtuális gépek biztonsági mentése közben felmerülő hibák elhárításáról szóló cikkben](backup-azure-vms-troubleshoot.md) talál segítséget.

## <a name="next-steps"></a>További lépések
A virtuális gépek most már megfelelő védelemmel rendelkeznek. A következő cikkekben a felügyeleti tevékenységekről, illetve a virtuális gépek helyreállítási pontról történő visszaállításáról tájékozódhat.

* A biztonsági mentés szabályzatának módosításáról további információt a [virtuális gépek az AzureRM.RecoveryServices.Backup parancsmagok segítségével végzett biztonsági mentéséről](backup-azure-vms-automation.md#create-a-protection-policy) szóló cikkben talál.
* [A virtuális gépek kezelése és figyelése](backup-azure-manage-vms.md)
* [Virtuális gépek visszaállítása](backup-azure-arm-restore-vms.md)
