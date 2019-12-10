---
title: fájl belefoglalása
description: fájl belefoglalása
services: functions
author: nzthiago
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2018
ms.author: nzthiago
ms.custom: include file
ms.openlocfilehash: fded43bb655cefda508b82eca94522730ab6da00
ms.sourcegitcommit: b5ff5abd7a82eaf3a1df883c4247e11cdfe38c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2019
ms.locfileid: "74941707"
---
## <a name="timeout"></a>Function alkalmazás időtúllépési időtartama 

A Function alkalmazás időtúllépési időtartamát a [Host. JSON](../articles/azure-functions/functions-host-json.md#functiontimeout) projektfájl functionTimeout tulajdonsága határozza meg. A következő táblázat az alapértelmezett és a maximális értéket mutatja percben mindkét csomag esetében és mindkét futásidejű verzióban:

| Felkészülés | Futtatókörnyezet verziója | Alapértelmezett | Maximum |
|------|---------|---------|---------|
| Használatalapú | 1. x | 5 | 10 |
| Használatalapú | 2. x | 5 | 10 |
| Használatalapú | 3. x | 5 | 10 |
| App Service | 1. x | Korlátlan | Korlátlan |
| App Service | 2. x | 30 | Korlátlan |
| App Service | 3. x | 30 | Korlátlan |

> [!NOTE] 
> A Function app timeout beállítástól függetlenül az 230 másodperc az a maximális időtartam, ameddig egy HTTP által aktivált függvény válaszol a kérelemre. Ennek oka az [Azure Load Balancer alapértelmezett üresjárati időkorlátja](../articles/app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds). A hosszú feldolgozási idő esetében érdemes lehet a [Durable functions aszinkron mintát](../articles/azure-functions/durable/durable-functions-overview.md#async-http) használni, vagy [késleltetni a tényleges munkát, és azonnali választ ad vissza](../articles/azure-functions/functions-best-practices.md#avoid-long-running-functions).
