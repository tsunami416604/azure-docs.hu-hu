---
title: Az Azure Monitor Azure Cosmos DB metrikáinak beolvasása
description: ''
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: 2eb61a6b9afa3cabf1733be120dfbdacb7de4534
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276794"
---
# <a name="monitor-and-debug-azure-cosmos-db-metrics-from-azure-monitor"></a>Figyelheti és hibakeresése az Azure Cosmos DB az Azure Monitor metrikái

Azure Cosmos DB-mérőszámok az Azure Monitor API tekintheti meg. Az Azure Monitor mérőszámokat, együttműködhet több módszert is biztosít, többek között az Azure Portalon, a hozzájuk férni a REST API-n keresztül vagy a lekérdezési őket a PowerShell vagy parancssori felület használatával. Az Azure Cosmos DB-metrikák közel valós idejű numerikus értékeket, alapértelmezés szerint gyűjtött perces gyakorisággal, ezeket a metrikákat is lehet összesíteni. Ezek a metrikák képesek a valós idejű felhasználói helyzetek támogatása.  

Ez a cikk ismerteti a különböző Azure Cosmos DB tekintheti meg az Azure Monitor metrikái Azure portal használatával. Ha érdekli a gyakori használati eseteket, és újra az Azure Cosmos DB-metrikák elemzéséhez, és ezek hibakeresési módjáról lásd: problémák [figyelése és hibakeresése az Azure Cosmos DB metrikákkal](use-metrics.md) cikk. Ön a meglévő Azure-Cosmos-fiókok valamelyikével, és az adatbázis, tároló, régió, kérelem vagy művelet szintek, a másik metrikákat tekinthet meg. Ügyeljen, hogy rendelkezik egy Azure Cosmos-fiókkal, mintaadatokkal, és az adatok CRUD-műveletek végrehajtása.

## <a name="view-metrics-from-azure-portal"></a>Metrikák megtekintése az Azure Portalról

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Válassza ki **figyelő** a bal oldali navigációs sávban, a **metrikák**.

   ![Az Azure Monitor metrikák paneljén](./media/cosmos-db-azure-monitor-metrics/monitor-metrics-blade.png)

1. Az a **metrikák** panel > **válasszon ki egy erőforrást** > Válassza ki a szükséges **előfizetés**, és **erőforráscsoport**. Az a **erőforrástípus**válassza **Azure Cosmos DB-fiókokhoz**, válasszon egyet a meglévő Azure-Cosmos-fiókoknak, és válassza ki **alkalmaz**. 

   ![Válassza ki a Cosmos DB-fiók metrikák megtekintése](./media/cosmos-db-azure-monitor-metrics/select-cosmosdb-account.png)

1. Ezután kiválaszthatja a metrika az elérhető mérőszámok listájából. Kiválaszthatja, hogy a metrikák meghatározott kérelemegység, storage, késést, rendelkezésre állást, Cassandra és mások. A listában szereplő összes rendelkezésre álló metrikák kapcsolatos részletes tudnivalókért lásd: a [metrikák kategória szerint](#metrics-by-category) című szakaszát. Ebben a példában válassza **Kérelemegységek** és **átlagos** összesítési értékeként. 

   Ezek az adatok mellett is választhat a **időtartomány** és **idő részletessége** metrikák. Maximális száma, a metrikák megtekintheti az elmúlt 30 napra vonatkozóan.  A szűrő alkalmazása után egy diagram jelenik meg a szűrő alapján. Láthatja, hogy a kijelölt időszakra vonatkozóan percenként felhasznált kérelemegységek átlagos száma.  

   ![Metrika kiválasztása az Azure Portalról](./media/cosmos-db-azure-monitor-metrics/metric-types.png)

## <a name="add-filters-to-metrics"></a>Mérőszámok szűrése

Metrikák és a egy adott szerint jelenik meg a diagramot is szűrheti **CollectionName**, **DatabaseName**, **OperationType**, **régió**, és **StatusCode**. A metrikák szűréséhez válasszon **szűrő hozzáadása** , és válassza ki például a kötelező tulajdonság **OperationType** , és válassza ki az értéket például **lekérdezés**. A diagram ekkor a a kijelölt időszakra vonatkozóan a lekérdezési művelethez felhasznált kérelemegységek jeleníti meg. A tárolt eljárás használatával végrehajtott műveletek nem jelentkezett be, így nem érhető el az OperationType metrika alapján.

![Jelölje be a metrika granularitási szűrő hozzáadása](./media/cosmos-db-azure-monitor-metrics/add-metrics-filter.png)

Metrikák használatával csoportosíthatja a **alkalmazni a felosztás** lehetőséget. Például a kérelemegység művelettípusonként csoport és a gráf összes műveletet a következő képen látható módon egyszerre megtekintése: 

![Adjon hozzá bontásával szűrő alkalmazása](./media/cosmos-db-azure-monitor-metrics/apply-metrics-splitting.png)


## <a id="metrics-by-category"></a>Metrikák kategória szerint

### <a name="request-metrics"></a>Kérelem-metrikák
            
|A metrika (metrikus megjelenített név)|Egység (aggregáció típusa) |Leírás|Dimenziók| Idő granularitással| Örökölt metrika leképezés | Használat |
|---|---|---|---| ---| ---| ---|
| TotalRequests (összes kérelem) | Count (darabszám) | Kérelmek száma| DatabaseName, CollectionName, Region, StatusCode| Összes | TotalRequests, Http 2xx, 3xx Http, a Http 400-as, a Http 401-es, belső kiszolgálóhiba szolgáltatás nem érhető el, a kérelmek szabályozva, másodpercenként átlagosan | Kérések száma állapot kódja, gyűjtemény jelenleg egy perces pontossággal figyelésére használható. A kérelmek másodpercenkénti átlagos száma az összesítés használatát perc, és nullával való osztás 60. |
| MetadataRequests (metaadat-kérelmek) |Count (darabszám) | A metaadat-kérelmek száma. Az Azure Cosmos DB tárolja a rendszer metaadat-gyűjtemény minden fiókhoz, amely lehetővé teszi, hogy számba venni a gyűjteményeket, adatbázisok, stb., és a konfigurációját, ingyenesen. | DatabaseName, CollectionName, Region, StatusCode| Összes| |A metaadat-kérelmek miatt szabályozások figyelésére használható.|
| MongoRequests (Mongo-kérelmek) | Count (darabszám) | Mongo-kérelmek száma | DatabaseName, CollectionName, Region, CommandName, ErrorCode| Összes |Mongo-lekérdezési kérelmek arányának, a Mongo-frissítési kérés arány, a Mongo kérelmek arányának törléséhez Mongo beszúrása a kérések aránya, Mongo száma kérelmek gyakorisága| Mongo-kérelem hibák nyomon követésére, a tanúsítványalgoritmusok parancsonként írja be. |

### <a name="request-unit-metrics"></a>Kérelem egységek metrikáit

|A metrika (metrikus megjelenített név)|Egység (aggregáció típusa)|Leírás|Dimenziók| Idő granularitással| Örökölt metrika leképezés | Használat |
|---|---|---|---| ---| ---| ---|
| MongoRequestCharge (Mongo kérés díj) | Száma (összesen) |Felhasznált Kérelemegységek mongo| DatabaseName, CollectionName, Region, CommandName, ErrorCode| Összes |Mongo-lekérdezés kérelem díjmentes, a Mongo frissítési kérelem díjmentes, a Mongo törlése kérelem díja, Mongo beszúrása kérelem számolunk fel, Mongo száma kérelem díja| Egy perc alatt Mongo erőforrás RUs figyelésére használható.|
| TotalRequestUnits (kérelmek teljes egység)| Száma (összesen) | Felhasznált egységek kérése| DatabaseName, CollectionName, Region, StatusCode |Összes| TotalRequestUnits| Jelenleg egy perces pontossággal teljes RU-használat figyelésére használható. Átlagos RU másodpercenként felhasznált perc teljes összesítést használ, és nullával való osztás 60.|
| ProvisionedThroughput (kiosztott átviteli sebesség)| Darabszám (Maximum) |Kiosztott átviteli sebességet gyűjtemény részletessége| DatabaseName, CollectionName| 5M| | Kiosztott átviteli sebesség gyűjteményenként figyelésére használható.|

### <a name="storage-metrics"></a>Storage-mérőszámok

|A metrika (metrikus megjelenített név)|Egység (aggregáció típusa)|Leírás|Dimenziók| Idő granularitással| Örökölt metrika leképezés | Használat |
|---|---|---|---| ---| ---| ---|
| AvailableStorage (rendelkezésre álló tár) |Bájtok száma (összesen) | 5 percenként részletesség régiónként, jelentett összes rendelkezésre álló tár| DatabaseName, CollectionName, régió| 5M| Rendelkezésre álló tár| Figyelhető a rendelkezésre álló tár kapacitása (csak a rögzített tároló gyűjtemények alkalmazható) minimális részletessége 5 percnek kell lennie.| 
| DataUsage (használati adatok) |Bájtok száma (összesen) |Teljes adathasználat jelenteni 5 percenként granularitási régiónként| DatabaseName, CollectionName, régió| 5M |Adatok mérete | Gyűjtemény és a régió teljes adathasználat figyelésére használható, minimális részletessége 5 percnek kell lennie.|
| IndexUsage (Index-használat) | Bájtok száma (összesen) |5 percenként granularitási régiónként jelenteni teljes Index használata| DatabaseName, CollectionName, régió| 5M| Index mérete| Gyűjtemény és a régió teljes adathasználat figyelésére használható, minimális részletessége 5 percnek kell lennie. |
| DocumentQuota (a dokumentum kvóta) | Bájtok száma (összesen) | Teljes tárolási kvótája régiónként 5 percenként granularitási jelenteni.| DatabaseName, CollectionName, régió| 5M |Tárkapacitás| Gyűjtemény és a régió teljes kvóta figyelésére használható, minimális részletessége 5 percnek kell lennie.|
| DocumentCount (dokumentumok száma) | Száma (összesen) |Teljes számát jelenti, 5 percenként granularitási régiónként| DatabaseName, CollectionName, régió| 5M |Dokumentumok száma|Dokumentumok száma, gyűjtemény és a régió figyelésére használható, minimális részletessége 5 percnek kell lennie.|

### <a name="latency-metrics"></a>Késés metrikák

|A metrika (metrikus megjelenített név)|Egység (aggregáció típusa)|Leírás|Dimenziók| Idő granularitással| Használat |
|---|---|---|---| ---| ---|
| ReplicationLatency (replikáció késése)| Idő ezredmásodpercben (Minimum, Maximum, átlag) | Fiók geo-kompatibilis a forrás- és régiók közötti P99 replikáció késése| SourceRegion, TargetRegion| Összes | Egy georeplikált fiók bármely két régió között P99 replikációs késésétől figyelésére használható. |


### <a name="availability-metrics"></a>Rendelkezésre állási metrikák

|A metrika (metrikus megjelenített név) |Egység (aggregáció típusa)|Leírás| Idő granularitással| Örökölt metrika leképezés | Használat |
|---|---|---|---| ---| ---|
| ServiceAvailability (a szolgáltatás rendelkezésre állása)| Százalék (Minimum, Maximum) | Egy órás részletességgel fiók kérelmek rendelkezésre állásáról| 1H | Szolgáltatás rendelkezésre állása | Az átadott kérelmek teljes összeg százalékában jelöli. Rendszerhiba miatt kell végrehajtani, ha az állapotkód értéke 410, tekinthető a kérelem 500 vagy 503-as használható a fiókját órás részletességgel rendelkezésre állásának figyeléséhez. |


### <a name="cassandra-api-metrics"></a>Cassandra API-metrikák

|A metrika (metrikus megjelenített név)|Egység (aggregáció típusa)|Leírás|Dimenziók| Idő granularitással| Használat |
|---|---|---|---| ---| ---|
| CassandraRequests (Cassandra Requests) | Count (darabszám) | Cassandra API-kérelmek száma| DatabaseName, CollectionName, ErrorCode, Region, OperationType, ResourceType| Összes| Jelenleg egy perces pontossággal Cassandra kérelmek figyelésére használható. A kérelmek másodpercenkénti átlagos száma az összesítés használatát perc, és nullával való osztás 60.|
| CassandraRequestCharges (Cassandra-kérelem díjon felül) | (Sum, Min, Max, Avg) száma | Cassandra API-kérések által felhasznált kérelemegységek| DatabaseName, CollectionName, Region, OperationType, ResourceType| Összes| Kérelemegység / perc a Cassandra API-fiók által használt figyelésére használható.|
| CassandraConnectionClosures (Cassandra kapcsolat szünnapok) |Count (darabszám) |Lezárt Cassandra-kapcsolatok száma| ClosureReason, régió| Összes | Az ügyfélszámítógépek és az Azure Cosmos DB Cassandra API közötti kapcsolatok figyelésére használható.|

## <a name="next-steps"></a>További lépések

* [Azure Cosmos DB-fiók metrikák panelről megtekintheti és figyelheti metrikák](use-metrics.md)

* [Diagnosztikai naplózás az Azure Cosmos DB-ben](logging.md)
