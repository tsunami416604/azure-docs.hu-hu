---
title: Az Azure Cosmos DB rendelkezés átviteli |} Microsoft Docs
description: Tudnivalók az Azure Cosmos DB containsers, gyűjtemények, diagramokat és táblák kiosztott átviteli sebesség.
services: cosmos-db
author: SnehaGunda
manager: kfile
documentationcenter: ''
ms.assetid: f98def7f-f012-4592-be03-f6fa185e1b1e
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: sngun
ms.openlocfilehash: fadbe5d2777bc5c8551558be80e77dd2785044a2
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="set-and-get-throughput-for-azure-cosmos-db-containers-and-database"></a>Állítsa be, és átviteli lekérése Azure Cosmos DB-tárolók és adatbázis

Az Azure portál használatával vagy az ügyfél SDK-k segítségével állíthatja be átviteli egy Cosmos-DB Azure-tárolót vagy a tárolókat. Ha átviteli állítja be a tárolók, összes blobhoz ossza meg a létesített átviteli sebesség. Az egyes tárolók üzembe helyezési teljesítményt garantálja a Foglalás átviteli adott tároló. Másrészt adatbázis átviteli kiépítés megosztását teszi az átviteli sebesség ahhoz az adatbázishoz tartozó összes tároló között. Azure Cosmos DB adatbázisban akkor olyan tárolók, amelyek megosztása a teljesítmény, valamint a tárolók, amelyek dedikált átviteli készlete. 

A létesített átviteli sebesség alapján, Azure Cosmos DB foglal le a tárolója és elágazást/rebalances adatokat tároló partíciók között, akkor növekedésével fizikai partíciókat.

Az egyes tároló szintjén RU/mp hozzárendelésekor a tárolók hozhatók létre *rögzített* vagy *korlátlan*. A rögzített méretű tárolók mérete legfeljebb 10 GB, feldolgozási sebessége legfeljebb 10000 RU/s lehet. Hozzon létre egy korlátlan számú tárolót, meg kell adnia egy minimális átviteli sebességgel 1000 RU/mp és egy [partíciókulcs](partition-data.md). Az adatok kell kell-e osztani több partíciót, szükség egy partíciós kulcs, amely rendelkezik egy nagy számosságot (több millió különböző értékeket 100) kiválasztásához. A partíciós kulcs számos különböző értékekkel kiválasztásával, győződjön meg arról, hogy a tároló/tábla/graph és a kérelmek is méretezhető egységesen Azure Cosmos DB. 

RU/mp tárolók egy készlete közötti hozzárendelésekor a tárolók ebbe a csoportba tartozó tekintendők *korlátlan* tárolók és a partíciókulcs kell megadnia.

![Az egyes tárolók és a tárolók beállított kérelemegység kiépítése](./media/request-units/provisioning_set_containers.png)

Ez a cikk végigvezeti az Azure Cosmos DB fiók különböző szinteken átviteli sebesség konfigurálásához szükséges lépéseket. 

## <a name="provision-throughput-by-using-azure-portal"></a>Kiépítés átviteli az Azure portál használatával

### <a name="provision-throughput-for-a-container-collectiongraphtable"></a>Kiépítés átviteli sebesség a tárolóhoz (gyűjtemény vagy graph vagy tábla)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).  
2. Válassza ki a bal oldali navigációs **összes erőforrás** és Azure Cosmos DB fiókja található.  
3. Konfigurálhatja az átviteli sebesség a tároló (gyűjtemény, graph, tábla) vagy a frissítés átviteli egy meglévő tároló létrehozása során.  
4. Egy tároló létrehozása közben az átviteli sebesség hozzárendeléséhez nyissa meg a **adatkezelő** panelhez, és válassza **új gyűjtemény** (új diagram, új tábla más API-k)  
5. Töltse ki az űrlap **gyűjtemény hozzáadása** panelen. A következő táblázat ismerteti a mezők ezen a panelen:  

   |**Beállítás**  |**Leírás**  |
   |---------|---------|
   |Adatbázis azonosítója  |  Adjon meg egy egyedi nevet az adatbázis azonosításához. Adatbázis egy olyan logikai tároló az egy vagy több gyűjteményt. Adatbázis nevének 1 és 255 karakternél rövidebb kell tartalmaznia, és nem tartalmazhatnak, \, #,?, vagy záró szóköz. |
   |Katalógus azonosítója  | Adjon egyedi nevet a gyűjtemény azonosításához. A gyűjteményazonosítók nevére ugyanazok a karakterkorlátozások vonatkoznak, mint az adatbázisnevekre. |
   |Tárkapacitás   | Ez az érték azt jelenti, hogy a tárolási kapacitást az adatbázis. Egy adott gyűjtemény átviteli létesítésekor tárolási kapacitás lehet **rögzített (10 GB-os)** vagy **korlátlan**. Korlátlan tárolási kapacitás meg kell adnia egy partíciókulcsot az adatok számára.  |
   |Teljesítmény   | Minden gyűjtemény és az adatbázis is lehet átviteli kérelem egység / másodperc.  Rögzített tárolási kapacitás iránti minimális átviteli sebesség 400 kérelemegység (RU/mp) másodpercenként, korlátlan tárolási kapacitás, a minimális átviteli 1000 RU/mp értékre van állítva.|

6. Ezeket a mezőket a beírt értékeket, válassza ki **OK** menti a beállításokat.  

   ![Egy gyűjtemény készlet kapacitása](./media/set-throughput/set-throughput-for-container.png)

7. Meglévő tároló adatátviteli sebességét frissítéséhez bontsa ki az adatbázis és a tároló, és kattintson a **beállítások**. Az új ablakban írja be az új átviteli értéket, és válassza ki **mentése**.  

   ![Átviteli sebesség egy gyűjtemény frissítéséhez](./media/set-throughput/update-throughput-for-container.png)

### <a name="provision-throughput-for-a-set-of-containers-or-at-the-database-level"></a>Kiépítés átviteli tárolók vagy az adatbázis szintjén készletének

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).  
2. Válassza ki a bal oldali navigációs **összes erőforrás** és Azure Cosmos DB fiókja található.  
3. Átviteli konfigurálhatja egy adatbázis vagy a frissítés átviteli sebesség a meglévő adatbázis létrehozása során.  
4. Egy adatbázis létrehozása közben az átviteli sebesség hozzárendeléséhez nyissa meg a **adatkezelő** panelhez, és válassza **új adatbázis**  
5. Töltse ki a **adatbázis-azonosító** értéke, ellenőrizze **rendelkezés átviteli** lehetőséget, és átviteli érték beállításához. Egy adatbázis 50 000 minimális átviteli értékű létesíthetők RU/mp.  

   ![Állítsa be az új adatbázis-beállítás átviteli sebesség](./media/set-throughput/set-throughput-with-new-database-option.png)

6. A meglévő adatbázis átviteli frissítéséhez bontsa ki az adatbázis és a tároló, és kattintson a **méretezési**. Az új ablakban írja be az új átviteli értéket, és válassza ki **mentése**.  

   ![Teljesítmény-adatbázis frissítése](./media/set-throughput/update-throughput-for-database.png)

### <a name="provision-throughput-for-a-set-of-containers-as-well-as-for-an-individual-container-in-a-database"></a>Kiépítés átviteli állítja be a tárolókat is beleértve egy egyéni adatbázis-tárolót forrásadatkockának

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).  
2. Válassza ki a bal oldali navigációs **összes erőforrás** és Azure Cosmos DB fiókja található.  
3. Hozzon létre egy adatbázist, és rendelje hozzá átviteli sebességet. Nyissa meg a **adatkezelő** panelhez, és válassza **új adatbázis**  
4. Töltse ki a **adatbázis-azonosító** értéke, ellenőrizze **rendelkezés átviteli** lehetőséget, és átviteli érték beállításához. Egy adatbázis 50 000 minimális átviteli értékű létesíthetők RU/mp.  

   ![Állítsa be az új adatbázis-beállítás átviteli sebesség](./media/set-throughput/set-throughput-with-new-database-option.png)

5. Ezután hozzon létre egy gyűjteményt a fenti lépés a létrehozott adatbázis belül. A gyűjtemény létrehozásához kattintson a jobb gombbal az adatbázist, és válassza ki a **új gyűjtemény**.  

6. Az a **gyűjtemény hozzáadása** panelen adja meg a gyűjtemény nevét, és kulcs partícióazonosító. Ha szükséges Ha úgy dönt, hogy nem átviteli értéket megadhat adott tároló átviteli, az átviteli sebesség a adatbázis rendelt meg van osztva a gyűjteményhez.  

   ![Igény szerint állítsa be az átviteli sebesség a tároló](./media/set-throughput/optionally-set-throughput-for-the-container.png)

## <a name="considerations-when-provisioning-throughput"></a>Átviteli kialakítási szempontjai

Az alábbiakban néhány megfontolások, amelyek segítenek döntse el, átviteli foglalás stratégia.

Vegye figyelembe a kiépítés átviteli szinten adatbázis (a tárolók készlete) a következő esetekben:

* Ha sikerült megosztani átviteli néhányat vagy mindegyiket tárolókat egy tucat vagy több száma.  

* Amikor telepít egy egyetlen-bérlő adatbázisból, amelyek célja, hogy futtatható a Azure Cosmos DB IaaS-kiszolgálón futó virtuális gépek vagy a helyszíni (a példában, a nosql-alapú vagy a relációs adatbázisok), és sok tárolók rendelkezik.  

* Ha azt szeretné, figyelembe kell venni a nem tervezett teljesítményt a munkaterhelések készletezett átviteli használatával az adatbázis szintjén.  

* Ön helyett egy egyedi tároló beállítás átviteli sebességet, való előkészítésével a teljes átviteli sebesség az adatbázis tárolókra csoportja között.

Vegye figyelembe a kiépítés átviteli, egy egyéni tárolót a következő esetekben:

* Ha kevesebb Azure Cosmos DB a tárolók száma.  

* Ha azt szeretné, és kérjen a garantált átviteli sebesség a szolgáltatásiszint-szerződés által támogatott adott tárolóban.

## <a name="throughput-ranges"></a>Átviteli sebesség tartományok

A következő táblázat felsorolja a rendelkezésre álló tárolók az átviteli sebesség:

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p></p></td>
            <td valign="top"><p><strong>Az egypartíciós tároló</strong></p></td>
            <td valign="top"><p><strong>Particionált tároló</strong></p></td>
            <td valign="top"><p><strong>Tárolók készlete</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>Minimális átviteli sebesség</p></td>
            <td valign="top"><p>400 kérelem egység / másodperc</p></td>
            <td valign="top"><p>1 000 kérelemegység / másodperc</p></td>
            <td valign="top"><p>50 000 kérelemegység / másodperc</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Maximális átviteli sebesség</p></td>
            <td valign="top"><p>10 000 kérelemegység / másodperc</p></td>
            <td valign="top"><p>Korlátlan</p></td>
            <td valign="top"><p>Korlátlan</p></td>
        </tr>
    </tbody>
</table>

<a id="set-throughput-sdk"></a>

## <a name="set-throughput-by-using-sql-api-for-net"></a>Állítsa be az átviteli sebesség SQL API használatával a .NET-hez

Íme egy tároló 3000 kérelem egység / másodperc az egyes tároló az SQL API .NET SDK használatával való létrehozásának egy kódrészletet:

```csharp
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 3000 });
```

Íme egy kódrészletet az üzembe helyezési 100 000 egység / másodperc között az SQL API .NET SDK használatával tárolók készlete kérelem:

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

Azure Cosmos DB átviteli foglalás modellt működik. Ez azt jelenti, hogy kell fizetni az átviteli sebesség *fenntartott*, függetlenül attól, hogy átviteli mekkora aktívan *használt*. Az alkalmazás által könnyen méretezheti száma fel és le terhelés, az adatok és a használati minták módosítása fenntartott RUs SDK-k, vagy használja a [Azure Portal](https://portal.azure.com).

Minden egyes tároló vagy elosztott tárolókban, van rendelve egy `Offer` Azure Cosmos DB, amelynek metaadatait a létesített átviteli sebesség erőforrás. A megfelelő ajánlat erőforrás egy tároló keresése, akkor új átviteli értékű frissítése módosíthatja a kiosztott átviteli sebesség. Íme egy kódrészletet a az átviteli sebesség a tároló módosítása a 5 000 kérelemegység / második .NET SDK használatával:

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

Ha megváltoztatja az átviteli sebesség, nincs hatással a tároló rendelkezésre állását, vagy a tárolók, készletét. Az új fenntartott átviteli sebességet általában hatékony alkalmazásra, az új átviteli másodpercen belül.

<a id="set-throughput-java"></a>

## <a name="to-set-the-throughput-by-using-the-sql-api-for-java"></a>Az átviteli sebesség beállítása Java az SQL API használatával

A következő kódrészletet lekérdezi az aktuális átviteli, és nem módosítja azt 500 RU/mp. A teljes kódminta, tekintse meg a [OfferCrudSamples.java](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java) fájlt a Githubon. 

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

## <a id="GetLastRequestStatistics"></a>Átviteli sebesség MongoDB API GetLastRequestStatistics parancs segítségével könnyebben nyerhet

A MongoDB API támogatja egy egyéni parancs *getLastRequestStatistics*, az a kérelem díjak egy adott művelethez.

Például a Mongo rendszerhéj hajtható végre a kérelem kell fizetni az ellenőrizni kívánt műveletet.
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

Egy fenntartott átviteli sebességet, az alkalmazás által igényelt mennyisége becslése módja a kérelem egység kell fizetni társított tipikus műveleteket futtatott egy reprezentatív elem, amelyet az alkalmazás és majd a számának becslése Műveletek, amelyek várhatóan másodpercenként végrehajtásához.

> [!NOTE]
> Ha méretét és az indexelt tulajdonságok száma jelentősen eltérő típusú elemekre, majd jegyezze fel a megfelelő műveletet kérelem egység kell fizetni társított minden egyes *típus* jellemző elem.
> 
> 

## <a name="get-throughput-by-using-mongodb-api-portal-metrics"></a>Átviteli sebesség MongoDB API portál mérőszámok segítségével könnyebben nyerhet

A legegyszerűbben úgy beszerezni a helyes becsült kérelem egység költségek a MongoDB API-adatbázis, hogy használja a [Azure-portálon](https://portal.azure.com) metrikákat. Az a *kérelem* és *kérelem kell fizetni* diagramokat, a kérelem egységek minden művelet nem használ-e, és hány kérelemegység használják ki egy másik viszonyítva becsült kaphat.

![MongoDB API portál metrikák][1]

### <a id="RequestRateTooLargeAPIforMongoDB"></a> Meghaladja a fenntartott átviteli sebességének korlátai a MongoDB API-ban.
Alkalmazások, amelyek mérete meghaladja a kiosztott átviteli sebesség egy tároló vagy egy tárolók sebessége korlátozott lesz, amíg a használat gyakorisága a kiosztott átviteli sebesség alá süllyed. A sebesség korlátozása esetén a háttér véget ér a kérelmet egy `16500` hibakód - `Too Many Requests`. Alapértelmezés szerint a MongoDB API automatikusan újrapróbálkozik legfeljebb 10-szer kell a visszatérésre egy `Too Many Requests` hibakód. Ha sok kap `Too Many Requests` hibakódok, érdemes lehet fontolja meg, vagy egy újrapróbálkozási logika a az alkalmazás hibakezelési rutinok vagy [növelje a kiosztott átviteli sebesség a tároló](set-throughput.md).

## <a name="throughput-faq"></a>Átviteli – gyakori kérdések

**I állíthatja az átviteli sebesség kisebb, mint 400 RU/mp?**

400 RU/mp a minimális átviteli sebesség érhető el a Cosmos DB egypartíciós tárolók (1000 RU/mp az particionált tárolókat minimális). Kérelem egységek 100 RU/mp intervallumok belül vannak beállítva, de az átviteli sebesség nem állítható be 100 RU/mp vagy bármely érték kisebb, mint a 400 RU/mp. Ha fejlesztéséhez és teszteléséhez Cosmos DB költséghatékony módszert keres, akkor használhatja az ingyenes [Azure Cosmos DB emulátor](local-emulator.md), amely központilag telepíthető helyileg ingyenesen. 

**Hogyan állíthatom be a MongoDB API-jával átviteli?**

Nincs átviteli sebesség beállításához MongoDB API kiterjesztés nélkül. A javaslat, hogy az SQL API-t használó, ahogy az [az átviteli sebesség beállítása a .NET-hez az SQL API használatával](#set-throughput-sdk).

## <a name="next-steps"></a>További lépések

* Átviteli sebesség és a kérelem egységek megbecsülheti, lásd: [egységek és az Azure Cosmos Adatbázisba becslése átviteli kérelem](request-units.md)

* Üzembe helyezési és folyamatos bolygónk-méretezéssel Cosmos DB, kapcsolatos további információkért lásd: [particionálás és méretezést Cosmos DB,](partition-data.md).

[1]: ./media/set-throughput/api-for-mongodb-metrics.png