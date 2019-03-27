---
title: Azure PowerShell-Példaszkript – egy hálózati biztonsági csoportra vonatkozó szabályt hozzáadása |} A Microsoft Docs
description: Az Azure PowerShell-Példaszkript – egy adott porton bejövő adatforgalom engedélyezésére hálózati biztonsági csoport hozzáadása.
services: service-fabric
documentationcenter: ''
author: aljo-microsoft
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 11/28/2017
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: fead6aa19775eab6cc3a1014e9f52b30cfa2cefa
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58498315"
---
# <a name="add-an-inbound-network-security-group-rule"></a>Adjon hozzá egy bejövő hálózati biztonságicsoport-szabályt

Ez a példaszkript létrehoz egy hálózati biztonsági csoportra vonatkozó szabályt engedélyezéséhez 8081-es porton bejövő forgalmat.  A szkript lekéri a `Microsoft.Network/networkSecurityGroups` , amely a fürtben található, erőforráshoz egy új hálózati biztonsági konfiguráció szabályt hoz létre, és frissíti a hálózati biztonsági csoport. Szabja testre a paramétereket szükség szerint.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ha szükséges, telepítse az Azure Powershellt utasításait a [Azure PowerShell útmutatójának](/powershell/azure/overview). 

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/service-fabric/add-inbound-nsg-rule/add-inbound-nsg-rule.ps1 "Update the RDP port range values")]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Lekéri a `Microsoft.Network/networkSecurityGroups` erőforrást. |
|[Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup)| Lekéri a hálózati biztonsági csoport nevét.|
|[Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Egy hálózati biztonsági szabály konfigurációjának hozzáadása egy hálózati biztonsági csoportot. |
|[Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup)| Beállítja a hálózati biztonsági csoport célállapotát.|

## <a name="next-steps"></a>További lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).
