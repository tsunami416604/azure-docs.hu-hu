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
ms.openlocfilehash: 083b0be20d4e9ad7769a9c673139a361fb7b70d4
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74009171"
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

## <a name="remarks"></a>Megjegyzések
- Ez az elem nem támogatja a `defaultValue` tulajdonságot.
- A `constraints`megvalósításának részleteiért lásd: [Microsoft. Common. szövegmező](microsoft-common-textbox.md).
- Ha a `options.hideConfirmation` értéke **true (igaz**), a rendszer elrejti a felhasználó jelszavának megerősítésére szolgáló második szövegmezőt. Az alapértelmezett érték **hamis**.

## <a name="sample-output"></a>Példa kimenet
```json
"p4ssw0rd"
```

## <a name="next-steps"></a>Következő lépések
* A felhasználói felületi definíciók létrehozásával kapcsolatban lásd: Bevezetés [a CreateUiDefinition](create-uidefinition-overview.md)használatába.
* A felhasználói felületi elemek általános tulajdonságainak leírását lásd: [CreateUiDefinition-elemek](create-uidefinition-elements.md).
