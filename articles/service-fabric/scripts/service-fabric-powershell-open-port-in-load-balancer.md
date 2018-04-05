---
title: Azure PowerShell-példaszkript – Alkalmazásport megnyitása a terheléselosztóban | Microsoft Docs
description: Azure PowerShell-példaszkript – Port megnyitása az Azure Load Balancerben Service Fabric-alkalmazás számára.
services: service-fabric
documentationcenter: ''
author: rwike77
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 03/19/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 1edaca9c354a7b65b47213c7970e823aee3cbe87
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="open-an-application-port-in-the-azure-load-balancer"></a>Alkalmazásport megnyitása az Azure Load Balancerben

Az Azure-ban futó Service Fabric-alkalmazások az Azure Load Balancer mögött találhatóak. A példaszkript megnyit egy portot az Azure Load Balancerben, hogy a Service Fabric-alkalmazás kommunikálhasson külső ügyfelekkel. Szabja testre a paramétereket szükség szerint. Ha a fürt egy hálózati biztonsági csoport része, [adjon hozzá egy bejövő hálózati biztonságicsoport-szabályt](service-fabric-powershell-add-nsg-rule.md), hogy engedélyezze a bejövő adatforgalmat.

Ha szükséges, telepítse a Service Fabric PowerShell-modult a [Service Fabric SDK](../service-fabric-get-started.md)-val. 

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/service-fabric/open-port-in-load-balancer/open-port-in-load-balancer.ps1 "Open a port in the load balancer")]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [Get-AzureRmResource](/powershell/module/azurerm.resources/get-azurermresource) | Lekér egy Azure-erőforrást.  |
| [Get-AzureRmLoadBalancer](/powershell/module/azurerm.network/get-azurermloadbalancer) | Lekéri az Azure Load Balancert. |
| [Add-AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/add-azurermloadbalancerprobeconfig) | Hozzáad egy mintavételi konfigurációt egy terheléselosztóhoz.|
| [Get-AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/get-azurermloadbalancerprobeconfig) | Lekér egy mintavételi konfigurációt egy terheléselosztó számára. |
| [Add-AzureRmLoadBalancerRuleConfig](/powershell/module/azurerm.network/add-azurermloadbalancerruleconfig) | Hozzáad egy szabálykonfigurációt egy terheléselosztóhoz. |
| [Set-AzureRmLoadBalancer](/powershell/module/azurerm.network/set-azurermloadbalancer) | Beállítja egy terheléselosztó célállapotát. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

További Powershell-példákat az Azure Service Fabrichez az [Azure PowerShell-példák](../service-fabric-powershell-samples.md) között találhat.
