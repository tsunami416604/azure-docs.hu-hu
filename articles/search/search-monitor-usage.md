---
title: Használat és a lekérdezés erőforrásmetrikákat egy keresési szolgáltatás – Azure Search figyelése
description: Naplózás engedélyezése, a lekérdezés tevékenységi metrikák, erőforrás-használatot és egyéb rendszeradatokat kérhet az Azure Search szolgáltatást.
author: HeidiSteen
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.devlang: na
ms.topic: conceptual
ms.date: 01/22/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: d7084a42f64234cff4e5e2742ed3d27a3fd00e1e
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58652297"
---
# <a name="monitor-resource-consumption-and-query-activity-in-azure-search"></a>Azure Search erőforrás-használat és a lekérdezés tevékenység figyelése

Az Azure Search szolgáltatás áttekintő lapján megtekintheti az erőforrás-használat, a lekérdezés metrikák és a mennyi kvóta érhető el, további indexek, indexelők és adatforrások létrehozása rendszer adatait. A portál segítségével is konfigurálhatja a log analytics vagy az állandó adatgyűjtés használt egy másik erőforrás. 

Naplók beállítása hasznos önkiszolgáló-diagnosztikai és megőrző működési előzményei. Belsőleg naplók léteznek háttérbeli rövid idő alatt, elegendő a vizsgálati és elemzési Ha, küldjön egy támogatási jegyet. Ha felett, és naplózza az információkat a hozzáférést, ha a cikkben bemutatott megoldások beállított.

Ez a cikk ismerteti a figyelés lehetőségeket, a naplózás engedélyezése és a naplófájl tárolási és a napló tartalmának megtekintése.

## <a name="metrics-at-a-glance"></a>Mérőszámot egyetlen pillantással

**Használati** és **figyelés** szakaszok az Áttekintés épített ki az erőforrás-használat a jelentés oldalon, és végrehajtási metrikák lekérdezése. Ezt az információt elérhetővé válik, amint megkezdi a szolgáltatás használatát a szükséges konfiguráció nélkül. Ez az oldal néhány percenként frissülnek. Ha meg vannak kapcsolatos döntések véglegesítése [melyik szint a termelési számítási feladatokhoz használandó](search-sku-tier.md), vagy e [állítsa be az aktív replikák és partíciók számát](search-capacity-planning.md), ezek a metrikák segítségére lehet a ezeket a döntéseket szerint jeleníti meg, milyen gyorsan erőforrásokat vesznek igénybe, és arról, hogy a jelenlegi konfiguráció kezeli-e a meglévő terhelés.

A **használati** lapon látható erőforrás rendelkezésre állási viszonyított jelenlegi [korlátok](search-limits-quotas-capacity.md). Az alábbi ábrán az ingyenes szolgáltatás, és minden típusú 3 objektumokat és 50 MB tárhelyet maximumon szól. Egy alapszintű vagy standard szintű service magasabb korlátokkal rendelkeznek, és növeli a partíció számát, ha maximális tárolási megnő arányosan.

![Hatékony korlátok viszonyított használatának állapota](./media/search-monitor-usage/usage-tab.png
 "viszonyított hatékony korlátok használatának állapota")

## <a name="queries-per-second-qps-and-other-metrics"></a>Másodpercenkénti lekérdezések (QPS) és száma egyéb metrika

A **figyelés** lapon látható átlagok, például a keresési metrikákat a áthelyezése *lekérdezések másodpercenként* lekérdezési (QPS), percenként összesítve. 
*Keresési késés* van a keresési szolgáltatás keresési lekérdezéseket, percenként összesítve feldolgozásához szükséges idő. *Szabályozott lekérdezések százalékos* (nem látható) van, amely is szabályozott, még / perc összesített keresési lekérdezések aránya.

Ezek a számok hozzávetőlegesek, és célja, hogy általános képet arról, hogy a rendszer szervizeli a kérelmeket. Tényleges QPS magasabb vagy alacsonyabb, mint a portál által jelentett szám lehet.

![Lekérdezések száma második tevékenység](./media/search-monitor-usage/monitoring-tab.png "lekérdezések száma második tevékenység")

## <a name="activity-logs"></a>Tevékenységnaplók

A **tevékenységnapló** adatokat gyűjt az Azure Resource Manager. A tevékenységnapló található adatokat például létrehozása vagy frissítése egy erőforráscsoport, a név rendelkezésre állását ellenőrzi, vagy egy hozzáférési kulcsot első kezeljék a kéréseket, a szolgáltatás törlése folyamatban van. 

Elérheti a **tevékenységnapló** a bal oldali navigációs ablaktáblán, vagy a felső ablakban parancsban sávon, vagy az értesítések a **diagnosztizálása és a problémák megoldásához** lap.

A szolgáltatáson belüli feladatokat, mint az index létrehozása vagy törlése egy adatforrást látni fogja például a "Get adminisztrációs kulcsot" minden kérést, de nem a megfelelő műveletet magát az általános értesítések. Az adatok ezen szintje engedélyeznie kell egy bővítményt figyelési megoldást.

## <a name="add-on-monitoring-solutions"></a>Kiegészítő figyelési megoldások

Az Azure Search tárol, ami azt jelenti, hogy a napló külsőleg kell tárolni kell adatokat kezel, az objektumok túl. Ha szeretné megőrizni a Teljesítménynapló-adatok az alábbi erőforrások valamelyikét konfigurálhatja. 

Az alábbi táblázat a naplók tárolásához és hozzáadása a szolgáltatási műveletek és az Application Insights segítségével a lekérdezési számítási feladatok részletes figyelését lehetőségeit hasonlítja össze.

| Erőforrás | Alkalmazási cél |
|----------|----------|
| [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) | Naplózott eseményeket és mérőszámokat lekérdezés, alapú egyet az alábbi, a sémák felhasználói események az alkalmazásban is vonatkozhatnak, és. Ez az az egyetlen megoldás, amely a felhasználói műveletek vagy jelek figyelembe veszi, leképezés események a felhasználó által kezdeményezett search-ben alkalmazáskód által küldött kérelmek szűrése szemben. Ezt a módszert használja, a másolás és beillesztés kialakítási kódja be a forrásfájlokat útvonal adatok kérése az Application Insightsba. További információkért lásd: [forgalmi elemzések keresése](search-traffic-analytics.md). |
| [Azure Monitor-naplók](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview) | A naplózott eseményeket és mérőszámokat lekérdezés,-alapú egyet az alábbi sémák. Eseményt naplózza a Log Analytics-munkaterületet. Lekérdezések egy munkaterületet a részletes információkat adnak a naplóból vonatkozóan futtathat le. További információkért lásd: [Ismerkedés az Azure Monitor naplóira](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-viewdata) |
| [Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) | A naplózott eseményeket és mérőszámokat lekérdezés,-alapú egyet az alábbi sémák. Eseményeket naplózza a Blob-tárolóba, és JSON-fájlban tárolt. Egy JSON-szerkesztő segítségével megtekintheti a fájl tartalmát.|
| [Event Hub](https://docs.microsoft.com/azure/event-hubs/) | Naplózott események és lekérdezés-metrikák, a jelen cikkben leírt sémák alapján. Válassza ezt a hatalmas naplók az alternatív gyűjtemény szolgáltatásként. |

Az Azure Monitor-naplók és a Blob storage érhetők el ingyenes megosztott szolgáltatásként, hogy Ön is próbálja ki ingyenesen elkölthetők az Azure-előfizetés teljes élettartama. Az Application Insights regisztráljon, és mindaddig, amíg az adatok mérete alkalmazás bizonyos korlátozások mellett használata díjmentes (lásd a [díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/monitor/) részletekért).

Ez a szakasz végigvezeti a lépéseken, engedélyezése és az Azure Blob storage összegyűjtésére és Azure Search-műveletek által létrehozott naplóadatok eléréséhez.

## <a name="enable-logging"></a>Naplózás engedélyezése

Indexelés és a lekérdezési számítási feladatok naplózása alapértelmezés szerint ki van kapcsolva, és naplózás az infrastruktúra és a hosszú távú, külső tárterület-bővítmény megoldások függ. Önmagában az csak megőrzött adatok az Azure Search szolgáltatásban az objektumokat hoz létre, és kezeli,, így a naplók máshol kell tárolni.

Ebben a szakaszban megismerheti, hogyan naplózott eseményeket és mérőszámokat adatokat tárolni a Blob storage használata lesz.

1. [Hozzon létre egy tárfiókot](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) Ha még nem rendelkezik. Elhelyezhet az Azure Search egyszerűbb karbantartása később Ha ehhez a gyakorlathoz használt összes erőforrást törli, ugyanabban az erőforráscsoportban.

2. Nyissa meg a keresési szolgáltatás áttekintés oldalát. A bal oldali navigációs panelen görgessen le a **figyelés** kattintson **figyelés engedélyezése**.

   ![Engedélyezze a monitorozást](./media/search-monitor-usage/enable-monitoring.png "figyelés engedélyezése")

3. Válassza ki az exportálandó adatokat: Naplók, mérőszámok vagy mindkettőt. Másolja a tárfiókhoz, küldje el az eseményközpontok felé, vagy exportálhatja, és az Azure Monitor naplóira.

   A Blob storage való archiválás, a storage-fiók léteznie kell. Tárolók és blobok Teljesítménynapló-adatok exportálásakor létrejön.

   ![Konfigurálás a blob storage archív](./media/search-monitor-usage/configure-blob-storage-archive.png "konfigurálása a blob storage-archívum")

4. A profil mentéséhez.

5. Teszt naplózása létrehozásával vagy objektumok törlése (hoz létre az alkalmazásnapló-események) és a lekérdezések elküldése (a metrikákat hoz létre). 

Naplózás engedélyezve van, a profil mentésekor. Tárolók csak akkor jönnek létre, amikor egy tevékenység napló vagy mérték. Amikor a rendszer az adatokat másolja egy tárfiókba, az adatok JSON-ként formázott és két tárolókban elhelyezett:

* insights-logs-operationlogs: a keresési forgalmi naplók
* insights-mérőszámok – pt1m: metrikák

Egy óra elteltével fog megjelenni a tárolók a Blob storage vesz igénybe. Nincs óránként, a tároló és a egy blob. 

Használhat [Visual Studio Code](#download-and-open-in-visual-studio-code) vagy egy másik JSON-szerkesztő a fájlok megtekintéséhez. 

### <a name="example-path"></a>Példa az elérési útra

```
resourceId=/subscriptions/<subscriptionID>/resourcegroups/<resourceGroupName>/providers/microsoft.search/searchservices/<searchServiceName>/y=2018/m=12/d=25/h=01/m=00/name=PT1H.json
```

## <a name="log-schema"></a>Séma
A search service forgalmi naplók tartalmazó blobok struktúrája ebben a szakaszban leírtak szerint. Minden egyes blob van egy gyökérobjektum nevű **rekordok** tartalmazó napló objektumokból álló tömb. Minden egyes blob minden művelet, amely során az adott órán következett rekordjait is tartalmazza.

| Name (Név) | Típus | Példa | Megjegyzések |
| --- | --- | --- | --- |
| time |dátum/idő |"2018-12-07T00:00:43.6872559Z" |A művelet időbélyeg |
| resourceId |sztring |"/ SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111 /<br/>RESOURCEGROUPS/ALAPÉRTELMEZETT/SZOLGÁLTATÓK /<br/> A MICROSOFT. KERESÉS/SEARCHSERVICES/SEARCHSERVICE" |Az erőforrás-azonosító |
| operationName |sztring |"Query.Search" |A művelet neve |
| operationVersion |sztring |"2017-11-11" |A használt api-verzió |
| category |sztring |"OperationLogs" |állandó |
| resultType |sztring |"Sikeres" |Érvényes értékek: Sikeres vagy sikertelen |
| resultSignature |int |200 |HTTP-eredménykód |
| durationMS |int |50 |Ennyi ezredmásodpercig tart a művelet időtartama |
| properties |objektum |az alábbi táblázatban foglaltuk össze |A művelet-specifikus adatokat tartalmazó objektum |

**Tulajdonságok séma**

| Name (Név) | Típus | Példa | Megjegyzések |
| --- | --- | --- | --- |
| Leírás |sztring |"GET /indexes('content')/docs" |A művelet végpont |
| Lekérdezés |sztring |"?search=AzureSearch&$count=true&api-version=2017-11-11" |A lekérdezési paraméterek |
| Dokumentumok |int |42 |Feldolgozott dokumentumok száma |
| indexName |sztring |"testindex" |A művelethez társított az index neve |

## <a name="metrics-schema"></a>Metrikák séma

Metrika rögzítése a lekérdezésekre vonatkozó kérelmek számára.

| Name (Név) | Típus | Példa | Megjegyzések |
| --- | --- | --- | --- |
| resourceId |sztring |"/ SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111 /<br/>RESOURCEGROUPS/ALAPÉRTELMEZETT/SZOLGÁLTATÓK /<br/>A MICROSOFT. KERESÉS/SEARCHSERVICES/SEARCHSERVICE" |az erőforrás-azonosító |
| MetricName |sztring |"Késés" |a metrika neve |
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

## <a name="download-and-open-in-visual-studio-code"></a>Töltse le és nyissa meg a Visual Studio Code-ban

Bármely JSON-szerkesztő segítségével a naplófájlban. Ha még nincs fiókja, javasoljuk, hogy [Visual Studio Code](https://code.visualstudio.com/download).

1. Az Azure Portalon nyissa meg a Storage-fiókjában. 

2. Kattintson a bal oldali navigációs ablaktáblában **Blobok**. Megtekintheti az **insights-logs-operationlogs** és **insights-mérőszámok – pt1m**. A naplóadatok exportálásakor a Blob storage tárolók az Azure Search jönnek létre.

3. Kattintson a mappahierarchiában le, amíg el nem éri a .JSON kiterjesztésű fájlt.  A helyi menü használatával töltse le a fájlt.

A fájl letöltése után, nyissa meg a JSON-szerkesztőt, a tartalom megtekintéséhez.

## <a name="use-system-apis"></a>A rendszer API-k használata
Az Azure Search REST API és a .NET SDK-val programozott hozzáférést biztosítanak a szolgáltatási metrikák, index és indexelő adatai, és a dokumentumok számát.

* [Szolgáltatások statisztikájának beolvasása](/rest/api/searchservice/get-service-statistics)
* [Index statisztikájának beolvasása](/rest/api/searchservice/get-index-statistics)
* [Dokumentumok száma](/rest/api/searchservice/count-documents)
* [Az indexelő állapotának beolvasása](/rest/api/searchservice/get-indexer-status)

A PowerShell vagy az Azure CLI használatával engedélyezéséhez tekintse a dokumentáció [Itt](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs#how-to-enable-collection-of-diagnostic-logs).

## <a name="next-steps"></a>További lépések

[A Microsoft Azure Search szolgáltatás kezelése](search-manage.md) további információt a szolgáltatások felügyeletével és [teljesítmény és optimalizálás](search-performance-optimization.md) finomhangolási útmutató a.