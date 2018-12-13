---
title: Azure rövid útmutató – Virtuális gépek biztonsági mentése a PowerShell használatával
description: Ismerje meg, hogyan készíthet biztonsági mentést virtuális gépeiről az Azure PowerShell használatával.
services: backup
author: rayne-wiselman
manager: carmonm
tags: azure-resource-manager, virtual-machine-backup
ms.service: backup
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 2/14/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 7971c1399cc905353e271e8774c0e7ad36ca79a9
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52869254"
---
# <a name="back-up-a-virtual-machine-in-azure-with-powershell"></a>Virtuális gép biztonsági mentése az Azure-ban a PowerShell használatával
Az Azure PowerShell-modul az Azure-erőforrások parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható. Adatai védelme érdekében érdemes rendszeres időközönként biztonság mentést végeznie. Az Azure Backup georedundáns helyreállítási tárolókban tárolható helyreállítási pontokat hoz létre. Ez a cikk a virtuális gépek (VM-ek) az Azure PowerShell modul használatával való biztonsági mentését mutatja be részletesen. Az [Azure CLI](quick-backup-vm-cli.md) vagy az [Azure Portal](quick-backup-vm-portal.md) használatával is elvégezheti ezeket a lépéseket.

Ez a rövid útmutató a meglévő Azure-beli virtuális gépek biztonsági mentését mutatja be. Ha létre kell hoznia egy virtuális gépet, [létrehozhatja azt az Azure PowerShell használatával](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json).

Ehhez a rövid útmutatóhoz az Azure PowerShell-modul 4.4-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: ` Get-Module -ListAvailable AzureRM`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket.


## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba
Jelentkezzen be az Azure-előfizetésbe a `Connect-AzureRmAccount` paranccsal, és kövesse a képernyőn megjelenő útmutatásokat.

```powershell
Connect-AzureRmAccount
```

Amikor első alkalommal használja az Azure Backup szolgáltatást, regisztrálnia kell az Azure Recovery Services-szolgáltatót az előfizetésében a [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider) paranccsal.

```powershell
Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
```


## <a name="create-a-recovery-services-vaults"></a>Recovery Services-tároló létrehozása
A Recovery Services-tároló egy logikai tároló, amely az egyes védett erőforrások, például az Azure-beli virtuális gépek biztonsági másolatainak adatait tárolja. Amikor egy védett erőforrás biztonsági mentésének feladata fut, a rendszer egy helyreállítási pontot hoz létre a Recovery Services-tárolóban. Ezt követően ezen helyreállítási pontok egyikével állíthatja vissza az adatokat egy adott időpontra.

Recovery Services-tárolót a [New-AzureRmRecoveryServicesVault](/powershell/module/azurerm.recoveryservices/new-azurermrecoveryservicesvault) paranccsal hozhat létre. Adja meg a védeni kívánt virtuális gépével megegyező erőforráscsoportot és helyet. Ha a virtuális gépet a [mintaparancsfájl](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json) használatával hozta létre, az erőforráscsoport a *myResourceGroup*, míg a virtuális gép a *myVM* nevet kapja, az erőforrások pedig a *WestEurope* (Nyugat-Európa) helyen lesznek.

```powershell
New-AzureRmRecoveryServicesVault `
    -ResourceGroupName "myResourceGroup" `
    -Name "myRecoveryServicesVault" `
    -Location "WestEurope"
```

A tároló alapértelmezés szerint georedundáns tárolásra van beállítva. Az adatok további védelme érdekében ez a tárhelyredundancia-szint biztosítja, hogy a rendszer egy másodlagos Azure-régióba replikálja az adatokat, amely a forrásadatok elsődleges helyétől több száz kilométerre található.

Annak érdekében, hogy a további lépésekben is használhassa ezt a tárolót, állítsa be a tárolási környezetet a [Set-AzureRmRecoveryServicesVaultContext](/powershell/module/AzureRM.RecoveryServices/Set-AzureRmRecoveryServicesVaultContext) paranccsal.

```powershell
Get-AzureRmRecoveryServicesVault `
    -Name "myRecoveryServicesVault" | Set-AzureRmRecoveryServicesVaultContext
```


## <a name="enable-backup-for-an-azure-vm"></a>Biztonsági mentés engedélyezése Azure-beli virtuális gépeken
Házirendek létrehozásával és használatával határozhatja meg, hogy a rendszer mikor futtassa a biztonsági mentési feladatokat, és meddig tárolja a helyreállítási pontokat. Az alapértelmezett védelmi házirend naponta egyszer futtat biztonsági mentési feladatot, és 30 napig őrzi meg a helyreállítási pontokat. Ezekkel az alapértelmezett értékekkel gyorsan biztosíthatja virtuális gépe védelmét. Először állítsa be az alapértelmezett házirendet a [Get-AzureRmRecoveryServicesBackupProtectionPolicy](/powershell/module/AzureRM.RecoveryServices.Backup/Get-AzureRmRecoveryServicesBackupProtectionPolicy) paranccsal:

```powershell
$policy = Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "DefaultPolicy"
```

A virtuális gépek biztonsági másolatainak védelmét az [Enable-AzureRmRecoveryServicesBackupProtection](/powershell/module/AzureRM.RecoveryServices.Backup/Enable-AzureRmRecoveryServicesBackupProtection) paranccsal engedélyezheti. Adja meg a használni kívánt házirendet, majd az erőforráscsoportot és a védeni kívánt virtuális gépet:

```powershell
Enable-AzureRmRecoveryServicesBackupProtection `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVM" `
    -Policy $policy
```


## <a name="start-a-backup-job"></a>Biztonsági mentési feladat indítása
Ha szeretné a biztonsági mentést most elindítani, ahelyett, hogy megvárná, amíg az alapértelmezett házirend az ütemezett időben futtatja a feladatot, használja a [Backup-AzureRmRecoveryServicesBackupItem](/powershell/module/azurerm.recoveryservices.backup/backup-azurermrecoveryservicesbackupitem) parancsot. Ez az első biztonsági mentési feladat létrehoz egy teljes helyreállítási pontot. Az ezt a kezdeti biztonsági mentést követő további biztonsági mentési feladatok növekményes helyreállítási pontokat hoznak létre. A növekményes helyreállítási pontok hatékonyan használják a tárhelyet és az időt, mivel csak az utolsó biztonsági mentés óta végzett módosításokat viszik át.

A következő parancsokkal egy tárolót határozhat meg a Recovery Services-tárolón belül a [Get-AzureRmRecoveryServicesBackupContainer](/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupcontainer) paranccsal, amely a biztonsági másolat adatait fogja tárolni. A rendszer minden olyan virtuális gépet, amelyről biztonsági másolat készül, egy elemként kezeli. A biztonsági mentési feladat indításához a virtuálisgép-elemmel kapcsolatos adatokat a [Get-AzureRmRecoveryServicesBackupItem](/powershell/module/AzureRM.RecoveryServices.Backup/Get-AzureRmRecoveryServicesBackupItem) paranccsal kérheti le.

```powershell
$backupcontainer = Get-AzureRmRecoveryServicesBackupContainer `
    -ContainerType "AzureVM" `
    -FriendlyName "myVM"

$item = Get-AzureRmRecoveryServicesBackupItem `
    -Container $backupcontainer `
    -WorkloadType "AzureVM"

Backup-AzureRmRecoveryServicesBackupItem -Item $item
```

Mivel ez az első biztonsági mentési feladat egy teljes helyreállítási pontot hoz létre, a folyamat akár 20 percet is igénybe vehet.


## <a name="monitor-the-backup-job"></a>A biztonsági mentési feladat monitorozása
A biztonsági mentési feladatok állapotának monitorozásához használja a [Get-AzureRmRecoveryservicesBackupJob](/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupjob) parancsot:

```powershell
Get-AzureRmRecoveryservicesBackupJob
```

A kimenet a következő példához hasonló, amelyen látható, hogy a biztonsági mentési feladat **InProgress** (Folyamatban) állapotban van:

```
WorkloadName   Operation         Status       StartTime              EndTime                JobID
------------   ---------         ------       ---------              -------                -----
myvm           Backup            InProgress   9/18/2017 9:38:02 PM                          9f9e8f14
myvm           ConfigureBackup   Completed    9/18/2017 9:33:18 PM   9/18/2017 9:33:51 PM   fe79c739
```

Amikor a biztonsági mentési feladat *Állapota* alatt *Befejezve* látható, a virtuális gép a Recovery Services védelme alatt áll, és megtörtént egy teljes helyreállítási pont tárolása.


## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása
Ha többé már nincs szükség rá, a virtuális gép védelmét bármikor leállíthatja, eltávolíthatja a helyreállítási pontokat és a Recovery Services-tárolót, majd törölheti az erőforráscsoportot és a társított virtuális gép erőforrásait. Ha egy meglévő virtuális gépet használt, eltekinthet a végső [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) parancs végrehajtásától, és megtarthatja az erőforráscsoportot és a virtuális gépet.

Ha egy Biztonsági mentéssel foglalkozó oktatóanyaggal folytatja, amely a virtuális gép adatainak visszaállítását ismerteti, ugorja át az ebben a szakaszban lévő lépéseket, és ugorjon a [Következő lépések](#next-steps) részre. 

```powershell
Disable-AzureRmRecoveryServicesBackupProtection -Item $item -RemoveRecoveryPoints
$vault = Get-AzureRmRecoveryServicesVault -Name "myRecoveryServicesVault"
Remove-AzureRmRecoveryServicesVault -Vault $vault
Remove-AzureRmResourceGroup -Name "myResourceGroup"
```


## <a name="next-steps"></a>További lépések
A rövid útmutató során létrehozott egy Recovery Services-tárolót, engedélyezte a védelmet a virtuális gépen, valamint létrehozta a kezdeti helyreállítási pontot. Ha bővebb információra van szüksége az Azure Backup és a Recovery Services szolgáltatásokkal kapcsolatban, lépjen tovább a következő oktatóanyagokra.

> [!div class="nextstepaction"]
> [Biztonsági mentés készítése több Azure-beli virtuális gépről](./tutorial-backup-vm-at-scale.md)
