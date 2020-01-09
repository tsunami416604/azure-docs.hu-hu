---
title: OptionsGroup FELHASZNÁLÓIFELÜLET-elem
description: A Azure Portal Microsoft. Common. OptionsGroup felhasználói felületi elemének ismertetése. Lehetővé teszi, hogy a felhasználók a felügyelt alkalmazások telepítésekor a rendelkezésre álló lehetőségek közül választhatnak.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: c1da0b0082bfe046adf9596a10397e9063c888be
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2020
ms.locfileid: "75652345"
---
# <a name="microsoftcommonoptionsgroup-ui-element"></a>Microsoft. Common. OptionsGroup felhasználói felületi elem

Az elérhető lehetőségek sorát tartalmazó kiválasztási vezérlő.

## <a name="ui-sample"></a>Felhasználói felület mintája

![Microsoft. Common. OptionsGroup](./media/managed-application-elements/microsoft.common.optionsgroup.png)

## <a name="schema"></a>Séma

```json
{
  "name": "element1",
  "type": "Microsoft.Common.OptionsGroup",
  "label": "Some options group",
  "defaultValue": "Value two",
  "toolTip": "",
  "constraints": {
    "allowedValues": [
      {
        "label": "Value one",
        "value": "one"
      },
      {
        "label": "Value two",
        "value": "two"
      }
    ],
    "required": true
  },
  "visible": true
}
```

## <a name="sample-output"></a>Példa kimenet

```json
"two"
```

## <a name="remarks"></a>Megjegyzések

- A `constraints.allowedValues` felirata egy elem megjelenített szövege, amelynek értéke az elem kimeneti értéke, ha ki van választva.
- Ha meg van adva, az alapértelmezett értéknek a `constraints.allowedValues`ban található címkének kell lennie. Ha nincs megadva, a `constraints.allowedValues` első eleme alapértelmezés szerint ki van választva. Az alapértelmezett érték **Null**.
- a `constraints.allowedValues`nak legalább egy elemmel kell rendelkeznie.

## <a name="next-steps"></a>Következő lépések

* A felhasználói felületi definíciók létrehozásával kapcsolatban lásd: Bevezetés [a CreateUiDefinition](create-uidefinition-overview.md)használatába.
* A felhasználói felületi elemek általános tulajdonságainak leírását lásd: [CreateUiDefinition-elemek](create-uidefinition-elements.md).
