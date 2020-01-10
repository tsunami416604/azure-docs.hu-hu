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
ms.openlocfilehash: 3501ff3f92ae045019df2766bbcf7fc2c3fec5b0
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75768918"
---
## <a name="timeout"></a>Function alkalmazás időtúllépési időtartama 

A Function alkalmazás időtúllépési időtartamát a [Host. JSON](../articles/azure-functions/functions-host-json.md#functiontimeout) projektfájl `functionTimeout` tulajdonsága határozza meg. A következő táblázat a csomagok és a különböző futásidejű verziók alapértelmezett és maximális értékeit mutatja percben:

| Felkészülés | Futtatókörnyezet verziója | Alapértelmezett | Maximum |
|------|---------|---------|---------|
| Használat | 1. x | 5 | 10 |
| Használat | 2. x | 5 | 10 |
| Használat | 3.x | 5 | 10 |
| App Service | 1. x | Korlátlan | Korlátlan |
| App Service | 2. x | 30 | Korlátlan |
| App Service | 3.x | 30 | Korlátlan |

> [!NOTE] 
> A Function app timeout beállítástól függetlenül az 230 másodperc az a maximális időtartam, ameddig egy HTTP által aktivált függvény válaszol a kérelemre. Ennek oka az [Azure Load Balancer alapértelmezett üresjárati időkorlátja](../articles/app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds). A hosszú feldolgozási idő esetében érdemes lehet a [Durable functions aszinkron mintát](../articles/azure-functions/durable/durable-functions-overview.md#async-http) használni, vagy [késleltetni a tényleges munkát, és azonnali választ ad vissza](../articles/azure-functions/functions-best-practices.md#avoid-long-running-functions).
