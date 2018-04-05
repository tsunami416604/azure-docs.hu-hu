---
title: Azure PowerShell-minták – Zónaredundáns méretezési csoport | Microsoft Docs
description: Azure PowerShell-minták
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: b121684b3b9a5d03fe89e0892179140e9f5de80f
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="create-a-zone-redundant-virtual-machine-scale-set-with-powershell"></a>Zónaredundáns virtuálisgép-méretezési csoport létrehozása PowerShell segítségével
Ez a szkript egy Windows Server 2016 rendszert futtató virtuálisgép-méretezési csoportot hoz létre több rendelkezésre állási zónában. A szkript futtatása után a virtuális gépet RDP-kapcsolaton keresztül érheti el.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript
[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/create-zone-redundant-scale-set/create-zone-redundant-scale-set.ps1 "Create zone-redundant scale set")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása
Az alábbi paranccsal eltávolítható az erőforráscsoport, a méretezési csoport és az összes kapcsolódó erőforrás.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Szkript ismertetése
A szkript a következő parancsokat használja az üzemelő példány létrehozásához. A táblázatban lévő összes elem a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) | Egy konfigurációs objektumot hoz létre, amely meghatározza a virtuális hálózat alhálózatát. |
| [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) | Egy virtuális hálózatot és alhálózatot hoz létre. |
| [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | Egy statikus nyilvános IP-címet hoz létre. |
| [New-AzureRmLoadBalancerFrontendIpConfig](/powershell/module/azurerm.network/new-azurermloadbalancerfrontendipconfig) | Egy konfigurációs objektumot hoz létre, amely meghatározza a terheléselosztó előtérrendszerét, beleértve a nyilvános IP-címet. |
| [New-AzureRmLoadBalancerBackendAddressPoolConfig](/powershell/module/azurerm.network/new-azurermloadbalancerbackendaddresspoolconfig) | Egy konfigurációs objektumot hoz létre, amely meghatározza a terheléselosztó hátterét. |
| [New-AzureRmLoadBalancer](/powershell/module/azurerm.network/new-azurermloadbalancer) | Egy terheléselosztót hoz létre az előtérrendszer és a háttér konfigurációs objektuma alapján. |
| [Add-AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/new-azurermloadbalancerprobeconfig) | Egy terheléselosztó állapotmintát hoz létre, amely monitorozza a 80-as TCP-port forgalmát. Ha 15 másodperces intervallumon belül két, egymást követő hiba jelentkezik, a végpont sérültnek tekinthető. |
| [Add-AzureRmLoadBalancerRuleConfig](/powershell/module/azurerm.network/new-azurermloadbalancerruleconfig) | Egy konfigurációs objektumot hoz létre, amely meghatározza a 80-as TCP-port forgalmának az előtér- és háttérkészlet közötti elosztására vonatkozó terheléselosztási szabályokat. |
| [Set-AzureRmLoadBalancer](/powershell/module/azurerm.network/set-azurermloadbalancer) | Frissíti a terheléselosztót az állapotmintával és a terheléselosztási szabályokkal. |
| [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) | Egy konfigurációs objektumot hoz létre, amely meghatározz egy hálózati biztonságicsoport-szabályt, amely engedélyezi a fogalmat a 80-as TCP-porton. |
| [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) | Egy hálózati biztonsági csoportot és szabályt hoz létre. |
| [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) | Frissíti a virtuális hálózatnak a hálózati biztonsági csoporthoz társítani kívánt alhálózatát. Minden az alhálózathoz csatlakozó virtuálisgép-példány örökli a hálózati biztonságicsoport-szabályokat. |
| [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork) | Frissíti a virtuális hálózatot az alhálózat és a hálózati biztonsági csoport konfigurációjával. |
| [New-AzureRmVmssIpConfig](/powershell/module/azurerm.compute/new-azurermvmssipconfig) | Egy konfigurációs objektumot hoz létre, amely meghatározza a virtuálisgép-méretezési csoport IP-címére vonatkozó adatokat a virtuálisgép-példányoknak a terheléselosztó háttérkészletéhez és a bejövő NAT-készlethez történő csatlakoztatásához.
| [New-AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig) | Egy konfigurációs objektumot hoz létre, amely meghatározza az egyes méretezési csoportokban található virtuálisgép-példányok számát, azok méretét, valamint a frissítési szabályzat üzemmódját. |
| [Set-AzureRmVmssStorageProfile](/powershell/module/azurerm.compute/set-azurermvmssstorageprofile) | Egy konfigurációs objektumot hoz létre, amely meghatározza a virtuálisgép-példányokhoz használandó virtuálisgép-rendszerképet. |
| [Set-AzureRmVmssOsProfile](/powershell/module/azurerm.compute/set-azurermvmssosprofile) | Egy konfigurációs profilt hoz létre, amely meghatározza virtuálisgép-példány nevét és felhasználói hitelesítő adatait. |
| [Add-AzureRmVmssNetworkInterfaceConfiguration](/powershell/module/azurerm.compute/add-azurermvmssnetworkinterfaceconfiguration) | Egy konfigurációs objektumot hoz létre, amely meghatározza az egyes virtuálisgép-példányok virtuális hálózati adapterkártyáját. |
| [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss) | Egyesíti az összes konfigurációs objektumot a virtuálisgép-méretezési csoport létrehozásához. |
| [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss) | Lekéri a virtuálisgép-méretezési csoport adatait. |
| [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension) | Egy virtuálisgép-bővítményt ad hozzá az egyéni szkriptekhez egy alapszintű webalkalmazás telepítéséhez. |
| [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss) | Frissíti a virtuálisgép-méretezési csoport modelljét a virtuálisgép-bővítmény alkalmazására. |
| [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) | Lekéri a terheléselosztó által a felhasználóhoz hozzárendelt nyilvános IP-cím adatait. |
|[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Eltávolít egy erőforráscsoportot és az összes abban található erőforrást. |


## <a name="next-steps"></a>További lépések
Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

A virtuálisgép-méretezési csoportokhoz használható további PowerShell-példaszkripteket az [Azure virtuálisgép-méretezési csoportokra vonatkozó dokumentációjában](../powershell-samples.md) találhat.