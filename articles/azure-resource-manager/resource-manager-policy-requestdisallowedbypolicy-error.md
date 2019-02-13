---
title: RequestDisallowedByPolicy hiba az Azure erőforrás-szabályzat |} A Microsoft Docs
description: Ismerteti a RequestDisallowedByPolicy hiba okát.
services: azure-resource-manager
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 8eea14703a7a4ed6fad56dc0bed981b84266e2db
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56112614"
---
# <a name="requestdisallowedbypolicy-error-with-azure-resource-policy"></a>Az Azure erőforrás-szabályzat RequestDisallowedByPolicy hiba

Ez a cikk ismerteti a RequestDisallowedByPolicy hiba okát, ez a hiba megoldás is biztosít.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="symptom"></a>Jelenség

A telepítés során kaphat egy **RequestDisallowedByPolicy** hiba, amely megakadályozza az erőforrások létrehozását. Az alábbi példa bemutatja a hiba:

```json
{
  "statusCode": "Forbidden",
  "serviceRequestId": null,
  "statusMessage": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}",
  "responseBody": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}"
}
```

## <a name="troubleshooting"></a>Hibaelhárítás

A házirendet, amely blokkolja az üzembe helyezés részleteit lekéréséhez használja a következő módszerek egyikét:

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A PowerShellben adja meg, hogy a házirend azonosítója, mint a `Id` paraméter beolvasni a szabályzatot, amely blokkolja az üzembe helyezés részleteit.

```PowerShell
(Get-AzPolicyDefinition -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition").Properties.policyRule | ConvertTo-Json
```

### <a name="azure-cli"></a>Azure CLI

Az Azure CLI-ben adja meg a szabályzat-definíció nevét:

```azurecli
az policy definition show --name regionPolicyAssignment
```

## <a name="solution"></a>Megoldás

Biztonság és megfelelőség az előfizetés adminisztrátorainak rendelné a házirendekben, amelyek korlátozzák az erőforrások telepítési módját. Például az előfizetés előfordulhat, hogy rendelkezik egy szabályzatot, amely megakadályozza, hogy a nyilvános IP-címek, hálózati biztonsági csoportok, felhasználó által megadott útvonalakat hoz létre, vagy útválasztási táblázatok. A megjelenő hibaüzenet a **tünetek** szakasz bemutatja a házirend nevét.
A probléma megoldásához tekintse át az erőforrás-szabályzatok, és határozza meg, hogyan helyezhet üzembe erőforrásokat, amelyek megfelelnek a ezek szabályzatok.

További információkért tekintse át a következő cikkeket:

- [Mi az Azure Policy?](../azure-policy/azure-policy-introduction.md)
- [Megfelelőség kikényszerítése céljából szabályzatok létrehozása és kezelése](../azure-policy/create-manage-policy.md)
