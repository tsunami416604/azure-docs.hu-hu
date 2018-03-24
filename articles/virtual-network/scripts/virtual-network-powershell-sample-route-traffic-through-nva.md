---
title: Az Azure PowerShell parancsfájl minta - útvonal forgalmát a hálózati virtuális készülék |} Microsoft Docs
description: Az Azure PowerShell parancsfájl minta - hálózat virtuális készülékként egy tűzfalat-útvonal forgalmát.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: powershell
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/20/2018
ms.author: jdial
ms.openlocfilehash: 766d800a7875536b729f564a3d9948e5598de6aa
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="route-traffic-through-a-network-virtual-appliance"></a>A hálózati virtuális készülék-útvonal forgalmát

Ez a parancsfájl-minta előtér- és alhálózatok hoz létre a virtuális hálózat. Az IP-továbbítás engedélyezve van a két alhálózat közötti forgalom egy virtuális Gépet is létrehoz. A parancsfájl futtatása után telepítheti a hálózati szoftverek, például egy tűzfal alkalmazást, a virtuális géphez.

A parancsfájl az Azure-ból végrehajthat [felhő rendszerhéj](https://shell.azure.com/powershell), vagy egy helyi PowerShell telepítési. Ha a PowerShell segítségével helyileg, ezt a parancsfájlt a AzureRM PowerShell modul verziója 5.4.1 szükséges-e vagy újabb. A telepített verzió megkereséséhez futtassa `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Login-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript


[!code-azurepowershell-interactive[main](../../../powershell_scripts/virtual-network/route-traffic-through-nva/route-traffic-through-nva.ps1 "Route traffic through a network virtual appliance")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

Futtassa a következő parancs futtatásával távolítsa el az erőforráscsoportot, virtuális gép és az összes kapcsolódó erőforrások:

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```
## <a name="script-explanation"></a>Szkript ismertetése

A parancsfájl a következő parancsokat egy erőforráscsoport, a virtuális hálózat és a hálózati biztonsági csoportok létrehozásához. Minden parancs az alábbi táblázatban hivatkozások parancs-specifikus dokumentációját:

| Parancs | Megjegyzések |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup)  | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) | Egy Azure-beli virtuális hálózat és az előtér-alhálózatot hoz létre. |
| [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) | Háttér- és DMZ-alhálózatot hoz létre. |
| [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | Létrehoz egy nyilvános IP-címet a virtuális gép az internetről való eléréséhez. |
| [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) | Létrehoz egy virtuális hálózati adapter és az IP-továbbítás engedélyezése. |
| [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) | A hálózati biztonsági csoport (NSG) hoz. |
| [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) | Hoz létre NSG-szabályok, amelyek lehetővé teszik a VM bejövő HTTP és HTTPS-portok. |
| [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig)| Az NSG-ket és útvonaltáblák alhálózatokra társítja. |
| [New-AzureRmRouteTable](/powershell/module/azurerm.network/new-azurermroutetable)| Az összes útvonal egy útválasztási táblázatot hoz létre. |
| [New-AzureRMRouteConfig](/powershell/module/azurerm.network/new-azurermrouteconfig)| Irányíthatja a forgalmat a alhálózatok között, valamint az interneten keresztül a virtuális gép útvonalakat hoz létre. |
| [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) | Létrehoz egy virtuális gépet, és a hálózati adapter csatlakozik. Ez a parancs is meghatározza a használandó virtuálisgép-lemezkép és a rendszergazdai hitelesítő adatait. |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup)  | Törli az erőforráscsoportot és a benne található összes erőforrást. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/azure/overview).

További virtuális hálózati PowerShell parancsfájl minták található [virtuális hálózati PowerShell-példák](../powershell-samples.md).