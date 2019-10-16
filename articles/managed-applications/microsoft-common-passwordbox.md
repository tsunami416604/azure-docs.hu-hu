---
title: Az Azure PasswordBox felhasználói felületének eleme | Microsoft Docs
description: A Azure Portal Microsoft. Common. PasswordBox felhasználói felületi elemének ismertetése. Lehetővé teszi, hogy a felhasználók titkos értéket adjanak a felügyelt alkalmazások telepítésekor.
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
ms.openlocfilehash: 4a8b760d113e29efb0efacbd41dcaa7432ecdcfd
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72332804"
---
# <a name="microsoftcommonpasswordbox-ui-element"></a>Microsoft. Common. PasswordBox felhasználói felületi elem
A jelszó megadására és megerősítésére szolgáló vezérlőelem.

## <a name="ui-sample"></a>Felhasználói felület mintája
![Microsoft. Common. PasswordBox](./media/managed-application-elements/microsoft.common.passwordbox.png)

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
- Ez az elem nem támogatja a `defaultValue` tulajdonságot.
- A `constraints` implementációjának részleteiért lásd: [Microsoft. Common. szövegmező](microsoft-common-textbox.md).
- Ha a `options.hideConfirmation` értéke **true (igaz**), a felhasználó jelszavának megerősítésére szolgáló második szövegmező rejtve marad. Az alapértelmezett érték **false (hamis**).

## <a name="sample-output"></a>Példa kimenet
```json
"p4ssw0rd"
```

## <a name="next-steps"></a>Következő lépések
* A felhasználói felületi definíciók létrehozásával kapcsolatban lásd: Bevezetés [a CreateUiDefinition](create-uidefinition-overview.md)használatába.
* A felhasználói felületi elemek általános tulajdonságainak leírását lásd: [CreateUiDefinition-elemek](create-uidefinition-elements.md).
