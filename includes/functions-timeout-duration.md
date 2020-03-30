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
ms.openlocfilehash: eca2d3359614875e5bff0c9bb67f006f0a8cdba1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77198326"
---
## <a name="function-app-timeout-duration"></a><a name="timeout"></a>A függvényalkalmazás idő-eltalegi időtartama 

A függvényalkalmazás időkimenő időtartamát a `functionTimeout` [host.json](../articles/azure-functions/functions-host-json.md#functiontimeout) projektfájlban lévő tulajdonság határozza meg. Az alábbi táblázat a tervek és a különböző futásidejű verziók alapértelmezett és maximális értékeit jeleníti meg percben:

| Felkészülés | Futásidejű verzió | Alapértelmezett | Maximum |
|------|---------|---------|---------|
| Használat | 1,x | 5 | 10 |
| Használat | 2,x | 5 | 10 |
| Használat | 3.x | 5 | 10 |
| Prémium | 1,x | 30 | Korlátlan |
| Prémium | 2,x | 30 | Korlátlan |
| Prémium | 3.x | 30 | Korlátlan |
| App Service | 1,x | Korlátlan | Korlátlan |
| App Service | 2,x | 30 | Korlátlan |
| App Service | 3.x | 30 | Korlátlan |

> [!NOTE] 
> Függetlenül attól, hogy a függvény alkalmazás időkérési beállítás, 230 másodperc a maximális idő, amely egy HTTP-aktivált függvény tehet a kérés megválaszolására. Ennek az az oka, hogy az [Azure Load Balancer alapértelmezett tétlen időtúlszáma](../articles/app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds)van. Hosszabb feldolgozási idő esetén fontolja meg a [Durable Functions async minta](../articles/azure-functions/durable/durable-functions-overview.md#async-http) használatát, vagy [halassza el a tényleges munkát, és adjon vissza azonnali választ.](../articles/azure-functions/functions-best-practices.md#avoid-long-running-functions)
