---
title: Diagnosztikai naplók segítségével az Azure Stream Analytics hibáinak elhárítása
description: Ez a cikk ismerteti az Azure Stream Analytics diagnosztikai naplóinak elemzése.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/19/2019
ms.custom: seodec18
ms.openlocfilehash: cc62a6b9f03bdd6dc8671a6cf96113a2234fc092
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2019
ms.locfileid: "57247154"
---
# <a name="troubleshoot-azure-stream-analytics-by-using-diagnostics-logs"></a>Az Azure Stream Analytics hibáinak elhárítása a diagnosztikai naplók használatával

Néha előfordul Azure Stream Analytics-feladat váratlanul feldolgozása leáll. Fontos, hogy ilyen esetekben hibaelhárítást tudjon végezni. A hibákat váratlan lekérdezési eredmény, az eszközök kapcsolata vagy váratlan szolgáltatásleállás okozhatja. A diagnosztikai naplók a Stream Analytics segítségével, azonosíthatja az okot, problémák, amikor fordulhat elő, és a helyreállítási idő csökkentése.

## <a name="log-types"></a>Napló típusa

Stream Analytics naplók két típusú kínálja:

* [A Tevékenységnaplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) (mindig bekapcsolva), amely feladatok végrehajtott műveletek betekintést biztosítanak.

* [Diagnosztikai naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) (konfigurálható), amely részletesebb betekintést nyújtson mindent, amely fordul elő, ha egy feladat. Diagnosztikai naplók a feladat létrehozásakor, a kezdő és záró, a feladat törlése. Ha a feladat frissül és a futás közben események terjed ki.

> [!NOTE]
> Szolgáltatások, például az Azure Storage, Azure Event hubs is – használható, és az Azure Monitor naplózza a nem megfelelő adatok elemzéséhez. Ezek a szolgáltatások díjszabási modellje alapján lesznek kiszámlázva.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="debugging-using-activity-logs"></a>Naplózza a hibakeresési tevékenység használatával

Alapértelmezés szerint a tevékenységnaplókat, és a Stream Analytics-feladat által végrehajtott műveletek magas szintű betekintést biztosítanak. Tevékenységnaplók található információk segíthetnek a negatív hatással a feladat a problémák kiváltó okának. Kövesse az alábbi lépéseket a Stream Analytics tevékenységeket tartalmazó naplók használata:

1. Jelentkezzen be az Azure Portalon, és válassza **tevékenységnapló** alatt **áttekintése**.

   ![Stream Analytics tevékenységnapló](./media/stream-analytics-job-diagnostic-logs/stream-analytics-menu.png)

2. Láthatja a végrehajtott műveletek listája. Minden műveletre, a feladat sikertelenségét okozó rendelkezik egy piros info buborékra.

3. Kattintson egy műveletet az összefoglaló nézet megjelenítéséhez. Itt információkat gyakran korlátozódik. A művelettel kapcsolatos további részletekért kattintson a **JSON**.

   ![Stream Analytics tevékenység log művelet összegzése](./media/stream-analytics-job-diagnostic-logs/operation-summary.png)

4. Görgessen le a **tulajdonságok** a JSON-t, amely részletesen ismerteti a sikertelen műveletet okozó hibáról, szakaszában. Ebben a példában a hiba a kötött szélességi értékeknek from out of futásidejű hiba okozta.

   ![JSON-hiba részletei](./media/stream-analytics-job-diagnostic-logs/error-details.png)

5. A JSON-ban a hibaüzenet alapján korrekciós műveleteket hajthatja végre. Ebben a példában ellenőrzi annak biztosítása érdekében a szélességi értéknek-90 fok közé esik, és 90 fok hozzá kell adni a lekérdezéshez.

6. Ha a hibaüzenet a Tevékenységnaplókban nem hasznos lehet az alapvető ok azonosítása, diagnosztikai naplók engedélyezése és az Azure Monitor naplóira.

## <a name="send-diagnostics-to-azure-monitor-logs"></a>Diagnostics küldése az Azure Monitor naplóira

Erősen ajánlott bekapcsolni a diagnosztikai naplókat, és elküldi azokat az Azure Monitor naplóira. Diagnosztikai naplók **ki** alapértelmezés szerint. Diagnosztikai naplók bekapcsolásához hajtsa végre ezeket a lépéseket:

1.  Jelentkezzen be az Azure Portalon, és keresse meg a Stream Analytics-feladatot. A **figyelés**válassza **diagnosztikai naplók**. Válassza ki **diagnosztika bekapcsolása**.

    ![Panel Navigálás a diagnosztikai naplók](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs-monitoring.png)  

2.  Hozzon létre egy **neve** a **diagnosztikai beállítások** melletti jelölőnégyzetet, és **Küldés a Log Analyticsnek**. Ezután adjon hozzá egy meglévő, vagy hozzon létre egy új **Log analytics-munkaterület**. Jelölje be a **végrehajtási** és **szerzői műveletek** alatt **LOG**, és **AllMetrics** alatt **METRIKA** . Kattintson a **Save** (Mentés) gombra.

    ![Diagnosztikai naplók beállításai](./media/stream-analytics-job-diagnostic-logs/diagnostic-settings.png)

3. A Stream Analytics-feladat indításakor a diagnosztikai naplók legyenek átirányítva a Log Analytics-munkaterületre. Nyissa meg a Log Analytics-munkaterületet, és válassza a **naplók** alatt a **általános** szakaszban.

   ![Általános beállítások mellett az Azure Monitor naplóira](./media/stream-analytics-job-diagnostic-logs/log-analytics-logs.png)

4. Is [saját lekérdezés](../azure-monitor/log-query/get-started-portal.md) feltételek keres, azonosíthatja a trendeket minták elemzése és az adatok alapján elemzéseket nyújtanak. Például írhat egy lekérdezést az üzenetet az csak a diagnosztikai naplók szűrése "a folyamatos átviteli feladat nem sikerült." Az Azure Stream Analytics diagnosztikai naplóinak vannak tárolva a **AzureDiagnostics** tábla.

   ![Diagnosztikai lekérdezési és eredmények](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs-query.png)

5. Ha egy lekérdezést, amely a megfelelő naplók keres, mentse kiválasztásával **mentése** , és adja meg egy nevet és egy kategóriát. Ezután létrehozhat egy riasztás kiválasztásával **Új riasztási szabály**. Ezután adja meg a riasztási feltételt. Válassza ki **feltétel** és adja meg a küszöbérték és a gyakoriság, amellyel az egyéni napló keresési értékeli ki.  

   ![Diagnosztikai naplóbeli keresési lekérdezés](./media/stream-analytics-job-diagnostic-logs/search-query.png)

6. Válassza ki a műveletcsoport, és adja meg a riasztás részleteit, például a nevet és leírást, a riasztási szabály létrehozása előtt. A diagnosztikai naplók a különböző feladatok irányíthatja a Log Analytics munkaterületén. Ez lehetővé teszi, hogy a riasztások beállítása után, amely az összes feladat között működik.  

## <a name="diagnostics-log-categories"></a>Diagnosztikai naplója kategóriák

Jelenleg a Microsoft két kategóriába diagnosztikai naplók rögzítése:

* **Szerzői**: A szerzői műveletek, például a hozzáadások és törlések bemeneteit és kimeneteit, hozzáadását és a lekérdezés frissítése és indítása vagy a feladat leállítása feladat létrehozásakor feladat kapcsolódó naplózási eseményeket rögzíti.

* **Végrehajtási**: Rögzíti a feladat végrehajtása során előforduló eseményeket.
    * Kapcsolódási hibák
    * Hibák az adatfeldolgozási, többek között:
        * Eseményeket, amelyek nem felelnek meg a lekérdezés definíciója (eltérő mezőtípusok és értékek, hiányzó mezőket, és így tovább)
        * Kifejezés kiértékelési hibák
    * Egyéb események és hibák

## <a name="diagnostics-logs-schema"></a>Diagnosztikai naplók séma

Az összes napló JSON formátumban vannak tárolva. Mindegyik bejegyzés rendelkezik a következő gyakori karakterlánc mezőket:

Name (Név) | Leírás
------- | -------
time | A napló Timestamp (az UTC).
resourceId | Az, hogy a művelet került sor, nagybetűvel erőforrás azonosítója. Ez magában foglalja az előfizetés-azonosító, az erőforráscsoportot és a feladat nevét. Ha például   **/SUBSCRIPTIONS/6503D296-DAC1-4449-9B03-609A1F4A1C87/RESOURCEGROUPS/MY-RESOURCE-GROUP/PROVIDERS/MICROSOFT. STREAMANALYTICS/STREAMINGJOBS/MYSTREAMINGJOB**.
category | Kategória, vagy jelentkezzen **végrehajtási** vagy **szerzői műveletek**.
operationName | A művelet, amelyet a rendszer neve. Ha például **események küldése: SQL kimeneti hiba írni mysqloutput**.
status | A művelet állapotát. Ha például **sikertelen** vagy **sikeres**.
szint | Naplózási szint. Ha például **hiba**, **figyelmeztetés**, vagy **tájékoztató**.
properties | Napló bejegyzés-specifikus részletei, szerializált JSON-karakterláncot. További információkért lásd: Ez a cikk következő szakaszaiban.

### <a name="execution-log-properties-schema"></a>Végrehajtási napló tulajdonságok séma

Feladatvégrehajtási naplók rendelkezik, amely a Stream Analytics-feladat végrehajtása során történt eseményekkel kapcsolatos információkat. A séma tulajdonságainak az esemény típusát függően változik. Jelenleg a következő típusú feladatvégrehajtási naplók van:

### <a name="data-errors"></a>Adathibák

Ez a kategória-naplók van bármilyen hiba, amely, amíg a feladat adatainak feldolgozása folyamatban van. Ezek a naplók általában read, adatok szerializálási, során jönnek létre, és írási műveleteket. Ezek a naplók nem tartalmazzák a csatlakozási hibák. Kapcsolódási hibák általános események kell kezelni.

Name (Név) | Leírás
------- | -------
Forrás | A feladat neve bemeneti vagy kimeneti, ahol a hiba történt.
Üzenet | A hibával kapcsolatos üzenet.
Típus | Hiba típusa. Ha például **DataConversionError**, **CsvParserError**, vagy **ServiceBusPropertyColumnMissingError**.
Adatok | Pontosan keresse meg a hiba okának hasznos adatokat tartalmaz. Alá csonkolása méretétől függően.

Attól függően, a **operationName** érték adathibák sémája a következő:
* **Események szerializálni**. Szerializálható események történnek események az olvasási műveletek során. Ezek fordulhat elő, amikor a bemeneti adatok nem felelnek meg a lekérdezés sémája az alábbi okok valamelyike:
    * *Adattípus-eltérés (Németország) esemény során szerializálni*: A mező a hibát okozó azonosítja.
    * *Nem olvasható egy esemény, érvénytelen szerializálási*: Tartalmazza a bemeneti adatokat a hely, ahol a hiba történt. Blob bemeneti eltolás és a egy minta az adatok a blob nevét tartalmazza.
* **Események küldése**. Írási műveletek során bekövetkező események küldése A streamelési eseményt a hibát okozó azonosítsa azokat.

### <a name="generic-events"></a>Általános események

Általános események terjed ki minden mást.

Name (Név) | Leírás
-------- | --------
Hiba | (nem kötelező) Hiba adatok. Általában Ha ez a kivétel adatai érhető el.
Üzenet| A fenti üzenet jelenik meg.
Típus | Üzenet típusa. Hibák kategorizálása belső leképezések. Ha például **JobValidationError** vagy **BlobOutputAdapterInitializationFailure**.
Korrelációs azonosító | [GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier) , amely egyedileg azonosítja a feladat végrehajtása. Az összes végrehajtás naplóbejegyzések kezdve a feladat elindul mindaddig, amíg a feladat leáll rendelkezik azonos **korrelációs azonosító** értéket.

## <a name="next-steps"></a>További lépések

* [Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Stream Analytics használatának első lépései](stream-analytics-real-time-fraud-detection.md)
* [Stream Analytics-feladatok méretezése](stream-analytics-scale-jobs.md)
* [Stream Analytics lekérdezési nyelv leírása](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Stream Analytics felügyeleti REST API-referencia](https://msdn.microsoft.com/library/azure/dn835031.aspx)
