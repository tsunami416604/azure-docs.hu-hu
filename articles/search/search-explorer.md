---
title: A Search Explorer eszköz használata az Azure Portal-Azure Searchban tárolt adatlekérdezéshez
description: A Search Explorer a Azure Portalbe van építve, amely hasznos lehet a tartalom feltárásához és a lekérdezések érvényesítéséhez Azure Searchokban. Karakterláncokat adhat meg a kifejezés vagy kifejezés kereséséhez, vagy a speciális szintaxissal rendelkező teljes keresési kifejezésekhez.
manager: nitinme
author: HeidiSteen
services: search
ms.service: search
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: fe66787ea82a8f97470199e99faadb72b85c83b2
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2019
ms.locfileid: "71178132"
---
# <a name="use-search-explorer-in-the-azure-portal-for-querying-documents-in-azure-search"></a>A Azure Portal keresési ablakának használata dokumentumok lekérdezéséhez Azure Search 

Ez a cikk bemutatja, hogyan lehet lekérdezni egy meglévő Azure Search indexet a Azure Portal a **Search Explorerben** . A keresési tallózót elindíthatja a parancssorból egyszerű vagy teljes Lucene lekérdezési kifejezések küldéséhez a szolgáltatás bármely meglévő indexéhez. 

   ![Keresési Explorer parancs a portálon](./media/search-explorer/search-explorer-cmd2.png "Keresési Explorer parancs a portálon")

## <a name="basic-search-strings"></a>Alapszintű keresési karakterláncok

Az alábbi példák feltételezik a beépített ingatlan-minta indexét. Ezt az indexet az adatimportálás varázsló segítségével hozhatja létre a portálon, és kiválaszthatja az adatforrásként szolgáló **mintákat** .

### <a name="example-1---empty-search"></a>1\. példa – üres keresés

Ha először szeretné megtekinteni a tartalmat, hajtson végre egy üres keresést a kifejezés nélküli **Keresés** gombra kattintva. Az üres keresések első lekérdezésként hasznosak, mivel teljes dokumentumokat ad vissza, így áttekintheti a dokumentumok összeállítását. Üres keresés esetén a keresés nem történik meg, és a dokumentumok tetszőleges sorrendben lesznek visszaadva (`"@search.score": 1` minden dokumentum esetében). Alapértelmezés szerint a rendszer 50-dokumentumokat ad vissza egy keresési kérelemben.

Üres keresés `*` egyenértékű szintaxisa: vagy `search=*`.

   ```Input
   search=*
   ```

   **Results**
   
   ![Üres lekérdezési példa Nem](./media/search-explorer/search-explorer-example-empty.png "minősített vagy üres lekérdezési példa")

### <a name="example-2---free-text-search"></a>2\. példa – ingyenes szöveges keresés

A szabad formátumú lekérdezések operátorokkal vagy anélkül is hasznosak az egyéni alkalmazásokból a Azure Searchba küldött, felhasználó által definiált lekérdezések szimulálásához. Figyelje meg, hogy amikor lekérdezési kifejezéseket vagy kifejezéseket ad meg, a keresés rangba kerül. Az alábbi példa egy ingyenes szöveges keresést mutat be.

   ```Input
   Seattle apartment "Lake Washington" miele OR thermador appliance
   ```

   **Results**

   A CTRL-F billentyűkombinációval adott feltételek alapján kereshet az eredmények között.

   ![Példa ingyenes szöveges lekérdezésre](./media/search-explorer/search-explorer-example-freetext.png "Példa ingyenes szöveges lekérdezésre")

### <a name="example-3---count-of-matching-documents"></a>3\. példa – egyező dokumentumok száma 

**$Count** hozzáadása az indexben található egyezések számának lekéréséhez. Üres keresés esetén a Count az indexben lévő dokumentumok teljes száma. Minősített keresés esetén a lekérdezés bemenetének megfelelő dokumentumok száma.

   ```Input1
   $count=true
   ```
   **Results**

   ![Dokumentumok száma – példa](./media/search-explorer/search-explorer-example-count.png "A megfelelő dokumentumok száma az indexben")

### <a name="example-4---restrict-fields-in-search-results"></a>4\. példa – mezők korlátozása a keresési eredmények között

**$Select** hozzáadása az eredmények a explicit módon megnevezett mezőkre való korlátozásához a **Search Explorerben**olvasható kimenethez. A keresési karakterlánc és a **$Count = True**érték megtartásához előtag **&** argumentumai a következővel:. 

   ```Input
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true
   ```

   **Results**

   ![Példa a mezők korlátozására](./media/search-explorer/search-explorer-example-selectfield.png "Mezők korlátozása a keresési eredmények között")

### <a name="example-5---return-next-batch-of-results"></a>5\. példa – az eredmények következő kötegének visszaküldése

A Azure Search a keresési rangsor alapján a legfontosabb 50-as egyezést adja vissza. A megfelelő dokumentumok következő készletének lekéréséhez fűzze hozzá a **$Top = 100, & $skip = 50** , hogy növelje az eredményhalmaz 100-es dokumentumokra (alapértelmezett érték: 50, maximális értéke 1000), kihagyva az első 50-dokumentumot. Ne felejtse el, hogy keresési feltételeket kell megadnia, például egy lekérdezési kifejezést vagy kifejezést a rangsorolt eredmények beszerzéséhez. Figyelje meg, hogy a keresési pontszámok csökkentik a keresési eredményekben elért mélyebb eredményeket.

   ```Input
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true&$top=100&$skip=50
   ```

   **Results**

   A ![Batch keresési eredményei](./media/search-explorer/search-explorer-example-topskip.png "Keresési eredmények következő kötegének visszaküldése")

## <a name="filter-expressions-greater-than-less-than-equal-to"></a>Szűrési kifejezések (nagyobb, mint, kisebb, mint, egyenlő)

Ha a szabadszöveges keresés helyett pontos feltételeket szeretne megadni, használja a **$Filter** paramétert. Ez a példa a 3-nál nagyobb hálószobákat keres:

   ```Input
   search=seattle condo&$filter=beds gt 3&$count=true
   ```
   
   **Results**

   ![Szűrő kifejezése](./media/search-explorer/search-explorer-example-filter.png "Szűrés feltételek szerint")

## <a name="order-by-expressions"></a>Rendezési kifejezések

**$OrderBy** hozzáadása az eredmények egy másik mezővel való rendezéséhez a keresési pontszám mellett. A kipróbálható példaként használható kifejezés:

   ```Input
   search=seattle condo&$select=listingId,beds,price&$filter=beds gt 3&$count=true&$orderby=price asc
   ```
   
   **Results**

   ![OrderBy kifejezés](./media/search-explorer/search-explorer-example-ordery.png "Rendezési sorrend módosítása")

**$Filter** és **$OrderBy** kifejezések is OData-konstrukciók. További információk: [OData-szűrőszintaxis](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

<a name="start-search-explorer"></a>

## <a name="how-to-start-search-explorer"></a>A Search Explorer elindítása

1. A [Azure Portal](https://portal.azure.com)nyissa meg a keresési szolgáltatás lapot az irányítópulton, vagy [Keresse meg a szolgáltatást](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) a szolgáltatás listában.

2. A szolgáltatás áttekintése lapon kattintson a **Search Explorer**elemre.

   ![Keresési Explorer parancs a portálon](./media/search-explorer/search-explorer-cmd2.png "Keresési Explorer parancs a portálon")

3. Válassza ki a lekérdezni kívánt indexet.

   ![Válassza ki a lekérdezni kívánt indexet](./media/search-explorer/search-explorer-changeindex-se2.png "Az index kiválasztása")

4. Igény szerint beállíthatja az API verzióját. Alapértelmezés szerint a jelenlegi általánosan elérhető API-verzió van kiválasztva, de ha a használni kívánt szintaxist használja, választhat egy előnézetet vagy egy régebbi API-t is.

5. Az index és az API-verzió kijelölése után írja be a keresési kifejezéseket vagy a teljes lekérdezési kifejezéseket a keresősávba, és kattintson a **Keresés** gombra a végrehajtáshoz.

   ![Adja meg a keresési kifejezéseket, és kattintson a Keresés gombra](./media/search-explorer/search-explorer-query-string-example.png "Adja meg a keresési kifejezéseket, és kattintson a Keresés gombra") .

Tippek a **Search Explorerben**való kereséshez:

+ Az eredményeket a rendszer részletes JSON-dokumentumként adja vissza, így teljes egészében megtekintheti a dokumentumok szerkezetét és tartalmát. A példákban megjelenített lekérdezési kifejezésekkel korlátozhatja, hogy mely mezők legyenek visszaadva.

+ A dokumentumok minden olyan mezőből állnak, amely az indexben beolvasható van megjelölve. Ha szeretné megtekinteni az index attribútumokat a portálon, kattintson a *Realestate-US-Sample* elemre az **indexek** listájában a keresés – áttekintés oldalon.

+ A kereskedelmi webböngészőben megadható, a szabad formátumú lekérdezések a végfelhasználói élmény teszteléséhez is hasznosak lehetnek. Tegyük fel például, hogy a beépített Realestate-minta indexe a "Seattle Apartments Lake Washington" kifejezést adja meg, majd a CTRL-F billentyűkombinációval megkeresheti a kifejezéseket a keresési eredmények között. 

+ A lekérdezési és szűrési kifejezéseket Azure Search által támogatott szintaxissal kell tagolni. Az alapértelmezett érték egy [egyszerű szintaxis](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search), de igény szerint a [teljes Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) is használhatja nagyobb teljesítményű lekérdezésekhez. A [szűrési kifejezések](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) egy OData szintaxis.


## <a name="next-steps"></a>További lépések

Az alábbi forrásokban további tudnivalókat és példákat találhat a lekérdezési szintaxisokról.

 + [Egyszerű lekérdezési szintaxis](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) 
 + [Lucene lekérdezési szintaxis](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 
 + [Példák Lucene-lekérdezésekre](search-query-lucene-examples.md) 
 + [OData szűrési kifejezés szintaxisa](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) 
