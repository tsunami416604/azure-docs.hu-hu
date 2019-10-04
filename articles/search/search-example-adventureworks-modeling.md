---
title: 'Példa: A AdventureWorks-leltári adatbázis modellezése – Azure Search'
description: Megtudhatja, hogyan modellezheti a kapcsolatokat, és hogyan alakíthatja át azt egy összevont adatkészletbe az indexeléshez és a teljes szöveges kereséshez Azure Searchban.
author: HeidiSteen
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: heidist
ms.openlocfilehash: c25dd34460e7e92bb20913f5b812044623dd38e3
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70274037"
---
# <a name="example-model-the-adventureworks-inventory-database-for-azure-search"></a>Példa: A Azure Search AdventureWorks-leltározási adatbázisának modellezése

A Azure Search az [indexelési (adatfeldolgozási) folyamat](search-what-is-an-index.md)bemenetként fogadja az összeolvasztott sorhalmazt. Ha a forrásadatok egy SQL Server viszonyítási adatbázisból származnak, ez a cikk azt mutatja be, hogyan hozható létre egy összevont sorhalmaz az indexelés előtt, az AdventureWorks példaként használva.

## <a name="about-adventureworks"></a>A AdventureWorks névjegye

Ha SQL Server példánya van, akkor lehet, hogy ismeri a [AdventureWorks-minta adatbázisát](https://docs.microsoft.com/sql/samples/adventureworks-install-configure?view=sql-server-2017). Az adatbázis táblái között öt tábla látható, amelyek a termékre vonatkozó információkat tartalmaznak.

+ **ProductModel**: név
+ **Termék**: név, szín, Ár, méret, súly, rendszerkép, kategória (minden sor egy adott ProductModel csatlakozik)
+ **ProductDescription**: Leírás
+ **ProductModelProductDescription**: területi beállítás (minden sor egy adott nyelvhez csatlakozik egy ProductModel egy adott ProductDescription)
+ **ProductCategory**: név, szülő kategória

Ennek a példának a célja, hogy az összes adatot egy összevont sorhalmazba kombinálja, amely a keresési indexbe betölthető. 

## <a name="considering-our-options"></a>A lehetőségek figyelembevétele

A naiv megközelítés a Product tábla összes sorát indexeli (ha szükséges), mivel a termék táblája a legpontosabb információkkal rendelkezik. Azonban ez a megközelítés kiteszi a keresési indexet, hogy észlelje az ismétlődéseket egy eredményhalmazt. Például a Road-650 modell két színben és hat méretben érhető el. Ezt követően a "Road Bikes" lekérdezését egy adott modell tizenkét példánya uralja, amely csak méret és szín alapján megkülönböztethető. A másik hat út-specifikus modellt mind az Alvilágban, mind a Search (a második oldalon).

  ![Termékek listája](./media/search-example-adventureworks/products-list.png "Termékek listája")
 
Figyelje meg, hogy a Road-650 modell tizenkét lehetőséggel rendelkezik. Az egy-a-többhöz típusú entitások sorait a rendszer a keresési indexben szereplő, előre összevont érték mezőkként jelöli meg legjobban.

A probléma megoldása nem annyira egyszerű, hogy a cél indexet a ProductModel táblába helyezze át. Ez figyelmen kívül hagyja a Product táblában szereplő fontos információkat, amelyeket továbbra is a keresési eredmények között kell megjeleníteni.

## <a name="use-a-collection-data-type"></a>Gyűjtemény adattípusának használata

A "helyes megközelítés" olyan keresési séma funkció használata, amely nem rendelkezik közvetlen párhuzamosan az adatbázis-modellben: **Collection(Edm.String)** . Ez a szerkezet a Azure Search index sémában van definiálva. A gyűjtemény adattípusa akkor használatos, ha egy nagyon hosszú (szimpla) karakterlánc helyett az egyes karakterláncok listáját kell képviselnie. Ha címkéket vagy kulcsszavakat tartalmaz, a mezőhöz gyűjtemény adattípust kell használnia.

A többértékű index mezők **(EDM. String)** a "Color", a "size" és a "képhez" tulajdonság megadásával a kiegészítő információk megmaradnak az elemzéshez és a szűréshez, az index ismétlődő bejegyzésekkel való szennyezése nélkül. Ehhez hasonlóan alkalmazzon összesítő függvényeket a numerikus termékek mezőire, az indexelési **minListPrice** az egyes termékek **listPrice**helyett.

Az ilyen struktúrákkal rendelkező indexek esetében a "Mountain Bikes" kifejezés a különálló kerékpár-modelleket jeleníti meg, miközben a fontos metaadatokat, például a színt, a méretet és a legalacsonyabb árat őrzi meg. Az alábbi képernyőfelvétel egy illusztrációt tartalmaz.

  ![Példa Mountain Bike-keresésre](./media/search-example-adventureworks/mountain-bikes-visual.png "Példa Mountain Bike-keresésre")

## <a name="use-script-for-data-manipulation"></a>Az adatkezeléshez használt parancsfájl használata

Sajnos az ilyen típusú modellezés nem érhető el egyszerűen csak SQL-utasításokkal. Ehelyett használjon egy egyszerű NodeJS-szkriptet az adattöltéshez, majd leképezheti a kereshető JSON-entitásokra.

A végső adatbázis – Keresés leképezése így néz ki:

+ Model (EDM. string: kereshető, szűrhető, lekérhető) a következő helyről: "ProductModel.Name"
+ description_en (EDM. string: kereshető) a "ProductDescription" a modellhez, ahol a Culture = "en"
+ Color (gyűjtemény (EDM. String): kereshető, szűrhető, megkereshető, lekérhető: a modell egyedi értékei a "Product. Color" típustól
+ size (gyűjtemény (EDM. String): kereshető, szűrhető, méretezhető, lekérhető: a modellhez tartozó "termék. size" egyedi értékek
+ rendszerkép (gyűjtemény (EDM. String): lekérhető): a modell "Product. ThumbnailPhoto" egyedi értékei
+ minStandardCost (EDM. Double: szűrhető, sokoldalú, rendezhető, lekérhető): a modell összes "Product. StandardCost" összesítésének minimuma
+ minListPrice (EDM. Double: szűrhető, sokoldalú, rendezhető, lekérhető): a modell összes "Product. ListPrice" összesítésének minimuma
+ minWeight (EDM. Double: szűrhető, sokoldalú, rendezhető, lekérhető): a modell összes "Product. Weight" összesítésének minimuma
+ termékek (gyűjtemény (EDM. String): kereshető, szűrhető, lekérhető): a modell "Product.Name" egyedi értékei

Miután csatlakozott a ProductModel táblához a termékkel, és ProductDescription, használja a [lodash](https://lodash.com/) ( C#vagy LINQ in) a eredményhalmazt gyors átalakításához:

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

Az eredményül kapott JSON a következőképpen néz ki:

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

Végezetül itt az SQL-lekérdezés, amely visszaküldi a kezdeti rekordhalmazt. Az [MSSQL](https://www.npmjs.com/package/mssql) NPM modul használatával betöltöttem az adataimat a NodeJS alkalmazásba.

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
> [Példa: Többszintű dimenziók besorolása Azure Search](search-example-adventureworks-multilevel-faceting.md)