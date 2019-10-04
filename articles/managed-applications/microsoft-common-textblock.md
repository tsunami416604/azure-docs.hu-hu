---
title: Az Azure TextBlock felhasználói felületi elemben |} A Microsoft Docs
description: Az Azure portal a Microsoft.Common.TextBlock felhasználói felületi elem ismerteti.
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
ms.openlocfilehash: 0dcd8ba1b1bacd990c91ce90ef0013caac446cdc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64722547"
---
# <a name="microsoftcommontextblock-ui-element"></a>Microsoft.Common.TextBlock UI element
Szöveg hozzáadása a portál felületén használható vezérlő.

## <a name="ui-sample"></a>Felhasználói felület minta
![Microsoft.Common.TextBox](./media/managed-application-elements/microsoft.common.textblock.png)

## <a name="schema"></a>Séma
```json
{
  "name": "text1",
  "type": "Microsoft.Common.TextBlock",
  "visible": true,
  "options": {
    "text": "Please provide the configuration values for your application.",
    "link": {
      "label": "Learn more",
      "uri": "https://www.microsoft.com"
    }
  }
}
```

## <a name="sample-output"></a>Példa kimenet

```json
"Please provide the configuration values for your application. Learn more"
```

## <a name="next-steps"></a>További lépések
* Felhasználóifelület-definíciók létrehozása bevezetésért lásd: [CreateUiDefinition használatának első lépései](create-uidefinition-overview.md).
* Egy felhasználói felületi elemeket általános tulajdonságok leírásáért lásd: [CreateUiDefinition elemek](create-uidefinition-elements.md).
