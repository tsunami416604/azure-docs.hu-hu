---
title: fájlbefoglalás
description: fájlbefoglalás
services: functions
author: nzthiago
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2018
ms.author: nzthiago
ms.custom: include file
ms.openlocfilehash: eca2d3359614875e5bff0c9bb67f006f0a8cdba1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "77198326"
---
## <a name="function-app-timeout-duration"></a><a name="timeout"></a>Function alkalmazás időtúllépési időtartama 

A függvény alkalmazásának időtúllépési időtartamát a `functionTimeout` Project fájl [host.js](../articles/azure-functions/functions-host-json.md#functiontimeout) tulajdonsága határozza meg. A következő táblázat a csomagok és a különböző futásidejű verziók alapértelmezett és maximális értékeit mutatja percben:

| Felkészülés | Futtatókörnyezet verziója | Alapértelmezett | Maximum |
|------|---------|---------|---------|
| Felhasználás | 1. x | 5 | 10 |
| Felhasználás | 2. x | 5 | 10 |
| Felhasználás | 3. x | 5 | 10 |
| Prémium | 1. x | 30 | Korlátlan |
| Prémium | 2. x | 30 | Korlátlan |
| Prémium | 3. x | 30 | Korlátlan |
| App Service | 1. x | Korlátlan | Korlátlan |
| App Service | 2. x | 30 | Korlátlan |
| App Service | 3. x | 30 | Korlátlan |

> [!NOTE] 
> A Function app timeout beállítástól függetlenül az 230 másodperc az a maximális időtartam, ameddig egy HTTP által aktivált függvény válaszol a kérelemre. Ennek oka az [Azure Load Balancer alapértelmezett üresjárati időkorlátja](../articles/app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds). A hosszú feldolgozási idő esetében érdemes lehet a [Durable functions aszinkron mintát](../articles/azure-functions/durable/durable-functions-overview.md#async-http) használni, vagy [késleltetni a tényleges munkát, és azonnali választ ad vissza](../articles/azure-functions/functions-best-practices.md#avoid-long-running-functions).
