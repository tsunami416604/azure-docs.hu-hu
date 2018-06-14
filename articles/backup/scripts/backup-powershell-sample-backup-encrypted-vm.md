---
title: Az Azure PowerShell-parancsfájl a minta - biztonsági mentése Azure virtuális gép |} Microsoft Docs
description: Az Azure PowerShell-parancsfájl a minta - Azure virtuális gép biztonsági mentése
services: backup
documentationcenter: ''
author: markgalioto
manager: carmonm
editor: ''
tags: ''
ms.assetid: ''
ms.service: backup
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/07/2017
ms.author: markgal
ms.custom: mvc
ms.openlocfilehash: 4376add4a2e51806bd5db228ad2fe2afcf2e4f57
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23842649"
---
# <a name="back-up-an-encrypted-azure-virtual-machine-with-powershell"></a>Készítsen biztonsági másolatot a PowerShell használatával titkosított Azure virtuális géphez

Ezt a parancsfájlt hoz létre a Recovery Services-tároló georedundáns tárolás (GRS) egy titkosított Azure virtuális géphez. Az alapértelmezett védelmi házirend vonatkozik, az a tárolóban. A házirendet hoz létre a napi biztonsági mentés a virtuális gép, és 30 napig őrzi meg minden egyes biztonsági másolat. A parancsfájl is elindítja az első helyreállítási pont, a virtuális géphez, és 365 nap őrzi meg a helyreállítási pont. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Mintaparancsfájl

[!code-powershell[main](../../../powershell_scripts/backup/backup-encrypted-vm/backup-encrypted-vm.ps1 "Back up encrypted virtual machine")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

A következő parancsot az erőforráscsoport, virtuális gép és az összes kapcsolódó erőforrások eltávolítása.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Parancsfájl ismertetése

A parancsfájl a következő parancsokat a központi telepítés létrehozásához. A parancs adott dokumentáció tábla mutató összes elemére.


| Parancs | Megjegyzések | 
|---|---| 
| [Új-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Az összes erőforrás tároló erőforrás csoportot hoz létre. | 
| [Új AzureRmRecoveryServicesVault](/powershell/module/azurerm.recoveryservices/New-AzureRmRecoveryServicesVault) | Létrehoz egy recovery services-tároló biztonsági másolatok tárolására. | 
| [Set-AzureRmRecoveryServicesBackupProperties](/powershell/module/azurerm.recoveryservices/Set-AzureRmRecoveryServicesBackupProperties) | A biztonsági mentés a Recovery Services-tárolónak a tároló tulajdonságainak beállítása. | 
| [Új AzureRmRecoveryServicesBackupProtectionPolicy](/powershell/module/azurerm.recoveryservices.backup/new-azurermrecoveryservicesbackupprotectionpolicy)| Védelmi házirend ütemezés házirenddel és adatmegőrzési hoz létre a Recovery Services-tároló. | 
| [Set-AzureRmKeyVaultAccessPolicy](/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy) | A szolgáltatás egyszerű hozzáférést biztosít a titkosítási kulcsokat a Key Vault engedélyeinek beállítása. | 
| [Enable-AzureRmRecoveryServicesBackupProtection](/powershell/module/azurerm.recoveryservices.backup/enable-azurermrecoveryservicesbackupprotection) | Lehetővé teszi, hogy a megadott biztonsági mentési házirenddel rendelkező elem biztonsági mentését. | 
| [Set-AzureRmRecoveryServicesBackupProtectionPolicy](/powershell/module/azurerm.recoveryservices.backup/set-azurermrecoveryservicesbackupprotectionpolicy)| Egy meglévő biztonsági mentés védelmi házirend módosítása. | 
| [Biztonsági mentés-AzureRmRecoveryServicesBackupItem](/powershell/module/azurerm.recoveryservices.backup/backup-azurermrecoveryservicesbackupitem) | Elindítja a biztonsági mentés egy védett Azure biztonsági mentési elem, amely nem kapcsolódik a biztonsági mentés ütemezése. |
| [Várjon, amíg-AzureRmRecoveryServicesBackupJob](/powershell/module/azurerm.recoveryservices.backup/wait-azurermrecoveryservicesbackupjob) | Az Azure biztonsági mentési feladat befejeződésére vár. | 
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Eltávolítja az erőforráscsoportot és belül található összes erőforrást. | 

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShell modul további információkért lásd: [Azure PowerShell dokumentációs](/powershell/azure/overview).

