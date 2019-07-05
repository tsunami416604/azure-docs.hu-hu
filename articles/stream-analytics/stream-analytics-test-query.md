---
title: Azure Stream Analytics-feladat, mintaadatokkal tesztelése
description: Ez a cikk ismerteti, hogyan használhatja az Azure Portalon egy Azure Stream Analytics-feladat, a minta bemenet tesztelése, és töltse fel a mintaadatokat.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 6/21/2019
ms.custom: seodec18
ms.openlocfilehash: 1a9caf83c6f4cd4ed15290afc872043c11234552
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508700"
---
# <a name="test-a-stream-analytics-query-with-sample-data"></a>A Stream Analytics lekérdezési mintaadatokkal tesztelése

Az Azure Stream Analytics használatával az adatokat a bemeneti vagy a mintaadatok feltöltése a lekérdezések tesztelése az Azure Portalon indítása és feladat leállítása nélkül.

## <a name="upload-or-sample-data-from-a-live-source-to-test-the-query"></a>Feltöltés vagy mintaadatokat is abból az élő forrást a lekérdezés teszteléséhez

1. Jelentkezzen be az Azure portálra. 

2. Keresse meg a meglévő Stream Analytics-feladatot, és válassza ki azt.

3. A Stream Analytics a feladat oldal, a a **Feladattopológia** szakaszban kattintson **lekérdezés** a lekérdezés szerkesztő ablak megnyitásához. 

4. A lekérdezés teszteléséhez, is majd vagy az adatokat a bemeneti élő, vagy feltöltése fájlból. Az adatok JSON-, CSV-vagy AVRO szerializálni kell. Minta beviteli UTF-8 kódolású legyen, és nem tömöríti. Csak a vessző (,) elválasztó a portálon a fürt megosztott kötetei szolgáltatás bemenet tesztelése esetén támogatott.

    1. A bemeneti élő: kattintson a jobb gombbal bármelyik a bemeneteket. Válassza ki **mintaadatok bemenetből**. A következő képernyőn beállíthatja a minta időtartamára. Mintavételi események élő forrásból fogja lekérni legfeljebb 1000 esemény vagy 1 MB (amelyik előbb bekövetkezik), így az adatok mintavételezése nem biztos, hogy a megadott teljes időtartam alatt.

    1. Fájl segítségével: kattintson a jobb gombbal bármelyik a bemeneteket. Válassza ki **fájlból mintaadatok feltöltése**. 

    ![Stream analytics query editor testovat dotaz](media/stream-analytics-test-query/stream-analytics-test-query-editor-upload.png)

5. A mintavételi vagy a feltöltés befejeződése után válassza ki a **tesztelése** a lekérdezést a mintaadatokkal tesztelheti.

    ![Stream analytics-lekérdezés szerkesztő teszt mintaadatok](media/stream-analytics-test-query/stream-analytics-test-query-editor-test.png)

6. A teszt eredménye későbbi használatra van szükség, ha a lekérdezés kimenete a böngészőt a letöltés eredményeit mutató hivatkozást tartalmazó jelenik meg. 

7. Iteratív módosítsa a lekérdezést, és a tesztelés közben ismételt használatával ellenőrizheti, hogyan változik a kimenet.

   ![Stream Analytics query editor sample output](media/stream-analytics-test-query/stream-analytics-test-query-editor-samples-output.png)

   Amikor lekérdezést használ több kimenetek, az eredményeket külön lapokra jelennek meg, és könnyedén válthat közöttük.

8. Miután meggyőződött a böngészőben megjelenített eredményekre **mentése** a lekérdezést. Ezután **Start** a feladatot, és biztosítani, hogy a bejövő események feldolgozására.

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
