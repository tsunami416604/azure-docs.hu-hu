---
title: Az Azure UserNameTextBox felhasználói felületi elemben |} A Microsoft Docs
description: Az Azure portal a Microsoft.Compute.UserNameTextBox felhasználói felületi elem ismerteti.
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
ms.openlocfilehash: 88ab13329a719ba1e1b8a7b5fba2f7a2d381eca2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64700845"
---
# <a name="microsoftcomputeusernametextbox-ui-element"></a>Microsoft.Compute.UserNameTextBox felhasználói felületi elemben
Szövegmező vezérlőelem a Windows és Linux-felhasználónevek beépített érvényesítése.

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
- Ha `constraints.required` értékre van állítva **igaz**, majd a szövegmező sikeresen érvényesíthető értékkel kell rendelkeznie. Az alapértelmezett érték **igaz**.
- `osPlatform` meg kell adni, és lehet **Windows** vagy **Linux**.
- `constraints.regex` van egy JavaScript Reguláriskifejezés-mintának. Ha meg van adva, majd a szövegmező értéknek egyeznie kell a minta ellenőrzése sikerült. Az alapértelmezett érték **null**.
- `constraints.validationMessage` a beviteli mező értéke által meghatározott érvényesítése sikertelen megjelenített karakterlánc `constraints.regex`. Ha nincs megadva, a szövegmező beépített ellenőrzési üzenetek szolgálnak. Az alapértelmezett érték **null**.
- Ez az elem rendelkezik, amely a megadott érték alapján beépített ellenőrzési `osPlatform`. A beépített ellenőrzési együtt egy egyéni reguláris kifejezést is használható. Ha `constraints.regex` van megadva, a beépített és egyéni ellenőrzés esetén.

## <a name="sample-output"></a>Példa kimenet
```json
"Example name"
```

## <a name="next-steps"></a>További lépések
* Felhasználóifelület-definíciók létrehozása bevezetésért lásd: [CreateUiDefinition használatának első lépései](create-uidefinition-overview.md).
* Egy felhasználói felületi elemeket általános tulajdonságok leírásáért lásd: [CreateUiDefinition elemek](create-uidefinition-elements.md).
