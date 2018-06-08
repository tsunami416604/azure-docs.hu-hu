---
title: Particionálás és Azure Cosmos DB horizontális skálázás |} Microsoft Docs
description: Ismerje meg, hogyan particionálási működését Azure Cosmos DB, hogyan lehet konfigurálni a particionálás és kulcsok partícióazonosító és hogyan válassza ki a megfelelő partíciókulcs az alkalmazáshoz.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: rimman
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bd1b52dd32976ce65458e1dfe1b50d228fbd6d0e
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34850525"
---
# <a name="partition-and-scale-in-azure-cosmos-db"></a>Partíció és a skála Azure Cosmos DB

[Az Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) egy globálisan elosztott, több modellre adatbázis szolgáltatás célja, hogy gyors és kiszámítható teljesítmény érdekében. Méretezés zökkenőmentesen együtt az alkalmazás növekedésével azt. Ez a cikk áttekintést az Azure Cosmos Adatbázisba hogyan működik az adatok particionálása modellek. Azt is bemutatja, hogyan konfigurálhatja a hatékony méretezést az alkalmazások Azure Cosmos DB tárolók.

Particionálás és partíciókulcsok Ez a videó ismerteti:

> [!VIDEO https://www.youtube.com/embed/SS6WrQ-HJ30]
> 

## <a name="partitioning-in-azure-cosmos-db"></a>Az Azure Cosmos DB particionálás
Az Azure Cosmos Adatbázisba tárolására és a séma nélküli adatok lekérdezése egy egyetlen számjegy ezredmásodperces késési bármilyen léptékben. Adattárolás nevű nyújt tárolók Azure Cosmos DB *gyűjtemények* (dokumentumok), a *diagramjait*, vagy *táblák*. 

Tárolók logikai erőforrások, és egy vagy több fizikai partíciók, sem kiszolgálók is kiterjedhetnek. A partíciók számának Azure Cosmos DB tároló mérete alapján, és egy tároló vagy egy tárolók kiosztott átviteli sebesség határozza meg. 

A *fizikai* partíciója fenntartott SSD-biztonsági tárolási változó mennyiségű számítási erőforrások (CPU és memória) együtt a rögzített méretű. Minden fizikai partíció replikálódik a magas rendelkezésre állás érdekében. Minden tárolók készlete egy vagy több fizikai partíció megoszthatja. Fizikai partíció felügyeleti teljes mértékben felügyelt Azure Cosmos DB, és nem kell összetett kód írása, és a partíciók kezeléséhez. Azure Cosmos DB tárolók olyan tárolási és átviteli korlátlan. 

A *logikai* belül fizikai javasoljuk, hogy az összes adatot tárolja egy egyetlen partíciókulcs-értékkel társított partíciót. Több logikai partíciót az ugyanazon fizikai partícióján fejezheti. Az alábbi ábra egyetlen tárolót három logikai partíciókkal rendelkezik. Minden logikai partíciót eltárolja a egy partíciókulcsot, LAX AMS és MEL kulcsattribútumokkal. Minden olyan LAX AMS és MEL logikai partíció nem lehet nagyobb legyen a maximális logikai partíciót 10 GB-os korlátját. 

![Erőforrás particionálás](./media/introduction/azure-cosmos-db-partitioning.png) 

Ha egy tároló megfelel a [Előfeltételek particionálás](#prerequisites), particionálás az teljes mértékben transzparens, az alkalmazáshoz. Azure Cosmos DB támogatja a gyors olvasást és írási műveleteket, lekérdezések, tranzakciós logikát, konzisztenciaszintek, és minden részletre kiterjedő hozzáférés-vezérlés keresztül módszerek/API-k egyetlen tároló-erőforrás. A szolgáltatás osztja el az adatok között fizikai és logikai partíciót és jobb oldali partícióra lekérdezési kérelmek útválasztás kezeli. 

## <a name="how-does-partitioning-work"></a>Particionálás működése

Particionálás működése Minden egyes elemnek rendelkeznie kell egy *partíciós kulcs* és egy *sorkulcsa*, amely egyedi azonosítása céljából. A partíciós kulcs úgy működik, mint az adatok logikai partíció, és a természetes határ Azure Cosmos DB biztosít osztja el az adatokat fizikai partíciók között. Az adatok logikai egypartíciós belül egyetlen fizikai partícióján kell lennie, de fizikai partíció felügyeleti Azure Cosmos DB kezeli. 

Röviden itt található particionálási hogyan működik az Azure Cosmos DB:

* Azure Cosmos DB tárolók készlete kiépítése **T** RU/mp (kérelmek / másodperc) átviteli sebesség.
* A háttérben Azure Cosmos DB kiépítését kiszolgálásához szükséges fizikai partíciók **T** kérések száma másodpercenként. Ha **T** magasabb, mint a maximális átviteli sebesség fizikai partíciónként **t**, majd az Azure Cosmos DB rendelkezések **N = T/t** fizikai partíciókat. Maximális átviteli sebesség / partition(t) értékének Azure Cosmos DB szerint van konfigurálva, ez az érték hozzá van rendelve, a teljes kiosztott átviteli sebesség és a használt hardverkonfiguráció alapján. 
* Azure Cosmos DB foglal le a kulcsfontosságú terület partíció kulcs kivonatok egyenlő vízszintes a **N** fizikai partíciókat. Igen, minden egyes fizikai partíció állomások **1/N** partícióazonosító kulcsértékei (logikai partíciót).
* Ha egy fizikai partíció **p** eléri a tárolási korlátját, Azure Cosmos DB zökkenőmentesen felosztja **p** két új fizikai partíciókra **p1** és **p2**. A kulcsok minden olyan fizikai új partíció nagyjából felének megfelelő értékeket továbbítja. Ez a művelet vágási láthatatlan, hogy az alkalmazást. Ha egy fizikai partíció eléri a tárolási korlátját, és az összes adat a fizikai partíción az azonos logikai partíciós kulcs tartozik, a felosztás művelet nem történik meg. Ennek az az oka az ugyanazon fizikai partícióján kell lennie, egyetlen logikai partíciókulcs tartozó összes adatot. Ebben az esetben egy másik partíció kulcs stratégia kell alkalmazni.
* Ha nagyobb átviteli sebesség kiépítése **t * N**, Azure Cosmos DB felosztja a nagyobb átviteli sebesség támogatásához a fizikai partíciók közül legalább egyet.

A partíciós kulcsok szemantikája némileg eltérő minden API szemantikáját megfelelően, az alábbi táblázatban látható módon:

| API | Partíciókulcs | Sorkulcs |
| --- | --- | --- |
| SQL | egyéni partíciós kulcs elérési útja | `id` kijavítva | 
| MongoDB | egyéni shard kulcs  | `_id` kijavítva | 
| Gremlin | egyéni partíció kulcstulajdonság | `id` kijavítva | 
| Tábla | `PartitionKey` kijavítva | `RowKey` kijavítva | 

Azure Cosmos-adatbázis használja a particionálás kivonat-alapú. Egy cikk írásakor Azure Cosmos DB csak a partíciós kulcs értékét, és a kivonatolt eredménye alapján határozza meg a mely partíció-elem tárolására. Azure Cosmos-adatbázis ugyanazon fizikai partícióján azonos partíciókulcsú minden elem tárolja. 

A partíciós kulcs választott egy fontos döntés, hogy módosítania kell a tervezés során nem. Válassza ki az egyik tulajdonságnév, amely számos különböző értékeket tartalmaz, és nem is memóriahozzáférési mintáitól. Ajánlott eljárás, számos különböző értékeket (például több száz vagy ezer) tartalmazó partíció kulcsa. Lehetővé teszi a számítási feladatok egyenletes szét ezeket az értékeket. Az ideális partíciós kulcs egy, a lekérdezéseket szűrőként gyakran megjelenik, és annak biztosítása érdekében, a megoldás méretezhető elegendő számossága.

Ha egy fizikai partíció eléri a tárolási korlátját, és az adatok a partíció rendelkezik ugyanazzal a partíciókulccsal, Azure Cosmos DB adja vissza a *"a partíciós kulcs elérte a maximális mérete 10 GB-os"* üzenetet, és a partíció nem osztott. Kiválasztása remek partíciókulcs-e egy nagyon fontos döntés. Fizikai partíciók egy Azure Cosmos DB belső fogalma és átmeneti. Az Azure Cosmos DB automatikusan átméretezi a számítási feladatok alapján fizikai partíciók száma. A fizikai partíciók száma alapján adatbázis-tervező nem szabad corelate helyette, ügyeljen arra, hogy válassza ki a megfelelő partíciókulcs (logikai partíciót). 

Válassza ki a partíciós kulcs úgy, hogy:

* A tároló terjesztési van, még akkor is a kulcsok között.
* A kötet terjesztési kérelmek adott időben van még a kulcsok között.
* Lekérdezések nagy feldolgozási által elindított hatékonyan továbbíthatók a partíciós kulcs belefoglalja a szűrőpredikátum.  
* A partíciós kulcs kiválasztása magasabb kulcsattribútumával használata általában ajánlott – mert, általában eredményez jobb terjesztési és méretezhetőséget. Például egy összetett kulcs alakítható szereplő számossága növeléséhez több tulajdonságok értékeit. 

Ha úgy dönt, hogy a fenti szempontok a partíciós kulcs, nem kell foglalkoznia a partíciók vagy mekkora átviteli lefoglalt fizikai partíciónként számát, Azure Cosmos DB méretezi fizikai partíciók számának és is méretezheti a az egyes partíciók szükség szerint.

Az Azure Cosmos DB tárolók hozhatók létre *rögzített* vagy *korlátlan* az Azure portálon. A rögzített méretű tárolók mérete legfeljebb 10 GB, feldolgozási sebessége legfeljebb 10000 RU/s lehet. Hozhat létre, korlátlan számú tárolót, adjon meg partíciókulcsot és 1000 RU/mp minimális átviteli sebességgel. Azure Cosmos DB tárolók átviteli tárolók, amelyben minden egyes tárolóban kell adja meg egy készlete közötti megosztásához is konfigurálható a partíció kulcsát, és korlátlan növelhető.

Célszerű ellenőrizni, hogy hogyan adatait a partíciók pontjain. A portál az adatok terjesztési ellenőrzéséhez nyissa meg a Azure Cosmos DB-fiókjába, majd kattintson a **metrikák** a **figyelés** szakaszban, majd kattintson a **tárolási** lap hogyan a adatok particionálása különböző fizikai partíciók között.

![Erőforrás particionálás](./media/partition-data/partitionkey-example.png)

A bal oldali a fenti kép rossz partíciókulcs eredményeket jeleníti meg, és a jobb oldali a fenti kép az eredmény látható, amikor remek partíciókulcs választott. A bal oldali képen látható, hogy az adatok van nem egyenlően elosztva a partíciók. Törekedni kell partíciókulcsot az adatok így néz a megfelelő kép ellátó kiválasztása.

<a name="prerequisites"></a>
## <a name="prerequisites-for-partitioning"></a>Particionálás előfeltételei

A fizikai partíciók automatikus-bontás **p1** és **p2** leírtak [hogyan működik a particionálási munkahelyi](#how-does-partitioning-work), a tároló léteznie kell legalább egy átviteli sebességgel 1000 RU/mp (vagy a megosztás átviteli tárolók csoportja között), ezért meg kell adni a partíciós kulcs. Ha (például egy gyűjteményt, egy grafikonon vagy tábla) tároló létrehozása az Azure portálon, válassza ki a **korlátlan** tárolási kapacitás beállítás korlátlan skálázás előnyeit. 

Ha egy tárolót az Azure portálon vagy programozottan létrehozott, és a kezdeti átviteli lett 1000 RU/mp vagy több partíció-kulcsot adott meg, kihasználhatja a korlátlan méretezést, a tároló nem történtek változások. Ez magában foglalja **rögzített** tárolók, amennyiben a kezdeti tároló legalább 1000 RU/mp átviteli hozták létre, és a partíciós kulcs van megadva.

Átviteli sebesség megosztani a tárolók blobkészletek részeként konfigurálva az összes tároló tekintendők **korlátlan** tárolók.

Ha létrehozott egy **rögzített** nem partíciós kulcs vagy átviteli kisebb, mint 1000 RU/mp-tárolóban, a tároló fog nem automatikus méretezése a cikkben leírtak szerint. A rögzített tároló egy korlátlan tárolóhoz (például egy legalább 1000 RU/mp és a partíciós kulcs) az adatok áttelepítéséhez kell használnia a [adatáttelepítési eszközét](import-data.md) vagy a [módosítás hírcsatorna könyvtár](change-feed.md). 

## <a name="partitioning-and-provisioned-throughput"></a>Particionálási és kiosztott átviteli sebesség
Kiszámítható teljesítmény készült Azure Cosmos-adatbázis. Amikor létrehoz egy tárolót vagy elosztott tárolókban, akkor lefoglalni a átviteli  *[kérelem egységek](request-units.md) (RU) másodpercenként*. Minden egyes kérelem lehetővé teszi egy RU kell fizetni, például a Processzor, memória és I/O művelethez rendszererőforrások mennyiségét arányos. Egy 1 KB-os dokumentum munkamenet-konzisztencia olvasási feldolgozó 1 RU. Egy olvasási 1 RU függetlenül tárolt elemek számát vagy egy időben futó egyidejű kérelmek számát jelenti. Nagyobb elemek szükség magasabb RUs méretétől függően. Ha ismeri az entitások és az alkalmazás támogatásához szükséges olvasások száma, az alkalmazás igényeinek szükséges átviteli pontos mennyisége oszthat meg. 

> [!NOTE]
> Egy tároló vagy egy tárolók kiosztott átviteli sebesség teljesen használatára, ki kell választania a partíciós kulcs, amely lehetővé teszi a kérelmek egyenletes szét összes különálló partíciókulcs-értékek.
> 
> 

<a name="designing-for-partitioning"></a>
## <a name="work-with-the-azure-cosmos-db-apis"></a>Az Azure Cosmos DB API-k használata
Az Azure portálon vagy az Azure CLI segítségével tárolók létrehozása, és bármikor skálázni őket. Ez a szakasz bemutatja, hogyan tárolók létrehozása, és adja meg az egyes API használatával kiosztott átviteli sebesség és a partíció kulcsot.


### <a name="sql-api"></a>SQL API
A következő példa bemutatja, hogyan hozhat létre az SQL API-val (gyűjtemény) tárolót. 

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

Egy elem (dokumentumok) segítségével elolvashatja a `GET` metódust a REST API vagy használatával `ReadDocumentAsync` az SDK-k egyikén.

```csharp
// Read document. Needs the partition key and the ID to be specified
DeviceReading document = await client.ReadDocumentAsync<DeviceReading>(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });
```

További információkért lásd: [Azure Cosmos adatbázis az SQL API-val a particionálás](sql-api-partition-data.md).

### <a name="mongodb-api"></a>MongoDB API
A MongoDB API-t a kedvenc eszköz, az illesztőprogram, vagy az SDK szilánkos gyűjtemény hozható létre. A jelen példában használjuk a Mongo rendszerhéj hozzon létre egy gyűjteményt.

A Mongo rendszerhéj:

```
db.runCommand( { shardCollection: "admin.people", key: { region: "hashed" } } )
```
    
Eredmény:

```JSON
{
    "_t" : "ShardCollectionResponse",
    "ok" : 1,
    "collectionsharded" : "admin.people"
}
```

### <a name="table-api"></a>Tábla API

A tábla API-t használó létrehozásához használja a `CreateIfNotExists` metódust. 

```csharp
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

CloudTable table = tableClient.GetTableReference("people");
table.CreateIfNotExists(throughput: 800);
```

Kiosztott átviteli sebesség be van állítva az egyik argumentumának `CreateIfNotExists`. A partíciós kulcs implicit módon jön létre a `PartitionKey` érték. 

Egyetlen entitás az alábbi kód segítségével kérheti le:

```csharp
// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = table.Execute(retrieveOperation);
```
További információkért lásd: [tábla API-val Develop](tutorial-develop-table-dotnet.md).

### <a name="gremlin-api"></a>Gremlin API

A Gremlin API-t használhatja az Azure portálon vagy az Azure parancssori felület hozhat létre egy grafikonon jelölő tárolót. Azt is megteheti mivel az Azure Cosmos DB több modellre, segítségével egy, a többi API-k létrehozása és a graph tároló méretezni.

A partíciókulcs és azonosító használatával Gremlin bármely csúcspont vagy peremhálózati olvashatók. Például a partíciós kulcs és a "Seattle" a sorkulcs ("USA) régió a diagram, található a csúcspont a következő szintaxis használatával:

```
g.V(['USA', 'Seattle'])
```

A partíciós kulcs és a sorkulcs él is hivatkozik.

```
g.E(['USA', 'I5'])
```

További információkért lásd: [particionált grafikon Azure Cosmos DB használatával](graph-partitioning.md).


<a name="designing-for-scale"></a>
## <a name="design-for-scale"></a>Skála kialakítása
Hatékony méretezést Azure Cosmos DB, a tároló létrehozásakor válassza ki a remek partíciókulcs kell. Nincsenek remek partíciókulcs kiválasztása két fő szempontjai:

* **Határ és a tranzakciók lekérdezése**. A választott partíciós kulcs használata a követelménnyel szemben tranzakciók több partíciós kulcsok méretezhető megoldás biztosításához az entitások szét kell terheléselosztást. Egy rendkívüli ugyanazzal a partíciókulccsal állíthatja be az összes elem, de ez a beállítás korlátozhatja a méretezhetőséget, a megoldás. Vagyis hozzárendelheti az egyes elemekhez tartozó egyedi partíciós kulcs. Ez a beállítás nem jól skálázható, de megakadályozza, hogy Ön dokumentumok közötti tranzakciókat tárolt eljárások és eseményindítók használatával. Az ideális partíciós kulcs lehetővé teszi lekérdezések hatékony használatát, és annak biztosítása érdekében, a megoldás méretezhető elegendő számossága. 
* **Nincs tárterületi és teljesítménybeli szűk keresztmetszetek**. Fontos, amely lehetővé teszi a különböző eltérő érték legyen elosztva írási műveletek tulajdonság kiválasztása. Ugyanazzal a partíciókulccsal kérelmek nem haladhatja meg a létesített átviteli sebesség partíció lefoglalva, és sebessége korlátozott lesz. Ezért fontos válassza ki a partíciós kulcs "interaktív területek" az alkalmazáson belül nem eredményez. Mert egyetlen partíciókulcs tartozó összes adatot a partíción belül kell tárolni, ne partíciós kulcsok, amelyek nagy mennyiségű adatot ugyanolyan értékéhez. 

Vizsgáljuk meg néhány valós forgatókönyv és jó partíciós kulcsok minden egyes:
* Ha a felhasználói profil backend megvalósításához a *Felhasználóazonosító* partíciós kulcsa a jó választás.
* Ha az IoT-adatok, például az eszköz állapotát, tárolja egy *Eszközazonosító* partíciós kulcsa a jó választás.
* Használata Azure Cosmos DB idősorozat adatok naplózásának a *állomásnév* vagy *folyamatazonosító* partíciós kulcsa a jó választás.
* Ha a több-bérlős architektúrák a *bérlői azonosító* partíciós kulcsa a jó választás.

Egyes esetekben, például az IoT- és felhasználói profilok használatához a partíciókulcs lehet ugyanaz, mint a *azonosító* (a dokumentum kulcs). Más esetekben az idősorozat adatokat, például lehetséges, hogy a partíciós kulcs, amely eltér a *azonosító*.

### <a name="partitioning-and-loggingtime-series-data"></a>A particionáló és naplózási/idősorozat adatok
A gyakori alkalmazási esetekben az Azure Cosmos Adatbázisba egyik naplózása és telemetriai adatokat. Fontos remek partíciókulcs kiválasztása ebben a forgatókönyvben, mert szükség lehet olvasási/írási hatalmas mennyiségű adatot. A partíciós kulcsok függ az olvasási és írási sebesség és a lekérdezések futtatásához várt típusú. Az alábbiakban néhány tipp az remek partíciókulcs kiválasztása:

* Ha a használati eset magában foglalja a kis mértékben írások, amelyek hosszú ideig keresztül gyűlik össze, és a kell lekérdezni az időbélyegek megadott további szűrőkkel, használja a Timestamp összesítését. Például egy jó megoldás, dátum partíciókulcsként használandó. Ezzel a megközelítéssel alapján is kereshet az adatok egy olyan partíciót adott dátum. 
* Ha a számítási feladatok írási műveleteket, amely ebben a forgatókönyvben gyakran előfordul, használja az időbélyeg nem alapuló partíciós kulcs. Azure Cosmos DB, terjesztése és írási műveletek egyenletesen méretezése különféle partíciók között. Itt egy *állomásnév*, *folyamatazonosító*, *Tevékenységazonosító*, vagy nagy számosságot egy másik tulajdonság akkor hasznos. 
* Egy másik megoldás, a hibrid megközelítést, ahol egy minden nap/hónap, a több tároló van, és a partíciós kulcs például részletesebb tulajdonság *állomásnév*. Ez a megközelítés azzal az előnyt, amely lehet különböző átviteli egyes tárolókban vagy a tárolókat az az időszak, és a méretezés és teljesítmény igényei alapján. Például az aktuális hónap tárolója előfordulhat, hogy el van látva egy nagyobb átviteli teljesítményt, mert szolgál, valamint az olvasási és írási műveletek. Előző hónap előfordulhat, hogy el van látva egy alacsonyabb teljesítményt, mert csak olvasási műveletek.

### <a name="partitioning-and-multitenancy"></a>Particionálás és több vállalat kiszolgálása
Ha egy több-bérlős-alkalmazást az Azure Cosmos DB készül olyanokat, vannak-e két népszerű terveket kell figyelembe venni: *bérlőnként egy partíciókulcsot* és *bérlőnként egy tároló*. Az alábbiakban és az egyes:

* **Bérlőnként egy partíciókulcsot**. Ebben a modellben bérlőkre közös elhelyezésű belül egyetlen tárolót. Lekérdezések és beszúrása egy-egy bérlő egyetlen partícióra Indexalapú. A bérlőhöz tartozó összes eleme között tranzakciós logikát is megvalósíthatja. Több bérlő megosztott tároló, mert úgy jobban használhatja a tárolás és a létesített átviteli sebesség készletezési erőforrásokat belül egyetlen tárolót minden bérlő, ahelyett hogy kiépítés az egyes bérlők számára. A hátránya, hogy nincs-e bérlőnként teljesítmény-elszigetelés érdekében. A teljesítmény növelése teljesítményének garantálása érdekében egy adott bérlő célzott növekszik és a bérlők a teljes tárolóhoz lesz érvényes.
* **Egy tároló bérlőnként**. Ebben a modellben minden bérlő rendelkezik saját tárolót, és bérlőnként garantált teljesítménnyel átviteli foglalhat. Ez a modell több költséghatékony, több-bérlős alkalmazásokhoz a néhány bérlőkkel.

Egy hibrid megközelítés, amely kis bérlők colocates együtt, és nagyobb bérlők számára saját tárolók elkülöníti is használható.

## <a name="next-steps"></a>További lépések
Ebben a cikkben azt előírt fogalmakat és ajánlott eljárások áttekintését méretezés és az Azure Cosmos Adatbázisba particionálást. 

* További tudnivalók [Azure Cosmos DB a létesített átviteli sebesség](request-units.md).
* További tudnivalók [az Azure Cosmos Adatbázisba globális terjesztési](distribute-data-globally.md).



