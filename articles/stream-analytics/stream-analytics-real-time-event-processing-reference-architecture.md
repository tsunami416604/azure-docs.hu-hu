---
title: A Stream Analytics esemény feldolgozása valós idejű Eseményfeldolgozási |} Microsoft Docs
description: Ismerje meg, hogyan képes együttműködni az Azure szolgáltatások valós idejű esemény feldolgozása és az elemzések engedélyezéséhez.
keywords: valós idejű feldolgozással, az események feldolgozásával, a referencia-architektúrában
services: stream-analytics,event-hubs,storage,sql-database
documentationcenter: ''
author: jseb225
manager: ryanw
ms.assetid: 11af48bc-313c-4527-8c80-91088dc9f3c6
ms.service: stream-analytics
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2017
ms.author: jeanb
ms.openlocfilehash: cf11a80a93a923f038b7d6a0f02a35938794c242
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2018
---
# <a name="reference-architecture-real-time-event-processing-with-microsoft-azure-stream-analytics"></a>Architektúra hivatkozni: a Microsoft Azure Stream Analytics valós idejű Eseményfeldolgozási
A referencia-architektúrában, valós idejű esemény feldolgozása az Azure Stream Analytics célja adjon meg egy általános tervezetének üzembe helyezéséhez egy valós idejű platform (PaaS) szolgáltatás adatfolyam-feldolgozási megoldásként a Microsoft Azure-ban.

## <a name="summary"></a>Összefoglalás
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

## <a name="get-help"></a>Segítség kérése
További támogatásért keresse fel az [Azure Stream Analytics-fórumot](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>További lépések
* [Az Azure Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)

