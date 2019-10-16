---
title: Az Azure TextBlock felhasználói felületének eleme | Microsoft Docs
description: A Azure Portal Microsoft. Common. TextBlock felhasználói felületi elemének ismertetése. A paranccsal adhat hozzá szöveget az illesztőfelülethez.
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
ms.openlocfilehash: da2453889f04352dbabe182772312d70deec4464
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331622"
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
