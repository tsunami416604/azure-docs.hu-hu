---
title: Diagnosztikai naplók segítségével az Azure Stream Analytics hibáinak elhárítása
description: Ez a cikk ismerteti az Azure Stream Analytics diagnosztikai naplóinak elemzése.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: db3c9874676e3240f6896c1e1ff8f873360c20d5
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53090822"
---
# <a name="troubleshoot-azure-stream-analytics-by-using-diagnostics-logs"></a>Az Azure Stream Analytics hibáinak elhárítása a diagnosztikai naplók használatával

Néha előfordul Azure Stream Analytics-feladat váratlanul feldolgozása leáll. Fontos tudni az ilyen esemény hibaelhárítása. Az esemény-lekérdezés váratlan eredményt, az eszközök kapcsolat, vagy egy nem várt szolgáltatáskiesés okozhatta. A diagnosztikai naplók a Stream Analytics segítségével problémák okának azonosításához, amikor azok bekövetkeznek, és a helyreállítási idő csökkentése.

## <a name="log-types"></a>Napló típusa

Stream Analytics naplók két típusú kínálja: 
* [A Tevékenységnaplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) (mindig bekapcsolt). Tevékenységnaplók feladatok végrehajtott műveletek betekintést biztosítanak.
* [Diagnosztikai naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) (konfigurálható). Diagnosztikai naplók mindent, ami fordul elő, ha egy feladat részletesebb betekintést nyújtson. Diagnosztikai naplók kezdje, ha a feladat jön létre, és a záró, a feladat törlése. Ha a feladat frissül és a futás közben események terjed ki.

> [!NOTE]
> Szolgáltatások, például az Azure Storage, az Azure Event Hubs és az Azure Log Analytics segítségével elemezheti a nem megfelelő adatokat. Ezek a szolgáltatások díjszabási modellje alapján lesznek kiszámlázva.
>

## <a name="turn-on-diagnostics-logs"></a>Kapcsolja be a diagnosztikai naplók

Diagnosztikai naplók **ki** alapértelmezés szerint. Diagnosztikai naplók bekapcsolásához hajtsa végre ezeket a lépéseket:

1.  Jelentkezzen be az Azure Portalon, és nyissa meg a streamelési feladat panelen. A **figyelés**válassza **diagnosztikai naplók**.

    ![Panel Navigálás a diagnosztikai naplók](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs-monitoring.png)  

2.  Válassza ki **diagnosztika bekapcsolása**.

    ![Kapcsolja be a Stream Analytics-diagnosztikai naplók](./media/stream-analytics-job-diagnostic-logs/turn-on-diagnostic-logs.png)

3.  Az a **diagnosztikai beállítások** lapon a **állapota**válassza **a**.

    ![A diagnosztikai naplókhoz állapotának módosítása](./media/stream-analytics-job-diagnostic-logs/save-diagnostic-log-settings.png)

4.  Az archiválási target (storage-fiók, event hub, a Log Analytics), amelyeket szeretne beállítani. Ezután válassza ki a gyűjtendő naplók kategóriáit (a végrehajtási, a szerzői műveletek). 

5.  Mentse az új diagnosztikai konfigurációt.

Diagnosztikai konfigurációja nagyjából 10 percet vesz igénybe érvénybe léptetéséhez. Ezt követően a naplók start jelennek meg a beállított archiválási célzott (tekintheti meg ezeket a **diagnosztikai naplók** oldal):

![Panel Navigálás a diagnosztikai naplók - archiválási célok](./media/stream-analytics-job-diagnostic-logs/view-diagnostic-logs-page.png)

Konfiguruje se diagnostika kapcsolatos további információkért lásd: [gyűjtése és a diagnosztika az Azure-erőforrások felhasználását](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).

## <a name="diagnostics-log-categories"></a>Diagnosztikai naplója kategóriák

Jelenleg a Microsoft két kategóriába diagnosztikai naplók rögzítése:

* **Szerzői**. Rögzíti a szerzői műveletek feladathoz kapcsolódó eseményeket naplózása: a feladat létrehozása, hozzáadása és törlése bemenetként, és kiírja, hozzáadása és a lekérdezés frissítése elindítása és leállítása a feladatot.
* **Végrehajtási**. Rögzíti a feladat végrehajtása során előforduló események:
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
properties | Napló bejegyzés-specifikus részletei, szerializált JSON-karakterláncot. További információkért tekintse meg a következő szakaszok.

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
    * *Adattípus-eltérés (Németország) esemény során szerializálni*: a mező a hibát okozó azonosítja.
    * *Nem olvasható egy esemény, érvénytelen szerializálási*: információt tartalmaz a bemeneti adatokat a hely, ahol a hiba történt. Blob bemeneti eltolás és a egy minta az adatok a blob nevét tartalmazza.
* **Események küldése**. Írási műveletek során bekövetkező események küldése A streamelési eseményt a hibát okozó azonosítsa azokat.

### <a name="generic-events"></a>Általános események

Általános események terjed ki minden mást.

Name (Név) | Leírás
-------- | --------
Hiba | (nem kötelező) Hiba adatok. Általában Ha ez a kivétel információ érhető el.
Üzenet| A fenti üzenet jelenik meg.
Típus | Üzenet típusa. Hibák kategorizálása belső leképezések. Ha például **JobValidationError** vagy **BlobOutputAdapterInitializationFailure**.
Korrelációs azonosító | [GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier) , amely egyedileg azonosítja a feladat végrehajtása. Az összes végrehajtás naplóbejegyzések kezdve a feladat elindul mindaddig, amíg a feladat leáll rendelkezik azonos **korrelációs azonosító** értéket.

## <a name="next-steps"></a>További lépések

* [Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Stream Analytics használatának első lépései](stream-analytics-real-time-fraud-detection.md)
* [Stream Analytics-feladatok méretezése](stream-analytics-scale-jobs.md)
* [Stream Analytics lekérdezési nyelv leírása](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Stream Analytics felügyeleti REST API-referencia](https://msdn.microsoft.com/library/azure/dn835031.aspx)
