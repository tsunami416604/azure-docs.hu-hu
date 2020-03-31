---
title: InfoBox felhasználói felület e-elem
description: A cikk az Azure Portal Microsoft.Common.InfoBox felhasználói felületének elemét ismerteti. A felügyelt alkalmazás telepítésekor szöveget vagy figyelmeztetéseket adhat hozzá.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: tomfitz
ms.openlocfilehash: 6d1e4a84904ef7022d9ce85803bf10285bf0b8ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75652475"
---
# <a name="microsoftcommoninfobox-ui-element"></a>Microsoft.Common.InfoBox felhasználói felület

Információs mezőt tartalmazó vezérlő. A mező fontos szöveget vagy figyelmeztetéseket tartalmaz, amelyek segítenek a felhasználóknak megérteni az általuk biztosított értékeket. További információkért az URI-hoz is csatolhat hivatkozást.

## <a name="ui-sample"></a>Felhasználói felület minta

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

* A `icon`területen használja a **Nincs**, **Információ**, **Figyelmeztetés**vagy **Hiba parancsot.**
* A `uri` szálláshely nem kötelező.

## <a name="next-steps"></a>További lépések

* A felhasználói felületdefiníciók létrehozásának bemutatása a [CreateUiDefinition](create-uidefinition-overview.md)első lépései című témakörben látható.
* A felhasználói felület elemeinek gyakori tulajdonságainak leírását a [CreateUiDefinition elements](create-uidefinition-elements.md)című témakörben található.
