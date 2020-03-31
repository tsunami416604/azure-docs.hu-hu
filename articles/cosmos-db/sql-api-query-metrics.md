---
title: SQL-lekérdezési metrikák az Azure Cosmos DB SQL API-hoz
description: Megtudhatja, hogyan eszköz és hibakeresés az Azure Cosmos DB-kérések SQL-lekérdezési teljesítmény.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: sngun
ms.openlocfilehash: ae1773ec1d470b9cff2efb00c200427b7b4c2fb4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "69614816"
---
# <a name="tuning-query-performance-with-azure-cosmos-db"></a>A lekérdezési teljesítmény finomhangolása az Azure Cosmos DB-vel

Az Azure Cosmos DB egy [SQL API-t biztosít az adatok lekérdezéséhez,](how-to-sql-query.md)séma vagy másodlagos indexek nélkül. Ez a cikk a következő információkat tartalmazza a fejlesztők számára:

* Az Azure Cosmos DB SQL-lekérdezés-végrehajtási működésének magas szintű részletei
* Részletek a lekérdezési kérelem- és válaszfejlécekről, valamint az ügyfél SDK-beállításairól
* Tippek és gyakorlati tanácsok a lekérdezési teljesítményhez
* Példák az SQL-végrehajtási statisztikák használatára a lekérdezésteljesítmény hibakereséséhez

## <a name="about-sql-query-execution"></a>AZ SQL-lekérdezés ek-végrehajtása

Az Azure Cosmos DB-ben tárolókban tárolja az adatokat, amelyek bármilyen [tárolási méretre vagy átviteli kapacitásra](partition-data.md)növekedhetnek. Az Azure Cosmos DB zökkenőmentesen skálázható adatok at fizikai partíciók a borítók alatt az adatok növekedésének kezelése vagy a kiosztott átviteli teljesítmény növelése. Sql-lekérdezéseket bármely tárolónak kiadhat a REST API vagy a támogatott [SQL SDK-k](sql-api-sdk-dotnet.md)egyik használatával.

A particionálás rövid áttekintése: definiálhat egy partíciókulcsot, például a "város", amely meghatározza, hogyan oszlik meg az adatok fizikai partíciók között. Az egyetlen partíciókulcshoz tartozó adatok (például "város" == "Seattle") egy fizikai partíción belül tárolódnak, de általában egyetlen fizikai partíciónak több partíciókulcsa van. Amikor egy partíció eléri a tárolási méretét, a szolgáltatás zökkenőmentesen felosztja a partíciót két új partícióra, és egyenletesen osztja el a partíciókulcsot ezek között a partíciók között. Mivel a partíciók átmenetiek, az API-k egy "partíciókulcs-tartomány" absztrakcióját használják, amely a partíciókulcs-kivonatok tartományait jelöli. 

Amikor lekérdezést ad ki az Azure Cosmos DB-nek, az SDK az alábbi logikai lépéseket hajtja végre:

* Elemezze az SQL-lekérdezést a lekérdezés végrehajtási tervének meghatározásához. 
* Ha a lekérdezés szűrőt tartalmaz a `SELECT * FROM c WHERE c.city = "Seattle"`partíciókulcshoz, például a rendszer egyetlen partícióra irányítja. Ha a lekérdezés nem rendelkezik szűrővel a partíciókulcson, akkor a rendszer az összes partíción végrehajtja, és az eredmények egyesített ügyféloldalt eredményeznek.
* A lekérdezés végrehajtása az egyes partíciókon belül sorozatban vagy párhuzamosan, az ügyfél konfigurációja alapján. Az egyes partíciókon belül a lekérdezés a lekérdezés összetettségétől, a konfigurált oldalmérettől és a gyűjtemény kiosztott átviteli hangától függően egy vagy több adatváltást tehet le. Minden végrehajtás a lekérdezés végrehajtása és szükség esetén a lekérdezés-végrehajtási statisztikák által felhasznált [kérelemegységek](request-units.md) számát adja vissza. 
* Az SDK a lekérdezés i. partíciók közötti összegzését hajtja végre. Ha például a lekérdezés egy ORDER BY-t tartalmaz a partíciók között, akkor az egyes partíciók ból származó eredmények egyesítése az eredmények globálisan rendezett sorrendbe adása. Ha a lekérdezés egy aggregációs, mint `COUNT`a, az egyes partíciók száma össze, hogy a teljes szám.

Az SDK-k különböző beállításokat biztosítanak a lekérdezés végrehajtásához. A .NET részben például ezek `FeedOptions` a beállítások az osztályban érhetők el. Az alábbi táblázat ezeket a beállításokat és a lekérdezések végrehajtási idejének befolyásolásának módját ismerteti. 

| Beállítás | Leírás |
| ------ | ----------- |
| `EnableCrossPartitionQuery` | Minden olyan lekérdezésnél, amelyet egynél több partíción kell végrehajtani, igaz értékre kell állítani. Ez egy explicit zászló, amely lehetővé teszi, hogy tudatos teljesítmény kompromisszumokat a fejlesztési idő alatt. |
| `EnableScanInQuery` | Ha leiratkozott az indexelésről, akkor igaz értékre kell állítania, de mindenképpen futtatni szeretné a lekérdezést egy vizsgálaton keresztül. Csak akkor alkalmazható, ha a kért szűrőelérési út indexelése le van tiltva. | 
| `MaxItemCount` | A kiszolgálóra oda-visszaút esetén visszaadandó elemek maximális száma. A -1 beállítással a kiszolgáló kezelheti az elemek számát. Azt is megteheti, hogy ezt az értéket úgy csökkentheti, hogy oda-visszaonként csak kis számú elemet kér be. 
| `MaxBufferedItemCount` | Ez egy ügyféloldali beállítás, amely a partíciók közötti ORDER BY végrehajtásakor korlátozza a memóriafelhasználást. A magasabb érték segít csökkenteni a partíciók közötti rendezés késését. |
| `MaxDegreeOfParallelism` | Lekérdezi vagy beállítja az egyidejű műveletek futtatásának ügyféloldali párhuzamos lekérdezésvégrehajtása az Azure Cosmos adatbázis-szolgáltatás ban. A pozitív tulajdonságérték az egyidejű műveletek számát a beállított értékre korlátozza. Ha 0-nál kisebb re van állítva, a rendszer automatikusan meghatározza a futtatandó egyidejű műveletek számát. |
| `PopulateQueryMetrics` | Lehetővé teszi a lekérdezésvégrehajtásá különböző fázisaiban , például a fordítási idő, az indexciklus-idő és a dokumentum betöltési idejének statisztikai statisztikáinak részletes naplózását. A lekérdezési statisztikákból származó kimenetet megoszthatja az Azure Support szolgáltatással a lekérdezési teljesítményproblémák diagnosztizálása érdekében. |
| `RequestContinuation` | Folytathatja a lekérdezés végrehajtását, ha átadja az átlátszatlan folytatási jogkivonatot, amelyet bármely lekérdezés visszaad. A folytatási jogkivonat magában foglalja a lekérdezés végrehajtásához szükséges összes állapotot. |
| `ResponseContinuationTokenLimitInKb` | Korlátozhatja a kiszolgáló által visszaadott folytatási token maximális méretét. Előfordulhat, hogy ezt be kell állítania, ha az alkalmazásgazda korlátozza a válaszfejléc méretét. Ennek beállítása növelheti a lekérdezés teljes időtartamát és a környezetben felhasznált rövid erőforrásokat.  |

Például vegyünk egy példa lekérdezést a partíciókulcsként `/city` kért és 100 000 RU/s átviteli igényű gyűjteményegy gyűjteményen kért lekérdezést. Ezt a lekérdezést a .NET-ben a következő hez hasonlóan `CreateDocumentQuery<T>` kérheti:

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

A fent látható SDK-kódrészlet a következő REST API-kérésnek felel meg:

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

Minden lekérdezés-végrehajtási lap megfelel `POST` egy `Accept: application/query+json` REST API-t a fejléc, és az SQL-lekérdezés a szervezetben. Minden lekérdezés egy vagy több oda-vissza `x-ms-continuation` utat tesz a kiszolgálóra úgy, hogy a token az ügyfél és a kiszolgáló között visszhangzik a végrehajtás folytatásához. A FeedOptions konfigurációs beállításai kérésfejlécek formájában kerülnek át a kiszolgálóhoz. Például `MaxItemCount` megfelel a `x-ms-max-item-count`. 

A kérelem a következő (az olvashatóság érdekében csonkolt) választ adja vissza:

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

A lekérdezésből visszaadott kulcsválasz-fejlécek a következők:

| Beállítás | Leírás |
| ------ | ----------- |
| `x-ms-item-count` | A válaszban visszaadott elemek száma. Ez a megadott `x-ms-max-item-count`, a maximális válasz hasznos terhelési méretén, a kiosztott átviteli sebességen és a lekérdezés végrehajtási idején belül elférő elemek számától függ. |  
| `x-ms-continuation:` | A lekérdezés végrehajtásának folytatásához a folytatási jogkivonat, ha további eredmények érhetők el. | 
| `x-ms-documentdb-query-metrics` | A végrehajtás lekérdezési statisztikája. Ez egy tagolt karakterlánc, amely a lekérdezés végrehajtásának különböző fázisaiban töltött idő statisztikáit tartalmazza. Visszaadta, `x-ms-documentdb-populatequerymetrics` ha `True`a beállítása . | 
| `x-ms-request-charge` | A lekérdezés által felhasznált [kérelemegységek](request-units.md) száma. | 

A REST API-kérelemfejléceiről és -beállításairól az [Erőforrások lekérdezése a REST API használatával című témakörben](https://docs.microsoft.com/rest/api/cosmos-db/querying-cosmosdb-resources-using-the-rest-api)talál további információt.

## <a name="best-practices-for-query-performance"></a>Gyakorlati tanácsok a lekérdezési teljesítményhez
Az alábbiakban a leggyakoribb tényezők, amelyek hatással vannak az Azure Cosmos DB lekérdezési teljesítmény. Mélyebbre ásunk az alábbi témakörök mindegyikében ebben a cikkben.

| Tényező | Tipp | 
| ------ | -----| 
| Kiosztott átviteli sebesség | Mérési ru lekérdezésenként, és győződjön meg arról, hogy rendelkezik a lekérdezésekhez szükséges kiépített átviteli. | 
| Particionálási és partíciókulcsok | Előnyben részesíti a partíciókulcs-értékkel rendelkező lekérdezéseket a szűrőzáradékban az alacsony késés miatt. |
| SDK- és lekérdezési beállítások | Kövesse az SDK ajánlott eljárásokat, például a közvetlen kapcsolatot, és hangolja be az ügyféloldali lekérdezés végrehajtási beállításait. |
| Hálózati késleltetés | Vegye figyelembe a hálózati terhelést a méréssorán, és használja a többhoming API-kat a legközelebbi régióból történő olvasáshoz. |
| Indexelési szabályzat | Győződjön meg arról, hogy rendelkezik a lekérdezéshez szükséges indexelési elérési utakkal/házirenddel. |
| Lekérdezésvégrehajtási mutatók | Elemezze a lekérdezés végrehajtási metrikák at a lekérdezési és adatalakzatok lehetséges újraírásainak azonosításához.  |

### <a name="provisioned-throughput"></a>Kiosztott átviteli sebesség
A Cosmos DB-ben adattárolókat hoz létre, amelyek mindegyike kérésegységekben (RU) /másodpercben kifejezett fenntartott átviteli sebességű. Az 1 KB-os dokumentum olvasása 1 RU, és minden művelet (beleértve a lekérdezéseket is) a műveletazonosítók összetettsége alapján meghatározott számú vt-re normalizálódik. Például ha 1000 RU/s kiépített a tároló, és van `SELECT * FROM c WHERE c.city = 'Seattle'` egy lekérdezés, mint amely 5 vt-k, majd hajtsa végre (1000 RU/s) / (5 RU/query) = 200 lekérdezés/s ilyen lekérdezések másodpercenként. 

Ha több mint 200 lekérdezést küld másodpercenként, a szolgáltatás 200/s feletti sebességkorlátozó kéréseket indít el. Az SDK-k automatikusan kezelik ezt az esetet egy visszalépés/újrapróbálkozás végrehajtásával, ezért előfordulhat, hogy ezek a lekérdezések nagyobb késést észlelnek. A kiosztott átviteli teljesítmény növelése a szükséges értékre javítja a lekérdezés késését és átviteli képességét. 

Ha többet szeretne megtudni a kérelemegységekről, olvassa el az [Egységek kérése ..](request-units.md)

### <a name="partitioning-and-partition-keys"></a>Particionálási és partíciókulcsok
Az Azure Cosmos DB használatával a lekérdezések általában a következő sorrendben, a leggyorsabb/leghatékonyabb tól a lassabbig és kevésbé hatékonyig hajthatók végre. 

* GET egyetlen partíciókulcsés elemkulcs
* Szűrőzáradékkal rendelkező lekérdezés egyetlen partíciókulcson
* Egyenlőség- vagy tartományszűrő záradék nélküli lekérdezés bármely tulajdonságon
* Lekérdezés szűrők nélkül

Lekérdezések, amelyek nek konzultálniuk kell az összes partíciót kell nagyobb késést, és magasabb rus-k felhasználásával. Mivel minden partíció automatikus indexelés az összes tulajdonság, a lekérdezés hatékonyan kiszolgálható az index ebben az esetben. A párhuzamossági beállításokkal gyorsabban leteheti a partíciókat átszelő lekérdezéseket.

A particionálásról és a partíciókulcsokról az [Azure Cosmos DB particionálása.](partition-data.md)

### <a name="sdk-and-query-options"></a>SDK- és lekérdezési beállítások
Tekintse meg a teljesítménytippek és [teljesítménytesztelés című](performance-testing.md) [témakört,](performance-tips.md) amelyből megtudhatja, hogyan szerezheti be a legjobb ügyféloldali teljesítményt az Azure Cosmos DB-től. Ez magában foglalja a legújabb SDK-k használatát, a platformspecifikus konfigurációk, például az alapértelmezett kapcsolatok számának konfigurálását, a szemétgyűjtés gyakoriságát és a könnyű kapcsolódási lehetőségek, például a Direct/TCP használatát. 


#### <a name="max-item-count"></a>Maximális cikkszám
Lekérdezések esetén az `MaxItemCount` értéke jelentős hatással lehet a végpontok között lekérdezési idő. Minden oda-vissza út a szerver re nem `MaxItemCount` több, mint az elemek száma (Alapértelmezett 100 elem). Ha ezt magasabb értékre állítja (-1 a maximum és ajánlott), a lekérdezés teljes időtartama összességében javítja a kiszolgáló és az ügyfél közötti adatváltások számát, különösen a nagy eredményhalmazokkal rendelkező lekérdezések esetében.

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        MaxItemCount = -1, 
    }).AsDocumentQuery();
```

#### <a name="max-degree-of-parallelism"></a>A párhuzamosság maximális mértéke
Lekérdezések esetén `MaxDegreeOfParallelism` állítsa be a, hogy azonosítsa az alkalmazás legjobb konfigurációit, különösen akkor, ha partícióközi lekérdezéseket hajt végre (a partíció-kulcs értékének szűrője nélkül). `MaxDegreeOfParallelism`a párhuzamos feladatok maximális számát szabályozza, azaz a párhuzamosan meglátogatott partíciók maximális számát. 

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

Tegyük fel, hogy
* D = Alapértelmezett Párhuzamos feladatok maximális száma (= az ügyfélgépen lévő processzorok teljes száma)
* P = A párhuzamos feladatok felhasználó által megadott maximális száma
* N = A lekérdezés megválaszolásához meglátogatott partíciók száma

A következőkben a párhuzamos lekérdezések hogyan viselkednének a P különböző értékeire.
* (P == 0) => soros üzemmód
* (P == 1) => Egy tevékenység maximális száma
* (P > 1) => min (P, N) párhuzamos feladatok 
* (P < 1) => Min (N, D) párhuzamos feladatok

Az SDK kiadási megjegyzések, valamint a megvalósított osztályok és metódusok részleteiaz [SQL SDK-k](sql-api-sdk-dotnet.md)

### <a name="network-latency"></a>Hálózati késleltetés
Tekintse meg az [Azure Cosmos DB globális disztribúcióját](tutorial-global-distribution-sql-api.md) a globális disztribúció beállításáról és a legközelebbi régióhoz való csatlakozásról. A hálózati késés jelentős hatással van a lekérdezés teljesítményére, ha több oda-vissza utat kell tennie, vagy nagy eredményhalmazt kell lekérnie a lekérdezésből. 

A lekérdezésvégrehajtási metrikák szakasza bemutatja, hogyan `totalExecutionTimeInMs`lehet lekérni a lekérdezések ( kiszolgáló végrehajtási idejét ( ), így különbséget tehet a lekérdezés végrehajtásával töltött idő és a hálózati átvitel során töltött idő között.

### <a name="indexing-policy"></a>Indexelési szabályzat
Lásd: [Indexelési házirend beállítása](index-policy.md) az elérési utak, -fajták és -módok indexelése, valamint a lekérdezések végrehajtásának hatása című témakörben. Alapértelmezés szerint az indexelési házirend kivonatindexelést használ a karakterláncokhoz, amely hatékony az egyenlőségi lekérdezésekhez, de a lekérdezések általi tartománylekérdezésekhez/sorrendhez nem. Ha tartománylekérdezésekre van szüksége a karakterláncokhoz, javasoljuk, hogy adja meg a Tartomány index típusát az összes karakterlánchoz. 

Alapértelmezés szerint az Azure Cosmos DB automatikus indexelést alkalmaz az összes adatra. Nagy teljesítményű beszúrási forgatókönyvek esetén fontolja meg az elérési utak kizárását, mivel ez csökkenti az egyes beszúrási műveletek vtv-költségét. 

## <a name="query-execution-metrics"></a>Lekérdezésvégrehajtási mutatók
A lekérdezések végrehajtásával kapcsolatos részletes metrikákat a választható `x-ms-documentdb-populatequerymetrics` fejléc (a`FeedOptions.PopulateQueryMetrics` .NET SDK) átadásával kaphatja meg. A visszaadott `x-ms-documentdb-query-metrics` érték a következő kulcs-érték párokkal rendelkezik, amelyek a lekérdezésvégrehajtásá speciális hibaelhárításához vannak szánva. 

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
| `totalExecutionTimeInMs` | ezredmásodperc | Lekérdezés végrehajtási ideje | 
| `queryCompileTimeInMs` | ezredmásodperc | Lekérdezés fordítási ideje  | 
| `queryLogicalPlanBuildTimeInMs` | ezredmásodperc | A logikai lekérdezési terv elkészítésének ideje | 
| `queryPhysicalPlanBuildTimeInMs` | ezredmásodperc | Fizikai lekérdezési terv elkészítésének ideje | 
| `queryOptimizationTimeInMs` | ezredmásodperc | A lekérdezés optimalizálásával töltött idő | 
| `VMExecutionTimeInMs` | ezredmásodperc | A lekérdezési futásidejű időszakban töltött idő | 
| `indexLookupTimeInMs` | ezredmásodperc | A fizikai indexrétegben töltött idő | 
| `documentLoadTimeInMs` | ezredmásodperc | A dokumentumok betöltése során eltöltött idő  | 
| `systemFunctionExecuteTimeInMs` | ezredmásodperc | A rendszer (beépített) függvényeinek végrehajtó összes ideje ezredmásodpercben  | 
| `userFunctionExecuteTimeInMs` | ezredmásodperc | A felhasználó által definiált függvények végrehajtásával töltött teljes idő ezredmásodpercben | 
| `retrievedDocumentCount` | count | Beolvasott dokumentumok teljes száma  | 
| `retrievedDocumentSize` | Bájt | Beolvasott dokumentumok teljes mérete bájtban  | 
| `outputDocumentCount` | count | Kimeneti bizonylatok száma | 
| `writeOutputTimeInMs` | ezredmásodperc | Lekérdezés végrehajtási ideje ezredmásodpercben | 
| `indexUtilizationRatio` | arány (<=1) | A szűrővel egyeztetett dokumentumok számának és a betöltött dokumentumok számának aránya  | 

Az ügyfél SDK-k belsőleg több lekérdezési műveleteket, hogy a lekérdezés tã¶rtÃgÃG ã¶sszevonã¶sna. Az ügyfél partíciónként egynél több hívást hajt `x-ms-max-item-count`végre, ha az összesített eredmény meghaladja , ha a lekérdezés meghaladja a partíció kiosztott átviteli rendszerét, vagy ha a lekérdezés hasznos terhelése eléri az oldalankénti maximális méretet, vagy ha a lekérdezés eléri a rendszer által lefoglalt időkorlátot. Minden részleges lekérdezés-végrehajtás a `x-ms-documentdb-query-metrics` lapot ad vissza az adott laphoz. 

Íme néhány mintalekérdezés, és hogyan értelmezhető a lekérdezés végrehajtásából visszaadott metrikák némelyike: 

| Lekérdezés | Mintametria | Leírás | 
| ------ | -----| ----------- |
| `SELECT TOP 100 * FROM c` | `"RetrievedDocumentCount": 101` | A beolvasott dokumentumok száma 100+1 a TOP záradéknak megfelelően. A lekérdezési idő `WriteOutputTime` `DocumentLoadTime` többnyire töltött, és mivel ez egy vizsgálat. | 
| `SELECT TOP 500 * FROM c` | `"RetrievedDocumentCount": 501` | A RetrievedDocumentCount mostantól magasabb (500+1 a TOP záradéknak megfelelően). | 
| `SELECT * FROM c WHERE c.N = 55` | `"IndexLookupTime": "00:00:00.0009500"` | Körülbelül 0,9 ms-ot költ ünk az IndexLookupTime-ban egy kulcskeresményhez, mert ez egy `/N/?`index-keresm én. | 
| `SELECT * FROM c WHERE c.N > 55` | `"IndexLookupTime": "00:00:00.0017700"` | Valamivel több időt (1,7 ms) töltött IndexLookupTime egy tartomány ban scan, mert ez egy index keres `/N/?`. | 
| `SELECT TOP 500 c.N FROM c` | `"IndexLookupTime": "00:00:00.0017700"` | A korábbi `DocumentLoadTime` lekérdezésekkel azonos időt `WriteOutputTime` töltött, de alacsonyabb, mert csak egy tulajdonságot vetítünk ki. | 
| `SELECT TOP 500 udf.toPercent(c.N) FROM c` | `"UserDefinedFunctionExecutionTime": "00:00:00.2136500"` | Körülbelül 213 ms-ot `UserDefinedFunctionExecutionTime` fordítanak az UDF `c.N`végrehajtására a minden egyes értékére. |
| `SELECT TOP 500 c.Name FROM c WHERE STARTSWITH(c.Name, 'Den')` | `"IndexLookupTime": "00:00:00.0006400", "SystemFunctionExecutionTime": "00:00:00.0074100"` | Körülbelül 0,6 ms `IndexLookupTime` költött `/Name/?`a . A lekérdezés végrehajtási idejének nagy része `SystemFunctionExecutionTime`(~7 ms) a rendszerben. |
| `SELECT TOP 500 c.Name FROM c WHERE STARTSWITH(LOWER(c.Name), 'den')` | `"IndexLookupTime": "00:00:00", "RetrievedDocumentCount": 2491,  "OutputDocumentCount": 500` | A lekérdezés vizsgálatként történik, `LOWER`mert a program a programot használja, és a 2491 beolvasott dokumentumból 500-at ad vissza. |


## <a name="next-steps"></a>További lépések
* A támogatott SQL-lekérdezés-operátorokról és kulcsszavakról az [SQL-lekérdezés](sql-query-getting-started.md)című témakörben olvashat. 
* A kérelemegységekről a [kérelemegységek ről](request-units.md)olvashat.
* Az indexelési szabályzatról az [indexelési szabályzat című](index-policy.md) témakörben olvashat. 


