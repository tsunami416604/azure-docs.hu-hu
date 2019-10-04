---
title: Az Azure szakasz felhasználói felületi elemben |} A Microsoft Docs
description: Az Azure portal a Microsoft.Common.Section felhasználói felületi elem ismerteti.
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
ms.openlocfilehash: c89b45dd4d8e6c2964f3d2bcbb6c3cef445c79e6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64698894"
---
# <a name="microsoftcommonsection-ui-element"></a>Microsoft.Common.Section UI element
Egy olyan vezérlőelem, amely egy vagy több elemet a fejléc alatt csoportosítja.

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
- `elements` rendelkeznie kell legalább egy elemet, és lehet kivételével minden elemtípus `Microsoft.Common.Section`.
- Ez az elem nem támogatja a `toolTip` tulajdonság.

## <a name="sample-output"></a>Példa kimenet
A kimeneti értékeket az elemek eléréséhez `elements`, használja a [basics()](create-uidefinition-functions.md#basics) vagy [steps()](create-uidefinition-functions.md#steps) függvények és felépítését:

```json
steps('configuration').section1.text1
```

Elementy typu `Microsoft.Common.Section` nincsenek kimeneti értékekre is van.

## <a name="next-steps"></a>További lépések
* Felhasználóifelület-definíciók létrehozása bevezetésért lásd: [CreateUiDefinition használatának első lépései](create-uidefinition-overview.md).
* Egy felhasználói felületi elemeket általános tulajdonságok leírásáért lásd: [CreateUiDefinition elemek](create-uidefinition-elements.md).
