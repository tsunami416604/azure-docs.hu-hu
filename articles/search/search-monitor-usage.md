---
title: Erőforrás-használati és lekérdezési metrikák figyelése
titleSuffix: Azure Cognitive Search
description: Naplózás engedélyezése, lekérdezési tevékenység metrikáinak beolvasása, erőforrás-használat és egyéb rendszeradatok Azure Cognitive Search szolgáltatásból.
manager: nitinme
author: HeidiSteen
ms.author: heidist
tags: azure-portal
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 7ef868f156ac537cb066f293872f69135c4df25f
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2020
ms.locfileid: "77059648"
---
# <a name="monitor-resource-consumption-and-query-activity-in-azure-cognitive-search"></a>Az erőforrás-felhasználás és a lekérdezési tevékenységek figyelése az Azure-ban Cognitive Search

Az Azure Cognitive Search szolgáltatás áttekintés lapján megtekintheti az erőforrás-használattal, a lekérdezési metrikákkal és a több index, indexelő és adatforrás létrehozásához rendelkezésre álló kvóta mennyiségét. A portál használatával is konfigurálhatja a log Analytics szolgáltatást, illetve az állandó adatgyűjtéshez használt más erőforrásokat. 

A naplók beállítása az öndiagnosztika és a működési Előzmények megőrzése esetén hasznos. Belsőleg a naplófájlok a háttérben, rövid idő alatt léteznek, elegendő a vizsgálathoz és az elemzéshez, ha támogatási jegyet ad. Ha szabályozni szeretné a naplózási adatokat, és hozzáférést szeretne biztosítani a naplófájlhoz, akkor a jelen cikkben ismertetett megoldások egyikét kell beállítania.

Ebben a cikkben megismerheti a figyelési lehetőségeit, a naplózási és naplózási műveletek engedélyezését, valamint a napló tartalmának megtekintését.

## <a name="metrics-at-a-glance"></a>Mérőszámok áttekintése

Az Áttekintés lapon beépített **használati** és **figyelési** szakaszt az erőforrás-felhasználással és a lekérdezés-végrehajtási metrikákkal kapcsolatos jelentésekben találja. Ezek az adatok a szolgáltatás használatának megkezdése után azonnal elérhetővé válnak, és nincs szükség konfigurációra. Ez az oldal néhány percenként frissül. Ha az éles számítási feladatokhoz [használt szintet](search-sku-tier.md)véglegesíti, vagy ha [módosítani szeretné az aktív replikák és partíciók számát](search-capacity-planning.md), akkor ezek a mérőszámok segítséget nyújthatnak ezekhez a döntésekhez azáltal, hogy megmutatják, hogy az erőforrások milyen gyorsan legyenek felhasználva, és hogy a jelenlegi konfiguráció milyen mértékben kezeli a meglévő terhelést.

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

Az Azure Cognitive Search nem tárol semmilyen, az általa kezelt objektumon túli adattárolást, ami azt jelenti, hogy a naplózási adatforrásokat külsőleg kell tárolni. Az alábbi erőforrások bármelyikét megadhatja, ha szeretné megőrizni a naplózási adatmennyiséget. 

A következő táblázat összehasonlítja a naplók tárolásának lehetőségeit, és részletesen figyeli a szolgáltatási műveleteket és a lekérdezési feladatokat a Application Insightson keresztül.

| Erőforrás | Felhasználási mód |
|----------|----------|
| [Azure Monitor-naplók](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview) | Naplózott események és lekérdezések mérőszámai az alábbi sémák alapján. Az események naplózása egy Log Analytics munkaterületre történik. Futtathat lekérdezéseket egy munkaterületen, ha részletes információkat ad vissza a naplóból. További információ: Ismerkedés [a Azure monitor-naplókkal](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-viewdata) |
| [Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) | Naplózott események és lekérdezések mérőszámai az alábbi sémák alapján. Az események egy blob-tárolóba kerülnek, és JSON-fájlokban tárolódnak. A fájl tartalmának megtekintéséhez használjon JSON-szerkesztőt.|
| [Event Hub](https://docs.microsoft.com/azure/event-hubs/) | A naplózott események és a lekérdezési metrikák a cikkben leírt sémák alapján. Válassza ezt alternatív adatgyűjtési szolgáltatásként a nagyon nagy naplók számára. |

A Azure Monitor naplók és a blob Storage is ingyenes szolgáltatásként érhető el, így ingyenesen kipróbálható az Azure-előfizetés élettartama. Application Insights ingyenesen regisztrálhatók és használhatók, amíg az alkalmazás adatainak mérete bizonyos korlátok között van (a részletekért tekintse meg a [díjszabási oldalt](https://azure.microsoft.com/pricing/details/monitor/) ).

A következő szakasz végigvezeti az Azure Blob Storage engedélyezésének és használatának lépésein az Azure Cognitive Search-műveletek által létrehozott naplózási adatok gyűjtéséhez és eléréséhez.

## <a name="enable-logging"></a>Naplózás engedélyezése

Az indexelési és lekérdezési számítási feladatok naplózása alapértelmezés szerint ki van kapcsolva, és a naplózási infrastruktúra és a hosszú távú külső tárolók kiegészítő megoldásaitól függ. Önmagában az Azure Cognitive Search által létrehozott és kezelt objektumok csak a megőrzött adatforrások, ezért a naplókat máshol kell tárolni.

Ez a szakasz azt ismerteti, hogyan használható a blob Storage a naplózott események és mérőszámok adatok tárolására.

1. [Hozzon létre egy Storage-fiókot](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) , ha még nem rendelkezik ilyennel. Azt is megteheti, hogy ugyanabba az erőforráscsoporthoz helyezi, mint az Azure Cognitive Search, hogy később leegyszerűsítse a tisztítást, ha törölni szeretné az ebben a gyakorlatban használt összes erőforrást.

   A Storage-fióknak ugyanabban a régióban kell lennie, mint az Azure Cognitive Search.

2. Nyissa meg a keresési szolgáltatás áttekintés lapját. A bal oldali navigációs panelen görgessen lefelé a **figyelés** területen, majd kattintson a **diagnosztikai beállítások**elemre.

   ![Diagnosztikai beállítások](./media/search-monitor-usage/diagnostic-settings.png "Diagnosztikai beállítások")

3. Válassza a **diagnosztikai beállítás hozzáadása** elemet.

4. Válassza ki az adatokat szeretne exportálni: naplók, metrikák, vagy mindkettőt. Átmásolhatja egy Storage-fiókba, elküldheti egy Event hubhoz, vagy exportálhatja Azure Monitor naplókba.

   A blob Storage-hoz való archiváláshoz csak a Storage-fióknak kell léteznie. A rendszer a tárolók és Blobok létrehozásához szükség lesz a naplófájlok exportálására.

   ![BLOB Storage-Archívum konfigurálása](./media/search-monitor-usage/configure-blob-storage-archive.png "BLOB Storage-Archívum konfigurálása")

5. A profil mentése

6. A naplózást objektumok létrehozásával vagy törlésével (naplófájl-események létrehozása) és lekérdezések elküldésével tesztelheti (mérőszámokat hoz létre). 

A naplózás a profil mentésekor van engedélyezve. A tárolók csak akkor jönnek létre, ha van egy tevékenység a naplóba vagy a mértékbe. Amikor az adat egy Storage-fiókba másolódik, az adat JSON-ként van formázva, és két tárolóba kerül:

* insights-logs-operationlogs: a keresési forgalmi naplók
* insights-mérőszámok – pt1m: metrikák

**A tárolók a blob Storage-ban való megjelenítése előtt egy órával tartanak. A tárolók száma óránként egy blob.**

A fájlok megtekintéséhez használhatja a [Visual Studio Code](#download-and-open-in-visual-studio-code) -ot vagy egy másik JSON-szerkesztőt is. 

### <a name="example-path"></a>Példa elérési út

```
resourceId=/subscriptions/<subscriptionID>/resourcegroups/<resourceGroupName>/providers/microsoft.search/searchservices/<searchServiceName>/y=2018/m=12/d=25/h=01/m=00/name=PT1H.json
```

## <a name="log-schema"></a>Séma
A keresési szolgáltatás forgalmi naplóit tartalmazó Blobok strukturálva vannak a jelen szakaszban leírtak szerint. Az egyes Blobok egy, a log objektumok tömbjét tartalmazó **rekordokat** tartalmaznak. Minden blob az adott órában végrehajtott összes művelethez tartalmaz rekordokat.

| Név | Típus | Példa | Megjegyzések |
| --- | --- | --- | --- |
| time |dátum/idő |"2018-12-07T00:00:43.6872559Z" |A művelet időbélyeg |
| resourceId |sztring |"/ SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111 /<br/>RESOURCEGROUPS/ALAPÉRTELMEZETT/SZOLGÁLTATÓK /<br/> A MICROSOFT. KERESÉS/SEARCHSERVICES/SEARCHSERVICE" |Az erőforrás-azonosító |
| operationName |sztring |"Query.Search" |A művelet neve |
| operationVersion |sztring |"2019-05-06" |A használt api-verzió |
| kategória |sztring |"OperationLogs" |állandó |
| resultType |sztring |"Sikeres" |A lehetséges értékek: sikeres vagy sikertelen |
| resultSignature |int |200 |HTTP-eredménykód |
| durationMS |int |50 |Ennyi ezredmásodpercig tart a művelet időtartama |
| tulajdonságok |objektum |az alábbi táblázatban foglaltuk össze |A művelet-specifikus adatokat tartalmazó objektum |

**Tulajdonságok séma**

| Név | Típus | Példa | Megjegyzések |
| --- | --- | --- | --- |
| Leírás |sztring |"GET /indexes('content')/docs" |A művelet végpont |
| Lekérdezés |sztring |"?search=AzureSearch&$count=true&api-version=2019-05-06" |A lekérdezési paraméterek |
| Dokumentumok |int |42 |Feldolgozott dokumentumok száma |
| indexName |sztring |"testindex" |A művelethez társított az index neve |

## <a name="metrics-schema"></a>Metrikák séma

A rendszer a lekérdezési kérelmek esetében rögzíti a metrikákat.

| Név | Típus | Példa | Megjegyzések |
| --- | --- | --- | --- |
| resourceId |sztring |"/ SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111 /<br/>RESOURCEGROUPS/ALAPÉRTELMEZETT/SZOLGÁLTATÓK /<br/>A MICROSOFT. KERESÉS/SEARCHSERVICES/SEARCHSERVICE" |az erőforrás-azonosító |
| metricName |sztring |"Késés" |a metrika neve |
| time |dátum/idő |"2018-12-07T00:00:43.6872559Z" |a művelet időbélyeg |
| átlag |int |64 |Átlagos értéke a metrika időközben a nyers minták |
| minimum |int |37 |A minimális érték a metrika időközben a nyers minták |
| maximum |int |78 |A maximális érték a metrika időközben a nyers minták |
| összeg |int |258 |A metrika időközben a nyers minták teljes értéke |
| count |int |4 |A metrika létrehozásához használt nyers minták száma. |
| timegrain |sztring |"PT1M" |A metrika az ISO 8601 az aggregációs időköz |

Az összes metrikák szerepelnek a jelentésben, az egy perces időközönként. Minden mérőszám minimális, maximális és átlagos értékek száma percenként.

A metrika-SearchQueriesPerSecond minimális érték a legkisebb érték a keresési lekérdezések másodpercenként, amely az adott percben regisztrálták. Ugyanez vonatkozik a maximális értéknél. A teljes perc átlagos, esetében az összesítést.
Ebben a forgatókönyvben egy perc alatt gondoljunk: egy második magas betöltése, amely a legnagyobb SearchQueriesPerSecond, átlagos terhelés, 58 másodperc követ, és végül egy második csak egy lekérdezést, amely a minimális.

ThrottledSearchQueriesPercentage, a minimális, maximális, átlagos és teljes, az összes ugyanazzal az értékkel rendelkeznek: voltak szabályozva, egy perc alatt keresési lekérdezések teljes száma a keresési lekérdezések aránya.

## <a name="download-and-open-in-visual-studio-code"></a>Letöltés és Megnyitás a Visual Studio Code-ban

A naplófájl megtekintéséhez bármely JSON-szerkesztőt használhat. Ha még nem rendelkezik ilyennel, javasoljuk, hogy a [Visual Studio Code](https://code.visualstudio.com/download)-ot ajánljuk.

1. Azure Portal nyissa meg a Storage-fiókját. 

2. A bal oldali navigációs panelen kattintson a **Blobok**elemre. Látnia kell az elemzéseket – **naplók – operationlogs** és elemzések – **mérőszámok – pt1m**. Ezeket a tárolókat az Azure Cognitive Search hozza létre, amikor a naplózási adatként a blob Storage-ba exportálja őket.

3. Kattintson a mappa-hierarchiára, amíg el nem éri a. JSON fájlt.  A fájl letöltéséhez használja a helyi menüt.

Miután letöltötte a fájlt, nyissa meg egy JSON-szerkesztőben a tartalom megtekintéséhez.

## <a name="use-system-apis"></a>A System API-k használata
Az Azure Cognitive Search REST API és a .NET SDK egyaránt biztosít programozott hozzáférést a szolgáltatási metrikák, az index és az indexelő adataihoz, valamint a dokumentumok számát.

* [Szolgáltatások statisztikáinak beolvasása](/rest/api/searchservice/get-service-statistics)
* [Index statisztikáinak beolvasása](/rest/api/searchservice/get-index-statistics)
* [Dokumentumok számlálása](/rest/api/searchservice/count-documents)
* [Indexelő állapotának beolvasása](/rest/api/searchservice/get-indexer-status)

A PowerShell vagy az Azure CLI használatával történő engedélyezéshez tekintse meg a dokumentációt [itt](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview).

## <a name="next-steps"></a>Következő lépések

A szolgáltatás felügyeletével és [teljesítményével, valamint](search-performance-optimization.md) a hangolási útmutatóval kapcsolatos további információkért a [Microsoft Azure felügyelheti a Search szolgáltatást](search-manage.md) .
