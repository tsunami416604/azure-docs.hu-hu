---
title: "Az Azure PowerShell-parancsfájl minta - módosítás az RDP-porttartomány. | Microsoft Docs"
description: "Az Azure PowerShell-parancsfájl minta - módosítja az RDP porttartomány telepített fürt."
services: service-fabric
documentationcenter: 
author: rwike77
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 11/28/2017
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: ca24c1afb9a29db7c5ff36d23a10da84ca2f45c3
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/29/2017
---
# <a name="update-the-rdp-port-range-values"></a>Az RDP-port tartományértékeknek frissítése

Ez a parancsfájlpélda módosítja a RDP port tartomány a fürtcsomóponton virtuális gépek a fürt telepítése után.  Az Azure PowerShell szolgál, hogy az alapul szolgáló virtuális gépek nem ciklus.  A parancsfájl lekérdezi a `Microsoft.Network/loadBalancers` erőforrás fürt erőforráscsoport és frissítése a `inboundNatPools.frontendPortRangeStart` és `inboundNatPools.frontendPortRangeEnd` értékeket. Szabja testre a paramétereket szükség szerint.

Szükség esetén telepítse az Azure PowerShell található utasítás használatával a [Azure PowerShell útmutató](/powershell/azure/overview). 

## <a name="sample-script"></a>Mintaparancsfájl

[!code-powershell[main](../../../powershell_scripts/service-fabric/change-rdp-port-range/change-rdp-port-range.ps1 "Update the RDP port range values")]

## <a name="script-explanation"></a>Parancsfájl ismertetése

A parancsfájl a következő parancsokat. Minden egyes parancsa a tábla-parancs adott dokumentációjára mutató hivatkozásokat.

| Parancs | Megjegyzések |
|---|---|
| [Get-AzureRmResource](/powershell/module/azurerm.resources/get-azurermresource) | Lekérdezi a `Microsoft.Network/loadBalancers` erőforrás. |
|[Set-AzureRmResource](/powershell/module/azurerm.resources/set-azurermresource)|Frissítések a `Microsoft.Network/loadBalancers` erőforrás.|

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShell modul további információkért lásd: [Azure PowerShell dokumentációs](/powershell/azure/overview).

Azure Service Fabric további Azure Powershell-példák találhatók a [Azure PowerShell-példák](../service-fabric-powershell-samples.md).
