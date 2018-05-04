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
ms.date: 03/23/2018
ms.author: sngun
ms.openlocfilehash: 0a53bb0a23fae386abbe71de944b073cbb93d502
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
---
# <a name="set-and-get-throughput-for-azure-cosmos-db-containers"></a>Állítsa be, és átviteli Azure Cosmos DB tárolók beolvasása

Átviteli sebesség az az Azure Cosmos DB tárolókat az Azure portálon vagy az ügyfél SDK-k segítségével állíthatja be. 

A következő táblázat felsorolja a rendelkezésre álló tárolók az átviteli sebesség:

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p></p></td>
            <td valign="top"><p><strong>Az egypartíciós tároló</strong></p></td>
            <td valign="top"><p><strong>Particionált tároló</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>Minimális átviteli sebesség</p></td>
            <td valign="top"><p>400 kérelem egység / másodperc</p></td>
            <td valign="top"><p>1000 kérelem egység / másodperc</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Maximális átviteli sebesség</p></td>
            <td valign="top"><p>10 000 kérelemegység / másodperc</p></td>
            <td valign="top"><p>Korlátlan</p></td>
        </tr>
    </tbody>
</table>

## <a name="to-set-the-throughput-by-using-the-azure-portal"></a>Az átviteli sebesség beállítása az Azure-portál használatával

1. Egy új ablakban nyissa meg a [Azure-portálon](https://portal.azure.com).
2. A bal oldali sávon kattintson **Azure Cosmos DB**, vagy kattintson a **minden szolgáltatás** alsó, majd görgessen a **adatbázisok**, és kattintson a **Azure Cosmos DB**.
3. Válassza ki a Cosmos DB fiókját.
4. Kattintson az új ablakban **adatkezelő** a navigációs menü.
5. Az új ablakban bontsa ki az adatbázis és a tároló majd **skála & beállítások**.
6. Az új ablakban írja be az új átviteli értéket a **átviteli** gombra, majd **mentése**.

<a id="set-throughput-sdk"></a>

## <a name="to-set-the-throughput-by-using-the-sql-api-for-net"></a>Az átviteli sebesség beállítása a .NET-hez az SQL API használatával

A következő kódrészletet lekérdezi az aktuális átviteli, és nem módosítja azt 500 RU/mp. A teljes kódminta, tekintse meg a [CollectionManagement](https://github.com/Azure/azure-documentdb-dotnet/blob/95521ff51ade486bb899d6913880995beaff58ce/samples/code-samples/CollectionManagement/Program.cs#L188-L216) projekt a Githubon.

```csharp
// Fetch the offer of the collection whose throughput needs to be updated
Offer offer = client.CreateOfferQuery()
    .Where(r => r.ResourceLink == collection.SelfLink)    
    .AsEnumerable()
    .SingleOrDefault();

// Set the throughput to the new value, for example 500 request units per second
offer = new OfferV2(offer, 500);

// Now persist these changes to the collection by replacing the original offer resource
await client.ReplaceOfferAsync(offer);
```

<a id="set-throughput-java"></a>

## <a name="to-set-the-throughput-by-using-the-sql-api-for-java"></a>Az átviteli sebesség beállítása Java az SQL API használatával

A következő kódrészletet lekérdezi az aktuális átviteli, és nem módosítja azt 500 RU/mp. A teljes kódminta, tekintse meg a [OfferCrudSamples.java](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java) fájlt a Githubon. 

```Java
// find offer associated with this collection
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

Például a Mongo rendszerhéj hajtható végre a kérelem díjat ellenőrizni szeretné a műveletet.
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

Ennek tudatában, egy fenntartott átviteli sebességet, az alkalmazás által igényelt mennyisége becslése módja a kérelem egység kell fizetni társított tipikus műveleteket futtatott egy reprezentatív elem, amelyet az alkalmazás és majd becslése a hajtsa végre a másodpercenként várhatóan műveletek száma.

> [!NOTE]
> Ha méretét és az indexelt tulajdonságok száma jelentősen eltérő típusú elemekre, majd jegyezze fel a megfelelő műveletet kérelem egység kell fizetni társított minden egyes *típus* jellemző elem.
> 
> 

## <a name="get-throughput-by-using-mongodb-api-portal-metrics"></a>Átviteli sebesség MongoDB API portál mérőszámok segítségével könnyebben nyerhet

A legegyszerűbben úgy beszerezni a helyes becsült kérelem egység költségek a MongoDB API-adatbázis, hogy használja a [Azure-portálon](https://portal.azure.com) metrikákat. Az a *kérelem* és *kérelem kell fizetni* diagramokat, a kérelem egységek minden művelet nem használ-e, és hány kérelemegység használják ki egy másik viszonyítva becsült kaphat.

![MongoDB API portál metrikák][1]

### <a id="RequestRateTooLargeAPIforMongoDB"></a> Meghaladja a fenntartott átviteli sebességének korlátai a MongoDB API-ban.
Alkalmazások, amelyek mérete meghaladja a létesített átviteli sebesség a tároló lehet sebessége korlátozott felhasználási aránya a kiosztott átviteli sebesség alá süllyed. A sebesség korlátozása esetén a háttér megelőző jelleggel véget ér a kérelmet egy `16500` hibakód - `Too Many Requests`. Alapértelmezés szerint a MongoDB API automatikusan újrapróbálkozik legfeljebb 10-szer kell a visszatérésre egy `Too Many Requests` hibakód. Ha sok kap `Too Many Requests` hibakódok, érdemes lehet fontolja meg, vagy egy újrapróbálkozási logika a az alkalmazás hibakezelési rutinok vagy [növelje a kiosztott átviteli sebesség a tároló](set-throughput.md).

## <a name="throughput-faq"></a>Átviteli – gyakori kérdések

**I állíthatja az átviteli sebesség kisebb, mint 400 RU/mp?**

400 RU/mp a minimális átviteli sebesség érhető el a Cosmos DB egypartíciós tárolók (1000 RU/mp az particionált tárolókat minimális). Kérelem egységek 100 RU/mp intervallumok belül vannak beállítva, de az átviteli sebesség nem állítható be 100 RU/mp vagy bármely érték kisebb, mint a 400 RU/mp. Ha fejlesztéséhez és teszteléséhez Cosmos DB költséghatékony módszert keres, akkor használhatja az ingyenes [Azure Cosmos DB emulátor](local-emulator.md), amely központilag telepíthető helyileg ingyenesen. 

**Hogyan állíthatom be a MongoDB API-jával througput?**

Nincs átviteli sebesség beállításához MongoDB API kiterjesztés nélkül. A javaslat, hogy az SQL API-t használó, ahogy az [az átviteli sebesség beállítása a .NET-hez az SQL API használatával](#set-throughput-sdk).

## <a name="next-steps"></a>További lépések

Üzembe helyezési és folyamatos bolygónk-méretezéssel Cosmos DB, kapcsolatos további információkért lásd: [particionálás és méretezést Cosmos DB,](partition-data.md).

[1]: ./media/set-throughput/api-for-mongodb-metrics.png