---
title: RequestDisallowedByPolicy hiba
description: A RequestDisallowedByPolicy hiba okának ismertetése az Azure Resource Manager használatával történő erőforrások üzembe helyezésekor.
author: genlin
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 41581ba48da2f2e717c5abf2a749f8fd2b86ac06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75477667"
---
# <a name="requestdisallowedbypolicy-error-with-azure-resource-policy"></a>RequestDisallowedByPolicy hiba az Azure erőforrás-házirendjével

Ez a cikk ismerteti az oka a RequestDisallowedByPolicy hiba, ez is megoldást nyújt erre a hibára.

## <a name="symptom"></a>Hibajelenség

A telepítés során előfordulhat, hogy egy **RequestDisallowedByPolicy** hiba, amely megakadályozza az erőforrások létrehozását. A következő példa a hibát mutatja be:

```json
{
  "statusCode": "Forbidden",
  "serviceRequestId": null,
  "statusMessage": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}",
  "responseBody": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}"
}
```

## <a name="troubleshooting"></a>Hibaelhárítás

A központi telepítést letiltó házirend részleteinek beolvasásához használja az alábbi módszerek egyikét:

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

A PowerShellben adja meg, `Id` hogy a szabályzat azonosítója, mint a paraméter a házirend, amely letiltotta a központi telepítést.

```powershell
(Get-AzPolicyDefinition -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition").Properties.policyRule | ConvertTo-Json
```

### <a name="azure-cli"></a>Azure CLI

Az Azure CLI-ben adja meg a szabályzatdefiníció nevét:

```azurecli
az policy definition show --name regionPolicyAssignment
```

## <a name="solution"></a>Megoldás

A biztonság vagy a megfelelőség érdekében az előfizetés-rendszergazdák olyan házirendeket rendelhetnek hozzá, amelyek korlátozzák az erőforrások üzembe helyezését. Előfordulhat például, hogy az előfizetés rendelkezik egy szabályzattal, amely megakadályozza a nyilvános IP-címek, a hálózati biztonsági csoportok, a felhasználó által definiált útvonalak vagy az útvonaltáblák létrehozását. A **Jelenség ek** szakaszban található hibaüzenet a házirend nevét jeleníti meg.
A probléma megoldásához tekintse át az erőforrás-házirendeket, és határozza meg, hogyan helyezheti üzembe az okat megfelelő erőforrásokat.

További információkért tekintse át a következő cikkeket:

- [Mi az Azure Policy?](../../governance/policy/overview.md)
- [Szabályzatok létrehozása és kezelése a megfelelőség kikényszerítése céljából](../../governance/policy/tutorials/create-and-manage.md)
