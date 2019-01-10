---
title: Az Azure Portalon az Azure Search – az indexek lekérdezése a keresési ablak
description: Az Azure portal-eszközök például a keresési ablak a lekérdezés indexek használata az Azure Search szolgáltatásban. Adja meg a keresési kifejezéseket, vagy teljesen minősített keresőkifejezések speciális szintaxissal.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 2aa372d1f917608de753007cc75ab0d608cafbba
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54188725"
---
# <a name="how-to-use-search-explorer-to-query-indexes-in-azure-search"></a>Az Azure Search lekérdezési indexeket a keresési ablak használatával 

Ez a cikk bemutatja, hogyan kérdezhet le egy meglévő Azure Search-indexeket a **keresési ablak** az Azure Portalon. A keresési ablak használatával nyújt egyszerű vagy teljes Lucene lekérdezési karakterláncokat küldhet bármely meglévő index a szolgáltatásban.

## <a name="start-search-explorer"></a>Indítsa el a keresési ablak

1. Az a [az Azure portal](https://portal.azure.com), nyissa meg a search szolgáltatás oldalát az irányítópultról vagy [keresse meg a szolgáltatást](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) a szolgáltatások listájában.

2. A szolgáltatás áttekintés oldalán kattintson **keresési ablak**.

   ![Keresési ablak parancs portálon](./media/search-explorer/search-explorer-cmd2.png "keresési ablak parancs portálon")

3. Válassza ki az indexet, lekérdezéséhez.

   ![Válassza ki az indexet a lekérdezés](./media/search-explorer/search-explorer-changeindex-se2.png "válassza ki az indexet")

4. Beállíthatja az API-verziót. Alapértelmezés szerint az aktuális általánosan elérhető API-verziót választja, de választhat egy előzetes verziójú vagy régebbi API, ha használni szeretné a szintaxist verzióspecifikus.

5. Miután az index és API-verzió van jelölve, adja meg a keresési kifejezéseket, vagy teljesen minősített lekérdezési kifejezések a keresősávba, és kattintson a **keresési** végrehajtásához.

   ![Írjon be keresési kifejezéseket, és kattintson a Keresés](./media/search-explorer/search-explorer-query-string-example.png "Enter keresési feltételeit, és kattintson a Keresés gombra")

Tippek a kereséshez **keresési ablak**:

+ A rendszer eredményeket ad a részletes JSON-dokumentumok formájában, hogy a dokumentum szerkezeti és a tartalom, megtekintheti az teljes egészében. Lekérdezési kifejezések, mely mezők visszaadott korlátot, a példákban szemléltetett módon használhatja.

+ Dokumentumok épülnek fel, minden mező megjelölve **lekérhető** az indexben. A portálon az indexattribútumok megtekintéséhez, kattintson a *realestate-us-sample* a a **indexek** a keresési áttekintése lapon lévő listából.

+ Szabad formátumú lekérdezéseket, hasonlóan egy kereskedelmi webböngészőben, előfordulhat, hogy meg egy végfelhasználói élmény teszteléséhez hasznos. Például ha a beépített minta ingatlanindex, "Bérbe lake Washington állambeli Seattle" adja meg, és ezután használja a Ctrl-F használati feltételeket a keresési eredmények között található. 

+ Az Azure Search által támogatott szintaxist kell kell csuklós lekérdezési és szűrési kifejezésekben. Az alapértelmezett érték egy [egyszerű szintaxis](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search), de lehetősége van [teljes Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) hatékonyabb lekérdezések. [Szűrés kifejezések](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) egy OData-szűrőszintaxis vannak.

## <a name="basic-search-strings"></a>Alapszintű keresőkifejezések

A következő példák azt feltételezik, hogy a beépített minta ingatlanindex. Ez az index létrehozásához további információkért lásd: [a rövid útmutató: Importálás, index és lekérdezése az Azure Portalon](search-get-started-portal.md).

### <a name="example-1---empty-search"></a>1. példa – üres keresés

Az első meg a tartalmat, hajtsa végre egy üres keresés kattintva **keresési** nincs a megadott feltételeket. Egy üres keresés hasznos első lekérdezésként, mert a teljes dokumentumot adja vissza, hogy áttekintheti, hogy a dokumentum az összeállításban. Az egy üres keresés nem nincs a keresési besorolás és a dokumentumok rendszer tetszőleges sorrendben adja vissza (`"@search.score": 1` tartozó összes dokumentumot). Alapértelmezés szerint 50 dokumentumok egy keresési kérelmet az adott vissza.

Egyenértékű egy üres keresés szintaxisa a következő `*` vagy `search=*`.

   ```Input
   search=*
   ```

   **Results**
   
   ![Üres lekérdezés például](./media/search-explorer/search-explorer-example-empty.png "Unqualified vagy üres lekérdezési példa")

### <a name="example-2---free-text-search"></a>2. példa – szabad szöveges keresés

Szabad formátumú lekérdezéseket, és anélkül operátorok, számos felhasználó által definiált lekérdezések az Azure Search egy egyéni alkalmazás által küldött hasznosak. Figyelje meg, hogy ha Ön adja meg a lekérdezés feltételeket vagy kifejezéseket, keresési besorolás kerül play. Az alábbi példában látható, a szabad szöveges keresés.

   ```Input
   Seattle apartment "Lake Washington" miele OR thermador appliance
   ```

   **Results**

   Az eredmények között keresendő kifejezéseket a lényeges Ctrl-F segítségével.

   ![Szabad szöveges lekérdezési példa](./media/search-explorer/search-explorer-example-freetext.png "szabad szöveges lekérdezési példa")

### <a name="example-3---count-of-matching-documents"></a>Az egyező dokumentumok száma 3 – példa 

Adjon hozzá **$count** beolvasni az indexben található egyezések számát. Egy üres keresés, a számláló értéke az indexben található dokumentumok teljes száma. Szolgáltatást egy minősített akkor a lekérdezés bemenettel dokumentumok számát.

   ```Input1
   $count=true
   ```
   **Results**

   ![Példa a dokumentumok száma](./media/search-explorer/search-explorer-example-count.png "az egyező dokumentumok indexben száma")

### <a name="example-4---restrict-fields-in-search-results"></a>4 – például korlátozhatja a mezőket a keresési eredmények között

Adjon hozzá **$select** szeretné szűkíteni a explicit módon elnevezett mezők olvashatóbb kimeneti **keresési ablak**. A keresési karakterlánc tartani és **$count = true**, argumentumokat előtag **&**. 

   ```Input
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true
   ```

   **Results**

   ![Korlát mezők példa](./media/search-explorer/search-explorer-example-selectfield.png "mezőket a keresési eredmények korlátozása")

### <a name="example-5---return-next-batch-of-results"></a>Az eredmények visszatérési következő köteg 5 – példa

Az Azure Search a keresési besorolás alapján az első 50 egyezést adja vissza. Fűzze hozzá a következő set az egyező dokumentumok lekéréséhez **$top = 100 & $skip = 50** 100 dokumentumok eredményhalmazt növelése érdekében (alapértelmezett érték az 50, a maximális érték 1000), a rendszer kihagyja az első 50 dokumentumokat. Ne felejtse el, hogy meg kell adnia a keresési feltételeknek, például a lekérdezési kifejezés vagy kifejezés, az eredmények lekérése rangsorolt. Keresési találatok eléri figyelje meg, hogy a keresési eredmények a mélyebb csökkentése.

   ```Input
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true&$top=100,&$skip=50
   ```

   **Results**

   ![Batch-keresési eredmények](./media/search-explorer/search-explorer-example-topskip.png "visszatérési következő köteg a keresési eredmények")

## <a name="filter-expressions-greater-than-less-than-equal-to"></a>Szűrés kifejezések (nagyobb, kisebb, egyenlő)

Használja a **$filter** paramétert, ha meg szeretné határozni a szabad szöveges keresés helyett pontos feltételeknek. Ebben a példában a 3-nál több hálószoba keresi: `search=seattle condo&$filter=beds gt 3&$count=true`

   ![Szűrési kifejezés](./media/search-explorer/search-explorer-example-filter.png "szűrése feltétel alapján")

## <a name="order-by-expressions"></a>Order by kifejezésnek

Adjon hozzá **$orderby** kattintva rendezheti az eredményeket a keresési pontszámtól mellett egy másik mező szerint. Mindezt felépíted teszteléséhez használhatja például kifejezés `search=seattle condo&$select=listingId,beds,price&$filter=beds gt 3&$count=true&$orderby=price asc`

   ![OrderBy kifejezés](./media/search-explorer/search-explorer-example-ordery.png "a rendezési sorrend módosítása")

Mindkét **$filter** és **$orderby** kifejezések OData építmények. További információk: [OData-szűrőszintaxis](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

## <a name="next-steps"></a>További lépések

Az alábbi forrásokban további tudnivalókat és példákat találhat a lekérdezési szintaxisokról.

 + [Egyszerű lekérdezési szintaxis](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) 
 + [Lucene lekérdezési szintaxis](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 
 + [Példák Lucene-lekérdezésekre](search-query-lucene-examples.md) 
 + [OData szűrési kifejezés szintaxisa](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) 