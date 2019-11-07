---
title: Adatfolyam-adatátvitel Azure SQL Database Stream Analytics Integration (előzetes verzió) használatával
description: A Azure Stream Analytics használatával továbbíthatja az adatstreameket egy Azure SQL Database-adatbázisba.
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
ms.openlocfilehash: fb889f14c3370e1297f98110903c64e93e09c946
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73687061"
---
# <a name="stream-data-by-using-azure-sql-database-stream-analytics-integration-preview"></a>Adatfolyam-adatátvitel Azure SQL Database Stream Analytics Integration (előzetes verzió) használatával

A felhasználók mostantól a valós idejű adatfolyam-adatok egy táblázatba való betöltését, feldolgozását, megtekintését és elemzését közvetlenül a Azure Portalban lévő SQL-adatbázisból [Azure stream Analytics](../stream-analytics/stream-analytics-introduction.md)használatával végezheti el. Ez a felhasználói élmény számos különböző forgatókönyvet támogat, például a csatlakoztatott autót, a távoli figyelést, a csalások észlelését és sok más lehetőséget. A Azure Portal kiválaszthatja az események forrását (Event hub/IoT Hub), megtekintheti a bejövő valós idejű eseményeket, és kiválaszthatja az események tárolására szolgáló táblázatot. Stream Analytics lekérdezési nyelvi lekérdezéseket is írhat a portálon a bejövő események átalakításához és a kiválasztott táblában való tárolásához. Ez az új belépési pont a Stream Analyticsban már létező létrehozási és konfigurációs élményeken felül van. Ez a felület az adatbázis kontextusában indul el, és lehetővé teszi, hogy gyorsan beállítson egy Stream Analytics feladatot, és zökkenőmentesen navigáljon a Azure SQL Database és a Stream Analytics-élmény között.

![Stream Analytics folyamat](media/sql-database-stream-analytics/stream-analytics-flow.png)

## <a name="key-benefits"></a>Főbb előnyök

- Minimális környezeti váltás: elindíthat egy SQL Database a portálon, és megkezdheti a valós idejű adatok betöltését egy táblába anélkül, hogy más szolgáltatásra kellene váltania. 
- Csökkentett számú lépés: az adatbázis és a tábla kontextusa a Stream Analytics feladatok előzetes konfigurálására szolgál.
- További egyszerű használat az előzetes verziójú adatokkal: a beérkező adatok előnézete az események forrásáról (Event hub/IoT Hub) a kiválasztott tábla kontextusában 


## <a name="prerequisites"></a>Előfeltételek

A cikkben szereplő lépések végrehajtásához a következő erőforrásokra van szükség:

- Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/). 
- Egy SQL-adatbázis. Részletekért lásd: [önálló adatbázis létrehozása Azure SQL Databaseban](sql-database-single-database-get-started.md).
- Tűzfalszabály, amely lehetővé teszi a számítógép számára az Azure SQL Serverhez való kapcsolódást. Részletekért lásd: [kiszolgálói szintű tűzfalszabály létrehozása](sql-database-server-level-firewall-rule.md).


## <a name="configure-stream-analytics-integration"></a>A stream Analytics-integráció konfigurálása

1. Jelentkezzen be az Azure portálra. 
2. Navigáljon ahhoz az SQL-adatbázishoz, ahol be szeretné állítani a folyamatos átviteli adatait. Válassza a **stream Analytics (előzetes verzió)** lehetőséget. 

    ![Stream Analytics](media/sql-database-stream-analytics/stream-analytics.png)

3. Az adatfolyam-adatok ebbe az SQL-adatbázisba való betöltéséhez válassza a **Létrehozás** lehetőséget, és adjon nevet a folyamatos átviteli feladatnak, majd válassza a **Tovább: bemenet**lehetőséget. 

    ![Stream Analytics-feladatok létrehozása](media/sql-database-stream-analytics/create-job.png)

4. Adja meg az események forrásának adatait, majd válassza a **Tovább: kimenet**lehetőséget.

   - **Bemenet típusa**: Event Hub/IoT hub
   - **Bemeneti alias**: adjon meg egy nevet az események forrásának azonosításához 
   - **Előfizetés**: ugyanaz, mint SQL Database előfizetés 
   - **Event hub-névtér**: névtér neve 
   - **Event hub neve**: az Event hub neve a kiválasztott névtéren belül 
   - **Event hub-házirend neve** (alapértelmezés szerint új létrehozása): adjon meg egy szabályzatot 
   - **Event hub fogyasztói csoport** (alapértelmezés szerint új létrehozása): felhasználói csoport nevének megadása  
     - Javasoljuk, hogy hozzon létre egy fogyasztói csoportot és egy szabályzatot minden olyan új Azure Stream Analytics feladatokhoz, amelyeket itt hoz létre. A fogyasztói csoportok csak öt egyidejű olvasót engedélyeznek, így az egyes feladatokhoz egy dedikált fogyasztói csoportot biztosítanak, így elkerülhetők a korlátot meghaladó hibák. A dedikált szabályzat lehetővé teszi a kulcs elforgatását vagy az engedélyek visszavonását anélkül, hogy ez hatással lenne más erőforrásokra.

    ![Stream Analytics-feladatok létrehozása](media/sql-database-stream-analytics/create-job-output.png)

5. Válassza ki, hogy melyik táblát szeretné bevenni a streaming-adataiba. Ha elkészült, válassza a **Létrehozás**lehetőséget.
   - **Felhasználónév**, **jelszó**: adja meg az SQL Server-hitelesítéshez tartozó hitelesítő adatait. Válassza az **Érvényesítés** lehetőséget.
   - **Tábla**: válassza az **új létrehozása** vagy a **meglévő használata**lehetőséget. Ehhez a folyamathoz válassza a **Létrehozás**lehetőséget. Ez egy új táblát hoz létre a stream Analytics-feladatok indításakor.

    ![Stream Analytics-feladatok létrehozása](media/sql-database-stream-analytics/create.png)

6. Megnyílik egy lekérdezési oldal a következő részletekkel:

   - A **bemenet** (bemeneti események forrása), amelyből adatokat fog betölteni  
   - Az átalakított adatokat tároló **kimenet** (kimeneti tábla) 
   - Példa [SAQL-lekérdezésre](../stream-analytics/stream-analytics-stream-analytics-query-patterns.md) a SELECT utasítással. 
   - **Bemeneti előnézet**: a bemeneti események forrásának legújabb bejövő adatainak pillanatképét jeleníti meg.  
     - A rendszer automatikusan észleli a szerializálási típust az adataiban (JSON/CSV). Manuálisan is módosíthatja azt JSON/CSV/AVRO. 
     - A bejövő adatértékeket táblázatos formátumban vagy nyers formátumban is megtekintheti. 
     - Ha a megjelenített adatai nem aktuálisak, válassza a **frissítés** lehetőséget a legújabb események megtekintéséhez. 
     - Válassza az **időtartomány kiválasztása** lehetőséget a lekérdezés teszteléséhez a bejövő események adott időtartománya alapján. 
     - Válassza a **minta bemenet feltöltése** lehetőséget a lekérdezés teszteléséhez egy JSON-vagy CSV-fájl feltöltésével. A SAQL-lekérdezések tesztelésével kapcsolatos további információkért lásd: [Azure stream Analytics-feladatok tesztelése a mintaadatokkel](../stream-analytics/stream-analytics-test-query.md). 

    ![lekérdezés tesztelése](media/sql-database-stream-analytics/test-query.png)


   - **Tesztek eredményei**: válassza a **teszt lekérdezés** lehetőséget, és láthatja a folyamatos átviteli lekérdezés eredményét. 

    ![teszteredmények](media/sql-database-stream-analytics/test-results.png)

   - **Teszteredmények sémája**: a tesztelés után megjeleníti a folyamatos átviteli lekérdezés eredményeinek sémáját. Győződjön meg arról, hogy a test Results séma megfelel a kimeneti sémának. 

    ![teszteredmények sémája](media/sql-database-stream-analytics/test-results-schema.png)


   - **Kimeneti séma**: az 5. lépésben kiválasztott tábla sémáját tartalmazza (új vagy meglévő).
     - Új létrehozása: Ha ezt a beállítást választotta az 5. lépésben, a séma még nem jelenik meg, amíg el nem indítja a folyamatos átviteli feladatot. Új tábla létrehozásakor válassza ki a megfelelő tábla-indexet. A tábla indexelésével kapcsolatos további információkért tekintse meg a következő témakörben [leírt fürtözött és nem fürtözött indexeket](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described/):.
    - Meglévő használata: Ha ezt a beállítást választotta az 5. lépésben, megjelenik a kiválasztott tábla sémája. 
 
7. Ha elkészült a lekérdezés & tesztelésével, válassza a **lekérdezés mentése**lehetőséget. Az átalakított adatok SQL-táblába való betöltésének megkezdéséhez válassza a **Start stream Analytics feladatok** lehetőséget. Miután véglegesíti a következő mezőket, **indítsa el** a feladatot. 
   - **Kimeneti kezdési idő**: Ez határozza meg a feladatok első kimenetének időpontját.  
     - Most: a feladatsor most elindítja az új bejövő adatok feldolgozását.
     - Custom (egyéni): a rendszer most elindítja a feladatot, de egy adott időpontból dolgozza fel az adatait (ez lehet a múltban vagy a jövőben is). További információ: [Azure stream Analytics feladatok elindítása](../stream-analytics/start-job.md).
   - **Folyamatos átviteli egységek**: a Azure stream Analytics díjszabása a szolgáltatásba való adatfeldolgozáshoz szükséges folyamatos átviteli egységek számával történik. További információ: [Azure stream Analytics díjszabása](https://azure.microsoft.com/pricing/details/stream-analytics/). 
   - **Kimeneti adathibaok feldolgozása**:  
     - Újrapróbálkozás: Ha hiba lép fel, Azure Stream Analytics újrapróbálkozik az esemény határozatlan idejű írásával, amíg az írás nem sikerül. Az újrapróbálkozások esetében nincs időkorlát. Végül az összes további eseményt le kell tiltani az újrapróbálkozást követően. Ez a beállítás az alapértelmezett kimeneti hiba kezelési szabályzata. 
     - Drop: Azure Stream Analytics el fog dobni minden olyan kimeneti eseményt, amely Adatátalakítási hibát eredményez. Az elvetett események később nem állíthatók helyre a feldolgozás újbóli megkísérléséhez. A rendszer az összes átmeneti hibát (például hálózati hibákat) újrapróbálkozik, függetlenül attól, hogy milyen kimeneti hiba történik a házirend-konfigurációban. 
   - **SQL Database kimeneti beállítások**: az előző lekérdezési lépés particionálási sémájának öröklésére szolgáló lehetőség, amely lehetővé teszi, hogy teljesen párhuzamos topológiát engedélyezzen több író használatával a táblához. További információ: [Azure stream Analytics kimenet Azure SQL Database](../stream-analytics/stream-analytics-sql-output-perf.md).
   - **Kötegek maximális száma**: az összes tömeges beszúrási tranzakcióval ellátott rekordok számának ajánlott felső korlátja.  
    A kimeneti hibák kezelésével kapcsolatos további információkért lásd: [kimeneti hibák házirendjei a Azure stream Analyticsban](../stream-analytics/stream-analytics-output-error-policy.md).  

    ![kezdési feladatok](media/sql-database-stream-analytics/start-job.png)

8. Miután elindította a feladatot, látni fogja a futó feladatot a listában, és a következő műveleteket hajthatja végre: 
   - **A művelet indítása/leállítása**: Ha a feladatot futtatja, leállíthatja a feladatot. Ha a feladatot leállítja, elkezdheti a feladatot. 
   - **Feladatok szerkesztése**: szerkesztheti a lekérdezést. Ha további módosításokat szeretne végrehajtani a feladatban, vegyen fel további bemeneteket/kimeneteket, majd nyissa meg a feladatot Stream Analyticsban. A Szerkesztés lehetőség le van tiltva, amikor a feladatot futtatják. 
   - **Előzetes verziójú kimeneti tábla**: megtekintheti a táblázatot az SQL Query Editorban. 
   - **Megnyitás stream Analyticsban**: Nyissa meg a feladatot a stream Analytics szolgáltatásban a figyelés, a feladathoz tartozó hibakeresés részleteinek megtekintéséhez. 


    ![Stream Analytics-feladatok](media/sql-database-stream-analytics/jobs.png)






## <a name="next-steps"></a>További lépések

- [Azure Stream Analytics dokumentáció](https://docs.microsoft.com/azure/stream-analytics/)
- [Azure Stream Analytics megoldás mintái](../stream-analytics/stream-analytics-solution-patterns.md)
