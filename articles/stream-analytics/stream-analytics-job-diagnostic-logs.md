---
title: Az Azure Stream Analytics hibaelhárítása diagnosztikai naplók használatával
description: Ez a cikk ismerteti, hogyan elemezheti a diagnosztikai naplók az Azure Stream Analytics.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/19/2019
ms.openlocfilehash: f318b373f6a6f46ee3a85703c6099c76568580ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426164"
---
# <a name="troubleshoot-azure-stream-analytics-by-using-diagnostics-logs"></a>Az Azure Stream Analytics hibaelhárítása diagnosztikai naplók használatával

Az Azure Stream Analytics-feladat feldolgozása néha váratlanul leáll. Fontos, hogy ilyen esetekben hibaelhárítást tudjon végezni. A hibákat váratlan lekérdezési eredmény, az eszközök kapcsolata vagy váratlan szolgáltatásleállás okozhatja. A Diagnosztikai naplók a Stream Analytics segítségével azonosítani az oka a problémák, amikor azok bekövetkeznek, és csökkenti a helyreállítási időt.

Erősen ajánlott a diagnosztikai naplók engedélyezése az összes feladathoz, mivel ez nagyban segít a hibakeresésben és a figyelésben.

## <a name="log-types"></a>Naplótípusok

A Stream Analytics kétféle naplót kínál:

* [Tevékenységnaplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) (mindig bekapcsolva), amelyek betekintést nyújtanak a feladatokon végrehajtott műveletekbe.

* [Diagnosztika naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) (konfigurálható), amely gazdagabb betekintést mindent, ami történik egy feladat. A diagnosztikai naplók a feladat létrehozásakor kezdődnek, és a feladat törlésekor befejeződnek. A feladat frissítésekor és futásközben idálnak eseményeket.

> [!NOTE]
> A nem megfelelő adatok elemzéséhez használhatja az olyan szolgáltatásokat, mint az Azure Storage, az Azure Event Hubs és az Azure Monitor-naplók. A szolgáltatások díjszabási modellje alapján kell fizetnie.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="debugging-using-activity-logs"></a>Hibakeresés tevékenységnaplók használatával

A tevékenységnaplók alapértelmezés szerint be vannak kapcsolva, és magas szintű betekintést nyújtanak a Stream Analytics-feladat által végrehajtott műveletekbe. A tevékenységnaplókban szereplő információk segíthetnek megtalálni a feladatot érintő problémák kiváltó okát. A Stream Analytics tevékenységnaplóinak használatához tegye a következő lépéseket:

1. Jelentkezzen be az Azure Portalra, és válassza **a Tevékenységnapló** **lehetőséget az Áttekintés területen.**

   ![Stream Analytics tevékenységnaplója](./media/stream-analytics-job-diagnostic-logs/stream-analytics-menu.png)

2. Megtekintheti a végrehajtott műveletek listáját. Minden olyan művelet, amely a feladat sikertelensítését okozta, piros információs buborékot jelenít meg.

3. Kattintson egy műveletre az összesítő nézet megtekintéséhez. Az itteni információk gyakran korlátozottak. Ha többet szeretne megtudni a műveletről, kattintson a **JSON gombra.**

   ![A Stream Analytics tevékenységnaplójának műveletének összegzése](./media/stream-analytics-job-diagnostic-logs/operation-summary.png)

4. Görgessen le a JSON **Tulajdonságok** szakaszához, amely a sikertelen műveletet okozó hiba részleteit tartalmazza. Ebben a példában a hiba a kötött szélességi értékekből származó futásidejű hiba miatt történt. A Stream Analytics-feladat által feldolgozott adatok eltérése adathibát okoz. Megismerheti a különböző [bemeneti és kimeneti adatok hibáit, és hogy miért fordulnak elő.](https://docs.microsoft.com/azure/stream-analytics/data-errors)

   ![JSON-hiba részletei](./media/stream-analytics-job-diagnostic-logs/error-details.png)

5. A JSON-ban található hibaüzenet alapján korrekciós műveleteket is végrehajthat. Ebben a példában ellenőrzi, hogy a szélességi érték között van -90 fok és 90 fok kell hozzáadni a lekérdezéshez.

6. Ha a tevékenységnaplókban található hibaüzenet nem hasznos a kiváltó ok azonosításában, engedélyezze a diagnosztikai naplókat, és használja az Azure Monitor-naplókat.

## <a name="send-diagnostics-to-azure-monitor-logs"></a>Diagnosztika küldése az Azure Monitor naplóiba

A diagnosztikai naplók bekapcsolása és az Azure Monitor-naplókba való elküldése erősen ajánlott. A diagnosztikai naplók alapértelmezés szerint ki vannak **kapcsolva.** A diagnosztikai naplók bekapcsolásához hajtsa végre az alábbi lépéseket:

1.  Jelentkezzen be az Azure Portalon, és keresse meg a Stream Analytics-feladat. A **Figyelés**csoportban válassza **a Diagnosztikai naplók**lehetőséget. Ezután válassza **a Diagnosztika bekapcsolása**lehetőséget.

    ![Blade navigáció a diagnosztikai naplókhoz](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs-monitoring.png)  

2.  Hozzon létre egy **nevet** a **Diagnosztikai beállítások között,** és jelölje be a Küldés a Log Analytics **szolgáltatásba**jelölőnégyzetet. Ezután adjon hozzá egy meglévőt, vagy hozzon létre egy új **Naplóelemzési munkaterületet.** Jelölje be a **Végrehajtás** és **a Szerzői műveletek** jelölőnégyzetet a LOG **,** és az **AllMetrics (Mérőszám)** **csoportban.** Kattintson a **Mentés** gombra. Javasoljuk, hogy a Stream Analytics-feladatával azonos Azure-régióban használjon Log Analytics-munkaterületet a további költségek elkerülése érdekében.

    ![Diagnosztikai naplók beállításai](./media/stream-analytics-job-diagnostic-logs/diagnostic-settings.png)

3. A Stream Analytics-feladat indításakor a diagnosztikai naplók a Log Analytics-munkaterületre kerülnek. A feladat diagnosztikai naplóinak megtekintéséhez válassza a **Naplók lehetőséget** a **Figyelés** szakaszban.

   ![Diagnosztikai naplók a figyelés csoportban](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs.png)

4. A Stream Analytics előre definiált lekérdezéseket biztosít, amelyek lehetővé teszik az Önt érdeklő naplók egyszerű keresését. A 3 kategória **általános**, **bemeneti adathibák** és **kimeneti adathibák**. Ha például a feladat összes fiókjában az elmúlt 7 napban előforduló összes hibát szeretné összefoglalót tekinteni, válassza a megfelelő előre definiált lekérdezés **futtatása** lehetőséget. 

   ![Diagnosztikai naplók a figyelés csoportban](./media/stream-analytics-job-diagnostic-logs/logs-categories.png)

   ![A naplók eredményei](./media/stream-analytics-job-diagnostic-logs/logs-result.png)

## <a name="diagnostics-log-categories"></a>Diagnosztikai naplókategóriák

Az Azure Stream Analytics a diagnosztikai naplók két kategóriáját rögzíti:

* **Szerzői**: Rögzíti a feladatszerzői műveletekhez kapcsolódó naplóeseményeket, például a munkahelyteremtést, a bemenetek és kimenetek hozzáadását és törlését, a lekérdezés hozzáadását és frissítését, valamint a feladat elindítását vagy leállítását.

* **Végrehajtás**: A feladat végrehajtása során bekövetkező eseményeket rögzíti.
    * Csatlakozási hibák
    * Adatfeldolgozási hibák, többek között:
        * A lekérdezésdefiníciónak nem megfelelő események (nem egyező mezőtípusok és értékek, hiányzó mezők és így tovább)
        * Kifejezéskiértékelési hibák
    * Egyéb események és hibák

## <a name="diagnostics-logs-schema"></a>Diagnosztikai naplók sémája

Minden napló JSON formátumban van tárolva. Minden bejegyzés a következő közös karakterláncmezőkkel rendelkezik:

Név | Leírás
------- | -------
time | A napló időbélyege (UTC-ben).
resourceId | Annak az erőforrásnak az azonosítója, amelyen a művelet zajlott, nagybetűvel. Tartalmazza az előfizetés azonosítóját, az erőforráscsoportot és a feladat nevét. Például a **/SUBSCRIPTIONS/6503D296-DAC1-4449-9B03-609A1F4A1C87/RESOURCEGROUPS/MY-RESOURCE-GROUP/PROVIDERS/MICROSOFT. STREAMANALYTICS/STREAMINGJOBS/MYSTREAMINGJOB**.
category | Naplókategória, **végrehajtás** vagy **szerzői jog.**
operationName | A naplózott művelet neve. **Például: Események küldése: SQL kimeneti írási hiba a mysqloutput**.
status | A művelet állapota. Például **sikertelen** vagy **sikeres**.
szint | Naplószint. Például **Hiba**, **Figyelmeztetés**vagy **Tájékoztató**.
properties | Naplóbejegyzés-specifikus részletek, JSON-karakterláncként szerializálva. További információt a cikk következő szakaszaiban talál.

### <a name="execution-log-properties-schema"></a>Végrehajtási napló tulajdonságainak sémája

A végrehajtási naplók információkat tartalmaznak a Stream Analytics-feladat végrehajtása során történt eseményekről. A tulajdonságok sémája attól függően változik, hogy az esemény adathiba vagy általános esemény.

### <a name="data-errors"></a>Adathibák

Minden olyan hiba, amely akkor fordul elő, afeladat feldolgozása során az adatok ebbe a kategóriába tartozik a naplók. Ezek a naplók leggyakrabban az adatok olvasása, szerializálása és írási műveletek során jönnek létre. Ezek a naplók nem tartalmazzák a kapcsolódási hibákat. A kapcsolódási hibákat a rendszer általános eseményként kezeli. További információ a különböző [bemeneti és kimeneti adathibák](https://docs.microsoft.com/azure/stream-analytics/data-errors)okairól.

Név | Leírás
------- | -------
Forrás | Annak a feladatbemenetnek vagy kimenetnek a neve, ahol a hiba történt.
Üzenet | A hibához társított üzenet.
Típus | A hiba típusa. Például **DataConversionError**, **CsvParserError**vagy **ServiceBusPropertyColumnMissingError**.
Adatok | Olyan adatokat tartalmaz, amelyek hasznosak a hiba forrásának pontos megtalálásához. Mérettől függően csonkolástól függően.

A **operationName** értéktől függően az adathibák sémája a következő:

* **Az események szerializálása** az eseményolvasási műveletek során történik. Ezek akkor fordulnak elő, ha a bemeneti adatok a következő okok valamelyike miatt nem felelnek meg a lekérdezéssémának:

   * *Írja be az eltérést az esemény (de)szerializálása során:* Azonosítja a hibát okozó mezőt.

   * *Nem lehet olvasni egy eseményt, érvénytelen szerializálás:* A bemeneti adatokban szereplő hely adatait listázza, ahol a hiba történt. Blob nevét blob bemenet, eltolás, és egy mintát az adatok.

* **A küldési események** írási műveletek során történnek. Azonosítják a hibát okozó streamelési eseményt.

### <a name="generic-events"></a>Általános események

Az általános események minden mást lefednek.

Név | Leírás
-------- | --------
Hiba | (nem kötelező) Hibainformáció. Ez általában kivételinformáció, ha rendelkezésre áll.
Üzenet| Naplóüzenet.
Típus | Az üzenet típusa. Leképezi a hibák belső kategorizálását. Például **JobValidationError** vagy **BlobOutputAdapterInitializationFailure**.
Korrelációs azonosító | [GUID,](https://en.wikipedia.org/wiki/Universally_unique_identifier) amely egyedileg azonosítja a feladat végrehajtását. A feladat kezdetétől a feladat leállításáig minden végrehajtási naplóbejegyzés azonos **korrelációs azonosítóértékkel** rendelkezik.

## <a name="next-steps"></a>További lépések

* [Bevezetés a Stream Analytics szolgáltatásba](stream-analytics-introduction.md)
* [Ismerkedés a Stream Analytics szolgáltatással](stream-analytics-real-time-fraud-detection.md)
* [Stream Analytics-feladatok skálázása](stream-analytics-scale-jobs.md)
* [A Stream Analytics lekérdezési nyelvének hivatkozása](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Stream Analytics-adatokkal kapcsolatos hibák](https://docs.microsoft.com/azure/stream-analytics/data-errors)
