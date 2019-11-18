---
title: Azure legördülő KEZELŐFELÜLETi elem | Microsoft Docs
description: A cikk a Azure Portal Microsoft. Common. DropDown felhasználói felületi elemét ismerteti. A használatával választható lehetőségek közül választhat a felügyelt alkalmazások telepítésekor.
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
ms.openlocfilehash: e1db91362d17d9100199f0b30119f8024f00a903
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151340"
---
# <a name="microsoftcommondropdown-ui-element"></a>Microsoft.Common.DropDown UI element

Egy legördülő listával rendelkező kiválasztási vezérlő.

## <a name="ui-sample"></a>Felhasználói felület mintája

![Microsoft.Common.DropDown](./media/managed-application-elements/microsoft.common.dropdown.png)

## <a name="schema"></a>Séma

```json
{
  "name": "element1",
  "type": "Microsoft.Common.DropDown",
  "label": "Example drop down",
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
- Ha meg van adva, az alapértelmezett értéknek a `constraints.allowedValues`ban található címkének kell lennie. Ha nincs megadva, a `constraints.allowedValues` első eleme van kiválasztva. Az alapértelmezett érték **Null**.
- a `constraints.allowedValues`nak legalább egy elemmel kell rendelkeznie.
- Ha nem szükséges értéket emulálni, adjon hozzá egy címkével ellátott, `""` (üres karakterlánc) értéket a `constraints.allowedValues`hoz.

## <a name="next-steps"></a>További lépések

* A felhasználói felületi definíciók létrehozásával kapcsolatban lásd: Bevezetés [a CreateUiDefinition](create-uidefinition-overview.md)használatába.
* A felhasználói felületi elemek általános tulajdonságainak leírását lásd: [CreateUiDefinition-elemek](create-uidefinition-elements.md).
