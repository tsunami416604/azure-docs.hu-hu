---
title: Az Azure legördülő felhasználói felületi elem |} Microsoft Docs
description: Azure-portálon a Microsoft.Common.DropDown felhasználói felületi elem ismerteti.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2018
ms.author: tomfitz
ms.openlocfilehash: 6c92304ae623807ffba05dcdbb576e1cef63b10c
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
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
  "label": "Some drop down",
  "defaultValue": "my value",
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
    ]
  },
  "visible": true
}
```

## <a name="remarks"></a>Megjegyzések
- A címke `constraints.allowedValues` a megjelenített szöveg-e egy elem, és a kimeneti értéket, az elem kiválasztásakor értéke.
- Ha meg van adva, az alapértelmezett érték lehet egy címke szerepel `constraints.allowedValues`. Ha nincs megadva, az első elem az `constraints.allowedValues` van kiválasztva. Az alapértelmezett érték **null**.
- `constraints.allowedValues` legalább egy elemet kell tartalmaznia.
- Ez az elem nem támogatja a `constraints.required` tulajdonság. Ez viselkedésének emulációjához, vegyen fel egy elemet, label és értékének `""` (üres karakterlánc) való `constraints.allowedValues`.

## <a name="sample-output"></a>Példa kimenet
```json
"Bar"
```

## <a name="next-steps"></a>További lépések
* A bevezetést UI-definíciók létrehozásáról lásd: [Ismerkedés a CreateUiDefinition](create-uidefinition-overview.md).
* Általános tulajdonságok felhasználói felületi elemei ismertetését lásd: [CreateUiDefinition elemek](create-uidefinition-elements.md).
