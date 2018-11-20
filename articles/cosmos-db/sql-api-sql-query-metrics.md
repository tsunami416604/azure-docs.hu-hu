---
title: SQL-lekérdezés metrikák az Azure Cosmos DB SQL API-hoz |} A Microsoft Docs
description: Ismerje meg, alakítsa ki és hibakeresése az Azure Cosmos DB-kérelmek SQL-lekérdezések teljesítményével kapcsolatos.
keywords: SQL-szintaxis, sql-lekérdezés, sql-lekérdezéseket, json lekérdezési nyelvet, adatbázis-tervezésben és sql-lekérdezések aggregátumfüggvények
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: na
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: sngun
ms.openlocfilehash: 9358e0a712f820671edec518b1cc93ecee5302ad
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2018
ms.locfileid: "52162519"
---
# <a name="tuning-query-performance-with-azure-cosmos-db"></a>Az Azure Cosmos DB lekérdezési teljesítmény hangolása

Az Azure Cosmos DB biztosítja a [adatok lekérdezése az SQL API](how-to-sql-query.md), anélkül, hogy a séma vagy másodlagos indexek. Ez a cikk a fejlesztők a következő információkat biztosítja:

* Azure Cosmos DB SQL-lekérdezésének végrehajtása működésével kapcsolatos részletes
* Részletes lekérdezési kérelmek és válaszfejlécek, és az ügyfél SDK-beállítások
* Tippek és gyakorlati tanácsok a lekérdezési teljesítmény
* Példa bemutatja, hogyan használják a lekérdezési teljesítmény hibakeresése SQL végrehajtási statisztika

## <a name="about-sql-query-execution"></a>Tudnivalók az SQL-lekérdezés végrehajtása

Az Azure Cosmos DB, tárolók, amelyek bármely növelhető az adatok tárolására [mérete vagy a kérelem tárolóteljesítmény](partition-data.md). Az Azure Cosmos DB zökkenőmentesen skálázható adatok valójában az adatmennyiség növekedésének kezeléséhez, vagy pedig növelje a kiosztott átviteli sebesség fizikai partíciók között. SQL-lekérdezések használatával, a REST API vagy a támogatott valamelyik tárolója sem számára küldhet [SQL SDK-k](sql-api-sdk-dotnet.md).

A particionálás rövid áttekintése: megadhat egy partíciókulcsot, például a "city", amely megadja, hogy hogyan van felosztva az adatok fizikai partíciók között. Önálló partíciókulcsokon tartozó adatok (például a "city" == "Seattle") egy fizikai partíciónak vannak tárolva, de általában egyetlen fizikai partíciók több partíciós kulccsal. Partíció mérete eléri, ha a szolgáltatás zökkenőmentesen a partíció felosztja a két új partíciót és a partíciókulcs egyenletesen elosztja érintett partíciók között. Mivel a partíciók átmeneti, az API-k használata egy "partíciókulcs-tartományok", amely azt jelzi, hogy a partíciós kulcs kivonatok adattartományokat absztrakciója. 

Amikor egy lekérdezést az Azure Cosmos DB, az SDK logikai lépéseket hajtja végre:

* Elemezni az SQL-lekérdezést a lekérdezés-végrehajtási terv meghatározásához. 
* Ha a lekérdezés a partíciókulcs alapján szűrőt tartalmaz, például `SELECT * FROM c WHERE c.city = "Seattle"`, ugyanazon a partíción lesz irányítva. Ha a lekérdezés nem rendelkezik egy szűrőt a partíciókulcsot, majd az összes partíció hajtja végre és eredményeket egyesítésekor ügyféloldali.
* A lekérdezés végrehajtása a-sorozat minden egyes partíción belül, vagy a párhuzamosan, az ügyfél-konfiguráció alapján. Minden egyes partíción belül előfordulhat, hogy a lekérdezés egyet, vagy a lekérdezés összetettségétől függően további adatváltások oldalméret konfigurálva, és a gyűjtemény átviteli sebesség kiosztott. Minden egyes végrehajtása számát adja vissza [kérelemegységek](request-units.md) felhasznált lekérdezés-végrehajtás, és ha szükséges, lekérdezés-végrehajtási statisztikák. 
* Az SDK-t több partícióra kiterjedő hajt végre egy a lekérdezés eredményeinek összegzése. Például egy ORDER BY záradék a lekérdezés több partícióra kiterjedő foglal magában, majd az egyes partíciók eredményeinek-e egyesítési rendezve, globálisan rendezett sorrendben adja vissza az eredményeket. Ha a lekérdezés egy összesítés, mint például `COUNT`, a számok az egyes partíciók összeadódnak előállításához a teljes száma.

Az SDK-k a lekérdezés-végrehajtáshoz különböző lehetőségeket nyújtanak. Ha például a .NET-ben ezek a lehetőségek érhetők el a `FeedOptions` osztály. A következő táblázat ismerteti ezeket a lehetőségeket, hogy azok hatással lekérdezés végrehajtási ideje. 

| Beállítás | Leírás |
| ------ | ----------- |
| `EnableCrossPartitionQuery` | Állítsa true esetében, amelyek lekérdezési feltétele, hogy egynél több partíció között hajtható végre. Ez az egy explicit jelzőt, hogy tudatában teljesítmény kompromisszumokat fejlesztési idő alatt. |
| `EnableScanInQuery` | Állítsa igaz értékre, ha az indexelés visszavonta, de szeretné futtatni a lekérdezést egy vizsgálattal ennek ellenére. Csak alkalmazható, ha a kért szűrő elérési útja indexelő le van tiltva. | 
| `MaxItemCount` | Térjen vissza adatváltási száma a kiszolgáló elemek maximális számát. -1-beállítása, hagyhatja, hogy a kiszolgáló kezelése elemek száma. Vagy ezt az értéket csak kis számú tétel / adatváltási beolvasása csökkentheti. 
| `MaxBufferedItemCount` | Ez egy ügyféloldali beállítást, és korlátozhatja a memóriát a partíciók közti ORDER BY végrehajtása során. Magasabb érték segítségével csökkentse a partíciók közti rendezéséhez. |
| `MaxDegreeOfParallelism` | Lekérdezi vagy beállítja az ügyféloldali futnak az Azure Cosmos DB adatbázis-szolgáltatás a párhuzamos lekérdezés-végrehajtás során párhuzamos műveletek számát. Egy pozitív tulajdonság értéke a értékét párhuzamos műveletek számát korlátozza. Ha 0-nál kisebb érték, a rendszer automatikusan úgy dönt, hogy futtassa a párhuzamos műveletek számát. |
| `PopulateQueryMetrics` | Lehetővé teszi, hogy a lekérdezés végrehajtása, például a fordítási idő, index hurok idő és a dokumentum különböző fázisait fordított idő statisztika részletes naplózás betöltési idő. Lekérdezési statisztikák kimenete megoszthatja lekérdezés teljesítménybeli problémák diagnosztizálása az Azure-támogatás. |
| `RequestContinuation` | A lekérdezés végrehajtása révén minden lekérdezés által visszaadott folytatási átlátszatlan folytathatja. A folytatási token magában foglalja az összes állapot, a lekérdezés-végrehajtáshoz szükséges. |
| `ResponseContinuationTokenLimitInKb` | Korlátozhatja a kiszolgáló által visszaadott folytatási tokent maximális méretét. Szüksége lehet állítsa ezt, ha az alkalmazás gazdagép korlátok a válaszok fejlécének mérete. Ezt a beállítást a teljes időtartam és a lekérdezés felhasznált Kérelemegységek növelheti.  |

Például, vessünk egy példalekérdezést a partíciókulccsal rendelkező gyűjtemény kért `/city` kulcs és a 100 000 RU/s átviteli sebesség kiosztott partícióként. A lekérdezés használatával kér `CreateDocumentQuery<T>` a .NET-ben például a következőképpen:

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        PopulateQueryMetrics = true, 
        MaxItemCount = -1, 
        MaxDegreeOfParallelism = -1, 
        EnableCrossPartitionQuery = true 
    }).AsDocumentQuery();

FeedResponse<dynamic> result = await query.ExecuteNextAsync();
```

A fenti SDK kódrészlet felel meg a következő REST API-kérelem:

```
POST https://arramacquerymetrics-westus.documents.azure.com/dbs/db/colls/sample/docs HTTP/1.1
x-ms-continuation: 
x-ms-documentdb-isquery: True
x-ms-max-item-count: -1
x-ms-documentdb-query-enablecrosspartition: True
x-ms-documentdb-query-parallelizecrosspartitionquery: True
x-ms-documentdb-query-iscontinuationexpected: True
x-ms-documentdb-populatequerymetrics: True
x-ms-date: Tue, 27 Jun 2017 21:52:18 GMT
authorization: type%3dmaster%26ver%3d1.0%26sig%3drp1Hi83Y8aVV5V6LzZ6xhtQVXRAMz0WNMnUuvriUv%2b4%3d
x-ms-session-token: 7:8,6:2008,5:8,4:2008,3:8,2:2008,1:8,0:8,9:8,8:4008
Cache-Control: no-cache
x-ms-consistency-level: Session
User-Agent: documentdb-dotnet-sdk/1.14.1 Host/32-bit MicrosoftWindowsNT/6.2.9200.0
x-ms-version: 2017-02-22
Accept: application/json
Content-Type: application/query+json
Host: arramacquerymetrics-westus.documents.azure.com
Content-Length: 52
Expect: 100-continue

{"query":"SELECT * FROM c WHERE c.city = 'Seattle'"}
```

REST API-val megfelel minden lekérdezés-végrehajtás lapon `POST` együtt a `Accept: application/query+json` fejlécére, majd az SQL-lekérdezést a törzsében. Egyes lekérdezések lehetővé teszi egy vagy több kerekíteni lelassítja a kiszolgálóra a `x-ms-continuation` jogkivonat végrehajtási folytatásához az ügyfél és kiszolgáló közötti meg. A konfigurációs beállítások FeedOptions továbbítódnak a kiszolgáló kérelemfejlécek formájában. Ha például `MaxItemCount` felel meg `x-ms-max-item-count`. 

A kérelem a következő (az olvashatóság érdekében csonkolva) választ ad vissza:

```
HTTP/1.1 200 Ok
Cache-Control: no-store, no-cache
Pragma: no-cache
Transfer-Encoding: chunked
Content-Type: application/json
Server: Microsoft-HTTPAPI/2.0
Strict-Transport-Security: max-age=31536000
x-ms-last-state-change-utc: Tue, 27 Jun 2017 21:01:57.561 GMT
x-ms-resource-quota: documentSize=10240;documentsSize=10485760;documentsCount=-1;collectionSize=10485760;
x-ms-resource-usage: documentSize=1;documentsSize=884;documentsCount=2000;collectionSize=1408;
x-ms-item-count: 2000
x-ms-schemaversion: 1.3
x-ms-alt-content-path: dbs/db/colls/sample
x-ms-content-path: +9kEANVq0wA=
x-ms-xp-role: 1
x-ms-documentdb-query-metrics: totalExecutionTimeInMs=33.67;queryCompileTimeInMs=0.06;queryLogicalPlanBuildTimeInMs=0.02;queryPhysicalPlanBuildTimeInMs=0.10;queryOptimizationTimeInMs=0.00;VMExecutionTimeInMs=32.56;indexLookupTimeInMs=0.36;documentLoadTimeInMs=9.58;systemFunctionExecuteTimeInMs=0.00;userFunctionExecuteTimeInMs=0.00;retrievedDocumentCount=2000;retrievedDocumentSize=1125600;outputDocumentCount=2000;writeOutputTimeInMs=18.10;indexUtilizationRatio=1.00
x-ms-request-charge: 604.42
x-ms-serviceversion: version=1.14.34.4
x-ms-activity-id: 0df8b5f6-83b9-4493-abda-cce6d0f91486
x-ms-session-token: 2:2008
x-ms-gatewayversion: version=1.14.33.2
Date: Tue, 27 Jun 2017 21:59:49 GMT
```

A kulcs válaszfejlécek, a lekérdezés által visszaadott a következők:

| Beállítás | Leírás |
| ------ | ----------- |
| `x-ms-item-count` | A válaszban visszaadott elemek száma. Ez az adott nyelvtől függ a megadott `x-ms-max-item-count`, a válasz maximális hasznos adatainak mérete, a kiosztott átviteli sebesség és a lekérdezés-végrehajtási idő férő elemek száma. |  
| `x-ms-continuation:` | A folytatási token, a lekérdezés végrehajtásának folytatásához, ha további eredmények érhetők el. | 
| `x-ms-documentdb-query-metrics` | Lekérdezési statisztika a végrehajtását. Ez az idő a lekérdezés végrehajtása a különböző fázisait statisztikáit tartalmazó tagolt karakterlánc. Visszaadott if `x-ms-documentdb-populatequerymetrics` értékre van állítva `True`. | 
| `x-ms-request-charge` | Hány [kérelemegységek](request-units.md) a lekérdezés által felhasznált. | 

A REST API-kérelemfejlécek és a lehetőségek részletes ismertetéséért lásd: [lekérdezése a REST API-val erőforrások](https://docs.microsoft.com/rest/api/cosmos-db/querying-cosmosdb-resources-using-the-rest-api).

## <a name="best-practices-for-query-performance"></a>Gyakorlati tanácsok a lekérdezési teljesítmény
Az alábbiakban a leggyakoribb tényező befolyásolja az Azure Cosmos DB lekérdezési teljesítmény. Hogy mélyebben megismerjük egyes ezek a témakörök ebben a cikkben.

| Tényező | Tipp | 
| ------ | -----| 
| Kiosztott átviteli sebesség | Lekérdezésenként RU mérni, és győződjön meg arról, hogy rendelkezik-e a szükséges kiosztott átviteli sebesség a lekérdezésekhez. | 
| Particionálás és a partíciókulcsok | A partíciós kulcs értékét a szűrőfeltételt az alacsony késés érdekében az alkalmazást a lekérdezéseket. |
| SDK-t és a lekérdezési beállítások | Hajtsa végre az SDK ajánlott eljárásaival, például a közvetlen kapcsolat, és finomhangolása az ügyféloldali lekérdezés-végrehajtási lehetőségeket. |
| Hálózati késleltetés | Hálózati mérési többletterhelést okoz a fiókot, és olvassa el a legközelebbi régióból a többkiszolgálós API-k használatával. |
| Indexelési házirend | Győződjön meg arról, hogy az indexelő elérési utak/szabályzatot a lekérdezéshez. |
| Lekérdezés-végrehajtási metrikák | Azonosíthatja a potenciális újraírások lekérdezési és alakzatok a lekérdezés végrehajtási metrikát elemezhet.  |

### <a name="provisioned-throughput"></a>Kiosztott átviteli sebesség
Cosmos dB-ben létrehozhat tárolókat az adatokat, minden kifejezett kérelem egységek (RU) másodpercenként fenntartott adattovábbítási kapacitással rendelkező. Egy 1 KB méretű dokumentum olvasási: 1 RU, és minden művelet (beleértve a lekérdezések) az összetettség alapján RUs rögzített számú van normalizálva. Például ha 1000 RU/s kiosztott a tárolóhoz, és rendelkezik lekérdezésben `SELECT * FROM c WHERE c.city = 'Seattle'` , amely 5 Kérelemegységet fogyaszt, akkor is végezhet (1000 RU/s) / (5 RU/lekérdezés) = 200 lekérdezés/s az ilyen lekérdezések másodpercenként. 

Ha több mint 200 állapotlekérdezés/mp, a szolgáltatás elindul, bejövő kéréseket a sebességhatárolt 200/s felett. Az SDK-k automatikusan kezelik az ebben az esetben a leállítási/ismételt végrehajtásával, ezért előfordulhat, hogy láthatja, hogy a lekérdezések egy nagyobb késést. A szükséges érték, a kiosztott átviteli sebesség növelése javítja a Lekérdezések késése és az átviteli sebességet. 

További információ a kérelemegységekről, lásd: [Kérelemegységek](request-units.md).

### <a name="partitioning-and-partition-keys"></a>Particionálás és a partíciókulcsok
Az Azure Cosmos DB lekérdezéseket végrehajtania a lassabb/kevésbé hatékony, a leggyorsabb és a legtöbb hatékony a következő sorrendben. 

* Egy önálló partíciókulcsokon és cikk kulcs LEKÉRÉSE
* Az önálló partíciókulcsokon szűrőfeltételt lekérdezése
* Lekérdezése nélkül egy egyenlőség vagy tartomány szűrőfeltételt bármely vlastnost
* Szűrők nélkül lekérdezése

Tekintse meg az összes partíció lekérdezések nagyobb késést kell, és magasabb fogyasztanak is. Mivel mindegyik partíció rendelkezik az összes tulajdonság automatikus indexelés, lekérdezés is hatékonyan kiszolgálható az indexből ebben az esetben. Lekérdezések partíciók gyorsabban a párhuzamossági beállítások használatával teheti meg.

Particionálás és a partíciókulcsok kapcsolatos további információkért lásd: [az Azure Cosmos DB particionálási](partition-data.md).

### <a name="sdk-and-query-options"></a>SDK-t és a lekérdezési beállítások
Lásd: [teljesítménnyel kapcsolatos tippek](performance-tips.md) és [Teljesítménytesztelés](performance-testing.md) az Azure Cosmos DB-ből a legjobb teljesítmény érdekében az ügyféloldali beszerzése. Ez magában foglalja a legújabb SDK-k használatával, a platform-specifikus konfigurációkat, például a kapcsolatokat, gyakorisága szemétgyűjtés, alapértelmezett száma konfigurálását és a könnyen használható kapcsolati lehetőségek, például a közvetlen/TCP használatával. 


#### <a name="max-item-count"></a>Elemek maximális száma
A lekérdezésekhez, értékét `MaxItemCount` jelentős hatással lehet a végpontok közötti lekérdezések során. Minden egyes körbeérnek a kiszolgálón lévő elemek száma legfeljebb adja vissza `MaxItemCount` (100 elemet alapértelmezés szerint). E beállítás hatására értéke (a-1 értéket a legnagyobb, és az ajánlott) javítja a lekérdezési időtartam teljes, a kiszolgáló és az ügyfél, különösen a lekérdezések nagy eredményhalmazt közötti adatváltások számát korlátozza.

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        MaxItemCount = -1, 
    }).AsDocumentQuery();
```

#### <a name="max-degree-of-parallelism"></a>Maximális párhuzamossági fok
A Lekérdezések finomhangolása a `MaxDegreeOfParallelism` felismerésében az ajánlott az alkalmazás, különösen akkor, ha a partícióra kiterjedő lekérdezések (a partíciókulcs értékhez szűrő) nélkül végezhet. `MaxDegreeOfParallelism`  a feladatok maximális száma párhuzamos, azaz a párhuzamos látogatják partíciók maximális szabályozza. 

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        MaxDegreeOfParallelism = -1, 
        EnableCrossPartitionQuery = true 
    }).AsDocumentQuery();
```

Tegyük fel, amelyek
* D = az alapértelmezett maximális száma (az ügyfélszámítógépen a processzor teljes száma =) párhuzamos feladatok
* P = párhuzamos feladatok maximális száma a felhasználó által megadott
* N látogatják a lekérdezést igénylő partíciók száma =

Az alábbiakban következményei hogyan viselkednek a párhuzamos lekérdezéseket a másik értékkel. o.
* (P == 0) = > soros mód
* (P == 1) = > egyetlen feladat maximális
* (P > 1) = > perc (P, N) párhuzamos feladatok 
* (P < 1) = > perc (N, D) párhuzamos feladatok

SDK kibocsátási megjegyzéseket, és részleteket megvalósított osztályok és módszerek az [SQL SDK-k](sql-api-sdk-dotnet.md)

### <a name="network-latency"></a>Hálózati késleltetés
Lásd: [Azure Cosmos DB globális terjesztésének](tutorial-global-distribution-sql-api.md) , hogy hogyan állítsa be a globális terjesztés, és csatlakozzon a legközelebbi régiót. Hálózati késés jelentős hatással a lekérdezési teljesítményre van szüksége több adatváltásra vagy lekérdezni a lekérdezésből származó nagy eredményhalmazt. 

A szakasz a lekérdezés végrehajtási metrikák azt ismerteti, hogyan kérheti le a kiszolgáló lekérdezések végrehajtási ideje ( `totalExecutionTimeInMs`), hogy meg tudja különböztetni idő a lekérdezés-végrehajtás és az idő a hálózati átvitel során.

### <a name="indexing-policy"></a>Indexelési szabályzat
Lásd: [indexelési szabályzat konfigurálása](indexing-policies.md) indexelés elérési utak, bármilyen, és módok és milyen hatással van, a lekérdezés végrehajtása céljából. Alapértelmezés szerint az indexelési házirendet használja kivonatoló indexelő-karakterlánc, amely hatékony egyenlőség lekérdezések esetében, de nem esik lekérdezések/rendezési lekérdezésekkel. Ha karakterláncokat kell lekérdezések, javasoljuk, adja meg a tartomány minden karakterlánc index típusa. 

## <a name="query-execution-metrics"></a>Lekérdezés-végrehajtási metrikák
Ezt a nem kötelező úgy szerezheti be a lekérdezések részletes metrikákért `x-ms-documentdb-populatequerymetrics` fejléc (`FeedOptions.PopulateQueryMetrics` a .NET SDK-ban). A visszaadott érték `x-ms-documentdb-query-metrics` azt jelentette a speciális hibaelhárításhoz, a lekérdezés végrehajtása a következő kulcs-érték párokat tartalmaz. 

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        PopulateQueryMetrics = true, 
    }).AsDocumentQuery();

FeedResponse<dynamic> result = await query.ExecuteNextAsync();

// Returns metrics by partition key range Id
IReadOnlyDictionary<string, QueryMetrics> metrics = result.QueryMetrics;

```

| Metrika | Unit (Egység) | Leírás | 
| ------ | -----| ----------- |
| `totalExecutionTimeInMs` | ezredmásodperc | Lekérdezés-végrehajtási idő | 
| `queryCompileTimeInMs` | ezredmásodperc | Lekérdezés fordítási idő  | 
| `queryLogicalPlanBuildTimeInMs` | ezredmásodperc | Logikai lekérdezésterv építi | 
| `queryPhysicalPlanBuildTimeInMs` | ezredmásodperc | Fizikai lekérdezésterv építi | 
| `queryOptimizationTimeInMs` | ezredmásodperc | A lekérdezés optimalizálása töltött idő | 
| `VMExecutionTimeInMs` | ezredmásodperc | Idő a lekérdezés futásidejű | 
| `indexLookupTimeInMs` | ezredmásodperc | Idő a fizikai index réteg | 
| `documentLoadTimeInMs` | ezredmásodperc | Idő a dokumentumok betöltése  | 
| `systemFunctionExecuteTimeInMs` | ezredmásodperc | A fordított teljes idő végrehajtó rendszer (beépített) függvény ezredmásodpercben  | 
| `userFunctionExecuteTimeInMs` | ezredmásodperc | A fordított teljes idő ezredmásodpercben végrehajtó felhasználó által definiált függvények | 
| `retrievedDocumentCount` | count | Lekérdezett dokumentumok összesített száma  | 
| `retrievedDocumentSize` | bájt | Teljes mérete (bájt) beolvasott dokumentumokat  | 
| `outputDocumentCount` | count | Kimeneti dokumentumok száma | 
| `writeOutputTimeInMs` | ezredmásodperc | Lekérdezés-végrehajtási idő (MS) | 
| `indexUtilizationRatio` | arány (< = 1) | A dokumentumok számát a szűrővel egyező dokumentumok száma aránya betöltése  | 

Az ügyfél SDK-k belső használatra elérhetővé teheti több lekérdezési műveletek a lekérdezés minden egyes partíción belül kiszolgálása érdekében. Az ügyfél hívást egynél több partíciónkénti Ha az összes eredmény meghaladja a `x-ms-max-item-count`, ha a lekérdezés meghaladja a kiosztott átviteli sebesség a partíció, vagy ha a lekérdezés hasznos eléri a maximális méretet, egy-egy lapon, vagy ha a lekérdezés eléri a rendszer lefoglalt időtúllépési korlát. Minden egyes részleges lekérdezés-végrehajtás adja vissza egy `x-ms-documentdb-query-metrics` lap. 

Az alábbiakban néhány mintalekérdezést, és a mérőszámok egyes értelmezése adja vissza a lekérdezés végrehajtása: 

| Lekérdezés | Minta metrika | Leírás | 
| ------ | -----| ----------- |
| `SELECT TOP 100 * FROM c` | `"RetrievedDocumentCount": 101` | A lekért dokumentumok száma: 1 100 + megfelelően a TOP záradékot. Lekérdezés többnyire időt vesz igénybe a `WriteOutputTime` és `DocumentLoadTime` mivel ez a vizsgálat. | 
| `SELECT TOP 500 * FROM c` | `"RetrievedDocumentCount": 501` | RetrievedDocumentCount már magasabb (500 + 1 a TOP záradékot megfelelően). | 
| `SELECT * FROM c WHERE c.N = 55` | `"IndexLookupTime": "00:00:00.0009500"` | Körülbelül 0.9-es ms van telik el a IndexLookupTime kulcskeresés, egy index keresési, mert `/N/?`. | 
| `SELECT * FROM c WHERE c.N > 55` | `"IndexLookupTime": "00:00:00.0017700"` | Valamivel több (1.7 ms) töltött idő IndexLookupTime keresztül a tartomány ellenőrzését egy index keresési, mert `/N/?`. | 
| `SELECT TOP 500 c.N FROM c` | `"IndexLookupTime": "00:00:00.0017700"` | Egy időben költött `DocumentLoadTime` előző lekérdezésekhez, de alacsonyabb `WriteOutputTime` , mert azt már kivetítést csak az egyik tulajdonság. | 
| `SELECT TOP 500 udf.toPercent(c.N) FROM c` | `"UserDefinedFunctionExecutionTime": "00:00:00.2136500"` | Körülbelül 213 ms töltött `UserDefinedFunctionExecutionTime` minden értékét az UDF végrehajtása `c.N`. |
| `SELECT TOP 500 c.Name FROM c WHERE STARTSWITH(c.Name, 'Den')` | `"IndexLookupTime": "00:00:00.0006400", "SystemFunctionExecutionTime": "00:00:00.0074100"` | Hamarosan a 0.6-os ms töltött `IndexLookupTime` a `/Name/?`. A lekérdezés végrehajtási idő (ms KB. 7) a legtöbb `SystemFunctionExecutionTime`. |
| `SELECT TOP 500 c.Name FROM c WHERE STARTSWITH(LOWER(c.Name), 'den')` | `"IndexLookupTime": "00:00:00", "RetrievedDocumentCount": 2491,  "OutputDocumentCount": 500` | A lekérdezést hajtjuk végre, a vizsgálat, mert használja `LOWER`, és 500 2491 beolvasott dokumentumokat kívül adja vissza. |


## <a name="next-steps"></a>További lépések
* A támogatott SQL-lekérdezési operátorokkal vagy kulcsszavak kapcsolatos további információkért lásd: [SQL-lekérdezés](how-to-sql-query.md). 
* Kérelemegység kapcsolatos további információkért lásd: [kérelemegységek](request-units.md).
* Indexelési házirend kapcsolatos további információkért lásd: [indexelési szabályzat](indexing-policies.md) 


