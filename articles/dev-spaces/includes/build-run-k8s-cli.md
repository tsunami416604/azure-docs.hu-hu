---
title: fájl belefoglalása
description: fájl belefoglalása
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: 1cf301ab3b4430c7bb79f6fdd72243f9f2ad1d9b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
## <a name="build-and-run-code-in-kubernetes"></a>Hozza létre, és futtassa a kódot Kubernetes
Most futtassa a kód! A Terminálszolgáltatások ablakban futtassa a parancsot a **kód gyökérmappa**, webfrontend:

```cmd
azds up
```

Nyomon követheti a parancs kimenetét a, a végrehajtás során számos dolgot láthatja:
- Forráskód szinkronizálva van a fejlesztési környezetet az Azure-ban.
- A tároló-lemezkép Azure, a kód mappában a Docker-eszközök által meghatározott részét.
- Kubernetes objektumok jönnek létre, amelyek ténylegesen használják a tároló kép megadottak szerint a Helm diagram a kód mappában.
- A tároló végpont(ok) információkat jelenít meg. Ebben az esetben azt vár egy HTTPS URL-címe.
- Ha a fenti szakaszokban sikeresen befejeződik, kezdjen el megjelenítéséhez `stdout` (és `stderr`) kimeneti, a tároló elindul.

> [!Note]
> Ezeket a lépéseket hosszabb ideig tart a először a `up` parancs fut, de utólagosan gyorsabb kell lennie.

## <a name="test-the-web-app"></a>A webalkalmazás tesztelése
A konzol kimeneti információ hozta létre nyilvános URL-cím vizsgálata a `up` parancsot. A képernyő lesz: 

`Running at public URL: https://<servicename>-<environmentname>.<guid>.<region>.aksapp.io` 

Nyissa meg az URL-cím egy böngészőablakban, és a webalkalmazás betöltése kell megjelennie. A tároló hajt végre, mivel `stdout` és `stderr` kimeneti továbbítja adatfolyamként való a terminálablakot.
