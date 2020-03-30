---
title: PasswordBox felhasználói felület eleme
description: A cikk a Microsoft.Common.PasswordBox felhasználói felületi elemét ismerteti az Azure Portalon. Lehetővé teszi a felhasználók számára, hogy titkos értéket adjanak meg a felügyelt alkalmazások telepítésekor.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 5f7d3a47482ccec9778b102d3509b5f8ef343185
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75652306"
---
# <a name="microsoftcommonpasswordbox-ui-element"></a>Microsoft.Common.PasswordBox felhasználói felület

A jelszó megadására és megerősítésére használható vezérlő.

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

- Ez az elem nem `defaultValue` támogatja a tulajdonságot.
- A megvalósításrészleteiről a `constraints` [Microsoft.Common.TextBox oldalon talál.](microsoft-common-textbox.md)
- Ha `options.hideConfirmation` értéke **igaz,** a felhasználó jelszavának megerősítésére szolgáló második szövegmező rejtett. Az alapértelmezett érték: **hamis**.

## <a name="next-steps"></a>További lépések

* A felhasználói felületdefiníciók létrehozásának bemutatása a [CreateUiDefinition](create-uidefinition-overview.md)első lépései című témakörben látható.
* A felhasználói felület elemeinek gyakori tulajdonságainak leírását a [CreateUiDefinition elements](create-uidefinition-elements.md)című témakörben található.
