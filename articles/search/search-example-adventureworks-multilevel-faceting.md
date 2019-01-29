---
title: 'Példa: Többszintű aspektusok – Azure Search'
description: Ismerje meg, hogyan hozhat létre több szintű besorolások létrehozását egy beágyazott navigációs szerkezetet, amelyeket megadhat az alkalmazáslapok jellemzőalapú szerkezetek.
author: cstone
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: chstone
ms.openlocfilehash: 7fa17528931be40109d81edac0f15a6a6822ec01
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55194815"
---
# <a name="example-multi-level-facets-in-azure-search"></a>Példa: Többszintű aspektusok az Azure Searchben

Az Azure Search-sémák explicit módon nem támogatják a több szintű besorolás kategóriák, de megbecsülheti az azok által kezelésére szolgáló tartalom indexelése és néhány speciális kezelést majd az eredmények alkalmazása előtt. 

## <a name="start-with-the-data"></a>Az adatokból

A példában ez a cikk az előző példában épül [modellezheti az AdventureWorks készlet adatbázis](search-example-adventureworks-modeling.md), többszintű jellemzőkezelés az Azure Search bemutatása.

– Az AdventureWorks rendelkezik egy egyszerű két szinten besorolást és a egy szülő-gyermek típusú kapcsolat. Rögzített hosszúságú besorolás depths, ez a struktúra a besorolás csoportosításához használható egyszerű SQL illesztési lekérdezés:

```T-SQL
SELECT 
  parent.Name Parent, category.Name Category, category.ProductCategoryId
FROM 
  SalesLT.ProductCategory category
LEFT JOIN
  SalesLT.ProductCategory parent
  ON category.ParentProductCategoryId=parent.ProductCategoryId
```

  ![Lekérdezés eredményei](./media/search-example-adventureworks/prod-query-results.png "lekérdezés eredményei")

## <a name="indexing-to-a-collection-field"></a>Egy gyűjtemény mezőre indexelése

Ez a struktúra tartalmazó az indexben, hozzon létre egy **Collection(Edm.String)** tárolja ezeket az adatokat, megakadályozhatja, hogy a attribútumok mező kereshető, szűrhető, kategorizálható tartalmazzák az Azure Search-séma mezőbe, és lekérhető.

Most tartalom, amelyre hivatkozik egy adott besorolást kategória indexelésekor küldje el a besorolás a besorolást az egyes fenyegetési szöveget tartalmazó tömb. Például, ha az entitás `ProductCategoryId = 5 (Mountain Bikes)`, küldje el a mezőt `[ "Bikes", "Bikes|Mountain Bikes"]`

Figyelje meg, hogy a gyermek kategória "Mountain Bike-OK" érték "Kerékpárok" szülőkategória felvételét. Minden alkategória kell ágyazza be a teljes elérési útvonalát a gyökérelem viszonyítva. A pipe karakterrel elválasztó tetszőleges, de ez konzisztensnek kell lennie, és nem szabad megjelennie a forrás szöveget. Az elválasztó karaktert helyreállítani a besorolás fa értékkorlátozás eredményeket az alkalmazás kódjában lesz használható.

## <a name="construct-the-query"></a>A lekérdezési konstrukció

A lekérdezések kiadásakor a következő dimenzió specifikáció (ahol a besorolás az a kategorizálható osztályozási mezőt) a következők: `facet = taxonomy,count:50,sort:value`

A hibaszám értéke elég nagy az összes lehetséges besorolás értékek visszaadásához kell lennie. Az AdventureWorks adatokat tartalmaznak, 41 eltérő besorolást, így `count:50` elegendő.

  ![Jellemzőalapú szűrő](./media/search-example-adventureworks/facet-filter.png "Jellemzőalapú szűrő")

## <a name="build-the-structure-in-client-code"></a>Az ügyfélkód a struktúra létrehozása

Az ügyfél alkalmazáskódban hozza a besorolás fa minden értékkorlátozás értéke a pipe karakterrel felosztásával.

```javascript
var sum = 0
  , categories = {children:{},length:0}
  , results = getSearchResults();
separator = separator || '|';
field = field || 'taxonomy';
results['@search.facets'][field].forEach(function(d) {
  var node = categories;
  var parts = d.value.split(separator);
  sum += d.count;
  parts.forEach(function(c, i) {
    if (!_(node.children).has(c)) {
      node.children[c] = {};
      node.children[c].count = d.count;
      node.children[c].children = {};
      node.children[c].length = 0;
      node.children[c].filter = parts.slice(0,i+1).join(separator);
      node.length++;
    }
    node = node.children[c];
  });
});
categories.count = sum;
```

A **kategóriák** objektum már használható pontos száma összecsukható besorolás fa megjelenítése:

  ![Többszintű jellemzőalapú szűrő](./media/search-example-adventureworks/multi-level-facet.png "többszintű jellemzőalapú szűrő")

 
Minden hivatkozás, a fanézetben a kapcsolódó szűrőt kell alkalmazni. Példa:

+ **besorolás bármely** `(x:x eq 'Accessories')` összes dokumentumot visszaadja a Kellékek ág
+ **besorolás bármely** `(x:x eq 'Accessories|Bike Racks')` csak egy kerékpárt állványt alkategóriát a Kellékek ág a dokumentumokat ad vissza.

Ez a módszer részletesebb besorolás fák hasonlóan összetettebb forgatókönyveket fednek le lesz skálázva és alkategóriák, a másik szülő kategóriák előforduló ismétlődő (például `Bike Components|Forks` és `Camping Equipment|Forks`).

> [!TIP]
> Lekérdezés sebesség visszaadott értékkorlátozással száma befolyásolja. Támogatja a nagyon nagy méretű besorolás csoportokat, érdemes lehet hozzáadni egy kategorizálható **Edm.String** mező a legfelső szintű besorolás értékét minden egyes dokumentum tárolásához. Ezután alkalmazza a fenti ugyanezen technika, de csak akkor hajtsa végre a gyűjtemény-értékkorlátozása lekérdezés (a gyökérmezőbe besorolás a szűrt) amikor a felhasználó kibővíti a legfelső szintű csomópontja. Vagy ha 100 %-os visszaírási nem szükséges, egyszerűen értékkorlátozás száma csökkentheti a indokolt, és győződjön meg arról, a dimenzió bejegyzések száma szerint vannak rendezve.

## <a name="see-also"></a>Lásd még

[Példa: Az Azure Search az AdventureWorks készlet adatbázis minta](search-example-adventureworks-modeling.md)