---
title: OptionsGroup felhasználói felület i
description: A cikk a Microsoft.Common.OptionsGroup felhasználói felületelemét ismerteti az Azure Portalon. Lehetővé teszi a felhasználók számára, hogy a felügyelt alkalmazások telepítésekor válasszanak a rendelkezésre álló lehetőségek közül.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: c1da0b0082bfe046adf9596a10397e9063c888be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75652345"
---
# <a name="microsoftcommonoptionsgroup-ui-element"></a>Microsoft.Common.OptionsGroup felhasználói felületi elem

Kijelölésvezérlő a rendelkezésre álló lehetőségek sorával.

## <a name="ui-sample"></a>Felhasználói felület minta

![Microsoft.Common.OptionsGroup](./media/managed-application-elements/microsoft.common.optionsgroup.png)

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

- A címke `constraints.allowedValues` egy elem megjelenítendő szövege, és értéke az elem kimeneti értéke, ha ki van jelölve.
- Ha meg van adva, az alapértelmezett `constraints.allowedValues`értéknek a címkéjének kell lennie a alkalmazásban. Ha nincs megadva, az `constraints.allowedValues` első elem alapértelmezés szerint be van jelölve. Az alapértelmezett érték **null**.
- `constraints.allowedValues`legalább egy tételnek rendelkeznie kell.

## <a name="next-steps"></a>További lépések

* A felhasználói felületdefiníciók létrehozásának bemutatása a [CreateUiDefinition](create-uidefinition-overview.md)első lépései című témakörben látható.
* A felhasználói felület elemeinek gyakori tulajdonságainak leírását a [CreateUiDefinition elements](create-uidefinition-elements.md)című témakörben található.
