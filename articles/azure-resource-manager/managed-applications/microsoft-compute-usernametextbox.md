---
title: UserNameTextBox FELHASZNÁLÓIFELÜLET-elem
description: Ismerteti a Microsoft. számítás. UserNameTextBox felhasználói felületi elemét Azure Portal. Lehetővé teszi a felhasználók számára a Windows-vagy Linux-felhasználónevek megadását.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: c7544ae7d872a64547cb6c57ce8af9a09fc6c3d8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75651903"
---
# <a name="microsoftcomputeusernametextbox-ui-element"></a>Microsoft. számítás. UserNameTextBox FELHASZNÁLÓIFELÜLET-elem

Egy szövegmező vezérlőelem a Windows-és Linux-felhasználónevek beépített ellenőrzésével.

## <a name="ui-sample"></a>Felhasználói felület mintája

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

- Ha `constraints.required` a értéke **true (igaz**), akkor a szövegmezőnek rendelkeznie kell egy, a sikeres ellenőrzéshez szükséges értékkel. Az alapértelmezett érték **true (igaz**).
- `osPlatform`kötelező megadni, és lehet **Windows** vagy **Linux**.
- `constraints.regex`JavaScript reguláris kifejezési minta. Ha meg van adva, akkor a szövegmező értékének meg kell egyeznie a sikeres ellenőrzéshez szükséges mintázattal. Az alapértelmezett érték **Null**.
- `constraints.validationMessage`egy karakterlánc, amely akkor jeleníthető meg, ha a szövegmező értéke nem felel meg `constraints.regex`a által megadott érvényesítésnek. Ha nincs megadva, a rendszer a szövegmező beépített érvényesítési üzeneteit használja. Az alapértelmezett érték **Null**.
- Ez az elem beépített érvényesítéssel rendelkezik, amely a (z) számára `osPlatform`megadott értéken alapul. A beépített érvényesítést egy egyéni reguláris kifejezéssel együtt is használhatja. Ha meg `constraints.regex` van adva egy érték, akkor a beépített és az egyéni érvényesítés is aktiválódik.

## <a name="next-steps"></a>További lépések

* A felhasználói felületi definíciók létrehozásával kapcsolatban lásd: Bevezetés [a CreateUiDefinition](create-uidefinition-overview.md)használatába.
* A felhasználói felületi elemek általános tulajdonságainak leírását lásd: [CreateUiDefinition-elemek](create-uidefinition-elements.md).
