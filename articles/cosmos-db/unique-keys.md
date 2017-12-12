---
title: "Az Azure Cosmos Adatbázisba egyedi kulcsok |} Microsoft Docs"
description: "Megtudhatja, hogyan egyedi kulcsok használata az Azure Cosmos DB adatbázisban."
services: cosmos-db
keywords: "egyedi kulcs korlátozás, egyedi kulcsmegkötés megsértése"
author: rafats
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: b15d5041-22dd-491e-a8d5-a3d18fa6517d
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/27/2017
ms.author: rafats
ms.openlocfilehash: 85157f3f0bcf86ae049c0bec76afb0ca33797b11
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="unique-keys-in-azure-cosmos-db"></a>Egyedi kulcsok vannak az Azure Cosmos-Adatbázisba

Egyedi kulcsok vannak elhelyezve a fejlesztők a adatintegritást réteg hozzáadása az adatbázishoz. Hozzon létre egy egyedi kulcs házirend, a tároló létrehozásakor, egy vagy több érték / egyediségének biztosítása [partíciókulcs](partition-data.md). Egy tároló létrehozása után a egyedi kulcs házirendnek, megakadályozza, hogy az ismétlődő értékek, az egyedi kulcs korlátozás által megadott értéket tartalmazó új vagy frissített elemek létrehozását.   

> [!NOTE]
> A legújabb verziói által támogatott egyedi kulcsok vannak a [.NET](documentdb-sdk-dotnet.md) és [.NET Core](documentdb-sdk-dotnet-core.md) SQL SDK-k, és a [MongoDB API](mongodb-feature-support.md#unique-indexes). A tábla API és a Graph API nem támogatja egyedi kulcsok vannak jelenleg. 
> 
>

## <a name="use-case"></a>Használati eset

Tegyük fel, vizsgáljuk meg egy felhasználói adatbázis társított hogyan egy [közösségi alkalmazás](use-cases.md#web-and-mobile-applications) egy egyedi kulcs házirend gyakorló e-mail címeket is hasznosak. Azáltal, hogy a felhasználó e-mail címe egyedi kulcs, akkor győződjön meg arról, minden egyes rekord tartalmazza-e egy egyedi e-mail címet, és nem új rekord duplikált e-mail címmel rendelkező hozható létre. 

Ha azt szeretné, hogy létre tudja hozni a felhasználók adott több bejegyzés azonos e-mail címet, de nem a azonos Utónév, Vezetéknév és az e-mail cím, más elérési utak hozzáadhatja az egyedi kulcs házirend. Így egyszerűen egy e-mail cím alapján egyedi kulcs létrehozása helyett létrehozhat egyedi kulcs, amely a utónevét, vezetéknevét és e-mail kombinációja. Ebben az esetben egyes három módjának egyedi kombinációja engedélyezve van, ezért az adatbázis elemek, amelyekhez a következő elérési út értékek tartalmazhatnak. Ezeket a rekordokat mindegyikének volna adja át a egyedi kulcs házirend.  

**Engedélyezett értékek az Utónév, a Vezetéknév és az e-mailek egyedi kulcsa**

|Utónév|Vezetéknév|E-mail-cím|
|---|---|---|
|Gaby|Duperre|gaby@contoso.com |
|Gaby|Duperre|gaby@fabrikam.com|
|Ivan|Duperre|gaby@fabrikam.com|
|    |Duperre|gaby@fabrikam.com|
|    |       |gaby@fabraikam.com|

Ha próbált beszúrni egy másik rekord, amelynek az a fenti táblázatban kombinációk, akkor hibaüzenet jelzi, hogy az egyedi megkötés nem teljesített. Hiba a következő Azure Cosmos DB adja vissza: "Erőforrás a megadott azonosító vagy név már létezik." vagy "Erőforrás a megadott azonosító, név vagy egyedi index már létezik." 

## <a name="using-unique-keys"></a>Egyedi kulcsok használata

Egyedi kulcsot kell meghatározni, ha a tároló jön létre, és egyedi kulcsa a partíciós kulcs hatókörét. A korábbi példában létrehozásához, ha Ön partícióazonosító alapján irányítószám, a tábla duplikált mindegyik partíció a rekord lehet.

Meglévő tárolók egyedi kulcsok használata nem lehet frissíteni.

A tároló egy egyedi kulcs házirend létrehozása után a házirend nem módosítható, kivéve, ha a tároló hozza létre újra. Ha szeretné valósítsa meg az egyedi kulcsok vannak a meglévő adatok, az új tároló létrehozásához, és a megfelelő adatok áttelepítési eszköz használatával helyezze át az adatokat az új tárolóhoz. Az SQL-tárolókat, használja a [adatáttelepítési eszköz](import-data.md). A MongoDB-tárolók, használjon [mongoimport.exe vagy mongorestore.exe](mongodb-migrate.md).

Minden egyedi kulcs legfeljebb 16 elérési értéket (például /firstName, /lastName, /address/zipCode stb.) tartalmazhat. 

Minden egyedi kulcs házirend legfeljebb 10 egyedi kulcsra vonatkozó megkötések vagy kombinációja lehet. Így a korábbi példában használó Utónév, vezetéknevét, és az e-mail cím csak egyetlen megkötés, és három elérhető 16 lehetséges elérési utak használ. 

A kérelem egység díja létrehozásához, frissítéséhez és valamivel nagyobb elem törlése lesz, ha egy egyedi kulcs házirendet a tárolón. 

Ritka egyedi kulcsok használata nem támogatott. Ha néhány egyedi elérési utak értékei hiányoznak, azok tekintendők különleges null értékű, amely szerepel az egyediségre vonatkozó feltételnek.

## <a name="sql-api-sample"></a>Az SQL API-minta

A következő példakód bemutatja, hogyan hozzon létre egy új SQL-tároló két egyedi kulcsra vonatkozó megkötések. Az első pedig az Utónév, a Vezetéknév, e-mailben a korábbi példában bemutatott korlátozás. A második pedig a felhasználók cím/zipCode. Egy minta JSON-fájl által használt elérési utak a egyedi kulcs házirend a Kódpélda követi. 

```csharp
// Create a collection with two separate UniqueKeys, one compound key for /firstName, /lastName,
// and /email, and another for /address/zipCode.
private static async Task CreateCollectionIfNotExistsAsync(string dataBase, string collection)
{
    try
    {
        await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(dataBase, collection));
    }
    catch (DocumentClientException e)
    {
        if (e.StatusCode == System.Net.HttpStatusCode.NotFound)
        {
            DocumentCollection myCollection = new DocumentCollection();
            myCollection.Id = collection;
            myCollection.PartitionKey.Paths.Add("/pk");
            myCollection.UniqueKeyPolicy = new UniqueKeyPolicy
            {
                UniqueKeys =
                new Collection<UniqueKey>
                {
                    new UniqueKey { Paths = new Collection<string> { "/firstName" , "/lastName" , "/email" }}
                    new UniqueKey { Paths = new Collection<string> { "/address/zipCode" } },

                }
            };
            await client.CreateDocumentCollectionAsync(
                UriFactory.CreateDatabaseUri(dataBase),
                myCollection,
                new RequestOptions { OfferThroughput = 2500 });
        }
        else
        {
            throw;
        }
    }
```

A minta JSON-dokumentum.

```json
{
    "id": "1",
    "firstName": "Gaby",
    "lastName": "Duperre",
    "email": "gaby@contoso.com",
    "address": [
        {            
            "line1": "100 Some Street",
            "line2": "Unit 1",
            "city": "Seattle",
            "state": "WA",
            "zipCode": 98012
        }
    ],
}
```
## <a name="mongodb-api-sample"></a>MongoDB API minta

A következő parancs minta bemutatja, hogyan hozzon létre egyedi indexet a Vezetéknév, Vezetéknév és e-mailek mezők a felhasználók gyűjtemény a MongoDB API. Ez biztosítja az összes három mező egyediségi a gyűjteményben lévő összes dokumentum. A MongoDB API gyűjteményekhez az egyedi index jön létre, a gyűjtemény létrehozása után, de a gyűjteményhez történő feltöltés előtt.

```
db.users.createIndex( { firstName: 1, lastName: 1, email: 1 }, { unique: true } )
```

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, elemek egyedi kulcs létrehozása egy adatbázisban. Ha első alkalommal hoz létre egy tárolót, tekintse át a [adatokat az Adatbázisba az Azure Cosmos particionálás](partition-data.md) , egyedi kulcsok és a partíciós kulcsok támaszkodnak egymással. 


