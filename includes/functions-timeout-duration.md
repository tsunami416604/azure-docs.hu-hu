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
ms.openlocfilehash: 0bd66699142e9e03f4a344d499624fe207cb9a45
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2019
ms.locfileid: "70963576"
---
## <a name="timeout"></a>Function alkalmazás időtúllépési időtartama 

A Function alkalmazás időtúllépési időtartamát a [Host. JSON](../articles/azure-functions/functions-host-json.md#functiontimeout) projektfájl functionTimeout tulajdonsága határozza meg. A következő táblázat az alapértelmezett és a maximális értéket mutatja percben mindkét csomag esetében és mindkét futásidejű verzióban:

| Felkészülés | Futtatókörnyezet verziója | Alapértelmezett | Maximum |
|------|---------|---------|---------|
| Használat | 1. x | 5 | 10 |
| Használat | 2. x | 5 | 10 |
| App Service | 1. x | Korlátlan | Korlátlan |
| App Service | 2. x | 30 | Korlátlan |

> [!NOTE] 
> A Function app timeout beállítástól függetlenül az 230 másodperc az a maximális időtartam, ameddig egy HTTP által aktivált függvény válaszol a kérelemre. Ennek oka az [Azure Load Balancer alapértelmezett üresjárati időkorlátja](../articles/app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds). A hosszú feldolgozási idő esetében érdemes lehet a [Durable functions aszinkron mintát](../articles/azure-functions/durable/durable-functions-overview.md#async-http) használni, vagy [késleltetni a tényleges munkát, és azonnali választ ad vissza](../articles/azure-functions/functions-best-practices.md#avoid-long-running-functions).
