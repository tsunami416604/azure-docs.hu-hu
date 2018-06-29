---
title: Az Azure szakasz felhasználói felületi elem |} Microsoft Docs
description: Azure-portálon a Microsoft.Common.Section felhasználói felületi elem ismerteti.
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
ms.openlocfilehash: 90ffae3dd8b05041c34d766e464eb68f793f6066
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37062978"
---
# <a name="microsoftcommonsection-ui-element"></a>Microsoft.Common.Section felhasználói felületi elem
A vezérlő, amely csoportosítja a fejléc alatt egy vagy több elemet.

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
- `elements` rendelkeznie kell legalább egy elemet, és rendelkezhetnek kivételével minden elemtípus `Microsoft.Common.Section`.
- Ez az elem nem támogatja a `toolTip` tulajdonság.

## <a name="sample-output"></a>Példa kimenet
A kimeneti értékek elemek eléréséhez `elements`, használja a [basics()](create-uidefinition-functions.md#basics) vagy [steps()](create-uidefinition-functions.md#steps) funkciók és felépítését:

```json
steps('configuration').section1.text1
```

Típusú elemek `Microsoft.Common.Section` nincs kimeneti értékűek magukat.

## <a name="next-steps"></a>További lépések
* A bevezetést UI-definíciók létrehozásáról lásd: [Ismerkedés a CreateUiDefinition](create-uidefinition-overview.md).
* Általános tulajdonságok felhasználói felületi elemei ismertetését lásd: [CreateUiDefinition elemek](create-uidefinition-elements.md).
