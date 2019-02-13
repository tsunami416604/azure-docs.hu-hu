---
title: Azure PowerShell-példaszkript – Windows rendszerű virtuális gép titkosítása | Microsoft Docs
description: Azure PowerShell-példaszkript – Windows rendszerű virtuális gép titkosítása
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/12/2017
ms.author: cynthn
ms.openlocfilehash: 3a3bfe80f251c6a11e19f33adb915d20b9819784
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56116031"
---
# <a name="encrypt-a-windows-virtual-machine-with-azure-powershell"></a>Windows rendszerű virtuális gép titkosítása az Azure PowerShell-lel

Ez a szkript egy biztonságos Azure Key Vault-tárolót, titkosító kulcsokat, egy Azure Active Directory-szolgáltatásnevet és egy Windows rendszerű virtuális gépet (VM) hoz létre. A virtuális gépet ezután a Key Vaultból származó titkosítási kulccsal és a szolgáltatásnév hitelesítő adataival titkosítjuk.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/encrypt-vm/encrypt-windows-vm.ps1 "Encrypt VM disks")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

Az alábbi paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja az üzemelő példány létrehozásához. A táblázatban lévő összes elem a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [New-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/new-azkeyvault) | Létrehoz egy Azure Key Vault-tárolót a védett adatok (például titkosítási kulcsok) tárolásához. |
| [Add-AzureKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultkey) | Létrehoz egy titkosítási kulcsot a Key Vault-tárolóban. |
| [New-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal) | Létrehoz egy Azure Active Directory-szolgáltatásnevet a titkosítási kulcsok biztonságos hitelesítéséhez és elérésének szabályozásához. |
| [Set-AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) | Beállítja a jogosultságokat a Key Vault-tárolóban, hogy a szolgáltatásnév hozzáférhessen a titkosítási kulcsokhoz. |
| [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) | Létrehozza a virtuális gépet, és csatlakoztatja a hálózati kártyához, virtuális hálózathoz, alhálózathoz és hálózati biztonsági csoporthoz. A parancs emellett megnyitja a 80-as portot, és beállítja a felügyeleti hitelesítő adatokat. |
| [Get-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/get-azkeyvault) | Lekéri a Key Vault szükséges adatait. |
| [Set-AzVMDiskEncryptionExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdiskencryptionextension) | Bekapcsolja egy virtuális gép titkosítását a szolgáltatásnév hitelesítési adataival és titkosítási kulcsával. |
| [Get-AzVmDiskEncryptionStatus](https://docs.microsoft.com/powershell/module/az.compute/get-azvmdiskencryptionstatus) | Megmutatja a virtuális gép titkosítási folyamatának állapotát. |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Eltávolít egy erőforráscsoportot és az összes abban található erőforrást. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

A virtuális gépekhez kapcsolódó további PowerShell-példaszkripteket az [Azure Windows rendszerű virtuális gépekre vonatkozó dokumentációjában](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) találhat.
