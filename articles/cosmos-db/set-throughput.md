---
title: Az Azure Cosmos DB üzembe helyezése átviteli |} A Microsoft Docs
description: Ismerje meg, hogyan állíthatja be az Azure Cosmos DB containsers, gyűjtemények, diagramok és táblázatok a kiosztott átviteli sebesség.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 07/03/2018
ms.author: sngun
ms.openlocfilehash: 6d37ae9eb5aa5961c5da2e4cce0e79679f1e65ac
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/27/2018
ms.locfileid: "39283642"
---
# <a name="set-and-get-throughput-for-azure-cosmos-db-containers-and-database"></a>Állítsa be, és az Azure Cosmos DB-tárolók és az adatbázis átviteli sebesség lekérdezése

Beállíthat átviteli sebességet egy Azure Cosmos DB-tároló vagy egy tárolót az Azure portal használatával, vagy az ügyfél SDK-k használatával. 

**Tároló üzembe helyezése átviteli sebesség:** átviteli tárolók készletének üzembe helyezésekor, ezeket az összes tároló megosztás a kiosztott átviteli sebesség. Az egyes tárolókhoz kiépítési átviteli garantálja a meghatározott tároló átviteli sebesség fenntartása. A tároló szintjén RU/s hozzárendelésekor a tárolók hozható létre *rögzített* vagy *korlátlan*. A rögzített méretű tárolók mérete legfeljebb 10 GB, feldolgozási sebessége legfeljebb 10000 RU/s lehet. A korlátlan tároló létrehozásához meg kell adnia egy minimális 1000 RU/s átviteli sebességet és a egy [partíciókulcs](partition-data.md). Az adatok több partíción kell osztani, előfordulhat, hogy rendelkezik, szükség, válassza ki a partíciós kulcs, amely rendelkezik egy nagy számosságú (100 millió egyedi érték). Számos különböző értékeket a partíciókulcs kiválasztásával, győződjön meg arról, hogy a tároló/tábla/graph és a kérések skálázhatók egyenletesen Azure Cosmos DB által. 

**A tárolók vagy az adatbázis kiépítése átviteli:** kiépítési átviteli adatbázis lehetővé teszi, hogy az átviteli sebességet, ahhoz az adatbázishoz tartozó összes tároló között megosztani. Egy Azure Cosmos DB adatbázison belül rendelkezhet tárolók egy készletét, amely megosztja az átviteli sebesség, valamint a tárolók, amely rendelkezik a dedikált átviteli sebességet. RU/s tárolók több hozzárendelésekor a tárolók a készlethez tartozó számít *korlátlan* tárolók és a egy partíciókulcsot kell megadnia.

A kiosztott átviteli sebesség alapján, az Azure Cosmos DB foglal le a tároló(k) bérletét, és az elágazást/rebalances adatokat tárolni a több partícióra kiterjedő növekedésével, fizikai partíciók. Tároló és az adatbázis átviteli szintű üzembe helyezésének egymástól különálló ajánlatok, és mindkét közötti váltáskor szükséges áttelepítés forrás adatait a célhelyre. Ami azt jelenti, hogy hozzon létre egy új adatbázist vagy egy új gyűjteményt, és ezután telepítse át az adatok segítségével kell [tömeges végrehajtó könyvtár](bulk-executor-overview.md) vagy [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md). Az alábbi ábrán különböző szinteken kiépítési átviteli sebesség:

![Különálló tárolókat, és a tárolók beállított kiépítési kérelemegység](./media/request-units/provisioning_set_containers.png)

A következő szakaszokban megismerheti a Azure Cosmos DB-fiókot a különböző szinteken átviteli sebesség konfigurálásához szükséges lépéseket. 

## <a name="provision-throughput-by-using-azure-portal"></a>Kiépítés átviteli sebesség az Azure portal használatával

### <a name="provision-throughput-for-a-container-collectiongraphtable"></a>Átviteli sebesség üzembe egy tárolót (gyűjteményt/graph vagy tábla)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).  
2. A bal oldali navigációs panelen válassza ki a **összes erőforrás** , és keresse meg az Azure Cosmos DB-fiókot.  
3. Átviteli sebesség (gyűjtemény, gráf, tábla) tároló vagy egy meglévő tárolót a frissítés átviteli létrehozásakor konfigurálhatja.  
4. Tároló létrehozása közben az átviteli sebesség hozzárendeléséhez nyissa meg a **adatkezelő** panelhez, és válassza **új gyűjtemény** (új gráf, új tábla más API-k)  
5. Töltse ki az űrlap **gyűjtemény hozzáadása** panelen. Mezők ezen a panelen a következő táblázat ismerteti:  

   |**Beállítás**  |**Leírás**  |
   |---------|---------|
   |Adatbázis azonosítója  |  Adjon meg egy egyedi nevet az adatbázis azonosításához. Adatbázis, egy vagy több gyűjtemény logikai tárolói. Az adatbázis neve 1–255 karakter hosszúságú lehet, és nem tartalmazhat /, \\, #, ? karaktereket vagy záró szóközt. |
   |Katalógus azonosítója  | Adjon meg egy egyedi nevet a gyűjtemény azonosításához. A gyűjteményazonosítók nevére ugyanazok a karakterkorlátozások vonatkoznak, mint az adatbázisnevekre. |
   |Tárkapacitás   | Ez az érték az adatbázis tárkapacitása jelöli. Egy adott gyűjtemény átviteli kiépítésekor tárolókapacitás lehet **rögzített méretű (10 GB)** vagy **korlátlan**. Korlátlan tárolási kapacitás szükséges partíciókulcsot az adatok.  |
   |Teljesítmény   | Minden gyűjtemény és az adatbázis kérelemegység / s átviteli sebesség is lehet.  Rögzített tárolókapacitás minimum 400 kérelemegység / másodperc (RU/s), a korlátlan tárolási kapacitás, a minimális átviteli sebesség 1000 RU/s értéke.|

6. Után az alábbi mezők értékeket ad meg, jelölje be az **OK** a beállítások mentéséhez.  

   ![Egy gyűjtemény teljesítmény beállítása](./media/set-throughput/set-throughput-for-container.png)

7. Frissíti egy meglévő tároló átviteli sebesség, bontsa ki az adatbázis és a tárolót, és kattintson a **beállítások**. Az új ablakban írja be az új átviteli sebesség értéket, majd **mentése**.  

   ![Átviteli sebesség, a gyűjtemény frissítése](./media/set-throughput/update-throughput-for-container.png)

### <a name="provision-throughput-for-a-set-of-containers-or-at-the-database-level"></a>A tárolók vagy az adatbázis szintjén beállított kiépítése átviteli

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).  
2. A bal oldali navigációs panelen válassza ki a **összes erőforrás** , és keresse meg az Azure Cosmos DB-fiókot.  
3. Átviteli sebesség konfigurálhatja egy adatbázist vagy a frissítés átviteli sebesség a meglévő adatbázis létrehozása során.  
4. Egy adatbázis létrehozása közben az átviteli sebesség hozzárendeléséhez nyissa meg a **adatkezelő** panelhez, és válassza **új adatbázis**  
5. Töltse ki a **adatbázis-azonosító** értéke, ellenőrizze **kiépítése átviteli** lehetőséget, és az átviteli sebesség érték beállításához. Egy adatbázis üzembe lehet helyezni minimális átviteli sebesség értékét 50 000 RU/s.  

   ![Teljesítmény beállítása az új adatbázis-beállítás](./media/set-throughput/set-throughput-with-new-database-option.png)

6. Frissíti egy meglévő adatbázist az átviteli sebesség, bontsa ki az adatbázis és a tárolót, és kattintson a **méretezési**. Az új ablakban írja be az új átviteli sebesség értéket, majd **mentése**.  

   ![Átviteli sebesség adatbázis frissítése](./media/set-throughput/update-throughput-for-database.png)

### <a name="provision-throughput-for-a-set-of-containers-as-well-as-for-an-individual-container-in-a-database"></a>Több tárolókhoz is lehet egy adatbázisban tároló üzembe helyezése átviteli

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).  
2. A bal oldali navigációs panelen válassza ki a **összes erőforrás** , és keresse meg az Azure Cosmos DB-fiókot.  
3. Hozzon létre egy adatbázist, és rendelje hozzá átviteli sebességet. Nyissa meg a **adatkezelő** panelhez, és válassza **új adatbázis**  
4. Töltse ki a **adatbázis-azonosító** értéke, ellenőrizze **kiépítése átviteli** lehetőséget, és az átviteli sebesség érték beállításához. Egy adatbázis üzembe lehet helyezni minimális átviteli sebesség értékét 50 000 RU/s.  

   ![Teljesítmény beállítása az új adatbázis-beállítás](./media/set-throughput/set-throughput-with-new-database-option.png)

5. Ezután hozzon létre egy gyűjteményt, a fenti lépésben létrehozott adatbázison belül. A gyűjtemény létrehozásához kattintson a jobb gombbal az adatbázist, majd válassza **új gyűjtemény**.  

6. Az a **gyűjtemény hozzáadása** panelen adja meg a gyűjtemény nevét, és a partíciókulcs. Szükség esetén meg is oszthatnak ki átviteli kapacitásokat a meghatározott tároló Ha úgy dönt, hogy nem rendelhet értéket átviteli sebesség, a hozzárendelt az adatbázis átviteli van osztva a gyűjteményhez.  

   ![Igény szerint állítsa be az átviteli sebesség a tároló](./media/set-throughput/optionally-set-throughput-for-the-container.png)

## <a name="considerations-when-provisioning-throughput"></a>Átviteli sebesség üzembe helyezésekor szempontok

Az alábbiakban néhány szempontot, amelyek segítségével döntse el, az átviteli sebesség foglalás stratégiát.

### <a name="considerations-when-provisioning-throughput-at-the-database-level"></a>Az adatbázis szintjén átviteli üzembe helyezésekor szempontok

Vegye figyelembe, hogy kiépítése az adatbázis szintjén (az tárolók készletének) a következő esetekben átviteli sebesség:

* Ha egy tucatnyi vagy több számot tartalmazó sikerült megosztása átviteli néhányat vagy mindegyiket.  

* Ha egy egybérlős adatbázis, amely az Azure Cosmos DB az IaaS-ban üzemeltetett virtuális gépek vagy a helyszíni (például a nosql-alapú vagy a relációs adatbázisok) futtatásához, és több tároló-ről végez áttelepítést.  

* Ha azt szeretné, érdemes figyelembe venni a számítási feladatok nem tervezett kiugrások készletezett átviteli használatával az adatbázis szintjén.  

* Tároló beállítás átviteli sebességet, helyett érdekli az összesített átviteli sebesség lekérdezése illenek különböző tárolók az adatbázison belül.

### <a name="considerations-when-provisioning-throughput-at-the-container-level"></a>A tároló szintjén átviteli üzembe helyezésekor szempontok

Vegye figyelembe, hogy átviteli sebességét az alábbi esetekben tároló kiépítése:

* Ha rendelkezik Azure Cosmos DB-tárolók kevesebb.  

* Ha azt szeretné, garantált átviteli lekérni egy adott tárolón szavatolja.

## <a name="throughput-ranges"></a>Átviteli sebesség tartományok

A következő táblázat a tárolók számára elérhető adatátviteli mennyiség.

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p></p></td>
            <td valign="top"><p><strong>Egypartíciós tárolók</strong></p></td>
            <td valign="top"><p><strong>A particionált tároló</strong></p></td>
            <td valign="top"><p><strong>A tárolók beállítása</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>Minimális átviteli sebesség</p></td>
            <td valign="top"><p>400 kérelemegység / s</p></td>
            <td valign="top"><p>1000 kérelemegység / s</p></td>
            <td valign="top"><p>50 000 kérelemegység / s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Maximális átviteli sebesség</p></td>
            <td valign="top"><p>10 000 kérelemegység / s</p></td>
            <td valign="top"><p>Korlátlan</p></td>
            <td valign="top"><p>Korlátlan</p></td>
        </tr>
    </tbody>
</table>

<a id="set-throughput-sdk"></a>

## <a name="set-throughput-by-using-sql-api-for-net"></a>Teljesítmény beállítása a .NET-hez az SQL API használatával

### <a name="set-throughput-at-the-container-level"></a>A tároló szintjén teljesítmény beállítása
Itt láthat egy kódrészletet, 3000 kérelemegység / másodperc tároló az SQL API .NET SDK használatával – a tároló létrehozásához:

```csharp
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 3000 });
```

### <a name="set-throughput-for-a-set-of-containers-at-the-database-level"></a>Az adatbázis szintjén tárolók készletének átviteli sebesség beállítása

Íme egy Fragment kódu Pro kiépítési 100 000 kérelemegység / másodperc illenek különböző tárolók az SQL API .NET SDK használatával:

```csharp
// Provision 100,000 RU/sec at the database level. 
// sharedCollection1 and sharedCollection2 will share the 100,000 RU/sec from the parent database
// dedicatedCollection will have its own dedicated 4,000 RU/sec, independant of the 100,000 RU/sec provisioned from the parent database
Database database = client.CreateDatabaseAsync(new Database { Id = "myDb" }, new RequestOptions { OfferThroughput = 100000 }).Result;

DocumentCollection sharedCollection1 = new DocumentCollection();
sharedCollection1.Id = "sharedCollection1";
sharedCollection1.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(database.SelfLink, sharedCollection1, new RequestOptions())

DocumentCollection sharedCollection2 = new DocumentCollection();
sharedCollection2.Id = "sharedCollection2";
sharedCollection2.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(database.SelfLink, sharedCollection2, new RequestOptions())

DocumentCollection dedicatedCollection = new DocumentCollection();
dedicatedCollection.Id = "dedicatedCollection";
dedicatedCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(database.SelfLink, dedicatedCollection, new RequestOptions { OfferThroughput = 4000 )
```

Az Azure Cosmos DB egy foglalás modellt használ az átviteli sebesség működik. Azt jelenti, a számlázás mennyiségének átviteli *fenntartott*, függetlenül attól, hogy mekkora részét, hogy az átviteli aktívan *használt*. Az alkalmazás a terhelés, az adatok és a használati minták módosítása egyszerűen méretezhetők felfelé száma és fenntartott Kérelemegységek SDK-k vagy a használatával a [az Azure Portal](https://portal.azure.com).

Minden tárolót vagy tárolók, készletét le van képezve egy `Offer` erőforrás metaadatait, a kiosztott átviteli sebesség rendelkezik az Azure Cosmos DB-ben. Módosíthatja a kiosztott átviteli sebesség keresése a kapcsolódó ajánlat erőforrás egy tárolóhoz, majd frissíti, az új átviteli sebesség érték. Itt láthat egy kódrészletet, a tároló átviteli összesen 5 000 kérelemegység / második a .NET SDK használatával történő megváltoztatása. Miután megváltoztatta az átviteli sebességet, frissítenie kell minden meglévő Azure portál windows jelenik meg a módosított átviteli sebességet. 

```csharp
// Fetch the resource to be updated
// For a updating throughput for a set of containers, replace the collection's self link with the database's self link
Offer offer = client.CreateOfferQuery()
                .Where(r => r.ResourceLink == collection.SelfLink)    
                .AsEnumerable()
                .SingleOrDefault();

// Set the throughput to 5000 request units per second
offer = new OfferV2(offer, 5000);

// Now persist these changes to the database by replacing the original resource
await client.ReplaceOfferAsync(offer);
```

Ez nincs hatással a tároló rendelkezésre állását, vagy tárolók, amikor módosítja az átviteli sebességet. Az új szolgáltatás számára fenntartott átviteli sebesség általában hatékony az alkalmazás az új átviteli másodpercen belül.

<a id="set-throughput-java"></a>

## <a name="to-set-the-throughput-by-using-the-sql-api-for-java"></a>Az átviteli sebesség beállítása a Javához készült az SQL API használatával

A következő kódrészlet lekérdezi az aktuális átviteli sebességet, és módosítja azt 500 RU/s. Egy teljes körű kódmintát talál a [OfferCrudSamples.java](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java) fájlt a Githubon. 

```Java
// find offer associated with this collection
// To change the throughput for a set of containers, use the database's resource id instead of the collection's resource id
Iterator < Offer > it = client.queryOffers(
    String.format("SELECT * FROM r where r.offerResourceId = '%s'", collectionResourceId), null).getQueryIterator();
assertThat(it.hasNext(), equalTo(true));

Offer offer = it.next();
assertThat(offer.getString("offerResourceId"), equalTo(collectionResourceId));
assertThat(offer.getContent().getInt("offerThroughput"), equalTo(throughput));

// update the offer
int newThroughput = 500;
offer.getContent().put("offerThroughput", newThroughput);
client.replaceOffer(offer);
```

## <a id="GetLastRequestStatistics"></a>Átviteli sebesség lekérdezése a MongoDB API-k GetLastRequestStatistics parancs használatával

A MongoDB API támogatja az egyéni parancsokat, *getLastRequestStatistics*, a kérelem díjak egy adott művelethez beolvasásakor.

A Mongo shell például hajtsa végre a műveletet, ellenőrizze a kérelem díja kívánt.
```
> db.sample.find()
```

Ezután hajtsa végre a parancsot *getLastRequestStatistics*.
```
> db.runCommand({getLastRequestStatistics: 1})
{
    "_t": "GetRequestStatisticsResponse",
    "ok": 1,
    "CommandName": "OP_QUERY",
    "RequestCharge": 2.48,
    "RequestDurationInMilliSeconds" : 4.0048
}
```

Egy fenntartott adattovábbítási kapacitással, az alkalmazás számára szükséges mennyiségű becslése módja jellemző műveleteket futtat egy reprezentatív elem az alkalmazása által használt társított kérelem egységek használata után jegyezze fel, és ezután becsülni az a másodpercenként végrehajtásához várhatóan műveleteket.

> [!NOTE]
> Ha a konfigurációelem-típusok tekintetében méretét és az indexelt tulajdonságok jelentősen eltérő rendelkezik, majd jegyezze fel a megfelelő művelet kérelem egységek használata után az egyes társított *típus* tipikus elem.
> 
> 

## <a name="get-throughput-by-using-mongodb-api-portal-metrics"></a>Átviteli sebesség lekérdezése a MongoDB API-portál metrikák használatával

Beolvasni egy jó becslés kérelem egységekre vonatkozó díjakon MongoDB API-val adatbázis használata esetén a legegyszerűbb módja a [az Azure portal](https://portal.azure.com) metrikákat. Az a *kérelmek száma* és *kérelem díj* diagramok, minden művelet is használja, és hány kérelemegység igényelnek egymáshoz viszonyított hány kérelemegység becsült kérheti.

![MongoDB API-portál mérőszámai][1]

### <a id="RequestRateTooLargeAPIforMongoDB"></a> A MongoDB API-ban mért fenntartott adatátviteli kapacitást meghaladó
Az alkalmazásokat, amelyek meghaladják a kiosztott átviteli sebesség egy tárolót vagy tárolók sebessége korlátozott lesz, amíg a fogyasztás sebessége a kiosztott átviteli sebesség alá csökken. A sebesség korlátozása esetén a háttér megszűnik a kérelem egy `16500` hibakód - `Too Many Requests`. Alapértelmezés szerint a MongoDB API-val automatikusan újrapróbálkozik legfeljebb 10 alkalommal visszaküldése előtt egy `Too Many Requests` hibakód. Ha azért küldtük Önnek, számos `Too Many Requests` hibakódok, érdemes figyelembe venni, vagy adja hozzá egy újrapróbálkozási logikát az alkalmazás hibakezelési rutinok vagy [tároló kiosztott átviteli sebesség növelése](set-throughput.md).

## <a name="throughput-faq"></a>Átviteli sebesség – gyakori kérdések

**Állítható kisebb, mint 400 RU/s, az átviteli sebesség?**

400 Kérelemegység/s a minimális átviteli sebesség érhető el a Cosmos DB-egypartíciós tárolók (1000 RU/s a particionált tárolók minimális). Kérelem egységeket 100 RU/s időközzel vannak beállítva, de az átviteli sebesség nelze nastavit nA 100 RU/s, vagy bármilyen érték kisebb, mint 400 RU/s. Ha egy költséghatékony fejlesztése és tesztelése a Cosmos DB módszert keres, akkor használhatja az ingyenes [Azure Cosmos DB Emulatort](local-emulator.md), amelyeket helyben is üzembe költségek nélkül. 

**Hogyan állíthatok be átviteli sebesség, a MongoDB API használatával?**

Nincs átviteli sebesség beállítása a MongoDB API kiterjesztés nélkül. Az ajánlás az, hogy az SQL API-val, ahogyan az [az átviteli sebesség beállítása a .NET-hez az SQL API használatával](#set-throughput-sdk).

## <a name="next-steps"></a>További lépések

* Teljesítmény- és kérésegységek becslése kapcsolatos további információkért lásd: [és kapacitása becsléséhez az Azure Cosmos DB kérése](request-units.md)

* Kiépítés és folyamatos globális Cosmos DB-vel kapcsolatos további tudnivalókért lásd: [particionálás és skálázás az, Cosmos DB](partition-data.md).

[1]: ./media/set-throughput/api-for-mongodb-metrics.png
