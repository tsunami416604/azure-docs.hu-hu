---
title: PowerShell-parancsfájlminta – Egy Azure virtuális gép biztonsági és biztonsági másolatot
description: Ebben a cikkben megtudhatja, hogyan használhatja az Azure PowerShell-parancsfájl-mintát egy Azure virtuális gép biztonsági másolatot.
ms.topic: sample
ms.date: 03/05/2019
ms.custom: mvc
ms.openlocfilehash: ad4d290961bd39659283795f6a58ccb815b639b0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74171804"
---
# <a name="back-up-an-encrypted-azure-virtual-machine-with-powershell"></a>Titkosított Azure-alapú virtuális gép biztonsági és biztonsági másolatot készül a PowerShell használatával

Ez a parancsfájl létrehoz egy Recovery Services-tároló georedundáns tárolással (GRS) egy titkosított Azure virtuális gép. Az alapértelmezett védelmi házirend a tárolóra vonatkozik. A házirend napi biztonsági mentést hoz létre a virtuális gép számára, és 30 napig megőrzi az egyes biztonsági mentéseket. A parancsfájl is elindítja a virtuális gép kezdeti helyreállítási pontját, és 365 napig megőrzi ezt a helyreállítási pontot.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/backup/backup-encrypted-vm/backup-encrypted-vm.ps1 "Back up encrypted virtual machine")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

Az alábbi paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja az üzemelő példány létrehozásához. A táblázatban lévő összes elem a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [Új-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) | Létrehoz egy helyreállítási szolgáltatások tárolóját a biztonsági mentések tárolásához. |
| [Set-AzRecoveryServicesBackuptulajdonság](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) | Beállítja a biztonsági mentési tároló tulajdonságait a Recovery Services tárolóban. |
| [Új-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy)| Védelmi házirendet hoz létre ütemezési és adatmegőrzési házirend használatával a Recovery Services tárolóban. |
| [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) | Beállítja a jogosultságokat a Key Vault-tárolóban, hogy a szolgáltatásnév hozzáférhessen a titkosítási kulcsokhoz. |
| [Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) | Adott biztonsági mentésvédelmi házirenddel rendelkező elem biztonsági mentésének engedélyezése. |
| [Set-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy)| Módosítja a meglévő biztonsági mentési védelmi házirendet. |
| [Backup-AzRecoveryServicesBackupitem](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem) | Elindítja a biztonsági mentést egy védett Azure Backup elem, amely nincs kötve a biztonsági mentésütemezés. |
| [Wait-AzRecoveryServicesBackupjob](https://docs.microsoft.com/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) | Megvárja, amíg befejeződik az Azure Backup-feladat. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Eltávolít egy erőforráscsoportot és az összes abban található erőforrást. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](https://docs.microsoft.com/powershell/azure/new-azureps-module-az).
