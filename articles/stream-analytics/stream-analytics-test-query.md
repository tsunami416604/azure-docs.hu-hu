---
title: Azure Stream Analytics-feladat tesztelése mintaadatokkal
description: Ez a cikk ismerteti, hogyan használhatja az Azure Portalon egy Azure Stream Analytics-feladat tesztelésére, mintabevitelre és mintaadatok feltöltésére.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 3/6/2020
ms.custom: seodec18
ms.openlocfilehash: de2b99666dbed4eaec3db3e56800e9a7352e10e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898388"
---
# <a name="test-an-azure-stream-analytics-job-in-the-portal"></a>Azure Stream Analytics-feladat tesztelése a portálon

Az Azure Stream Analytics szolgáltatásban a feladat indítása vagy leállítása nélkül is tesztelheti a lekérdezést. A streamelési forrásokból érkező adatok lekérdezéseit tesztelheti, vagy mintaadatokat tölthet fel egy helyi fájlból az Azure Portalon. A helyi mintaadatokból vagy élő adatokból helyileg is tesztelheti a lekérdezéseket a [Visual Studio](stream-analytics-live-data-local-testing.md) és a Visual Studio [Code alkalmazásban.](visual-studio-code-local-run-live-input.md)

## <a name="automatically-sample-incoming-data-from-input"></a>Bejövő adatok automatikus mintája a bemenetből

Az Azure Stream Analytics automatikusan lekéri az eseményeket a streamelési bemenetekből. Futtathat lekérdezéseket az alapértelmezett mintán, vagy beállíthat egy adott időkeretet a mintához.

1. Jelentkezzen be az Azure portálra.

2. Keresse meg és válassza ki a meglévő Stream Analytics-feladatot.

3. A Stream Analytics feladatlapon, a **Feladattopológia** fejlécalatt válassza a **Lekérdezés** lehetőséget a Lekérdezésszerkesztő ablak megnyitásához. 

4. A bejövő események mintalistájának megtekintéséhez jelölje ki a fájlikonnal rendelkező bemenetet, és a mintaesemények automatikusan megjelennek a **Bemeneti előnézetben**.

   a. Az adatok szerializálási típusát a rendszer automatikusan észleli, ha annak JSON vagy CSV. Manuálisan is módosíthatja JSON, CSV, AVRO-ra a legördülő menü ben lévő lehetőség módosításával.
    
   b. A választóval **táblázat** vagy **nyers** formátumban tekintheti meg az adatokat.
    
   c. Ha a megjelenített adatok nem aktuálisak, a legújabb események megtekintéséhez válassza a **Frissítés** lehetőséget.

   Az alábbi táblázat táblázat formátumú adatokat **mutat be:**

   ![Azure Stream Analytics mintabevitel táblázat formátumban](./media/stream-analytics-test-query/asa-sample-table.png)

   Az alábbi táblázat egy példa a **Nyers formátumú adatokra:**

   ![Azure Stream Analytics-mintabevitel nyers formátumban](./media/stream-analytics-test-query/asa-sample-raw.png)

5. Ha a lekérdezést bejövő adatokkal szeretné tesztelni, válassza **a Lekérdezés tesztelése**lehetőséget. Az eredmények a **Teszteredmények** lapon jelennek meg. Az eredmények letöltéséhez az **Eredmények letöltése** lehetőséget is választhatja.

   ![Az Azure Stream Analytics mintateszt-lekérdezéseredményei](./media/stream-analytics-test-query/asa-test-query.png)

6. Ha a lekérdezést a bejövő események egy adott időtartományával szeretné tesztelni, válassza **az Időtartomány kiválasztása**lehetőséget.
   
   ![Az Azure Stream Analytics időtartománya a bejövő mintaeseményekhez](./media/stream-analytics-test-query/asa-select-time-range.png)

7. Állítsa be a lekérdezés teszteléséhez használni kívánt események időtartományát, és válassza a **Minta**lehetőséget. Ebben az időkeretben legfeljebb 1000 eseményt vagy 1 MB-ot kérhet le, attól függően, hogy melyik következik be előbb.

   ![Az Azure Stream Analytics beállította a bejövő mintaesemények időtartományát](./media/stream-analytics-test-query/asa-set-time-range.png)

8. Miután a kiválasztott időtartományban mintavételre került az események, megjelennek a **Bemeneti előnézet** lapon.

   ![Az Azure Stream Analytics nézet teszteredményei](./media/stream-analytics-test-query/asa-view-test-results.png)

9. Válassza **az Alaphelyzet** lehetőséget a bejövő események mintalistájának megtekintéséhez. Ha az Alaphelyzet lehetőséget **választja,** az időtartomány kiválasztása elvész. Válassza **a Lekérdezés tesztelése** lehetőséget a lekérdezés teszteléséhez és az eredmények áttekintéséhez a **Teszteredmények** lapon.

10. Amikor módosítja a lekérdezést, az új lekérdezési logika teszteléséhez válassza a **Lekérdezés mentése** lehetőséget. Ez lehetővé teszi, hogy iteratív módon módosítsa a lekérdezést, és tesztelje újra, hogy a kimenet hogyan változik.

11. Miután ellenőrizte a böngészőben megjelenített eredményeket, készen áll a feladat **elindítására.**

## <a name="upload-sample-data-from-a-local-file"></a>Mintaadatok feltöltése helyi fájlból

Az élő adatok használata helyett használhat egy helyi fájlból származó mintaadatokat az Azure Stream Analytics-lekérdezés teszteléséhez.

1. Jelentkezzen be az Azure portálra.
   
2. Keresse meg meglévő Stream Analytics-feladatát, és válassza ki.

3. A Stream Analytics feladatlapon, a **Feladattopológia** fejlécalatt válassza a **Lekérdezés** lehetőséget a Lekérdezésszerkesztő ablak megnyitásához.

4. Ha helyi fájllal szeretné tesztelni a lekérdezést, válassza a **Beviteli előnézet** lapon a **Mintabevitel feltöltése** lehetőséget. 

   ![Az Azure Stream Analytics mintafájlfeltöltése](./media/stream-analytics-test-query/asa-upload-sample-file.png)

5. Töltse fel a helyi fájlt a lekérdezés teszteléséhez. Csak JSON, CSV vagy AVRO formátumú fájlokat tölthet fel. Válassza **az OK gombot.**

   ![Az Azure Stream Analytics mintafájlfeltöltése](./media/stream-analytics-test-query/asa-upload-sample-json-file.png)

6. Amint feltölti a fájlt, az űrlapon lévő fájl tartalmát táblázatként vagy nyers formátumban is megtekintheti. Ha az Alaphelyzet lehetőséget **választja,** a mintaadatok visszaállnak az előző szakaszban ismertetett bejövő bemeneti adatokhoz. A lekérdezés teszteléséhez bármely más fájlt feltölthet.

7. Válassza **a Lekérdezés tesztelése** lehetőséget a lekérdezés feltöltött mintafájllal való teszteléséhez.

8. A teszteredmények a lekérdezés alapján jelennek meg. Módosíthatja a lekérdezést, és az új lekérdezési logika teszteléséhez a **Lekérdezés mentése** lehetőséget választhatja. Ez lehetővé teszi, hogy iteratív módon módosítsa a lekérdezést, és tesztelje újra, hogy a kimenet hogyan változik.

9. Ha több kimenetet használ a lekérdezésben, az eredmények a kiválasztott kimenet alapján jelennek meg. 

   ![Az Azure Stream Analytics kiválasztott kimenete](./media/stream-analytics-test-query/asa-sample-test-selected-output.png)

10. Miután ellenőrizte a böngészőben megjelenített eredményeket, **elindíthatja** a feladatot.

## <a name="next-steps"></a>További lépések
* [IoT-megoldás létrehozása a Stream Analytics használatával:](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-build-an-iot-solution-using-stream-analytics)ez az oktatóanyag végigvezeti Önt egy teljes körű megoldás létrehozásához egy adatgenerátorral, amely szimulálja a forgalmat egy fizetős fülkében.

* [Azure Stream Analytics Query Language Reference (Referencia az Azure Stream Analytics lekérdezési nyelvhez)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)

* [Példák lekérdezése a Stream Analytics gyakori használati mintáira](stream-analytics-stream-analytics-query-patterns.md)

* [Az Azure Stream Analytics bemeneteinek megismerése](stream-analytics-add-inputs.md)

* [Az Azure Stream Analytics kimeneteinek megismerése](stream-analytics-define-outputs.md)
