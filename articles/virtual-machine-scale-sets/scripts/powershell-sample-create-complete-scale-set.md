---
title: Azure PowerShell-minták – Teljes virtuálisgép-méretezési csoport létrehozása | Microsoft Docs
description: Azure PowerShell-minták
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/29/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: c92b922adeb2e3cd80a271062704feb3bdb4c949
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38652156"
---
# <a name="create-a-complete-virtual-machine-scale-set-with-powershell"></a>Teljes virtuálisgép-méretezési csoport létrehozása PowerShell segítségével
Ez a szkript létrehoz egy Windows Server 2016 rendszert futtató virtuálisgép-méretezési csoportot. A rendszer az egyes erőforrásokat konfigurálja és hozza létre ahelyett, hogy a [New-AzureRmVmss elérhető beépített erőforrás-létrehozási lehetőségeit használná](powershell-sample-create-simple-scale-set.md). A szkript futtatása után a virtuálisgép-példányokat RDP-kapcsolaton keresztül érheti el.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript
[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/complete-scale-set/complete-scale-set.ps1 "Create a complete virtual machine scale set")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása
Az alábbi paranccsal eltávolítható az erőforráscsoport, a méretezési csoport és az összes kapcsolódó erőforrás.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroupName
```

## <a name="script-explanation"></a>Szkript ismertetése
A szkript a következő parancsokat használja az üzemelő példány létrehozásához. A táblázatban lévő összes elem a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) | Alhálózati konfigurációt hoz létre. Ez a konfiguráció a virtuális hálózat létrehozására szolgál. |
| [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) | Virtuális hálózatot hoz létre. |
| [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | Egy nyilvános IP-címet hoz létre. |
| [New-AzureRmLoadBalancerFrontendIpConfig](/powershell/module/azurerm.network/new-azurermloadbalancerfrontendipconfig) | Létrehozza egy terheléselosztó előtérbeli IP-konfigurációját. |
| [New-AzureRmLoadBalancerBackendAddressPoolConfig](/powershell/module/azurerm.network/new-azurermloadbalancerbackendaddresspoolconfig) | Létrehozza egy terheléselosztó háttércímkészlet-konfigurációját. |
| [New-AzureRmLoadBalancerInboundNatRuleConfig](/powershell/module/azurerm.network/new-azurermloadbalancerinboundnatruleconfig) | Létrehoz egy bejövő NAT-szabálykonfigurációt egy terheléselosztó számára. |
| [New-AzureRmLoadBalancer](/powershell/module/azurerm.network/new-azurermloadbalancer) | Létrehoz egy terheléselosztót. |
| [Add-AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/new-azurermloadbalancerprobeconfig) | Létrehoz egy mintavételi konfigurációt egy terheléselosztó számára. |
| [Add-AzureRmLoadBalancerRuleConfig](/powershell/module/azurerm.network/new-azurermloadbalancerruleconfig) | Létrehoz egy szabálykonfigurációt egy terheléselosztó számára. |
| [Set-AzureRmLoadBalancer](/powershell/module/AzureRM.Network/Set-AzureRmLoadBalancer) | Frissíti a terheléselosztót a megadott információkkal. |
| [New-AzureRmVmssIpConfig](/powershell/module/AzureRM.Compute/New-AzureRmVmssIpConfig) | IP-konfigurációt hoz létre a méretezési csoport virtuálisgép-példányaihoz. A virtuálisgép-példányok a terheléselosztó háttérkészletéhez, a NAT-készlethez és a virtuális hálózat alhálózatához csatlakoznak. |
| [New-AzureRmVmssConfig](/powershell/module/AzureRM.Compute/New-AzureRmVmssConfig) | Méretezésicsoport-konfigurációt hoz létre. Ebbe a konfigurációba olyan információk tartoznak, mint a létrehozni kívánt virtuálisgép-példányok száma, a virtuális gép SKU (méret) és a frissítési szabályzat módja. A konfiguráció további parancsmagokkal van hozzáadva, és a méretezési csoport létrehozása során használatos. |
| [Set-AzureRmVmssStorageProfile](/powershell/module/AzureRM.Compute/Set-AzureRmVmssStorageProfile) | Meghatározza a virtuálisgép-példányokhoz használni kívánt rendszerképet, és hozzáadja azt a méretezési csoport konfigurációjához. |
| [Set-AzureRmVmssOsProfile](/powershell/module/AzureRM.Compute/Set-AzureRmVmssStorageProfile) | Megadja a rendszergazdai felhasználónevet és jelszót, valamint a virtuális gép elnevezési előtagját. Ezeket az értékeket hozzáadja a méretezési csoport konfigurációjához. |
| [Add-AzureRmVmssNetworkInterfaceConfiguration](/powershell/module/AzureRM.Compute/Add-AzureRmVmssNetworkInterfaceConfiguration) | Virtuális hálózati adaptert ad hozzá a virtuálisgép-példányokhoz az IP-konfiguráció alapján. Ezeket az értékeket hozzáadja a méretezési csoport konfigurációjához. |
| [New-AzureRmVmss](/powershell/module/AzureRM.Compute/New-AzureRmVmss) | Létrehozza a méretezési csoportot a méretezési csoport konfigurációjában megadott információk alapján. |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Eltávolít egy erőforráscsoportot és az összes abban található erőforrást. |

## <a name="next-steps"></a>További lépések
Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

A virtuálisgép-méretezési csoportokhoz használható további PowerShell-példaszkripteket az [Azure virtuálisgép-méretezési csoportokra vonatkozó dokumentációjában](../powershell-samples.md) találhat.
