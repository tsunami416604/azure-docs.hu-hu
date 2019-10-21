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
ms.openlocfilehash: 47c2429363945cf1529cee6e49947aaea95b7022
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/18/2019
ms.locfileid: "72597389"
---
## <a name="timeout"></a>Function alkalmazás időtúllépési időtartama 

A Function alkalmazás időtúllépési időtartamát a [Host. JSON](../articles/azure-functions/functions-host-json.md#functiontimeout) projektfájl functionTimeout tulajdonsága határozza meg. A következő táblázat az alapértelmezett és a maximális értéket mutatja percben mindkét csomag esetében és mindkét futásidejű verzióban:

| Felkészülés | Futtatókörnyezet verziója | Alapértelmezett | Maximum |
|------|---------|---------|---------|
| Használatalapú | 1. x | 5 | 10 |
| Használatalapú | 2. x | 5 | 10 |
| Használatalapú | 3. x (előzetes verzió) | 5 | 10 |
| App Service | 1. x | Korlátlan | Korlátlan |
| App Service | 2. x | 30 | Korlátlan |
| App Service | 3. x (előzetes verzió) | 30 | Korlátlan |

> [!NOTE] 
> A Function app timeout beállítástól függetlenül az 230 másodperc az a maximális időtartam, ameddig egy HTTP által aktivált függvény válaszol a kérelemre. Ennek oka az [Azure Load Balancer alapértelmezett üresjárati időkorlátja](../articles/app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds). A hosszú feldolgozási idő esetében érdemes lehet a [Durable functions aszinkron mintát](../articles/azure-functions/durable/durable-functions-overview.md#async-http) használni, vagy [késleltetni a tényleges munkát, és azonnali választ ad vissza](../articles/azure-functions/functions-best-practices.md#avoid-long-running-functions).
