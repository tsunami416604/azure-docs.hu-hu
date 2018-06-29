---
title: Az Azure PasswordBox felhasználói felületi elem |} Microsoft Docs
description: Azure-portálon a Microsoft.Common.PasswordBox felhasználói felületi elem ismerteti.
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
ms.openlocfilehash: adaece4a450f758c22ffecb356826962806d9d9b
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37060103"
---
# <a name="microsoftcommonpasswordbox-ui-element"></a>Microsoft.Common.PasswordBox felhasználói felületi elem
Ez a vezérlő segítségével adja meg, és erősítse meg a jelszót.

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

## <a name="next-steps"></a>További lépések
* A bevezetést UI-definíciók létrehozásáról lásd: [Ismerkedés a CreateUiDefinition](create-uidefinition-overview.md).
* Általános tulajdonságok felhasználói felületi elemei ismertetését lásd: [CreateUiDefinition elemek](create-uidefinition-elements.md).
