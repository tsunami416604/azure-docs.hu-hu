---
title: Azure Monitor Azure Cosmos DB metrikáinak beolvasása
description: Megtudhatja, hogyan tekintheti meg Azure Monitor Azure Cosmos DB metrikáinak különböző kategóriáit Azure Portal használatával.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/01/2019
ms.openlocfilehash: 905eca99c137af2fd40a1243de8fabd15314477c
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973745"
---
# <a name="monitor-and-debug-azure-cosmos-db-metrics-from-azure-monitor"></a>Azure Cosmos DB metrikák figyelése és hibakeresése Azure Monitor

Azure Monitor API-ból Azure Cosmos DB metrikákat tekinthet meg. A Azure Monitor számos módszert biztosít a metrikák, például a Azure Portal, a REST APIon keresztül történő hozzáférés vagy a PowerShell vagy a parancssori felület használatával történő lekérdezéséhez. Azure Cosmos DB mérőszámok alacsony késésű numerikus értékek, amelyek alapértelmezés szerint egyperces gyakorisággal vannak összegyűjtve, ezeket a mérőszámokat összesítheti is. Ezek a metrikák valós idejű forgatókönyvek támogatására alkalmasak.  

Ez a cikk a Azure Portal használatával megtekinthető Azure Monitor különböző Azure Cosmos DB metrikákat ismerteti. Ha a gyakori használati esetek érdeklik, és az ilyen problémák elemzéséhez és hibakereséséhez használt Azure Cosmos DB mérőszámok Azure Cosmos DB cikkben található [mérőszámok figyelése és hibakeresése](use-metrics.md) . A meglévő Azure Cosmos-fiókok egyikét fogja használni, és megtekintheti az adatbázis, a tároló, a régió, a kérelem vagy a műveleti szintek különböző metrikáit. Ezért győződjön meg róla, hogy rendelkezik egy Azure Cosmos-fiókkal, és végezze el az adatokon végzett SZIFILISZt.

## <a name="view-metrics-from-azure-portal"></a>Metrikák megtekintése Azure Portal

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Válassza a **figyelő** lehetőséget a bal oldali navigációs sávon, és válassza a **metrikák**lehetőséget.

   ![Metrikák ablaktábla Azure Monitor](./media/cosmos-db-azure-monitor-metrics/monitor-metrics-blade.png)

1. A **metrikák** ablaktáblán > **válasszon ki egy erőforrást** > Válassza ki a szükséges **előfizetést**és **erőforráscsoportot**. Az **erőforrástípus**mezőben válassza a **Azure Cosmos db fiókok**lehetőséget, válasszon ki egy meglévő Azure Cosmos-fiókot, majd válassza az **alkalmaz**lehetőséget. 

   ![Cosmos DB fiók kiválasztása a metrikák megtekintéséhez](./media/cosmos-db-azure-monitor-metrics/select-cosmosdb-account.png)

1. Következő lépésként kiválaszthat egy mérőszámot az elérhető metrikák listájából. Kiválaszthatja a kérelmekhez tartozó mérőszámokat, a tárolást, a késést, a rendelkezésre állást, a Cassandra és másokat. A lista összes elérhető metrikájának részletes megismeréséhez tekintse meg a jelen cikk [mérőszámok kategóriánként](#metrics-by-category) című szakaszát. Ebben a példában a **kérelmek egységeit** és az **átlagot** adja meg az összesítési értékként. 

   Ezen részletek mellett kiválaszthatja a metrikák **időtartományát** és **időrészletességét** is. A maximális értéknél megtekintheti az elmúlt 30 nap mérőszámait.  A szűrő alkalmazása után egy diagram jelenik meg a szűrő alapján. A kiválasztott időszakra vonatkozóan percenként felhasználható kérelmek átlagos számát láthatja.  

   ![Metrika kiválasztása a Azure Portal](./media/cosmos-db-azure-monitor-metrics/metric-types.png)

## <a name="add-filters-to-metrics"></a>Szűrők hozzáadása metrikához

Az adott **CollectionName**, **databasename**, **OperationType**, **régió**és **statuscode**által megjelenített mérőszámokat és diagramokat is szűrheti. A metrikák szűréséhez válassza a **szűrő hozzáadása** lehetőséget, és válassza ki a szükséges tulajdonságot (például **OperationType** ), és válasszon egy értéket (például **lekérdezés**). A gráf ezután megjeleníti a kiválasztott időszakban a lekérdezési művelethez felhasznált kérelmek egységeit. A tárolt eljáráson keresztül végrehajtott műveletek nincsenek naplózva, így azok nem érhetők el az OperationType metrika alatt.

![Szűrő hozzáadása a metrika részletességének kiválasztásához](./media/cosmos-db-azure-monitor-metrics/add-metrics-filter.png)

A metrikákat a **felosztás alkalmazása** lehetőség használatával csoportosíthatja. Például csoportosíthatja a kérelmek egységeit, és megtekintheti az összes művelet gráfját, amint az alábbi képen is látható: 

![Alkalmazás-felosztási szűrő hozzáadása](./media/cosmos-db-azure-monitor-metrics/apply-metrics-splitting.png)


## <a id="metrics-by-category"></a>Metrikák kategóriánként

### <a name="request-metrics"></a>Kérelem-metrikák
            
|Metrika (metrika megjelenítendő neve)|Egység (aggregáció típusa) |Leírás|Dimenziók| Időbeli részletesség| Örökölt metrika leképezése | Használat |
|---|---|---|---| ---| ---| ---|
| TotalRequests (összes kérelem) | Darabszám (darabszám) | Benyújtott kérelmek száma| DatabaseName, CollectionName, régió, StatusCode| Összes | TotalRequests, http-2xx, http-3xx, http 400, HTTP 401, belső kiszolgálóhiba, szolgáltatás nem érhető el, szabályozott kérelmek, átlagos kérelmek másodpercenként | Az állapotkód, a tároló percenkénti részletességgel történő figyelésére használatos. Az átlagos kérelmek másodpercenkénti lekéréséhez használja a Count összesítést percek alatt, és ossza meg a 60-as számmal. |
| MetadataRequests (metaadat-kérelmek) |Darabszám (darabszám) | A metaadat-kérelmek száma. Azure Cosmos DB minden fiókhoz rendszermetaadat-tárolót tart fenn, amely lehetővé teszi a gyűjtemények, adatbázisok stb. és azok konfigurációinak számbavételét díjmentesen. | DatabaseName, CollectionName, régió, StatusCode| Összes| |Metaadat-kérelmek miatti szabályozások figyelésére használatos.|
| MongoRequests (Mongo kérelmek) | Darabszám (darabszám) | A Mongo kérelmek száma | DatabaseName, CollectionName, régió, CommandName, ErrorCode| Összes |Mongo-lekérdezési kérelmek gyakorisága, a Mongo frissítési kérelmi aránya, a Mongo törlési kérelmi aránya, a Mongo beszúrási kérelmi aránya, a Mongo száma kérelmek aránya| Mongo-kérési hibák figyelésére használatos. |

### <a name="request-unit-metrics"></a>Adategység metrikáinak kérése

|Metrika (metrika megjelenítendő neve)|Egység (aggregáció típusa)|Leírás|Dimenziók| Időbeli részletesség| Örökölt metrika leképezése | Használat |
|---|---|---|---| ---| ---| ---|
| MongoRequestCharge (Mongo-kérelem díja) | Darabszám (összesen) |Felhasznált Mongo-kérelmek egységei| DatabaseName, CollectionName, régió, CommandName, ErrorCode| Összes |Mongo-lekérdezési kérelem díja, Mongo frissítési kérelmének díja, Mongo törlési kérelmének díja, Mongo-kérelem díja, Mongo száma kérelmek díja| Egy percen belül figyeli a Mongo-erőforrást.|
| TotalRequestUnits (összes kérelem egysége)| Darabszám (összesen) | Felhasználható kérési egységek| DatabaseName, CollectionName, régió, StatusCode |Összes| TotalRequestUnits| A teljes RU-használat figyelésére használható percenkénti részletességgel. Ha másodpercenként átlagosan felhasznált RU-t szeretné lekérni, használja a teljes összesítést percenként, és ossza meg a 60-as számmal.|
| ProvisionedThroughput (kiépített átviteli sebesség)| Darabszám (maximum) |Kiépített átviteli sebesség a tároló részletessége szerint| DatabaseName, ContainerName| 5P| | Tárolón kiépített átviteli sebesség figyelésére szolgál.|

### <a name="storage-metrics"></a>Tárolási metrikák

|Metrika (metrika megjelenítendő neve)|Egység (aggregáció típusa)|Leírás|Dimenziók| Időbeli részletesség| Örökölt metrika leképezése | Használat |
|---|---|---|---| ---| ---| ---|
| AvailableStorage (rendelkezésre álló tár) |Bájtok (összesen) | Régiónként 5 perces részletességgel jelentett teljes rendelkezésre álló tárterület| DatabaseName, CollectionName, régió| 5P| Rendelkezésre álló tár| A rendelkezésre álló tárolókapacitás (csak a rögzített tárolási gyűjtemények esetében érvényes) figyeléséhez használatos minimális részletességnek 5 percnek kell lennie.| 
| DataUsage (adathasználat) |Bájtok (összesen) |Régiónként 5 perces részletességgel jelentett teljes adatfelhasználás| DatabaseName, CollectionName, régió| 5P |Adatok mérete | A tároló és régió teljes adatfelhasználásának figyelésére szolgál, a minimális részletességnek 5 percnek kell lennie.|
| IndexUsage (használat indexelése) | Bájtok (összesen) |Az összes indexelési használat összesen 5 perces részletességgel számolt be régiónként| DatabaseName, CollectionName, régió| 5P| Index mérete| A tároló és régió teljes adatfelhasználásának figyelésére szolgál, a minimális részletességnek 5 percnek kell lennie. |
| DocumentQuota (dokumentum kvótája) | Bájtok (összesen) | Régiónként 5 perces részletességgel jelentett teljes tárolási kvóta.| DatabaseName, CollectionName, régió| 5P |Tárkapacitás| A tároló és régió teljes kvótájának figyelésére szolgál, a minimális részletességnek 5 percnek kell lennie.|
| DocumentCount (dokumentumok száma) | Darabszám (összesen) |Régiónként 5 perces részletességgel jelentett dokumentumok száma összesen| DatabaseName, CollectionName, régió| 5P |Dokumentumok száma|A dokumentumok számának a tárolóban és régióban való figyelésére szolgál, a minimális részletességnek 5 percnek kell lennie.|

### <a name="latency-metrics"></a>Késési metrikák

|Metrika (metrika megjelenítendő neve)|Egység (aggregáció típusa)|Leírás|Dimenziók| Időbeli részletesség| Használat |
|---|---|---|---| ---| ---|
| ReplicationLatency (replikációs késés)| Ezredmásodperc (minimum, maximum, átlag) | P99 replikációs késés a forrás-és a célcsoportok között a Geo-kompatibilis fiókhoz| SourceRegion, TargetRegion| Összes | Egy földrajzilag replikált fiók két régiója közötti P99-replikációs késés figyelésére használatos. |


### <a name="availability-metrics"></a>Rendelkezésre állási metrikák

|Metrika (metrika megjelenítendő neve) |Egység (aggregáció típusa)|Leírás| Időbeli részletesség| Örökölt metrika leképezése | Használat |
|---|---|---|---| ---| ---|
| ServiceAvailability (szolgáltatás rendelkezésre állása)| Százalék (minimum, maximum) | A fiók egy órás részletességgel kéri a rendelkezésre állást| 1H | Szolgáltatás rendelkezésre állása | Az átadott kérelmek százalékos arányát jelöli. A rendszer hibát okozó kérést küld, ha az állapotkód 410, 500 vagy 503, amely a fiók rendelkezésre állásának figyelésére használatos az óránkénti részletességgel. |


### <a name="cassandra-api-metrics"></a>Cassandra API metrikák

|Metrika (metrika megjelenítendő neve)|Egység (aggregáció típusa)|Leírás|Dimenziók| Időbeli részletesség| Használat |
|---|---|---|---| ---| ---|
| CassandraRequests (Cassandra-kérelmek) | Darabszám (darabszám) | Cassandra API kérelmek száma| DatabaseName, CollectionName, ErrorCode, Region, OperationType, ResourceType| Összes| A Cassandra-kérések figyelésére szolgál percenkénti részletességgel. Az átlagos kérelmek másodpercenkénti lekéréséhez használja a Count összesítést percek alatt, és ossza meg a 60-as számmal.|
| CassandraRequestCharges (Cassandra-kérelmek díjai) | Darabszám (Sum, min, Max, AVG) | Cassandra API kérelmek által felhasznált kérési egységek| DatabaseName, CollectionName, Region, OperationType, ResourceType| Összes| Egy Cassandra API fiók által percenként használt RUs figyelésére használatos.|
| CassandraConnectionClosures (Cassandra-kapcsolatok bezárása) |Darabszám (darabszám) |Lezárt Cassandra-kapcsolatok száma| ClosureReason, régió| Összes | Az ügyfelek és a Azure Cosmos DB Cassandra API közötti kapcsolat figyelésére használatos.|

## <a name="next-steps"></a>További lépések

* [Metrikák megtekintése és figyelése Azure Cosmos DB-fiók metrikái paneljéről](use-metrics.md)

* [Diagnosztikai naplózás a Azure Cosmos DBban](logging.md)
