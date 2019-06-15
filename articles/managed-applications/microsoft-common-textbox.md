---
title: Az Azure szövegmező felhasználói felületi elemben |} A Microsoft Docs
description: Az Azure portal a Microsoft.Common.TextBox felhasználói felületi elem ismerteti.
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
ms.openlocfilehash: b06e8b49efe8b6de720fa9bb819d4720e4f80fb6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "61044556"
---
# <a name="microsoftcommontextbox-ui-element"></a>Microsoft.Common.TextBox UI element
Segítségével szerkesztheti a formázatlan szöveges vezérlő.

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

## <a name="remarks"></a>Megjegyzések
- Ha `constraints.required` értékre van állítva **igaz**, majd a szövegmező sikeresen érvényesíthető értékkel kell rendelkeznie. Az alapértelmezett érték **hamis**.
- `constraints.regex` van egy JavaScript Reguláriskifejezés-mintának. Ha meg van adva, majd a szövegmező értéknek egyeznie kell a minta ellenőrzése sikerült. Az alapértelmezett érték **null**.
- `constraints.validationMessage` : a szövegmező értékét érvényesítése sikertelen megjelenített karakterlánc. Ha nincs megadva, a szövegmező beépített ellenőrzési üzenetek szolgálnak. Az alapértelmezett érték **null**.
- Adjon meg egy értéket meg lehet `constraints.regex` amikor `constraints.required` értékre van állítva **hamis**. Ebben a forgatókönyvben egy érték nem sikerült érvényesíteni a beviteli mező szükséges. Ha meg van adva, akkor meg kell egyeznie a reguláris kifejezési minta.

## <a name="sample-output"></a>Példa kimenet

```json
"my text value"
```

## <a name="next-steps"></a>További lépések
* Felhasználóifelület-definíciók létrehozása bevezetésért lásd: [CreateUiDefinition használatának első lépései](create-uidefinition-overview.md).
* Egy felhasználói felületi elemeket általános tulajdonságok leírásáért lásd: [CreateUiDefinition elemek](create-uidefinition-elements.md).
