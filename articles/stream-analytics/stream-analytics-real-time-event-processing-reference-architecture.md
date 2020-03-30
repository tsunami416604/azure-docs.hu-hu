---
title: Valós idejű eseményfeldolgozás az Azure Stream Analytics használatával
description: Ez a cikk ismerteti a referencia architektúra valós idejű eseményfeldolgozás és elemzés az Azure Stream Analytics használatával valósítja meg.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/24/2017
ms.openlocfilehash: d219b3fcb27b23527c0a651bc8e842a9e036bfc2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75431484"
---
# <a name="reference-architecture-real-time-event-processing-with-microsoft-azure-stream-analytics"></a>Referenciaarchitektúra: Valós idejű eseményfeldolgozás a Microsoft Azure Stream Analytics segítségével
Az Azure Stream Analytics valós idejű eseményfeldolgozási referenciaarchitektúrája egy általános tervvel szolgál a valós idejű platform szolgáltatásként (PaaS) adatfolyam-feldolgozási megoldás microsoft Azure-mal való üzembe helyezéséhez.

## <a name="summary"></a>Összefoglalás
Hagyományosan az elemzési megoldások olyan képességeken alapulnak, mint az ETL (kibontás, átalakítás, betöltés) és az adattárház, ahol az adatoktárolás az elemzés előtt történik. A változó követelmények, beleértve a gyorsabban érkező adatokat is, a meglévő modellt a végsőkig kényszerítik. Az adatok elemzése a tároló előtt a mozgó adatfolyamokon belül az egyik megoldás, és bár ez nem új képesség, a megközelítést nem fogadták el széles körben az összes iparági vertikális ágazatban. 

A Microsoft Azure az elemzési technológiák széles körű katalógusát kínálja, amelyek különböző megoldási forgatókönyvek és követelmények tömbjét képesek támogatni. Az ajánlatok széles körű használatával kapcsolatos, teljes körű üzembe helyezendő Azure-szolgáltatások kiválasztása kihívást jelent. Ez a dokumentum az eseménystreamelési megoldásokat támogató különböző Azure-szolgáltatások képességeit és együttműködését ismerteti. Azt is ismerteti, néhány forgatókönyv, amelyben az ügyfelek számára előnyös az ilyen típusú megközelítés.

## <a name="contents"></a>Tartalom
* Vezetői összefoglaló
* Bevezetés a valós idejű elemzésbe
* Valós idejű adatok értékajánlata az Azure-ban
* Gyakori forgatókönyvek a valós idejű elemzéshez
* Építészet és összetevők
  * Adatforrások
  * Adatintegrációs réteg
  * Valós idejű elemzési réteg
  * Adattárolási réteg
  * Bemutató / fogyasztási réteg
* Összegzés

**Szerző:** Charles Feddersen, megoldástervező, Data Insights Kiválósági Központ, Microsoft Corporation

**Megjelent:** 2015. január

**Verzió:** 1.0

**Letöltés:** [Valós idejű eseményfeldolgozás a Microsoft Azure Stream Analytics segítségével](https://download.microsoft.com/download/6/2/3/623924DE-B083-4561-9624-C1AB62B5F82B/real-time-event-processing-with-microsoft-azure-stream-analytics.pdf)

## <a name="get-help"></a>Segítségkérés
További segítségért próbálja ki az [Azure Stream Analytics fórumot](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>További lépések
* [Bevezetés az Azure Stream Analytics szolgáltatásba](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs (Azure Stream Analytics-feladatok méretezése)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Referencia az Azure Stream Analytics lekérdezési nyelvhez)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)

