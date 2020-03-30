---
title: JSON-adatok használata
description: Az Azure SQL Database lehetővé teszi az adatok elemzését, lekérdezését és formázását a JavaScript-objektumjelölés (JSON) jelölésében.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
ms.date: 01/15/2019
ms.openlocfilehash: 958d937ad85fd62249c7ce3f0e0ab2f8cc1d1b80
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73819939"
---
# <a name="getting-started-with-json-features-in-azure-sql-database"></a>A JSON funkcióinak első lépései az Azure SQL Database-ben
Az Azure SQL Database lehetővé teszi a JavaScript-objektumjelölés [(JSON)](https://www.json.org/) formátumban ábrázolt adatok elemzésével és lekérdezésével, és a relációs adatok JSON-szövegként történő exportálását. Az Azure SQL Database-ben a következő JSON-forgatókönyvek érhetők el:
- [Relációs adatok formázása JSON formátumban](#formatting-relational-data-in-json-format) záradék használatával. `FOR JSON`
- [JSON-adatok használata](#working-with-json-data)
- [JSON-adatok lekérdezése](#querying-json-data) JSON skalárfüggvények használatával.
- [A JSON átalakítása táblázatos formátumba](#transforming-json-into-tabular-format) a függvény használatával. `OPENJSON`

## <a name="formatting-relational-data-in-json-format"></a>Relációs adatok formázása JSON formátumban
Ha olyan webszolgáltatással rendelkezik, amely adatokat fogad el az adatbázisrétegből, és JSON formátumú választ ad, vagy ügyféloldali JavaScript-keretrendszereket vagy -kódtárakat, amelyek JSON formátumú adatokat fogadnak el, az adatbázis tartalmát jsonként közvetlenül az SQL-lekérdezésben formázhatja. Többé nem kell írnia az Azure SQL Database-ből Származó eredményeket JSON-ként formázó alkalmazáskódot, vagy néhány JSON-szerializálási könyvtárat kell megadnia a táblázatos lekérdezéseredmények konvertálásához, majd az objektumok JSON formátumba való szerializálásához. Ehelyett használhatja a FOR JSON-záradékot az SQL-lekérdezési eredmények JSON-ként való formázásához az Azure SQL Database-ben, és közvetlenül az alkalmazásban használhatja.

A következő példában a Sales.Customer tábla sorai JSON-ként vannak formázva a FOR JSON záradék használatával:

```
select CustomerName, PhoneNumber, FaxNumber
from Sales.Customers
FOR JSON PATH
```

A FOR JSON PATH záradék a lekérdezés eredményeit JSON-szövegként formázza. Az oszlopnevek et kulcsokként használják, míg a cellaértékekJSON-értékekként jönnek létre:

```
[
{"CustomerName":"Eric Torres","PhoneNumber":"(307) 555-0100","FaxNumber":"(307) 555-0101"},
{"CustomerName":"Cosmina Vlad","PhoneNumber":"(505) 555-0100","FaxNumber":"(505) 555-0101"},
{"CustomerName":"Bala Dixit","PhoneNumber":"(209) 555-0100","FaxNumber":"(209) 555-0101"}
]
```

Az eredményhalmaz JSON-tömbként van formázva, ahol minden sor külön JSON-objektumként van formázva.

A PATH azt jelzi, hogy a JSON-eredmény kimeneti formátumát pontjelöléssel szabhatja testre az oszlopaliasokban. A következő lekérdezés módosítja a "CustomerName" kulcs nevét a kimeneti JSON formátumban, és a telefon- és faxszámokat a "Kapcsolattartó" alobjektumba helyezi:

```
select CustomerName as Name, PhoneNumber as [Contact.Phone], FaxNumber as [Contact.Fax]
from Sales.Customers
where CustomerID = 931
FOR JSON PATH, WITHOUT_ARRAY_WRAPPER
```

A lekérdezés kimenete így néz ki:

```
{
    "Name":"Nada Jovanovic",
    "Contact":{
           "Phone":"(215) 555-0100",
           "Fax":"(215) 555-0101"
    }
}
```

Ebben a példában egy JSON-objektumot adtunk vissza tömb helyett a [WITHOUT_ARRAY_WRAPPER](https://msdn.microsoft.com/library/mt631354.aspx) beállítás megadásával. Ezt a beállítást akkor használhatja, ha tudja, hogy egyetlen objektumot ad vissza lekérdezés eredményeként.

A FOR JSON-záradék fő értéke az, hogy lehetővé teszi a beágyazott JSON-objektumokként vagy tömbökként formázott adatbázisból származó összetett hierarchikus adatok visszaadását. A következő példa bemutatja, hogyan `Orders` lehet a `Customer` táblázatból azalábbi `Orders`sorokat egymásba ágyazott tömbként szerepelni:

```
select CustomerName as Name, PhoneNumber as Phone, FaxNumber as Fax,
        Orders.OrderID, Orders.OrderDate, Orders.ExpectedDeliveryDate
from Sales.Customers Customer
    join Sales.Orders Orders
        on Customer.CustomerID = Orders.CustomerID
where Customer.CustomerID = 931
FOR JSON AUTO, WITHOUT_ARRAY_WRAPPER

```

Ahelyett, hogy külön lekérdezéseket küldene az Ügyféladatok beolvasásához, majd a kapcsolódó rendelések listájának beolvasásához, egyetlen lekérdezéssel beszerezheti az összes szükséges adatot, amint az a következő mintakimenetben látható:

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
Ha nem rendelkezik szigorúan strukturált adatokkal, ha összetett alobjektumokkal, tömbökkel vagy hierarchikus adatokkal rendelkezik, vagy ha az adatstruktúrák idővel fejlődnek, a JSON formátum segítségével bármilyen összetett adatstruktúrát képviselhet.

A JSON egy szöveges formátum, amely az Azure SQL Database bármely más karakterlánctípusához hasonlóan használható. A JSON-adatokat szabványos NVARCHAR-ként is elküldheti vagy tárolhatja:

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

Az ebben a példában használt JSON-adatokat az NVARCHAR(MAX) típus jelöli. A JSON beszúrható ebbe a táblába, vagy a tárolt eljárás argumentumaként adható meg a szokásos Transact-SQL szintaxis sal, ahogy az a következő példában látható:

```
EXEC InsertProduct 'Toy car', '{"Price":50,"Color":"White","tags":["toy","children","games"]}'
```

Az Azure SQL Database-ben a karakterlánc-adatokkal működő ügyféloldali nyelvek vagy kódtárak is működni fognak a JSON-adatokkal. A JSON bármely olyan táblában tárolható, amely támogatja az NVARCHAR típust, például egy memóriaoptimalizált vagy egy rendszerverziós táblában. A JSON sem az ügyféloldali kódban, sem az adatbázisrétegben nem vezet be korlátozást.

## <a name="querying-json-data"></a>JSON-adatok lekérdezése
Ha az Azure SQL-táblákban tárolt JSON formátumú adatokat, JSON-függvények lehetővé teszik, hogy ezeket az adatokat bármely SQL-lekérdezésben használja.

Az Azure SQL-adatbázisban elérhető JSON-függvények lehetővé teszik a JSON formátumú adatok kezelését bármely más SQL-adattípusként. A JSON-szövegből egyszerűen kinyerheti az értékeket, és JSON-adatokat használhat bármely lekérdezésben:

```
select Id, Title, JSON_VALUE(Data, '$.Color'), JSON_QUERY(Data, '$.tags')
from Products
where JSON_VALUE(Data, '$.Color') = 'White'

update Products
set Data = JSON_MODIFY(Data, '$.Price', 60)
where Id = 1
```

A JSON_VALUE függvény kibont egy értéket az Adatok oszlopban tárolt JSON-szövegből. Ez a függvény javascript-szerű elérési utat használ a JSON-szövegben lévő érték kibontásához. A kinyert érték az SQL-lekérdezés bármely részében használható.

A JSON_QUERY függvény hasonló a JSON_VALUE. A JSON_VALUE ellentétben ez a függvény összetett alobjektumokat, például jsonszövegbe helyezett tömböket vagy objektumokat bont ki.

A JSON_MODIFY függvény lehetővé teszi, hogy megadja az érték elérési útját a JSON-szövegben, amelyet frissíteni kell, valamint egy új értéket, amely felülírja a régit. Így könnyedén frissítheti a JSON-szöveget anélkül, hogy a teljes szerkezetet újra elemezheti volna.

Mivel a JSON szabványos szövegben van tárolva, nincs garancia arra, hogy a szövegoszlopokban tárolt értékek megfelelően vannak formázva. Ellenőrizheti, hogy a JSON-oszlopban tárolt szöveg megfelelően van-e formázva az Azure SQL Database-ellenőrzési korlátozások és az ISJSON-függvény használatával:

```
ALTER TABLE Products
    ADD CONSTRAINT [Data should be formatted as JSON]
        CHECK (ISJSON(Data) > 0)
```

Ha a bemeneti szöveg megfelelően formázott JSON, az ISJSON függvény az 1 értéket adja vissza. A JSON-oszlop minden beszúrásakor vagy frissítésénél ez a megkötés ellenőrzi, hogy az új szöveges érték nem hibásan formázott JSON-t.

## <a name="transforming-json-into-tabular-format"></a>A JSON átalakítása táblázatos formátumba
Az Azure SQL Database lehetővé teszi a JSON-gyűjtemények táblázatos formátumba való átalakítását, valamint a JSON-adatok betöltését vagy lekérdezését.

Az OPENJSON egy táblaértékű függvény, amely elemzi a JSON-szöveget, megkeresi a JSON-objektumok tömbjét, végighalad a tömb elemein, és a tömb minden eleméhez egy sort ad vissza a kimeneti eredményben.

![JSON táblázatos](./media/sql-database-json-features/image_2.png)

A fenti példában megadhatjuk, hogy hol keresse meg a megnyitandó JSON-tömböt (a $-ban). Rendelések elérési útja), milyen oszlopokat kell eredményként visszaadni, és hol található a cellákként visszaadott JSON-értékek.

A @orders változóJSON-tömbjét sorok halmazává alakíthatjuk át, elemezhetjük ezt az eredményhalmazt, vagy sorokat szúrhatunk be egy szabványos táblába:

```
CREATE PROCEDURE InsertOrders(@orders nvarchar(max))
AS BEGIN

    insert into Orders(Number, Date, Customer, Quantity)
    select Number, Date, Customer, Quantity
    FROM OPENJSON (@orders)
     WITH (
            Number varchar(200),
            Date datetime,
            Customer varchar(200),
            Quantity int
     )

END
```
A JSON-tömbként formázott és a tárolt eljárás paramétereként megadott rendelések gyűjteménye elemezhető és beilleszthető a Rendelések táblába.

## <a name="next-steps"></a>További lépések
A JSON alkalmazásba való integrálásáról az alábbi forrásokból megtudhatja:

* [TechNet Blog](https://blogs.technet.microsoft.com/dataplatforminsider/20../../json-in-sql-server-2016-part-1-of-4/)
* [MSDN dokumentáció](https://msdn.microsoft.com/library/dn921897.aspx)
* [Channel 9 videó](https://channel9.msdn.com/Shows/Data-Exposed/SQL-Server-2016-and-JSON-Support)

Ha többet szeretne megtudni a JSON alkalmazásba való integrálásának különböző forgatókönyveiről, tekintse meg a [Channel 9 videó](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/JSON-as-a-bridge-betwen-NoSQL-and-relational-worlds) bemutatóit, vagy keressen egy olyan forgatókönyvet, amely megfelel a használati esetnek a [JSON blogbejegyzésekben.](https://blogs.msdn.com/b/sqlserverstorageengine/archive/tags/json/)

