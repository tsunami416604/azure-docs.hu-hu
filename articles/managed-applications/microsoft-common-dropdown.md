---
title: Az Azure legördülő felhasználói felületi elemben |} A Microsoft Docs
description: Az Azure portal a Microsoft.Common.DropDown felhasználói felületi elem ismerteti.
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
ms.openlocfilehash: e78fa419b067c0bad48229dcfd8d4e986fc16903
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "62117301"
---
# <a name="microsoftcommondropdown-ui-element"></a>Microsoft.Common.DropDown UI element
Egy legördülő lista kiválasztása vezérlőt.

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

- A címke `constraints.allowedValues` egy elem a megjelenített szöveg, és a kimeneti érték, amikor a kijelölt elem értéke.
- Ha meg van adva, az alapértelmezett érték lehet egy címke szerepel `constraints.allowedValues`. Ha nincs megadva, az első elem az `constraints.allowedValues` van kiválasztva. Az alapértelmezett érték **null**.
- `constraints.allowedValues` rendelkeznie kell legalább egy elemet.
- Nem kötelező értéket emulációjához, vegyen fel egy elemet, egy címke és érték `""` (üres karakterlánc) való `constraints.allowedValues`.

## <a name="sample-output"></a>Példa kimenet
```json
"two"
```

## <a name="next-steps"></a>További lépések
* Felhasználóifelület-definíciók létrehozása bevezetésért lásd: [CreateUiDefinition használatának első lépései](create-uidefinition-overview.md).
* Egy felhasználói felületi elemeket általános tulajdonságok leírásáért lásd: [CreateUiDefinition elemek](create-uidefinition-elements.md).
