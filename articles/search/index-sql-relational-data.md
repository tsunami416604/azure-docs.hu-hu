---
title: SQL relációs adatok modellezése importáláshoz és indexeléshez
titleSuffix: Azure Cognitive Search
description: Ismerje meg, hogyan modellezi a relációs adatokat, de normalizált egy egyszerű eredményhalmaz, az indexelés és a teljes szöveges keresés az Azure Cognitive Search.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 3b973dd05d23d190c77986ca9bf6d39656739cd8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72790092"
---
# <a name="how-to-model-relational-sql-data-for-import-and-indexing-in-azure-cognitive-search"></a>Relációs SQL-adatok modellezése importáláshoz és indexeléshez az Azure Cognitive Search szolgáltatásban

Az Azure Cognitive Search egy sima sorhalmazt fogad el az [indexelési folyamat bemeneteként.](search-what-is-an-index.md) Ha a forrásadatok egy SQL Server relációs adatbázisillesztett tábláiból származnak, ez a cikk bemutatja, hogyan hozhat létre az eredményhalmazt, és hogyan modellezheti a szülő-gyermek kapcsolatot egy Azure Cognitive Search-indexben.

Illusztrációként egy hipotetikus hoteladatbázisra hivatkozunk, amely [a bemutató adatokon](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/hotels)alapul. Tegyük fel, hogy az adatbázis egy Hotels$ táblázatból áll, amely 50 szállodát tartalmaz, és egy Szobák$ asztal különböző típusú, árakés kényelmi szobákkal, összesen 750 szobával. A táblák között egy-a-többhöz kapcsolat van. Megközelítésünkben egy nézet biztosítja azt a lekérdezést, amely 50 sort, szállodánként egy sort ad vissza, és minden sorba beágyazódik a kapcsolódó szobarészletek.

   ![Táblák és megtekintés a Szállodák adatbázisban](media/index-sql-relational-data/hotels-database-tables-view.png "Táblák és megtekintés a Szállodák adatbázisban")


## <a name="the-problem-of-denormalized-data"></a>A probléma a denormalizált adatok

Az egy-a-többhöz kapcsolatok használata során az egyik kihívás az, hogy az egyesített táblákra épülő szabványos lekérdezések denormalizált adatokat adnak vissza, ami nem működik jól az Azure Cognitive Search-forgatókönyvben. Vegye figyelembe a következő példát, amely csatlakozik a szállodákhoz és a szobákhoz.

```sql
SELECT * FROM Hotels$
INNER JOIN Rooms$
ON Rooms$.HotelID = Hotels$.HotelID
```
A lekérdezés eredményei visszaadják az összes Hotel mezőt, majd az összes Szoba mezőt, az előzetes szállodai adatok pedig minden szobaértékhez ismétlődnek.

   ![Denormalizált adatok, redundáns szállodai adatok szobamezők hozzáadásakor](media/index-sql-relational-data/denormalize-data-query.png "Denormalizált adatok, redundáns szállodai adatok szobamezők hozzáadásakor")


Bár ez a lekérdezés sikeres a felszínen (egy síksorkészlet összes adatának biztosítása), nem tudja biztosítani a megfelelő dokumentumstruktúrát a várt keresési élményhez. Az indexelés során az Azure Cognitive Search minden egyes bevitt sorhoz létrehoz egy keresési dokumentumot. Ha a keresési dokumentumok úgy nézett ki, mint a fenti eredmények, akkor volna érzékelt másolatai - hét külön dokumentumot a Twin Dome hotel egyedül. A lekérdezés a "szállodák Floridában" adna vissza hét eredményt csak a Twin Dome hotel, nyomja más releváns szállodák mélyen a keresési eredményeket.

Ahhoz, hogy a várt tapasztalat egy dokumentum egy szálloda, meg kell adnia egy sorhalmaza a megfelelő részletességgel, de teljes körű információkat. Szerencsére, meg tudod csinálni ezt könnyen elfogadásával a technikák ebben a cikkben.

## <a name="define-a-query-that-returns-embedded-json"></a>Beágyazott JSON-értéket visszaadó lekérdezés definiálása

A várt keresési élmény biztosításához az adatkészletnek az Azure Cognitive Search minden egyes keresési dokumentumához egy sorból kell állnia. A példánkban azt szeretnénk, hogy minden szállodához egy sor legyen, de azt is szeretnénk, ha a felhasználók más, a szobákkal kapcsolatos területeken is kereshetnének, mint például az éjszakai ár, az ágyak mérete és száma, vagy a strandra néző kilátás, amelyek mindegyike egy szoba részleteinek része.

A megoldás az, hogy rögzítse a szoba részleteit beágyazott JSON-ként, majd helyezze be a JSON-struktúrát egy nézetben egy mezőbe, ahogy az a második lépésben látható. 

1. Tegyük fel, hogy két összekapcsolt asztala van, szállodák $ és szobák$, amelyek 50 hotel és 750 szoba adatait tartalmazzák, és a HotelID mezőben csatlakoznak. Ezek az asztalok egyenként 50 szállodát és 750 kapcsolódó szobát tartalmaznak.

    ```sql
    CREATE TABLE [dbo].[Hotels$](
      [HotelID] [nchar](10) NOT NULL,
      [HotelName] [nvarchar](255) NULL,
      [Description] [nvarchar](max) NULL,
      [Description_fr] [nvarchar](max) NULL,
      [Category] [nvarchar](255) NULL,
      [Tags] [nvarchar](255) NULL,
      [ParkingIncluded] [float] NULL,
      [SmokingAllowed] [float] NULL,
      [LastRenovationDate] [smalldatetime] NULL,
      [Rating] [float] NULL,
      [StreetAddress] [nvarchar](255) NULL,
      [City] [nvarchar](255) NULL,
      [State] [nvarchar](255) NULL,
      [ZipCode] [nvarchar](255) NULL,
      [GeoCoordinates] [nvarchar](255) NULL
    ) ON [PRIMARY] TEXTIMAGE_ON [PRIMARY]
    GO

    CREATE TABLE [dbo].[Rooms$](
      [HotelID] [nchar](10) NULL,
      [Description] [nvarchar](255) NULL,
      [Description_fr] [nvarchar](255) NULL,
      [Type] [nvarchar](255) NULL,
      [BaseRate] [float] NULL,
      [BedOptions] [nvarchar](255) NULL,
      [SleepsCount] [float] NULL,
      [SmokingAllowed] [float] NULL,
      [Tags] [nvarchar](255) NULL
    ) ON [PRIMARY]
    GO
    ```

2. Hozzon létre egy nézetet a`SELECT * from dbo.Hotels$`szülőtábla ( ) összes mezőjéből, egy új *Szobák* mező hozzáadásával, amely egy beágyazott lekérdezés kimenetét tartalmazza. A **FOR JSON** `SELECT * from dbo.Rooms$` AUTO záradék szerkezetek a kimenet JSON. 

     ```sql
   CREATE VIEW [dbo].[HotelRooms]
   AS
   SELECT *, (SELECT *
              FROM dbo.Rooms$
              WHERE dbo.Rooms$.HotelID = dbo.Hotels$.HotelID FOR JSON AUTO) AS Rooms
   FROM dbo.Hotels$
   GO
   ```

   A következő képernyőképen látható a kapott nézet, a *Szobák* nvarchar mező alján. A *Szobák* mező csak a HotelRooms nézetben létezik.

   ![HotelSzobák kilátás](media/index-sql-relational-data/hotelsrooms-view.png "HoteRooms kilátás")

1. Futtassa `SELECT * FROM dbo.HotelRooms` a sorkészlet beolvasásához. Ez a lekérdezés 50 sort ad vissza, szállodánként egyet, a kapcsolódó szobaadatokat JSON-gyűjteményként. 

   ![Sorkészlet a HotelRooms nézetből](media/index-sql-relational-data/hotelrooms-rowset.png "Sorkészlet a HotelRooms nézetből")

Ez a sorkészlet most már készen áll az Azure Cognitive Search importálására.

> [!NOTE]
> Ez a megközelítés feltételezi, hogy a beágyazott JSON az [SQL Server oszlopméret-korlátja](https://docs.microsoft.com/sql/sql-server/maximum-capacity-specifications-for-sql-server)alatt van. Ha az adatok nem férnek el, kipróbálhat egy programozott megközelítést, amint azt a [Példa: Az AdventureWorks Inventory adatbázisának modellezése az Azure Cognitive Search számára.](search-example-adventureworks-modeling.md)

 ## <a name="use-a-complex-collection-for-the-many-side-of-a-one-to-many-relationship"></a>Összetett gyűjtemény használata egy-a-többhöz kapcsolat "több" oldalához

Az Azure Cognitive Search oldalán hozzon létre egy indexsémát, amely az egy-a-többhöz kapcsolatot a beágyazott JSON használatával modellezi. Az előző szakaszban létrehozott eredményhalmaz általában megfelel az alábbi indexsémának (néhány mezőt röviden vágunk ki).

A következő példa hasonló az [Összetett adattípusok modellezése](search-howto-complex-data-types.md#creating-complex-fields)című példához. A *Szobák* struktúra, amely a cikk középpontjában állt, egy *hotel*nevű index mezőgyűjteményében található. Ez a példa a *Cím*összetett típusát is megjeleníti, amely abban különbözik a *Szobáktól,* hogy rögzített elemhalmazból áll, szemben a gyűjteményben engedélyezett elemek többszörös, tetszőleges számával.

```json
{
  "name": "hotels",
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
    { "name": "Description_fr", "type": "Edm.String", "searchable": true, "analyzer": "fr.lucene" },
    { "name": "Category", "type": "Edm.String", "searchable": true, "filterable": false },
    { "name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "facetable": true },
    { "name": "Address", "type": "Edm.ComplexType",
      "fields": [
        { "name": "StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true },
        { "name": "City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
        { "name": "StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true }
      ]
    },
    { "name": "Rooms", "type": "Collection(Edm.ComplexType)",
      "fields": [
        { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
        { "name": "Description_fr", "type": "Edm.String", "searchable": true, "analyzer": "fr.lucene" },
        { "name": "Type", "type": "Edm.String", "searchable": true },
        { "name": "BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true },
        { "name": "BedOptions", "type": "Edm.String", "searchable": true, "filterable": true, "facetable": true },
        { "name": "SleepsCount", "type": "Edm.Int32", "filterable": true, "facetable": true },
        { "name": "SmokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true },
        { "name": "Tags", "type": "Edm.Collection", "searchable": true }
      ]
    }
  ]
}
```

Az előző eredményhalmaz és a fenti indexséma alapján rendelkezik a sikeres indexelési művelethez szükséges összes összetevővel. Az összeolvasztott adatkészlet megfelel az indexelési követelményeknek, mégis megőrzi a részletes információkat. Az Azure Cognitive Search indexben a keresési eredmények könnyen hotelalapú entitásokká esnek, miközben megőrzik az egyes szobák környezetét és azok attribútumait.

## <a name="next-steps"></a>További lépések

Saját adatkészlethasználatával az [Adatok importálása varázslóval](search-import-data-portal.md) hozhatja létre és töltheti be az indexet. A varázsló észleli a beágyazott JSON-gyűjteményt, például a *Rooms*gyűjteményben található gyűjteményt, és egy összetett típusgyűjteményt tartalmazó indexsémát következtet ki. 

  ![Az Adatok importálása varázsló által kikövetkeztetett index](media/index-sql-relational-data/search-index-rooms-complex-collection.png "Az Adatok importálása varázsló által kikövetkeztetett index")

Az alábbi rövid útmutatóval ismerkedjen meg az Adatok importálása varázsló alapvető lépéseivel.

> [!div class="nextstepaction"]
> [Rövid útmutató: Keresési index létrehozása az Azure Portal használatával](search-get-started-portal.md)