---
title: Azure rövid útmutató – Virtuális gépek biztonsági mentése a PowerShell használatával
description: Ismerje meg, hogyan készíthet biztonsági mentést virtuális gépeiről az Azure PowerShell használatával.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 03/05/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 00ec813aec37697526233532b75ba6c55bf852c2
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58906072"
---
# <a name="back-up-a-virtual-machine-in-azure-with-powershell"></a>Virtuális gép biztonsági mentése az Azure-ban a PowerShell használatával

A [Azure PowerShell AZ](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.4.0) -modul létrehozása és kezelése Azure-erőforrások parancssorból vagy szkriptekkel történő használható. 

[Az Azure Backup](backup-overview.md) a helyszíni gépek és az alkalmazások és az Azure virtuális gépek biztonsági mentését. Ez a cikk bemutatja, hogyan készíthet biztonsági másolatot egy Azure virtuális Gépen AZ modult. Másik megoldásként készíthet biztonsági mentést egy virtuális gépet a [Azure CLI-vel](quick-backup-vm-cli.md), vagy a a [az Azure portal](quick-backup-vm-portal.md).

Ez a rövid útmutató a meglévő Azure-beli virtuális gépek biztonsági mentését mutatja be. Ha létre kell hoznia egy virtuális gépet, [létrehozhatja azt az Azure PowerShell használatával](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json).

Ebben a rövid útmutatóhoz az Azure PowerShell modul 1.0.0-ás AZ vagy újabb. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="log-in-and-register"></a>Bejelentkezés és regisztráció

1. Jelentkezzen be az Azure-előfizetésbe a `Connect-AzAccount` paranccsal, és kövesse a képernyőn megjelenő útmutatásokat.

    ```powershell
    Connect-AzAccount
    ```
2. Az első alkalommal használja az Azure Backup, az Azure Recovery Services-szolgáltatót regisztrálnia kell az előfizetésében lévő [Register-AzResourceProvider](/powershell/module/az.Resources/Register-azResourceProvider), az alábbiak szerint:

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```


## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

A [Recovery Services-tároló](backup-azure-recovery-services-vault-overview.md) olyan logikai tároló, amely tárolja a védett erőforrások, például az Azure virtuális gépek biztonsági másolati adatait. Amikor egy biztonsági mentési feladat fut, a Recovery Services-tároló belül egy helyreállítási pontot hoz létre. Ezt követően ezen helyreállítási pontok egyikével állíthatja vissza az adatokat egy adott időpontra.

A tároló létrehozásakor:

- Az erőforráscsoportot és helyet adja meg az erőforráscsoportot és helyet a virtuális gép biztonsági.
- Ha ez [mintaparancsfájl](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json) a virtuális gép létrehozásához, az erőforráscsoport van **myResourceGroup**, a virtuális gép ***myVM**, az erőforrások pedig a **WestEurope**  régióban.
- Az Azure Backup automatikusan kezeli a biztonsági másolat adatainak tárolására. Alapértelmezés szerint a tárolót használja [Georedundáns tárolást (GRS)](../storage/common/storage-redundancy-grs.md). Georedundáns tárolás biztosítja, hogy a biztonsági másolatba mentett adatok replikációja egy másodlagos Azure-régióban, több száz mérföld a forrásadatok elsődleges.

Most hozzon létre egy tárolót.


1. Használja a [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault)a tároló létrehozásához:

    ```powershell
    New-AzRecoveryServicesVault `
        -ResourceGroupName "myResourceGroup" `
        -Name "myRecoveryServicesVault" `
    -Location "WestEurope"
    ```

2. Állítsa be a tárolási környezetet a [Set-AzRecoveryServicesVaultContext](/powershell/module/az.RecoveryServices/Set-azRecoveryServicesVaultContext), az alábbiak szerint:

    ```powershell
    Get-AzRecoveryServicesVault `
        -Name "myRecoveryServicesVault" | Set-AzRecoveryServicesVaultContext
    ```

3. Módosítsa a tárolót a storage redundancia konfigurációját (LRS vagy GRS) [Set-AzRecoveryServicesBackupProperties](https://docs.microsoft.com/powershell/module/az.recoveryservices/Set-AzRecoveryServicesBackupProperties?view=azps-1.6.0), az alábbiak szerint:
    
    ```powershell
    Get-AzRecoveryServicesVault `
        -Name "myRecoveryServicesVault" | Set-AzRecoveryServicesBackupProperties -BackupStorageRedundancy LocallyRedundant/GeoRedundant
    ```
    > [!NOTE]
    > Tárhely-redundancia módosíthatók, csak akkor, ha nem találhatók biztonsági mentési elemek védett a tárolóba.

## <a name="enable-backup-for-an-azure-vm"></a>Biztonsági mentés engedélyezése Azure-beli virtuális gépeken

Biztonsági mentés engedélyezése egy Azure virtuális gép, és adjon meg egy biztonsági mentési szabályzatot.

- A szabályzat határozza meg a biztonsági mentések futtatási idejének, és hogy mennyi ideig megőrződjön-e a biztonsági mentések által létrehozott helyreállítási pontokat.
- Az alapértelmezett védelmi házirend a virtuális gép egy biztonsági mentés naponta egyszer fut, és 30 napig őrzi meg a létrehozott helyreállítási pontokat. Ez az alapértelmezett házirend segítségével gyorsan biztosíthatja virtuális GÉPE védelmét. 

Biztonsági mentés engedélyezése a következőképpen:

1. Először állítsa be az alapértelmezett házirendet a [Get-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy):

    ```powershell
    $policy = Get-AzRecoveryServicesBackupProtectionPolicy     -Name "DefaultPolicy"
    ```

2. A virtuális gép biztonsági mentésének engedélyezése [engedélyezése – AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection). Adja meg a szabályzatot, az erőforráscsoportot és a virtuális gép nevét.

    ```powershell
    Enable-AzRecoveryServicesBackupProtection `
        -ResourceGroupName "myResourceGroup" `
        -Name "myVM" `
        -Policy $policy
    ```


## <a name="start-a-backup-job"></a>Biztonsági mentési feladat indítása

Biztonsági másolatok a biztonsági mentési szabályzatban meghatározott ütemezés szerint futtatni. Egy ad-hoc biztonsági mentési is futtathatja:

- Az első kezdeti biztonsági mentési feladat létrehoz egy teljes helyreállítási pontot.
- A kezdeti biztonsági mentés után biztonsági mentési feladatok növekményes helyreállítási pontokat hoz létre.
- A növekményes helyreállítási pontok hatékonyan használják a tárhelyet és az időt, mivel csak az utolsó biztonsági mentés óta végzett módosításokat viszik át.

Egy ad-hoc biztonsági mentés végrehajtásához használja a[Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem). 
- Egy tárolót határozhat meg a tároló, amely a biztonsági másolat adatait a [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer).
- A rendszer minden olyan virtuális gépet, amelyről biztonsági másolat készül, egy elemként kezeli. Indítsa el a biztonsági mentési feladat, szerezze be a virtuális Gépet a információit [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem).

Egy ad-hoc biztonsági mentési feladat futtatása a következő:

1. Adja meg a tároló, szerezze be a virtuális gép adatai és a biztonsági mentés futtatása.

    ```powershell
    $backupcontainer = Get-AzRecoveryServicesBackupContainer `
        -ContainerType "AzureVM" `
        -FriendlyName "myVM"

    $item = Get-AzRecoveryServicesBackupItem `
        -Container $backupcontainer `
        -WorkloadType "AzureVM"

    Backup-AzRecoveryServicesBackupItem -Item $item
    ```

2. Szüksége lehet akár 20 percig, várjon, mivel az első biztonsági mentési feladat létrehoz egy teljes helyreállítási pontot. A feladat figyeléséhez a következő eljárásban leírtak szerint.


## <a name="monitor-the-backup-job"></a>A biztonsági mentési feladat monitorozása

1. Futtatás [Get-AzRecoveryservicesBackupJob](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) a feladat állapotának monitorozásához.

    ```powershell
    Get-AzRecoveryservicesBackupJob
    ```
    Kimenet hasonlít a következő példának, amely bemutatja a feladatban az **InProgress**:

    ```
    WorkloadName   Operation         Status       StartTime              EndTime                JobID
    ------------   ---------         ------       ---------              -------                -----
    myvm           Backup            InProgress   9/18/2017 9:38:02 PM                          9f9e8f14
    myvm           ConfigureBackup   Completed    9/18/2017 9:33:18 PM   9/18/2017 9:33:51 PM   fe79c739
    ```

2. Ha a feladat állapota: **befejezve**, a virtuális gép védett, és rendelkezik egy teljes helyreállítási pontot tárolni.


## <a name="clean-up-the-deployment"></a>Az üzemelő példány eltávolítása

Ha már nincs szüksége a biztonsági másolatot készíteni a virtuális Gépet, akkor távolíthatja el.
- Ha azt szeretné, és próbálja ki a virtuális gép visszaállítása, hagyja ki a tiszta fel.
- Ha egy meglévő virtuális Gépet használt, eltekinthet a végső [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) megtarthatja az erőforráscsoportot és a virtuális gép helyen.

Tiltsa le a védelmet, távolítsa el a helyreállítási pontokat és a tárolót. Ezután a következőképpen törölje az erőforráscsoportot és a társított virtuális gép erőforrásait:

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $item -RemoveRecoveryPoints
$vault = Get-AzRecoveryServicesVault -Name "myRecoveryServicesVault"
Remove-AzRecoveryServicesVault -Vault $vault
Remove-AzResourceGroup -Name "myResourceGroup"
```


## <a name="next-steps"></a>További lépések

A rövid útmutató során létrehozott egy Recovery Services-tárolót, engedélyezte a védelmet a virtuális gépen, valamint létrehozta a kezdeti helyreállítási pontot. 

- [Ismerje meg, hogyan](tutorial-backup-vm-at-scale.md) a virtuális gépek biztonsági mentése az Azure Portalon.
- [Ismerje meg, hogyan](tutorial-restore-disk.md) gyorsan a virtuális gép visszaállítása
