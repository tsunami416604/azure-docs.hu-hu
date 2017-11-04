---
title: "Az Azure PowerShell-parancsfájl minták – egy Windows virtuális gép titkosítása |} Microsoft Docs"
description: "Az Azure PowerShell-parancsfájl minták – egy Windows virtuális gép titkosítása"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/02/2017
ms.author: iainfou
ms.openlocfilehash: 9279fea482fcd8716bcd996985e10f500a4775ce
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="encrypt-a-windows-virtual-machine-with-azure-powershell"></a>Az Azure PowerShell Windows virtuális gépek titkosítása

Ez a parancsfájl létrehoz egy biztonságos Azure Key Vault, a titkosítási kulcsokat, az Azure Active Directory szolgáltatás egyszerű és a Windows rendszerű virtuális gép (VM). A virtuális Gépet a rendszer ezután titkosítja a Key Vault és a szolgáltatás egyszerű hitelesítő adatait a titkosítási kulcs használatával.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Mintaparancsfájl

[!code-powershell[main](../../../powershell_scripts/virtual-machine/encrypt-vm/encrypt-windows-vm.ps1 "Encrypt VM disks")]

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
| [Új-AzureRmKeyVault](/powershell/module/azurerm.keyvault/new-azurermkeyvault) | Létrehoz egy Azure Key Vault, például a titkosítási kulcsok biztonságos adatok tárolására. |
| [-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/add-azurekeyvaultkey) | A Key Vault egy titkosítási kulcsot hoz létre. |
| [Új AzureRmADServicePrincipal](/powershell/module/azurerm.resources/new-azurermadserviceprincipal) | Létrehoz egy Azure Active Directory szolgáltatás egyszerű biztonságosan hitelesítéséhez és titkosítási kulcsok való hozzáférés szabályozása. |
| [Set-AzureRmKeyVaultAccessPolicy](/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy) | A szolgáltatás egyszerű hozzáférést biztosít a titkosítási kulcsokat a Key Vault engedélyeinek beállítása. |
| [Új AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) | Létrehoz egy alhálózati konfigurációt. Ezt a konfigurációt használja a virtuális hálózat létrehozásának folyamatát. |
| [Új-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) | Virtuális hálózat létrehozása. |
| [Új AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | Létrehoz egy nyilvános IP-címet. |
| [Új AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) | Létrehoz egy hálózati biztonsági csoport szabály konfigurációt. Ez a konfiguráció segítségével egy NSG-szabály létrehozása az NSG létrehozásakor. |
| [Új AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) | Hálózati biztonsági csoportot hoz létre. |
| [Get-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig) | Lekérdezi az alhálózati adatokat. Ezt az információt a hálózati illesztő létrehozása során használatos. |
| [Új AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) | Létrehoz egy adott hálózati csatoló. |
| [Új AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) | Létrehoz egy Virtuálisgép-konfiguráció. Ez a konfiguráció tartoznak a virtuális gép nevét, az operációs rendszer és a rendszergazdai hitelesítő adatokkal. A konfiguráció a Virtuálisgép-létrehozása során használatos. |
| [Új AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) | Hozzon létre egy virtuális gépet. |
| [Get-AzureRmKeyVault](/powershell/module/azurerm.keyvault/get-azurermkeyvault) | Lekérdezi a Key Vault szükséges információk |
| [Set-AzureRmVMDiskEncryptionExtension](/powershell/module/azurerm.compute/set-azurermvmdiskencryptionextension) | Engedélyezi a titkosítás használatát a virtuális gépek, a szolgáltatás egyszerű hitelesítő adatait és a titkosítási kulcs segítségével. |
| [Get-AzureRmVmDiskEncryptionStatus](/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus) | A virtuális gép titkosítási folyamat állapotát jeleníti meg. |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Eltávolítja az erőforráscsoportot és belül található összes erőforrást. |

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShell modul további információkért lásd: [Azure PowerShell dokumentációs](/powershell/azure/overview).

További virtuális gép PowerShell-parancsfájl példák találhatók a [Azure Windows virtuális dokumentációját](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
