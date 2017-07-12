---
title: "Eseményforrás hozzáadása Azure Time Series Insight-környezethez | Microsoft Docs"
description: "Ebben az oktatóanyagban csatlakoztatni fog egy eseményforrást az Azure Time Series Insights-környezetéhez"
keywords: 
services: time-series-insights
documentationcenter: 
author: op-ravi
manager: santoshb
editor: cgronlun
ms.assetid: 
ms.service: time-series-insights
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/21/2017
ms.author: omravi
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: ffa2eaf3680e68ac14aabf49b6308caeb173fd43
ms.contentlocale: hu-hu
ms.lasthandoff: 07/01/2017

---

<a id="create-an-event-source-for-your-time-series-insights-environment-using-the-ibiza-portal" class="xliff"></a>

# Eseményforrás létrehozása Azure Time Series Insights-környezethez az Ibiza Portal használatával

Egy Time Series Insights-eseményforrás egy eseményközvetítőből, például az Azure Event Hubsból származik. A Time Series Insights közvetlenül csatlakozik az eseményforrásokhoz, és anélkül dolgozza fel az adatfolyamot, hogy a felhasználóknak akár egy sor kódot is kéne írniuk. A Time Series Insights jelenleg az Azure Event Hubs és Azure IoT Hubs forrásokat támogatja. A jövőben további eseményforrásokkal is bővülni fog.

<a id="steps-to-add-an-event-source-to-your-environment" class="xliff"></a>

## Eseményforrás környezethez való hozzáadásának lépései

1.  Jelentkezzen be az [Ibiza Portalra](https://portal.azure.com).
2.  Az Ibiza Portal bal oldali menüjében kattintson a „Minden erőforrás” lehetőségre.
3.  Válassza ki az Azure Time Series Insights-környezetet.

  ![A Time Series Insights-eseményforrás létrehozása](media/add-event-source/getstarted-create-event-source-1.png)

4.  Válassza az „Eseményforrások” lehetőséget, és kattintson a „+ Hozzáadás” gombra.

  ![A Time Series Insights-eseményforrás létrehozása – részletek](media/add-event-source/getstarted-create-event-source-2.png)

5.  Adja meg az eseményforrás nevét. Ez a név az eseményforrástól származó összes eseményhez hozzá lesz rendelve, és a lekérdezéskor elérhető.
6.  Válasszon eseményközpontot az adott előfizetés eseményközpont-erőforrásainak listájából. Egyébként válassza az „Eseményközpont beállításainak kézi megadása” import-beállítást, ha más előfizetéshez tartozó eseményközpontot kíván megadni. Az eseményeket JSON formátumban kell közzétenni.
7.  Olyan szabályzatot válasszon, amelynek olvasási engedélye van az eseményközpontban.
8.  Határozza meg az eseményközpont fogyasztói csoportját.

  > [!IMPORTANT]
  > Ügyeljen arra, hogy ezt a fogyasztói csoportot ne használja másik szolgáltatás (például Stream Analytics-feladat vagy másik Time Series Insights-környezet). Ha a fogyasztói csoportot más szolgáltatások is használják, az zavarhatja az olvasási műveleteket ebben a környezetben és a többi szolgáltatásban is. Ha a „$Default” elemet használja a fogyasztói csoportként, előfordulhat, hogy más olvasók újra fel fogják használni a csoportot.

9.  Kattintson a „Létrehozás” elemre.

Az eseményforrás létrehozása után a Time Series Insights automatikusan megkezdi az adatok streamelését a környezetbe.

<a id="next-steps" class="xliff"></a>

## Következő lépések

* [Események küldése](time-series-insights-send-events.md) az eseményforrásnak
* A környezet megtekintése a [Time Series Insights portálon](https://insights.timeseries.azure.com)

