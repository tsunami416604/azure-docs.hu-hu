---
title: Használat és statisztikák egy keresési szolgáltatás – Azure Search figyelése
description: Nyomon követheti az erőforrás-felhasználás, és az index mérete az Azure Search, egy üzemeltetett felhőalapú keresési szolgáltatás a Microsoft Azure-ban.
author: HeidiSteen
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.devlang: na
ms.topic: conceptual
ms.date: 11/09/2017
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: aaeb24b836b47f72d0be299738e6c90f599f8d1f
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2018
ms.locfileid: "53631895"
---
# <a name="monitor-an-azure-search-service-in-azure-portal"></a>Az Azure portal Azure Search szolgáltatás figyelése

Az Azure Search keresési szolgáltatások teljesítményét és kihasználtságát követési különböző erőforrásokat kínál. Hozzáférést biztosít a metrikák, naplók, indexstatisztikáit és kiterjesztett figyelési képességekkel a Power bi-ban. Ez a cikk ismerteti a különböző figyelési stratégiái engedélyezése és az eredményül kapott adatok értelmezése.

## <a name="azure-search-metrics"></a>Az Azure Search-metrikák
Metrikák közel valós idejű betekintést a keresési szolgáltatás biztosít, és minden szolgáltatás, amelynek nincs további beállítások érhetők el. Segítségével akár 30 napig a service teljesítményének nyomon követésével.

Az Azure Search számára gyűjti az adatokat három különböző metrikák:

* Keresési késés: A keresési szolgáltatás keresési lekérdezéseket, percenként összesítve feldolgozásához szükséges időt.
* Keresési lekérdezések másodpercenkénti (lekérdezési QPS): Keresés száma percenként összesítve másodpercenként fogadott lekérdezések.
* Szabályozott lekérdezések százalékos aránya: Keresési lekérdezések voltak szabályozva, percenként összesítve, százalékos értéke.

![Képernyőkép a QPS tevékenység][1]

### <a name="set-up-alerts"></a>Riasztások beállítása
A metrika részletei lapon konfigurálhatja a riasztások e-mailben értesítést, vagy automatikus műveletek indításához, ha egy metrika átlépi a küszöbértéket, megadott.

Metrikák kapcsolatos további információkért ellenőrizze a teljes Azure Monitor dokumentációja.  

## <a name="how-to-track-resource-usage"></a>Erőforrás-használat nyomon követését
Indexek és dokumentumok méretétől növekedését követési segítségével proaktív módon módosítsa a kapacitását előtt szerezze meg a szolgáltatás számára létrehozott felső határát. Ez a portálon vagy a REST API-val programozott módon teheti meg.

### <a name="using-the-portal"></a>A portál használata

Erőforrás-használat figyelése, tekintse meg a számát, és a szolgáltatásra vonatkozó statisztikák a [portál](https://portal.azure.com).

1. Jelentkezzen be a [portálra](https://portal.azure.com).
2. Nyissa meg az Azure Search szolgáltatás irányítópultját. A kezdőlapon található csempék a szolgáltatás, vagy tallózással az Ugrósávon a szolgáltatás megnyithatja.

Használat szakasz foglalja össze a mérőszám, amely közli, hogy milyen rendelkezésre álló erőforrások részének jelenleg használatban van. Szolgáltatási korlátok indexek, a dokumentumok és a tárolási kapcsolatos tudnivalókat lásd: [szolgáltatási korlátozásaival](search-limits-quotas-capacity.md).

  ![Használat csempe][2]

> [!NOTE]
> A fenti képernyőképen az ingyenes szolgáltatás, amely legfeljebb egy replika és a mindegyik partíció, és csak 3 gazdagép indexeket, 10 000 dokumentum vagy 50 MB adatot az, amelyiket hamarabb. Létrehozott egy alap vagy Standard csomagban szolgáltatások sokkal nagyobb, szolgáltatási korlátokkal rendelkeznek. A rétegek kiválasztásáról további információkért lásd: [szint vagy SKU kiválasztása](search-sku-tier.md).
>
>

### <a name="using-the-rest-api"></a>A REST API használata
Az Azure Search REST API és a .NET SDK-val programozott hozzáférést biztosítanak a szolgáltatások mérőszámait.  Ha használ [indexelők](https://msdn.microsoft.com/library/azure/dn946891.aspx) index betöltése az Azure SQL Database vagy Azure Cosmos DB, egy további API érhető el az első van szüksége a számokat.

* [Index statisztikájának beolvasása](/rest/api/searchservice/get-index-statistics)
* [Dokumentumok száma](/rest/api/searchservice/count-documents)
* [Az indexelő állapotának beolvasása](/rest/api/searchservice/get-indexer-status)

## <a name="how-to-export-logs-and-metrics"></a>Naplók és metrikák exportálása

A műveletnaplók exportálhatja a szolgáltatás és a metrikák az előző szakaszban leírt szükséges nyers adatok. A művelet naplók tudatják, hogyan használják a szolgáltatás, és képes használni a Power bi-BÓL adatokat másolja egy storage-fiókot. Az Azure search egy figyelési Power BI-tartalomcsomag nyújt erre a célra.


### <a name="enabling-monitoring"></a>Figyelés engedélyezése
Nyissa meg az Azure Search szolgáltatás az [az Azure portal](https://portal.azure.com) alatt a Monitorozás engedélyezése lehetőséget.

Válassza ki az exportálandó adatokat: Naplók, mérőszámok vagy mindkettőt. Másolja a tárfiókhoz, küldje el az eseményközpontok felé, vagy exportálhatja, és a Log Analytics.

![A portálon figyelés engedélyezése][3]

A PowerShell vagy az Azure CLI használatával engedélyezéséhez tekintse a dokumentáció [Itt](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs#how-to-enable-collection-of-diagnostic-logs).

### <a name="logs-and-metrics-schemas"></a>Naplók és mérőszámok sémák
Amikor a rendszer az adatokat másolja egy tárfiókba, az adatok vannak formázva, két tárolót a JSON-t és annak helye:

* insights-logs-operationlogs: a keresési forgalmi naplók
* insights-mérőszámok – pt1m: metrikák

Nincs óránként, a tároló és a egy blob.

Példa az elérési útra: `resourceId=/subscriptions/<subscriptionID>/resourcegroups/<resourceGroupName>/providers/microsoft.search/searchservices/<searchServiceName>/y=2015/m=12/d=25/h=01/m=00/name=PT1H.json`

#### <a name="log-schema"></a>Séma
A naplókat blobok tartalmazzák a keresési szolgáltatás forgalmi naplók.
Minden egyes blob van egy gyökérobjektum nevű **rekordok** log objektumok egy tömbjét tartalmazza.
Minden egyes blob rendelkezik a művelet, amely során az adott órán következett rögzíti.

| Name (Név) | Típus | Példa | Megjegyzések |
| --- | --- | --- | --- |
| time |dátum/idő |"2015-12-07T00:00:43.6872559Z" |A művelet időbélyeg |
| resourceId |sztring |"/ SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111 /<br/>RESOURCEGROUPS/ALAPÉRTELMEZETT/SZOLGÁLTATÓK /<br/> A MICROSOFT. KERESÉS/SEARCHSERVICES/SEARCHSERVICE" |Az erőforrás-azonosító |
| operationName |sztring |"Query.Search" |A művelet neve |
| operationVersion |sztring |"2015-02-28" |A használt api-verzió |
| category |sztring |"OperationLogs" |állandó |
| resultType |sztring |"Sikeres" |Érvényes értékek: Sikeres vagy sikertelen |
| resultSignature |int |200 |HTTP-eredménykód |
| durationMS |int |50 |Ennyi ezredmásodpercig tart a művelet időtartama |
| properties |objektum |az alábbi táblázatban foglaltuk össze |A művelet-specifikus adatokat tartalmazó objektum |

**Tulajdonságok séma**

| Name (Név) | Típus | Példa | Megjegyzések |
| --- | --- | --- | --- |
| Leírás |sztring |"GET /indexes('content')/docs" |A művelet végpont |
| Lekérdezés |sztring |"? keresési = AzureSearch & $count = true & api-version = 2015-02-28" |A lekérdezési paraméterek |
| Dokumentumok |int |42 |Feldolgozott dokumentumok száma |
| indexName |sztring |"testindex" |A művelethez társított az index neve |

#### <a name="metrics-schema"></a>Metrikák séma

| Name (Név) | Típus | Példa | Megjegyzések |
| --- | --- | --- | --- |
| resourceId |sztring |"/ SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111 /<br/>RESOURCEGROUPS/ALAPÉRTELMEZETT/SZOLGÁLTATÓK /<br/>A MICROSOFT. KERESÉS/SEARCHSERVICES/SEARCHSERVICE" |az erőforrás-azonosító |
| MetricName |sztring |"Késés" |a metrika neve |
| time |dátum/idő |"2015-12-07T00:00:43.6872559Z" |a művelet időbélyeg |
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

## <a name="analyzing-your-data-with-power-bi"></a>A Power BI-adatok elemzése

Azt javasoljuk, [Power BI](https://powerbi.microsoft.com) vizsgálata és-adatok megjelenítése. Könnyedén csatlakozhat az Azure Storage-fiókot, és gyorsan az adatok elemzésének megkezdéséhez.

Az Azure Search biztosít egy [Power BI-tartalomcsomag](https://app.powerbi.com/getdata/services/azure-search) , amely lehetővé teszi, hogy monitorozható és értelmezhető a keresési forgalom előre meghatározott diagramok és táblázatok. Power BI-jelentések, amelyek automatikusan csatlakozhat az adataihoz, és a keresési szolgáltatás kapcsolatos vizuális elemzést biztosítanak egy készletét tartalmazza. További információkért lásd: a [tartalomcsomag súgóoldalán](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-search/).

![A Power BI-irányítópultot az Azure Search][4]

## <a name="next-steps"></a>További lépések
Felülvizsgálat [méretezése a replikák és partíciók](search-limits-quotas-capacity.md) hogyan partíciókat és -replikákat a meglévő service lefoglalt elosztása érdekében.

Látogasson el [felügyelje keresőszolgáltatását a Microsoft Azure](search-manage.md) további információt a szolgáltatások felügyeletével, vagy [teljesítmény és optimalizálás](search-performance-optimization.md) finomhangolási útmutató a.

További információ a lenyűgöző jelentések létrehozásához. Lásd: [Ismerkedés a Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/) részletekért

<!--Image references-->
[1]: ./media/search-monitor-usage/AzSearch-Monitor-BarChart.PNG
[2]: ./media/search-monitor-usage/AzureSearch-Monitor1.PNG
[3]: ./media/search-monitor-usage/AzureSearch-Enable-Monitoring.PNG
[4]: ./media/search-monitor-usage/AzureSearch-PowerBI-Dashboard.png
