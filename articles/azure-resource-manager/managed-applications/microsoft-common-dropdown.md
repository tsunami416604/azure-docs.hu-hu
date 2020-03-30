---
title: Legördülő felhasználói felület eleme
description: A cikk a Microsoft.Common.DropDown UI elem az Azure Portalon. Felügyelt alkalmazás telepítésekor választhat a rendelkezésre álló lehetőségek közül.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: a09f9695c18f368a585dbcd0d1e654dee4adfa03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75652384"
---
# <a name="microsoftcommondropdown-ui-element"></a>Microsoft.Common.DropDown felhasználói felület eleme

Kijelölésvezérlő legördülő listával.

## <a name="ui-sample"></a>Felhasználói felület minta

![Microsoft.Common.DropDown](./media/managed-application-elements/microsoft.common.dropdown.png)

## <a name="schema"></a>Séma

```json
{
  "name": "element1",
  "type": "Microsoft.Common.DropDown",
  "label": "Example drop down",
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
- Ha meg van adva, az alapértelmezett `constraints.allowedValues`értéknek a címkéjének kell lennie a alkalmazásban. Ha nincs megadva, az `constraints.allowedValues` első elem lesz kiválasztva. Az alapértelmezett érték **null**.
- `constraints.allowedValues`legalább egy tételnek rendelkeznie kell.
- Ha nem kötelező értéket szeretne emulálni, adjon `""` hozzá egy elemet `constraints.allowedValues`címkével és értékkel (üres karakterlánc) a alkalmazáshoz.

## <a name="next-steps"></a>További lépések

* A felhasználói felületdefiníciók létrehozásának bemutatása a [CreateUiDefinition](create-uidefinition-overview.md)első lépései című témakörben látható.
* A felhasználói felület elemeinek gyakori tulajdonságainak leírását a [CreateUiDefinition elements](create-uidefinition-elements.md)című témakörben található.
