---
title: Keresés explorer lekérdezési eszköz az Azure Portalon
titleSuffix: Azure Cognitive Search
description: Ebben az Azure Portalon gyorsindítás, a Search Explorer segítségével a lekérdezés szintaxisát, teszt lekérdezési kifejezések, vagy vizsgálja meg a keresési dokumentumot. A search explorer lekérdezi az indexeket az Azure Cognitive Search-ben.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 03/27/2020
ms.openlocfilehash: 9fb34141d19866a2f49ac164e0d89802cf7818c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369660"
---
# <a name="quickstart-use-search-explorer-to-run-queries-in-the-portal"></a>Gyorsútmutató: Lekérdezések futtatása a portálon a Kereséskezelővel

**A Search Explorer** egy beépített lekérdezési eszköz, amellyel lekérdezéseket futtat az Azure Cognitive Search keresési indexében. Ez az eszköz megkönnyíti a lekérdezés szintaxisának megismerését, a lekérdezés vagy a szűrőkifejezés tesztelését, illetve az indexfrissítés eredményeinek megerősítését az újabb tartalom tartalmának ellenőrzésével.

Ez a rövid útmutató **az ingatlan-us-minta-index** et használja a Kereséskezelő bemutatásához. A kérelmek fogalmazása a [Search REST API használatával](https://docs.microsoft.com/rest/api/searchservice/)történik, és a válaszok JSON-dokumentumként vannak visszaadva.

## <a name="prerequisites"></a>Előfeltételek

+ [Hozzon létre egy Azure Cognitive Search szolgáltatást,](search-create-service-portal.md) vagy [keressen egy meglévő szolgáltatást](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) a jelenlegi előfizetése alatt. A rövid útmutatóhoz ingyenes szolgáltatást használhat.

+ **a quickstart hoz a realestate-us-sample-index** et használja. Az [**Adatok importálása**](search-import-data-portal.md) varázslósegítségével hozza létre az indexet a beépített mintaadatforrásból.

## <a name="start-search-explorer"></a>Keresés kezelőjének indítása

1. Az [Azure Portalon](https://portal.azure.com)nyissa meg a keresési szolgáltatás lapját az irányítópultról, vagy [keresse meg a szolgáltatást.](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)

1. Nyissa meg a Keresés kezelőt a parancssávról:

   ![Keresés explorer parancs a portálon](./media/search-explorer/search-explorer-cmd2.png "Keresés explorer parancs a portálon")

    Vagy használja a beágyazott **Kereséskezelő** lapot egy megnyitott indexen:

   ![Keresés lap](./media/search-explorer/search-explorer-tab.png "Keresés lap")

## <a name="unspecified-query"></a>Meghatározatlan lekérdezés

A tartalom első vizsgálatához hajtson végre egy üres keresést a **Keresés** gombra, feltételek nélkül. Az üres keresés első lekérdezésként hasznos, mivel teljes dokumentumokat ad vissza, így áttekintheti a dokumentum összetételét. Üres keresés esetén nincs keresési rang, és a dokumentumok`"@search.score": 1` tetszőleges sorrendben kerülnek visszaadásra (minden dokumentum esetében). Alapértelmezés szerint 50 dokumentumot ad vissza a keresési kérelem.

Az üres keresés egyenértékű `*` szintaxisa vagy `search=*`.
   
   ```http
   search=*
   ```

   **Results (Eredmények)**
   
   ![Példa üres lekérdezésre](./media/search-explorer/search-explorer-example-empty.png "Példa nem minősített vagy üres lekérdezésre")

## <a name="free-text-search"></a>Szabad szöveges keresés

Az ingyenes enciklikálású lekérdezések operátorokkal vagy operátorok nélkül hasznosak az egyéni alkalmazásból az Azure Cognitive Search szolgáltatásba küldött, felhasználó által definiált lekérdezések szimulálásához. A rendszer csak azindex-definícióban **kereshetőként hozzárendelhető** mezőket vizsgálja meg az egyezések szempontjából. 

Figyelje meg, hogy amikor keresési feltételeket, például lekérdezési kifejezéseket vagy kifejezéseket ad meg, a keresési rangsor is szerepet játszik. A következő példa egy szabad szöveges keresést mutat be.

   ```http
   Seattle apartment "Lake Washington" miele OR thermador appliance
   ```

   **Results (Eredmények)**

   A Ctrl-F segítségével az eredmények között kereshet bizonyos érdeklődési feltételeket.

   ![Példa szabadszöveges lekérdezésre](./media/search-explorer/search-explorer-example-freetext.png "Példa szabadszöveges lekérdezésre")

## <a name="count-of-matching-documents"></a>Az egyező dokumentumok száma 

Adja hozzá **a $count=true** értéket az indexben található egyezések számának leéséhez. Üres kereséskor a számláló az indexben lévő dokumentumok teljes száma. Minősített keresés esetén a lekérdezésbemenetnek megfelelő dokumentumok száma.

   ```http
   $count=true
   ```

   **Results (Eredmények)**

   ![Példa dokumentumok száma](./media/search-explorer/search-explorer-example-count.png "Az egyező dokumentumok száma az indexben")

## <a name="limit-fields-in-search-results"></a>Mezők korlátozása a keresési eredményekben

Adja hozzá [**$select,**](search-query-odata-select.md) hogy az eredményeket a kifejezetten elnevezett mezőkre korlátozza a **Kereséskezelőben**olvashatóbb kimenethez. A keresési karakterlánc és a **$count=true**érték **&** megtartásához az előtag argumentumait a mezőben kell tartani. 

   ```http
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true
   ```

   **Results (Eredmények)**

   ![Példa mezők korlátozására](./media/search-explorer/search-explorer-example-selectfield.png "Mezők korlátozása a keresési eredményekben")

## <a name="return-next-batch-of-results"></a>Az eredmények következő kötegének visszaadása

Az Azure Cognitive Search a keresési rangsor alapján adja vissza az 50 legjobb találatot. Az egyező dokumentumok következő készletének leképezéséhez fűzz **hozzá $top=100 &$skip=50-hez,** hogy az eredményhalmazt 100 dokumentumra növelje (az alapértelmezett érték 50, a maximum 1000), kihagyva az első 50 dokumentumot. Emlékezzünk vissza, hogy a rangsorolt eredmények lereményéhez keresési feltételeket ( például lekérdezési kifejezést vagy kifejezést) kell megadnia. Figyelje meg, hogy a keresési pontszámok annál mélyebbre csökkennek a keresési eredmények ben.

   ```http
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true&$top=100&$skip=50
   ```

   **Results (Eredmények)**

   ![Kötegelt keresési eredmények](./media/search-explorer/search-explorer-example-topskip.png "A keresési eredmények következő kötegének visszaküldése")

## <a name="filter-expressions-greater-than-less-than-equal-to"></a>Szűrőkifejezések (nagyobbak, kisebbek, egyenlőek)

Használja a [**$filter**](search-query-odata-filter.md) paramétert, ha a szabad szöveges keresés helyett pontos feltételeket szeretne megadni. A mezőt az indexben **szűrhetőként** kell hozzárendelni. Ez a példa a 3-nál nagyobb hálószobákat keresi:

   ```http
   search=seattle condo&$filter=beds gt 3&$count=true
   ```
   
   **Results (Eredmények)**

   ![Szűrőkifejezés](./media/search-explorer/search-explorer-example-filter.png "Szűrés feltételek szerint")

## <a name="order-by-expressions"></a>Rendelési kifejezések

Adjon hozzá [**$orderby**](search-query-odata-orderby.md) az eredmények másik mező szerint való rendezéséhez a keresési pontszám mellett. A mezőt az indexben **rendezhetőként** kell hozzárendelni. Egy példa kifejezés segítségével kipróbálni ezt a következő:

   ```http
   search=seattle condo&$select=listingId,beds,price&$filter=beds gt 3&$count=true&$orderby=price asc
   ```
   
   **Results (Eredmények)**

   ![Orderby kifejezés](./media/search-explorer/search-explorer-example-ordery.png "Rendezési sorrend módosítása")

Mind **$filter,** mind **$orderby** kifejezések OData-konstrukciók. További információk: [OData-szűrőszintaxis](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

<a name="start-search-explorer"></a>

## <a name="takeaways"></a>Legfontosabb ismeretek

Ebben a rövid útmutatóban a **Search Explorer** használatával lekérdezegy indexet a REST API használatával.

+ Az eredmények részletes JSON-dokumentumokként kerülnek visszaadásra, így teljes egészében megtekintheti a dokumentumépítést és -tartalmat. A példákban látható lekérdezési kifejezések segítségével korlátozhatja a visszaadott mezőket.

+ A dokumentumok az indexben **Visszaérhetőként** megjelölt összes mezőből állnak. Az indexattribútumok megtekintéséhez a keresés áttekintése lap **Indexek** listájában kattintson az *ingatlan-us-minta* elemre.

+ A szabad formátumú lekérdezések, hasonlóan ahhoz, amit egy kereskedelmi webböngészőben beírhat, hasznosak a végfelhasználói élmény teszteléséhez. Például a beépített ingatlanminta index, megadhatja a "Seattle apartments lake washington", majd a Ctrl-F segítségével keresse meg a kifejezéseket a keresési eredmények között. 

+ A lekérdezési és szűrőkifejezések az Azure Cognitive Search által támogatott szintaxisban vannak megfogalmazva. Az alapértelmezett érték egy [egyszerű szintaxis,](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)de a [teljes Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) használható hatékonyabb lekérdezésekhez. [A szűrőkifejezések](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) odata szintaxis.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha a saját előfizetésében dolgozik, érdemes az egyes projektek végén eldöntenie, hogy szüksége lesz-e még a létrehozott erőforrásokra. A továbbra is futó erőforrások költségekkel járhatnak. Az erőforrások egyesével is törölhetők, de az erőforráscsoport törlésével egyszerre eltávolítható az összes erőforrás is.

Az erőforrásokat a portálon keresheti meg és kezelheti a bal oldali navigációs ablak **Minden erőforrás** vagy **Erőforráscsoport** hivatkozásával.

Ha ingyenes szolgáltatást használ, ne feledje, hogy három indexelésre, indexelőre és adatforrásra van korlátozva. Törölheti az egyes elemeket a portálon, hogy a korlát alatt maradjon. 

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a lekérdezési struktúrákról és a szintaxisról, használja a Postman t vagy egy ezzel egyenértékű eszközt olyan lekérdezési kifejezések létrehozásához, amelyek az API több részét használják ki. A [Search REST API](https://docs.microsoft.com/rest/api/searchservice/) különösen hasznos a tanulás és a feltárás.

> [!div class="nextstepaction"]
> [Alaplekérdezés létrehozása a Postman ben](search-query-simple-examples.md)