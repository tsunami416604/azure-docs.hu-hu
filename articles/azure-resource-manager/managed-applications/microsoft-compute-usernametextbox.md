---
title: UserNameTextBox ui elem
description: A cikk a Microsoft.Compute.UserNameTextBox UI elemét ismerteti az Azure Portalon. Lehetővé teszi a felhasználók számára, hogy Windows vagy Linux felhasználói neveket adjanak meg.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: c7544ae7d872a64547cb6c57ce8af9a09fc6c3d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651903"
---
# <a name="microsoftcomputeusernametextbox-ui-element"></a>Microsoft.Compute.UserNameTextBox felhasználói felület

Szövegdoboz-vezérlő beépített érvényesítéssel Windows és Linux felhasználói nevekhez.

## <a name="ui-sample"></a>Felhasználói felület minta

![Microsoft.Compute.UserNameTextBox](./media/managed-application-elements/microsoft.compute.usernametextbox.png)

## <a name="schema"></a>Séma

```json
{
  "name": "element1",
  "type": "Microsoft.Compute.UserNameTextBox",
  "label": "User name",
  "defaultValue": "",
  "toolTip": "",
  "constraints": {
    "required": true,
    "regex": "^[a-z0-9A-Z]{1,30}$",
    "validationMessage": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
  },
  "osPlatform": "Windows",
  "visible": true
}
```

## <a name="sample-output"></a>Példa kimenet

```json
"Example name"
```

## <a name="remarks"></a>Megjegyzések

- Ha `constraints.required` értéke **igaz,** akkor a szövegdoboznak kell egy értékkel a sikeres érvényesítéshez. Az alapértelmezett érték **igaz**.
- `osPlatform`meg kell adni, és lehet **Windows** vagy **Linux**.
- `constraints.regex`egy JavaScript reguláris kifejezésminta. Ha meg van adva, akkor a szövegdoboz értékének meg kell egyeznie a mintával a sikeres érvényesítéshez. Az alapértelmezett érték **null**.
- `constraints.validationMessage`egy karakterlánc, amely akkor jelenik meg, ha a `constraints.regex`szövegdoboz értéke nem felel meg a . Ha nincs megadva, akkor a program a szövegdoboz beépített érvényesítési üzeneteit használja. Az alapértelmezett érték **null**.
- Ez az elem beépített érvényesítéssel rendelkezik, amely `osPlatform`a számára megadott értéken alapul. A beépített ellenőrzés egyéni reguláris kifejezéssel együtt használható. Ha meg `constraints.regex` van adva egy érték, akkor a beépített és az egyéni érvényesítés ek is aktiválódnak.

## <a name="next-steps"></a>További lépések

* A felhasználói felületdefiníciók létrehozásának bemutatása a [CreateUiDefinition](create-uidefinition-overview.md)első lépései című témakörben látható.
* A felhasználói felület elemeinek gyakori tulajdonságainak leírását a [CreateUiDefinition elements](create-uidefinition-elements.md)című témakörben található.
