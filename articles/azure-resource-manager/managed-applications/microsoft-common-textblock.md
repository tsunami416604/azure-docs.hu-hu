---
title: TextBlock FELHASZNÁLÓIFELÜLET-elem
description: A Azure Portal Microsoft. Common. TextBlock felhasználói felületi elemének ismertetése. A paranccsal adhat hozzá szöveget az illesztőfelülethez.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 418056cb149f4441bac49db839a0dba40c2bb42d
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2020
ms.locfileid: "75652215"
---
# <a name="microsoftcommontextblock-ui-element"></a>Microsoft. Common. TextBlock felhasználói felületi elem

Egy vezérlőelem, amellyel szöveget adhat hozzá a portál felületéhez.

## <a name="ui-sample"></a>Felhasználói felület mintája

![Microsoft. Common. szövegmező](./media/managed-application-elements/microsoft.common.textblock.png)

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

## <a name="next-steps"></a>Következő lépések

* A felhasználói felületi definíciók létrehozásával kapcsolatban lásd: Bevezetés [a CreateUiDefinition](create-uidefinition-overview.md)használatába.
* A felhasználói felületi elemek általános tulajdonságainak leírását lásd: [CreateUiDefinition-elemek](create-uidefinition-elements.md).
