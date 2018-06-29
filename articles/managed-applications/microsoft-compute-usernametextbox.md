---
title: Az Azure UserNameTextBox felhasználói felületi elem |} Microsoft Docs
description: Azure-portálon a Microsoft.Compute.UserNameTextBox felhasználói felületi elem ismerteti.
services: managed-applications
documentationcenter: na
author: tfitzmac
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 9f07c5bf9ba1f1880fa142beb52455522425e68d
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063301"
---
# <a name="microsoftcomputeusernametextbox-ui-element"></a>Microsoft.Compute.UserNameTextBox felhasználói felületi elem
A szövegmező-vezérlőt a Windows és Linux-felhasználónevek beépített érvényesítéssel.

## <a name="ui-sample"></a>Felhasználói felület minta
![Microsoft.Compute.UserNameTextBox](./media/managed-application-elements/microsoft.compute.usernametextbox.png)

## <a name="schema"></a>Séma
```json
{
  "name": "element1",
  "type": "Microsoft.Compute.UserNameTextBox",
  "label": "User name",
  "defaultValue": "",
  "toolTip": "",
  "constraints": {
    "required": true,
    "regex": "^[a-z0-9A-Z]{1,30}$",
    "validationMessage": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
  },
  "osPlatform": "Windows",
  "visible": true
}
```

## <a name="remarks"></a>Megjegyzések
- Ha `constraints.required` értéke **igaz**, akkor a szövegmező sikeresen érvényesíthető értékűnek kell lennie. Az alapértelmezett érték **igaz**.
- `osPlatform` meg kell adni, és lehet **Windows** vagy **Linux**.
- `constraints.regex` a JavaScript reguláris kifejezési minta van. Ha meg van adva, majd a szövegmező értékét kell megfelel a mintának sikeresen érvényesíthető. Az alapértelmezett érték **null**.
- `constraints.validationMessage` Ha a szövegmező értékét nem sikerült a megadott érvényesítési megjelenítendő karakterlánc `constraints.regex`. Ha nincs megadva, akkor a szövegmező beépített ellenőrzési üzenetek használja. Az alapértelmezett érték **null**.
- Ez az elem van a megadott alapuló beépített ellenőrzési `osPlatform`. A beépített ellenőrzési együtt egy egyéni reguláris kifejezést is használható. Ha értéket `constraints.regex` van megadva, a beépített és egyéni ellenőrzések aktiválódnak.

## <a name="sample-output"></a>Példa kimenet
```json
"Example name"
```

## <a name="next-steps"></a>További lépések
* A bevezetést UI-definíciók létrehozásáról lásd: [Ismerkedés a CreateUiDefinition](create-uidefinition-overview.md).
* Általános tulajdonságok felhasználói felületi elemei ismertetését lásd: [CreateUiDefinition elemek](create-uidefinition-elements.md).
