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
ms.openlocfilehash: 7bec506575f1c526e59487edb67349cdf862f1a7
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331668"
---
# <a name="microsoftcommonoptionsgroup-ui-element"></a>Microsoft. Common. OptionsGroup felhasználói felületi elem
Az elérhető lehetőségek sorát tartalmazó kiválasztási vezérlő.

## <a name="ui-sample"></a>Felhasználói felület mintája
![Microsoft. Common. OptionsGroup](./media/managed-application-elements/microsoft.common.optionsgroup.png)

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

## <a name="remarks"></a>Megjegyzések
- A `constraints.allowedValues` címkéje egy elem megjelenített szövege, és az értéke az elem kimeneti értéke, ha ki van választva.
- Ha meg van adva, az alapértelmezett értéknek a `constraints.allowedValues` címkének kell lennie. Ha nincs megadva, a `constraints.allowedValues` első eleme alapértelmezés szerint ki van választva. Az alapértelmezett érték **Null**.
- a `constraints.allowedValues` értéknek legalább egy elemmel kell rendelkeznie.

## <a name="sample-output"></a>Példa kimenet
```json
"two"
```

## <a name="next-steps"></a>Következő lépések
* A felhasználói felületi definíciók létrehozásával kapcsolatban lásd: Bevezetés [a CreateUiDefinition](create-uidefinition-overview.md)használatába.
* A felhasználói felületi elemek általános tulajdonságainak leírását lásd: [CreateUiDefinition-elemek](create-uidefinition-elements.md).
