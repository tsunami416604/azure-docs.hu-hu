---
title: 'Példa: Az AdventureWorks Inventory adatbázis ának modellezése'
titleSuffix: Azure Cognitive Search
description: Ismerje meg, hogyan modellezze a relációs adatokat, és alakítsa át őket összeolvasztott adatkészletté, indexeléshez és teljes szöveges kereséshez az Azure Cognitive Search-ben.
author: HeidiSteen
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: heidist
ms.openlocfilehash: edb6162724938962df8a7340afea6e930a0b1049
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72792991"
---
# <a name="example-model-the-adventureworks-inventory-database-for-azure-cognitive-search"></a>Példa: Az AdventureWorks Inventory adatbázisának modellezése az Azure Cognitive Search szolgáltatáshoz

Az Azure Cognitive Search egy összeolvasztott sorhalmazt fogad el az [indexelési (adatbetöltési) folyamat bemeneteként.](search-what-is-an-index.md) Ha a forrásadatok SQL Server relációs adatbázisból származnak, ez a cikk bemutatja az egyik módszert az összeolvasztott sorhalmaz indexelés előtt történő létrehozására, példaként az AdventureWorks mintaadatbázis használatával.

## <a name="about-adventureworks"></a>Az AdventureWorks-ről

Ha SQL Server-példánya van, lehet, hogy ismeri az [AdventureWorks mintaadatbázist.](https://docs.microsoft.com/sql/samples/adventureworks-install-configure?view=sql-server-2017) Az adatbázisban található táblák között öt tábla található, amelyek termékinformációkat tárnak fel.

+ **ProductModel**: név
+ **Termék**: név, szín, költség, méret, súly, kép, kategória (minden sor egy adott ProductModel modellhez csatlakozik)
+ **ProductDescription**: leírás
+ **ProductModelProductDescription**: területi beállítás (minden sor egy productmodelt egy adott nyelvhez egy adott termékleíráshoz csatlakozik)
+ **ProductCategory**: név, szülőkategória

Az összes ilyen adat kombinálása egy összeolvasztott sorhalmaz, amely beszámítható egy keresési index a példa célja. 

## <a name="considering-our-options"></a>Figyelembe véve a lehetőségeinket

A naiv megközelítés az lenne, hogy indexeli a Termék tábla összes sorát (adott esetben összekapcsolva), mivel a Termék tábla rendelkezik a legkonkrétabb információkkal. Ez a megközelítés azonban a keresési indexet egy eredménykészletben észlelt ismétlődések nek tenné ki. A Road-650 modell például két színben és hat méretben érhető el. A "közúti kerékpárok" lekérdezését ezután ugyanannak a modellnek a tizenkét példánya uralja, amelyek csak a méret és a szín szerint különböztethetők meg. A másik hat út-specifikus modell mind kiszorulna az alsó világ keresés: a második oldalon.

  ![Termékek listája](./media/search-example-adventureworks/products-list.png "Termékek listája")
 
Figyelje meg, hogy a Road-650 modell tizenkét lehetőség közül választhat. Az egy-a-többhöz entitássorok leginkább többértékű mezőkként vagy előre összesített értékmezőkként jelennek meg a keresési indexben.

A probléma megoldása nem olyan egyszerű, mint a célindex áthelyezése a ProductModel táblába. Ezzel figyelmen kívül hagyná a fontos adatokat a Termék táblában, amelyektovábbra is megjelennek a keresési eredményekközött.

## <a name="use-a-collection-data-type"></a>Gyűjtemény adattípus használata

A "helyes megközelítés" egy olyan keresési séma-szolgáltatás, amely nem rendelkezik közvetlen párhuzamos stafétával az adatbázismodellben: **Gyűjtemény(Edm.String)**. Ez a konstrukció az Azure Cognitive Search indexsémája határozza meg. A Gyűjtemény adattípusát akkor használja a rendszer, ha az egyes karakterláncok listáját kell képviselnie, nem pedig egy nagyon hosszú (egy- ) karakterláncot. Ha vannak címkéi vagy kulcsszavai, ehhez a mezőhöz gyűjtemény adattípust kell használnia.

A **Gyűjtemény (Edm.String)** többértékű indexmezőinek "szín", "méret" és "kép" esetében történő meghatározásával a kiegészítő információk megmaradnak a dimenziók kifejezésre és szűréséhez anélkül, hogy az indexet ismétlődő bejegyzésekkel szennyezné. Hasonlóképpen alkalmazza az összesítő függvényeket a numerikus termékmezőkre, és minden egyes terméklista helyett **a minListPrice** értéket indexelje. **listPrice**

Adott egy index ezeket a struktúrákat, a keresés a "mountain bikes" azt mutatják, diszkrét kerékpár modellek, miközben fontos metaadatok, mint a szín, méret, és a legalacsonyabb ár. Az alábbi képernyőkép illusztrációt tartalmaz.

  ![Hegyikerékpáros keresés példa](./media/search-example-adventureworks/mountain-bikes-visual.png "Hegyikerékpáros keresés példa")

## <a name="use-script-for-data-manipulation"></a>Parancsfájl használata adatkezeléshez

Sajnos az ilyen típusú modellezés nem érhető el könnyen SQL utasítások egyedül. Ehelyett egy egyszerű NodeJS-parancsfájl segítségével töltse be az adatokat, majd rendelje hozzá keresésbarát JSON-entitásokba.

A végső adatbázis-keresés leképezés így néz ki:

+ modell (Edm.String: kereshető, szűrhető, visszakereshető) a "ProductModel.Name"
+ description_en (Edm.String: kereshető) a "ProductDescription" a modell, ahol culture='en'
+ szín (Gyűjtemény(Edm.String): kereshető, szűrhető, facetable, visszakereshető): egyedi értékeket "Product.Color" a modell
+ méret (Gyűjtemény(Edm.String): kereshető, szűrhető, facetable, visszakereshető): egyedi értékek a "Product.Size" a modell
+ image (Collection(Edm.String): visszakereshető): egyedi értékek a modell "Product.ThumbnailPhoto" tulajdonságából
+ minStandardCost (Edm.Double: szűrhető, facetable, rendezhető, visszakereshető): összesített minimum az összes "Product.StandardCost" a modell
+ minListPrice (Edm.Double: szűrhető, facetable, rendezhető, visszakereshető): összesített minimum az összes "Product.ListPrice" a modell
+ minWeight (Edm.Double: szűrhető, facetable, rendezhető, visszakereshető): összesített minimum az összes "Product.Weight" a modell
+ termékek (Collection(Edm.String): kereshető, szűrhető, visszakereshető): egyedi értékek a modell "Product.Name" részéről

Miután csatlakozott a ProductModel táblához a Product és a ProductDescription alkalmazással, a [lodash](https://lodash.com/) (vagy a Linq c#) használatával gyorsan átalakíthatja az eredményértéket:

```javascript
var records = queryYourDatabase();
var models = _(records)
  .groupBy('ModelName')
  .values()
  .map(function(d) {
    return {
      model: _.first(d).ModelName,
      description: _.first(d).Description,
      colors: _(d).pluck('Color').uniq().compact().value(),
      products: _(d).pluck('ProductName').uniq().compact().value(),
      sizes: _(d).pluck('Size').uniq().compact().value(),
      images: _(d).pluck('ThumbnailPhotoFilename').uniq().compact().value(),
      minStandardCost: _(d).pluck('StandardCost').min(),
      maxStandardCost: _(d).pluck('StandardCost').max(),
      minListPrice: _(d).pluck('ListPrice').min(),
      maxListPrice: _(d).pluck('ListPrice').max(),
      minWeight: _(d).pluck('Weight').min(),
      maxWeight: _(d).pluck('Weight').max(),
    };
  })
  .value();
```

Az így kapott JSON így néz ki:

```json
[
  {
    "model": "HL Road Frame",
    "colors": [
      "Black",
      "Red"
    ],
    "products": [
      "HL Road Frame - Black, 58",
      "HL Road Frame - Red, 58",
      "HL Road Frame - Red, 62",
      "HL Road Frame - Red, 44",
      "HL Road Frame - Red, 48",
      "HL Road Frame - Red, 52",
      "HL Road Frame - Red, 56",
      "HL Road Frame - Black, 62",
      "HL Road Frame - Black, 44",
      "HL Road Frame - Black, 48",
      "HL Road Frame - Black, 52"
    ],
    "sizes": [
      "58",
      "62",
      "44",
      "48",
      "52",
      "56"
    ],
    "images": [
      "no_image_available_small.gif"
    ],
    "minStandardCost": 868.6342,
    "maxStandardCost": 1059.31,
    "minListPrice": 1431.5,
    "maxListPrice": 1431.5,
    "minWeight": 961.61,
    "maxWeight": 1043.26
  }
]
```

Végül, itt van az SQL-lekérdezés ta- vissza a kezdeti recordset. Az [mssql](https://www.npmjs.com/package/mssql) npm modult használtam az adatok betöltéséhez a NodeJS alkalmazásba.

```T-SQL
SELECT
  m.Name as ModelName,
  d.Description,
  p.Name as ProductName,
  p.*
FROM 
  SalesLT.ProductModel m
INNER JOIN 
  SalesLT.ProductModelProductDescription md
  ON m.ProductModelId = md.ProductModelId
INNER JOIN 
  SalesLT.ProductDescription d
  ON md.ProductDescriptionId = d.ProductDescriptionId
LEFT JOIN 
  SalesLT.product p
  ON m.ProductModelId = p.ProductModelId
WHERE
  md.Culture='en'
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Példa: Többszintű dimenziós taxonómiák az Azure Cognitive Search szolgáltatásban](search-example-adventureworks-multilevel-faceting.md)