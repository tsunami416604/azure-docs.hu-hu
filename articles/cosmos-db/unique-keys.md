---
title: Azure Cosmos DB-ben egyedi kulcsaival |} A Microsoft Docs
description: Ismerje meg az Azure Cosmos DB adatbázis egyedi kulcsok használata.
services: cosmos-db
keywords: egyedi key megkötést, egyedi kulcsmegkötés megsértése
author: rafats
manager: kfile
editor: monicar
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 08/08/2018
ms.author: rafats
ms.openlocfilehash: 796971ff541b62a22a70df4022ab78817e7158e9
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2018
ms.locfileid: "40003317"
---
# <a name="unique-keys-in-azure-cosmos-db"></a>Azure Cosmos DB-ben egyedi kulcsaival

Egyedi kulcsok nyújtanak a fejlesztők számára lehetővé teszi, hogy adatintegritási réteget egy saját adatbázisba. Egyedi hoz létre, amikor egy tároló jön létre, egy vagy több értéket kiszolgálónként egyediségének biztosítása [partíciókulcs](partition-data.md). Miután a tároló egyedi létre lett hozva, ez megakadályozza, hogy az ismétlődő értékek az egyedi kulcsmegkötés által megadott értéket tartalmazó új vagy frissített elemek létrehozását.   

> [!NOTE]
> A legújabb verziói által támogatott egyedi kulcsok a [.NET](sql-api-sdk-dotnet.md) és [.NET Core](sql-api-sdk-dotnet-core.md) SQL SDK-k és a [MongoDB API-val](mongodb-feature-support.md#unique-indexes). A Table API és a Graph API támogatja jelenleg egyedi kulcsok. 
> 
>

## <a name="use-case"></a>Használati eset

Példaként nézzük, hogyan egy felhasználói adatbázishoz társított egy [közösségi alkalmazás](use-cases.md#web-and-mobile-applications) hasznát venném egyedi kellene az e-mail-címeket. Azáltal, hogy a felhasználó e-mail-cím egy egyedi kulcsot, akkor győződjön meg arról, minden egyes rekord egyedi e-mail-címmel rendelkezik, és nincs új rekord hozható létre duplikált e-mail-címmel rendelkező. 

Ha azt szeretné, hogy létre tudja hozni a felhasználók adott több rekord ugyanazt az e-mail címét, de nem az azonos Utónév, Vezetéknév és az e-mail cím, hozzáadhat további elérési utak a egyedi kulcs szabályzathoz. Ezért egy e-mail-cím alapján egyedi kulcs létrehozása helyett létrehozhat egy egyedi kulcs, amely az utónevét, vezetéknevét és e-mail kombinációja. Ebben az esetben három módjának minden egyéni kombinációja engedélyezve van, ezért az adatbázis elemek, amelyekhez a következő elérési út értékeket tartalmazhat. Az egyedi kulcs házirend egyes ezeket a rekordokat kellene átadnia.  

**Megengedett értékek az Utónév, Vezetéknév és e-mailek egyedi kulcs**

|Utónév|Vezetéknév|E-mail-cím|
|---|---|---|
|Gaby|Duperre|gaby@contoso.com |
|Gaby|Duperre|gaby@fabrikam.com|
|Ivan|Duperre|gaby@fabrikam.com|
|    |Duperre|gaby@fabrikam.com|
|    |       |gaby@fabraikam.com|

Egy másik rekord beillesztése a fenti táblázatban szereplő kombinációk bármelyikével kísérelt meg, ha meg szeretné megjelenik egy hibaüzenet, hogy az egyedi megkötés nem teljesült. Azure Cosmos DB által visszaadott hiba a "Erőforrás a megadott azonosító vagy név már létezik." vagy az "Erőforrás a megadott azonosító, név vagy egyedi index már létezik." 

## <a name="using-unique-keys"></a>Egyedi kulcsok használata

Egyedi kulcsok kell meghatározni, ha a tároló jön létre, és a partíciókulcs hatókörét egyedi kulcsa. A korábbi példában létrehozásához, ha particionálja alapján zip-kód, az egyes partíciók duplikált tábla rekordjai lehet.

Meglévő tároló egyedi kulcsok használata nem frissíthető.

Egyedi-tároló létrehozása után a szabályzat nem módosítható, kivéve, ha a tároló hozza létre újra. Ha rendelkezik meglévő egyedi kulcsok megvalósítani kívánt adatokat, az új tároló létrehozása, és a megfelelő adatáttelepítési eszköz segítségével az adatok áthelyezése az új tárolóhoz. SQL-tárolók, használja a [adatáttelepítési eszköz](import-data.md). Használja a MongoDB-tárolók, [mongoimport.exe vagy mongorestore.exe](mongodb-migrate.md).

Minden egyedi kulcs legfeljebb 16 elérési útja értéket (például /firstName, /lastName, /address/zipCode, stb.) is szerepelnek. 

Minden egyedi kulcs szabályzat legfeljebb 10 egyedi kulcsra vonatkozó megkötések, vagy kombinációk és az összes egyedi index tulajdonságok kombinált elérési útjait nem lehet hosszabb 60 karakternél. Így a korábbi példában használó Utónév, Vezetéknév, e-mail-cím csak egyetlen megkötés, és három elérhető 16 lehetséges elérési utakat használ. 

Kérelem egység díjat létrehozása, frissítése, és a egy elem törlése lesz valamivel nagyobb, ha a tároló egyedi. 

Ritka egyedi kulcsok nem támogatottak. Ha az egyes egyedi elérési utak érték hiányzik, ezeket külön null értékként, amely szerepel az egyediségre vonatkozó feltételnek kell kezelni.

## <a name="sql-api-sample"></a>Az SQL API-minta

Az alábbi példakód bemutatja, hogyan hozzon létre egy új SQL-tároló két egyedi kulcsokra vonatkozó korlátozások. Az első akadálya, az Utónév, Vezetéknév, e-mail-korlátozást a korábbi példában bemutatott módon. A második pedig a felhasználók cím/irányítószám. Egy példa JSON-fájlt, amely az elérési utakat használ a egyedi kulcs házirend követi a példakód. 

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

Példa JSON-dokumentumok.

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
> Adjon a megjegyzés egyedi nevet, kis-és nagybetűket. A fenti példában látható, egyedi nevet /address/zipcode van beállítva. Ha az adatok irányítószám, majd azt szúrja be "null" egyedi kulcs irányítószám nem egyenlő irányítószám szerint. És a kis-és nagybetűk miatt irányítószám az összes többi rekordot nem fogja tudni lehet beszúrni, mert ismétlődő "null" megsérti az egyedi megkötés.

## <a name="mongodb-api-sample"></a>MongoDB API-minta

Az alábbi parancs minta bemutatja, hogyan hozzon létre egy egyedi indexet a firstName, lastName és e-mailek mezőket a felhasználóknak gyűjtemény a MongoDB API-hoz. Ez biztosítja az egyedi-e az összes három mezőt kombinációja a gyűjteményben lévő összes dokumentum. MongoDB API-gyűjtemény az egyedi index jön létre a gyűjtemény létrehozása után, de a gyűjtemény feltöltése előtt.

> [!NOTE]
> A MongoDB API-fiókok egyedi kulcs formátuma eltér a az SQL API-fiókok, ahol nem kell a fordított perjel (/) karakter előtt a mező nevét adja meg. 

```
db.users.createIndex( { firstName: 1, lastName: 1, email: 1 }, { unique: true } )
```
## <a name="configure-unique-keys-by-using-azure-portal"></a>Egyedi kulcsok konfigurálása az Azure Portal használatával

A fenti szakaszokban található Kódminták bemutatjuk, hogyan adhatja meg egyedi kulcsra vonatkozó megkötések, amikor a rendszer létrehoz egy gyűjteményt az SQL API-t vagy a MongoDB API használatával. De azt is egyedi kulcsok határozhatók meg, ha az Azure Portalon létrehozhat egy gyűjteményt a webes felhasználói felületen. 

- Keresse meg a **adatkezelő** a Cosmos DB-fiókban
- Kattintson a **új gyűjtemény**
- A szakasz egyedi kulcsok ** gombra kattintva adhat hozzá a kívánt egyedi kulcsra vonatkozó megkötések **egyedi kulcs hozzáadása**

![Egyedi kulcsok definiálása az adatkezelőben](./media/unique-keys/unique-keys-azure-portal.png)

- Ha meg szeretne létrehozni egy egyedi kulcsmegkötés lastName elérési úton, hozzáadhat `/lastName`.
- Ha meg szeretne létrehozni egy egyedi kulcsmegkötés a lastName firstName kombináció, hozzáadása `/lastName,/firstName`

Ezzel a módszerrel kattintson **OK** a gyűjtemény létrehozásához.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan hozzon létre egyedi kulcsok elemek egy adatbázisban. Ha első alkalommal hoz létre egy tárolót, tekintse át [adatokat az Azure Cosmos DB particionálási](partition-data.md) , egyedi kulcsok és a partíciókulcsok támaszkodnak egymással. 


