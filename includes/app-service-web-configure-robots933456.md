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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255828"
---
## <a name="robots933456-in-logs"></a>robotok933456 a naplókban

A tárolónaplókban a következő üzenet jelenhet meg:

```
2019-04-08T14:07:56.641002476Z "-" - - [08/Apr/2019:14:07:56 +0000] "GET /robots933456.txt HTTP/1.1" 404 415 "-" "-"
```

Az üzenet biztonságosan figyelmen kívül hagyható. `/robots933456.txt`egy hamis URL-cím, amelyet az App Service annak ellenőrzésére használ, hogy a tároló képes-e a kérelmek kiszolgálására. A 404-es válasz egyszerűen azt jelzi, hogy az elérési út nem létezik, de lehetővé teszi az App Service tudja, hogy a tároló kifogástalan állapotú, és készen áll a kérésekre.

