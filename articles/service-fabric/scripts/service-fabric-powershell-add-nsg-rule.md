---
title: Azure PowerShell-Példaszkript – egy hálózati biztonsági csoportra vonatkozó szabályt hozzáadása |} A Microsoft Docs
description: Az Azure PowerShell-Példaszkript – egy adott porton bejövő adatforgalom engedélyezésére hálózati biztonsági csoport hozzáadása.
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
ms.date: 11/28/2017
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: 526a568bbcd7249e4f8917e8cdd82a0de71bfb0a
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58664998"
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
