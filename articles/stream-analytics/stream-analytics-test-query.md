---
title: Azure Stream Analytics-feladatok tesztelése mintaadatok
description: Ez a cikk azt ismerteti, hogyan használható a Azure Portal a Azure Stream Analytics feladatok teszteléséhez, a minta bemenetekhez és a mintaadatok feltöltéséhez.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/23/2019
ms.custom: seodec18
ms.openlocfilehash: d68c1ee084efc146d69d8404aadb8acbb4cc33d1
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2019
ms.locfileid: "73891755"
---
# <a name="test-an-azure-stream-analytics-job-with-sample-data"></a>Azure Stream Analytics-feladatok tesztelése mintaadatok

A Azure Stream Analytics a feladatok elindítása vagy leállítása nélkül is tesztelheti a lekérdezést. A bejövő adatok lekérdezéseit tesztelheti a bemeneti fogadóból, vagy feltöltötte a mintaadatok egy helyi fájlból az Azure Portalon. A lekérdezéseket helyileg is tesztelheti a helyi mintaadatok vagy élő adatok alapján a [Visual Studióban](stream-analytics-live-data-local-testing.md) és a [Visual Studio Code](vscode-local-run.md)-ban. 

## <a name="sample-incoming-data-from-input"></a>Bejövő adatok mintája bemenetből

Azure Stream Analytics automatikusan beolvassa az eseményeket a folyamatos átviteli bemenetből. Futtathat lekérdezéseket az alapértelmezett mintán, vagy beállíthatja a minta egy adott időkeretét.

1. Jelentkezzen be az Azure portálra.

2. Keresse meg és válassza ki a meglévő Stream Analytics feladatot.

3. A Stream Analytics-feladatok lap **feladatok topológiájának** fejlécében válassza a **lekérdezés** lehetőséget a lekérdezés-szerkesztő ablak megnyitásához. 

4. Ha szeretné megtekinteni a bejövő események mintáját, válassza a bevitel fájl ikont, és a minta eseményei automatikusan megjelennek a **bemeneti előnézetben**. 

   a. A rendszer automatikusan észleli az adataihoz tartozó szerializálási típust, ha a JSON-vagy CSV-fájlja. A legördülő menüben lévő lehetőség módosításával manuálisan is módosíthatja a JSON-t, a CSV-t és a AVRO.
    
   b. A választóval megtekintheti az adatait **táblázatos** vagy **nyers** formátumban.
    
   c. Ha a megjelenített adatai nem aktuálisak, válassza a **frissítés** lehetőséget a legújabb események megtekintéséhez.

   A következő táblázat a **táblázatos formátumú**adatmennyiségeket szemlélteti:

   ![Azure Stream Analytics minta bemenet táblázatos formátumban](./media/stream-analytics-test-query/asa-sample-table.png)

   A következő táblázat a **nyers formátumú**adatmennyiségeket szemlélteti:

   ![Azure Stream Analytics minta bemenete nyers formátumban](./media/stream-analytics-test-query/asa-sample-raw.png)

5. A lekérdezés a bejövő adatértékekkel való teszteléséhez válassza a **lekérdezés tesztelése**lehetőséget. Az eredmények a **test Results (eredmények** ) lapon jelennek meg. Az eredmények letöltéséhez kiválaszthatja az **eredmények** letöltése lehetőséget is.

   ![Azure Stream Analytics minta teszt lekérdezés eredményei](./media/stream-analytics-test-query/asa-test-query.png)

6. Ha a lekérdezéseket a bejövő események adott időtartományára szeretné tesztelni, válassza az **időtartomány kiválasztása**lehetőséget.
   
   ![Azure Stream Analytics időtartomány a bejövő minták eseményeihez](./media/stream-analytics-test-query/asa-select-time-range.png)

7. Állítsa be a lekérdezés teszteléséhez használni kívánt események időtartományát, és válassza a **minta**lehetőséget. Az adott időkereten belül akár 1000 eseményt, vagy 1 MB-ot is megadhat, attól függően, hogy melyik érkezik először.

   ![Azure Stream Analytics időtartomány beállítása a bejövő minták eseményeihez](./media/stream-analytics-test-query/asa-set-time-range.png)

8. Ha az eseményeket a kiválasztott időtartományra vonatkozóan mintavételnek tekinti, a **bemeneti előnézet** lapon jelennek meg.

   ![Azure Stream Analytics a tesztek eredményeinek megtekintése](./media/stream-analytics-test-query/asa-view-test-results.png)

9. A bejövő események mintájának megtekintéséhez válassza az **Alaphelyzetbe állítás** lehetőséget. Ha az **Alaphelyzetbe állítás**lehetőséget választja, az időtartomány kiválasztása el fog veszni. Válassza a **lekérdezés tesztelése** lehetőséget a lekérdezés teszteléséhez, és tekintse át az eredményeket a **teszteredmények** lapon.

10. Amikor módosításokat végez a lekérdezésben, válassza a **lekérdezés mentése** lehetőséget az új lekérdezési logika teszteléséhez. Ez lehetővé teszi a iteratív módosítását és újbóli tesztelését, hogy megtekintse a kimenet változásait.

11. A böngészőben megjelenő eredmények ellenőrzése után készen áll a művelet **elindítására** .

## <a name="upload-sample-data-from-a-local-file"></a>Mintaadatok feltöltése helyi fájlból

Az élő adatok helyett helyi fájlokból származó mintaadatok használatával tesztelheti a Azure Stream Analytics lekérdezést.

1. Jelentkezzen be az Azure portálra.
   
2. Keresse meg a meglévő Stream Analytics feladatot, és válassza ki.

3. A Stream Analytics-feladatok lap **feladatok topológiájának** fejlécében válassza a **lekérdezés** lehetőséget a lekérdezés-szerkesztő ablak megnyitásához.

4. A lekérdezés helyi fájllal való teszteléséhez válassza a **minta bemenet feltöltése** a **bemeneti előnézet** lapon lehetőséget. 

   ![Minta Azure Stream Analytics feltöltése](./media/stream-analytics-test-query/asa-upload-sample-file.png)

5. Töltse fel a helyi fájlt a lekérdezés teszteléséhez. Csak a JSON-, CSV-vagy AVRO-formátumokkal tölthet fel fájlokat. Kattintson az **OK** gombra.

   ![Minta Azure Stream Analytics feltöltése](./media/stream-analytics-test-query/asa-upload-sample-json-file.png)

6. Amint feltölti a fájlt, a fájl tartalma táblázatként vagy nyers formátumban is megtekinthető az űrlapon. Ha az **Alaphelyzetbe állítás**lehetőséget választja, a mintaadatok visszatérnek az előző szakaszban ismertetett bejövő bemeneti adatokhoz. Bármilyen más fájlt is feltölthet a lekérdezés teszteléséhez.

7. A lekérdezés **tesztelése** lehetőség kiválasztásával tesztelheti a lekérdezést a feltöltött minta fájljával.

8. A tesztek eredményei a lekérdezés alapján jelennek meg. Módosíthatja a lekérdezést, és a lekérdezés **mentése** lehetőségre kattintva tesztelheti az új lekérdezési logikát. Ez lehetővé teszi a iteratív módosítását és újbóli tesztelését, hogy megtekintse a kimenet változásait.

9. Ha több kimenetet használ a lekérdezésben, az eredmények a kiválasztott kimenet alapján jelennek meg. 

   ![Azure Stream Analytics kiválasztott kimenet](./media/stream-analytics-test-query/asa-sample-test-selected-output.png)

10. A böngészőben megjelenített eredmények ellenőrzése után **elindíthatja** a feladatot.

## <a name="next-steps"></a>További lépések

* [Azure Stream Analytics Query Language Reference](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)

* [Példák a gyakori Stream Analytics használati mintákra](stream-analytics-stream-analytics-query-patterns.md)

* [Azure Stream Analytics-bemenetek ismertetése](stream-analytics-add-inputs.md)

* [A Azure Stream Analytics kimenetének megismerése](stream-analytics-define-outputs.md)