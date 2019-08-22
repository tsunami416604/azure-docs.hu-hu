---
title: 'Példa: Többszintű dimenziók – Azure Search'
description: Ismerje meg, hogyan hozhat létre sokrétű struktúrákat többszintű besorolásokhoz, és hogyan hozhat létre egy beágyazott navigációs struktúrát, amelyet az alkalmazás lapjain is tartalmazhat.
author: cstone
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: chstone
ms.openlocfilehash: 5a6fda0157f0f3a4ca5861acd4bcbead7839e451
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69649939"
---
# <a name="example-multi-level-facets-in-azure-search"></a>Példa: Többszintű aspektusok az Azure Searchben

Azure Search sémák nem támogatják explicit módon a többszintű besorolási kategóriákat, de a tartalom indexelés előtti módosításával, majd az eredményekre vonatkozó speciális kezelés alkalmazásával közelítheti meg őket. 

## <a name="start-with-the-data"></a>Az adatkezelés megkezdése

A cikkben szereplő példa egy korábbi példára épül, amely a [AdventureWorks-leltári adatbázis modellezésével](search-example-adventureworks-modeling.md)mutatja be Azure Search többszintű aspektusait.

A AdventureWorks egyszerű, kétszintű besorolást tartalmaz egy szülő-gyermek kapcsolattal. A struktúra rögzített hosszúságú taxonómiai mélysége esetén egy egyszerű SQL JOIN-lekérdezést használhat a taxonómia csoportosításához:

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

## <a name="indexing-to-a-collection-field"></a>Indexelés egy gyűjtemény mezőjébe

A struktúrát tartalmazó indexben hozzon létre egy **gyűjteményt (EDM. String)** a Azure Search sémában az adatok tárolásához, és győződjön meg arról, hogy a mező attribútumai között kereshető, szűrhető, és lekérhető.

Most, amikor egy adott besorolási kategóriára hivatkozó tartalmat indexel, elküldheti a besorolást egy olyan tömbként, amely a taxonómia egyes szintjeiből szöveget tartalmaz. Például egy olyan entitás esetében, amely `ProductCategoryId = 5 (Mountain Bikes)`a esetében beküldi a mezőt`[ "Bikes", "Bikes|Mountain Bikes"]`

Figyelje meg, hogy a "bikes" szülő kategóriába belefoglalt "Mountain Bikes" érték szerepel a gyermek kategóriában. Minden alkategóriának a gyökér elemhez viszonyított teljes elérési útját kell beágyaznia. A cső karakter elválasztója nem megfelelő, de konzisztensnek kell lennie, és nem szerepelhet a forrás szövegében. A rendszer az alkalmazás kódjában az elválasztó karaktert fogja használni a taxonómia fájának a dimenziós eredményekből való újraépítéséhez.

## <a name="construct-the-query"></a>A lekérdezés kiépítése

A lekérdezések kiállításakor adja meg a következő aspektusi meghatározást (ahol a taxonómia a saját aspektusú besorolási mező):`facet = taxonomy,count:50,sort:value`

A darabszám értékének elég magasnak kell lennie ahhoz, hogy visszaállítsa az összes lehetséges besorolási értéket. A AdventureWorks adatok 41 különböző besorolási értékeket tartalmaznak, ezért `count:50` elegendő.

  ![Sokoldalú szűrő](./media/search-example-adventureworks/facet-filter.png "Sokoldalú szűrő")

## <a name="build-the-structure-in-client-code"></a>A struktúra létrehozása az ügyfél kódjában

Az ügyfélalkalmazás kódjában állítsa össze a taxonómia faszerkezetét úgy, hogy az egyes dimenziók értékét a cső karakterére bontja.

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

A **Categories (kategóriák** ) objektum mostantól lehetővé teszi egy összecsukható taxonómia-fa pontos számlálását:

  ![többszintű szűrő](./media/search-example-adventureworks/multi-level-facet.png "többszintű szűrő")

 
A fában lévő összes hivatkozásnak alkalmaznia kell a kapcsolódó szűrőt. Példa:

+ **Taxonómia/** `(x:x eq 'Accessories')` a kellékek ág összes dokumentumának visszaadása
+ **Taxonómia/minden olyan** `(x:x eq 'Accessories|Bike Racks')` dokumentumot ad vissza, amely a tartozékok ág alatti Bike-állványok alkategóriája.

Ez a módszer az összetettebb forgatókönyvek, például a mélyebb besorolású fák és a különböző szülő-kategóriákba tartozó duplikált alkategóriák (például `Bike Components|Forks` : és `Camping Equipment|Forks`) esetén is méretezhető.

> [!TIP]
> A lekérdezési sebességet a visszaadott dimenziók száma érinti. A nagyon nagy taxonómiai készletek támogatásához érdemes lehet egy sokrétű **EDM. String** mezőt hozzáadni az egyes dokumentumok legfelső szintű besorolási értékének tárolásához. Ezt követően alkalmazza ugyanezt a technikát, de csak a gyűjtemény-dimenziós lekérdezést hajtsa végre (szűrve a gyökérszintű taxonómia mezőben), amikor a felhasználó kibont egy legfelső szintű csomópontot. Ha azonban a 100%-os visszahívás nem szükséges, egyszerűen csökkentse a dimenziók számát egy ésszerű számra, és győződjön meg arról, hogy a dimenziók bejegyzéseinek száma szám szerint rendezve történik.

## <a name="see-also"></a>Lásd még

[Példa: A Azure Search AdventureWorks-leltározási adatbázisának modellezése](search-example-adventureworks-modeling.md)