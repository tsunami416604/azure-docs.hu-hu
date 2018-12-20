---
title: Az Azure PowerShell-Példaszkript – egy Azure virtuális gép biztonsági mentése |} A Microsoft Docs
description: Az Azure PowerShell-Példaszkript – egy Azure virtuális gép biztonsági mentése
services: backup
documentationcenter: ''
author: rayne-wiselman
manager: carmonm
editor: ''
tags: ''
ms.assetid: ''
ms.service: backup
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/07/2017
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: eb22dc88c971e0ddc293fabd64bfd30145b2edd1
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/20/2018
ms.locfileid: "53651384"
---
# <a name="back-up-an-encrypted-azure-virtual-machine-with-powershell"></a>Biztonsági mentése a PowerShell-lel titkosított Azure virtuális gépeken

Ez a szkript létrehoz egy Recovery Services-tárolót a georedundáns tárolás (GRS) titkosított Azure virtuális gépként. Az alapértelmezett védelmi házirend a tárolóhoz van alkalmazva. A szabályzat létrehoz egy napi biztonsági mentést a virtuális gép, és 30 napig őrzi meg az egyes biztonsági mentéséhez. A parancsfájl is elindítja a kezdeti helyreállítási pontot a virtuális gép, és 365 napig őrzi meg a helyreállítási pontban. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/backup/backup-encrypted-vm/backup-encrypted-vm.ps1 "Back up encrypted virtual machine")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

Az alábbi paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja az üzemelő példány létrehozásához. A táblázatban lévő összes elem a hozzá tartozó dokumentációra hivatkozik.


| Parancs | Megjegyzések | 
|---|---| 
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. | 
| [Új AzureRmRecoveryServicesVault](/powershell/module/azurerm.recoveryservices/New-AzureRmRecoveryServicesVault) | Létrehoz egy recovery services-tárolót a biztonsági másolatok tárolására. | 
| [Set-AzureRmRecoveryServicesBackupProperties](/powershell/module/azurerm.recoveryservices/Set-AzureRmRecoveryServicesBackupProperties) | Csoportok biztonsági mentési tároló tulajdonságainak a Recovery Services-tároló. | 
| [Új-AzureRmRecoveryServicesBackupProtectionPolicy](/powershell/module/azurerm.recoveryservices.backup/new-azurermrecoveryservicesbackupprotectionpolicy)| Alkalmazásvédelmi szabályzat ütemezése házirenddel és adatmegőrzési hoz létre a Recovery Services-tárolóban. | 
| [Set-AzureRmKeyVaultAccessPolicy](/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy) | Beállítja a jogosultságokat a Key Vault-tárolóban, hogy a szolgáltatásnév hozzáférhessen a titkosítási kulcsokhoz. | 
| [Enable-AzureRmRecoveryServicesBackupProtection](/powershell/module/azurerm.recoveryservices.backup/enable-azurermrecoveryservicesbackupprotection) | Lehetővé teszi, hogy a megadott biztonsági mentési házirenddel rendelkező elem biztonsági mentését. | 
| [Set-AzureRmRecoveryServicesBackupProtectionPolicy](/powershell/module/azurerm.recoveryservices.backup/set-azurermrecoveryservicesbackupprotectionpolicy)| Módosítja egy meglévő biztonsági mentési alkalmazásvédelmi szabályzatot. | 
| [Backup-AzureRmRecoveryServicesBackupItem](/powershell/module/azurerm.recoveryservices.backup/backup-azurermrecoveryservicesbackupitem) | Elindul a biztonsági mentés egy védett Azure biztonsági mentési elem, amely a biztonsági mentés ütemezése nem kötődik. |
| [Wait-AzureRmRecoveryServicesBackupJob](/powershell/module/azurerm.recoveryservices.backup/wait-azurermrecoveryservicesbackupjob) | Az Azure biztonsági mentési feladat befejeződésére vár. | 
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Eltávolít egy erőforráscsoportot és az összes abban található erőforrást. | 

## <a name="next-steps"></a>További lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

