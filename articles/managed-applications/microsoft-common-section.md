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
ms.openlocfilehash: a48c89785e0a448609026aab53364f6cf704e948
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331652"
---
# <a name="microsoftcommonsection-ui-element"></a>Microsoft. Common. szakasz FELHASZNÁLÓIFELÜLET-elem
Egy vezérlőelem, amely egy vagy több elemet csoportosít egy fejléc alá.

## <a name="ui-sample"></a>Felhasználói felület mintája
![Microsoft. Common. szakasz](./media/managed-application-elements/microsoft.common.section.png)

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
- a `elements` értéknek legalább egy elemnek kell lennie, és a `Microsoft.Common.Section` kivételével minden elemtípus tartozhat.
- Ez az elem nem támogatja a `toolTip` tulajdonságot.

## <a name="sample-output"></a>Példa kimenet
A `elements` elemek kimeneti értékeinek eléréséhez használja az [alapok ()](create-uidefinition-functions.md#basics) vagy a [Steps ()](create-uidefinition-functions.md#steps) függvényt és a dot jelölést:

```json
steps('configuration').section1.text1
```

A `Microsoft.Common.Section` típusú elemeknek nincsenek saját kimeneti értékei.

## <a name="next-steps"></a>Következő lépések
* A felhasználói felületi definíciók létrehozásával kapcsolatban lásd: Bevezetés [a CreateUiDefinition](create-uidefinition-overview.md)használatába.
* A felhasználói felületi elemek általános tulajdonságainak leírását lásd: [CreateUiDefinition-elemek](create-uidefinition-elements.md).
