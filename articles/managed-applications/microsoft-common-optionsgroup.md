---
title: Az Azure OptionsGroup felhasználói felületi elemben |} A Microsoft Docs
description: Az Azure portal a Microsoft.Common.OptionsGroup felhasználói felületi elem ismerteti.
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
ms.openlocfilehash: 9aee881844e9338cc1da2484a94c8355f2516c82
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60252003"
---
# <a name="microsoftcommonoptionsgroup-ui-element"></a>Microsoft.Common.OptionsGroup UI element
A rendelkezésre álló lehetőségek sor kijelölési vezérlő.

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

## <a name="remarks"></a>Megjegyzések
- A címke `constraints.allowedValues` egy elem a megjelenített szöveg, és a kimeneti érték, amikor a kijelölt elem értéke.
- Ha meg van adva, az alapértelmezett érték lehet egy címke szerepel `constraints.allowedValues`. Ha nincs megadva, az első elem az `constraints.allowedValues` alapértelmezés szerint ki van választva. Az alapértelmezett érték **null**.
- `constraints.allowedValues` rendelkeznie kell legalább egy elemet.

## <a name="sample-output"></a>Példa kimenet
```json
"two"
```

## <a name="next-steps"></a>További lépések
* Felhasználóifelület-definíciók létrehozása bevezetésért lásd: [CreateUiDefinition használatának első lépései](create-uidefinition-overview.md).
* Egy felhasználói felületi elemeket általános tulajdonságok leírásáért lásd: [CreateUiDefinition elemek](create-uidefinition-elements.md).
