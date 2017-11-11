---
title: "Használati és az Azure Search szolgáltatás statisztikáinak figyeléséhez |} Microsoft Docs"
description: "Erőforrás-felhasználás és index mérete nyomon az Azure Search, egy üzemeltetett felhőalapú keresőszolgáltatás, a Microsoft Azure."
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: 
ms.service: search
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 11/09/2017
ms.author: heidist
ms.openlocfilehash: fe852afedfc1cce99d81b8ab53c6c80df34ac6d6
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/10/2017
---
# <a name="monitoring-an-azure-search-service"></a>Egy Azure Search szolgáltatás figyelése

Az Azure Search használati és teljesítményadatokat keresési szolgáltatások nyomon követése különböző erőforrások kínál. Hozzáférést biztosít metrikákat, naplók, index statisztikák és a Power BI kiterjesztett figyelési képességekkel. Ez a cikk ismerteti a különböző megfigyelési stratégiák engedélyezése és az eredményül kapott adatok értelmezése.

## <a name="azure-search-metrics"></a>Az Azure Search metrikák
Metrikák közel valós idejű információkat a keresőszolgáltatása biztosítanak, és elérhető összes szolgáltatáshoz, további beállítás nélkül. Ezek segítségével nyomon követhető a szolgáltatás akár 30 napig teljesítményére.

Az Azure Search számára gyűjti az adatokat három különböző metrikák:

* Keresési késés: a keresési szolgáltatás keresési lekérdezések percenként összesítve feldolgozásához szükséges időt.
* / Másodperc (QPS) keresőkifejezések: keresési száma percenként összesítve másodpercenként fogadott lekérdezések.
* Szabályozottan halmozott keresési lekérdezések százalékos aránya: percenként összesítve volt szabályozva, keresési lekérdezések aránya.

![Képernyőfelvétel a QPS tevékenység][1]

### <a name="set-up-alerts"></a>Riasztások beállítása
A metrika részletek lapról konfigurálhat való e-mailben értesítést vagy automatikus műveletek metrika ebbe a megadott küszöbértéket.

Metrikák kapcsolatos további információkért ellenőrizze a teljes Azure-figyelő dokumentációja.  

## <a name="how-to-track-resource-usage"></a>Hogyan erőforrás-használat követése
Indexek és a dokumentum mérete tartásához követési segítségével proaktív módon állítsa be a kapacitás elérte-e a felső határ, a szolgáltatás létrehozása előtt. Ehhez a portálon vagy programozottan a REST API használatával.

### <a name="using-the-portal"></a>A portál használata

Erőforrás-használat figyelését, számok és a szolgáltatás a statisztikáit megtekintése a [portal](https://portal.azure.com).

1. Jelentkezzen be a [portal](https://portal.azure.com).
2. Nyissa meg az Azure Search szolgáltatás irányítópultját. A szolgáltatás csempék kezdőlapján található, vagy tallózással is kikeresheti azt a szolgáltatást a az Ugrósávon tallózással.

A használati szakasz tartalmazza azokat a mérő jelzi, hogy milyen részére rendelkezésre álló erőforrások jelenleg használatban van. Szolgáltatás korlátozások az indexet, dokumentumokat és a tárolási információkért lásd: [szolgáltatási korlátait](search-limits-quotas-capacity.md).

  ![Használata csempe][2]

> [!NOTE]
> A fenti képernyőfelvételen a szabad szolgáltatást, amely legfeljebb egy másodpéldány és particionálása, minden egyes, és csak 3 állomás indexek, 10 000 dokumentumokat vagy adatokat 50 MB lehet az, amelyik előbb következik be. Létrehozás dátuma: Basic vagy Standard szint szolgáltatások sokkal nagyobb szolgáltatásra vonatkozó korlátozások rendelkeznek. A réteg kiválasztásáról további információkért lásd: [válasszon egy réteghez vagy SKU](search-sku-tier.md).
>
>

### <a name="using-the-rest-api"></a>A REST API használatával
Az Azure Search REST API, mind a .NET SDK szolgáltatás metrikák programozott hozzáférést biztosítanak.  Ha használ [indexelők](https://msdn.microsoft.com/library/azure/dn946891.aspx) index betöltése az Azure SQL Database vagy az Azure Cosmos DB, egy további API érhető el van szüksége a számok eléréséhez.

* [Megtekintheti a statisztikákat Index](/rest/api/searchservice/get-index-statistics)
* [A dokumentumok száma](/rest/api/searchservice/count-documents)
* [Az indexelő állapotának beolvasása](/rest/api/searchservice/get-indexer-status)

## <a name="how-to-export-logs-and-metrics"></a>Naplók és a metrikák exportálása

A műveletnaplók exportálhatja a szolgáltatás és a metrikák az előző szakaszban leírt nyers adatok. A műveletnaplókat lehetővé teszik tudja hogyan használják a szolgáltatás, és képes használni a Power BI-ból adatok tárfiókba másolása során. Az Azure search biztosítja a figyelési Power BI-tartalomcsomag erre a célra.


### <a name="enabling-monitoring"></a>Figyelés engedélyezése
Nyissa meg az Azure Search szolgáltatás található a [Azure-portálon](http://portal.azure.com) a a figyelés engedélyezése beállítás.

Az exportálni kívánt adatok kiválasztásához: naplók, metrikák vagy mindkettőt. Másolja a tárfiók, elküldi a eseményközpontban, vagy Naplóelemzési exportálni onnan.

![A portál figyelés engedélyezése][3]

Engedélyezi a PowerShell vagy az Azure parancssori felület használatával, olvassa el a dokumentációt [Itt](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs#how-to-enable-collection-of-diagnostic-logs).

### <a name="logs-and-metrics-schemas"></a>Naplók és a metrikák sémák
Az adatok tárfiókba másolását követően az adatokat két tárolók JSON és annak helyként van formázva:

* insights-logs-operationlogs: a keresési forgalmi naplók
* insights-metrikák-pt1m: metrikáihoz

Egy blob tároló száma óránként van.

Példa elérési útja:`resourceId=/subscriptions/<subscriptionID>/resourcegroups/<resourceGroupName>/providers/microsoft.search/searchservices/<searchServiceName>/y=2015/m=12/d=25/h=01/m=00/name=PT1H.json`

#### <a name="log-schema"></a>Séma
A naplókat blobok a keresési szolgáltatás forgalmi naplók tartalmazzák.
Minden egyes blob tartozik egy legfelső szintű objektum nevű **rekordok** , amely tartalmazza a napló objektumokból álló tömb.
Minden egyes blob rekordok rendelkezzen a művelet, amely során az azonos órához került sor.

| Név | Típus | Példa | Megjegyzések |
| --- | --- | --- | --- |
| time |Dátum és idő |"2015-12-07T00:00:43.6872559Z" |A művelet időbélyegzője |
| resourceId |Karakterlánc |"/ SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111 /<br/>RESOURCEGROUPS/ALAPÉRTELMEZETT/SZOLGÁLTATÓK /<br/> MICROSOFT. KERESÉSI/SEARCHSERVICES/SEARCHSERVICE" |Az erőforrás-azonosítója |
| operationName |Karakterlánc |"Query.Search" |A művelet neve |
| operationVersion |Karakterlánc |"2015-02-28" |A használt api-verzió |
| category |Karakterlánc |"OperationLogs" |állandó |
| resultType |Karakterlánc |"Sikeres" |A lehetséges értékek: sikeres vagy sikertelen volt |
| resultSignature |int |200 |HTTP eredménykódja |
| durationMS |int |50 |A művelet ezredmásodpercben időtartama |
| properties |Objektum |az alábbi táblázat |A művelet vonatkozó adatokat tartalmazó objektum |

**Tulajdonságok séma**
| Név | Típus | Példa | Megjegyzések |
| --- | --- | --- | --- |
| Leírás |Karakterlánc |"/Indexes('content')/docs beolvasása" |A művelet végpont |
| Lekérdezés |Karakterlánc |"? keresési = AzureSearch & $count = true & api-version = 2015-02-28" |A lekérdezés-paraméterek |
| Dokumentumok |int |42 |Feldolgozott dokumentumok száma |
| indexName |Karakterlánc |"testindex" |A művelethez társított index nevét |

#### <a name="metrics-schema"></a>Metrikák séma
| Név | Típus | Példa | Megjegyzések |
| --- | --- | --- | --- |
| resourceId |Karakterlánc |"/ SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111 /<br/>RESOURCEGROUPS/ALAPÉRTELMEZETT/SZOLGÁLTATÓK /<br/>MICROSOFT. KERESÉSI/SEARCHSERVICES/SEARCHSERVICE" |az erőforrás-azonosító |
| metricName |Karakterlánc |"Várakozási" |a mérték neve |
| time |Dátum és idő |"2015-12-07T00:00:43.6872559Z" |a művelet időbélyeg |
| Átlagos |int |64 |A nyers minták a metrika időközben átlagos érték |
| minimális |int |37 |A minimális érték a metrika időközben nyers minták |
| Maximális |int |78 |A maximális érték a metrika időközben nyers minták |
| összesen |int |258 |A nyers minták a metrika időközben teljes értéke |
| Száma |int |4 |A metrika létrehozásához használt nyers minták száma |
| időkeretben vannak |Karakterlánc |"PT1M" |A metrika az ISO 8601 metrikaindítójának aggregációs időköze |

Minden metrikák egy perces időközönként jelenti. Minden mérőszám minimális, maximális és átlagos száma percenként értékeket.

A SearchQueriesPerSecond metrika minimális érték a legalacsonyabb, hogy percben regisztrált másodpercenként keresési lekérdezések. Ugyanez érvényes a maximális érték. A teljes perc átlagos, nem az összesítést.
Gondoljon egy perc alatt erről a forgatókönyvről: egy második, a magas be, amely a legnagyobb SearchQueriesPerSecond 58 másodperc átlagos terhelés, kiegészítve a, és végül egy második csak egy lekérdezéssel, vagyis a minimális.

ThrottledSearchQueriesPercentage, a minimális, maximális, átlagos és a teljes, az összes is ugyanazt az értéket: volt szabályozva, egy perc alatt keresési lekérdezések teljes száma a keresési lekérdezések aránya.

## <a name="analyzing-your-data-with-power-bi"></a>Az adatokat a Power BI elemzése

Azt javasoljuk, [Power BI](https://powerbi.microsoft.com) vizsgálatát, és az adatok megjelenítése. Egyszerűen csatlakoztassa az Azure Storage-fiókot, és gyorsan indítsa el az adatok elemzése.

Az Azure Search biztosít egy [Power BI-tartalomcsomag](https://app.powerbi.com/getdata/services/azure-search) , ami lehetővé teszi a figyelheti és a keresési forgalom előre definiált diagramok és táblák ismertetése. A Power BI-jelentések, amelyek automatikusan kapcsolódhat adataihoz, és adja meg a keresési szolgáltatás visual észrevételeket tartalmaz. További információkért lásd: a [tartalomcsomag súgólap](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-search/).

![Az Azure Search Power BI-irányítópulton][4]

## <a name="next-steps"></a>Következő lépések
Felülvizsgálati [méretezni a replikák és a partíciók](search-limits-quotas-capacity.md) kapcsolatos útmutatás partíciókat és a meglévő szolgáltatás replikáit kiosztásáért elosztása érdekében.

Látogasson el [kezelése a Microsoft Azure – keresés szolgáltatást](search-manage.md) további információt a felügyeleti szolgáltatás, vagy [teljesítmény- és optimalizálási](search-performance-optimization.md) a finomhangoláshoz útmutatást.

További tudnivalók elképesztő jelentések létrehozásához. Lásd: [első lépések a Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/) részletek

<!--Image references-->
[1]: ./media/search-monitor-usage/AzSearch-Monitor-BarChart.PNG
[2]: ./media/search-monitor-usage/AzureSearch-Monitor1.PNG
[3]: ./media/search-monitor-usage/AzureSearch-Enable-Monitoring.PNG
[4]: ./media/search-monitor-usage/AzureSearch-PowerBI-Dashboard.png
