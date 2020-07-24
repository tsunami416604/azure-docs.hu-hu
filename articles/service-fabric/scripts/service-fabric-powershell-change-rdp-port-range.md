---
title: Azure PowerShell-példaszkript – Az RPD-porttartomány módosítása | Microsoft Docs
description: Azure PowerShell-példaszkript – Módosítja egy üzembe helyezett fürt RPD-porttartományát.
services: service-fabric
tags: azure-service-management
author: athinanthny
ms.author: atsenthi
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 03/19/2018
ms.custom: sfrev
ms.openlocfilehash: 5cc72423d7e901f82cdf7e40f5de72bd54e9cec1
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87076168"
---
# <a name="update-the-rdp-port-range-values"></a>Az RPD-porttartomány értékeinek frissítése

Ez a példaszkript módosítja az RPD-porttartomány értékeit a fürtcsomópont virtuális gépein a fürt üzembe helyezése után.  Az Azure PowerShellre azért van szükség, hogy a rendszer ne váltson a háttérben lévő virtuális gépek között.  A parancsfájl lekéri az `Microsoft.Network/loadBalancers` erőforrást a fürt erőforráscsoporthoz, és frissíti a `inboundNatPools.frontendPortRangeStart` és az `inboundNatPools.frontendPortRangeEnd` értékeket. Szabja testre a paramétereket szükség szerint.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Szükség esetén telepítse az Azure PowerShellt az [Azure PowerShell útmutatójának](/powershell/azure/) utasításait követve.

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/service-fabric/change-rdp-port-range/change-rdp-port-range.ps1 "Update the RDP port range values")]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Lekéri a `Microsoft.Network/loadBalancers` erőforrást. |
|[Set-AzResource](/powershell/module/az.resources/set-azresource)|Frissíti a `Microsoft.Network/loadBalancers` erőforrást.|

## <a name="next-steps"></a>További lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/).

További Azure Powershell-példákat az Azure Service Fabrichez az [Azure PowerShell-példák](../service-fabric-powershell-samples.md) között találhat.
