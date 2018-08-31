---
title: Particionálás és vízszintes méretezés az Azure Cosmos DB |} A Microsoft Docs
description: Ismerje meg az Azure Cosmos DB, hogyan konfigurálja a particionálási és particionálhatja a kulcsok és hogy miként választható ki a megfelelő partíciókulcs az alkalmazás hogyan particionálási működését.
services: cosmos-db
author: aliuy
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 07/26/2018
ms.author: andrl
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3cc2794105eff196c3e1db02d664a89c9b37e318
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2018
ms.locfileid: "43286985"
---
# <a name="partition-and-scale-in-azure-cosmos-db"></a>Particionálási és horizontális Azure Cosmos DB-ben

[Az Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) egy globálisan elosztott, többmodelles adatbázis-szolgáltatás gyors és kiszámítható teljesítményt nyújt segítséget. Zökkenőmentesen skálázható együtt az alkalmazás. Ez a cikk áttekintést hogyan működik minden az adatok particionálása a modellek az Azure Cosmos DB-ben. Emellett bemutatja, hogyan lehet az alkalmazások hatékony méretezése az Azure Cosmos DB-tárolók konfigurálása.

## <a name="partitioning-in-azure-cosmos-db"></a>Az Azure Cosmos DB particionálási
Az Azure Cosmos DB (dokumentumok) gyűjtemények, diagramok és táblázatok nevezett adatok tárolására szolgáló tárolókat biztosít. A tárolók logikai erőforrások, és a egy vagy több fizikai partíciók vagy a kiszolgálók is kiterjedhetnek. A partíciók számát, a tárméret alapján az Azure Cosmos DB és az átviteli sebesség kiosztott részéért a egy tárolót vagy tárolók készletét határozza meg. 


### <a name="physical-partition"></a>Fizikai partíciónként

A *fizikai* partíciója egy meghatározott méretű változó mennyiségű számítási erőforrások (CPU és memória) kombinálva fenntartott SSD-alapú tárolást. Minden egyes fizikai partíciók magas rendelkezésre állás érdekében a rendszer replikálja. Tárolók minden készlete egy vagy több fizikai partíciók megoszthatja. Fizikai partíciók felügyeleti teljes körűen felügyelt Azure Cosmos DB által, és nem kell komplex kódot írnia, vagy a partíciók kezeléséhez. Az Azure Cosmos DB-tárolók olyan korlátlan tárolási és átviteli sebesség tekintetében. Fizikai partíciók egy Azure Cosmos DB belső fogalma, és átmeneti jellegűek. Az Azure Cosmos DB automatikusan skálázzák a számítási feladatok alapján fizikai partíciók száma. Így Ön nem corelate az adatbázis-tervezésben fizikai partíciók száma alapján inkább, gondoskodnia kell választani a megfelelő partíciókulcs, amely megadja, hogy a logikai partíció. 

### <a name="logical-partition"></a>Logikai partíció

A *logikai* partíciót belül egy fizikai partíciónak, amely egy adott partíciókulcs-értékhez társított minden adatot tárol. Több logikai partíciót is megtörténhet, az ugyanazon fizikai partícióján. Az alábbi ábrán az egyetlen tároló három logikai partícióval rendelkezik. Egyes logikai partíciók rendre egy partíciókulcsot, LAX AMS és MEL adatait tárolja. Minden olyan LAX AMS és MEL logikai partíció meghaladja a 10 GB-os logikai partíciók maximális korlát nem növelhető. 

![Erőforrás-particionálás](./media/introduction/azure-cosmos-db-partitioning.png) 

Ha egy tároló megfelel a [Előfeltételek particionálás](#prerequisites), particionálási rendszer teljes mértékben átlátható az alkalmazás. Az Azure Cosmos DB kezeli az adatok eloszthatók a fizikai és logikai partíció, és a megfelelő partícióra lekérdezésekre vonatkozó kérelmek útválasztását. 

## <a name="how-does-partitioning-work"></a>A particionálás működése

Mindegyik dokumentumnak rendelkeznie kell egy *partíciókulcs* és a egy *sorkulcs*, amely egyedi azonosításához. A partíciós kulcs működik-e az adatok logikai partíció, és határ természetes Azure Cosmos DB biztosítja az adatok eloszthatók a fizikai partíciók. **Egy logikai partíció adatait egyetlen fizikai partíciók belül kell lennie, és az Azure Cosmos DB által kezelt fizikai partíciók felügyeleti**. 

Röviden a következő particionálás hogyan működik az Azure Cosmos DB:

* Üzembe helyez egy Azure Cosmos DB-tárolókat készletét **T** (kérések száma másodpercenként) RU/s átviteli sebesség.  
* A színfalak mögött az Azure Cosmos DB kiosztja kiszolgálására szükséges fizikai partíciók **T** vonatkozó kérelmek másodpercenkénti száma. Ha **T** magasabb, mint a maximális átviteli sebesség száma fizikai partíciónként **t**, majd az Azure Cosmos DB rendelkezések **N T/t =** fizikai partíciókra. Azure Cosmos DB által konfigurált partition(t) egységenkénti maximális adatátviteli sebesség értékének, ez az érték hozzá van rendelve, a teljes kiosztott átviteli kapacitás és a használt hardveres konfigurációja alapján.  
* Az Azure Cosmos DB foglalja le a fő területet partíció kulcs kivonatok kitöltéssel a **N** fizikai partíciókra. Így minden egyes fizikai partíciók által üzemeltetett logikai partíciók száma **1/N** * partíciókulcs-értékek száma.  
* Ha egy fizikai partíciónak **p** éri el a tárolási korlátot, az Azure Cosmos DB zökkenőmentesen bontja **p** be két új fizikai partíciók **p1** és **p2**. A kulcsok minden egyes új fizikai partíciókra körülbelül felét megfelelő értékeket osztja el. Ez a felosztási művelet az alkalmazás láthatatlan. Ha egy fizikai partíciónak eléri a tárolási kapacitását, és ugyanazt a logikai partíciókulcsot az adatok fizikai partíción tartozik, a felosztási művelet nem történik meg. Ennek az oka egy egyetlen logikai partíciókulcsot minden adatát ugyanazon fizikai partícióján kell lennie. Ebben az esetben egy másik partíció stratégiájának kell alkalmazni.  
* Amikor üzembe helyez nagyobb átviteli sebesség **t * N**, az Azure Cosmos DB oszt fel egy vagy több, a fizikai partíciók támogatása a nagyobb sebesség érdekében.

A partíciókulcsok szemantikát kissé eltérő, minden egyes API-t, szemantikájának megfelelően, az alábbi táblázatban látható módon:

| API | Partíciókulcs | Sorkulcs |
| --- | --- | --- |
| SQL | Egyéni partíciós kulcs elérési útja | `id` kijavítva | 
| MongoDB | Egyéni szegmenskulcs  | `_id` kijavítva | 
| Gremlin | Egyéni partíció kulcstulajdonság | `id` kijavítva | 
| Tábla | `PartitionKey` kijavítva | `RowKey` kijavítva | 

Az Azure Cosmos DB használ a kivonat-alapú particionálás. Egy cikk írásakor az Azure Cosmos DB kivonatolja a partíciókulcs-értékkel, és a kivonatolt eredmény segítségével határozza meg, hogy melyik partíciót kell tárolni az elemet. 

> [!NOTE]
> Az Azure Cosmos DB az ugyanazon fizikai partícióján ugyanazzal a partíciókulccsal rendelkező összes elem tárolja. 

## <a name="best-practices-when-choosing-a-partition-key"></a>Ha olyan partíciókulcsot ajánlott eljárások

A partíciós kulcs, amely egy fontos döntés, hogy módosítania kell a tervezés során. Válassza ki a tulajdonság nevét, a széles körű értékeket, és még az adathozzáférési mintáknak megfelelően. Ajánlott eljárás az eltérő értékeket (például több száz vagy ezer) sok partíciókulcsot kell legyen. Lehetővé teszi a számítási feladatok egyenletes elosztása a különböző ezeket az értékeket. Ideális egy partíciókulcsot, amelyik a lekérdezéseket szűrőként gyakran megjelenik, és annak biztosítása érdekében, a megoldás méretezhető elegendő számossága.

Ha egy fizikai partíciónak eléri a tárolási kapacitását, és az adatok a partíció ugyanazzal a partíciókulccsal rendelkezik, az Azure Cosmos DB adja vissza a *"partíciókulcs elérte maximális mérete 10 GB-os"* üzenetet, és a partíció nincs felosztva. Nagyon fontos döntés remek partíciókulcs választása. 

Olyan partíciókulcsot válasszon, hogy:

* A tároló terjesztési még minden kulcs esetében.  
* A kötet terjesztési kérelmek adott időben még minden kulcs esetében.  

  Célszerű ellenőrizni, hogy az adatok partíciókba vannak elosztva. A portálon az adatok terjesztési ellenőrzéséhez nyissa meg az Azure Cosmos DB-fiókot, és kattintson a **metrikák** a **figyelés** szakaszt, és kattintson a **tárolási** lapján megtekintheti, hogyan a adatok particionálása különböző fizikai partíciók között.

  ![Erőforrás-particionálás](./media/partition-data/partitionkey-example.png)

  A bal oldali a fenti kép az eredmény hibás partíciókulcs és a megfelelő a fenti kép az eredményt, ha egy remek partíciókulcs-t választotta. A bal oldali ábrán láthatja, hogy az adatok nem egyenletesen el van a partíciók közt. Törekedni kell választani egy partíciókulcsot, amely elosztja a adatait, hogy meggyőződjünk a rendszerkép hasonlóan néz ki.

* Lekérdezések, amelyek nagy feldolgozási által elindított hatékonyan átirányíthatók a szűrőpredikátumban többek között a partíciókulcs alapján.  
* Partíciókulcs választása magasabb számossága általában előnyben részesített – mert, általában poskytne jobb terjesztési és méretezhetőségét. Például egy szintetikus kulcsot is formátumú elkülönített változó összefűzésével előállítjuk növelheti a számosságot több tulajdonságok értékeit.  

Ha úgy dönt, hogy a fenti szempontok a partíciókulcsot, nem kell aggódnia a partíciók vagy száma fizikai partíciónként, milyen átviteli sebességre lefoglalt számát, az Azure Cosmos DB elvégzi a horizontális felskálázást fizikai partíciók száma és is méretezheti a az egyes partíciók szükséges.

<a name="prerequisites"></a>
## <a name="prerequisites-for-partitioning"></a>A particionálás előfeltételei

Az Azure Cosmos DB-tárolók hozhatók létre rögzített vagy korlátlan, az Azure Portalon. A rögzített méretű tárolók mérete legfeljebb 10 GB, feldolgozási sebessége legfeljebb 10000 RU/s lehet. Hozzon létre egy tároló korlátlan, az egy partíciókulcsot és a egy minimális 1000 RU/s átviteli kell megadnia. Az Azure Cosmos DB-tárolók is konfigurálható átviteli sebesség, tárolók, ahol a tárolók adja meg kell között megosztani egy partíció kulcsra, és korlátlan növelhető. Csak az alábbi előfeltételek particionálás és méretezés figyelembe venni:

* Ha (például egy gyűjteményt, gráf vagy tábla) tároló létrehozása az Azure Portalon, válassza ki a **korlátlan** tárolási kapacitás lehetőség kihasználásához korlátlan méretezés. A fizikai partíciók auto-bontás **p1** és **p2** leírtak szerint [particionálási működése](#how-does-partitioning-work), a tárolót létre kell hozni egy 1000 RU/s átviteli sebesség vagy több (vagy több tároló megosztás átviteli), és a egy partíciókulcsot kell megadni. 

* Ha létrehozott egy tárolót az Azure Portalon vagy programozott módon, és a kezdeti átviteli volt 1000 RU/s, vagy több megadott egy partíciókulcsot, akkor kihasználhatja korlátlan méretezés a tároló módosítása nélkül. Ez magában foglalja **rögzített méretű** tárolók, amennyiben a kezdeti tároló létrehozásakor legalább 1000 RU/s átviteli sebesség és a partíciókulcs meg van adva.

* Az összes tároló átviteli megosztásához tárolók készletét részeként konfigurált számít **korlátlan** tárolók.

Ha létrehozott egy **rögzített méretű** tároló nélkül partícióazonosító kulcs vagy az átviteli sebesség kisebb, mint 1000 RU/s, a tároló nem automatikus skálázás lesz. Az adatok áttelepíthetők rögzített tárolóba a korlátlan tároló, kell használnia a [adatáttelepítési eszközét](import-data.md) vagy a [módosítási hírcsatorna könyvtár](change-feed.md). 

## <a name="partitioning-and-provisioned-throughput"></a>Particionálási és kiosztott átviteli sebesség
Az Azure Cosmos DB kiszámítható teljesítményt lett tervezve. Amikor létrehoz egy tárolót vagy tárolók, az átviteli sebességet, hogy fenntartott  *[kérelemegység](request-units.md) (RU) másodpercenként*. Minden kérelmet lehetővé teszi egy RU díj, amely a rendszer-erőforrásokat, például a Processzor, memória és a művelet által felhasznált i/o-mennyiség arányos. Egy 1 KB-os dokumentum munkamenet-konzisztencia az olvasási használ fel 1 RU. Olvasás: 1 RU tárolt elemek száma és a egy időben futó egyidejű kérelmek száma függetlenül. Nagyobb elemek méretétől függően a nagyobb kérelemegység szükséges. Ha ismeri az entitások és támogatása az alkalmazáshoz szükséges olvasások száma, pontos mennyisége az alkalmazás számára szükséges átviteli sebességtől telepíthet. 

> [!NOTE]
> Használja ki teljesen a átviteli sebesség kiosztott részéért a egy tárolót vagy tárolók készletét, választania kell egy partíciókulcsot, amely lehetővé teszi, hogy egyenlően eloszthatja a kérelmeket minden különálló partíciós kulcsérték között.
> 
> 

<a name="designing-for-partitioning"></a>
## <a name="create-partition-key"></a>Partíciós kulcs létrehozása 
Használhatja az Azure Portalon vagy az Azure CLI-tárolók létrehozása, és bármikor skálázhatja őket. Ez a szakasz bemutatja, hogyan hozhat létre tárolókat, és adja meg a kiosztott átviteli sebesség és a partíció kulcsot minden egyes API-val.


### <a name="sql-api"></a>SQL API
A következő minta bemutatja, hogyan hozzon létre egy tárolót (gyűjteményt), az SQL API használatával. 

```csharp
DocumentClient client = new DocumentClient(new Uri(endpoint), authKey);
await client.CreateDatabaseAsync(new Database { Id = "db" });

DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 20000 });
```

Egy elem (dokumentum) használatával beolvashatja a `GET` metódus a REST API vagy a használatával `ReadDocumentAsync` az SDK-k egyikén.

```csharp
// Read document. Needs the partition key and the ID to be specified
DeviceReading document = await client.ReadDocumentAsync<DeviceReading>(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });
```

További információkért lásd: [particionálást az Azure Cosmos DB SQL API használatával](sql-api-partition-data.md).

### <a name="mongodb-api"></a>MongoDB API
A MongoDB API-val létrehozhat egy gyűjteményt a kedvenc eszközzel, az illesztőprogram vagy az SDK segítségével. Ebben a példában használjuk a Mongo Shell hozzon létre egy gyűjteményt.

A Mongo-rendszerhéjban:

```
db.runCommand( { shardCollection: "admin.people", key: { region: "hashed" } } )
```
    
Eredmények:

```JSON
{
    "_t" : "ShardCollectionResponse",
    "ok" : 1,
    "collectionsharded" : "admin.people"
}
```

### <a name="table-api"></a>Tábla API

Hozzon létre egy táblát, a Table API-val, használja a `CreateIfNotExists` metódust. 

```csharp
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

CloudTable table = tableClient.GetTableReference("people");
table.CreateIfNotExists(throughput: 800);
```

Kiosztott átviteli sebesség van beállítva, argumentumként `CreateIfNotExists`. A partíciókulcs implicit módon létrejön a `PartitionKey` értéket. 

Az alábbi kód használatával kérheti le egyetlen entitást:

```csharp
// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = table.Execute(retrieveOperation);
```
További információkért lásd: [fejlesztés a Table API-val](tutorial-develop-table-dotnet.md).

### <a name="gremlin-api"></a>Gremlin API

A Gremlin API-val használhatja az Azure portal vagy az Azure CLI-vel, hozzon létre egy tárolót, amely egy graph jelöli. Azt is megteheti mivel az Azure Cosmos DB többmodelles, használhatja a más API-k egyik hozhat létre, és a graph-tároló méretezése.

Minden csomópont- vagy peremtábla a partíciókulcs és Azonosítóját a Gremlin használatával olvashatja. Például egy Graph-régióhoz ("USA") a partíciókulcs és a "Seattle" sorkulcsként, annak csúcs a következő szintaxis használatával:

```
g.V(['USA', 'Seattle'])
```

A partíciókulcs és a sorkulcs egy edge hivatkozhat.

```
g.E(['USA', 'I5'])
```

További információkért lásd: [egy particionált graph használatával az Azure Cosmos DB](graph-partitioning.md).

## <a name="form-partition-key-by-concatenating-multiple-fields"></a>Űrlap partíciókulcs elkülönített változó összefűzésével előállítjuk több mező

Egy partíciókulcsot, is alkotnak, összefűzi és több tulajdonságértékek padding a elem egy egyetlen mesterséges "partitionKey" tulajdonságát be. Ezek a kulcsok szintetikus kulcsokként hivatkozunk.

Például hogy egy dokumentumot, amely a következőhöz hasonló:

```json
{
"deviceId": "abc-123",
"date": 2018
}
```

Az egyik lehetőség, hogy /deviceId vagy /date partitionKey beállításához. Ha szeretné az eszközazonosítót és a dátum partíciós kulcsot. Fűzze össze ezt a két értéket az olyan mesterséges "partitionKey" tulajdonságra, és állítsa a partíciókulcs /partitionKey.

```json
{
"deviceId": "abc-123",
"date": 2018,
"partitionKey": "abc-123-2018"
}
```

Valós idejű forgatókönyveket használhat dokumentumok több ezer, így az ügyfél kiszolgálóoldali logikát fűz össze adatokat egy szintetikus kulcs be, a szintetikus kulcs beszúrása a dokumentumok és, amellyel megadhassa a partíciókulcsot, be kell állítania.

<a name="designing-for-scale"></a>
## <a name="design-for-scale"></a>Méretezés tervezése
Az Azure Cosmos DB hatékony méretezését, válasszon ki egy remek partíciókulcs, a tároló létrehozásakor kell. Van egy remek partíciókulcs kiválasztásakor két fő szempontok:

* **Lekérdezési határ és a tranzakciók**. A választott partíciókulcs a követelménnyel szemben tranzakciók használata több partíciókulcsok egy méretezhető megoldás biztosítása érdekében az entitások szét kell terheléselosztást végeznie. Egy rendkívüli ugyanazzal a partíciókulccsal beállíthatja az összes elemet, de ezzel a beállítással korlátozhatja, hogy a megoldás méretezhetőségét. Vagyis minden egyedi partíciókulccsal rendelhet hozzá. Ez a választás rugalmasan méretezhető, de megakadályozza, hogy Ön kifejezett dokumentumok közötti tranzakciókat keresztül tárolt eljárások és eseményindítók használatával. Egy ideális partíciós kulcs lehetővé teszi, hogy hatékony lekérdezések, és annak biztosítása érdekében, a megoldás méretezhető elegendő számossága. 
* **Nincs tárolás és teljesítmény szűk**. Fontos válasszon egy tulajdonságot, amely lehetővé teszi az írási műveletek legyen elosztva a különböző különböző értékeket. Ugyanazzal a partíciókulccsal kérelmek nem haladhatja meg a kiosztott átviteli sebesség partíció lefoglalva, és sebessége korlátozott lesz. Ezért fontos, hogy az alkalmazáson belül "hotspotok" nem eredményez olyan partíciókulcsot válasszon. Egy partíción belül minden adatát egyetlen partíciókulcsot kell tárolni, mivel ne partíciókulcsok, amelyek nagy mennyiségű adat esetében ugyanazt az értéket. 

Az egyes, nézzük meg, néhány valós forgatókönyv és a jó partíciókulcsok:
* Ha egy felhasználó profil háttérrendszer implementálja a *Felhasználóazonosító* jó választás az olyan partíciókulcsot.
* Ha az IoT-adatok, például az eszköz állapotát, tárolja egy *Eszközazonosító* jó választás az olyan partíciókulcsot.
* Használata Azure Cosmos DB-naplózás idősorozat-adatokhoz, a *állomásnév* vagy *folyamat azonosítója* jó választás az olyan partíciókulcsot.
* Ha egy több-bérlős architektúra a *bérlőazonosító* jó választás az olyan partíciókulcsot.

Az egyes használati esetek, például IoT és a felhasználói profilok, a partíciókulcs lehet ugyanaz, mint a *azonosító* (dokumentum-kulcs). Más esetekben az idősorozat-adatokat, például lehetséges, hogy egy partíciókulcsot, amely eltér a *azonosító*.

### <a name="partitioning-and-loggingtime-series-data"></a>Particionálási és a naplózási/idősorozat-adatok
Az Azure Cosmos DB a gyakori alkalmazási helyzetek egyik, a naplózás és a telemetriai adatokat. Fontos remek partíciókulcs kiválasztása ebben a forgatókönyvben, mert előfordulhat, hogy kell olvasási/írási hatalmas mennyiségű adatot. A partíciókulcs választás attól függ, az olvasási és írási sebessége és a lekérdezések futtatásához várt típusú. Íme néhány tipp a megfelelő partíciókulcs kiválasztása:

* Ha a használati eset foglalja magában, amely egy hosszú idővel gyűlnek írási kis mértékű, le kell kérdeznie az időbélyegek sávokban további szűrőkkel, használja a összesítését, az időbélyeget. Ha például egy jó módszer, hogy dátum partíciókulcsként. Ezt a módszert használja lekérdezheti, ha az összes adatot egyetlen partícióról az adott dátumra vonatkozóan. 
* Ha a számítási feladatok írási műveltekből, amely nagyon gyakori az ebben a forgatókönyvben, használja a partíciós kulcs nem alapján történő küldés időbélyegzője legyen. Azure Cosmos DB-hez mint ilyen, terjesztése és írási műveletek egyenletesen méretezése különböző partíciók között. Itt egy *állomásnév*, *folyamat azonosítója*, *Tevékenységazonosító*, vagy egy másik tulajdonság a magas Számosság jó választás. 
* Másik megoldás, a hibrid megközelítés, ahol egy minden nap/hó, a több tároló van, és a partíciókulcs például egy részletesebb tulajdonság *állomásnév*. Ennek a megközelítésnek vannak az egyes tárolók vagy egy tárolót az időtartomány és a méretezés és teljesítmény igények alapján beállíthat különböző átviteli előnyökkel. Például egy tárolót az aktuális hónaphoz előfordulhat, hogy üzembe egy nagyobb átviteli sebességet, mert a szolgál az olvasások és írások. Korábbi hónapok előfordulhat, hogy építeni egy alacsonyabb átviteli sebességet, mivel a csak olvasási szolgál.

### <a name="partitioning-and-multitenancy"></a>Particionálás és a több-bérlős módhoz
Ha egy több-bérlős alkalmazásban, az Azure Cosmos DB, vannak-e két népszerű formátumukban kell figyelembe venni: *bérlőnként egy partíciókulcsot* és *bérlőnként több tároló*. Az alábbiakban és a hátrányai minden:

* **Bérlőnként egy partíciókulcsot**. Ebben a modellben a bérlők közös elhelyezésű belül egyetlen tároló. Lekérdezések és a egy adott bérlő Beszúrások ugyanazon a partíción végezhetők. A bérlőhöz tartozó összes elemek között is tranzakciós logikát valósíthat meg. Több bérlő osztozik egy tárolót, mivel jobban használhat tárolási és átviteli sebességet biztosítanak készletezési erőforrásokat belül egyetlen tároló minden bérlő esetében minden egyes bérlőhöz kiépítés helyett. A hátránya, hogy nem rendelkezik a teljesítmény-elkülönítési bérlőnként. A teljes tárolót a megcélzott növekszik és a bérlők számára egy adott bérlő alkalmazza a egyre nagyobb átviteli sebességet, hogy garantálja a teljesítményt.
* **Bérlőnként több tároló**. Ebben a modellben minden egyes bérlő rendelkezik saját tárolót, és a fenntartott átviteli sebességhez és bérlőnként garantált teljesítményt. Ez a modell több költséghatékony, több-bérlős alkalmazások néhány bérlőkkel.

A hibrid megközelítés, amely kis méretű bérlővel együtt colocates és különíti el a nagyobb méretű bérlők számára saját tárolókat is használhatja.

## <a name="next-steps"></a>További lépések
Ebben a cikkben azt fogalmakat és ajánlott eljárások áttekintését méretezhetők, és az Azure Cosmos DB particionálási nyújtja. 

* Ismerje meg [kiosztott átviteli sebesség az Azure Cosmos DB](request-units.md).
* Ismerje meg [az Azure Cosmos DB globális terjesztésének](distribute-data-globally.md).



