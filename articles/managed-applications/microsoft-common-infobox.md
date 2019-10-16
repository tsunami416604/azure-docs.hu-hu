---
title: Az Azure bezárása felhasználói felületének eleme | Microsoft Docs
description: A Azure Portal Microsoft. Common. bezárása felhasználói felületi elemének ismertetése. A segítségével szöveget vagy figyelmeztetéseket adhat hozzá a felügyelt alkalmazás telepítésekor.
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
ms.date: 06/15/2018
ms.author: tomfitz
ms.openlocfilehash: 67ae05c2ec7b36d76e49f26d5765bbc68b952292
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331688"
---
# <a name="microsoftcommoninfobox-ui-element"></a>Microsoft. Common. bezárása felhasználói felületi elem
Egy adatmezőt tartalmazó vezérlőelem. A mező olyan fontos szöveget vagy figyelmeztetéseket tartalmaz, amelyek segítségével a felhasználók megismerhetik az általuk nyújtott értékeket. További információkhoz is hivatkozhat egy URI-ra.

## <a name="ui-sample"></a>Felhasználói felület mintája
![Microsoft. Common. bezárása](./media/managed-application-elements/microsoft.common.infobox.png)


## <a name="schema"></a>Séma
```json
{
  "name": "text1",
  "type": "Microsoft.Common.InfoBox",
  "visible": true,
  "options": {
    "icon": "None",
    "text": "Nullam eros mi, mollis in sollicitudin non, tincidunt sed enim. Sed et felis metus, rhoncus ornare nibh. Ut at magna leo.",
    "uri": "https://www.microsoft.com"
  }
}
```

## <a name="remarks"></a>Megjegyzések

* @No__t – 0 esetén használja a **none**, az **info**, a **Warning**vagy a **Error**értéket.
* A `uri` tulajdonság nem kötelező.

## <a name="sample-output"></a>Példa kimenet

```json
"Nullam eros mi, mollis in sollicitudin non, tincidunt sed enim. Sed et felis metus, rhoncus ornare nibh. Ut at magna leo."
```

## <a name="next-steps"></a>Következő lépések
* A felhasználói felületi definíciók létrehozásával kapcsolatban lásd: Bevezetés [a CreateUiDefinition](create-uidefinition-overview.md)használatába.
* A felhasználói felületi elemek általános tulajdonságainak leírását lásd: [CreateUiDefinition-elemek](create-uidefinition-elements.md).
