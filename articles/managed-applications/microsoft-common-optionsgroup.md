---
title: Az Azure OptionsGroup felhasználói felületének eleme | Microsoft Docs
description: A Azure Portal Microsoft. Common. OptionsGroup felhasználói felületi elemének ismertetése. Lehetővé teszi, hogy a felhasználók a felügyelt alkalmazások telepítésekor a rendelkezésre álló lehetőségek közül választhatnak.
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
ms.openlocfilehash: 1210c24687c0cd1f38e33674d297dd37fe4d2995
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151887"
---
# <a name="microsoftcommonoptionsgroup-ui-element"></a>Microsoft. Common. OptionsGroup felhasználói felületi elem

Az elérhető lehetőségek sorát tartalmazó kiválasztási vezérlő.

## <a name="ui-sample"></a>Felhasználói felület mintája

![Microsoft.Common.OptionsGroup](./media/managed-application-elements/microsoft.common.optionsgroup.png)

## <a name="schema"></a>Séma

```json
{
  "name": "element1",
  "type": "Microsoft.Common.OptionsGroup",
  "label": "Some options group",
  "defaultValue": "Value two",
  "toolTip": "",
  "constraints": {
    "allowedValues": [
      {
        "label": "Value one",
        "value": "one"
      },
      {
        "label": "Value two",
        "value": "two"
      }
    ],
    "required": true
  },
  "visible": true
}
```

## <a name="sample-output"></a>Példa kimenet

```json
"two"
```

## <a name="remarks"></a>Megjegyzések

- A `constraints.allowedValues` felirata egy elem megjelenített szövege, amelynek értéke az elem kimeneti értéke, ha ki van választva.
- Ha meg van adva, az alapértelmezett értéknek a `constraints.allowedValues`ban található címkének kell lennie. Ha nincs megadva, a `constraints.allowedValues` első eleme alapértelmezés szerint ki van választva. Az alapértelmezett érték **Null**.
- a `constraints.allowedValues`nak legalább egy elemmel kell rendelkeznie.

## <a name="next-steps"></a>További lépések

* A felhasználói felületi definíciók létrehozásával kapcsolatban lásd: Bevezetés [a CreateUiDefinition](create-uidefinition-overview.md)használatába.
* A felhasználói felületi elemek általános tulajdonságainak leírását lásd: [CreateUiDefinition-elemek](create-uidefinition-elements.md).
