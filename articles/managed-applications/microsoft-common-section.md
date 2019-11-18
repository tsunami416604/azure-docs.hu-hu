---
title: Az Azure szakasz felhasználói felületének eleme | Microsoft Docs
description: A cikk a Microsoft. Common. szakasz FELHASZNÁLÓIFELÜLET-elemét ismerteti Azure Portalhoz. A segítségével csoportosíthatja az elemeket a portálon a felügyelt alkalmazások telepítéséhez.
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
ms.openlocfilehash: fd2c1105078b918043791fd0f18395409bb32f7c
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151714"
---
# <a name="microsoftcommonsection-ui-element"></a>Microsoft.Common.Section UI element

Egy vezérlőelem, amely egy vagy több elemet csoportosít egy fejléc alá.

## <a name="ui-sample"></a>Felhasználói felület mintája

![Microsoft.Common.Section](./media/managed-application-elements/microsoft.common.section.png)

## <a name="schema"></a>Séma

```json
{
  "name": "section1",
  "type": "Microsoft.Common.Section",
  "label": "Example section",
  "elements": [
    {
      "name": "text1",
      "type": "Microsoft.Common.TextBox",
      "label": "Example text box 1"
    },
    {
      "name": "text2",
      "type": "Microsoft.Common.TextBox",
      "label": "Example text box 2"
    }
  ],
  "visible": true
}
```

## <a name="remarks"></a>Megjegyzések

- a `elements`nak legalább egy elemmel kell rendelkeznie, és a `Microsoft.Common.Section`kivételével minden elemtípus tartozhat.
- Ez az elem nem támogatja a `toolTip` tulajdonságot.

## <a name="sample-output"></a>Példa kimenet
A `elements`elemeinek kimeneti értékeinek eléréséhez használja az [alapok ()](create-uidefinition-functions.md#basics) vagy a [Steps ()](create-uidefinition-functions.md#steps) függvényt és a dot jelölést:

```json
steps('configuration').section1.text1
```

A (`Microsoft.Common.Section` típusú elemeknek nincsenek saját kimeneti értékei.

## <a name="next-steps"></a>További lépések

* A felhasználói felületi definíciók létrehozásával kapcsolatban lásd: Bevezetés [a CreateUiDefinition](create-uidefinition-overview.md)használatába.
* A felhasználói felületi elemek általános tulajdonságainak leírását lásd: [CreateUiDefinition-elemek](create-uidefinition-elements.md).
