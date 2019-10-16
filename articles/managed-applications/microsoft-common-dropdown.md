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
ms.openlocfilehash: 5784b5df9b522f0489ca1f6087627b45cec5349d
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331746"
---
# <a name="microsoftcommondropdown-ui-element"></a>Microsoft. Common. DropDown felhasználói felületi elem
Egy legördülő listával rendelkező kiválasztási vezérlő.

## <a name="ui-sample"></a>Felhasználói felület mintája
![Microsoft. Common. DropDown](./media/managed-application-elements/microsoft.common.dropdown.png)

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

## <a name="remarks"></a>Megjegyzések

- A `constraints.allowedValues` címkéje egy elem megjelenített szövege, és az értéke az elem kimeneti értéke, ha ki van választva.
- Ha meg van adva, az alapértelmezett értéknek a `constraints.allowedValues` címkének kell lennie. Ha nincs megadva, a `constraints.allowedValues` első eleme van kiválasztva. Az alapértelmezett érték **Null**.
- a `constraints.allowedValues` értéknek legalább egy elemmel kell rendelkeznie.
- Ha nem szükséges értéket emulálni, adjon hozzá egy címkével ellátott, `""` (üres karakterlánc) értéket a `constraints.allowedValues` értékhez.

## <a name="sample-output"></a>Példa kimenet
```json
"two"
```

## <a name="next-steps"></a>Következő lépések
* A felhasználói felületi definíciók létrehozásával kapcsolatban lásd: Bevezetés [a CreateUiDefinition](create-uidefinition-overview.md)használatába.
* A felhasználói felületi elemek általános tulajdonságainak leírását lásd: [CreateUiDefinition-elemek](create-uidefinition-elements.md).
