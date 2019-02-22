---
title: Azure PowerShell-példaszkript – Forgalom irányítása hálózati virtuális készüléken keresztül | Microsoft Docs
description: Azure PowerShell-példaszkript – Forgalom irányítása hálózati virtuális készüléken keresztül.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/20/2018
ms.author: jdial
ms.openlocfilehash: 56b526b186acdd0903910a5a10f5fa3aa3c454df
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2019
ms.locfileid: "56650070"
---
# <a name="route-traffic-through-a-network-virtual-appliance-script-sample"></a>Forgalom irányítása hálózati virtuális készüléken keresztül – példaszkript

Ez a példaszkript előtérbeli és háttérbeli alhálózattal rendelkező virtuális hálózatot hoz létre. Ezen kívül létrehoz egy virtuális gépet, amelyen az IP-továbbítás két alhálózat közötti útválasztása engedélyezve van. A szkript futtatása után hálózati szoftvereket, például tűzfal-alkalmazást telepíthet a virtuális gépre.

A szkriptet az Azure [Cloud Shellben](https://shell.azure.com/powershell) vagy egy helyi PowerShell-telepítésből futtathatja. Ha helyileg használja a Powershellt, ehhez a szkripthez a Az PowerShell-modul 5.4.1-es vagy újabb. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/virtual-network/route-traffic-through-nva/route-traffic-through-nva.ps1 "Route traffic through a network virtual appliance")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

Az alábbi paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás:

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript az alábbi parancsokkal létrehoz egy erőforráscsoportot, egy virtuális hálózatot és hálózati biztonsági csoportokat. Az alábbi táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik:

| Parancs | Megjegyzések |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)  | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Létrehoz egy Azure-beli virtuális hálózatot és előtérbeli alhálózatot. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Háttérbeli és DMZ-alhálózatokat hoz létre. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Nyilvános IP-címet hoz létre, amely lehetővé teszi a virtuális gép elérését az internetről. |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | Virtuális hálózati adatpert hoz létre, és engedélyezi hozzá az IP-továbbítást. |
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Egy hálózati biztonsági csoportot (NSG) hoz létre. |
| [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) | NSG-szabályokat hoz létre, amelyek engedélyezik a virtuális gép bejövő HTTP- és HTTPS-portjait. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig)| Az alhálózatokhoz rendeli az NSG-ket és az útválasztási táblázatokat. |
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable)| Útválasztási táblázatot hoz létre minden útvonalhoz. |
| [New-AzRouteConfig](/powershell/module/az.network/new-azrouteconfig)| Útvonalakat hoz létre, amellyel irányíthatja a forgalmat az alhálózatok és az internet között a virtuális gépen keresztül. |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | Létrehoz egy virtuális gépet, és csatolja hozzá a NIC-t. A parancs megadja továbbá a használandó virtuálisgép-rendszerképet és a rendszergazdai hitelesítő adatokat. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)  | Töröl egy erőforráscsoportot és a benne található összes erőforrást. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/azure/overview).

A [virtuális hálózatokra vonatkozó PowerShell-minták](../powershell-samples.md) között további, a virtuális hálózatokra vonatkozó PowerShell-példaszkripteket talál.