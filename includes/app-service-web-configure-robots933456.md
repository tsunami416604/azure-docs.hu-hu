---
title: fájlbefoglalás
description: fájlbefoglalás
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/02/2020
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 6fc1f4152b2e16e1597c018e5af6e0245b075c3b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "78255828"
---
## <a name="robots933456-in-logs"></a>robots933456 a naplókban

Előfordulhat, hogy a következő üzenet jelenik meg a tároló naplóiban:

```
2019-04-08T14:07:56.641002476Z "-" - - [08/Apr/2019:14:07:56 +0000] "GET /robots933456.txt HTTP/1.1" 404 415 "-" "-"
```

Az üzenet biztonságosan figyelmen kívül hagyható. `/robots933456.txt`a egy olyan próbabábu URL-elérési útja, amelyet a App Service a használ annak vizsgálatára, hogy a tároló képes-e kérések kiszolgálására. Egy 404-es válasz egyszerűen azt jelzi, hogy az elérési út nem létezik, de lehetővé teszi, hogy App Service tudja, hogy a tároló kifogástalan állapotú, és készen áll a kérelmekre való válaszadás

