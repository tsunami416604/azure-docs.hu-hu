---
title: RequestDisallowedByPolicy hiba az Azure Resource Policy szolgáltatással | Microsoft Docs
description: A RequestDisallowedByPolicy hibájának okát ismerteti az erőforrások Azure Resource Manager-vel való telepítésekor.
services: azure-resource-manager
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: c791342bf68f84f6893e549d8528d1a861aa9040
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72390300"
---
# <a name="requestdisallowedbypolicy-error-with-azure-resource-policy"></a>RequestDisallowedByPolicy hiba az Azure erőforrás-házirendjével

Ez a cikk a RequestDisallowedByPolicy hibájának okát mutatja be, továbbá megoldást nyújt erre a hibára.

## <a name="symptom"></a>Hibajelenség

Az üzembe helyezés során **RequestDisallowedByPolicy** hibaüzenet jelenhet meg, amely megakadályozza az erőforrások létrehozását. A következő példa a hibát mutatja be:

```json
{
  "statusCode": "Forbidden",
  "serviceRequestId": null,
  "statusMessage": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}",
  "responseBody": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}"
}
```

## <a name="troubleshooting"></a>Hibakeresés

Az üzembe helyezést blokkoló házirend részleteinek lekéréséhez használja a következő módszerek egyikét:

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A PowerShellben adja meg a házirend-azonosítót `Id` paraméterként, hogy lekérje a központi telepítést blokkoló házirend részleteit.

```powershell
(Get-AzPolicyDefinition -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition").Properties.policyRule | ConvertTo-Json
```

### <a name="azure-cli"></a>Azure parancssori felület (CLI)

Az Azure CLI-ben adja meg a házirend-definíció nevét:

```azurecli
az policy definition show --name regionPolicyAssignment
```

## <a name="solution"></a>Megoldás

Biztonság vagy megfelelőség esetén az előfizetési rendszergazdák olyan házirendeket rendelhetnek, amelyek korlátozzák az erőforrások központi telepítését. Előfordulhat például, hogy az előfizetése olyan házirenddel rendelkezik, amely megakadályozza a nyilvános IP-címek, a hálózati biztonsági csoportok, a felhasználó által megadott útvonalak vagy az útválasztási táblák létrehozását. A **jelenségek** szakaszban található hibaüzenet a szabályzat nevét mutatja.
A probléma megoldásához tekintse át az erőforrás-házirendeket, és határozza meg, hogyan telepítse a szabályzatoknak megfelelő erőforrásokat.

További információkért tekintse át a következő cikkeket:

- [Mi az Azure Policy?](../governance/policy/overview.md)
- [Szabályzatok létrehozása és kezelése a megfelelőség kikényszerítése céljából](../governance/policy/tutorials/create-and-manage.md)
