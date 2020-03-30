---
title: Szakasz UI elem
description: A cikk a Microsoft.Common.Section UI elem az Azure Portalon. A portál elemeinek csoportosítására használható felügyelt alkalmazások üzembe helyezéséhez.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 999a7386ee7c5b3ef76f87280cc2d0cd45f143cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75652254"
---
# <a name="microsoftcommonsection-ui-element"></a>Microsoft.Common.Section felhasználói felületi elem

Egy vagy több elemet egy címsor alá csoportosító vezérlő.

## <a name="ui-sample"></a>Felhasználói felület minta

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

- `elements`legalább egy elemmel kell rendelkeznie, és `Microsoft.Common.Section`az összes elemtípust tartalmazhatja, kivéve a .
- Ez az elem nem `toolTip` támogatja a tulajdonságot.

## <a name="sample-output"></a>Példa kimenet
Az elemek `elements`kimeneti értékeinek eléréséhez használja az [alapok()](create-uidefinition-functions.md#basics) vagy [steps()](create-uidefinition-functions.md#steps) függvényeket és a pontjelölést:

```json
steps('configuration').section1.text1
```

A típuselemeknek `Microsoft.Common.Section` nincsenek kimeneti értékeik.

## <a name="next-steps"></a>További lépések

* A felhasználói felületdefiníciók létrehozásának bemutatása a [CreateUiDefinition](create-uidefinition-overview.md)első lépései című témakörben látható.
* A felhasználói felület elemeinek gyakori tulajdonságainak leírását a [CreateUiDefinition elements](create-uidefinition-elements.md)című témakörben található.
