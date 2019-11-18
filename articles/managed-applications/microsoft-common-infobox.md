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
ms.openlocfilehash: 3a72aaaa15b55b2f0fbc0a227c36a4b2f624d43b
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151328"
---
# <a name="microsoftcommoninfobox-ui-element"></a>Microsoft.Common.InfoBox UI element

Egy adatmezőt tartalmazó vezérlőelem. A mező olyan fontos szöveget vagy figyelmeztetéseket tartalmaz, amelyek segítségével a felhasználók megismerhetik az általuk nyújtott értékeket. További információkhoz is hivatkozhat egy URI-ra.

## <a name="ui-sample"></a>Felhasználói felület mintája

![Microsoft.Common.InfoBox](./media/managed-application-elements/microsoft.common.infobox.png)


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

## <a name="sample-output"></a>Példa kimenet

```json
"Nullam eros mi, mollis in sollicitudin non, tincidunt sed enim. Sed et felis metus, rhoncus ornare nibh. Ut at magna leo."
```

## <a name="remarks"></a>Megjegyzések

* `icon`esetén használja a **none**, az **info**, a **Warning**vagy a **Error**értéket.
* A `uri` tulajdonság nem kötelező.

## <a name="next-steps"></a>További lépések

* A felhasználói felületi definíciók létrehozásával kapcsolatban lásd: Bevezetés [a CreateUiDefinition](create-uidefinition-overview.md)használatába.
* A felhasználói felületi elemek általános tulajdonságainak leírását lásd: [CreateUiDefinition-elemek](create-uidefinition-elements.md).
