---
title: 'Példa: Az AdventureWorks készlet adatbázis – Azure Search modell'
description: Útmutató a relációs adatok, átalakítja őket egy egybesimított adatkészlet, az indexelés és a teljes szöveges keresés az Azure Search-be.
author: cstone
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: chstone
ms.openlocfilehash: 6d5d01dfbbcfda56818f5c38b06117a87e021445
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55174567"
---
# <a name="example-model-the-adventureworks-inventory-database-for-azure-search"></a>Példa: Az Azure Search az AdventureWorks készlet adatbázis minta

Modellezési strukturált adatbázis hatékony keresési indexbe tartalom ritkán egy könnyen érthető megjegyzésblokkok írására. gyakorlat. Ütemezés és a változáskezelés elkülönített, létezik az igazi kihívást a forrásadatok tábla tartományhoz csatlakoztatott állapotuk Forrássorok denormalizálni az keresési mobilbarát entitásokkal. Ebben a cikkben az AdventureWorks mintaadatokat, az elérhető online, jelölje ki az áttérés adatbázisából kereséséhez közös élményt. 

## <a name="about-adventureworks"></a>– Az AdventureWorks kapcsolatban

Ha egy SQL Server-példányt, esetleg ismerős t az AdventureWorks mintaadatbázissal. A táblák az adatbázisban szereplő közé tartoznak a termékinformációk teszi közzé öt táblákhoz.

+ **ProductModel**: név
+ **A termék**: név, szín, költség, méret, súly, kép, kategória (minden sor egy adott ProductModel csatlakozik)
+ **ProductDescription**: leírás
+ **ProductModelProductDescription**: területi beállítás (minden sor csatlakoztatja egy ProductModel egy adott ProductDescription egy adott nyelven)
+ **ProductCategory**: név, a szülő kategória

Összefűzhet a létrejövő adatok olvasódnak be a keresési index egybesimított sorhalmaz a feladattól. 

## <a name="considering-our-options"></a>A mérlegeli beállításai

A natív megközelítés lenne óta a Product tábla összes sort a Product táblából (adott esetben csatlakozik) indexelése a legtöbb adott információval rendelkezik. Azonban ezt a megközelítést vezet a keresési index észlelt ismétlődések az adott eredményül. Például a közúti – 650 modell két színek és hat méretek érhető el. A lekérdezés a "közúti kerékpárok" majd lenne uralja ugyanannak a modellnek, csak a méretét és színét differenciált tizenkét példányok. A hat közúti-specifikus modelleket szeretne minden kell relegated keresés elsajátították a nether tételéhez: két lapon.

  ![Termékek listájának](./media/search-example-adventureworks/products-list.png "termékek listája")
 
Figyelje meg, hogy rendelkezik-e a közúti – 650 modell tizenkét beállítások. -A-többhöz entitás sorok legjobb jelentésekként jelennek meg több értéket vagy előtti aggregated érték mező a keresési indexben.

A probléma megoldása nem mozdítani az célindex ProductModel táblához. Ezzel a fontos adatokat a Product tábla még mindig finomítja a keresési eredmények között lenne figyelmen kívül.

## <a name="use-a-collection-data-type"></a>Az adatgyűjtési adattípusa

A "megfelelő módszer" egy keresési-séma-szolgáltatás, amely nem rendelkezik közvetlen párhuzamos az adatbázis-modell használatához: **Collection(Edm.String)**. Egy gyűjtemény típusa használatos, ha önálló karakterláncokra, nem pedig egy nagyon hosszú listáját (szimpla) karakterlánc. Ha a címkék vagy a kulcsszavak, használja a mező egy gyűjtemény típusa.

Többértékű index mezőinek definiálásával **Collection(Edm.String)** "szín", "méretben" és "image", a kiegészítő információkat nem megőrzött, a többnyelvűséget és a szűrési szennyező az ismétlődő bejegyzéseket az index. Ehhez hasonlóan az aggregátumfüggvények alkalmazni a numerikus termék mezők, az indexelés **minListPrice** helyett minden egyetlen termék **listPrice**.

Adja meg az ezen szerkezetek index, a keresést "mountain Bike-OK" jelenik meg különálló kerékpár modellek, fontos metaadatokat, például színét, méretét és a legalacsonyabb ár megőrzése. Az alábbi képernyőképen illusztrálja.

  ![Mountain bike keresési példa](./media/search-example-adventureworks/mountain-bikes-visual.png "Mountain bike keresési példa")

## <a name="use-script-for-data-manipulation"></a>Az adatkezelés parancsfájl használata

Sajnos az ilyen típusú modellezési nem könnyen elérhető önálló SQL-utasítások segítségével. Ehelyett egyszerű NodeJS parancsfájl használata az adatok betöltéséhez és képezze keresési mobilbarát JSON entitásokkal.

A végső adatbázis-keresés hozzárendelés a következőhöz hasonló:

+ modell (Edm.String: kereshető, szűrhető, lekérdezhető) a "ProductModel.Name"
+ description_en (Edm.String: kereshető) a "ProductDescription" modell, culture = "en"
+ szín (Collection(Edm.String): lekérhető kereshető, szűrhető, kategorizálható): a modell "Product.Color" egyéni értékeivel
+ méret (Collection(Edm.String): lekérhető kereshető, szűrhető, kategorizálható): a modell "Product.Size" egyéni értékeivel
+ kép (Collection(Edm.String): lekérhető): a modell "Product.ThumbnailPhoto" egyéni értékeivel
+ minStandardCost (Edm.Double: szűrhető, kategorizálható, rendezhető, lekérdezhető): az összes "Product.StandardCost" modell összesített minimuma
+ minListPrice (Edm.Double: szűrhető, kategorizálható, rendezhető, lekérdezhető): az összes "Product.ListPrice" modell összesített minimuma
+ minWeight (Edm.Double: szűrhető, kategorizálható, rendezhető, lekérdezhető): az összes "Product.Weight" modell összesített minimuma
+ termékek (Collection(Edm.String): kereshető, szűrhető, lekérdezhető): a modell "Product.Name" egyéni értékeivel

A ProductModel tábla termék és ProductDescription, használja a csatlakozás után [lodash](https://lodash.com/) (vagy a Linq C#) eredményhalmaza gyors átalakításához:

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

Az eredményül kapott JSON így néz ki:

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

Végül Íme az SQL-lekérdezést a kezdeti rekordhalmaz vissza. Használtam a [mssql](https://www.npmjs.com/package/mssql) az adatok betöltése a NodeJS-alkalmazásokban az npm modult.

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
> [Példa: Az Azure Search több szintű értékkorlátozás besorolások](search-example-adventureworks-multilevel-faceting.md)


