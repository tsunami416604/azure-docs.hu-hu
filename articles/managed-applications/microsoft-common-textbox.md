---
title: "Az Azure által felügyelt alkalmazás szövegmező felhasználói felületi elem |} Microsoft Docs"
description: "A témakör ismerteti a Microsoft.Common.TextBox felhasználói felületi elem Azure által felügyelt alkalmazások"
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
ms.date: 10/12/2017
ms.author: tomfitz
ms.openlocfilehash: d3fae42ae202fe720761382e1020fa8bd8c62b44
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2017
---
# <a name="microsoftcommontextbox-ui-element"></a>Microsoft.Common.TextBox felhasználói felületi elem
Segítségével szerkesztheti a formázatlan szöveges vezérlő. Ez az elem használata során [Azure által felügyelt alkalmazások létrehozására](publish-service-catalog-app.md).

## <a name="ui-sample"></a>Felhasználói felület minta
![Microsoft.Common.TextBox](./media/managed-application-elements/microsoft.common.textbox.png)

## <a name="schema"></a>Séma
```json
{
  "name": "element1",
  "type": "Microsoft.Common.TextBox",
  "label": "Some text box",
  "defaultValue": "foobar",
  "toolTip": "Halp!",
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
- `constraints.regex`a JavaScript reguláris kifejezési minta van. Ha meg van adva, majd a szövegmező értékét kell megfelel a mintának sikeresen érvényesíthető. Az alapértelmezett érték **null**.
- `constraints.validationMessage`egy olyan karakterlánc jelenítsen meg, ha a szövegmező értékét érvényesítése sikertelen. Ha nincs megadva, akkor a szövegmező beépített ellenőrzési üzenetek használja. Az alapértelmezett érték **null**.
- Adjon meg egy értéket lehet `constraints.regex` amikor `constraints.required` értéke **hamis**. Ebben a forgatókönyvben egy érték nincs szükség a szövegmező sikeresen érvényesíthető. Ha egy meg van adva, akkor meg kell egyeznie a reguláris kifejezési minta.

## <a name="sample-output"></a>Példa kimenet

```json
"foobar"
```

## <a name="next-steps"></a>Következő lépések
* Felügyelt alkalmazások bemutatása, lásd: [Azure kezelt alkalmazás – áttekintés](overview.md).
* A bevezetést UI-definíciók létrehozásáról lásd: [Ismerkedés a CreateUiDefinition](create-uidefinition-overview.md).
* Általános tulajdonságok felhasználói felületi elemei ismertetését lásd: [CreateUiDefinition elemek](create-uidefinition-elements.md).
