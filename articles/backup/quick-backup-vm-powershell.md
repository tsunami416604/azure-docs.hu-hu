---
title: Rövid útmutató – A Virtuális gép biztonsági és biztonsági elégsével
description: Ebben a rövid útmutatóban megtudhatja, hogyan biztonsági másolatot ad az Azure virtuális gépeiről az Azure PowerShell-modullal.
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 04/16/2019
ms.custom: mvc
ms.openlocfilehash: 8021ca553a1434c891bee911e85d351c61938594
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "74171947"
---
# <a name="back-up-a-virtual-machine-in-azure-with-powershell"></a>Virtuális gép biztonsági mentése az Azure-ban a PowerShell használatával

Az [Azure PowerShell AZ](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.4.0) modul azure-erőforrások létrehozására és kezelésére szolgál a parancssorból vagy parancsfájlokban.

[Az Azure Backup](backup-overview.md) biztonsági másolatot készít a helyszíni gépekről és alkalmazásokról, valamint az Azure virtuális gépekről. Ez a cikk bemutatja, hogyan biztonsági másolatot egy Azure virtuális gép az AZ modul. Másik lehetőségként biztonsági másolatot is tehet egy virtuális gépről az [Azure CLI](quick-backup-vm-cli.md)használatával vagy az [Azure Portalon.](quick-backup-vm-portal.md)

Ez a rövid útmutató a meglévő Azure-beli virtuális gépek biztonsági mentését mutatja be. Ha létre kell hoznia egy virtuális gépet, [létrehozhatja azt az Azure PowerShell használatával](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json).

Ehhez a rövid útmutatóhoz az Azure PowerShell AZ modul 1.0.0-s vagy újabb verziója szükséges. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-and-register"></a>Bejelentkezés és regisztráció

1. Jelentkezzen be az Azure-előfizetésbe a `Connect-AzAccount` paranccsal, és kövesse a képernyőn megjelenő útmutatásokat.

    ```powershell
    Connect-AzAccount
    ```

2. Az Azure Backup első használatakor regisztrálnia kell az Azure Recovery Service szolgáltatót az előfizetésében a [Register-AzResourceProvider szolgáltatással,](/powershell/module/az.Resources/Register-azResourceProvider)az alábbiak szerint:

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

A [Recovery Services-tároló](backup-azure-recovery-services-vault-overview.md) egy logikai tároló, amely tárolja a biztonsági mentési adatokat a védett erőforrások, például az Azure virtuális gépek. Amikor egy biztonsági mentési feladat fut, létrehoz egy helyreállítási pontot a Recovery Services-tárolóban. Ezt követően ezen helyreállítási pontok egyikével állíthatja vissza az adatokat egy adott időpontra.

A tároló létrehozásakor:

- Az erőforráscsoport és a hely, adja meg az erőforráscsoport és a hely a virtuális gép biztonsági másolatot szeretne.
- Ha ezt a [mintaparancsfájlt](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json) használta a virtuális gép létrehozásához, az erőforráscsoport a **myResourceGroup**, a virtuális gép ***myVM,** és az erőforrások a **WestEurope** régióban találhatók.
- Az Azure Backup automatikusan kezeli a tárolt adatok tárolását. Alapértelmezés szerint a tároló [georedundáns tárolást (GRS)](../storage/common/storage-redundancy-grs.md)használ. A georedundancia biztosítja, hogy a biztonsági másolat adatai replikálódnak egy másodlagos Azure-régióba, több száz mérföldre az elsődleges régiótól.

Most hozzon létre egy trezort:

1. A vault létrehozásához használja a [New-AzRecoveryServicesVault-ot:](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault)

    ```powershell
    New-AzRecoveryServicesVault `
        -ResourceGroupName "myResourceGroup" `
        -Name "myRecoveryServicesVault" `
    -Location "WestEurope"
    ```

2. Állítsa be a tároló környezetét a [Set-AzRecoveryServicesVaultContext környezettel](/powershell/module/az.RecoveryServices/Set-azRecoveryServicesVaultContext)az alábbiak szerint:

    ```powershell
    Get-AzRecoveryServicesVault `
        -Name "myRecoveryServicesVault" | Set-AzRecoveryServicesVaultContext
    ```

3. Módosítsa a tároló tároló kapacitásredundancia-konfigurációját (LRS/GRS) a [Set-AzRecoveryServicesBackupProperty tulajdonsággal,](https://docs.microsoft.com/powershell/module/az.recoveryservices/Set-AzRecoveryServicesBackupProperty)az alábbiak szerint:

    ```powershell
    Get-AzRecoveryServicesVault `
        -Name "myRecoveryServicesVault" | Set-AzRecoveryServicesBackupProperty -BackupStorageRedundancy LocallyRedundant/GeoRedundant
    ```

    > [!NOTE]
    > A tárolási redundancia csak akkor módosítható, ha nincsenek biztonsági mentési elemek a tárolóban.

## <a name="enable-backup-for-an-azure-vm"></a>Biztonsági mentés engedélyezése Azure-beli virtuális gépeken

Engedélyezi a biztonsági mentést egy Azure virtuális gép, és adja meg a biztonsági mentési szabályzatot.

- A házirend határozza meg, hogy mikor futnak a biztonsági mentések, és mennyi ideig őrizze meg a biztonsági mentések által létrehozott helyreállítási pontokat.
- Az alapértelmezett védelmi házirend naponta egyszer futtat egy biztonsági mentést a virtuális gép számára, és 30 napig megőrzi a létrehozott helyreállítási pontokat. Ezzel az alapértelmezett szabályzattal gyorsan védheti a virtuális gép.

A biztonsági mentés engedélyezése az alábbiak szerint:

1. Először állítsa be az alapértelmezett házirendet a [Get-AzRecoveryServicesBackupProtectionPolicy (Alapértelmezett házirend) segítségével:](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy)

    ```powershell
    $policy = Get-AzRecoveryServicesBackupProtectionPolicy     -Name "DefaultPolicy"
    ```

2. Virtuális gép biztonsági mentésének engedélyezése az [Enable-AzRecoveryServicesBackupProtection segítségével.](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) Adja meg a házirendet, az erőforráscsoportot és a virtuális gép nevét.

    ```powershell
    Enable-AzRecoveryServicesBackupProtection `
        -ResourceGroupName "myResourceGroup" `
        -Name "myVM" `
        -Policy $policy
    ```

## <a name="start-a-backup-job"></a>Biztonsági mentési feladat indítása

A biztonsági mentések a biztonsági mentési házirendben megadott ütemezés szerint futnak. Igény szerinti biztonsági mentést is futtathat:

- Az első kezdeti biztonsági mentési feladat létrehoz egy teljes helyreállítási pontot.
- A kezdeti biztonsági mentés után minden biztonsági mentési feladat növekményes helyreállítási pontokat hoz létre.
- A növekményes helyreállítási pontok hatékonyan használják a tárhelyet és az időt, mivel csak az utolsó biztonsági mentés óta végzett módosításokat viszik át.

Igény szerinti biztonsági mentés futtatásához használja a [Backup-AzRecoveryServicesBackupItem elemet.](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem)

- Megad egy tárolót a tárolóban, amely a biztonsági mentési adatokat a [Get-AzRecoveryServicesBackupContainer tárolóval tárolja.](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer)
- A rendszer minden olyan virtuális gépet, amelyről biztonsági másolat készül, egy elemként kezeli. Biztonsági mentési feladat indításához a [Get-AzRecoveryServicesBackupItem segítségével](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem)kaphat információkat a virtuális gépről.

Igény szerinti biztonsági mentési feladat futtatása az alábbiak szerint:

1. Adja meg a tárolót, szerezze be a virtuális gép adatait, és futtassa a biztonsági mentést.

    ```powershell
    $backupcontainer = Get-AzRecoveryServicesBackupContainer `
        -ContainerType "AzureVM" `
        -FriendlyName "myVM"

    $item = Get-AzRecoveryServicesBackupItem `
        -Container $backupcontainer `
        -WorkloadType "AzureVM"

    Backup-AzRecoveryServicesBackupItem -Item $item
    ```

2. Előfordulhat, hogy legfeljebb 20 percet kell várnia, mivel az első biztonsági mentési feladat létrehoz egy teljes helyreállítási pontot. A feladat figyelése a következő eljárásban leírtak szerint.

## <a name="monitor-the-backup-job"></a>A biztonsági mentési feladat monitorozása

1. Futtassa [a Get-AzRecoveryservicesBackupJob parancsot](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) a feladat állapotának figyeléséhez.

    ```powershell
    Get-AzRecoveryservicesBackupJob
    ```

    A kimenet hasonló a következő példához, amely a feladatot InProgress néven jeleníti **meg:**

    ```output
    WorkloadName   Operation         Status       StartTime              EndTime                JobID
    ------------   ---------         ------       ---------              -------                -----
    myvm           Backup            InProgress   9/18/2017 9:38:02 PM                          9f9e8f14
    myvm           ConfigureBackup   Completed    9/18/2017 9:33:18 PM   9/18/2017 9:33:51 PM   fe79c739
    ```

2. Ha a feladat állapota **befejeződött,** a virtuális gép védett, és egy teljes helyreállítási pont van tárolva.

## <a name="clean-up-the-deployment"></a>A telepítés karbantartása

Ha már nem kell biztonsági másolatot a virtuális gép, akkor tisztítsa meg.

- Ha szeretné kipróbálni a virtuális gép visszaállítását, hagyja ki a karbantartást.
- Ha egy meglévő virtuális gép, kihagyhatja a végső [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) parancsmag, hogy az erőforráscsoport és a virtuális gép a helyén marad.

Tiltsa le a védelmet, távolítsa el a visszaállítási pontokat és a tárolót. Ezután törölje az erőforráscsoportot és a kapcsolódó virtuálisgép-erőforrásokat az alábbiak szerint:

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $item -RemoveRecoveryPoints
$vault = Get-AzRecoveryServicesVault -Name "myRecoveryServicesVault"
Remove-AzRecoveryServicesVault -Vault $vault
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>További lépések

A rövid útmutató során létrehozott egy Recovery Services-tárolót, engedélyezte a védelmet a virtuális gépen, valamint létrehozta a kezdeti helyreállítási pontot.

- [Ismerje meg, hogyan](tutorial-backup-vm-at-scale.md) biztonsági másolatot a virtuális gépek ről az Azure Portalon.
- [Ismerje meg, hogyan állíthatja](tutorial-restore-disk.md) vissza gyorsan a virtuális gépeket
