---
title: Indexelő az adatbejárás során az importálás során
titleSuffix: Azure Cognitive Search
description: A kereshető adatok kinyeréséhez és az Azure Cognitive Search indexének feltöltéséhez bejárási Azure SQL Database, SQL felügyelt példány, Azure Cosmos DB vagy Azure Storage.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/11/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 5861e79054bed0d9d75258dfa9cb39b198f0f93d
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2021
ms.locfileid: "98216444"
---
# <a name="indexers-in-azure-cognitive-search"></a>Indexelők az Azure Cognitive Searchben

Az Azure Cognitive Search *Indexelő* egy olyan webbejáró, amely egy külső Azure-adatforrásból Kinyeri a kereshető adatokat és metaadatokat, és a keresési indexet a forrásadatok és az index közötti mező-mező leképezések használatával tölti fel. Ezt a módszert más néven "lekéréses modellnek" is nevezzük, mert a szolgáltatás olyan kódot kér le, amely nem rendelkezik olyan kóddal, amely az adott indexbe felveszi az adattípust.

Az indexelő csak az Azure-ban, az [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), az [Azure Cosmos db](search-howto-index-cosmosdb.md), az [Azure Table Storage](search-howto-indexing-azure-tables.md) és a [blob Storage](search-howto-indexing-azure-blob-storage.md)egyéni indexelő szolgáltatásával. Az indexelő konfigurálásakor meg kell adnia egy adatforrást (forrás), valamint egy indexet (cél). Számos forrás – például a blob Storage – rendelkezik az adott tartalomtípushoz tartozó további konfigurációs tulajdonságokkal.

Az Indexelő szolgáltatást igény szerint vagy ismétlődő adatfrissítési ütemezés szerint futtathatja, amely akár öt percenként is futtatható. A gyakoribb frissítések esetében olyan leküldéses modellre van szükség, amely egyszerre frissíti az Azure Cognitive Search és a külső adatforrás adatait is.

## <a name="usage-scenarios"></a>Használati forgatókönyvek

Az indexelő egyetlen eszközként használhatja az adatfeldolgozáshoz, vagy használhat olyan technikák kombinációját, amelyek csak néhány mezőt töltenek be az indexből, igény szerint átalakíthatja vagy gazdagíthatja a tartalmat az út mentén. A következő táblázat összefoglalja a főbb forgatókönyveket.

| Használati eset |Stratégia |
|----------|---------|
| Önálló forrás | Ez a minta a legegyszerűbb: az egyik adatforrás a keresési index egyetlen szolgáltatója. A forrásból egy olyan mezőt fog azonosítani, amely egyedi értékeket tartalmaz, amelyek a keresési indexben a dokumentum kulcsaként szolgálnak. A rendszer az egyedi értéket fogja használni azonosítóként. Minden más forrás mező implicit módon vagy explicit módon van leképezve az index megfelelő mezőihez. </br></br>Fontos elvihetőség, hogy a dokumentum kulcsa a forrásadatokből származik. A keresési szolgáltatás nem hoz alapértékeket. A későbbi futtatások során új kulcsokkal rendelkező bejövő dokumentumok lesznek hozzáadva, míg a meglévő kulcsokkal rendelkező bejövő dokumentumok egyesítése vagy felülírása attól függően, hogy az index mezői null értékűek vagy fel vannak-e töltve. |
| Több forrás| Az indexek több forrásból is fogadhatnak tartalmakat, ahol minden egyes Futtatás új tartalmat hoz egy másik forrásból. </br></br>Az egyik eredmény lehet egy olyan index, amely minden indexelő futtatása után a dokumentumokat fogja megnyerni, és az összes forrásból származó teljes dokumentum teljes egészében létrejött. Az 1-100-as dokumentumok például a blob Storage-ból származnak, a 101-200-es dokumentumok pedig az Azure SQL-ből, és így tovább. Ennek a forgatókönyvnek a feladata az összes bejövő adathoz használható index séma megtervezése, valamint a keresési indexben egységes dokumentum-kulcs szerkezete. Natív módon a dokumentumok egyedi azonosítására szolgáló értékek egy blob-tárolóban és egy SQL-tábla elsődleges kulcsa metadata_storage_path. Képzelje el, hogy az egyik vagy mindkét forrást módosítani kell, hogy a kulcsfontosságú értékeket közös formátumban adja meg, függetlenül a tartalom eredetétől. Ebben az esetben várhatóan bizonyos szintű előfeldolgozást kell végeznie az adatok homogenizálása érdekében, hogy azt egyetlen indexbe lehessen húzni.</br></br>Lehetséges, hogy az első futtatáskor részlegesen kitöltött dokumentumok kereshetők, majd a későbbi futtatások további kitöltésével más forrásokból származó értékeket hoznak. Például a 1-10 mezők blob Storage-ból, 11-20 az Azure SQL-ből és így tovább. Ennek a mintának az a kihívása, hogy minden indexelési Futtatás ugyanazt a dokumentumot célozza meg. A mezők meglévő dokumentumba való egyesítéséhez meg kell egyeznie a dokumentum kulcsával. A forgatókönyv bemutatását lásd [: oktatóanyag: index több adatforrásból](tutorial-multiple-data-sources.md). |
| Tartalom átalakítása | A Cognitive Search támogatja a választható [AI-gazdagító](cognitive-search-concept-intro.md) viselkedéseket, amelyek képelemzést és természetes nyelvi feldolgozást hoznak létre új kereshető tartalom és struktúra létrehozásához. Az AI-bővítés Indexer-alapú, csatolt [készségkészlet](cognitive-search-working-with-skillsets.md)keresztül történik. Ha AI-bővítést szeretne végezni, az indexelő továbbra is indexre és adatforrásra van szüksége, de ebben az esetben a készségkészlet-feldolgozást hozzáadja az indexelő végrehajtásához. |

## <a name="approaches-for-creating-and-managing-indexers"></a>Indexelők létrehozásának és kezelésének módszerei

Az indexelők létrehozása és kezelése a következő módszerekkel történhet:

+ [Portál > adatimportálás varázsló](search-import-data-portal.md)
+ [Szolgáltatás REST API-ja](/rest/api/searchservice/Indexer-operations)
+ [.NET SDK](/dotnet/api/azure.search.documents.indexes.models.searchindexer)

Ha SDK-t használ, hozzon létre egy [SearchIndexerClient](/dotnet/api/azure.search.documents.indexes.searchindexerclient) az indexelő, az adatforrások és a szakértelmével használatához. A fenti hivatkozás a .NET SDK-hoz készült, de az SDK-k SearchIndexerClient és hasonló API-kat biztosítanak.

Kezdetben az új adatforrások előzetes verzióként vannak bejelentve, és csak REST-alapúak. Az általános rendelkezésre állás elvégzése után a teljes támogatás a Portálon és a különböző SDK-k részét képezi, amelyek mindegyike saját kiadási ütemezésen alapul.

## <a name="permissions"></a>Engedélyek

Az indexelő rendszerhez kapcsolódó összes művelet, beleértve az állapot vagy a definíciók beszerzésére irányuló kéréseket, [rendszergazdai API-kulcs](search-security-api-keys.md)szükséges.

<a name="supported-data-sources"></a>

## <a name="supported-data-sources"></a>Támogatott adatforrások

Az indexelő adattárakat térképez fel az Azure-ban.

+ [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
+ [Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md) (előzetes verzió)
+ [Azure-Table Storage](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](search-howto-index-cosmosdb.md)
+ [Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [SQL Managed Instance](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)
+ [SQL Server az Azure Virtual Machines szolgáltatásban](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)

## <a name="stages-of-indexing"></a>Az indexelés szakaszai

Ha a kezdeti futtatáskor az index üres, akkor az indexelő a táblázatban vagy a tárolóban megadott összes adattal beolvasható. A későbbi futtatások során az indexelő általában csak a módosított adatértékeket észlelheti és kérheti le. A Blobok esetében az észlelés módosítása automatikus. Más adatforrások esetében, mint például az Azure SQL vagy a Cosmos DB, engedélyezni kell az észlelés módosítását.

Az indexelő minden egyes betöltött dokumentum esetében több lépést valósít meg vagy koordinál, a dokumentum lekérésével a "handoff" végső keresőmotorba az indexeléshez. Opcionálisan az indexelő is szerepet játszik a készségkészlet-végrehajtás és-kimenetek vezetésében, feltételezve, hogy készségkészlet van definiálva.

:::image type="content" source="media/search-indexer-overview/indexer-stages.png" alt-text="Indexelő szakaszai" border="false":::

### <a name="stage-1-document-cracking"></a>1. lépés: a dokumentum repedése

A dokumentumok repedése a fájlok megnyitásának és a tartalom kinyerésének folyamata. Az adatforrás típusától függően az indexelő megpróbál különböző műveleteket végrehajtani a potenciálisan indexelhető tartalom kinyeréséhez.  

Angol nyelvű Példák:  

+ Ha a dokumentum egy [Azure SQL-adatforrás](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)egyik rekordja, az indexelő Kinyeri a rekord összes mezőjét.
+ Ha a dokumentum egy [Azure Blob Storage adatforrásban](search-howto-indexing-azure-blob-storage.md)található PDF-fájl, az indexelő Kinyeri a szöveget, a képeket és a metaadatokat.
+ Ha a dokumentum egy [Cosmos db adatforrásban](search-howto-index-cosmosdb.md)lévő rekord, az indexelő kibontja a mezőket és almezőket a Cosmos db dokumentumból.

### <a name="stage-2-field-mappings"></a>2. fázis: mező-hozzárendelések 

Az indexelő Kinyeri a szöveget a forrás mezőből, és elküldi azt egy index vagy egy Tudásbázis célhely mezőjébe. Ha a mezők nevei és típusai egybeesnek, az elérési út üres. Előfordulhat azonban, hogy más neveket vagy típusokat szeretne használni a kimenetben, ebben az esetben meg kell adnia az indexelő számára a mező leképezését. Ez a lépés a dokumentum repedése, de az átalakítások előtt következik be, amikor az indexelő beolvassa a forrás dokumentumait. Amikor meghatároz egy [mező-hozzárendelést](search-indexer-field-mappings.md), a forrás mező értékét a rendszer a cél mezőre, módosítás nélkül továbbítja. A mező-hozzárendelések nem kötelezőek.

### <a name="stage-3-skillset-execution"></a>3. fázis: Készségkészlet végrehajtás

A készségkészlet-végrehajtás egy opcionális lépés, amely a beépített vagy egyéni AI-feldolgozást hívja meg. Előfordulhat, hogy az optikai karakterfelismerést (OCR) képelemzés formájában kell megadnia, vagy szükség lehet a nyelvi fordításra. Az átalakítástól függetlenül a készségkészlet-végrehajtás is az, ahol a dúsítás történik. Ha egy indexelő egy folyamat, úgy gondolhatja, hogy egy [készségkészlet](cognitive-search-defining-skillset.md) "folyamat a folyamaton belül". A készségkészlet a szaktudás nevű saját lépések sorával rendelkezik.

### <a name="stage-4-output-field-mappings"></a>4. fázis: kimeneti mezők leképezése

Ha készségkészlet tartalmaz, valószínűleg a kimeneti mezők leképezéseit is meg kell adnia. A készségkészlet kimenete valójában a dúsított dokumentumnak nevezett információk fája. A kimeneti mezők leképezése lehetővé teszi, hogy kiválassza a fa azon részeit, amelyek az index mezőibe képezhetők le. Útmutató a [kimeneti mezők leképezésének definiálásához](cognitive-search-output-field-mapping.md).

Míg a mező-hozzárendelések az adatforrásból a cél mezőkhöz társított Verbatim-értékeket, a kimeneti mezők leképezései azt mondják el, hogy az indexelő Hogyan rendeli hozzá az átalakított értékeket a dúsított dokumentumhoz az indexben lévő cél mezőkhöz. A mező-hozzárendelések eltérően, amelyek nem kötelezőnek számítanak, minden esetben meg kell adnia egy kimeneti mező leképezését minden olyan átalakított tartalomhoz, amelynek egy indexben kell lennie.

A következő képen egy példa indexelő [hibakeresési munkamenetének](cognitive-search-debug-session.md) ábrázolása látható az indexelő szakaszaiban: dokumentum repedések, mező-hozzárendelések, készségkészlet-végrehajtás és kimeneti mezők leképezése.

:::image type="content" source="media/search-indexer-overview/sample-debug-session.png" alt-text="Példa hibakeresési munkamenetre" lightbox="media/search-indexer-overview/sample-debug-session.png":::

## <a name="basic-configuration-steps"></a>Alapszintű konfigurációs lépések

Az indexelők az adott adatforrások esetében egyedi funkciókat biztosítanak. Ezért az indexelő- vagy az adatforrás-konfiguráció egyes szempontjai az indexelő típusától függően változnak. Az alapvető felépítés és követelmények azonban minden indexelő esetében azonosak. Az alábbiakban az összes indexelőre érvényes lépések láthatóak.

### <a name="step-1-create-a-data-source"></a>1. lépés: Adatforrás létrehozása

Az indexelő beolvassa az adatforrás-kapcsolatokat *egy adatforrás-objektumból* . Az adatforrás-definíció egy kapcsolati karakterláncot és esetleg hitelesítő adatokat biztosít. Az erőforrás létrehozásához hívja meg a [create Datasource](/rest/api/searchservice/create-data-source) REST API vagy a [SearchIndexerDataSourceConnection osztályt](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) .

Az adatforrások konfigurálása és kezelése az azokat használó indexelőktől függetlenül történik, ami azt jelenti, hogy egy adatforrást több indexelő is használhat egyidejűleg, egynél több index betöltésére.

### <a name="step-2-create-an-index"></a>2. lépés: Index létrehozása

Az indexelők automatizálni tudják az adatfeldolgozáshoz kapcsolódó bizonyos feladatokat, de az indexek létrehozása nem tartozik ezek közé. Előfeltételként olyan előre meghatározott indexre van szükség, amelynek mezői egyeznek a külső adatforrás mezőivel. A mezőknek meg kell egyezniük a név és az adattípus alapján. Az indexek strukturálásával kapcsolatos további információkért lásd: [index létrehozása (Azure Cognitive Search REST API)](/rest/api/searchservice/Create-Index) vagy [SearchIndex osztály](/dotnet/api/azure.search.documents.indexes.models.searchindex). A mezők társításával kapcsolatos segítségért lásd: [mező-hozzárendelések az Azure Cognitive Search indexelő](search-indexer-field-mappings.md)szolgáltatásban.

> [!Tip]
> Az indexelők nem tudnak indexet létrehozni Önnek, de a portál **Adatok importálása** varázslója a segítségére lehet ebben. A legtöbb esetben a varázsló következtetni tud az indexsémára a forrás meglévő metaadataiból, és előállít egy olyan előzetes indexsémát, amely beágyazott módon szerkeszthető mindaddig, amíg a varázsló aktív. Miután létrejött az index a szolgáltatásban, a további szerkesztés a portálon a legtöbb esetben új mezők hozzáadására van korlátozva. A varázsló használatát érdemes megfontolnia az indexek létrehozásakor, de az áttekintésükkor nem. A gyakorlati tanuláshoz végezze el a [portál útmutatójában](search-get-started-portal.md) foglalt lépéseket.

### <a name="step-3-create-and-schedule-the-indexer"></a>3. lépés: Az indexelő létrehozása és ütemezése

Az indexelő definíciója egy olyan szerkezet, amely az adatfeldolgozással kapcsolatos összes elemet egyesíti. A szükséges elemek közé tartozik az adatforrás és az index. A választható elemek közé tartozik az ütemterv és a mező leképezése. A mező-hozzárendelések csak akkor választhatók, ha a forrás mezők és az index mezők világosan megfelelnek. Az indexelő strukturálásával kapcsolatos további információkért lásd: az [Indexelő létrehozása (Azure Cognitive Search REST API)](/rest/api/searchservice/Create-Indexer).

<a id="RunIndexer"></a>

## <a name="run-indexers-on-demand"></a>Indexelő futtatása igény szerint

Habár az indexelés ütemezését közösen is megteheti, az indexelő a [Run paranccsal](/rest/api/searchservice/run-indexer)is igény szerint hívható meg:

```http
POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=2020-06-30
api-key: [Search service admin key]
```

> [!NOTE]
> Ha a Run API sikeres kódot ad vissza, az indexelő meghívása ütemezve lett, de a tényleges feldolgozás aszinkron módon történik. 

Az indexelő állapotát a portálon vagy az [Indexelő status API](/rest/api/searchservice/get-indexer-status)-n keresztül követheti nyomon. 

<a name="GetIndexerStatus"></a>

## <a name="get-indexer-status"></a>Indexelő állapotának beolvasása

Az indexelő állapotának és végrehajtásának előzményeit az [Indexelő állapotának lekérése paranccsal](/rest/api/searchservice/get-indexer-status)kérheti le:

```http
GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2020-06-30
api-key: [Search service admin key]
```

A válasz általános indexelő állapotot, az utolsó (vagy folyamatban lévő) indexelő meghívást, valamint a legutóbbi indexelő hívás előzményeit tartalmazza.

```output
{
    "status":"running",
    "lastResult": {
        "status":"success",
        "errorMessage":null,
        "startTime":"2018-11-26T03:37:18.853Z",
        "endTime":"2018-11-26T03:37:19.012Z",
        "errors":[],
        "itemsProcessed":11,
        "itemsFailed":0,
        "initialTrackingState":null,
        "finalTrackingState":null
     },
    "executionHistory":[ {
        "status":"success",
         "errorMessage":null,
        "startTime":"2018-11-26T03:37:18.853Z",
        "endTime":"2018-11-26T03:37:19.012Z",
        "errors":[],
        "itemsProcessed":11,
        "itemsFailed":0,
        "initialTrackingState":null,
        "finalTrackingState":null
    }]
}
```

A végrehajtási előzmények legfeljebb a 50 legutóbbi befejezett végrehajtást tartalmazzák, amelyek fordított időrendi sorrendben vannak rendezve (így a legutóbbi végrehajtás a válaszban elsőként jelenik meg).

## <a name="next-steps"></a>Következő lépések

Az alapok megismerése után következő lépés a követelmények és az egyes adatforrástípusokra jellemző feladatok áttekintése.

+ [Azure SQL Database, SQL felügyelt példány vagy SQL Server Azure-beli virtuális gépen](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Cosmos DB](search-howto-index-cosmosdb.md)
+ [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
+ [Azure-Table Storage](search-howto-indexing-azure-tables.md)
+ [CSV-Blobok indexelése az Azure Cognitive Search blob indexelő használatával](search-howto-index-csv-blobs.md)
+ [JSON-Blobok indexelése az Azure Cognitive Search blob indexelő](search-howto-index-json-blobs.md)