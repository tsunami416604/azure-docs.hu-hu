---
title: "Az Azure által felügyelt alkalmazás szakasz felhasználói felületi elem |} Microsoft Docs"
description: "A témakör ismerteti a Microsoft.Common.Section felhasználói felületi elem Azure által felügyelt alkalmazások"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/12/2017
ms.author: tomfitz
ms.openlocfilehash: 5a460fde88982c53a7ef3a1ec444d50c1c482fc4
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2017
---
# <a name="microsoftcommonsection-ui-element"></a>Microsoft.Common.Section felhasználói felületi elem
A vezérlő, amely csoportosítja a fejléc alatt egy vagy több elemet. Ez az elem használata során [Azure által felügyelt alkalmazások létrehozására](publish-service-catalog-app.md).

## <a name="ui-sample"></a>Felhasználói felület minta
![Microsoft.Common.Section](./media/managed-application-elements/microsoft.common.section.png)

## <a name="schema"></a>Séma
```json
{
  "name": "section1",
  "type": "Microsoft.Common.Section",
  "label": "Some section",
  "elements": [
    {
      "name": "element1",
      "type": "Microsoft.Common.TextBox",
      "label": "Some text box 1"
    },
    {
      "name": "element2",
      "type": "Microsoft.Common.TextBox",
      "label": "Some text box 2"
    }
  ],
  "visible": true
}
```

## <a name="remarks"></a>Megjegyzések
- `elements`legalább egy elemet kell tartalmaznia, és kivételével az összes elem típust is tartalmazhatnak `Microsoft.Common.Section`.
- Ez az elem nem támogatja a `toolTip` tulajdonság.

## <a name="sample-output"></a>Példa kimenet
A kimeneti értékek elemek eléréséhez `elements`, használja a [basics()](create-uidefinition-functions.md#basics) vagy [steps()](create-uidefinition-functions.md#steps) funkciók és felépítését:

```json
basics('section1').element1
```

Típusú elemek `Microsoft.Common.Section` nincs kimeneti értékűek magukat.

## <a name="next-steps"></a>Következő lépések
* Felügyelt alkalmazások bemutatása, lásd: [Azure kezelt alkalmazás – áttekintés](overview.md).
* A bevezetést UI-definíciók létrehozásáról lásd: [Ismerkedés a CreateUiDefinition](create-uidefinition-overview.md).
* Általános tulajdonságok felhasználói felületi elemei ismertetését lásd: [CreateUiDefinition elemek](create-uidefinition-elements.md).
