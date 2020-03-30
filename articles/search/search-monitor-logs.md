---
title: Naplóadatok gyűjtése
titleSuffix: Azure Cognitive Search
description: Naplóadatok gyűjtése és elemzése egy diagnosztikai beállítás engedélyezésével, majd a Kusto lekérdezési nyelv segítségével az eredmények feltárása.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: 86e869bc08552ea11728c508486a4784eccf4042
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462358"
---
# <a name="collect-and-analyze-log-data-for-azure-cognitive-search"></a>Naplóadatok gyűjtése és elemzése az Azure Cognitive Search szolgáltatáshoz

A diagnosztikai vagy üzemeltetési naplók betekintést nyújtanak az Azure Cognitive Search részletes műveleteibe, és hasznosak a szolgáltatás és a számítási feladatok folyamatainak figyeléséhez. Belsőleg naplók léteznek a háttérrendszer egy rövid ideig, elegendő a vizsgálat és elemzés, ha a fájl egy támogatási jegyet. Ha azonban önirányítást szeretne a működési adatokkal szemben, konfigurálnia kell egy diagnosztikai beállítást, hogy megadja a naplózási adatok gyűjtésének helyét.

A naplók beállítása a diagnosztika és a működési előzmények megőrzése esetén hasznos. A naplózás engedélyezése után lekérdezéseket futtathat, vagy jelentéseket készíthet strukturált elemzésre.

Az alábbi táblázat az adatok gyűjtésére és megőrzésére vonatkozó lehetőségeket sorolja fel.

| Erőforrás | Alkalmazási cél |
|----------|----------|
| [Küldés a Log Analytics-munkaterületre](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-resource-logs) | Az események és a metrikák egy Log Analytics-munkaterületre kerülnek, amely lekérdezhető a portálon a részletes információk visszaadása érdekében. Bevezetés: Az [Azure Monitor naplóinak első lépései című témakörben](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-viewdata) |
| [Archiválás a Blob tárházával](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) | Az események és a metrikák egy Blob-tárolóba vannak archiválva, és JSON-fájlokban tárolódnak. Naplók lehet elég szemcsés (az óra / perc), hasznos egy adott esemény kutatásához, de nem a nyílt végű vizsgálat. A JSON-szerkesztővel nyers naplófájlt vagy Power BI-t tekinthet meg a naplóadatok összesítéséhez és megjelenítéséhez.|
| [Streamelés az Eseményközpontba](https://docs.microsoft.com/azure/event-hubs/) | Az események és a metrikák streamelése egy Azure Event Hubs szolgáltatásba. Válassza ezt a lehetőséget, mint egy alternatív adatgyűjtési szolgáltatás nagyon nagy naplók. |

Az Azure Monitor-naplók és a Blob-tárhely is ingyenes szolgáltatásként érhető el, így az Azure-előfizetés teljes élettartama alatt díjmentesen kipróbálhatja. Az Application Insights szabadon regisztrálhat, és mindaddig használható, amíg az alkalmazásadatok mérete bizonyos korlátok alatt van (a részleteket lásd az [árképzési oldalon).](https://azure.microsoft.com/pricing/details/monitor/)

## <a name="prerequisites"></a>Előfeltételek

Ha a Log Analytics vagy az Azure Storage használatával, erőforrásokat hozhat létre előre.

+ [Naplóelemzési munkaterület létrehozása](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)

+ [Tárfiók létrehozása](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)

## <a name="enable-data-collection"></a>Az adatgyűjtés engedélyezése

A diagnosztikai beállítások határozzák meg a naplózott események és metrikák gyűjtésének módját.

1. A **Monitorozás** területen kattintson a **Diagnosztikai beállítások** elemre.

   ![Diagnosztikai beállítások](./media/search-monitor-usage/diagnostic-settings.png "Diagnosztikai beállítások")

1. Válassza **a + Diagnosztikai beállítás hozzáadása lehetőséget**

1. Jelölje be **a Log Analytics**jelölőnégyzetet, válassza ki a munkaterületet, és válassza az **OperationLogs** and **AllMetrics lehetőséget.**

   ![Adatgyűjtés konfigurálása](./media/search-monitor-usage/configure-storage.png "Adatgyűjtés konfigurálása")

1. Mentse a beállítást.

1. A naplózás engedélyezése után a keresési szolgáltatás segítségével indítsa el a naplók és metrikák generálását. Időbe telik, amíg a naplózott események és mérőszámok elérhetővé válnak.

A Log Analytics esetében néhány percig tart, amíg az adatok elérhetővé válikk, majd futtathatja a Kusto-lekérdezéseket az adatok visszaadásához. További információt a [Lekérdezéskérések figyelése](search-monitor-logs.md)című témakörben talál.

Blob storage esetén egy órát vesz igénybe, mielőtt a tárolók megjelennek a Blob storage-ban. Tárolónként óránként egy blob van. A tárolók csak akkor jönnek létre, ha van naplózandó vagy mérhető tevékenység. Amikor az adatokat egy tárfiókba másolja, az adatok JSON-ként lesznek formázva, és két tárolóba kerülnek:

+ insights-logs-operationlogs: keresési forgalmi naplók
+ insights-metrics-pt1m: mérőszámokhoz

## <a name="query-log-information"></a>Lekérdezési napló adatai

A diagnosztikai naplókban két tábla tartalmazza az Azure Cognitive Search naplóit és metrikákat: **Az AzureDiagnostics** és az **AzureMetrics.**

1. A **Figyelés**csoportban válassza **a Naplók**lehetőséget.

1. Adja meg az **AzureMetrics** a lekérdezési ablakban. Futtassa ezt az egyszerű lekérdezést, hogy megismerkedjen a táblában gyűjtött adatokkal. Görgessen végig a táblázaton a mutatók és értékek megtekintéséhez. Figyelje meg a rekordok számát a tetején, és ha a szolgáltatás már egy ideje mérőszámokat gyűjt, érdemes lehet módosítani az időintervallumot, hogy egy kezelhető adatkészletet kapjon.

   ![AzureMetrics tábla](./media/search-monitor-usage/azuremetrics-table.png "AzureMetrics tábla")

1. Táblázatos eredményhalmaz tabularis eredményhalmazának visszaadásához írja be a következő lekérdezést.

   ```
   AzureMetrics
    | project MetricName, Total, Count, Maximum, Minimum, Average
   ```

1. Ismételje meg az előző lépéseket, kezdve **az AzureDiagnostics** az összes oszlop ot ad vissza tájékoztató célokra, majd egy szelektívebb lekérdezést, amely kinyeri az érdekesebb információkat.

   ```
   AzureDiagnostics
   | project OperationName, resultSignature_d, DurationMs, Query_s, Documents_d, IndexName_s
   | where OperationName == "Query.Search" 
   ```

   ![AzureDiagnostics tábla](./media/search-monitor-usage/azurediagnostics-table.png "AzureDiagnostics tábla")

## <a name="log-schema"></a>Naplóséma

Az Azure Cognitive Search naplóadatait tartalmazó adatstruktúrák megfelelnek az alábbi sémának. 

A Blob storage esetében minden blob rendelkezik egy gyökérobjektummal, amelyet naplóobjektumok tömbjét tartalmazó **rekordoknak** neveznek. Minden blob tartalmazza az ugyanabban az órában végzett összes művelet rekordjait.

Az alábbi táblázat a diagnosztikai naplózás közös mezőinek részleges listáját tartalmazza.

| Név | Típus | Példa | Megjegyzések |
| --- | --- | --- | --- |
| időgenerálva |dátum/idő |"2018-12-07T00:00:43.6872559Z" |A művelet időbélyege |
| resourceId |sztring |"/ELŐFIZETÉSEK/11111111-1111-1111-1111-1111111111/<br/>ERŐFORRÁSCSOPORTOK/ALAPÉRTELMEZETT/SZOLGÁLTATÓK/<br/> Microsoft. KERESÉS/SEARCHSERVICES/SEARCHSERVICE" |A forrásazonosító |
| operationName |sztring |"Lekérdezés.Keresés" |A művelet neve |
| operationVersion |sztring |"2019-05-06" |A használt api-verzió |
| category |sztring |"OperationLogs" |Állandó |
| resultType |sztring |"Siker" |Lehetséges értékek: Sikeres vagy sikertelen |
| resultSignature |int |200 |HTTP eredménykód |
| durationMS |int |50 |A művelet időtartama ezredmásodpercben |
| properties |objektum |lásd az alábbi táblázatot |Műveletspecifikus adatokat tartalmazó objektum |

### <a name="properties-schema"></a>Tulajdonságok sémája

Az alábbi tulajdonságok az Azure Cognitive Search-re jellemzőek.

| Név | Típus | Példa | Megjegyzések |
| --- | --- | --- | --- |
| Description_s |sztring |"GET /indexes('content')/docs" |A művelet végpontja |
| Documents_d |int |42 |Feldolgozott dokumentumok száma |
| IndexName_s |sztring |"teszt-index" |A művelethez társított index neve |
| Query_s |sztring |"?search=AzureSearch&$count=true&api-version=2019-05-06" |A lekérdezés paraméterei |

## <a name="metrics-schema"></a>Metrikák sémája

Metrikák a lekérdezési kérelmek rögzítése és mérése egy perces időközönként. Minden metrika elérhetővé teszi a minimális, maximális és átlagos percenkénti értékeket. További információt a [Lekérdezéskérések figyelése](search-monitor-queries.md)című témakörben talál.

| Név | Típus | Példa | Megjegyzések |
| --- | --- | --- | --- |
| resourceId |sztring |"/ELŐFIZETÉSEK/11111111-1111-1111-1111-1111111111/<br/>ERŐFORRÁSCSOPORTOK/ALAPÉRTELMEZETT/SZOLGÁLTATÓK/<br/>Microsoft. KERESÉS/SEARCHSERVICES/SEARCHSERVICE" |erőforrásazonosítója |
| metricName |sztring |"Látencia" |a mérőszám neve |
| time |dátum/idő |"2018-12-07T00:00:43.6872559Z" |a művelet időbélyege |
| átlag |int |64 |A nyers minták átlagos értéke a metrikai időintervallumban, egységek másodpercben vagy százalékban, a metrikától függően. |
| minimum |int |37 |A nyers minták minimális értéke a metrikus időintervallumban, másodpercben. |
| maximum |int |78 |A nyers minták maximális értéke a metrikus időintervallumban, másodpercben.  |
| összesen |int |258 |A nyers minták teljes értéke a metrikai időintervallumban, másodpercben.  |
| count |int |4 |A csomópont által a naplóba egy percen belül kibocsátott metrikák száma.  |
| időmés |sztring |"PT1M" |A metrika időgabonája az ISO 8601-ben. |

Gyakori, hogy a lekérdezések ezredmásodpercben futnak, így csak a másodpercként mérhető lekérdezések jelennek meg a qps-hez hasonló metrikában.

A **keresési lekérdezések másodpercenként** metrika, minimum a legalacsonyabb érték a keresési lekérdezések másodpercenként, hogy az adott percben regisztrált. Ugyanez vonatkozik a maximális értékre is. Átlagos, az összesített az egész percben. Egy percen belül például előfordulhat, hogy egy ilyen minta: egy másodperc a nagy terhelés, amely a maximális SearchQueriesPerSecond, majd 58 másodperc átlagos terhelés, és végül egy másodperc csak egy lekérdezés, amely a minimális.

A **szabályozott keresési lekérdezések százaléka,** minimális, maximális, átlagos és összes, mind ugyanazt az értéket: a százalékos keresési lekérdezések, amelyek szabályozták, a keresési lekérdezések teljes száma egy perc alatt.

## <a name="view-raw-log-files"></a>Nyers naplófájlok megtekintése

A Blob storage a naplófájlok archiválására szolgál. A naplófájl megtekintéséhez bármelyik JSON-szerkesztőt használhatja. Ha még nem rendelkezik ilyen, javasoljuk a [Visual Studio Code](https://code.visualstudio.com/download).

1. Az Azure Portalon nyissa meg a Storage-fiókját. 

2. A bal oldali navigációs ablakban kattintson a **Blobs gombra.** Meg kell jelennie **insights-logs-operationlogs** és **insights-metrics-pt1m**. Ezeket a tárolókat az Azure Cognitive Search hozta létre, amikor a naplóadatok blobtárolóba kerülnek.

3. Kattintson lefelé a mappahierarchiában, amíg el nem éri a .json fájlt.  A helyi menü segítségével töltse le a fájlt.

Miután a fájlt letöltötte, nyissa meg egy JSON szerkesztőben a tartalom megtekintéséhez.

## <a name="next-steps"></a>További lépések

Ha még nem tette meg, tekintse át a keresési szolgáltatások figyelésének alapjait, és ismerje meg a felügyeleti képességek teljes skáláját.

> [!div class="nextstepaction"]
> [Műveletek és tevékenységek figyelése az Azure Cognitive Search szolgáltatásban](search-monitor-usage.md)