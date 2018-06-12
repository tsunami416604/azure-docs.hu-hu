---
title: Az Azure Cosmos Adatbázisba egyedi kulcsok |} Microsoft Docs
description: Megtudhatja, hogyan egyedi kulcsok használata az Azure Cosmos DB adatbázisban.
services: cosmos-db
keywords: egyedi kulcs korlátozás, egyedi kulcsmegkötés megsértése
author: rafats
manager: kfile
editor: monicar
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 03/21/2018
ms.author: rafats
ms.openlocfilehash: d12109efbb157b1e0c15b1a4c0d005fa98c44858
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35261100"
---
# <a name="unique-keys-in-azure-cosmos-db"></a>Egyedi kulcsok vannak az Azure Cosmos-Adatbázisba

Egyedi kulcsok vannak elhelyezve a fejlesztők a adatintegritást réteg hozzáadása az adatbázishoz. Hozzon létre egy egyedi kulcs házirend, a tároló létrehozásakor, egy vagy több érték / egyediségének biztosítása [partíciókulcs](partition-data.md). Egy tároló létrehozása után a egyedi kulcs házirendnek, megakadályozza, hogy az ismétlődő értékek, az egyedi kulcs korlátozás által megadott értéket tartalmazó új vagy frissített elemek létrehozását.   

> [!NOTE]
> A legújabb verziói által támogatott egyedi kulcsok vannak a [.NET](sql-api-sdk-dotnet.md) és [.NET Core](sql-api-sdk-dotnet-core.md) SQL SDK-k, és a [MongoDB API](mongodb-feature-support.md#unique-indexes). A tábla API és a Graph API nem támogatja egyedi kulcsok vannak jelenleg. 
> 
>

## <a name="use-case"></a>Használati eset

Tegyük fel, vizsgáljuk meg egy felhasználói adatbázis társított hogyan egy [közösségi alkalmazás](use-cases.md#web-and-mobile-applications) egy egyedi kulcs házirend gyakorló e-mail címeket is hasznosak. Azáltal, hogy a felhasználó e-mail címe egyedi kulcs, akkor győződjön meg arról, minden egyes rekord tartalmazza-e egy egyedi e-mail címet, és nem új rekord duplikált e-mail címmel rendelkező hozható létre. 

Ha azt szeretné, hogy létre tudja hozni a felhasználók adott több bejegyzés azonos e-mail címet, de nem a azonos Utónév, Vezetéknév és az e-mail cím, más elérési utak hozzáadhatja az egyedi kulcs házirend. Ezért helyett egy e-mail cím alapján egyedi kulcs létrehozása, létrehozhat egy egyedi kulcs, amely a utónevét, vezetéknevét és e-mail kombinációja. Ebben az esetben egyes három módjának egyedi kombinációja engedélyezve van, ezért az adatbázis elemek, amelyekhez a következő elérési út értékek tartalmazhatnak. Ezeket a rekordokat mindegyikének volna adja át a egyedi kulcs házirend.  

**Engedélyezett értékek az Utónév, a Vezetéknév és az e-mailek egyedi kulcsa**

|Utónév|Vezetéknév|E-mail-cím|
|---|---|---|
|Gaby|Duperre|gaby@contoso.com |
|Gaby|Duperre|gaby@fabrikam.com|
|Ivan|Duperre|gaby@fabrikam.com|
|    |Duperre|gaby@fabrikam.com|
|    |       |gaby@fabraikam.com|

Ha próbált beszúrni egy másik rekord, amelynek az a fenti táblázatban kombinációk, akkor hibaüzenet jelzi, hogy az egyedi megkötés nem teljesített. Azure Cosmos DB visszaadott hiba: "Erőforrás a megadott azonosító vagy név már létezik." vagy "Erőforrás a megadott azonosító, név vagy egyedi index már létezik." 

## <a name="using-unique-keys"></a>Egyedi kulcsok használata

Egyedi kulcsot kell meghatározni, ha a tároló jön létre, és egyedi kulcsa a partíciós kulcs hatókörét. A korábbi példában létrehozásához, ha Ön partícióazonosító alapján irányítószám, a tábla duplikált mindegyik partíció a rekord lehet.

Meglévő tároló egyedi kulcsok használata nem frissíthető.

A tároló egy egyedi kulcs házirend létrehozása után a házirend nem módosítható, kivéve, ha a tároló hozza létre újra. Ha szeretné valósítsa meg az egyedi kulcsok vannak a meglévő adatok, az új tároló létrehozásához, és a megfelelő adatok áttelepítési eszköz használatával helyezze át az adatokat az új tárolóhoz. Az SQL-tárolókat, használja a [adatáttelepítési eszköz](import-data.md). A MongoDB-tárolók, használjon [mongoimport.exe vagy mongorestore.exe](mongodb-migrate.md).

Minden egyedi kulcs legfeljebb 16 elérési értéket (például /firstName, /lastName, /address/zipCode stb.) tartalmazhat. 

Minden egyedi kulcs házirend rendelkezhet legfeljebb 10 egyedi kulcsra vonatkozó megkötések, vagy kombinációk és a kombinált útvonalak egyedi index összes tulajdonság nem lehet hosszabb 60 karakternél. Így a korábbi példában használó Utónév, vezetéknevét, és az e-mail cím csak egyetlen megkötés, és három elérhető 16 lehetséges elérési utak használ. 

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
                    new UniqueKey { Paths = new Collection<string> { "/address/zipcode" } },
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
    "pk": "1234",
    "firstName": "Gaby",
    "lastName": "Duperre",
    "email": "gaby@contoso.com",
    "address": 
        {            
            "line1": "100 Some Street",
            "line2": "Unit 1",
            "city": "Seattle",
            "state": "WA",
            "zipcode": 98012
        }
    
}
```
> [!NOTE]
> Adja a megjegyzés egyedi nevet, és a nagybetűk között. Ahogy a fenti példa, /address/zipcode egyedi neve van beállítva. Ha az adatok ZipCode fog működni, majd azt szúrnak "null" egyedi kulcs, zipcode nincs ZipCode egyenlő. És miatt a kis-és nagybetűk ZipCode az összes többi rekordot nem fogja tudni lehet beszúrni, ismétlődő "null" megsérti a egyedi kulcs korlátozás.

## <a name="mongodb-api-sample"></a>MongoDB API minta

A következő parancs minta bemutatja, hogyan hozzon létre egyedi indexet a Vezetéknév, Vezetéknév és e-mailek mezők a felhasználók gyűjtemény a MongoDB API. Ez biztosítja az összes három mező egyediségi a gyűjteményben lévő összes dokumentum. A MongoDB API gyűjteményekhez az egyedi index jön létre, a gyűjtemény létrehozása után, de a gyűjteményhez történő feltöltés előtt.

```
db.users.createIndex( { firstName: 1, lastName: 1, email: 1 }, { unique: true } )
```
## <a name="configure-unique-keys-by-using-azure-portal"></a>Egyedi kulcsok konfigurálása az Azure portál használatával

Fent, akkor a szakaszokban találhat mintakódok bemutatjuk, hogyan adhatja meg egyedi kulcsra vonatkozó megkötések, a gyűjtemény létrehozásakor az SQL API-t vagy a MongoDB API használatával. De is definiálhat egyedi kulcs, ha az Azure portálon létrehoz egy gyűjteményt a webes felhasználói felületen keresztül. 

- Keresse meg a **adatkezelő** Cosmos DB fiókja
- Kattintson a **új gyűjtemény**
- A szakasz egyedi kulcsok ** kattintva adhat hozzá a kívánt egyedi kulcsra vonatkozó megkötések **egyedi kulcs hozzáadása**

![Adjon meg egyedi kulcs az adatkezelő](./media/unique-keys/unique-keys-azure-portal.png)

- Ha szeretné hozzon létre egy egyedi kulcs korlátozás a Vezetéknév útvonalon, hozzáadhat `/lastName`.
- Ha szeretné hozzon létre egy egyedi kulcs megszorítását a Vezetéknév Keresztnév kombinációja, ad hozzá `/lastName,/firstName`

Miután befejezte az kattintson **OK** a gyűjtemény létrehozásához.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, elemek egyedi kulcs létrehozása egy adatbázisban. Ha első alkalommal hoz létre egy tárolót, tekintse át a [adatokat az Adatbázisba az Azure Cosmos particionálás](partition-data.md) , egyedi kulcsok és a partíciós kulcsok támaszkodnak egymással. 


