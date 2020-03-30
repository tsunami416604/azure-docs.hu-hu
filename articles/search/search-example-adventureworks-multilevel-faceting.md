---
title: 'Példa: Többszintű dimenziók'
titleSuffix: Azure Cognitive Search
description: Ismerje meg, hogyan hozhat létre dimenzióstruktúrát a többszintű taxonómiákhoz, és hozzon létre egy beágyazott navigációs struktúrát, amelyet az alkalmazáslapokon is felvehet.
author: HeidiSteen
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.openlocfilehash: 8672fa0911d1a031205bb3340fa0c03ab9492a28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72792943"
---
# <a name="example-multi-level-facets-in-azure-cognitive-search"></a>Példa: Többszintű dimenziók az Azure Cognitive Search-ben

Az Azure Cognitive Search sémák nem kifejezetten támogatják a többszintű taxonómiai kategóriákat, de közelítheti őket a tartalom indexelés előtti kezelésével, majd az eredmények speciális kezelésének alkalmazásával. 

## <a name="start-with-the-data"></a>Kezdje az adatokkal

Ebben a cikkben szereplő példa egy korábbi példára, [az AdventureWorks Inventory-adatbázis modellezésére](search-example-adventureworks-modeling.md)épül, amely bemutatja a többszintű dimenziókat az Azure Cognitive Search-ben.

Az AdventureWorks egy egyszerű, kétszintű taxonómiával rendelkezik, szülő-gyermek kapcsolattal. A struktúra rögzített hosszúságú taxonómiai mélységeiben egy egyszerű SQL illesztési lekérdezés használható a rendszerbeálló csoportosítására:

```T-SQL
SELECT 
  parent.Name Parent, category.Name Category, category.ProductCategoryId
FROM 
  SalesLT.ProductCategory category
LEFT JOIN
  SalesLT.ProductCategory parent
  ON category.ParentProductCategoryId=parent.ProductCategoryId
```

  ![Lekérdezés eredményei](./media/search-example-adventureworks/prod-query-results.png "Lekérdezés eredményei")

## <a name="indexing-to-a-collection-field"></a>Indexelés gyűjteménymezőbe

Az index et tartalmazó struktúrában hozzon létre egy **Collection(Edm.String)** mezőt az Azure Cognitive Search sémában az adatok tárolásához, és győződjön meg arról, hogy a mezőattribútumok tartalmaznak kereshető, szűrhető, facetable és visszakereshető.

Most, amikor indexelése tartalom, amely hivatkozik egy adott taxonómiai kategóriában, küldje el a taxonómia, mint egy tömb, amely szöveget minden szintjén a taxonómia. Például egy olyan `ProductCategoryId = 5 (Mountain Bikes)`entitás esetében, amelynek esetében a mezőt`[ "Bikes", "Bikes|Mountain Bikes"]`

Figyeljük meg a felvételét a szülő kategóriában "Bikes" a gyermek kategóriában érték "Mountain Bikes". Minden alkategóriának be kell ágyaznia a teljes elérési útját a gyökérelemhez képest. A csőkarakter-elválasztó tetszőleges, de konzisztensnek kell lennie, és nem jelenhet meg a forrásszövegben. Az elválasztó karakter t használja az alkalmazáskódban a taxonómiai fa facet eredményekből való rekonstruálására.

## <a name="construct-the-query"></a>A lekérdezés összeállítása

A lekérdezések kiadásakor adja meg a következő mezőspecifikációt (ahol a taxonómia a facetable taxonómia mező):`facet = taxonomy,count:50,sort:value`

A darabszám értékének elég magasnak kell lennie ahhoz, hogy az összes lehetséges rendszerszámértéket visszaadja. Az AdventureWorks-adatok 41 különböző rendszerértéket `count:50` tartalmaznak, így elegendőek.

  ![Felületes szűrő](./media/search-example-adventureworks/facet-filter.png "Felületes szűrő")

## <a name="build-the-structure-in-client-code"></a>A struktúra létrehozása az ügyfélkódban

Az ügyfélalkalmazás kódjában rekonstruálja a taxonómiai fát a csőkarakter minden egyes értékének felosztásával.

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

A **kategóriák** objektum most már használható, hogy egy összecsukható taxonómia fa pontos száma:

  ![többszintű felületű szűrő](./media/search-example-adventureworks/multi-level-facet.png "többszintű felületű szűrő")

 
A fa minden hivatkozásának alkalmaznia kell a kapcsolódó szűrőt. Példa:

+ **taxonómia/bármely minden** `(x:x eq 'Accessories')` dokumentum visszavallása a Kellékek ágban
+ **taxonómia/bármely** `(x:x eq 'Accessories|Bike Racks')` bevallás csak a Kerékpártartók alkategóriájával rendelkező dokumentumokat adja vissza a Kellékek ág alatt.

Ez a technika olyan összetettebb forgatókönyveket fog lefedni, `Bike Components|Forks` mint a mélyebb taxonómiai fák `Camping Equipment|Forks`és a különböző szülőkategóriákban előforduló duplikált alkategóriák (például és a ) .

> [!TIP]
> A lekérdezés sebességét befolyásolja a visszaadott adatok száma. A nagyon nagy taxonómiai készletek támogatásához fontolja meg egy facetable **Edm.String** mező hozzáadását az egyes dokumentumok legfelső szintű taxonómiai értékének megtartásához. Ezután alkalmazza ugyanazt a fenti technikát, de csak akkor hajtsa végre a gyűjtemény-facet lekérdezést (a gyökérrendszertani mezőre szűrve), amikor a felhasználó kibont egy legfelső szintű csomópontot. Vagy ha 100%-os visszahívás nem szükséges, egyszerűen csökkentse a facet száma egy ésszerű számot, és győződjön meg arról, hogy a facet bejegyzések szám szerint vannak rendezve.

## <a name="see-also"></a>Lásd még

[Példa: Az AdventureWorks Inventory adatbázisának modellezése az Azure Cognitive Search szolgáltatáshoz](search-example-adventureworks-modeling.md)