---
title: "Az Azure erőforrás-házirenddel RequestDisallowedByPolicy hiba |} Microsoft Docs"
description: "Ismerteti a RequestDisallowedByPolicy hiba okát."
services: azure-resource-manager,azure-portal
documentationcenter: 
author: genlin
manager: cshepard
editor: 
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: support-article
ms.date: 03/09/2018
ms.author: genli
ms.openlocfilehash: 5a9efa6b807e933726104e7af315589ede5d9b74
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2018
---
# <a name="requestdisallowedbypolicy-error-with-azure-resource-policy"></a>Az Azure erőforrás-házirenddel RequestDisallowedByPolicy hiba

Ez a cikk ismerteti a RequestDisallowedByPolicy hiba okát, is megoldást nyújt az ezt a hibát.

## <a name="symptom"></a>Jelenség

Központi telepítése során léphet egy **RequestDisallowedByPolicy** hibával, ami megakadályozza, hogy az erőforrások létrehozásához. A következő példa bemutatja a hiba:

```json
{
  "statusCode": "Forbidden",
  "serviceRequestId": null,
  "statusMessage": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}",
  "responseBody": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}"
}
```

## <a name="troubleshooting"></a>Hibaelhárítás

A házirendet, amely blokkolja a központi telepítés részleteit lekéréséhez használja a következő módszerek egyikét:

### <a name="powershell"></a>PowerShell

A PowerShellben adja meg, hogy a házirend az azonosítója, mint a `Id` paraméter a házirendet, amely blokkolja a központi telepítés részleteinek beolvasása.

```PowerShell
(Get-AzureRmPolicyDefinition -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition").Properties.policyRule | ConvertTo-Json
```

### <a name="azure-cli"></a>Azure CLI

Az Azure CLI 2.0 adja meg a házirend-definíció nevét:

```azurecli
az policy definition show --name regionPolicyAssignment
```

## <a name="solution"></a>Megoldás

Biztonsági és megfelelőségi az előfizetés rendszergazdáihoz házirendek hogyan vannak telepítve az erőforrások korlátozó lehet rendelni. Például az előfizetés lehet, hogy egy házirendet, amely megakadályozza, hogy a nyilvános IP-címek, hálózati biztonsági csoportokat, felhasználói útvonalak létrehozásával, vagy útvonaltáblát. A hibaüzenet jelenik meg a a **jelenségek** szakasz jeleníti meg a házirend nevét.
A probléma megoldásához tekintse át az erőforrás-házirendekkel, és határozza meg az erőforrásokat, amelyek megfelelnek a ezek a házirendek központi telepítéséről.

További információkért tekintse át a következő cikkeket:

- [Mi az Azure-házirendet?](../azure-policy/azure-policy-introduction.md)
- [Kényszeríteni a megfelelőségi szabályzatok létrehozása és kezelése](../azure-policy/create-manage-policy.md)
