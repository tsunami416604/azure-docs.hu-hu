---
title: Azure Stream Analytics diagnosztikai naplók segítségével hibaelhárítása
description: Ez a cikk ismerteti az Azure Stream Analytics-diagnosztikai naplók elemzése.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/20/2017
ms.openlocfilehash: 9001a2962806ee3e691fa448dde162d12c6ecdd2
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="troubleshoot-azure-stream-analytics-by-using-diagnostics-logs"></a>Azure Stream Analytics hibaelhárítás diagnosztikai naplók segítségével

Egy Azure Stream Analytics-feladat alkalmanként váratlanul feldolgozása leáll. Fontos tudni hibáinak elhárítása az ilyen esemény. Az esemény oka lehet egy nem várt lekérdezés eredménye, és eszközöket, vagy egy nem várt szolgáltatáskiesés. A diagnosztikai naplók, a Stream Analytics segítségével, azonosíthatja az okot, problémák, amikor fordulhat elő, és a helyreállítási idő csökkentése.

## <a name="log-types"></a>Napló típusa

A Stream Analytics kétféle típusú naplókat kínál: 
* [Tevékenységi naplóit](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) (always on). Tevékenységi naplóit osszon ki feladatokat végre műveletek betekintést.
* [Diagnosztikai naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) (konfigurálható). Diagnosztikai naplók betekintést gazdagabb mindent, ami egy feladattal történik. Diagnosztikai naplók kezdő, ha a feladat jön létre, és a záró a projekt törlésekor. Amikor a feladat frissül, és futás közbeni események terjed ki.

> [!NOTE]
> Szolgáltatások például az Azure tárolás, az Azure Event Hubs és az Azure Log Analytics segítségével elemezheti a nem megfelelő adatokat. Ezek a szolgáltatások árképzési modelljével alapján van szó.
>

## <a name="turn-on-diagnostics-logs"></a>Kapcsolja be a diagnosztikai naplók

Diagnosztikai naplók **ki** alapértelmezés szerint. Kapcsolja be a diagnosztikai naplók, végezze el az alábbi lépéseket:

1.  Jelentkezzen be az Azure-portálon, és nyissa meg a folyamatos átviteli feladat paneljét. A **figyelés**, jelölje be **diagnosztikai naplók**.

    ![A diagnosztika naplókhoz panel navigációs](./media/stream-analytics-job-diagnostic-logs/image1.png)  

2.  Válassza ki **a diagnosztika bekapcsolásához**.

    ![Kapcsolja be a diagnosztikai naplók](./media/stream-analytics-job-diagnostic-logs/image2.png)

3.  Az a **diagnosztikai beállítások** lap, a **állapot**, jelölje be **a**.

    ![A diagnosztikai naplók állapotának módosítása](./media/stream-analytics-job-diagnostic-logs/image3.png)

4.  Az archiválási cél (tárfiók, az event hubs, Log Analytics), amelyet beállítása. Ezután válassza ki a naplófájlokat, amelyek szeretne gyűjteni kategóriáinak (a végrehajtási, a szerzői műveletek). 

5.  Az új diagnosztikai konfiguráció mentéséhez.

A diagnosztikai konfigurációja körülbelül 10 percig érvénybe vesz igénybe. Ezt követően a naplók start jelennek meg a beállított archiválási cél (a megtekintheti a **diagnosztikai naplók** lap):

![A diagnosztika naplókhoz - archiválási célok panel navigációs](./media/stream-analytics-job-diagnostic-logs/image4.png)

Diagnosztika konfigurálásával kapcsolatos további információkért lásd: [gyűjtése és diagnosztikai adatokat az Azure-erőforrások felhasználását](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).

## <a name="diagnostics-log-categories"></a>A diagnosztikai naplófájlok kategóriák

Jelenleg nem rögzíteni diagnosztikai naplók két csoportja:

* **Szerzői**. Rögzíti a naplóesemények kapcsolatos szerzői műveletek feladat: a feladat létrehozása, hozzáadása és törlése a bemeneti és kimeneti, hozzáadása és a lekérdezés frissítése indítása és leállítása a feladatot.
* **Végrehajtási**. Feladat végrehajtása során előforduló eseményeket rögzíti:
    * Kapcsolódási hibák
    * Adatok feldolgozása hibák, beleértve:
        * Az eseményeket, amelyek nem felelnek meg a lekérdezés definíciója (nem egyező típusú és értékek, hiányzó mezőket, és így tovább)
        * Kifejezés kiértékelése hibák
    * Egyéb kapcsolódó események és hibák

## <a name="diagnostics-logs-schema"></a>Diagnosztikai naplók séma

Összes napló JSON formátumban vannak tárolva. Mindegyik bejegyzés rendelkezik a következő általános karakterlánc mezők:

Name (Név) | Leírás
------- | -------
time | Időbélyeg (UTC szerint) a napló.
resourceId | Az, hogy a művelet került sor, nagybetűvel erőforrás-azonosítója. Ez magában foglalja az előfizetés-azonosító, az erőforráscsoport és a feladat nevét. Például   **/SUBSCRIPTIONS/6503D296-DAC1-4449-9B03-609A1F4A1C87/RESOURCEGROUPS/MY-RESOURCE-GROUP/PROVIDERS/MICROSOFT. STREAMANALYTICS/STREAMINGJOBS/MYSTREAMINGJOB**.
category | Kategória, vagy jelentkezzen **végrehajtási** vagy **szerzői műveletek**.
operationName | A művelet naplózott neve. Például **események küldése: SQL kimeneti hiba írni mysqloutput**.
status | A művelet állapotát. Például **sikertelen** vagy **sikeres**.
szint | Naplózási szint. Például **hiba**, **figyelmeztetés**, vagy **tájékoztató**.
properties | Napló bejegyzés-specifikus részletei, szerializálható kézjegyként JSON karakterláncnak. További információ a következő szakaszban talál.

### <a name="execution-log-properties-schema"></a>Végrehajtási tulajdonságok séma

A feladatvégrehajtási naplók rendelkezik eseményekről, amelyek a Stream Analytics-feladat végrehajtása során történt. A séma tulajdonságok attól függően változik, az esemény típusát. Jelenleg a következő típusú feladatvégrehajtási naplók van:

### <a name="data-errors"></a>Adatok hibák

A feladat adatainak feldolgozása során előforduló hiba van a kategória a naplók. Ezek a naplók leggyakrabban, beolvasott adatok szerializálása, során jönnek létre, és írási műveletek. Ezek a naplók nem tartalmaznak kapcsolódási hibák. Kapcsolódási hibák általános események tekintendők.

Name (Név) | Leírás
------- | -------
Forrás | A feladat neve bemeneti vagy kimeneti, ahol a hiba történt.
Üzenet | A hibával kapcsolatos üzenet.
Típus | Hiba típusa. Például **DataConversionError**, **CsvParserError**, vagy **ServiceBusPropertyColumnMissingError**.
Adatok | Pontosan keresse meg a hiba okáról hasznos adatot tartalmaz. Hatálya csonkolása méretétől függően.

Attól függően a **operationName** érték adatok hibák sémája a következő:
* **Események szerializálni**. Szerializálható esemény olvasási műveletek során bekövetkező események. Jelentkeznek, amikor a bemeneti adatok nem felel meg a lekérdezés sémája az alábbi okok valamelyike miatt:
    * *Típuseltérés (de) esemény során szerializálni*: a mező a hibát okozó azonosítja.
    * *Nem olvasható be az esemény, érvénytelen szerializálási*: Ha a hiba történt a bemeneti adatok helyét sorolja fel. A blob bemeneti, eltolás és egy minta az adatok blob neve tartalmazza.
* **Események küldése**. Az írási műveletek során bekövetkező események küldése Azok a hibát okozó adatfolyam esemény azonosításához.

### <a name="generic-events"></a>Általános események

Általános események fedik le minden más.

Name (Név) | Leírás
-------- | --------
Hiba | (választható) Hiba adatok. Általában ami kivételek adatai, amennyiben az rendelkezésre áll.
Üzenet| A fenti üzenet jelenik meg.
Típus | Üzenet típusa. A hibák belső kategorizálási leképezések. Például **JobValidationError** vagy **BlobOutputAdapterInitializationFailure**.
Korrelációs azonosító | [GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier) , amely egyedileg azonosítja a feladat végrehajtása. Az összes végrehajtási naplóbejegyzések abból az időből, amikor a feladat elindul mindaddig, amíg a feladat már van azonos **korrelációs azonosító** érték.

## <a name="next-steps"></a>További lépések

* [A Stream Analytics bemutatása](stream-analytics-introduction.md)
* [A Stream Analytics használatába](stream-analytics-real-time-fraud-detection.md)
* [Stream Analytics-feladatok méretezése](stream-analytics-scale-jobs.md)
* [Stream Analytics lekérdezési nyelvi referencia](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [A Stream Analytics felügyeleti REST API-referencia](https://msdn.microsoft.com/library/azure/dn835031.aspx)
