---
title: "Az Azure által felügyelt alkalmazás UserNameTextBox felhasználói felületi elem |} Microsoft Docs"
description: "A témakör ismerteti a Microsoft.Compute.UserNameTextBox felhasználói felületi elem Azure által felügyelt alkalmazások"
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
ms.openlocfilehash: 6a395915af274750eb57a085ee51b55fdd392615
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2017
---
# <a name="microsoftcomputeusernametextbox-ui-element"></a>Microsoft.Compute.UserNameTextBox felhasználói felületi elem
A szövegmező-vezérlőt a Windows és Linux-felhasználónevek beépített érvényesítéssel. Ez az elem használata során [Azure által felügyelt alkalmazások létrehozására](publish-service-catalog-app.md).

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
- Ha `constraints.required` értéke **igaz**, majd a szövegmező sikeresen érvényesíthető értéket kell tartalmaznia. Az alapértelmezett érték **igaz**.
- `osPlatform`meg kell adni, és lehet **Windows** vagy **Linux**.
- `constraints.regex`a JavaScript reguláris kifejezési minta van. Ha meg van adva, majd a szövegmező értékét kell megfelel a mintának sikeresen érvényesíthető. Az alapértelmezett érték **null**.
- `constraints.validationMessage`Ha a szövegmező értékét nem sikerült a megadott érvényesítési megjelenítendő karakterlánc `constraints.regex`. Ha nincs megadva, akkor a szövegmező beépített ellenőrzési üzenetek használja. Az alapértelmezett érték **null**.
- Ez az elem van a megadott alapuló beépített ellenőrzési `osPlatform`. A beépített ellenőrzési együtt egy egyéni reguláris kifejezést is használható.
Ha értéket `constraints.regex` van megadva, a beépített és egyéni ellenőrzések aktiválódnak.

## <a name="sample-output"></a>Példa kimenet
```json
"tabrezm"
```

## <a name="next-steps"></a>Következő lépések
* Felügyelt alkalmazások bemutatása, lásd: [Azure kezelt alkalmazás – áttekintés](overview.md).
* A bevezetést UI-definíciók létrehozásáról lásd: [Ismerkedés a CreateUiDefinition](create-uidefinition-overview.md).
* Általános tulajdonságok felhasználói felületi elemei ismertetését lásd: [CreateUiDefinition elemek](create-uidefinition-elements.md).
