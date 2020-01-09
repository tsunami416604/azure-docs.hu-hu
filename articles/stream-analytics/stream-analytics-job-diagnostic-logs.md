---
title: Diagnosztikai naplók használata Azure Stream Analytics
description: Ez a cikk bemutatja, hogyan elemezheti a diagnosztikai naplókat a Azure Stream Analyticsban.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/19/2019
ms.openlocfilehash: f318b373f6a6f46ee3a85703c6099c76568580ba
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75426164"
---
# <a name="troubleshoot-azure-stream-analytics-by-using-diagnostics-logs"></a>Azure Stream Analytics hibakeresése diagnosztikai naplók használatával

Az Azure Stream Analytics-feladat feldolgozása néha váratlanul leáll. Fontos, hogy ilyen esetekben hibaelhárítást tudjon végezni. A hibákat váratlan lekérdezési eredmény, az eszközök kapcsolata vagy váratlan szolgáltatásleállás okozhatja. A diagnosztikai naplók a Stream Analytics segítségével azonosíthatja a problémák okát, amikor azok bekövetkeznek, és csökkentik a helyreállítási időt.

Javasoljuk, hogy minden feladat esetében engedélyezze a diagnosztikai naplókat, mivel ez nagy mértékben segítséget nyújt a hibakereséshez és a figyeléshez.

## <a name="log-types"></a>Naplózási típusok

Stream Analytics kétféle naplót kínál:

* A [tevékenységek naplói](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) (always on), amelyek betekintést nyújtanak a feladatokon végrehajtott műveletekre.

* [Diagnosztikai naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) (konfigurálható), amelyek mélyebb betekintést nyújtanak a feladatokba kerülő összes művelethez. A diagnosztikai naplók a feladatok létrehozásakor és befejezésekor kezdődnek, amikor a rendszer törli a feladatot. Ezek a feladatok a feladatoknak a frissítésekor és a futása közben is lefedik az eseményeket.

> [!NOTE]
> Olyan szolgáltatásokat használhat, mint például az Azure Storage, az Azure Event Hubs és a Azure Monitor naplók a nem megfelelő adatelemzések elemzéséhez. Ezen szolgáltatások díjszabási modellje alapján számítjuk fel a díjat.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="debugging-using-activity-logs"></a>Hibakeresés a Tevékenységnaplók használatával

A Tevékenységnaplók alapértelmezés szerint be vannak kapcsolva, és magas szintű betekintést nyújtanak a Stream Analytics-feladatok által végrehajtott műveletekre. A tevékenység naplóiban található információk segíthetnek megtalálni a feladatát befolyásoló problémák kiváltó okát. A következő lépések végrehajtásával használhatja a Stream Analyticsi tevékenység naplóit:

1. Jelentkezzen be a Azure Portalba, és válassza a **műveletnapló** lehetőséget az **Áttekintés**területen.

   ![Stream Analytics tevékenység naplója](./media/stream-analytics-job-diagnostic-logs/stream-analytics-menu.png)

2. A végrehajtott műveletek listája látható. Minden olyan művelet, amely a feladat hibáját okozta, piros információs buborékmal rendelkezik.

3. Kattintson egy műveletre az összesítő nézet megtekintéséhez. Az itt található információk gyakran korlátozottak. Ha további információt szeretne megtudni a műveletről, kattintson a **JSON**elemre.

   ![Stream Analytics tevékenység naplózási műveletének összegzése](./media/stream-analytics-job-diagnostic-logs/operation-summary.png)

4. Görgessen le a JSON **Tulajdonságok** szakaszába, amely részletesen ismerteti a sikertelen műveletet okozó hibát. Ebben a példában a hiba oka, hogy egy futásidejű hiba történt a kötött szélességi értékektől. Egy Stream Analytics feladatsor által feldolgozott adatok eltérése adathibát okoz. Megismerheti a [bemeneti és kimeneti adatok különböző hibáit és azok okait](https://docs.microsoft.com/azure/stream-analytics/data-errors).

   ![JSON-hiba részletei](./media/stream-analytics-job-diagnostic-logs/error-details.png)

5. A JSON-hibaüzeneten alapuló korrekciós műveleteket is végrehajthat. Ebben a példában ellenőrzi, hogy a szélességi érték a-90 fok és a 90 fok közé kerüljön-e a lekérdezéshez.

6. Ha a tevékenység naplójában lévő hibaüzenet nem hasznos a kiváltó ok azonosításában, engedélyezze a diagnosztikai naplókat, és használja Azure Monitor naplókat.

## <a name="send-diagnostics-to-azure-monitor-logs"></a>Diagnosztika küldése Azure Monitor naplókba

A diagnosztikai naplók bekapcsolásával és a Azure Monitor naplókba való elküldésével kifejezetten ajánlott. A diagnosztikai naplók alapértelmezés szerint **ki vannak kapcsolva** . A diagnosztikai naplók bekapcsolásához hajtsa végre a következő lépéseket:

1.  Jelentkezzen be a Azure Portalba, és navigáljon a Stream Analytics feladatokhoz. A **figyelés**területen válassza a **diagnosztikai naplók**lehetőséget. Ezután kattintson **a diagnosztika bekapcsolása**elemre.

    ![Panel navigációs elemei a diagnosztikai naplókhoz](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs-monitoring.png)  

2.  Hozzon létre egy **nevet** a **diagnosztikai beállítások** között, és jelölje be a **Küldés log Analyticsra**jelölőnégyzetet. Ezután vegyen fel egy meglévőt, vagy hozzon létre egy új **log Analytics-munkaterületet**. A **naplózás**területen keresse meg a **végrehajtás** és a **szerzői műveletek** jelölőnégyzetét, és **AllMetrics** a **metrika**területen. Kattintson a **Mentés** gombra. A további költségek elkerülése érdekében ajánlott Log Analytics munkaterületet használni ugyanabban az Azure-régióban, mint a Stream Analytics feladatot.

    ![Diagnosztikai naplók beállításai](./media/stream-analytics-job-diagnostic-logs/diagnostic-settings.png)

3. A Stream Analytics-feladatok elindításakor a rendszer a diagnosztikai naplókat a Log Analytics-munkaterületre irányítja. A feladathoz tartozó diagnosztikai naplók megtekintéséhez válassza a **figyelés** szakaszban a **naplók** lehetőséget.

   ![Diagnosztikai naplók a figyelés alatt](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs.png)

4. Stream Analytics olyan előre definiált lekérdezéseket biztosít, amelyek segítségével könnyedén megkeresheti a kívánt naplókat. A 3 kategória **általános**, **bemeneti adatokkal kapcsolatos hibákkal** és **kimeneti adatokkal kapcsolatos hibákat**tartalmaz. Ha például szeretné megtekinteni a feladatok összes hibájának összegzését az elmúlt 7 napban, válassza ki a megfelelő előre definiált lekérdezés **futtatását** . 

   ![Diagnosztikai naplók a figyelés alatt](./media/stream-analytics-job-diagnostic-logs/logs-categories.png)

   ![Naplók eredményei](./media/stream-analytics-job-diagnostic-logs/logs-result.png)

## <a name="diagnostics-log-categories"></a>Diagnosztikai naplók kategóriái

Azure Stream Analytics a diagnosztikai naplók két kategóriáját rögzíti:

* **Szerzői**műveletek: rögzíti a feladatok létrehozásához kapcsolódó műveleteket, például a feladatok létrehozását, a bemenetek és kimenetek hozzáadását és törlését, a lekérdezés hozzáadását és frissítését, valamint a feladatok elindítását és leállítását.

* **Végrehajtás**: rögzíti a feladatok végrehajtása során előforduló eseményeket.
    * Csatlakozási hibák
    * Adatfeldolgozási hibák, beleértve az alábbiakat:
        * Azok az események, amelyek nem felelnek meg a lekérdezés definíciójának (nem egyező mezőtípus és értékek, hiányzó mezők stb.)
        * Kifejezés-értékelési hibák
    * Egyéb események és hibák

## <a name="diagnostics-logs-schema"></a>Diagnosztikai naplók sémája

Az összes napló JSON formátumban van tárolva. Mindegyik bejegyzés a következő általános karakterlánc-mezőket tartalmazhatja:

Név | Leírás
------- | -------
time | A napló időbélyegzője (UTC).
resourceId | Annak az erőforrásnak az azonosítója, amelyre a művelet bekerült, nagybetűvel. Magában foglalja az előfizetés-azonosítót, az erőforráscsoportot és a feladatnév nevét. Például: **/Subscriptions/6503D296-DAC1-4449-9B03-609A1F4A1C87/RESOURCEGROUPS/My-Resource-Group/Providers/Microsoft. STREAMANALYTICS/STREAMINGJOBS/MYSTREAMINGJOB**.
category | Napló kategóriája, **végrehajtás** vagy **szerzői**művelet.
operationName | A naplózott művelet neve. Például az **események küldése: az SQL kimeneti írási hiba mysqloutput**.
status | A művelet állapota. Például sikertelen vagy **sikeres** **volt** .
szint | Naplózási szint. Például: **hiba**, **Figyelmeztetés**vagy **tájékoztató**.
properties | Naplóbejegyzés-specifikus részletek, JSON-karakterláncként szerializálva. További információkért tekintse meg a cikk következő részeit.

### <a name="execution-log-properties-schema"></a>Végrehajtási napló tulajdonságai sémája

A végrehajtási naplók információkkal rendelkeznek a Stream Analytics feladatok végrehajtása során történt eseményekről. A tulajdonságok sémája attól függően változik, hogy az esemény Adathiba vagy általános esemény-e.

### <a name="data-errors"></a>Adathibák

Minden olyan hiba, amely akkor fordul elő, amikor a feladatsor az adatok feldolgozását végzi. Ezek a naplók leggyakrabban az adatok olvasási, szerializálási és írási műveletei során jönnek létre. Ezek a naplók nem tartalmaznak csatlakozási hibákat. A csatlakozási hibákat általános eseményekként kezeli a rendszer. További információt a különböző [bemeneti és kimeneti adatokkal kapcsolatos hibák](https://docs.microsoft.com/azure/stream-analytics/data-errors)okairól itt talál.

Név | Leírás
------- | -------
Forrás | A feladathoz tartozó bemenet vagy kimenet neve, ahol a hiba történt.
Üzenet | A hibához tartozó üzenet.
Type (Típus) | A hiba típusa. Például: **DataConversionError**, **CsvParserError**vagy **ServiceBusPropertyColumnMissingError**.
Adatok | Olyan információkat tartalmaz, amelyek hasznosak a hiba forrásának pontos megtalálásához. A mérettől függően a csonkítás függ.

A **operationName** értékétől függően az adathibák a következő sémával rendelkeznek:

* Az **események szerializálása** az esemény-olvasási műveletek során következik be. Akkor fordulnak elő, ha a bemeneti adatok nem felelnek meg a lekérdezési sémának az alábbi okok valamelyike miatt:

   * A következő *típus nem egyezik az esemény (de) szerializálásakor*: a hibát okozó mezőt azonosítja.

   * *Nem lehet olvasni egy eseményt, érvénytelen szerializálás*: felsorolja a bemeneti adatok azon helyével kapcsolatos információkat, ahol a hiba történt. Tartalmazza a Blobok bemenetének, eltolásának és az adatok mintájának a nevét.

* Az **események küldése** az írási műveletek során történik. A hibát okozó folyamatos átviteli eseményt azonosítják.

### <a name="generic-events"></a>Általános események

Az általános események minden mást lefedik.

Név | Leírás
-------- | --------
Hiba | választható Hiba adatai. Általában ez a kivételi információ, ha elérhető.
Üzenet| A naplózási üzenet.
Type (Típus) | Az üzenet típusa. Leképezés a hibák belső kategorizálására. Például: **JobValidationError** vagy **BlobOutputAdapterInitializationFailure**.
Korrelációs azonosító | [GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier) , amely egyedileg azonosítja a feladatok végrehajtását. Az összes végrehajtási naplóbejegyzés abban az időponttól kezdve, amíg a feladatoknak nem kell megegyezniük a **korrelációs azonosító** értékével.

## <a name="next-steps"></a>Következő lépések

* [Bevezetés a Stream Analyticsba](stream-analytics-introduction.md)
* [Ismerkedés a Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Stream Analytics feladatok méretezése](stream-analytics-scale-jobs.md)
* [Stream Analytics lekérdezés nyelvi referenciája](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [AdatStream Analyticsi hibák](https://docs.microsoft.com/azure/stream-analytics/data-errors)
