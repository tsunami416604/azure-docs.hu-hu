---
title: Azure PowerShell-példaszkript – Hálózat létrehozása többszintű alkalmazásokhoz | Microsoft Docs
description: Azure PowerShell-példaszkript – Hálózat létrehozása többszintű alkalmazásokhoz.
services: virtual-network
documentationcenter: virtual-network
author: georgewallace
manager: timlt
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: powershell
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 05/16/2017
ms.author: gwallace
ms.openlocfilehash: 0d680bdab7e72f722ca3d653037e5f707607e72e
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2019
ms.locfileid: "56649357"
---
# <a name="create-a-network-for-multi-tier-applications"></a>Többrétegű alkalmazások hálózat létrehozása

Ez a példaszkript előtérbeli és háttérbeli alhálózattal rendelkező virtuális hálózatot hoz létre. Az előtérbeli alhálózat felé irányuló forgalom a HTTP- és az SSH-protokollon, a háttérbeli alhálózat felé irányuló forgalom pedig a MySQL-en, a 3306-os porton keresztül engedélyezett. A szkript futtatása után két virtuális géppel fog rendelkezni, eggyel-eggyel mindegyik olyan alhálózatban, amelyben üzembe helyezhet webkiszolgálót és MySQL-szoftvert.

Szükség esetén telepítse az Azure PowerShellt az [Azure PowerShell útmutatójának](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) utasításait követve, majd a `Connect-AzAccount` futtatásával hozza létre a kapcsolatot az Azure-ral.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/virtual-network/virtual-network-multi-tier-application/virtual-network-multi-tier-application.ps1  "Virtual network for multi-tier application")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

Az alábbi paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript az alábbi parancsokkal létrehoz egy erőforráscsoportot, egy virtuális hálózatot és hálózati biztonsági csoportokat. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Létrehoz egy Azure-beli virtuális hálózatot és előtérbeli alhálózatot. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Háttérbeli alhálózatot hoz létre. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Létrehoz egy nyilvános IP-címet a virtuális gép elérését az internetről. |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | Virtuális hálózati adaptereket hoz létre, és a virtuális hálózat előtérbeli és háttérbeli alhálózataihoz csatolja őket. |
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Az előtérbeli és a háttérbeli alhálózatokhoz társított hálózati biztonsági csoportokat (NSG) hoz létre. |
| [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) |NSG-szabályokat hoz létre, amelyek engedélyeznek vagy blokkolnak adott alhálózatokra mutató bizonyos portokat. |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | Virtuális gépeket hoz létre, és minden virtuális géphez csatol egy NIC-t. A parancs megadja továbbá a használandó virtuálisgép-rendszerképet és a rendszergazdai hitelesítő adatokat. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Töröl egy erőforráscsoportot és a benne található összes erőforrást. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/azure/overview).

További hálózatkezelési PowerShell-példaszkripteket az [Azure-hálózatkezelés áttekintő dokumentációjában](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json) találhat.
