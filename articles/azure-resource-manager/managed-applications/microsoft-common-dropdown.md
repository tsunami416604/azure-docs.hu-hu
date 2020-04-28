---
title: Legördülő KEZELŐFELÜLETi elem
description: A cikk a Azure Portal Microsoft. Common. DropDown felhasználói felületi elemét ismerteti. A használatával választható lehetőségek közül választhat a felügyelt alkalmazások telepítésekor.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: a09f9695c18f368a585dbcd0d1e654dee4adfa03
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75652384"
---
# <a name="microsoftcommondropdown-ui-element"></a>Microsoft. Common. DropDown felhasználói felületi elem

Egy legördülő listával rendelkező kiválasztási vezérlő.

## <a name="ui-sample"></a>Felhasználói felület mintája

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

- A címkéje `constraints.allowedValues` az elem megjelenített szövege, a értéke pedig az elem kimeneti értéke, ha ki van választva.
- Ha meg van adva, az alapértelmezett értéknek szerepelnie kell `constraints.allowedValues`a címkében. Ha nincs megadva, az első elem `constraints.allowedValues` van kiválasztva. Az alapértelmezett érték **Null**.
- `constraints.allowedValues`legalább egy elemmel kell rendelkeznie.
- Ahhoz, hogy egy értéket ne lehessen emulálni, adjon hozzá egy címkével ellátott és `""` (üres karakterlánc) értéket `constraints.allowedValues`a következőhöz:.

## <a name="next-steps"></a>További lépések

* A felhasználói felületi definíciók létrehozásával kapcsolatban lásd: Bevezetés [a CreateUiDefinition](create-uidefinition-overview.md)használatába.
* A felhasználói felületi elemek általános tulajdonságainak leírását lásd: [CreateUiDefinition-elemek](create-uidefinition-elements.md).
