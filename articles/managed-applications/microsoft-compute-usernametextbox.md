---
title: Az Azure UserNameTextBox felhasználói felületének eleme | Microsoft Docs
description: Ismerteti a Microsoft. számítás. UserNameTextBox felhasználói felületi elemét Azure Portal. Lehetővé teszi a felhasználók számára a Windows-vagy Linux-felhasználónevek megadását.
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
ms.openlocfilehash: 875c73c546fa52642959e2593d41f9af82c13797
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331580"
---
# <a name="microsoftcomputeusernametextbox-ui-element"></a>Microsoft. számítás. UserNameTextBox FELHASZNÁLÓIFELÜLET-elem
Egy szövegmező vezérlőelem a Windows-és Linux-felhasználónevek beépített ellenőrzésével.

## <a name="ui-sample"></a>Felhasználói felület mintája
![Microsoft. számítás. UserNameTextBox](./media/managed-application-elements/microsoft.compute.usernametextbox.png)

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
- Ha a `constraints.required` értéke **true (igaz**), akkor a szövegmezőnek rendelkeznie kell egy, a sikeres ellenőrzéshez szükséges értékkel. Az alapértelmezett érték **true (igaz**).
- meg kell adni a `osPlatform` értéket, és lehet **Windows** vagy **Linux**.
- a `constraints.regex` JavaScript reguláris kifejezési minta. Ha meg van adva, akkor a szövegmező értékének meg kell egyeznie a sikeres ellenőrzéshez szükséges mintázattal. Az alapértelmezett érték **Null**.
- @no__t – 0 karakterlánc, amely akkor jeleníthető meg, ha a szövegmező értéke sikertelen a `constraints.regex` által megadott érvényesítésnél. Ha nincs megadva, a rendszer a szövegmező beépített érvényesítési üzeneteit használja. Az alapértelmezett érték **Null**.
- Ez az elem beépített érvényesítéssel rendelkezik, amely a `osPlatform` számára megadott értéken alapul. A beépített érvényesítést egy egyéni reguláris kifejezéssel együtt is használhatja. Ha a `constraints.regex` érték van megadva, akkor a beépített és az egyéni érvényesítés is aktiválódik.

## <a name="sample-output"></a>Példa kimenet
```json
"Example name"
```

## <a name="next-steps"></a>Következő lépések
* A felhasználói felületi definíciók létrehozásával kapcsolatban lásd: Bevezetés [a CreateUiDefinition](create-uidefinition-overview.md)használatába.
* A felhasználói felületi elemek általános tulajdonságainak leírását lásd: [CreateUiDefinition-elemek](create-uidefinition-elements.md).
