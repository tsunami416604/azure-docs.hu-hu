---
title: Az Azure szakasz felhasználói felületi elem |} Microsoft Docs
description: Azure-portálon a Microsoft.Common.Section felhasználói felületi elem ismerteti.
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
ms.date: 03/30/2018
ms.author: tomfitz
ms.openlocfilehash: 46ea2e3d404ac3ec9b7f909257451991dbb55f53
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
# <a name="microsoftcommonsection-ui-element"></a>Microsoft.Common.Section UI element
A vezérlő, amely csoportosítja a fejléc alatt egy vagy több elemet.

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
- `elements` legalább egy elemet kell tartalmaznia, és kivételével az összes elem típust is tartalmazhatnak `Microsoft.Common.Section`.
- Ez az elem nem támogatja a `toolTip` tulajdonság.

## <a name="sample-output"></a>Példa kimenet
A kimeneti értékek elemek eléréséhez `elements`, használja a [basics()](create-uidefinition-functions.md#basics) vagy [steps()](create-uidefinition-functions.md#steps) funkciók és felépítését:

```json
basics('section1').element1
```

Típusú elemek `Microsoft.Common.Section` nincs kimeneti értékűek magukat.

## <a name="next-steps"></a>További lépések
* A bevezetést UI-definíciók létrehozásáról lásd: [Ismerkedés a CreateUiDefinition](create-uidefinition-overview.md).
* Általános tulajdonságok felhasználói felületi elemei ismertetését lásd: [CreateUiDefinition elemek](create-uidefinition-elements.md).
