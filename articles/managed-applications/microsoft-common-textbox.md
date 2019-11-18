---
title: Az Azure szövegmező felhasználói felületének eleme | Microsoft Docs
description: Ismerteti a Microsoft. Common. szövegmező felhasználói felületének a Azure Portalhoz tartozó elemét. Formázatlan szöveg hozzáadására használható.
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
ms.openlocfilehash: a5f28b763023bb60bca8f79bcaa9f2169281e466
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151098"
---
# <a name="microsoftcommontextbox-ui-element"></a>Microsoft. Common. szövegdoboz felhasználói felületének eleme

Formázatlan szöveg szerkesztésére szolgáló vezérlőelem.

## <a name="ui-sample"></a>Felhasználói felület mintája

![Microsoft.Common.TextBox](./media/managed-application-elements/microsoft.common.textbox.png)

## <a name="schema"></a>Séma

```json
{
  "name": "element1",
  "type": "Microsoft.Common.TextBox",
  "label": "Example text box 1",
  "defaultValue": "my text value",
  "toolTip": "Use only allowed characters",
  "constraints": {
    "required": true,
    "regex": "^[a-z0-9A-Z]{1,30}$",
    "validationMessage": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
  },
  "visible": true
}
```

## <a name="sample-output"></a>Példa kimenet

```json
"my text value"
```

## <a name="remarks"></a>Megjegyzések

- Ha a `constraints.required` értéke **true (igaz**), akkor a szövegmezőnek rendelkeznie kell egy, a sikeres ellenőrzéshez szükséges értékkel. Az alapértelmezett érték **hamis**.
- `constraints.regex` egy JavaScript reguláris kifejezés mintája. Ha meg van adva, akkor a szövegmező értékének meg kell egyeznie a sikeres ellenőrzéshez szükséges mintázattal. Az alapértelmezett érték **Null**.
- `constraints.validationMessage` egy karakterlánc, amely akkor jeleníthető meg, ha a szövegmező értéke sikertelen az ellenőrzés során. Ha nincs megadva, a rendszer a szövegmező beépített érvényesítési üzeneteit használja. Az alapértelmezett érték **Null**.
- `constraints.regex` értéket is megadhat, ha `constraints.required` **hamis**értékre van állítva. Ebben az esetben nincs szükség értékre a szövegmező sikeres ellenőrzéséhez. Ha meg van adva, meg kell egyeznie a reguláris kifejezési mintával.

## <a name="next-steps"></a>További lépések

* A felhasználói felületi definíciók létrehozásával kapcsolatban lásd: Bevezetés [a CreateUiDefinition](create-uidefinition-overview.md)használatába.
* A felhasználói felületi elemek általános tulajdonságainak leírását lásd: [CreateUiDefinition-elemek](create-uidefinition-elements.md).
