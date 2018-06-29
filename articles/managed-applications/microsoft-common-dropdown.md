---
title: Az Azure legördülő felhasználói felületi elem |} Microsoft Docs
description: Azure-portálon a Microsoft.Common.DropDown felhasználói felületi elem ismerteti.
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: f953e1dc15e12c37c30a86ebd7536b1126bf18f7
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37062000"
---
# <a name="microsoftcommondropdown-ui-element"></a>Microsoft.Common.DropDown felhasználói felületi elem
A legördülő lista kijelölési vezérlőt.

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

## <a name="remarks"></a>Megjegyzések

- A címke `constraints.allowedValues` a megjelenített szöveg-e egy elem, és a kimeneti értéket, az elem kiválasztásakor értéke.
- Ha meg van adva, az alapértelmezett érték lehet egy címke szerepel `constraints.allowedValues`. Ha nincs megadva, az első elem az `constraints.allowedValues` van kiválasztva. Az alapértelmezett érték **null**.
- `constraints.allowedValues` legalább egy elemmel kell rendelkeznie.
- Nem kötelező értéket emulációjához, vegyen fel egy elemet, címke és értékének `""` (üres karakterlánc) való `constraints.allowedValues`.

## <a name="sample-output"></a>Példa kimenet
```json
"two"
```

## <a name="next-steps"></a>További lépések
* A bevezetést UI-definíciók létrehozásáról lásd: [Ismerkedés a CreateUiDefinition](create-uidefinition-overview.md).
* Általános tulajdonságok felhasználói felületi elemei ismertetését lásd: [CreateUiDefinition elemek](create-uidefinition-elements.md).
