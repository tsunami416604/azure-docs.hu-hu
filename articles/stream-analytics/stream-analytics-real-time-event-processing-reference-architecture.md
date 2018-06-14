---
title: Azure Stream Analytics esemény feldolgozása használatával valós idejű esemény feldolgozása
description: Ez a cikk ismerteti a referencia-architektúrában, valós idejű esemény feldolgozása és elemzés használata az Azure Stream Analytics eléréséhez.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/24/2017
ms.openlocfilehash: 8a5d426d67916e010c7fff048eebdc77b93c5c38
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
ms.locfileid: "30902582"
---
# <a name="reference-architecture-real-time-event-processing-with-microsoft-azure-stream-analytics"></a>Architektúra hivatkozni: a Microsoft Azure Stream Analytics valós idejű Eseményfeldolgozási
A referencia-architektúrában, valós idejű esemény feldolgozása az Azure Stream Analytics célja adjon meg egy általános tervezetének üzembe helyezéséhez egy valós idejű platform (PaaS) szolgáltatás adatfolyam-feldolgozási megoldásként a Microsoft Azure-ban.

## <a name="summary"></a>Összegzés
Hagyományosan elemzési megoldásokat képességeit ETL (kinyerés, átalakítás, betöltés) és az adatraktározás terén, például előtt elemzési adatokat tároló volna alapozni. Változó követelményeknek megfelelően további gyorsan érkező adatokat, beleértve a létező modell leküldendő a határértéket. Adatok áthelyezése előtt tárolási adatfolyamok elemzését teszi egyetlen megoldás, és bár ez nem egy új funkció, a módszer nem széles körben elfogadott közötti összes iparági referenciaegyenesen. 

Microsoft Azure biztosít egy kiterjedt katalógust, amely támogathatja a másik megoldás forgatókönyvek és követelmények tömbje analytics technológiák. Mely Azure-szolgáltatások telepítése végpont megoldás kiválasztása a hardverekről ajánlatok adott feladat lehet. A dokumentum célja, hogy szolgáltatásait és való együttműködés a különböző Azure-szolgáltatáshoz, amely egy esemény-adatfolyam-megoldás támogatja. Ismerteti azokat a forgatókönyveket, amelyben az ügyfelek is kihasználhatja az ilyen típusú megközelítés le.

## <a name="contents"></a>Tartalom
* Vezetői összefoglaló
* Valós idejű elemzés bemutatása
* A valós idejű adatok Azure-ban Értékajánlatához
* Valós idejű elemzések gyakori helyzetek
* Architektúrája és összetevői
  * Adatforrások
  * Adatintegráció réteg
  * Valós idejű elemzési réteg
  * Adatok tárolási rétegből
  * Bemutató / fogyasztás réteg
* Összegzés

**Szerző:** Insights adatközpont Charles Feddersen, megoldásokért felelős mérnök kiváló, Microsoft Corporation

**Közzétett:** 2015. januári

**Változat:** 1.0

**Letöltés:** [valós idejű, a Microsoft Azure Stream Analytics Eseményfeldolgozási](http://download.microsoft.com/download/6/2/3/623924DE-B083-4561-9624-C1AB62B5F82B/real-time-event-processing-with-microsoft-azure-stream-analytics.pdf)

## <a name="get-help"></a>Segítségkérés
Ha további segítségre van szüksége, próbálkozzon a [Azure Stream Analytics-fórumot](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>További lépések
* [Az Azure Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)

