---
title: 'Oktatóanyag: Több Azure virtuális gép biztonsági mentése a PowerShellhasználatával'
description: Ez az oktatóanyag több Azure-beli virtuális gép biztonsági mentését részletezi egy Recovery Services-tárolóba az Azure PowerShell használatával.
ms.topic: tutorial
ms.date: 03/05/2019
ms.custom: mvc
ms.openlocfilehash: 154238eae78ce44b9fc91058e58d9a11e254c0f9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74171784"
---
# <a name="back-up-azure-vms-with-powershell"></a>Azure-beli virtuális gépek biztonsági mentése PowerShell-lel

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ez az oktatóanyag bemutatja, hogyan telepíthet egy [Azure Backup](backup-overview.md) Recovery Services-tárolót több Azure-beli virtuális gép ről a PowerShell használatával.  

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
>
> * Hozzon létre egy Helyreállítási szolgáltatások tároló, és állítsa be a tároló környezetben.
> * Biztonsági mentési szabályzat meghatározása
> * A biztonsági mentési szabályzat alkalmazása több virtuális gép védelme érdekében
> * Indítsa el az igény szerinti biztonsági mentési feladatot a védett virtuális gépek előtt biztonsági másolatot készíteni (vagy védeni) egy virtuális gép, be kell fejeznie az [előfeltételeket, hogy előkészítse](backup-azure-arm-vms-prepare.md) a környezetet a virtuális gépek védelmére.

> [!IMPORTANT]
> Ez az oktatóanyag feltételezi, hogy már létrehozott egy erőforráscsoportot és egy Azure-beli virtuális gépet.

## <a name="sign-in-and-register"></a>Bejelentkezés és regisztráció

1. Jelentkezzen be az Azure-előfizetésbe a `Connect-AzAccount` paranccsal, és kövesse a képernyőn megjelenő útmutatásokat.

    ```powershell
    Connect-AzAccount
    ```

2. Az Azure Backup első használatakor regisztrálnia kell az Azure Recovery Service szolgáltatót az előfizetésében a [Register-AzResourceProvider szolgáltatással.](/powershell/module/az.Resources/Register-azResourceProvider) Ha már regisztrált, hagyja ki ezt a lépést.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

A [Recovery Services-tároló](backup-azure-recovery-services-vault-overview.md) egy logikai tároló, amely tárolja a biztonsági mentési adatokat a védett erőforrások, például az Azure virtuális gépek. Amikor egy biztonsági mentési feladat fut, létrehoz egy helyreállítási pontot a Recovery Services-tárolóban. Ezt követően ezen helyreállítási pontok egyikével állíthatja vissza az adatokat egy adott időpontra.

* Ebben az oktatóanyagban hozza létre a tárolóugyanabban az erőforráscsoportban és helyen, mint a virtuális gép, amelyről biztonsági másolatot szeretne készíteni.
* Az Azure Backup automatikusan kezeli a tárolt adatok tárolását. Alapértelmezés szerint a tároló [georedundáns tárolást (GRS)](../storage/common/storage-redundancy-grs.md)használ. A georedundancia biztosítja, hogy a biztonsági másolat adatai replikálódnak egy másodlagos Azure-régióba, több száz mérföldre az elsődleges régiótól.

Hozza létre a tárolót az alábbiak szerint:

1. A Vault létrehozásához használja a [New-AzRecoveryServicesVault.Use the New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault)to create the vault. Adja meg az erőforráscsoport nevét és helyét a virtuális gép, amelyről biztonsági másolatot szeretne tudni.

    ```powershell
    New-AzRecoveryServicesVault -Name myRSvault -ResourceGroupName "myResourceGroup" -Location "EastUS"
    ```

2. Számos Azure Backup-parancsmaghoz szükséges bemenetként a helyreállítási tár objektum. Ebből az okból célszerű egy változóban tárolni a helyreállítási tár objektumot.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault –Name myRSVault
    ```

3. Állítsa be a tároló környezetét a [Set-AzRecoveryServicesVaultContext](/powershell/module/az.RecoveryServices/Set-azRecoveryServicesVaultContext)segítségével.

   * A tárolási környezet a tár által védett adatok típusa.
   * A környezet beállítása után az összes további parancsmagra vonatkozik

     ```powershell
     Get-AzRecoveryServicesVault -Name "myRSVault" | Set-AzRecoveryServicesVaultContext
     ```

## <a name="back-up-azure-vms"></a>Azure-beli virtuális gépek biztonsági mentése

A biztonsági mentések a biztonsági mentési házirendben megadott ütemezés szerint futnak. Helyreállítási tár létrehozásakor a tár alapértelmezett védelmi és megőrzési szabályzatokkal rendelkezik.

* Az alapértelmezett védelmi házirend egy adott időpontban naponta egyszer biztonsági mentési feladatot indít el.
* Az alapértelmezett megőrzési szabályzat 30 napig őrzi meg a napi helyreállítási pontokat.

Az Azure virtuális gép engedélyezéséhez és biztonsági mentéséhez az oktatóanyagban az alábbi lehetőségeket kell elvégeznünk:

1. Adjon meg egy tárolót a tárolóban, amely a biztonsági mentési adatokat a [Get-AzRecoveryServicesBackupContainer tárolóval tárolja.](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer)
2. A biztonsági mentéshez szükséges virtuális gépek egy elem. Biztonsági mentési feladat indításához a [Get-AzRecoveryServicesBackupItem segítségével](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem)kaphat információkat a virtuális gépről.
3. Igény szerinti biztonsági mentés futtatása a[Backup-AzRecoveryServicesBackupItem segítségével.](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem)
    * Az első kezdeti biztonsági mentési feladat létrehoz egy teljes helyreállítási pontot.
    * A kezdeti biztonsági mentés után minden biztonsági mentési feladat növekményes helyreállítási pontokat hoz létre.
    * A növekményes helyreállítási pontok hatékonyan használják a tárhelyet és az időt, mivel csak az utolsó biztonsági mentés óta végzett módosításokat viszik át.

A biztonsági mentés engedélyezése és futtatása az alábbiak szerint:

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered -FriendlyName "V2VM"
$item = Get-AzRecoveryServicesBackupItem -Container $namedContainer -WorkloadType AzureVM
$job = Backup-AzRecoveryServicesBackupItem -Item $item
```

## <a name="troubleshooting"></a>Hibaelhárítás

Ha a virtuális gép biztonsági mentése közben problémákba ütközik, tekintse át ezt a [hibaelhárítási cikket.](backup-azure-vms-troubleshoot.md)

### <a name="deleting-a-recovery-services-vault"></a>Recovery Services-tároló törlése

Ha törölnie kell egy tárolót, először törölje a helyreállítási pontokat a tárolóban, majd törölje a tároló regisztrációját az alábbiak szerint:

```powershell
$Cont = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered
$PI = Get-AzRecoveryServicesBackupItem -Container $Cont[0] -WorkloadType AzureVm
Disable-AzRecoveryServicesBackupProtection -RemoveRecoveryPoints $PI[0]
Unregister-AzRecoveryServicesBackupContainer -Container $namedContainer
Remove-AzRecoveryServicesVault -Vault $vault1
```

## <a name="next-steps"></a>További lépések

* [Tekintse át](backup-azure-vms-automation.md) az Azure-beli virtuális gépek PowerShell használatával történő biztonsági mentésének és visszaállításának részletesebb forgatókönyvét.
* [Azure-beli virtuális gépek kezelése és figyelése](backup-azure-manage-vms.md)
* [Azure-beli virtuális gépek visszaállítása](backup-azure-arm-restore-vms.md)
