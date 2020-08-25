---
title: PowerShell-parancsfájl minta – Azure-beli virtuális gép biztonsági mentése
description: Ebből a cikkből megtudhatja, hogyan használhat Azure PowerShell parancsfájl-mintát egy Azure-beli virtuális gép biztonsági mentésére.
ms.topic: sample
ms.date: 03/05/2019
ms.custom: mvc
ms.openlocfilehash: 755c4dbfffbd4e083c591d64c0f156e15f6e3780
ms.sourcegitcommit: ac7ae29773faaa6b1f7836868565517cd48561b2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/25/2020
ms.locfileid: "88826582"
---
# <a name="back-up-an-encrypted-azure-virtual-machine-with-powershell"></a>Titkosított Azure-beli virtuális gép biztonsági mentése a PowerShell-lel

Ez a szkript létrehoz egy Recovery Services-tárolót egy geo-redundáns tárolással (GRS) egy titkosított Azure-beli virtuális géphez. Az alapértelmezett védelmi házirend a tárolóra lesz alkalmazva. A házirend napi biztonsági mentést készít a virtuális géphez, és 30 napig őrzi meg az egyes biztonsági másolatokat. A parancsfájl a virtuális gép kezdeti helyreállítási pontját is elindítja, és 365 napig megőrzi a helyreállítási pontot.

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

| Parancs | Jegyzetek |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [Új – AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) | Létrehoz egy Recovery Services tárolót a biztonsági másolatok tárolásához. |
| [Set-AzRecoveryServicesBackupProperty](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) | A biztonsági mentési tár tulajdonságainak beállítása Recovery Services-tárolón. |
| [Új – AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy)| Védelmi házirendet hoz létre a Recovery Services-tárolóban ütemezett házirend és adatmegőrzési szabályzat használatával. |
| [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) | Beállítja a jogosultságokat a Key Vault-tárolóban, hogy a szolgáltatásnév hozzáférhessen a titkosítási kulcsokhoz. |
| [AzRecoveryServicesBackupProtection engedélyezése](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) | Lehetővé teszi egy adott biztonsági mentési szabályzattal rendelkező elem biztonsági mentését. |
| [Set-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy)| Egy meglévő biztonsági mentési védelmi szabályzatot módosít. |
| [Backup – AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem) | Elindít egy biztonsági mentést a védett Azure Backup olyan eleme számára, amely nincs a biztonsági mentési ütemtervhez kötve. |
| [Várakozás – AzRecoveryServicesBackupJob](/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) | Egy Azure Backup-feladatokra vár. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Eltávolít egy erőforráscsoportot és az összes abban található erőforrást. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/new-azureps-module-az).
