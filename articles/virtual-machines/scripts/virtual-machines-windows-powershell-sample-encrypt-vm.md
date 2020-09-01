---
title: Azure PowerShell-példaszkript – Windows rendszerű virtuális gép titkosítása
description: Azure PowerShell-példaszkript – Windows rendszerű virtuális gép titkosítása
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/12/2017
ms.author: cynthn
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 102187d52ec86a7a87223975ae3d1f6bbe9097d7
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2020
ms.locfileid: "89078775"
---
# <a name="encrypt-a-windows-virtual-machine-with-azure-powershell"></a>Windows rendszerű virtuális gép titkosítása az Azure PowerShell-lel

Ez a szkript egy biztonságos Azure Key Vault-tárolót, titkosító kulcsokat, egy Azure Active Directory-szolgáltatásnevet és egy Windows rendszerű virtuális gépet (VM) hoz létre. A virtuális gépet ezután a Key Vaultból származó titkosítási kulccsal és a szolgáltatásnév hitelesítő adataival titkosítjuk.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/encrypt-vm/encrypt-windows-vm.ps1 "Encrypt VM disks")]

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
| [Új – AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault) | Létrehoz egy Azure Key Vault-tárolót a védett adatok (például titkosítási kulcsok) tárolásához. |
| [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) | Létrehoz egy titkosítási kulcsot a Key Vault-tárolóban. |
| [Új – AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal) | Létrehoz egy Azure Active Directory-szolgáltatásnevet a titkosítási kulcsok biztonságos hitelesítéséhez és elérésének szabályozásához. |
| [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) | Beállítja a jogosultságokat a Key Vault-tárolóban, hogy a szolgáltatásnév hozzáférhessen a titkosítási kulcsokhoz. |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | Létrehozza a virtuális gépet, és csatlakoztatja a hálózati kártyához, virtuális hálózathoz, alhálózathoz és hálózati biztonsági csoporthoz. A parancs emellett megnyitja a 80-as portot, és beállítja a felügyeleti hitelesítő adatokat. |
| [Get-AzKeyVault](/powershell/module/az.keyvault/get-azkeyvault) | Lekéri a Key Vault szükséges adatait. |
| [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) | Bekapcsolja egy virtuális gép titkosítását a szolgáltatásnév hitelesítési adataival és titkosítási kulcsával. |
| [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) | Megmutatja a virtuális gép titkosítási folyamatának állapotát. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Eltávolít egy erőforráscsoportot és az összes abban található erőforrást. |

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/).

A virtuális gépekhez kapcsolódó további PowerShell-példaszkripteket az [Azure Windows rendszerű virtuális gépekre vonatkozó dokumentációjában](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) találhat.
