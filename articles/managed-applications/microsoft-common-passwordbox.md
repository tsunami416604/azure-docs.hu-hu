---
title: "Az Azure által felügyelt alkalmazás PasswordBox felhasználói felületi elem |} Microsoft Docs"
description: "A témakör ismerteti a Microsoft.Common.PasswordBox felhasználói felületi elem Azure által felügyelt alkalmazások"
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
ms.openlocfilehash: 6d9f2b7cf56375d3a609cff20e928307c13bf2b8
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2017
---
# <a name="microsoftcommonpasswordbox-ui-element"></a>Microsoft.Common.PasswordBox felhasználói felületi elem
Ez a vezérlő segítségével adja meg, és erősítse meg a jelszót. Ez az elem használata során [Azure által felügyelt alkalmazások létrehozására](publish-service-catalog-app.md).

## <a name="ui-sample"></a>Felhasználói felület minta
![Microsoft.Common.PasswordBox](./media/managed-application-elements/microsoft.common.passwordbox.png)

## <a name="schema"></a>Séma
```json
{
  "name": "element1",
  "type": "Microsoft.Common.PasswordBox",
  "label": {
    "password": "Password",
    "confirmPassword": "Confirm password"
  },
  "toolTip": "",
  "constraints": {
    "required": true,
    "regex": "",
    "validationMessage": ""
  },
  "options": {
    "hideConfirmation": false
  },
  "visible": true
}
```

## <a name="remarks"></a>Megjegyzések
- Ez az elem nem támogatja a `defaultValue` tulajdonság.
- A megvalósítás részletei `constraints`, lásd: [Microsoft.Common.TextBox](microsoft-common-textbox.md).
- Ha `options.hideConfirmation` értéke **igaz**, erősítse meg a jelszót a második szöveges jelölőnégyzet be van-e rejtve. Az alapértelmezett érték **hamis**.

## <a name="sample-output"></a>Példa kimenet
```json
"p4ssw0rd"
```

## <a name="next-steps"></a>Következő lépések
* Felügyelt alkalmazások bemutatása, lásd: [Azure kezelt alkalmazás – áttekintés](overview.md).
* A bevezetést UI-definíciók létrehozásáról lásd: [Ismerkedés a CreateUiDefinition](create-uidefinition-overview.md).
* Általános tulajdonságok felhasználói felületi elemei ismertetését lásd: [CreateUiDefinition elemek](create-uidefinition-elements.md).
