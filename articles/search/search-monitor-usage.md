---
title: Az erőforrás-használat és a lekérdezési metrikák figyelése egy keresési szolgáltatáshoz – Azure Search
description: Naplózás engedélyezése, lekérdezési tevékenység metrikáinak beolvasása, erőforrás-használat és egyéb rendszeradatok egy Azure Search szolgáltatásból.
author: HeidiSteen
manager: nitinme
tags: azure-portal
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: heidist
ms.openlocfilehash: fe8061f8e99742f9dc5c1181235c4203aaad82ca
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/21/2019
ms.locfileid: "72331216"
---
# <a name="monitor-resource-consumption-and-query-activity-in-azure-search"></a>Az erőforrás-felhasználás és a lekérdezési tevékenységek figyelése Azure Search

A Azure Search szolgáltatás áttekintés lapján megtekintheti az erőforrás-használattal, a lekérdezési metrikákkal, valamint a további indexek, indexelő és adatforrások létrehozásához rendelkezésre álló kvóta mennyiségét. A portál használatával is konfigurálhatja a log Analytics szolgáltatást, illetve az állandó adatgyűjtéshez használt más erőforrásokat. 

A naplók beállítása az öndiagnosztika és a működési Előzmények megőrzése esetén hasznos. Belsőleg a naplófájlok a háttérben, rövid idő alatt léteznek, elegendő a vizsgálathoz és az elemzéshez, ha támogatási jegyet ad. Ha szabályozni szeretné a naplózási adatokat, és hozzáférést szeretne biztosítani a naplófájlhoz, akkor a jelen cikkben ismertetett megoldások egyikét kell beállítania.

Ebben a cikkben megismerheti a figyelési lehetőségeit, a naplózási és naplózási műveletek engedélyezését, valamint a napló tartalmának megtekintését.

## <a name="metrics-at-a-glance"></a>Mérőszámok áttekintése

Az Áttekintés lapon beépített **használati** és **figyelési** szakaszt az erőforrás-felhasználással és a lekérdezés-végrehajtási metrikákkal kapcsolatos jelentésekben találja. Ezek az adatok a szolgáltatás használatának megkezdése után azonnal elérhetővé válnak, és nincs szükség konfigurációra. Ez az oldal néhány percenként frissül. Ha az éles számítási feladatokhoz [használt szintet](search-sku-tier.md)véglegesíti, vagy ha [módosítani szeretné az aktív replikák és partíciók számát](search-capacity-planning.md), akkor ezek a metrikák az erőforrások felhasználásának gyakoriságát mutatják be. és milyen jól kezeli a jelenlegi konfiguráció a meglévő terhelést.

A **használat** lapon az erőforrások rendelkezésre állása az aktuális [határértékekhez](search-limits-quotas-capacity.md)viszonyítva jelenik meg. Az alábbi ábra az ingyenes szolgáltatás, amely az egyes típusok 3 objektumára, illetve 50 MB tárterületre van korlátozva. Egy alapszintű vagy standard szolgáltatás magasabb korláttal rendelkezik, és ha megnöveli a partíciók számát, a maximális tárterület arányosan növekszik.

![A használati állapot a hatályos határértékekhez képest](./media/search-monitor-usage/usage-tab.png
 "A használati állapot a hatályos határértékekhez képest")

## <a name="queries-per-second-qps-and-other-metrics"></a>Másodpercenkénti lekérdezések (QPS) és egyéb mérőszámok

A **figyelés** lapon a percenként összesített mérőszámok (például keresési *lekérdezések másodpercenkénti száma* (QPS)) mozgóátlagai láthatók. 
A *keresési késés* azt az időtartamot, ameddig a keresési szolgáltatás a keresési lekérdezések feldolgozásához szükséges, percenként összesítve. A *szabályozott keresési lekérdezések százalékos aránya* (nem látható) a szabályozott keresési lekérdezések százalékos aránya, percenként összesítve.

Ezek a számok közelítve jelennek meg, és arra szolgálnak, hogy a rendszer milyen jól szolgálja ki a karbantartási kérelmeket. A tényleges QPS lehet magasabb vagy alacsonyabb, mint a portálon jelentett szám.

![Lekérdezések másodpercenkénti tevékenysége](./media/search-monitor-usage/monitoring-tab.png "Lekérdezések másodpercenkénti tevékenysége")

## <a name="activity-logs"></a>Tevékenységnaplók

A **tevékenység naplója** adatokat gyűjt a Azure Resource Managerból. Példák a tevékenység naplójában található információkra: szolgáltatás létrehozása vagy törlése, erőforráscsoport frissítése, a név rendelkezésre állásának ellenőrzése, vagy egy szolgáltatás-hozzáférési kulcs beszerzése a kérelmek kezeléséhez. 

A **tevékenység naplóját** a bal oldali navigációs ablaktáblán, vagy a felső ablak parancssáv vagy a **problémák diagnosztizálása és megoldása** oldalon található értesítések közül lehet elérni.

Az olyan szolgáltatáson belüli feladatokhoz, mint például az index létrehozása vagy egy adatforrás törlése, az egyes kérelmekhez hasonló általános értesítések jelennek meg, például a "rendszergazdai kulcs beolvasása", de nem az adott művelet. Ezen szintű információk esetében engedélyeznie kell egy kiegészítő figyelési megoldást.

## <a name="add-on-monitoring-solutions"></a>Kiegészítő figyelési megoldások

Azure Search nem tárol semmilyen, az általa kezelt objektumon túli adattárolást, ami azt jelenti, hogy a naplózási adatforrásokat külsőleg kell tárolni. Az alábbi erőforrások bármelyikét megadhatja, ha szeretné megőrizni a naplózási adatmennyiséget. 

A következő táblázat összehasonlítja a naplók tárolásának lehetőségeit, és részletesen figyeli a szolgáltatási műveleteket és a lekérdezési feladatokat a Application Insightson keresztül.

| Erőforrás | Alkalmazási cél |
|----------|----------|
| [Azure Monitor-naplók](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview) | Naplózott események és lekérdezések mérőszámai az alábbi sémák alapján. Az események naplózása egy Log Analytics munkaterületre történik. Futtathat lekérdezéseket egy munkaterületen, ha részletes információkat ad vissza a naplóból. További információ: Ismerkedés [a Azure monitor-naplókkal](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-viewdata) |
| [Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) | Naplózott események és lekérdezések mérőszámai az alábbi sémák alapján. Az események egy blob-tárolóba kerülnek, és JSON-fájlokban tárolódnak. A fájl tartalmának megtekintéséhez használjon JSON-szerkesztőt.|
| [Event Hub](https://docs.microsoft.com/azure/event-hubs/) | A naplózott események és a lekérdezési metrikák a cikkben leírt sémák alapján. Válassza ezt alternatív adatgyűjtési szolgáltatásként a nagyon nagy naplók számára. |

A Azure Monitor naplók és a blob Storage is ingyenes szolgáltatásként érhető el, így ingyenesen kipróbálható az Azure-előfizetés élettartama. Application Insights ingyenesen regisztrálhatók és használhatók, amíg az alkalmazás adatainak mérete bizonyos korlátok között van (a részletekért tekintse meg a [díjszabási oldalt](https://azure.microsoft.com/pricing/details/monitor/) ).

A következő szakasz végigvezeti az Azure Blob Storage engedélyezésének és használatának lépésein a Azure Search műveletek által létrehozott naplózási adatok gyűjtéséhez és eléréséhez.

## <a name="enable-logging"></a>Naplózás engedélyezése

Az indexelési és lekérdezési számítási feladatok naplózása alapértelmezés szerint ki van kapcsolva, és a naplózási infrastruktúra és a hosszú távú külső tárolók kiegészítő megoldásaitól függ. Önmagában az egyetlen megőrzött adatAzure Search a létrehozott és felügyelt objektumok, ezért a naplókat máshol kell tárolni.

Ez a szakasz azt ismerteti, hogyan használható a blob Storage a naplózott események és mérőszámok adatok tárolására.

1. [Hozzon létre egy Storage-fiókot](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) , ha még nem rendelkezik ilyennel. Azt is megteheti, hogy ugyanabba az erőforráscsoporthoz helyezi, mint a Azure Search a tisztítást később, ha törölni szeretné az ebben a gyakorlatban használt összes erőforrást.

   A Storage-fióknak ugyanabban a régióban kell lennie, mint Azure Search.

2. Nyissa meg a keresési szolgáltatás áttekintés lapját. A bal oldali navigációs panelen görgessen lefelé a **figyelés** elemre, és kattintson a **figyelés engedélyezése**lehetőségre.

   ![Figyelés engedélyezése](./media/search-monitor-usage/enable-monitoring.png "Monitorozás engedélyezése")

3. Válassza ki az exportálni kívánt adatokat: naplók, metrikák vagy mindkettő. Átmásolhatja egy Storage-fiókba, elküldheti egy Event hubhoz, vagy exportálhatja Azure Monitor naplókba.

   A blob Storage-hoz való archiváláshoz csak a Storage-fióknak kell léteznie. A rendszer a tárolók és Blobok létrehozásához szükség lesz a naplófájlok exportálására.

   ![BLOB Storage-Archívum konfigurálása](./media/search-monitor-usage/configure-blob-storage-archive.png "BLOB Storage-Archívum konfigurálása")

4. Mentse a profilt.

5. A naplózást objektumok létrehozásával vagy törlésével (naplófájl-események létrehozása) és lekérdezések elküldésével tesztelheti (mérőszámokat hoz létre). 

A naplózás a profil mentésekor van engedélyezve. A tárolók csak akkor jönnek létre, ha van egy tevékenység a naplóba vagy a mértékbe. Amikor az adat egy Storage-fiókba másolódik, az adat JSON-ként van formázva, és két tárolóba kerül:

* elemzések – naplók – operationlogs: keresési forgalmi naplók
* áttekintések – mérőszámok – pt1m: metrikák esetén

**A tárolók a blob Storage-ban való megjelenítése előtt egy órával tartanak. A tárolók száma óránként egy blob.**

A fájlok megtekintéséhez használhatja a [Visual Studio Code](#download-and-open-in-visual-studio-code) -ot vagy egy másik JSON-szerkesztőt is. 

### <a name="example-path"></a>Példa elérési út

```
resourceId=/subscriptions/<subscriptionID>/resourcegroups/<resourceGroupName>/providers/microsoft.search/searchservices/<searchServiceName>/y=2018/m=12/d=25/h=01/m=00/name=PT1H.json
```

## <a name="log-schema"></a>Napló sémája
A keresési szolgáltatás forgalmi naplóit tartalmazó Blobok strukturálva vannak a jelen szakaszban leírtak szerint. Az egyes Blobok egy, a log objektumok tömbjét tartalmazó **rekordokat** tartalmaznak. Minden blob az adott órában végrehajtott összes művelethez tartalmaz rekordokat.

| Név | Type (Típus) | Példa | Megjegyzések |
| --- | --- | --- | --- |
| time |dátum/idő |"2018-12-07T00:00:43.6872559 Z" |A művelet időbélyege |
| resourceId |sztring |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/ALAPÉRTELMEZETT/SZOLGÁLTATÓK/<br/> Microsoft. KERESÉS/SEARCHSERVICES/SEARCHSERVICE " |Az Ön ResourceId |
| operationName |sztring |"Lekérdezés. keresés" |A művelet neve |
| operationVersion |sztring |"2019-05-06" |A használt API-verzió |
| category |sztring |"OperationLogs" |állandó |
| resultType |sztring |Sikeres |Lehetséges értékek: sikeres vagy sikertelen |
| resultSignature |int |200 |HTTP-eredmény kódja |
| Átl |int |50 |A művelet időtartama ezredmásodpercben |
| properties |objektum |Tekintse meg a következő táblázatot |Művelet-specifikus adathalmazt tartalmazó objektum |

**Tulajdonságok séma**

| Név | Type (Típus) | Példa | Megjegyzések |
| --- | --- | --- | --- |
| Leírás |sztring |"/Indexes beolvasása (" tartalom ")/docs" |A művelet végpontja |
| Lekérdezés |sztring |"? Search = AzureSearch & $count = igaz & API-Version = 2019-05-06" |A lekérdezési paraméterek |
| Dokumentumok |int |42 |Feldolgozott dokumentumok száma |
| indexName |sztring |"testindex" |A művelethez társított index neve |

## <a name="metrics-schema"></a>Mérőszámok sémája

A rendszer a lekérdezési kérelmek esetében rögzíti a metrikákat.

| Név | Type (Típus) | Példa | Megjegyzések |
| --- | --- | --- | --- |
| resourceId |sztring |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/ALAPÉRTELMEZETT/SZOLGÁLTATÓK/<br/>Microsoft. KERESÉS/SEARCHSERVICES/SEARCHSERVICE " |az erőforrás-azonosító |
| metricName |sztring |Késleltetés |a metrika neve |
| time |dátum/idő |"2018-12-07T00:00:43.6872559 Z" |a művelet időbélyege |
| átlagos |int |64 |A nyers minták átlagos értéke a metrika időintervallumában |
| Minimális |int |37 |A nyers minták minimális értéke a metrika időintervallumában |
| Maximális |int |78 |A nyers minták maximális értéke a metrikai időintervallumban |
| teljes |int |258 |A nyers minták teljes értéke a metrikai időintervallumban |
| count |int |4 |A metrika létrehozásához használt nyers minták száma |
| timegrain |sztring |"PT1M" |A metrika időbeli kiőrlése ISO 8601 |

Az összes metrika egyperces időközönként szerepel. Minden metrika percenkénti minimális, maximális és átlagos értékeket tesz elérhetővé.

A SearchQueriesPerSecond metrika esetében a minimális érték az adott percben regisztrált keresési lekérdezések másodpercenkénti legalacsonyabb értéke. Ugyanez vonatkozik a maximális értékre is. Az átlag a teljes percenkénti összesítés.
Gondolja át ezt a forgatókönyvet egy percen belül: egy másodperces nagy terhelést, amely a SearchQueriesPerSecond maximális értéke, majd az átlagos terhelés 58 másodperc, végül egy másodperc csak egy lekérdezéssel, amely a minimum.

A ThrottledSearchQueriesPercentage, a minimum, a maximum, az átlag és az összes érték esetében ugyanaz az érték: a leszabályozott keresési lekérdezések százalékos aránya a keresési lekérdezések teljes számától egy percen belül.

## <a name="download-and-open-in-visual-studio-code"></a>Letöltés és Megnyitás a Visual Studio Code-ban

A naplófájl megtekintéséhez bármely JSON-szerkesztőt használhat. Ha még nem rendelkezik ilyennel, javasoljuk, hogy a [Visual Studio Code](https://code.visualstudio.com/download)-ot ajánljuk.

1. Azure Portal nyissa meg a Storage-fiókját. 

2. A bal oldali navigációs panelen kattintson a **Blobok**elemre. Látnia kell az elemzéseket – **naplók – operationlogs** és elemzések – **mérőszámok – pt1m**. Ezeket a tárolókat a Azure Search hozza létre, amikor a rendszer a naplózási adatként a blob Storage-ba exportálja.

3. Kattintson a mappa-hierarchiára, amíg el nem éri a. JSON fájlt.  A fájl letöltéséhez használja a helyi menüt.

Miután letöltötte a fájlt, nyissa meg egy JSON-szerkesztőben a tartalom megtekintéséhez.

## <a name="use-system-apis"></a>A System API-k használata
A Azure Search REST API és a .NET SDK egyaránt biztosít programozott hozzáférést a szolgáltatási metrikák, az index és az indexelő adataihoz, valamint a dokumentumok számát.

* [Szolgáltatások statisztikáinak beolvasása](/rest/api/searchservice/get-service-statistics)
* [Index statisztikáinak beolvasása](/rest/api/searchservice/get-index-statistics)
* [Dokumentumok számlálása](/rest/api/searchservice/count-documents)
* [Indexelő állapotának beolvasása](/rest/api/searchservice/get-indexer-status)

A PowerShell vagy az Azure CLI használatával történő engedélyezéshez tekintse meg a dokumentációt [itt](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview).

## <a name="next-steps"></a>Következő lépések

A szolgáltatás felügyeletével és [teljesítményével, valamint](search-performance-optimization.md) a hangolási útmutatóval kapcsolatos további információkért a [Microsoft Azure felügyelheti a Search szolgáltatást](search-manage.md) .
