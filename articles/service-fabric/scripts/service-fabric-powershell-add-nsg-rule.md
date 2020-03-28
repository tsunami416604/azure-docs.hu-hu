---
title: Hálózati biztonsági csoportszabály hozzáadása a PowerShellben
description: Azure PowerShell-parancsfájlminta – hálózati biztonsági csoportot ad hozzá egy adott port bejövő forgalmának engedélyezéséhez.
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 11/28/2017
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 33ee8028031bf94d69d11b08c347f84db94494a3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75610420"
---
# <a name="add-an-inbound-network-security-group-rule"></a>Bejövő hálózati biztonsági csoportszabály hozzáadása

Ez a mintaparancsfájl létrehoz egy hálózati biztonsági csoportszabályt, amely engedélyezi a bejövő forgalmat a 8081-es porton.  A parancsfájl leveszi a hálózati biztonsági csoportot, létrehoz egy új hálózati biztonsági konfigurációs szabályt, és frissíti a hálózati biztonsági csoportot. Szabja testre a paramétereket szükség szerint.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Szükség esetén telepítse az Azure PowerShellt az [Azure PowerShell-útmutatóban](/powershell/azure/overview)található utasítások alapján. 

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/service-fabric/add-inbound-nsg-rule/add-inbound-nsg-rule.ps1 "Update the RDP port range values")]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Lekéri a `Microsoft.Network/networkSecurityGroups` erőforrást. |
|[Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup)| A hálózati biztonsági csoport név szerint leáll.|
|[Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Hálózati biztonsági szabály konfigurációját adja hozzá egy hálózati biztonsági csoporthoz. |
|[Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup)| Beállítja egy hálózati biztonsági csoport célállapotát.|

## <a name="next-steps"></a>További lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).
