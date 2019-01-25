---
title: Azure PowerShell-példaszkript – Log Analytics | Microsoft Docs
description: Azure PowerShell-példaszkript – Log Analytics
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/01/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: d2b9b2a4940d749fcad8c885cc2e02ab8cf1870f
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54886212"
---
# <a name="create-a-log-analytics-monitored-vm-with-powershell"></a>Log Analytics által monitorozott virtuális gép létrehozása a PowerShell-lel

Ez a szkript egy Azure-beli virtuális gépet hoz létre, telepíti a Log Analytics-ügynököt, és regisztrálja a rendszert egy Log Analytics-munkaterületen. A szkript futtatását követően a virtuális gép megjelenik a konzolon.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-monitor-oms/create-vm-monitor-oms.ps1 "Create VM")]

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
| [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) | Alhálózati konfigurációt hoz létre. Ez a konfiguráció a virtuális hálózat létrehozására szolgál. |
| [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) | Virtuális hálózatot hoz létre. |
| [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | Egy nyilvános IP-címet hoz létre. |
| [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) | Egy hálózati biztonsági csoport (NSG) szabálykonfigurációját hozza létre. Ez a konfiguráció az NSG-re vonatkozó szabály létrehozására szolgál az NSG létrehozásakor. |
| [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) | Egy hálózati biztonsági csoportot hoz létre. |
| [Get-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig) | Lekéri az alhálózattal kapcsolatos információkat. Ezeket az adatokat a rendszer hálózati adapter létrehozásakor használja. |
| [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) | Hálózati adaptert hoz létre. |
| [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) | Egy virtuálisgép-konfigurációt hoz létre. Ebben a konfigurációban olyan információk szerepelnek, mint a virtuális gép neve, az operációs rendszer és a rendszergazdai hitelesítő adatok. A rendszer a virtuális gépek létrehozása során használja ezt a konfigurációt. |
| [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) | Virtuális gépet hoz létre. |
| [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) | Egy virtuálisgép-bővítményt ad hozzá a virtuális géphez. Ebben az esetben a Log Analytics-ügynökbővítmény telepíti a Log Analytics-ügynököt, és regisztrálja a virtuális gépet egy Log Analytics-munkaterületen. |
|[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Eltávolít egy erőforráscsoportot és az összes abban található erőforrást. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

A virtuális gépekhez kapcsolódó további PowerShell-példaszkripteket az [Azure Linux rendszerű virtuális gépekre vonatkozó dokumentációjában](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) találhat.
