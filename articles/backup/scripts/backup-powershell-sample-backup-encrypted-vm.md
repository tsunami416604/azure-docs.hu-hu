---
title: Az Azure PowerShell-Példaszkript – egy Azure virtuális gép biztonsági mentése |} A Microsoft Docs
description: Az Azure PowerShell-Példaszkript – egy Azure virtuális gép biztonsági mentése
services: backup
documentationcenter: ''
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: sample
ms.date: 03/05/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: bc54832b300bf7a70d067f07b9eb7cc67404f2e7
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58496803"
---
# <a name="back-up-an-encrypted-azure-virtual-machine-with-powershell"></a>Biztonsági mentése a PowerShell-lel titkosított Azure virtuális gépeken

Ez a szkript létrehoz egy Recovery Services-tárolót a georedundáns tárolás (GRS) titkosított Azure virtuális gépként. Az alapértelmezett védelmi házirend a tárolóhoz van alkalmazva. A szabályzat létrehoz egy napi biztonsági mentést a virtuális gép, és 30 napig őrzi meg az egyes biztonsági mentéséhez. A parancsfájl is elindítja a kezdeti helyreállítási pontot a virtuális gép, és 365 napig őrzi meg a helyreállítási pontban.

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
| [New-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) | Létrehoz egy recovery services-tárolót a biztonsági másolatok tárolására. | 
| [Set-AzRecoveryServicesBackupProperties](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperties) | Csoportok biztonsági mentési tároló tulajdonságainak a Recovery Services-tároló. | 
| [New-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy)| Alkalmazásvédelmi szabályzat ütemezése házirenddel és adatmegőrzési hoz létre a Recovery Services-tárolóban. | 
| [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) | Beállítja a jogosultságokat a Key Vault-tárolóban, hogy a szolgáltatásnév hozzáférhessen a titkosítási kulcsokhoz. | 
| [Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) | Lehetővé teszi, hogy a megadott biztonsági mentési házirenddel rendelkező elem biztonsági mentését. | 
| [Set-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy)| Módosítja egy meglévő biztonsági mentési alkalmazásvédelmi szabályzatot. | 
| [Backup-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem) | Elindul a biztonsági mentés egy védett Azure biztonsági mentési elem, amely a biztonsági mentés ütemezése nem kötődik. |
| [Wait-AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) | Az Azure biztonsági mentési feladat befejeződésére vár. | 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Eltávolít egy erőforráscsoportot és az összes abban található erőforrást. | 

## <a name="next-steps"></a>További lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](https://docs.microsoft.com/powershell/azure/new-azureps-module-az).

