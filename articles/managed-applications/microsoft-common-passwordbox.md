---
title: Az Azure PasswordBox felhasználói felületi elemben |} A Microsoft Docs
description: Az Azure portal a Microsoft.Common.PasswordBox felhasználói felületi elem ismerteti.
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
ms.openlocfilehash: 944f59da680c3a058a3cd245cca48d903e44ab87
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64710939"
---
# <a name="microsoftcommonpasswordbox-ui-element"></a>Microsoft.Common.PasswordBox UI element
Egy olyan vezérlőelem, adja meg és erősítsen meg egy jelszót is lehet.

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
- A megvalósítás részleteit, `constraints`, lásd: [Microsoft.Common.TextBox](microsoft-common-textbox.md).
- Ha `options.hideConfirmation` értékre van állítva **igaz**, rejtett szöveget a másodikba általában a felhasználó jelszavát. Az alapértelmezett érték **hamis**.

## <a name="sample-output"></a>Példa kimenet
```json
"p4ssw0rd"
```

## <a name="next-steps"></a>További lépések
* Felhasználóifelület-definíciók létrehozása bevezetésért lásd: [CreateUiDefinition használatának első lépései](create-uidefinition-overview.md).
* Egy felhasználói felületi elemeket általános tulajdonságok leírásáért lásd: [CreateUiDefinition elemek](create-uidefinition-elements.md).
