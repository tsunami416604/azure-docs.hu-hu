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
ms.openlocfilehash: 22de937c33edc7c8ec6944acf657937a1ce604f7
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2019
ms.locfileid: "55981894"
---
# <a name="create-a-complete-virtual-machine-scale-set-with-powershell"></a>Teljes virtuálisgép-méretezési csoport létrehozása PowerShell segítségével

Ez a szkript létrehoz egy Windows Server 2016 rendszert futtató virtuálisgép-méretezési csoportot. Az egyéni erőforrások konfigurált és hozta létre, a használata helyett a [beépített erőforrás létrehozási beállítások érhető el itt a New-AzVmss](powershell-sample-create-simple-scale-set.md). A szkript futtatása után a virtuálisgép-példányokat RDP-kapcsolaton keresztül érheti el.


[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/complete-scale-set/complete-scale-set.ps1 "Create a complete virtual machine scale set")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása
Az alábbi paranccsal eltávolítható az erőforráscsoport, a méretezési csoport és az összes kapcsolódó erőforrás.

```powershell
Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="script-explanation"></a>Szkript ismertetése
A szkript a következő parancsokat használja az üzemelő példány létrehozásához. A táblázatban lévő összes elem a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Alhálózati konfigurációt hoz létre. Ez a konfiguráció a virtuális hálózat létrehozására szolgál. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Virtuális hálózatot hoz létre. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Egy nyilvános IP-címet hoz létre. |
| [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) | Létrehozza egy terheléselosztó előtérbeli IP-konfigurációját. |
| [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) | Létrehozza egy terheléselosztó háttércímkészlet-konfigurációját. |
| [New-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) | Létrehoz egy bejövő NAT-szabálykonfigurációt egy terheléselosztó számára. |
| [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) | Létrehoz egy terheléselosztót. |
| [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig) | Létrehoz egy mintavételi konfigurációt egy terheléselosztó számára. |
| [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig) | Létrehoz egy szabálykonfigurációt egy terheléselosztó számára. |
| [Set-AzLoadBalancer](/powershell/module/az.Network/Set-azLoadBalancer) | Frissíti a terheléselosztót a megadott információkkal. |
| [New-AzVmssIpConfig](/powershell/module/az.Compute/New-azVmssIpConfig) | IP-konfigurációt hoz létre a méretezési csoport virtuálisgép-példányaihoz. A virtuálisgép-példányok a terheléselosztó háttérkészletéhez, a NAT-készlethez és a virtuális hálózat alhálózatához csatlakoznak. |
| [New-AzVmssConfig](/powershell/module/az.Compute/New-azVmssConfig) | Méretezésicsoport-konfigurációt hoz létre. Ebbe a konfigurációba olyan információk tartoznak, mint a létrehozni kívánt virtuálisgép-példányok száma, a virtuális gép SKU (méret) és a frissítési szabályzat módja. A konfiguráció további parancsmagokkal van hozzáadva, és a méretezési csoport létrehozása során használatos. |
| [Set-AzVmssStorageProfile](/powershell/module/az.Compute/Set-azVmssStorageProfile) | Meghatározza a virtuálisgép-példányokhoz használni kívánt rendszerképet, és hozzáadja azt a méretezési csoport konfigurációjához. |
| [Set-AzVmssOsProfile](/powershell/module/az.Compute/Set-azVmssStorageProfile) | Megadja a rendszergazdai felhasználónevet és jelszót, valamint a virtuális gép elnevezési előtagját. Ezeket az értékeket hozzáadja a méretezési csoport konfigurációjához. |
| [Add-AzVmssNetworkInterfaceConfiguration](/powershell/module/az.Compute/Add-azVmssNetworkInterfaceConfiguration) | Virtuális hálózati adaptert ad hozzá a virtuálisgép-példányokhoz az IP-konfiguráció alapján. Ezeket az értékeket hozzáadja a méretezési csoport konfigurációjához. |
| [New-AzVmss](/powershell/module/az.Compute/New-azVmss) | Létrehozza a méretezési csoportot a méretezési csoport konfigurációjában megadott információk alapján. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Eltávolít egy erőforráscsoportot és az összes abban található erőforrást. |

## <a name="next-steps"></a>További lépések
Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

A virtuálisgép-méretezési csoportokhoz használható további PowerShell-példaszkripteket az [Azure virtuálisgép-méretezési csoportokra vonatkozó dokumentációjában](../powershell-samples.md) találhat.
