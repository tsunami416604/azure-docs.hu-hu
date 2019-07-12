---
title: Az Azure Stream Analytics Eseményfeldolgozási használatával valós idejű eseményfeldolgozás
description: Ez a cikk ismerteti a referencia-architektúra, valós idejű eseményfeldolgozás és elemzési Azure Stream Analytics használatával érhető el.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/24/2017
ms.openlocfilehash: ab1ca89427f332151120420c3c087902584706f4
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621786"
---
# <a name="reference-architecture-real-time-event-processing-with-microsoft-azure-stream-analytics"></a>Referenciaarchitektúra: Valós idejű eseményfeldolgozás a Microsoft Azure Stream Analytics
A referenciaarchitektúra az Azure Stream Analytics valós idejű eseményfeldolgozás a funkcionalitást biztosít egy általános tervezet üzembe helyezéséhez egy valós idejű platformjával a Microsoft Azure platformszolgáltatás (PaaS) streamfeldolgozó megoldás.

## <a name="summary"></a>Összegzés
Hagyományosan elemzési megoldásokat rendelkezik alapul szolgáló képességeket beépítettük, és az adattárházak, ETL (extract, transform, load) adatok tárolására elemzés előtt. Követelményeknek megfelelően, több gyorsan beérkező adatot, beleértve a már meglévő modell küld a korlátot. Adatok áthelyezése a storage előtt Streamek elemzése lehetővé teszi egy megoldást, és nem érhető el egy olyan új képesség, míg a módszer nem széles körben fogadott összes iparági referenciaegyenesen között. 

A Microsoft Azure biztosít egy kiterjedt katalógus, amely támogathatja a másik megoldás-forgatókönyveket és a követelmények tömbjét elemzési technológiák. Melyik Azure-szolgáltatások üzembe helyezése egy teljes körű megoldás kiválasztása az ajánlatok bővítheti a megadott kihívást jelenthet. Ez a tanulmány szolgáltatásait és üzemeltetést, a különböző Azure-szolgáltatások, amelyek támogatják az esemény-adatfolyam-megoldás célja. Ismerteti azokat a forgatókönyveket, amelyben ügyfeleink az ilyen megközelítés is kihasználhatják a.

## <a name="contents"></a>Tartalom
* Vezetői összefoglaló
* Bevezetés a valós idejű elemzés
* Az Azure-ban a valós idejű adatok Értékajánlat
* Valós idejű elemzések gyakori forgatókönyvei
* Architektúra és összetevők
  * Adatforrások
  * Adatintegrációs réteg
  * Valós idejű elemzési réteg
  * Adatok tárolási réteg
  * Bemutató / fogyasztás. réteg
* Összegzés

**Szerző:** Insights adatközpont Charles Feddersen, Felhőmegoldás-fejlesztő mérnök, a minőség, a Microsoft Corporation

**Közzététel:** 2015. január

**Verzió:** 1.0

**Letöltés:** [Valós idejű Eseményfeldolgozás a Microsoft Azure Stream Analytics](https://download.microsoft.com/download/6/2/3/623924DE-B083-4561-9624-C1AB62B5F82B/real-time-event-processing-with-microsoft-azure-stream-analytics.pdf)

## <a name="get-help"></a>Segítségkérés
További segítségre van szüksége, próbálja meg a [Azure Stream Analytics-fórumot](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>További lépések
* [Az Azure Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Azure Stream Analytics Query Language Reference](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)

