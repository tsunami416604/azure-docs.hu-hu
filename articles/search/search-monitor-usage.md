---
title: Search-szolgáltatás – Azure Search az erőforrás használat és a lekérdezési statisztikák figyelése
description: Lekérdezés tevékenységi metrikák, erőforrás-használat és egyéb rendszeradatokat kaphat az Azure Search szolgáltatást.
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
ms.openlocfilehash: 5f8a4e7dcaa1bc2df71246f67d06fc63ae4fcd06
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54883500"
---
# <a name="monitor-an-azure-search-service-in-azure-portal"></a>Az Azure portal Azure Search szolgáltatás figyelése

Az Azure Search szolgáltatás áttekintő lapján megtekintheti az erőforrás-használatot, valamint lekérdezési mérőszámokat, például a lekérdezések egy második lekérdezési (QPS), a Lekérdezések késése és a kérelmek szabályozva lettek aránya rendszer adatait. A portál segítségével ezenkívül figyelési képességek az Azure platformon, mélyebb adatgyűjtés széles használhatja. 

Ez a cikk azonosítja, és az Azure Search-műveletek naplózása elérhető lehetőségeit hasonlítja össze. Engedélyezi a naplózást és a naplók tárolásához, és hogyan bontsa ki a begyűjtött információkat a vonatkozó utasításokat tartalmazza.

Ha egy támogatási jegyet, nincsenek adott feladatokat vagy adatokat kell megadnia. A támogatási szakértők rendelkezik a szükséges információkat a konkrét problémák kivizsgálása.  

## <a name="metrics-at-a-glance"></a>Mérőszámot egyetlen pillantással

**Használati** és **figyelés** áttekintése épített szakaszok tárhelyhasználat megjelenítését, és végrehajtási metrikák lekérdezése. Ezt az információt elérhetővé válik, amint megkezdi a szolgáltatás használatát a szükséges konfiguráció nélkül. Ez az oldal néhány percenként frissülnek. Ha meg vannak kapcsolatos döntések véglegesítése [melyik szint a termelési számítási feladatokhoz használandó](search-sku-tier.md), vagy e [állítsa be az aktív replikák és partíciók számát](search-capacity-planning.md), ezek a metrikák segítségére lehet a ezeket a döntéseket szerint jeleníti meg, milyen gyorsan erőforrásokat vesznek igénybe, és arról, hogy a jelenlegi konfiguráció kezeli-e a meglévő terhelés.

A **használati** lapon látható erőforrás rendelkezésre állási viszonyított jelenlegi [korlátok](search-limits-quotas-capacity.md). Az alábbi ábrán az ingyenes szolgáltatás, és minden típusú 3 objektumokat és 50 MB tárhelyet maximumon szól. Egy alapszintű vagy standard szintű service magasabb korlátokkal rendelkeznek, és növeli a partíció számát, ha maximális tárolási megnő arányosan.

![Hatékony korlátok viszonyított használatának állapota](./media/search-monitor-usage/usage-tab.png
 "viszonyított hatékony korlátok használatának állapota")

## <a name="queries-per-second-qps-and-other-metrics"></a>Másodpercenkénti lekérdezések (QPS) és száma egyéb metrika

A **figyelés** lapon látható átlagok, például a keresési metrikákat a áthelyezése *lekérdezések másodpercenként* lekérdezési (QPS), percenként összesítve. 
*Keresési késés* van a keresési szolgáltatás keresési lekérdezéseket, percenként összesítve feldolgozásához szükséges idő. *Szabályozott lekérdezések százalékos* (nem látható) van, amely is szabályozott, még / perc összesített keresési lekérdezések aránya.

![Lekérdezések száma második tevékenység](./media/search-monitor-usage/monitoring-tab.png "lekérdezések száma második tevékenység")

## <a name="activity-logs"></a>Tevékenységnaplók

A **tevékenységnapló** adatokat gyűjt az Azure Resource Manager. A tevékenységnapló található adatokat például létrehozása vagy frissítése egy erőforráscsoport, a név rendelkezésre állását ellenőrzi, vagy egy hozzáférési kulcsot első kezeljék a kéréseket, a szolgáltatás törlése folyamatban van. 

Elérheti a **tevékenységnapló** a bal oldali navigációs ablaktáblán, vagy a felső ablakban parancsban sávon, vagy az értesítések a **diagnosztizálása és a problémák megoldásához** lap.

A szolgáltatáson belüli feladatokat, mint az index létrehozása vagy törlése egy adatforrást látni fogja például a "Get adminisztrációs kulcsot" minden kérést, de nem a megfelelő műveletet magát az általános értesítések. Az adatok ezen szintje engedélyeznie kell egy bővítményt figyelési megoldást.

## <a name="add-on-monitoring-solutions"></a>Kiegészítő figyelési megoldások

Az Azure Search tárol, ami azt jelenti, hogy a napló külsőleg kell tárolni kell adatokat kezel, az objektumok túl. Ha szeretné megőrizni a Teljesítménynapló-adatok az alábbi erőforrások valamelyikét konfigurálhatja. 

Az alábbi táblázat a naplók tárolásához és hozzáadása a szolgáltatási műveletek és az Application Insights segítségével a lekérdezési számítási feladatok részletes figyelését lehetőségeit hasonlítja össze.

| Erőforrás | Használt |
|----------|----------|
| [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) | [Forgalmi elemzések keresése](search-traffic-analytics.md). Ez az az egyetlen megoldás, amely rögzíti a kérelmek, az értékeket az alábbi naplózás és mérőszámok sémák azonosított túllépnek további információt. Ezzel a módszerrel, másolás és beillesztés kialakítási kódja be a forrásfájlokat alkalmazáskérelem adatai átirányítása az Application Insights lekérdezési kifejezés bemenetek, nulla egyezést, a lekérdezések elemzés céljából, és így tovább. Javasoljuk, hogy a Power BI az analytics előtér, az Application insights szolgáltatásban tárolt adatokra.  |
| [Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) | Kérelmek és a metrikák, alapú egyet az alábbi sémák. Eseményeket naplózza a Blob-tárolóba. Javasoljuk, hogy az Excel vagy a Power BI az analytics előtér, az Azure Blob storage-ban tárolt adatokhoz.|
| [Event Hub](https://docs.microsoft.com/azure/event-hubs/) | Kérelmek és a metrikák, a jelen cikkben leírt sémák alapján. Válassza ezt a hatalmas naplók az alternatív gyűjtemény szolgáltatásként. |

Egy figyelését teszi lehetővé az Azure search [Power BI-tartalomcsomag](https://app.powerbi.com/getdata/services/azure-search) így is elemezheti a naplófájlok adatait. A tartalomcsomag automatikusan csatlakozhat az adataihoz, és a keresési szolgáltatás kapcsolatos vizuális elemzést biztosítanak konfigurált jelentések áll. További információkért lásd: a [tartalomcsomag súgóoldalán](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-search/).

A Blob storage lehetőséget ingyenes, megosztott szolgáltatásként érhető el, így kipróbálhatja, ingyenesen elkölthetők az Azure-előfizetés teljes élettartama. Ez a szakasz végigvezeti a lépéseken, engedélyezése és az Azure Blob storage összegyűjtésére és Azure Search-műveletek által létrehozott naplóadatok eléréséhez.

## <a name="enable-logging"></a>Naplózás engedélyezése

Indexelés és a lekérdezési számítási feladatok naplózása alapértelmezés szerint ki van kapcsolva, és a naplózás az infrastruktúra és a külső tárhelyen bővítmény megoldások függ. Önmagában csak megőrzött Azure Search adatai indexeket, így naplók máshol kell tárolni.

Ebben a szakaszban megismerheti a Blob storage használata a naplózott eseményeket és mérőszámokat adatokat tartalmazhat lesz.

1. [Hozzon létre egy tárfiókot](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) Ha még nem rendelkezik. Elhelyezhet az Azure Search egyszerűbb karbantartása később Ha ehhez a gyakorlathoz használt összes erőforrást törli, ugyanabban az erőforráscsoportban.

2. Nyissa meg a keresési szolgáltatás áttekintés oldalát. A bal oldali navigációs panelen görgessen le a **figyelés** kattintson **figyelés engedélyezése**.

   ![Engedélyezze a monitorozást](./media/search-monitor-usage/enable-monitoring.png "figyelés engedélyezése")

3. Válassza ki az exportálandó adatokat: Naplók, mérőszámok vagy mindkettőt. Másolja a tárfiókhoz, küldje el az eseményközpontok felé, vagy exportálhatja, és a Log Analytics.

   A Blob storage való archiválás, a storage-fiók léteznie kell. Tárolók és blobok Teljesítménynapló-adatok exportálásakor létrejön.

   ![Konfigurálás a blob storage archív](./media/search-monitor-usage/configure-blob-storage-archive.png "konfigurálása a blob storage-archívum")

4. A profil mentéséhez.

5. Teszt naplózása létrehozásával vagy objektumok törlése (a egy működési naplót hoz létre) és a lekérdezések elküldése (a metrikákat hoz létre). 

Naplózás engedélyezve van, ha a profil mentéséhez, a tárolók csak akkor hoz létre egy esemény napló vagy az mértékcsoport esetén. A tárolók jelennek meg több percet is igénybe vehet. Is [megjelenítheti az adatokat a Power bi-ban](#analyze-with-power-bi) amint elérhetővé válik.

Amikor a rendszer az adatokat másolja egy tárfiókba, az adatok JSON-ként formázott és két tárolókban elhelyezett:

* insights-logs-operationlogs: a keresési forgalmi naplók
* insights-mérőszámok – pt1m: metrikák

Nincs óránként, a tároló és a egy blob.

Példa az elérési útra: `resourceId=/subscriptions/<subscriptionID>/resourcegroups/<resourceGroupName>/providers/microsoft.search/searchservices/<searchServiceName>/y=2018/m=12/d=25/h=01/m=00/name=PT1H.json`

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

## <a name="analyze-with-power-bi"></a>Elemzés a Power bi-ban

Azt javasoljuk, [Power BI](https://powerbi.microsoft.com) feltárhatja és megjelenítheti az adatokat, különösen akkor, ha engedélyezte a [forgalmi elemzések keresése](search-traffic-analytics.md). További információkért lásd: a [tartalomcsomag súgóoldalán](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-search/).

Kapcsolatokhoz a tárfiók nevet és hozzáférési kulcsát, amely az Azure portál oldalainak szerezhet a **hozzáférési kulcsok** a storage-fiók irányítópult lapja.

1. Telepítse a [Power BI-tartalomcsomag](https://app.powerbi.com/getdata/services/azure-search). A tartalomcsomag hozzáadja az előre meghatározott diagramok és táblázatok forgalmi elemzések kereséséhez rögzített további adatelemzés számára hasznos. 

   Ha a Blob storage vagy egy másik tárolási mechanizmus használata, és nem adott hozzá a kódot, rendszerállapot, a tartalomcsomag kihagyhatja, és a beépített Power BI-Vizualizációk.

2. Nyissa meg **Power BI**, kattintson a **adatok lekérése** > **szolgáltatások** > **Azure Search**.

3. Adja meg a storage-fiók, jelölje be a nevét **kulcs** a hitelesítéshez, és illessze a hozzáférési kulcs.

4. Importálja az adatokat, és kattintson a **adatainak megtekintéséhez**.

A következő képernyőképen látható a beépített jelentéseket, és elemzésére diagramok keresse meg a traffic analytics.

![A Power BI-irányítópultot az Azure Search](./media/search-monitor-usage/AzureSearch-PowerBI-Dashboard.png "Power BI-irányítópultot az Azure Search")

## <a name="get-sys-info-apis"></a>Sys-adatok API-k lekérése
Az Azure Search REST API és a .NET SDK-val programozott hozzáférést biztosítanak a szolgáltatási metrikák, index és indexelő adatai, és a dokumentumok számát.

* [Szolgáltatások statisztikájának beolvasása](/rest/api/searchservice/get-service-statistics)
* [Index statisztikájának beolvasása](/rest/api/searchservice/get-index-statistics)
* [Dokumentumok száma](/rest/api/searchservice/count-documents)
* [Az indexelő állapotának beolvasása](/rest/api/searchservice/get-indexer-status)

A PowerShell vagy az Azure CLI használatával engedélyezéséhez tekintse a dokumentáció [Itt](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs#how-to-enable-collection-of-diagnostic-logs).

## <a name="next-steps"></a>További lépések

[A Microsoft Azure Search szolgáltatás kezelése](search-manage.md) további információt a szolgáltatások felügyeletével és [teljesítmény és optimalizálás](search-performance-optimization.md) finomhangolási útmutató a.

További információ a lenyűgöző jelentések létrehozásához. Lásd: [Ismerkedés a Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/) részleteiről.

