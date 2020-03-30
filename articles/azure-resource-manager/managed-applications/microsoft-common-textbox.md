---
title: TextBox felhasználói felülete
description: A cikk a Microsoft.Common.TextBox Felhasználói felület elemét ismerteti az Azure Portalon. Formázatlan szöveg hozzáadására használható.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: e9f084badda9ea1905e43c6f00b29aaf957a6dbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75652280"
---
# <a name="microsoftcommontextbox-ui-element"></a>Microsoft.Common.TextBox felhasználói felület

A formázatlan szöveg szerkesztésére használható vezérlő.

## <a name="ui-sample"></a>Felhasználói felület minta

![Microsoft.Common.TextBox](./media/managed-application-elements/microsoft.common.textbox.png)

## <a name="schema"></a>Séma

```json
{
  "name": "element1",
  "type": "Microsoft.Common.TextBox",
  "label": "Example text box 1",
  "defaultValue": "my text value",
  "toolTip": "Use only allowed characters",
  "constraints": {
    "required": true,
    "regex": "^[a-z0-9A-Z]{1,30}$",
    "validationMessage": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
  },
  "visible": true
}
```

## <a name="sample-output"></a>Példa kimenet

```json
"my text value"
```

## <a name="remarks"></a>Megjegyzések

- Ha `constraints.required` értéke **igaz,** akkor a szövegdoboznak kell egy értékkel a sikeres érvényesítéshez. Az alapértelmezett érték: **hamis**.
- `constraints.regex`egy JavaScript reguláris kifejezésminta. Ha meg van adva, akkor a szövegdoboz értékének meg kell egyeznie a mintával a sikeres érvényesítéshez. Az alapértelmezett érték **null**.
- `constraints.validationMessage`egy karakterlánc, amely akkor jelenik meg, ha a szövegdoboz értéke nem érvényesítése sikertelen. Ha nincs megadva, akkor a program a szövegdoboz beépített érvényesítési üzeneteit használja. Az alapértelmezett érték **null**.
- Meg adható annak az értéknek a `constraints.regex` megadása, ha `constraints.required` **hamis**értékre van állítva. Ebben az esetben a szövegmező sikeres érvényesítéséhez nincs szükség értékre. Ha van megadva, annak meg kell egyeznie a reguláris kifejezés mintájával.

## <a name="next-steps"></a>További lépések

* A felhasználói felületdefiníciók létrehozásának bemutatása a [CreateUiDefinition](create-uidefinition-overview.md)első lépései című témakörben látható.
* A felhasználói felület elemeinek gyakori tulajdonságainak leírását a [CreateUiDefinition elements](create-uidefinition-elements.md)című témakörben található.
