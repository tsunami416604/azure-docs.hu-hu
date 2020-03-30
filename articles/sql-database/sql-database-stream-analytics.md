---
title: Adatok streamelése a Stream Analytics-integráció használatával (előzetes verzió)
description: Az Azure Stream Analytics segítségével adatokat továbbíthatok egy Azure SQL-adatbázisba.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: ajetasin
ms.author: ajetasi
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: d233d3c98cc495e4b9e84142781f5eb9faa7eec8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73820844"
---
# <a name="stream-data-by-using-azure-sql-database-stream-analytics-integration-preview"></a>Adatok streamelése az Azure SQL Database Stream Analytics-integráció használatával (előzetes verzió)

A felhasználók mostantól az Azure Stream Analytics használatával közvetlenül az Azure PortalSQL-adatbázisából bedolgozhatják, feldolgozhatják, megtekinthetik és elemezhetik a valós idejű streamelési adatokat egy táblába az [Azure Portalon.](../stream-analytics/stream-analytics-introduction.md) Ez a tapasztalat lehetővé teszi a legkülönbözőbb forgatókönyvek, mint például a csatlakoztatott autó, távoli figyelés, csalás észlelése, és még sok más. Az Azure Portalon kiválaszthatja az eseményforrást (Event Hub/IoT Hub), megtekintheti a bejövő valós idejű eseményeket, és kiválaszthat egy táblát az események tárolásához. Stream Analytics-lekérdezési nyelvi lekérdezési lekérdezéseket is írhat a portálon a bejövő események átalakításához és a kiválasztott táblában való tárolásukhoz. Ez az új belépési pont a Stream Analyticsben már létező létrehozási és konfigurációs élmények mellett található. Ez a felhasználói élmény az adatbázis környezetéből indul ki, így gyorsan beállíthat egy Stream Analytics-feladatot, és zökkenőmentesen navigálhat az Azure SQL Database és a Stream Analytics-élmények között.

![Stream Analytics-folyamat](media/sql-database-stream-analytics/stream-analytics-flow.png)

## <a name="key-benefits"></a>Főbb előnyök

- Minimális környezetváltás: A portálon lévő SQL-adatbázisból indítható, és elkezdheti a valós idejű adatok betöltését egy táblába anélkül, hogy más szolgáltatásra váltana. 
- Lépések számának csökkentése: Az adatbázis és a tábla környezete a Stream Analytics-feladat előzetes konfigurálásához használható.
- További egyszerű használat az előzetes adatokkal: Az eseményforrásból érkező bejövő adatok (Event Hub/IoT Hub) előnézetének megtekintése a kiválasztott táblázat környezetében 


## <a name="prerequisites"></a>Előfeltételek

Az cikkben ismertetett lépések elvégzéséhez az alábbi erőforrásokra lesz szüksége:

- Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/) 
- Egy SQL adatbázis. További információt az [Egyetlen adatbázis létrehozása az Azure SQL Database-ben című témakörben talál.](sql-database-single-database-get-started.md)
- Tűzfalszabály, amely lehetővé teszi a számítógép számára, hogy csatlakozzon az Azure SQL-kiszolgálóhoz. További információt a [Kiszolgálószintű tűzfalszabály létrehozása](sql-database-server-level-firewall-rule.md)című témakörben talál.


## <a name="configure-stream-analytics-integration"></a>Streamanalitikai integráció konfigurálása

1. Jelentkezzen be az Azure portálra. 
2. Keresse meg az SQL-adatbázist, ahol be szeretné dolgozni a streamelési adatokat. Válassza **a Stream analytics (előzetes verzió) lehetőséget.** 

    ![Stream Analytics](media/sql-database-stream-analytics/stream-analytics.png)

3. A streamelési adatok SQL-adatbázisba történő betöltésének megkezdéséhez válassza a **Létrehozás és** név adást a streamelési feladatnak, majd válassza a **Tovább: Bevitel**lehetőséget. 

    ![Stream Analytics-feladat létrehozása](media/sql-database-stream-analytics/create-job.png)

4. Adja meg az események forrásának adatait, majd válassza **a Tovább: Kimenet**lehetőséget.

   - **Bemenet típusa**: Event Hub/IoT Hub
   - **Bemeneti alias**: Adjon meg egy nevet az események forrásának azonosításához 
   - **Előfizetés**: Megegyezik az SQL Database-előfizetéssel 
   - **Event Hub névtér**: Névtér neve 
   - **Az eseményközpont neve**: A kijelölt névtérben lévő eseményközpont neve 
   - **Az Event Hub házirendjének neve** (Új létrehozása alapértelmezett): Házirendnév megadása 
   - **Event Hub fogyasztói csoport** (Új létrehozása alapértelmezett): Adja meg a fogyasztói csoport nevét  
     - Azt javasoljuk, hogy hozzon létre egy fogyasztói csoportot és egy szabályzatot minden új Azure Stream Analytics-feladathoz, amelyet innen hoz létre. A fogyasztói csoportok csak öt egyidejű olvasót engedélyeznek, így minden feladathoz külön fogyasztói csoport biztosítása elkerüli az e korlát túllépéséből eredő hibákat. A dedikált házirend lehetővé teszi a kulcs elforgatását vagy az engedélyek visszavonását anélkül, hogy más erőforrásokat befolyásolna.

    ![Stream Analytics-feladat létrehozása](media/sql-database-stream-analytics/create-job-output.png)

5. Válassza ki, hogy melyik táblába szeretné bedolgozni a streamelési adatokat. Miután végzett, válassza a **Létrehozás lehetőséget.**
   - **Felhasználónév**, **Jelszó:** Adja meg hitelesítő adatait az SQL-kiszolgáló hitelesítéséhez. Válassza az **Érvényesítés** lehetőséget.
   - **Táblázat**: Válassza **az Új létrehozása vagy** a Meglévő **használata**lehetőséget. Ebben a folyamatban válasszuk a **Létrehozás lehetőséget.** Ez létrehoz egy új táblát, amikor elindítja a stream Analytics-feladatot.

    ![Stream Analytics-feladat létrehozása](media/sql-database-stream-analytics/create.png)

6. Megnyílik egy lekérdezési lap a következő részletekkel:

   - A **bemeneti** (bemeneti események forrása), amelyből adatokat fog beadni  
   - A **kimenet** (kimeneti tábla), amely az átalakított adatokat tárolja 
   - Minta [SAQL lekérdezés](../stream-analytics/stream-analytics-stream-analytics-query-patterns.md) SELECT utasítással. 
   - **Bemeneti előnézet**: A bemeneti események forrásából érkező legújabb bejövő adatok pillanatképét jeleníti meg.  
     - A szerializálás típusa az adatok automatikusan észleli (JSON/CSV). Manuálisan is módosíthatja JSON/CSV/AVRO-ra. 
     - A bejövő adatok at táblázat vagy nyers formátumban tekintheti meg. 
     - Ha a megjelenített adatok nem aktuálisak, a legújabb események megtekintéséhez válassza a **Frissítés** lehetőséget. 
     - Válassza **az Időtartomány kiválasztása lehetőséget,** ha a lekérdezést a bejövő események egy adott időtartományával szeretné tesztelni. 
     - Válassza **a Mintabevitel feltöltése** lehetőséget a lekérdezés teszteléséhez egy minta JSON/CSV-fájl feltöltésével. A SAQL-lekérdezés teszteléséről az [Azure Stream Analytics-feladat tesztelése mintaadatokkal](../stream-analytics/stream-analytics-test-query.md)című témakörben talál további információt. 

    ![teszt lekérdezés](media/sql-database-stream-analytics/test-query.png)


   - **Teszteredmények:** Válassza a **Teszt lekérdezés** lehetőséget, és láthatja a streamelési lekérdezés eredményeit 

    ![vizsgálati eredmények](media/sql-database-stream-analytics/test-results.png)

   - **Vizsgálati eredmények sémája**: A streamelési lekérdezés eredményeinek sémája látható a tesztelés után. Győződjön meg arról, hogy a teszteredmények sémája megegyezik a kimeneti sémával. 

    ![teszteredmények sémája](media/sql-database-stream-analytics/test-results-schema.png)


   - **Kimeneti séma**: Az 5.
     - Új létrehozása: Ha ezt a beállítást választotta az 5. Új tábla létrehozásakor válassza ki a megfelelő táblaindexet. A táblaindexelésről további információt [a Fürtözött és nem fürtözött indexek leírása című témakörben talál.](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described/)
    - Meglévő használata: Ha ezt a beállítást választotta az 5. 
 
7. Miután befejezte a lekérdezés szerkesztési & tesztelését, válassza a **Lekérdezés mentése**lehetőséget. Válassza **a Stream Analytics-feladat indítása lehetőséget** az átalakított adatok SQL táblává történő betöltésének megkezdéséhez. A következő mezők véglegesítése után **indítsa el** a feladatot. 
   - **Kimeneti kezdési idő**: Ez határozza meg a feladat első kimenetének idejét.  
     - Most: A feladat most indul, és feldolgozza az új bejövő adatokat.
     - Egyéni: A feladat most indul el, de egy adott időpontból (amely lehet a múltban vagy a jövőben) adatokat fog feldolgozni. További információ: [Azure Stream Analytics-feladat indítása.](../stream-analytics/start-job.md)
   - **Streamelési egységek:** Az Azure Stream Analytics díja az adatok szolgáltatásba történő feldolgozásához szükséges streamelési egységek száma. További információ: [Azure Stream Analytics-díjszabás.](https://azure.microsoft.com/pricing/details/stream-analytics/) 
   - **Kimeneti adatok hibakezelése:**  
     - Újrapróbálkozás: Hiba esetén az Azure Stream Analytics újramegpróbálja az eseményt határozatlan ideig írni, amíg az írás sikeres nem lesz. Az újrapróbálkozások nem időtúlodott. Végül az összes további esemény feldolgozása az újrapróbálkozó esemény blokkolja. Ez a beállítás az alapértelmezett kimeneti hibakezelési házirend. 
     - Drop: Az Azure Stream Analytics minden olyan kimeneti eseményt eldob, amely adatkonverziós hibát eredményez. Az elvetett események később nem állíthatók helyre a feldolgozás újbóli megkísérléséhez. Az összes átmeneti hibát (például a hálózati hibákat) a kimeneti hibakezelési házirend konfigurációjától függetlenül újra próbálkozik. 
   - **SQL Database kimeneti beállítások:** Az előző lekérdezési lépés particionálási sémájának öröklésére szolgáló lehetőség, amely lehetővé teszi a teljes mértékben párhuzamos topológiát több íróval a táblához. További információ: [Azure Stream Analytics-kimenet az Azure SQL Database számára.](../stream-analytics/stream-analytics-sql-output-perf.md)
   - **Maximális kötegszám:** Az egyes tömeges beszúrási tranzakciókhoz küldött rekordok számának ajánlott felső határa.  
    A kimeneti hibakezelésről további információt az [Azure Stream Analytics kimeneti hibaszabályzata című témakörben talál.](../stream-analytics/stream-analytics-output-error-policy.md)  

    ![feladat indítása](media/sql-database-stream-analytics/start-job.png)

8. Miután elindítja a feladatot, látni fogja a Futó feladat a listában, és a következő műveleteket teheti: 
   - **A feladat indítása/leállítása:** Ha a feladat fut, leállíthatja a feladatot. Ha a feladat leáll, elindíthatja a feladatot. 
   - **Feladat szerkesztése:** A lekérdezés szerkeszthető. Ha azt szeretnénk, hogy több módosítást a feladat ex, adjunk hozzá további bemenetek/kimenetek, majd nyissa meg a feladatot a Stream Analytics. A Szerkesztés beállítás le van tiltva, ha a feladat fut. 
   - **Kimeneti tábla előnézete**: A tábla előnézete az SQL-lekérdezésszerkesztőben tekinthető meg. 
   - **Megnyitás a Stream Analytics szolgáltatásban:** Nyissa meg a feladatot a Stream Analytics szolgáltatásban a feladat figyelési és hibakeresési részleteinek megtekintéséhez. 


    ![adatfolyam-elemzési feladatok](media/sql-database-stream-analytics/jobs.png)






## <a name="next-steps"></a>További lépések

- [Az Azure Stream Analytics dokumentációja](https://docs.microsoft.com/azure/stream-analytics/)
- [Az Azure Stream Analytics megoldásmintái](../stream-analytics/stream-analytics-solution-patterns.md)
