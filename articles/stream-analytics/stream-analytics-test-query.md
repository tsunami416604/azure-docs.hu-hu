---
title: Az Azure Stream Analytics-feladat mintaadatokkal tesztelése |} Microsoft Docs
description: Hogyan tesztelheti a lekérdezéseket a Stream Analytics-feladatok.
keywords: a tesztfeladat, adjon meg mintavételi, feltöltési minta dátum
documentationcenter: ''
services: stream-analytics
author: SnehaGunda
manager: kfile
ms.assetid: ''
ms.service: stream-analytics
ms.workload: data-services
ms.date: 03/18/2018
ms.author: sngun
ms.openlocfilehash: c026a91fff5b8ef5774993b335f8d61877aa5d39
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="test-your-stream-analytics-query-with-sample-data"></a>A Stream Analytics-lekérdezés mintaadatokkal tesztelése

Azure Stream Analytics segítségével töltse fel adatokat, valamint a lekérdezés a portálon elindítása vagy a feladat leállítása nélkül.

## <a name="upload-sample-data-and-test-the-query"></a>Töltse fel a mintaadatok és a lekérdezés tesztelése

1. Keresse meg a meglévő Stream Analytics-feladat egyik > kattintson a **lekérdezés** a Lekérdezésszerkesztő ablakának megnyitása. 

2. A minta bemeneti adatokat tartalmazó lekérdezés teszteléséhez kattintson a jobb gombbal bármelyik bemenet, és válassza **tölthet fel fájlból adatot**. Jelenleg csak JSON formátumú az adatait feltöltheti. Ha az adatok egy másik formátumban, például a fürt megosztott kötetei szolgáltatás, akkor kell azt átalakíthatja JSON feltöltés előtt. Használhatja, mint bármely opensource átalakítás eszközt [a JSON-konverter CSV](http://www.convertcsv.com/csv-to-json.htm) az adatok JSON formátumúvá konvertálni.

    ![Stream analytics lekérdezési szerkesztő lekérdezés tesztelése](media/stream-analytics-test-query/stream-analytics-test-query-editor-upload.png)

3. A feltöltés befejeződése után kattintson **tesztelése** tesztelése a megadott mintaadatokat irányuló lekérdezés.

    ![Stream analytics lekérdezési szerkesztő teszt mintaadatokat](media/stream-analytics-test-query/stream-analytics-test-query-editor-test.png)

4. Ha azt szeretné, a vizsgálat kimeneti későbbi használatra menti, a lekérdezés kimenetét megjelenik egy hivatkozás a letöltés eredményeit a webböngésző. Mostantól egyszerűen és ismételt módosítsa a lekérdezést és tesztelik azt hogy ismételten hogyan a kimeneti változik.

   ![Stream Analytics query editor minta kimenet](media/stream-analytics-test-query/stream-analytics-test-query-editor-samples-output.png)

Ha több kimenet a lekérdezésben, külön-külön minden kimeneti eredmények megjelenítéséhez, és könnyen váltása. Miután meggyőződött a a böngészőben megjelenített mentheti a lekérdezési eredmények indítsa el a feladatot, és tájékoztassa feldolgozni az eseményeket hiba nélkül.
Ha további segítségre van szüksége, próbálkozzon a [Azure Stream Analytics-fórumot](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>További lépések

* [Az Azure Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)
