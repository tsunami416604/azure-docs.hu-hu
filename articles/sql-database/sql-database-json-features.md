---
title: "Azure SQL adatbázis JSON-funkció |} Microsoft Docs"
description: "Az Azure SQL Database segítségével elemzési, lekérdezés, és adatok formázása a JavaScript Object Notation (JSON) jelöléssel."
services: sql-database
documentationcenter: 
author: jovanpop-msft
manager: jhubbard
editor: 
ms.assetid: 55860105-2f5f-4b10-87a0-99faa32b5653
ms.service: sql-database
ms.custom: develop databases
ms.devlang: NA
ms.date: 11/15/2016
ms.author: jovanpop
ms.workload: On Demand
ms.topic: article
ms.tgt_pltfrm: NA
ms.openlocfilehash: 8877b0bb779501df003ce11d66d9625c2f99e9ce
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2017
---
# <a name="getting-started-with-json-features-in-azure-sql-database"></a>Ismerkedés az Azure SQL Database JSON szolgáltatásai
Az Azure SQL Database lehetővé teszi elemezni, és JavaScript Object Notation ábrázolt adatok lekérdezése [(JSON)](http://www.json.org/) formázása, és a relációs adatok exportálása JSON-szövegként.

JSON-ja adatcsere modern webes és mobilalkalmazások használt népszerű adatformátum. JSON is félig strukturált adatok tárolásához, naplófájlokban vagy hasonló NoSQL-adatbázisok használt [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/). Sok REST webszolgáltatások ad találatot formázott JSON-szövegként, vagy fogadja el az adatok JSON formátumú. A legtöbb Azure szolgáltatásokkal – például [Azure Search](https://azure.microsoft.com/services/search/), [Azure Storage](https://azure.microsoft.com/services/storage/), és [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) REST-végpont vissza vagy JSON felhasználását.

Azure SQL Database lehetővé teszi a JSON-adatok egyszerűen dolgozhat, és az adatbázis olyan modern-szolgáltatásokkal integrálja.

## <a name="overview"></a>Áttekintés
Az Azure SQL-adatbázis a következő funkciókat biztosít a JSON-adatokkal végzett munka:

![JSON-funkciók](./media/sql-database-json-features/image_1.png)

Ha JSON-szöveg, JSON kinyeri az adatokat, vagy győződjön meg arról, hogy a beépített funkciókkal megfelelően formázott JSON [JSON_VALUE](https://msdn.microsoft.com/library/dn921898.aspx), [JSON_QUERY](https://msdn.microsoft.com/library/dn921884.aspx), és [ISJSON](https://msdn.microsoft.com/library/dn921896.aspx). A [JSON_MODIFY](https://msdn.microsoft.com/library/dn921892.aspx) funkció lehetővé teszi, hogy frissítse JSON-szövegben lévő értéket. További speciális lekérdezés és elemzés céljából, [OPENJSON](https://msdn.microsoft.com/library/dn921885.aspx) függvény a sorokra alakíthatja át a JSON-objektumok tömbjét. SQL-lekérdezést a visszaadott eredményhalmaz hajtható végre. Végül pedig a [FOR JSON](https://msdn.microsoft.com/library/dn921882.aspx) záradékot, amely lehetővé teszi, hogy formázni a JSON-szövegként a relációs táblákban tárolt adatokat.

## <a name="formatting-relational-data-in-json-format"></a>Formázási relációs adatok JSON formátumban
Ha egy webszolgáltatás-bővítmény vesz az adatbázis adatait réteg és biztosít egy válasz JSON formátumú, vagy JSON-ként formázott ügyféloldali JavaScript-keretrendszerek vagy szalagtár szerepel, amely adatokat fogad, közvetlenül az SQL-lekérdezést az adatbázis-tartalom formázhatók JSON-ként. Már nem írása, amely az Azure SQL Database JSON-ként eredményeinek formázza alkalmazáskód használ, vagy bizonyos alakítsa át a táblázatos lekérdezési eredményeket, majd a JSON formátumban objektumokat szerializálni a JSON-szerializálási objektumtára tartalmazza. A FOR JSON záradék használatával ehelyett SQL lekérdezési eredmények formázza a JSON-t az Azure SQL Database, és közvetlenül az alkalmazás használatát.

A következő példában a Sales.Customer tábla azon sorait-ként formázott JSON a FOR JSON záradék használatával:

```
select CustomerName, PhoneNumber, FaxNumber
from Sales.Customers
FOR JSON PATH
```

A FOR JSON Path UTASÍTÁST záradék a lekérdezés eredményeinek JSON-szövegként formázza. Oszlopnevek használatosak kulcsok, amíg az értékek JSON értékként jönnek létre:

```
[
{"CustomerName":"Eric Torres","PhoneNumber":"(307) 555-0100","FaxNumber":"(307) 555-0101"},
{"CustomerName":"Cosmina Vlad","PhoneNumber":"(505) 555-0100","FaxNumber":"(505) 555-0101"},
{"CustomerName":"Bala Dixit","PhoneNumber":"(209) 555-0100","FaxNumber":"(209) 555-0101"}
]
```

A JSON-tömb, ahol mindegyik sor külön JSON-objektumként van formázva az eredményhalmaz formátuma.

Elérési út azt jelenti, hogy testre szabhatja a kimeneti formátum a JSON eredményének oszlopaliasnévként pontjelöléssel. A következő lekérdezés módosítja a kimeneti JSON formátumban a "CustomerName" kulcs nevét, és a telefon-és faxszáma helyezi az "Ügyfél" altípusa objektum:

```
select CustomerName as Name, PhoneNumber as [Contact.Phone], FaxNumber as [Contact.Fax]
from Sales.Customers
where CustomerID = 931
FOR JSON PATH, WITHOUT_ARRAY_WRAPPER
```

Ez a lekérdezés eredményének így néz ki:

```
{
    "Name":"Nada Jovanovic",
    "Contact":{
           "Phone":"(215) 555-0100",
           "Fax":"(215) 555-0101"
    }
}
```

Ebben a példában azt egy adott JSON-objektum helyett egy tömb által visszaadott megadása a [WITHOUT_ARRAY_WRAPPER](https://msdn.microsoft.com/library/mt631354.aspx) lehetőséget. Ezt a beállítást is használhatja, ha tudja, hogy a lekérdezés egyetlen objektumot ad vissza.

A fő a FOR JSON záradék értéke, mellyel vissza összetett hierarchikus adatokat az adatbázisból, beágyazott JSON-objektumok vagy tömbök formázott. A következő példa bemutatja, hogyan tartalmazza, amelyek az ügyfél rendelések beágyazott tömb rendeléseket:

```
select CustomerName as Name, PhoneNumber as Phone, FaxNumber as Fax,
        Orders.OrderID, Orders.OrderDate, Orders.ExpectedDeliveryDate
from Sales.Customers Customer
    join Sales.Orders Orders
        on Customer.CustomerID = Orders.CustomerID
where Customer.CustomerID = 931
FOR JSON AUTO, WITHOUT_ARRAY_WRAPPER

```

Helyett külön felhasználói adatok lekérdezését, és majd beolvasni a kapcsolódó rendelések, kaphat egyetlen lekérdezést, az összes szükséges adatot küld a következő minta kimenet látható módon:

```
{
  "Name":"Nada Jovanovic",
  "Phone":"(215) 555-0100",
  "Fax":"(215) 555-0101",
  "Orders":[
    {"OrderID":382,"OrderDate":"2013-01-07","ExpectedDeliveryDate":"2013-01-08"},
    {"OrderID":395,"OrderDate":"2013-01-07","ExpectedDeliveryDate":"2013-01-08"},
    {"OrderID":1657,"OrderDate":"2013-01-31","ExpectedDeliveryDate":"2013-02-01"}
]
}
```

## <a name="working-with-json-data"></a>JSON-adatok használata
Ha nincs szigorúan strukturált adatok, ha olyan alárendelt összetett objektumok, tömbök vagy hierarchikus adatokhoz, vagy a adatstruktúrák verzióinformációk, a JSON formátum segítségével bármely összetett adatszerkezet képviseli.

JSON-ja a szöveges formátum, mint minden más karakterlánc az Azure SQL Database használható. Küldjön, vagy JSON-adatok tárolót, mint egy normál NVARCHAR:

```
CREATE TABLE Products (
  Id int identity primary key,
  Title nvarchar(200),
  Data nvarchar(max)
)
go
CREATE PROCEDURE InsertProduct(@title nvarchar(200), @json nvarchar(max))
AS BEGIN
    insert into Products(Title, Data)
    values(@title, @json)
END
```

A JSON-adatokat ebben a példában használt jelképezi a NVARCHAR(MAX) típus használatával. JSON szúrhatók be ezt a táblázatot, vagy a tárolt eljárás szabványos Transact-SQL-szintaxis használatával az alábbi példában látható módon argumentumaként megadott:

```
EXEC InsertProduct 'Toy car', '{"Price":50,"Color":"White","tags":["toy","children","games"]}'
```

Bármely ügyféloldali nyelvi vagy a könyvtárban, amely az Azure SQL-adatbázis karakterlánc-adatokkal is működik a JSON-adatokat. JSON tárolhatja bármilyen, amely támogatja a NVARCHAR típus, például egy memóriaoptimalizált vagy tábla rendszerverzióval ellátott tábla. JSON nem vezet be bármely megkötés vagy az ügyféloldali kódot, vagy az adatbázis-rétegben.

## <a name="querying-json-data"></a>JSON-adatok lekérdezése
Ha az Azure SQL-táblák tárolt JSON-ként formázott adatok, JSON funkciók lehetővé teszik, hogy ezeket az adatokat az SQL-lekérdezésben használni.

JSON-funkciók Azure SQL adatbázis lehetővé teszik a rendelkezésre álló úgy kezelje, mint bármely más SQL adattípus JSON formátumú adatok. Könnyen értékek kinyerése a JSON-szöveg, és JSON-adatokat a lekérdezésben használni:

```
select Id, Title, JSON_VALUE(Data, '$.Color'), JSON_QUERY(Data, '$.tags')
from Products
where JSON_VALUE(Data, '$.Color') = 'White'

update Products
set Data = JSON_MODIFY(Data, '$.Price', 60)
where Id = 1
```

A JSON_VALUE függvény értéket kiolvassa a az adatok oszlopban tárolt JSON-szöveg. Ez a funkció a JavaScript-szerű elérési út egy JSON-szövegben kibontásához értékre hivatkoznak. A kiolvasott értékkel használható SQL-lekérdezésben bármely részén.

A JSON_QUERY függvény JSON_VALUE hasonlít. Eltérően JSON_VALUE Ez a függvény ki összetett alárendelt objektumot, például a tömb, vagy a JSON-szövegben kerülnek objektumokat.

A JSON_MODIFY funkció lehetővé teszi a JSON-szöveg, amelyet frissíteni kell, valamint egy értéket, amely a régit, azzal felülírja a érték elérési útjának megadása. Ily módon egyszerűen frissítheti JSON-szöveg struktúra reparsing nélkül.

Mivel a JSON egyszerű szövegként tárolódnak, nincsenek nem garantálja, hogy a szöveges oszlop tárolt értékek megfelelően formázott. Ellenőrizheti, hogy a szöveg JSON oszlopban tárolt megfelelően van-e formázva szabványos Azure SQL Database ellenőrzési korlátozásokban, és a ISJSON függvény használatával:

```
ALTER TABLE Products
    ADD CONSTRAINT [Data should be formatted as JSON]
        CHECK (ISJSON(Data) > 0)
```

Ha a bemeneti szöveg megfelelően formázott JSON-ban, a ISJSON függvény adja vissza az 1. Minden Beszúrás vagy frissítés JSON oszlop ennél a határértéknél fog ellenőrizze, hogy új szöveges érték nem nem megfelelően formázott JSON-NÁ.

## <a name="transforming-json-into-tabular-format"></a>A táblázatos formátumra JSON átalakítása
Az Azure SQL Database emellett lehetővé teszi a JSON-gyűjtemények adatokká táblázatos formátumban és a load vagy a lekérdezés JSON átalakító.

OPENJSON JSON-szöveg elemez, megkeresi a JSON-objektumok tömbje, a tömb elemei telepítéseket és több olyan sort adja vissza a kimeneti eredmények a tömb egyes elemei a táblázat értékű függvényből.

![A táblázatos JSON](./media/sql-database-json-features/image_2.png)

A fenti példában hol található a JSON-tömb, amely (az a $ meg kell nyitni adható meg. Rendelések elérési út), az eredmény, hol található a JSON-értékek cellákat visszaadott kell adható vissza oszlopokat.

Egy JSON-tömb, az azt alakíthatja át a @orders sorokra, a változó eredménykészlet elemzése, vagy sorok beillesztéséhez egy szabványos táblázatot:

```
CREATE PROCEDURE InsertOrders(@orders nvarchar(max))
AS BEGIN

    insert into Orders(Number, Date, Customer, Quantity)
    select Number, Date, Customer, Quantity
    OPENJSON (@orders)
     WITH (
            Number varchar(200),
            Date datetime,
            Customer varchar(200),
            Quantity int
     )

END
```
A gyűjtemény rendelések formázott JSON-tömb, és a tárolt eljárás paramétere elemzése, és a rendeléseket táblába beszúrt egyikéhez.

## <a name="next-steps"></a>Következő lépések
Megtudhatja, hogyan integrálható a JSON az alkalmazásba, jelölje ki ezeket az erőforrásokat:

* [TechNet Blog](https://blogs.technet.microsoft.com/dataplatforminsider/2016/01/05/json-in-sql-server-2016-part-1-of-4/)
* [Az MSDN dokumentációját](https://msdn.microsoft.com/library/dn921897.aspx)
* [A Channel 9 videót](https://channel9.msdn.com/Shows/Data-Exposed/SQL-Server-2016-and-JSON-Support)

A JSON integrálása az alkalmazás különböző forgatókönyvekkel kapcsolatos további tudnivalókért lásd: ezen bemutatók [Channel 9 videót](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/JSON-as-a-bridge-betwen-NoSQL-and-relational-worlds) vagy olyan forgatókönyvekben, amelyek a használati eset a megfelelő található [JSON blogbejegyzéseket](http://blogs.msdn.com/b/sqlserverstorageengine/archive/tags/json/).

