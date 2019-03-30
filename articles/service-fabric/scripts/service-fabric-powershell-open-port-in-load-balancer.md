---
title: Azure PowerShell-példaszkript – Alkalmazásport megnyitása a terheléselosztóban | Microsoft Docs
description: Azure PowerShell-példaszkript – Port megnyitása az Azure Load Balancerben Service Fabric-alkalmazás számára.
services: service-fabric
documentationcenter: ''
author: aljo-microsoft
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 05/18/2018
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: 942dd995651f4c672d50163e5c380b62155b2227
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58667259"
---
# <a name="open-an-application-port-in-the-azure-load-balancer"></a>Alkalmazásport megnyitása az Azure Load Balancerben

Az Azure-ban futó Service Fabric-alkalmazások az Azure Load Balancer mögött találhatóak. A példaszkript megnyit egy portot az Azure Load Balancerben, hogy a Service Fabric-alkalmazás kommunikálhasson külső ügyfelekkel. Szabja testre a paramétereket szükség szerint. Ha a fürt egy hálózati biztonsági csoport része, [adjon hozzá egy bejövő hálózati biztonságicsoport-szabályt](service-fabric-powershell-add-nsg-rule.md), hogy engedélyezze a bejövő adatforgalmat.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ha szükséges, telepítse a Service Fabric PowerShell-modult a [Service Fabric SDK](../service-fabric-get-started.md)-val. 

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/service-fabric/open-port-in-load-balancer/open-port-in-load-balancer.ps1 "Open a port in the load balancer")]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Lekér egy Azure-erőforrást.  |
| [Get-AzLoadBalancer](/powershell/module/az.network/get-azloadbalancer) | Lekéri az Azure Load Balancert. |
| [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig) | Hozzáad egy mintavételi konfigurációt egy terheléselosztóhoz.|
| [Get-AzLoadBalancerProbeConfig](/powershell/module/az.network/get-azloadbalancerprobeconfig) | Lekér egy mintavételi konfigurációt egy terheléselosztó számára. |
| [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig) | Hozzáad egy szabálykonfigurációt egy terheléselosztóhoz. |
| [Set-AzLoadBalancer](/powershell/module/az.network/set-azloadbalancer) | Beállítja egy terheléselosztó célállapotát. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

További Powershell-példákat az Azure Service Fabrichez az [Azure PowerShell-példák](../service-fabric-powershell-samples.md) között találhat.
