---
title: "Az Azure PowerShell-parancsfájl minták – egy hálózati biztonsági szabály hozzáadása |} Microsoft Docs"
description: "Az Azure PowerShell-parancsfájl minta - egy adott portot a bejövő adatforgalom engedélyezésére hálózati biztonsági csoport hozzáadása."
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
ms.openlocfilehash: fd3c648ee63c45bef305658832a4d31dfdb213be
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/29/2017
---
# <a name="add-an-inbound-network-security-group-rule"></a>Vegyen fel egy bejövő hálózati biztonsági csoport

Ez a parancsfájlpélda szabályt hoz létre hálózati biztonsági csoport port 8081 bejövő adatforgalom engedélyezésére.  A parancsfájl lekérdezi a `Microsoft.Network/networkSecurityGroups` erőforrás a fürtön található, egy új hálózati biztonsági konfiguráció szabályt hoz létre, és frissíti a hálózati biztonsági csoport. Szabja testre a paramétereket szükség szerint.

Szükség esetén telepítse az Azure PowerShell, a található utasításokat követve a [Azure PowerShell útmutató](/powershell/azure/overview). 

## <a name="sample-script"></a>Mintaparancsfájl

[!code-powershell[main](../../../powershell_scripts/service-fabric/add-inbound-nsg-rule/add-inbound-nsg-rule.ps1 "Update the RDP port range values")]

## <a name="script-explanation"></a>Parancsfájl ismertetése

A parancsfájl a következő parancsokat. Minden egyes parancsa a tábla-parancs adott dokumentációjára mutató hivatkozásokat.

| Parancs | Megjegyzések |
|---|---|
| [Get-AzureRmResource](/powershell/module/azurerm.resources/get-azurermresource) | Lekérdezi a `Microsoft.Network/networkSecurityGroups` erőforrás. |
|[Get-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/get-azurermnetworksecuritygroup)| Lekérdezi a hálózati biztonsági csoport neve.|
|[Adja hozzá AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/add-azurermnetworksecurityruleconfig)| A hálózati biztonsági szabály konfiguráció hozzáadása a hálózati biztonsági csoport. |
|[Set-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/set-azurermnetworksecuritygroup)| A cél állapotának beállítása a hálózati biztonsági csoport.|

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShell modul további információkért lásd: [Azure PowerShell dokumentációs](/powershell/azure/overview).
