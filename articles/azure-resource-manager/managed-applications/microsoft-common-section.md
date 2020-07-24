---
title: Szakasz felhasználói felületének eleme
description: A cikk a Microsoft. Common. szakasz FELHASZNÁLÓIFELÜLET-elemét ismerteti Azure Portalhoz. A segítségével csoportosíthatja az elemeket a portálon a felügyelt alkalmazások telepítéséhez.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 924aff8f2ba3d796b65f52494845f3b10018065c
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87063965"
---
# <a name="microsoftcommonsection-ui-element"></a>Microsoft. Common. szakasz FELHASZNÁLÓIFELÜLET-elem

Egy vezérlőelem, amely egy vagy több elemet csoportosít egy fejléc alá.

## <a name="ui-sample"></a>Felhasználói felület mintája

![Microsoft.Common.Section](./media/managed-application-elements/microsoft-common-section.png)

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

- `elements`tartalmaznia kell legalább egy elemet, és az összes elem típusát, kivéve a következőt: `Microsoft.Common.Section` .
- Ez az elem nem támogatja a `toolTip` tulajdonságot.

## <a name="sample-output"></a>Példa kimenet
A elemek kimeneti értékeinek eléréséhez használja az alapértékek `elements` [()](create-ui-definition-referencing-functions.md#basics) vagy a [Steps ()](create-ui-definition-referencing-functions.md#steps) függvényt és a dot jelölést:

```json
steps('configuration').section1.text1
```

A Type elemeknek `Microsoft.Common.Section` nincsenek saját kimeneti értékei.

## <a name="next-steps"></a>További lépések

* A felhasználói felületi definíciók létrehozásával kapcsolatban lásd: Bevezetés [a CreateUiDefinition](create-uidefinition-overview.md)használatába.
* A felhasználói felületi elemek általános tulajdonságainak leírását lásd: [CreateUiDefinition-elemek](create-uidefinition-elements.md).
