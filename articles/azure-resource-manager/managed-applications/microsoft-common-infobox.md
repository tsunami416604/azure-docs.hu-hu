---
title: Bezárása FELHASZNÁLÓIFELÜLET-elem
description: A Azure Portal Microsoft. Common. bezárása felhasználói felületi elemének ismertetése. A segítségével szöveget vagy figyelmeztetéseket adhat hozzá a felügyelt alkalmazás telepítésekor.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: tomfitz
ms.openlocfilehash: 6d1e4a84904ef7022d9ce85803bf10285bf0b8ac
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "75652475"
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

## <a name="sample-output"></a>Példa kimenet

```json
"Nullam eros mi, mollis in sollicitudin non, tincidunt sed enim. Sed et felis metus, rhoncus ornare nibh. Ut at magna leo."
```

## <a name="remarks"></a>Megjegyzések

* A esetében a `icon` **nincs**, az **info**, a **Warning**vagy a **Error**értéket használja.
* A `uri` tulajdonság megadása nem kötelező.

## <a name="next-steps"></a>További lépések

* A felhasználói felületi definíciók létrehozásával kapcsolatban lásd: Bevezetés [a CreateUiDefinition](create-uidefinition-overview.md)használatába.
* A felhasználói felületi elemek általános tulajdonságainak leírását lásd: [CreateUiDefinition-elemek](create-uidefinition-elements.md).
