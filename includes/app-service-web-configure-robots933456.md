---
title: fájl belefoglalása
description: fájl belefoglalása
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/02/2020
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 6fc1f4152b2e16e1597c018e5af6e0245b075c3b
ms.sourcegitcommit: d4a4f22f41ec4b3003a22826f0530df29cf01073
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2020
ms.locfileid: "78255828"
---
## <a name="robots933456-in-logs"></a>robots933456 a naplókban

Előfordulhat, hogy a következő üzenet jelenik meg a tároló naplóiban:

```
2019-04-08T14:07:56.641002476Z "-" - - [08/Apr/2019:14:07:56 +0000] "GET /robots933456.txt HTTP/1.1" 404 415 "-" "-"
```

Nyugodtan figyelmen kívül hagyhatja ezt az üzenetet. a `/robots933456.txt` egy olyan dummy URL-cím elérési útja, amelyet a App Service használ annak vizsgálatára, hogy a tároló képes-e kérések kiszolgálására. Egy 404-es válasz egyszerűen azt jelzi, hogy az elérési út nem létezik, de lehetővé teszi, hogy App Service tudja, hogy a tároló kifogástalan állapotú, és készen áll a kérelmekre való válaszadás

