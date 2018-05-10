---
title: Az Azure szövegmező felhasználói felületi elem |} Microsoft Docs
description: Azure-portálon a Microsoft.Common.TextBox felhasználói felületi elem ismerteti.
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
ms.openlocfilehash: 4238d241ec5daacd06485b118a8ccf9b68a9d6d9
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="microsoftcommontextbox-ui-element"></a>Microsoft.Common.TextBox felhasználói felületi elem
Segítségével szerkesztheti a formázatlan szöveges vezérlő.

## <a name="ui-sample"></a>Felhasználói felület minta
![Microsoft.Common.TextBox](./media/managed-application-elements/microsoft.common.textbox.png)

## <a name="schema"></a>Séma
```json
{
  "name": "element1",
  "type": "Microsoft.Common.TextBox",
  "label": "Some text box",
  "defaultValue": "my value",
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
- Ha `constraints.required` értéke **igaz**, majd a szövegmező sikeresen érvényesíthető értéket kell tartalmaznia. Az alapértelmezett érték **hamis**.
- `constraints.regex` a JavaScript reguláris kifejezési minta van. Ha meg van adva, majd a szövegmező értékét kell megfelel a mintának sikeresen érvényesíthető. Az alapértelmezett érték **null**.
- `constraints.validationMessage` egy olyan karakterlánc jelenítsen meg, ha a szövegmező értékét érvényesítése sikertelen. Ha nincs megadva, akkor a szövegmező beépített ellenőrzési üzenetek használja. Az alapértelmezett érték **null**.
- Adjon meg egy értéket lehet `constraints.regex` amikor `constraints.required` értéke **hamis**. Ebben a forgatókönyvben egy érték nincs szükség a szövegmező sikeresen érvényesíthető. Ha egy meg van adva, akkor meg kell egyeznie a reguláris kifejezési minta.

## <a name="sample-output"></a>Példa kimenet

```json
"my value"
```

## <a name="next-steps"></a>További lépések
* A bevezetést UI-definíciók létrehozásáról lásd: [Ismerkedés a CreateUiDefinition](create-uidefinition-overview.md).
* Általános tulajdonságok felhasználói felületi elemei ismertetését lásd: [CreateUiDefinition elemek](create-uidefinition-elements.md).
