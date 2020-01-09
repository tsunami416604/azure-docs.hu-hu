---
title: PasswordBox FELHASZNÁLÓIFELÜLET-elem
description: A Azure Portal Microsoft. Common. PasswordBox felhasználói felületi elemének ismertetése. Lehetővé teszi, hogy a felhasználók titkos értéket adjanak a felügyelt alkalmazások telepítésekor.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 5f7d3a47482ccec9778b102d3509b5f8ef343185
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2020
ms.locfileid: "75652306"
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
    "regex": "^[a-zA-Z0-9]{8,}$",
    "validationMessage": "Password must be at least 8 characters long, contain only numbers and letters"
  },
  "options": {
    "hideConfirmation": false
  },
  "visible": true
}
```

## <a name="sample-output"></a>Példa kimenet

```json
"p4ssw0rd"
```

## <a name="remarks"></a>Megjegyzések

- Ez az elem nem támogatja a `defaultValue` tulajdonságot.
- A `constraints`megvalósításának részleteiért lásd: [Microsoft. Common. szövegmező](microsoft-common-textbox.md).
- Ha a `options.hideConfirmation` értéke **true (igaz**), a rendszer elrejti a felhasználó jelszavának megerősítésére szolgáló második szövegmezőt. Az alapértelmezett érték **false (hamis**).

## <a name="next-steps"></a>Következő lépések

* A felhasználói felületi definíciók létrehozásával kapcsolatban lásd: Bevezetés [a CreateUiDefinition](create-uidefinition-overview.md)használatába.
* A felhasználói felületi elemek általános tulajdonságainak leírását lásd: [CreateUiDefinition-elemek](create-uidefinition-elements.md).
