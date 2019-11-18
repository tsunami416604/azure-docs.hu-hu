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
ms.openlocfilehash: ab51a4096745c2930199685ac678638a956f21e0
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151541"
---
# <a name="microsoftcommonpasswordbox-ui-element"></a>Microsoft.Common.PasswordBox UI element

A jelszó megadására és megerősítésére szolgáló vezérlőelem.

## <a name="ui-sample"></a>Felhasználói felület mintája

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
- Ha a `options.hideConfirmation` értéke **true (igaz**), a rendszer elrejti a felhasználó jelszavának megerősítésére szolgáló második szövegmezőt. Az alapértelmezett érték **hamis**.

## <a name="next-steps"></a>További lépések

* A felhasználói felületi definíciók létrehozásával kapcsolatban lásd: Bevezetés [a CreateUiDefinition](create-uidefinition-overview.md)használatába.
* A felhasználói felületi elemek általános tulajdonságainak leírását lásd: [CreateUiDefinition-elemek](create-uidefinition-elements.md).
