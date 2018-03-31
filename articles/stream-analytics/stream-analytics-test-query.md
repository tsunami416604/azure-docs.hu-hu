---
title: Az Azure Stream Analytics lekérdezési tesztelése |} Microsoft Docs
description: Hogyan tesztelheti a lekérdezéseket a Stream Analytics-feladatok.
keywords: lekérdezés tesztelése, a lekérdezés hibaelhárítása
documentation center: ''
services: stream-analytics
author: jseb225
manager: ryanw
ms.assetid: ''
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: jeanb
ms.openlocfilehash: 50bfce426bf48ba986887f8a2e2873fe04ea2507
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2018
---
# <a name="test-azure-stream-analytics-queries-in-the-azure-portal"></a>Azure Stream Analytics lekérdezések tesztelése az Azure-portálon

Az Azure Stream Analytics lekérdezések tesztelheti az Azure portálon anélkül, hogy indítása vagy leállítása egy feladatot.

## <a name="test-the-input"></a>A bemeneti tesztelése

1. A minta bemeneti adatok teszteléséhez kattintson a jobb gombbal bármelyik bemenet, és válassza **tölthet fel fájlból adatot**. Jelenleg csak JSON formátumú az adatait feltöltheti. Ha az adatok egy másik formátumban, például a fürt megosztott kötetei szolgáltatás, akkor kell azt átalakíthatja JSON feltöltés előtt. Használhatja, mint bármely opensource átalakítás eszközt [a JSON-konverter CSV](http://www.convertcsv.com/csv-to-json.htm) az adatok JSON formátumúvá konvertálni.

    ![Stream analytics lekérdezési szerkesztő lekérdezés tesztelése](media/stream-analytics-test-query/stream-analytics-test-query-editor-upload.png)

2. A feltöltés befejeződése után kattintson **tesztelése** tesztelése a megadott mintaadatokat irányuló lekérdezés.

    ![Stream analytics lekérdezési szerkesztő teszt mintaadatokat](media/stream-analytics-test-query/stream-analytics-test-query-editor-test.png)

A lekérdezés kimenetét jelenik meg a böngészőben a letöltés eredményeit hivatkozással kell menteni szeretné a teszt eredménye későbbi használatra. Mostantól egyszerűen és ismételt módosítsa a lekérdezést és tesztelik azt hogy ismételten hogyan a kimeneti változik.

![Stream Analytics query editor minta kimenet](media/stream-analytics-test-query/stream-analytics-test-query-editor-samples-output.png)

A lekérdezésben használt több kimenettel külön-külön tekintse meg mindkét kimenetek eredményét, és könnyen közötti váltáshoz.

Miután elkészült a lekérdezés mentéséhez, indítsa el a feladatot, és lehetővé teszik a böngészőben megjelenített eredményekre feldolgozni az eseményeket hiba nélkül.

## <a name="get-help"></a>Segítség kérése

Ha további segítségre van szüksége, próbálkozzon a [Azure Stream Analytics-fórumot](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>További lépések

* [Az Azure Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)
