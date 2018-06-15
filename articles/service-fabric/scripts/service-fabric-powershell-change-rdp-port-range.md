---
title: Azure PowerShell-példaszkript – Az RPD-porttartomány módosítása | Microsoft Docs
description: Azure PowerShell-példaszkript – Módosítja egy üzembe helyezett fürt RPD-porttartományát.
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
ms.openlocfilehash: 83fb6cc03f605a60b06f31fa6ddd82cd4e3e899e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
ms.locfileid: "30180187"
---
# <a name="update-the-rdp-port-range-values"></a>Az RPD-porttartomány értékeinek frissítése

Ez a példaszkript módosítja az RPD-porttartomány értékeit a fürtcsomópont virtuális gépein a fürt üzembe helyezése után.  Az Azure PowerShellre azért van szükség, hogy a rendszer ne váltson a háttérben lévő virtuális gépek között.  A szkript lekéri a fürt erőforráscsoportjában található `Microsoft.Network/loadBalancers` erőforrást, és frissíti az `inboundNatPools.frontendPortRangeStart` és `inboundNatPools.frontendPortRangeEnd` értékeket. Szabja testre a paramétereket szükség szerint.

Szükség esetén telepítse az Azure PowerShellt az [Azure PowerShell útmutatójának](/powershell/azure/overview) utasításait követve. 

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/service-fabric/change-rdp-port-range/change-rdp-port-range.ps1 "Update the RDP port range values")]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [Get-AzureRmResource](/powershell/module/azurerm.resources/get-azurermresource) | Lekéri a `Microsoft.Network/loadBalancers` erőforrást. |
|[Set-AzureRmResource](/powershell/module/azurerm.resources/set-azurermresource)|Frissíti a `Microsoft.Network/loadBalancers` erőforrást.|

## <a name="next-steps"></a>További lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

További Azure Powershell-példákat az Azure Service Fabrichez az [Azure PowerShell-példák](../service-fabric-powershell-samples.md) között találhat.
