---
title: Szakasz felhasználói felületének eleme
description: A cikk a Microsoft. Common. szakasz FELHASZNÁLÓIFELÜLET-elemét ismerteti Azure Portalhoz. A segítségével csoportosíthatja az elemeket a portálon a felügyelt alkalmazások telepítéséhez.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 999a7386ee7c5b3ef76f87280cc2d0cd45f143cd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75652254"
---
# <a name="microsoftcommonsection-ui-element"></a>Microsoft. Common. szakasz FELHASZNÁLÓIFELÜLET-elem

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

- `elements`tartalmaznia kell legalább egy elemet, és az összes elem típusát, kivéve `Microsoft.Common.Section`a következőt:.
- Ez az elem nem támogatja `toolTip` a tulajdonságot.

## <a name="sample-output"></a>Példa kimenet
A elemek `elements`kimeneti értékeinek eléréséhez használja az alapértékek [()](create-uidefinition-functions.md#basics) vagy a [Steps ()](create-uidefinition-functions.md#steps) függvényt és a dot jelölést:

```json
steps('configuration').section1.text1
```

A Type `Microsoft.Common.Section` elemeknek nincsenek saját kimeneti értékei.

## <a name="next-steps"></a>További lépések

* A felhasználói felületi definíciók létrehozásával kapcsolatban lásd: Bevezetés [a CreateUiDefinition](create-uidefinition-overview.md)használatába.
* A felhasználói felületi elemek általános tulajdonságainak leírását lásd: [CreateUiDefinition-elemek](create-uidefinition-elements.md).
