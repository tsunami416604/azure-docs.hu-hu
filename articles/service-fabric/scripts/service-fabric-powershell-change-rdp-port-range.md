---
title: Azure PowerShell-példaszkript – Az RPD-porttartomány módosítása | Microsoft Docs
description: Azure PowerShell-példaszkript – Módosítja egy üzembe helyezett fürt RPD-porttartományát.
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 03/19/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 4470cf8898f1d174f8ec2bb6860e2e30aeff9fe2
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68592309"
---
# <a name="update-the-rdp-port-range-values"></a>Az RPD-porttartomány értékeinek frissítése

Ez a példaszkript módosítja az RPD-porttartomány értékeit a fürtcsomópont virtuális gépein a fürt üzembe helyezése után.  Az Azure PowerShellre azért van szükség, hogy a rendszer ne váltson a háttérben lévő virtuális gépek között.  A szkript lekéri a fürt erőforráscsoportjában található `Microsoft.Network/loadBalancers` erőforrást, és frissíti az `inboundNatPools.frontendPortRangeStart` és `inboundNatPools.frontendPortRangeEnd` értékeket. Szabja testre a paramétereket szükség szerint.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Szükség esetén telepítse az Azure PowerShellt az [Azure PowerShell útmutatójának](/powershell/azure/overview) utasításait követve. 

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/service-fabric/change-rdp-port-range/change-rdp-port-range.ps1 "Update the RDP port range values")]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Lekéri a `Microsoft.Network/loadBalancers` erőforrást. |
|[Set-AzResource](/powershell/module/az.resources/set-azresource)|Frissíti a `Microsoft.Network/loadBalancers` erőforrást.|

## <a name="next-steps"></a>További lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

További Azure Powershell-példákat az Azure Service Fabrichez az [Azure PowerShell-példák](../service-fabric-powershell-samples.md) között találhat.
