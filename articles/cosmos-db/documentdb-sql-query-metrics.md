---
title: "SQL lekérdezés metrikák Azure Cosmos DB SQL API-hoz |} Microsoft Docs"
description: "További tudnivalók állíthatnak be, és a hibakeresési Azure Cosmos DB kérelmek SQL lekérdezési teljesítményét."
keywords: "SQL-szintaxis, sql-lekérdezést, az sql-lekérdezések, json lekérdezési nyelv, adatbázis fogalmait és az sql-lekérdezések, összesítő függvények"
services: cosmos-db
documentationcenter: 
author: arramac
manager: jhubbard
editor: monicar
ms.assetid: b2fa8e8f-7291-45a3-9bd1-7284ed9077f8
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/02/2017
ms.author: arramac
ms.openlocfilehash: 2cb6319356a536aebc1db3122cf80b8736d1fd4f
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="tuning-query-performance-with-azure-cosmos-db"></a>Az Azure Cosmos DB lekérdezési teljesítmény hangolása

[!INCLUDE [cosmos-db-sql-api](../../includes/cosmos-db-sql-api.md)]

Az Azure Cosmos DB biztosít egy [SQL API-t a lekérdezésre adatok](documentdb-sql-query.md), anélkül, hogy a séma vagy másodlagos kulcsot. Ez a cikk a fejlesztők számára a következő adatokat tartalmazza:

* Nagy részletességű Azure Cosmos adatbázis SQL-lekérdezés végrehajtása működéséről
* A lekérdezés kérés- és válaszfejlécekről, és az ügyfél SDK-beállítások részletei
* Tippek és ajánlott eljárások a lekérdezési teljesítmény
* Példák SQL végrehajtási statisztika lekérdezési teljesítmény hibakeresési használatára

## <a name="about-sql-query-execution"></a>Tudnivalók az SQL-lekérdezés végrehajtása

Az Azure Cosmos Adatbázisba, adattárolásra-tárolókban, amely bármelyik növelhető [tárolási méretét, vagy kérjen teljesítmény](partition-data.md). Azure Cosmos-adatbázis zökkenőmentesen arányosan adatok fizikai partíciók kezeléséhez az adatmennyiség-növekedés, vagy növelje a kiosztott átviteli sebesség a színfalak között. A tárolóhoz, a támogatott közül vagy a REST API használatával adhat ki az SQL-lekérdezések [SQL SDK-k](documentdb-sdk-dotnet.md).

Particionálás rövid áttekintést: megadhatja a partíciós kulcs, például a "város", amely megadja, hogy milyen adatok fizikai partíciók osztani. Egyetlen partíciókulcs tartozó adatokat (például "város" == "Seattle") a fizikai partíción belül található, de általában egyetlen fizikai partícióján több partíciós kulcsok. Amikor egy partíció eléri a tárolási méretét, a szolgáltatás zökkenőmentesen felosztja a partíció két új partíciót és a partíciókulcs egyenlően osztja ezek a partíciók közötti. Mivel partíció átmeneti, az API-k használata egy "partíció kulcs tartományt", amely azt jelzi, a tartományokat a partíciós kulcs kivonatok absztrakciós. 

Lekérdezés kiadni Azure Cosmos DB, az SDK logikai lépéseket hajtja végre:

* Elemezni az SQL-lekérdezést a lekérdezés-végrehajtási terv meghatározásához. 
* Ha a lekérdezés tartalmaz egy szűrőt a partíciós kulcs ellen, például `SELECT * FROM c WHERE c.city = "Seattle"`, egyetlen partícióra történik. Ha a lekérdezés nem rendelkezik egy szűrőt a partíciós kulcs, majd az összes partíció végrehajtása, eredmények egyesítve lesznek az ügyféloldali.
* A lekérdezés minden partíció sorozat futtatásuk vagy párhuzamosan, alapuló ügyfél-konfigurációt. Minden partíción belül előfordulhat, hogy a lekérdezés egyet vagy lekérdezés összetettségétől függően további kiszolgálókkal való adatváltások számát konfigurálva az ajánlott méretet, és sebességét, a gyűjtemény kiépítése. Minden egyes végrehajtása számát adja vissza [egységek kérelem](request-units.md) felhasznált lekérdezés-végrehajtáshoz, és szükség esetén lekérdezés végrehajtási statisztika. 
* Az SDK-t egy a lekérdezés eredményeinek összefoglalását partíciók keresztül hajtja végre. Például ha a lekérdezés egy ORDER BY közötti partíciók magában foglalja, majd az egyes partíciók eredményei egyesítési rendezett to globálisan sorrendet az eredményeket. Ha a lekérdezés például összesítést `COUNT`, az egyes partíciók száma összeadódnak eredményezett a teljes száma.

Az SDK-k a lekérdezés-végrehajtáshoz különböző lehetőségeket kínál. Például a .NET ezek a lehetőségek állnak rendelkezésre a `FeedOptions` osztály. A következő táblázat ismerteti ezeket a beállításokat, és milyen hatással lesznek a lekérdezés-végrehajtási idő. 

| Beállítás | Leírás |
| ------ | ----------- |
| `EnableCrossPartitionQuery` | Meg kell igaz a lekérdezést, amely igényel között legfeljebb egy partíciója hajthatnak végre. Ez az egy explicit jelzőt, amely lehetővé teszi annak tudatában teljesítmény mellékhatásokkal végre fejlesztési idő alatt. |
| `EnableScanInQuery` | Igaz értéket, ha az indexelő visszavonta igényét, de a lekérdezés segítségével a vizsgálat futtatását szeretne értékre kell állítani. Csak végezhető el, ha a kért szűrő elérési útja indexelő le van tiltva. | 
| `MaxItemCount` | A kiszolgáló / oda-vissza visszaadandó elemek maximális száma. -1-beállítása, hogy a kiszolgáló kezelése az elemek száma. Vagy ezt az értéket csak kevés elemek száma oda-vissza beolvasása csökkenthető. 
| `MaxBufferedItemCount` | Ez egy ügyféloldali beállítást, és korlátozza a memória-felhasználás kereszt-partíció ORDER BY végrehajtása során használt. A nagyobb érték csökkentheti a kereszt-partíció rendezés a késési. |
| `MaxDegreeOfParallelism` | Lekérdezi vagy beállítja az ügyféloldali futtatása során az Azure Cosmos DB szolgáltatásban párhuzamos lekérdezés-végrehajtás párhuzamos műveletek számát. Egy pozitív tulajdonság értéke a értékét párhuzamos műveletek számának korlátozása. Ha az értéke kisebb, mint 0, a rendszer automatikusan úgy dönt, futtatásához párhuzamos műveletek számát. |
| `PopulateQueryMetrics` | Lehetővé teszi, hogy a lekérdezés-végrehajtás, mint a fordítás során, a index hurok idő és a dokumentum különböző fázisait töltött idő statisztika részletes naplózás betöltési ideje. Lekérdezés teljesítmény eseményadatokat Azure-támogatással rendelkező megoszthatja zónalekérdezési statisztika kimenetét. |
| `RequestContinuation` | A lekérdezés-végrehajtás történő lekérdezés által visszaadott folytatási átlátszatlan folytathatja. A folytatási kód magában foglalja a lekérdezés-végrehajtáshoz szükséges összes állapotát. |
| `ResponseContinuationTokenLimitInKb` | Korlátozhatja a kiszolgáló által visszaadott folytatási maximális méretét. Szükség lehet állítsa-e az alkalmazás-állomás korlátok a válasz fejléc mérete. A teljes időtartam és a lekérdezés felhasznált RUs növelheti a beállítás.  |

Például vessen példalekérdezést a partíciós kulcs egy gyűjtemény kért `/city` a partíció, és a kiépített 100 000 RU/s átviteli sebesség. A lekérdezés segítségével kér le `CreateDocumentQuery<T>` a .NET, a következő:

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

A fenti, SDK részlet felel meg a következő REST API-kérelem:

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

Minden egyes lekérdezés végrehajtása lap megfelel-e a REST API `POST` rendelkező a `Accept: application/query+json` fejlécet, és az SQL-lekérdezést a törzsében. Minden egyes lekérdezés esetén egy vagy több kerekíteni az a kiszolgáló elérését a `x-ms-continuation` token annál az ügyfél és kiszolgáló folytatja a végrehajtási között. A konfigurációs beállítások FeedOptions kerülnek átadásra a kiszolgálói kérelem fejléc formájában. Például `MaxItemCount` megfelel-e `x-ms-max-item-count`. 

A kérelem a következő (csonkolt olvashatóság) választ ad vissza:

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

A kulcs válaszfejlécek a lekérdezés által visszaadott közé tartoznak a következők:

| Beállítás | Leírás |
| ------ | ----------- |
| `x-ms-item-count` | A válaszban visszaküldött elemek száma. Ez az függ a megadott `x-ms-max-item-count`, a válasz maximális terhelés méretének, a kiosztott átviteli sebesség és a lekérdezés-végrehajtási idő illeszkedő elemek száma. |  
| `x-ms-continuation:` | A folytatási kód folytatni a lekérdezés futtatása, ha további eredmények érhetők el. | 
| `x-ms-documentdb-query-metrics` | A lekérdezés végrehajtása statisztikája. Ez a lekérdezés-végrehajtás különböző szakaszainak töltött időt a statisztikai adatait tartalmazó tagolt karakterláncot. Visszaadott if `x-ms-documentdb-populatequerymetrics` értéke `True`. | 
| `x-ms-request-charge` | Hány [egységek kérelem](request-units.md) a lekérdezés által felhasznált. | 

A REST API kérelemfejléc és a beállítások a részletekért lásd: [erőforrásokat a REST API használatával](https://docs.microsoft.com/rest/api/documentdb/querying-documentdb-resources-using-the-rest-api).

## <a name="best-practices-for-query-performance"></a>Gyakorlati tanácsok a lekérdezési teljesítmény
Leggyakoribb Azure Cosmos DB lekérdezések teljesítményét befolyásoló tényezők a következők: A Microsoft feltárva minden, az alábbi témakörökben találja ebben a cikkben.

| Tényező | Tipp | 
| ------ | -----| 
| Kiosztott átviteli kapacitás | RU mérjük lekérdezésenként, és győződjön meg arról, hogy rendelkezik-e a szükséges kiosztott átviteli sebesség a lekérdezések. | 
| Particionálás és partíciós kulcsok | A partíciós kulcs értékét a szűrőfeltételben az alacsony késleltetés az alkalmazást a lekérdezések. |
| SDK-t és a lekérdezés beállításai | Kövesse a bevált gyakorlatokat SDK például közvetlen kapcsolatot, és az ügyféloldali lekérdezés végrehajtási beállítások hangolását. |
| Hálózati késés | Protokollterhelés a mérési hálózati fiókot, és többhelyű API-k használata a legközelebbi régiót olvasni. |
| Indexelési házirendet | Győződjön meg arról, hogy rendelkezik-e a szükséges indexelési elérési utak/házirendet a lekérdezéshez. |
| Lekérdezés-végrehajtási metrikák | Vizsgálja meg a lekérdezés végrehajtása metrikák lekérdezés- és alakzatok lehetséges újraírások azonosításához.  |

### <a name="provisioned-throughput"></a>Kiosztott átviteli kapacitás
Cosmos DB az adatokat, minden egyes kérelem egységek (RU) másodpercenként kifejezett fenntartott átviteli tárolók hoz létre. Egy 1 KB-os dokumentum olvasási 1 RU, és minden művelet (beleértve a lekérdezések) normalizálva összetettsége alapján RUs rögzített száma. Például ha 1000 RU/mp a tároló üzembe, és rendelkezik a lekérdezés például `SELECT * FROM c WHERE c.city = 'Seattle'` , amely feldolgozó 5 RUs, majd végezhet (1000 RU/mp) / (5 RU/lekérdezés) = 200 lekérdezés/s ilyen lekérdezések száma másodpercenként. 

Ha több mint 200 állapotlekérdezés/mp, a szolgáltatás elindul, bejövő kérelmek Sebességhatároló 200/mp feletti. Az SDK-k automatikusan kezelik az ebben az esetben egy leállítási újra elvégzésével, ezért bizonyára észrevette, hogy a lekérdezések egy nagyobb késleltetéssel járhat. A szükséges érték a létesített átviteli sebesség növelése javítja a lekérdezés-késleltetés és a teljesítményt. 

Kapcsolatos további tudnivalókért lásd: [egységek kérelem](request-units.md).

### <a name="partitioning-and-partition-keys"></a>Particionálás és partíciós kulcsok
Az Azure Cosmos DB általában lekérdezések hajtsa végre a következő sorrendben a leggyorsabb/legtöbb hatékony a lassabb/kevésbé hatékony. 

* Egy olyan partíciót és elem kulcsot az beszerzése
* Egy szűrési záradékot a egypartíciós kulcs lekérdezése
* Az egyik tulajdonságnak sem egyenlőség vagy tartomány szűrő záradék nélkül lekérdezése
* Lekérdezése nélkül szűrők

Tekintse át az összes partíció lekérdezéseket kell nagyobb késleltetéssel járhat, és magasabb RUs felhasználhat. Mindegyik partíció rendelkezik, az automatikus indexeléshez szemben az összes tulajdonság, mert a lekérdezés szolgáltatható hatékonyan az indexből ebben az esetben. Lekérdezések, amelyek több partíciót gyorsabban párhuzamossági lehetőségek segítségével végezheti el.

Particionálás és partíciókulcsok kapcsolatos további információkért lásd: [Azure Cosmos DB a particionálás](partition-data.md).

### <a name="sdk-and-query-options"></a>SDK-t és a lekérdezés beállításai
Lásd: [teljesítmény tippek](performance-tips.md) és [Teljesítménytesztelés](performance-testing.md) az beszerzése a legjobb ügyféloldali teljesítményével az Azure Cosmos-Adatbázisból. Tartalmazzák a legújabb SDK-k használatával, a platform-specifikus konfigurációk például alapértelmezett kapcsolatok száma, szemétgyűjtés, gyakoriságának beállítása és a közvetlen/TCP például egyszerűsített kapcsolati lehetőségek használatával. 


#### <a name="max-item-count"></a>Elemek maximális száma
A lekérdezések, értékének `MaxItemCount` jelentős hatással lehet a végpont lekérdezés idejét. Minden egyes oda-vissza a kiszolgálóhoz fog visszaadni elemeinek száma nagyobb `MaxItemCount` (100 elemek alapértelmezett). Ezt a nagyobb érték beállítást (a -1 érték legnagyobb, és az ajánlott) javítja a lekérdezés teljes időtartama, ha a kiszolgáló és az ügyfél, különösen a lekérdezések nagy eredményhalmazt közötti adatváltások számát korlátozza.

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        MaxItemCount = -1, 
    }).AsDocumentQuery();
```

#### <a name="max-degree-of-parallelism"></a>Párhuzamossági maximális fok
A lekérdezések hangolja a `MaxDegreeOfParallelism` felismerésében a legjobb az alkalmazáshoz, különösen akkor, ha Ön lekérdezésére kereszt-partíció (nélkül egy szűrőt a partíciókulcs érték). `MaxDegreeOfParallelism`a feladatok maximális száma párhuzamos, azaz, a maximális száma párhuzamos látogatják partíciók szabályozza. 

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
* D = alapértelmezett maximális száma párhuzamos tevékenységek (= az ügyfélszámítógépen. a processzor teljes száma)
* P = párhuzamos tevékenységek maximális száma a felhasználó által megadott
* N =, amelyet a lekérdezést a meglátogatott partíciók száma

Az alábbiakban hogyan a párhuzamos lekérdezések viselkednek a p különböző értékeket következményei
* (P == 0) = > soros üzemmód
* (P == 1) = > maximális egy feladat
* (P > 1) = > Min (P, N) párhuzamos tevékenységek 
* (P < 1) = > Min (N, D) párhuzamos tevékenységek

Az SDK kibocsátási megjegyzéseket, és részleteket megvalósított osztályokat és metódusokat [SQL SDK-k](documentdb-sdk-dotnet.md)

### <a name="network-latency"></a>Hálózati késés
Lásd: [Azure Cosmos DB globális terjesztési](tutorial-global-distribution-documentdb.md) globális terjesztési beállítása, és csatlakozzon a legközelebbi régiót. Hálózati késés jelentős hatással a lekérdezési teljesítményre van szüksége több üzenetváltások utak számát vagy a lekérdezés nagy eredményhalmazt beolvasása. 

A lekérdezés végrehajtása mérőszámokat szakasz azt ismerteti, hogyan lekérdezések server végrehajtási idejének lekérdezésére ( `totalExecutionTimeInMs`), így meg tudja különböztetni a lekérdezés-végrehajtás töltött időt és az idő a hálózati átvitel során.

### <a name="indexing-policy"></a>Indexelési házirendet
Lásd: [konfigurálása az indexelési házirendet](indexing-policies.md) indexeléshez elérési utak, különböző, és módok és milyen hatással lesznek a lekérdezés végrehajtása. Alapértelmezés szerint az indexelési házirendet használja kivonatoló indexelő karakterláncok, amelyek a következő időponttól érvényes egyenlőség lekérdezések, de nem lekérdezések tartomány rendelés lekérdezések. Ha lekérdezések karakterláncok, ajánlott az összes karakterlánc tartomány index típusának megadásával. 

## <a name="query-execution-metrics"></a>Lekérdezés-végrehajtási metrikák
A lekérdezés-végrehajtás részletes metrikák szerezheti be a nem kötelező sikeres `x-ms-documentdb-populatequerymetrics` fejléc (`FeedOptions.PopulateQueryMetrics` a .NET SDK-ban). A visszaadott érték `x-ms-documentdb-query-metrics` rendelkezik a következő kulcs-érték párok azt jelentette, hogy a speciális lekérdezés-végrehajtás hibaelhárításához. 

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
| `queryCompileTimeInMs` | ezredmásodperc | Lekérdezés fordítás során  | 
| `queryLogicalPlanBuildTimeInMs` | ezredmásodperc | Logikai lekérdezésterv építi | 
| `queryPhysicalPlanBuildTimeInMs` | ezredmásodperc | Fizikai lekérdezésterv építi | 
| `queryOptimizationTimeInMs` | ezredmásodperc | A lekérdezés optimalizálása töltött idő | 
| `VMExecutionTimeInMs` | ezredmásodperc | Idő a lekérdezés futásidejű | 
| `indexLookupTimeInMs` | ezredmásodperc | Idő a fizikai index réteg | 
| `documentLoadTimeInMs` | ezredmásodperc | Idő a dokumentum betöltése  | 
| `systemFunctionExecuteTimeInMs` | ezredmásodperc | A fordított teljes idő végrehajtás alatt álló rendszer (beépített) funkciók ezredmásodpercben  | 
| `userFunctionExecuteTimeInMs` | ezredmásodperc | Töltött teljes idő, ezredmásodpercben végrehajtó felhasználó által definiált függvények | 
| `retrievedDocumentCount` | darab | Lekért dokumentumok száma összesen  | 
| `retrievedDocumentSize` | bájt | A beolvasott dokumentumokat a bájtok teljes mérete  | 
| `outputDocumentCount` | darab | Kimeneti dokumentumok száma | 
| `writeOutputTimeInMs` | ezredmásodperc | Lekérdezés-végrehajtási idő ezredmásodpercben | 
| `indexUtilizationRatio` | arány (< = 1) | A betöltött dokumentumok száma szűrőt egyező dokumentumok száma aránya  | 

Az ügyfél SDK-k belső teheti több lekérdezési műveletek kiszolgálásához a lekérdezés minden partíción belül. Az ügyfél hívást egynél több partíciónkénti Ha meghaladja a teljes eredmények `x-ms-max-item-count`, ha a lekérdezés meghaladja a kiosztott átviteli sebesség a partíció található, vagy ha a lekérdezés forgalma eléri a maximális méretet egy oldalon, vagy ha a lekérdezés eléri a rendszer lefoglalt időkorlátja. Minden egyes részleges lekérdezés-végrehajtás adja vissza egy `x-ms-documentdb-query-metrics` lap. 

Az alábbiakban néhány mintalekérdezések, és a lekérdezés-végrehajtás visszaadni értelmezése a metrikák egy részénél: 

| Lekérdezés | A minta-metrika | Leírás | 
| ------ | -----| ----------- |
| `SELECT TOP 100 * FROM c` | `"RetrievedDocumentCount": 101` | A beolvasott dokumentumokat értéke 100 + 1 kombinációval felel meg a TOP záradék. Lekérdezési idő többnyire töltött `WriteOutputTime` és `DocumentLoadTime` mivel ez a vizsgálat. | 
| `SELECT TOP 500 * FROM c` | `"RetrievedDocumentCount": 501` | RetrievedDocumentCount már nagyobb (500 + 1 a TOP záradék megfelelően). | 
| `SELECT * FROM c WHERE c.N = 55` | `"IndexLookupTime": "00:00:00.0009500"` | Hamarosan 0.9 ms van töltött IndexLookupTime kulcs kereséshez, mert az index keresési `/N/?`. | 
| `SELECT * FROM c WHERE c.N > 55` | `"IndexLookupTime": "00:00:00.0017700"` | Valamivel több (1.7 ms) töltött idő IndexLookupTime keresztül tartomány vizsgálatot, mivel az index keresési `/N/?`. | 
| `SELECT TOP 500 c.N FROM c` | `"IndexLookupTime": "00:00:00.0017700"` | A fordított egyszerre `DocumentLoadTime` előző lekérdezésekhez, de kisebb `WriteOutputTime` , mert azt még kivetítéséről csak egy tulajdonság. | 
| `SELECT TOP 500 udf.toPercent(c.N) FROM c` | `"UserDefinedFunctionExecutionTime": "00:00:00.2136500"` | Hamarosan 213 ms töltött `UserDefinedFunctionExecutionTime` feldolgozás alatt álló UDF minden értékének `c.N`. |
| `SELECT TOP 500 c.Name FROM c WHERE STARTSWITH(c.Name, 'Den')` | `"IndexLookupTime": "00:00:00.0006400", "SystemFunctionExecutionTime": "00:00:00.0074100"` | Hamarosan 0,6 ms töltött `IndexLookupTime` a `/Name/?`. A lekérdezés-végrehajtási idő (ms ~ 7) a legtöbb `SystemFunctionExecutionTime`. |
| `SELECT TOP 500 c.Name FROM c WHERE STARTSWITH(LOWER(c.Name), 'den')` | `"IndexLookupTime": "00:00:00", "RetrievedDocumentCount": 2491,  "OutputDocumentCount": 500` | Lekérdezést végrehajtja a rendszer egy vizsgálatot, mivel a program `LOWER`, és 500 2491 lekérdezése dokumentumból ad vissza. |


## <a name="next-steps"></a>Következő lépések
* A támogatott SQL-lekérdezési operátorok és kulcsszavak kapcsolatos további tudnivalókért lásd: [SQL-lekérdezés](documentdb-sql-query.md). 
* Kapcsolatos további tudnivalókért lásd: [egységek kérelem](request-units.md).
* Az indexelő házirenddel kapcsolatos további tudnivalókért lásd: [indexelő házirend](indexing-policies.md) 


