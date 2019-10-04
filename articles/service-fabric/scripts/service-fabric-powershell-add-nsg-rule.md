---
title: Azure PowerShell parancsfájl-minta – hálózati biztonsági csoport szabályának hozzáadása | Microsoft Docs
description: Azure PowerShell parancsfájl-minta – egy hálózati biztonsági csoport hozzáadásával engedélyezi a bejövő forgalmat egy adott porton.
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
ms.openlocfilehash: 8e3187ba399ffa4d6c0ca863380205f7fbbfaae8
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036117"
---
# <a name="add-an-inbound-network-security-group-rule"></a>Bejövő hálózati biztonsági csoport szabályának hozzáadása

Ez a példa egy hálózati biztonsági csoportra vonatkozó szabályt hoz létre, amely engedélyezi a bejövő forgalmat a 8081-as porton.  A parancsfájl lekéri `Microsoft.Network/networkSecurityGroups` az erőforrást, amelyben a fürt található, létrehoz egy új hálózati biztonsági konfigurációs szabályt, és frissíti a hálózati biztonsági csoportot. Szabja testre a paramétereket szükség szerint.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ha szükséges, telepítse a Azure PowerShellt a [Azure PowerShell útmutatóban](/powershell/azure/overview)található utasítások alapján. 

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/service-fabric/add-inbound-nsg-rule/add-inbound-nsg-rule.ps1 "Update the RDP port range values")]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Lekéri a `Microsoft.Network/networkSecurityGroups` erőforrást. |
|[Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup)| Lekéri a hálózati biztonsági csoportot név szerint.|
|[Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Hálózati biztonsági szabály konfigurációját adja hozzá egy hálózati biztonsági csoporthoz. |
|[Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup)| Beállítja egy hálózati biztonsági csoport céljának állapotát.|

## <a name="next-steps"></a>További lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).
