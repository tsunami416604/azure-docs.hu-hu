---
title: 'Példa: többszintű dimenziók – Azure Search'
description: Ismerje meg, hogyan hozhat létre sokrétű struktúrákat többszintű besorolásokhoz, és hogyan hozhat létre egy beágyazott navigációs struktúrát, amelyet az alkalmazás lapjain is tartalmazhat.
author: HeidiSteen
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.openlocfilehash: 9a56bba55f9b3a59126168bc2bbbd50927c3fc78
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/21/2019
ms.locfileid: "70274085"
---
# <a name="example-multi-level-facets-in-azure-search"></a>Példa: többszintű aspektusok a Azure Searchban

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

Most, amikor egy adott besorolási kategóriára hivatkozó tartalmat indexel, elküldheti a besorolást egy olyan tömbként, amely a taxonómia egyes szintjeiből szöveget tartalmaz. Egy `ProductCategoryId = 5 (Mountain Bikes)` rendelkező entitás esetében például küldje el a mezőt `[ "Bikes", "Bikes|Mountain Bikes"]`

Figyelje meg, hogy a "bikes" szülő kategóriába belefoglalt "Mountain Bikes" érték szerepel a gyermek kategóriában. Minden alkategóriának a gyökér elemhez viszonyított teljes elérési útját kell beágyaznia. A cső karakter elválasztója nem megfelelő, de konzisztensnek kell lennie, és nem szerepelhet a forrás szövegében. A rendszer az alkalmazás kódjában az elválasztó karaktert fogja használni a taxonómia fájának a dimenziós eredményekből való újraépítéséhez.

## <a name="construct-the-query"></a>A lekérdezés kiépítése

A lekérdezések kiállításakor adja meg a következő aspektusi meghatározást (ahol a taxonómia a saját aspektusú besorolási mező): `facet = taxonomy,count:50,sort:value`

A darabszám értékének elég magasnak kell lennie ahhoz, hogy visszaállítsa az összes lehetséges besorolási értéket. A AdventureWorks adatok 41 különböző besorolási értékeket tartalmaznak, így `count:50` elegendő.

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

+ **taxonómia/bármely** `(x:x eq 'Accessories')` a kellékek ágban lévő összes dokumentumot visszaadja
+ a **taxonómia/bármely** `(x:x eq 'Accessories|Bike Racks')` csak azokat a dokumentumokat adja vissza, amelyek a tartozékok ág alatti Bike Rack alkategóriával rendelkeznek.

Ez a módszer az összetettebb forgatókönyvek, például a mélyebb besorolású fák és a különböző szülő-kategóriákba tartozó duplikált alkategóriák (például `Bike Components|Forks` és `Camping Equipment|Forks`) esetében méretezhető.

> [!TIP]
> A lekérdezési sebességet a visszaadott dimenziók száma érinti. A nagyon nagy taxonómiai készletek támogatásához érdemes lehet egy sokrétű **EDM. String** mezőt hozzáadni az egyes dokumentumok legfelső szintű besorolási értékének tárolásához. Ezt követően alkalmazza ugyanezt a technikát, de csak a gyűjtemény-dimenziós lekérdezést hajtsa végre (szűrve a gyökérszintű taxonómia mezőben), amikor a felhasználó kibont egy legfelső szintű csomópontot. Ha azonban a 100%-os visszahívás nem szükséges, egyszerűen csökkentse a dimenziók számát egy ésszerű számra, és győződjön meg arról, hogy a dimenziók bejegyzéseinek száma szám szerint rendezve történik.

## <a name="see-also"></a>Lásd még:

[Példa: a Azure Search AdventureWorks-leltározási adatbázisának modellezése](search-example-adventureworks-modeling.md)