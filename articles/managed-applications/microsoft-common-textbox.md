---
title: Az Azure szövegmező felhasználói felületi elem |} Microsoft Docs
description: Azure-portálon a Microsoft.Common.TextBox felhasználói felületi elem ismerteti.
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
ms.openlocfilehash: fa3e5fff8080acb9e824ffe27f6c149054804830
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063646"
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
- Ha `constraints.required` értéke **igaz**, akkor a szövegmező sikeresen érvényesíthető értékűnek kell lennie. Az alapértelmezett érték **hamis**.
- `constraints.regex` a JavaScript reguláris kifejezési minta van. Ha meg van adva, majd a szövegmező értékét kell megfelel a mintának sikeresen érvényesíthető. Az alapértelmezett érték **null**.
- `constraints.validationMessage` egy olyan karakterlánc jelenítsen meg, ha a szövegmező értékét érvényesítése sikertelen. Ha nincs megadva, akkor a szövegmező beépített ellenőrzési üzenetek használja. Az alapértelmezett érték **null**.
- Adjon meg egy értéket lehet `constraints.regex` amikor `constraints.required` értéke **hamis**. Ebben a forgatókönyvben egy érték nem szükséges a szövegmező sikeresen érvényesíthető. Ha egy meg van adva, akkor meg kell egyeznie a reguláris kifejezési minta.

## <a name="sample-output"></a>Példa kimenet

```json
"my text value"
```

## <a name="next-steps"></a>További lépések
* A bevezetést UI-definíciók létrehozásáról lásd: [Ismerkedés a CreateUiDefinition](create-uidefinition-overview.md).
* Általános tulajdonságok felhasználói felületi elemei ismertetését lásd: [CreateUiDefinition elemek](create-uidefinition-elements.md).
