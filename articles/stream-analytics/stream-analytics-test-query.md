---
title: Egy Azure Stream Analytics-feladat mintaadatokkal tesztelése
description: Hogyan tesztelheti a lekérdezéseket a Stream Analytics-feladatok.
keywords: A cikkből megtudhatja, hogyan használható az Azure-portálon tesztelése egy Azure Stream Analytics-feladat, a minta bemenet, és töltse fel a mintaadatokat.
services: stream-analytics
author: jasonwhowell
ms.author: jasonh
ms.reviewer: jasonh
manager: kfile
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/27/2018
ms.openlocfilehash: 3dc9091934f3db8ededc13f74d2f302eccace4d6
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2018
---
# <a name="test-a-stream-analytics-query-with-sample-data"></a>A Stream Analytics-lekérdezés mintaadatokkal tesztelése

Azure Stream Analytics segítségével töltse fel adatokat, valamint a lekérdezés az Azure portálon elindítása vagy a feladat leállítása nélkül.

## <a name="upload-sample-data-and-test-the-query"></a>Töltse fel a mintaadatok és a lekérdezés tesztelése

1. Jelentkezzen be az Azure portálra. 

2. Keresse meg a meglévő Stream Analytics-feladat, és válassza ki azt.

3. A Stream Analytics a sikertelen feladat-oldalon, a a **feladat topológia** elemcsoportban válasszon **lekérdezés** a Lekérdezésszerkesztő ablakának megnyitása. 

4. A minta bemeneti adatokat tartalmazó lekérdezés teszteléséhez kattintson a jobb gombbal bármelyik bemenet.  Válassza ki **tölthet fel fájlból adatot**.

   Az adatok csak JSON formátumú adatokat kell lennie. Ha az adatok egy másik formátumban, például a fürt megosztott kötetei szolgáltatás, akkor kell azt átalakíthatja JSON feltöltés előtt. Használhatja, mint bármely opensource átalakítás eszközt [a JSON-konverter CSV](http://www.convertcsv.com/csv-to-json.htm) az adatok JSON formátumúvá konvertálni.

    ![Stream analytics lekérdezési szerkesztő lekérdezés tesztelése](media/stream-analytics-test-query/stream-analytics-test-query-editor-upload.png)

5. A feltöltés befejeződése után válassza ki a **tesztelése** tesztelése a megadott mintaadatokat irányuló lekérdezés.

    ![Stream analytics lekérdezési szerkesztő teszt mintaadatokat](media/stream-analytics-test-query/stream-analytics-test-query-editor-test.png)

6. Ha a teszt eredménye későbbi használatra, a lekérdezés kimenetét megjelenik egy hivatkozás a letöltés eredményeit a webböngésző. 

7. Ismételt módosítsa a lekérdezést, és tesztelik azt ismételt használatával ellenőrizheti, hogyan a kimeneti változik.

   ![Stream Analytics query editor minta kimenet](media/stream-analytics-test-query/stream-analytics-test-query-editor-samples-output.png)

   Ha több kimenet egy lekérdezésben használja, a eredmények jelennek külön lapon, és akkor könnyen lehet váltani közöttük.

8. Miután ellenőrizte, hogy a böngészőben megjelenített eredményekre **mentése** a lekérdezést. Majd **Start** a feladatot, és hagyja, hogy a bejövő események feldolgozásához.

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
