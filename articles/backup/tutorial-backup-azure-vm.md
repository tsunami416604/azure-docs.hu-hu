---
title: Készítsen biztonsági másolatot a több Azure virtuális gépeken a PowerShell-lel
description: Ez az oktatóanyag részletesen több Azure virtuális gépek biztonsági mentésének Azure PowerShell-lel a Recovery Services-tárolóra.
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: tutorial
ms.date: 03/05/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 85e5fc7e1c8a4561b51afaf0d665fedb6d9cde1f
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58258377"
---
# <a name="back-up-azure-vms-with-powershell"></a>Biztonsági mentése Azure virtuális gépeken a PowerShell-lel

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ez az oktatóanyag azt ismerteti, hogyan helyezhet üzembe egy [Azure Backup](backup-overview.md) a PowerShell használata több Azure virtuális gépek biztonsági mentése Recovery Services-tároló.  

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Hozzon létre egy Recovery Services-tárolót, és állítsa be a tárolási környezet.
> * Biztonsági mentési szabályzat meghatározása
> * A biztonsági mentési szabályzat alkalmazása több virtuális gép védelme érdekében
> * Az eseményindító egy igény szerinti biztonsági mentési feladat előtt a védett virtuális gépek is biztonsági mentése (vagy védelme) egy virtuális gépet, el kell végeznie a [Előfeltételek](backup-azure-arm-vms-prepare.md) készítse elő a környezetét a virtuális gépek védelmére. 

> [!IMPORTANT]
> Ez az oktatóanyag feltételezi, hogy már létrehozott egy erőforráscsoportot és egy Azure-beli virtuális gépet.


## <a name="log-in-and-register"></a>Bejelentkezés és regisztráció


1. Jelentkezzen be az Azure-előfizetésbe a `Connect-AzAccount` paranccsal, és kövesse a képernyőn megjelenő útmutatásokat.

    ```powershell
    Connect-AzAccount
    ```
2. Az első alkalommal használja az Azure Backup, az Azure Recovery Services-szolgáltatót regisztrálnia kell az előfizetésében lévő [Register-AzResourceProvider](/powershell/module/az.Resources/Register-azResourceProvider). Ha már regisztrálta, kihagyhatja ezt a lépést.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```


## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

A [Recovery Services-tároló](backup-azure-recovery-services-vault-overview.md) olyan logikai tároló, amely tárolja a védett erőforrások, például az Azure virtuális gépek biztonsági másolati adatait. Amikor egy biztonsági mentési feladat fut, a Recovery Services-tároló belül egy helyreállítási pontot hoz létre. Ezt követően ezen helyreállítási pontok egyikével állíthatja vissza az adatokat egy adott időpontra.


- Ebben az oktatóanyagban a tároló a ugyanazt az erőforráscsoportot és helyet a virtuális Gépet, készítsen biztonsági másolatot szeretne létrehozni.
- Az Azure Backup automatikusan kezeli a biztonsági másolat adatainak tárolására. Alapértelmezés szerint a tárolót használja [Georedundáns tárolást (GRS)](../storage/common/storage-redundancy-grs.md). Georedundáns tárolás biztosítja, hogy a biztonsági másolatba mentett adatok replikációja egy másodlagos Azure-régióban, több száz mérföld a forrásadatok elsődleges.

Hozza létre a tárolót a következőképpen:

1. Használja a [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault), hozza létre a tárolót. Az erőforráscsoport-nevet és a virtuális Gépet, készítsen biztonsági másolatot szeretne helyének megadásához.

    ```powershell
    New-AzRecoveryServicesVault -Name myRSvault -ResourceGroupName "myResourceGroup" -Location "EastUS"
    ```
2. Számos Azure Backup-parancsmaghoz szükséges bemenetként a helyreállítási tár objektum. Ebből az okból célszerű egy változóban tárolni a helyreállítási tár objektumot.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault –Name myRSVault
    ```
    
3. Állítsa be a tárolási környezetet a [Set-AzRecoveryServicesVaultContext](/powershell/module/az.RecoveryServices/Set-azRecoveryServicesVaultContext).

   - A tárolási környezet a tár által védett adatok típusa.
   - A környezet beállítását követően minden további parancsmagra vonatkozik

     ```powershell
     Get-AzRecoveryServicesVault -Name "myRSVault" | Set-AzRecoveryServicesVaultContext
     ```

## <a name="back-up-azure-vms"></a>Azure-beli virtuális gépek biztonsági mentése

Biztonsági másolatok a biztonsági mentési szabályzatban meghatározott ütemezés szerint futtatni. Helyreállítási tár létrehozásakor a tár alapértelmezett védelmi és megőrzési szabályzatokkal rendelkezik.

- Az alapértelmezett védelmi házirend indítja el a biztonsági mentési feladatot, egy adott időpontban naponta.
- Az alapértelmezett megőrzési szabályzat 30 napig őrzi meg a napi helyreállítási pontokat. 

Engedélyezi, és ebben az oktatóanyagban a mentése az Azure virtuális gép biztonsági mentése, hogy tegye a következőket:

1. Adjon meg egy tárolót a tárolóban, amely a biztonsági másolat adatait a [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer).
2. Minden virtuális gép biztonsági mentésének egy elemet. Indítsa el a biztonsági mentési feladat, szerezze be a virtuális Gépet a információit [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem).
3. Egy ad-hoc biztonsági mentés futtatása[Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem). 
    - Az első kezdeti biztonsági mentési feladat létrehoz egy teljes helyreállítási pontot.
    - A kezdeti biztonsági mentés után biztonsági mentési feladatok növekményes helyreállítási pontokat hoz létre.
    - A növekményes helyreállítási pontok hatékonyan használják a tárhelyet és az időt, mivel csak az utolsó biztonsági mentés óta végzett módosításokat viszik át.

Engedélyezze, és a következőképpen futtassa a biztonsági mentés:

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered -FriendlyName "V2VM"
$item = Get-AzRecoveryServicesBackupItem -Container $namedContainer -WorkloadType AzureVM
$job = Backup-AzRecoveryServicesBackupItem -Item $item
```

## <a name="troubleshooting"></a>Hibaelhárítás 

Ha problémákat tapasztal során biztonsági másolatot készíteni a virtuális gépről, tekintse át ezt [hibaelhárításról szóló cikk](backup-azure-vms-troubleshoot.md).

### <a name="deleting-a-recovery-services-vault"></a>Recovery Services-tároló törlése

Tároló törlése van szüksége, ha először törölje a tárban lévő helyreállítási pontokat, és ezután regisztrációjának törlése a tárolóhoz, a következő:


```powershell
$Cont = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered
$PI = Get-AzRecoveryServicesBackupItem -Container $Cont[0] -WorkloadType AzureVm
Disable-AzRecoveryServicesBackupProtection -RemoveRecoveryPoints $PI[0]
Unregister-AzRecoveryServicesBackupContainer -Container $namedContainer
Remove-AzRecoveryServicesVault -Vault $vault1
```

## <a name="next-steps"></a>További lépések

- [Felülvizsgálat](backup-azure-vms-automation.md) biztonsági mentése és visszaállítása az Azure virtuális gépeken a PowerShell-lel részletesebb leírását. 
- [Felügyelheti és figyelheti az Azure virtuális gépek](backup-azure-manage-vms.md)
- [Az Azure virtuális gépek visszaállítása](backup-azure-arm-restore-vms.md)
