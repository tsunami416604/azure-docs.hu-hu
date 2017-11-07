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
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: 2e821c0369c6f01a7f09361c1093259429a79fa6
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2017
---
# <a name="requestdisallowedbypolicy-error-with-azure-resource-policy"></a>Az Azure erőforrás-házirenddel RequestDisallowedByPolicy hiba

Ez a cikk ismerteti a RequestDisallowedByPolicy hiba okát, is megoldást nyújt az ezt a hibát.

## <a name="symptom"></a>Jelenség

Próbál végezni egy műveletet üzembe helyezése során, akkor fordulhat elő, egy **RequestDisallowedByPolicy** hibával, ami megakadályozza a művelet befejezését. A következő példa bemutatja a hiba:

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

### <a name="method-1"></a>1. módszer

A PowerShellben adja meg, hogy a házirend az azonosítója, mint a `Id` paraméter a házirendet, amely blokkolja a központi telepítés részleteinek beolvasása.

```PowerShell
(Get-AzureRmPolicyDefinition -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition").Properties.policyRule | ConvertTo-Json
```

### <a name="method-2"></a>2. módszer 

Az Azure CLI 2.0 adja meg a házirend-definíció nevét: 

```azurecli
az policy definition show --name regionPolicyAssignment
```

## <a name="solution"></a>Megoldás

A biztonsági és megfelelőségi esetében az informatikai részleg egy erőforrás-házirend tiltja a nyilvános IP-cím címek, hálózati biztonsági csoportokat, felhasználói útvonalak vagy útvonaltáblák létrehozása előfordulhat, hogy kényszerítése. A hibaüzenet jelenik meg a a **jelenségek** szakasz bemutatja egy nevű házirendet **regionPolicyDefinition**. A házirend lehet egy másik nevet.
A probléma megoldásához, az informatikai részleg tekintse át az erőforrás-házirendekkel való együttműködésre, és határozza meg, hogyan felelnek meg ezek a házirendek a kért művelet végrehajtásához.

További információkért tekintse át a következő cikkeket:

- [Erőforrás-házirendek – áttekintés](resource-manager-policy.md)
- [Házirend-hozzárendelések megtekintése portálon keresztül](resource-manager-policy-portal.md)
