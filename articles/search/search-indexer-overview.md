---
title: Indexelők indexelőtámogatást adatforrások - indexelés során Azure keresése
description: Feltérképezi az Azure SQL Database, az Azure Cosmos DB vagy az Azure Storage tárolókat a kereshető adatok kinyeréséhez és az Azure Search-index feltöltéséhez.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: na
ms.topic: conceptual
ms.date: 03/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: b485b6b7f6ddbdb45d3ca6170c29a9af3c5b63dc
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2019
ms.locfileid: "57407986"
---
# <a name="indexers-in-azure-search"></a>Indexelők az Azure Search szolgáltatásban

Egy *indexelő* az Azure Search egy olyan webbejáró, amely kinyeri a kereshető adatok és metaadatok egy külső Azure adatforrásból és tölti fel az indexet alapján mező mező leképezések az index és az adatforrás között. Ez a módszer van más néven "lekérési modellnek", mert a szolgáltatás végzi az adatok lekérését, amelyek adatokat ad hozzá egy index kód írása nélkül.

Az indexelők adatforrástípusokon vagy egyéni indexelők az SQL Server Azure, a Cosmos DB, az Azure Table Storage és a Blob Storage-platformokon alapulnak. BLOB storage-indexelő a blob tartalomtípusok jellemző további tulajdonságokkal rendelkezik.

Az indexelőt használhatja kizárólag adatfeldolgozásra, illetve olyan kombinált műveletekben is, amelyek részeként az indexelő használatával csak az index bizonyos mezőinek betöltése történik meg.

Az indexelők futtatása történhet igény szerint vagy ismétlődő adatfrissítési ütemterv alapján, akár 15 perces gyakorisággal is. Az ennél gyakoribb frissítésekhez olyan leküldési modellre van szükség, amely egyszerre frissíti az adatokat az Azure Search szolgáltatásban és a külső adatforrásban is.

## <a name="approaches-for-creating-and-managing-indexers"></a>Indexelők létrehozásának és kezelésének módszerei

Az indexelők létrehozása és kezelése a következő módszerekkel történhet:

* [Portál > adatok importálása varázsló](search-import-data-portal.md)
* [Szolgáltatás REST API-ja](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations)

Az új indexelőket először előzetes verziójú funkcióként vezetjük be. Az előzetes verziójú funkciók API-kban (REST és .NET) kerülnek bemutatásra, majd később, azok általánosan elérhetővé tétele után integráljuk őket a portál rendszerével. Új indexelő kiértékelése esetén érdemes számolni azzal, hogy kódírásra is sor fog kerülni.

## <a name="permissions"></a>Engedélyek

Az indexelők, beleértve az állapot vagy a definíciók, GET-kérésekhez kapcsolódó minden művelethez szükség van egy [rendszergazdai api-kulcsát](search-security-api-keys.md). 

<a name="supported-data-sources"></a>

## <a name="supported-data-sources"></a>Támogatott adatforrások

Az indexelők adattárak feltérképezi az Azure-ban.

* [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
* [Azure Table Storage](search-howto-indexing-azure-tables.md) 

> [!Note]
> Az Azure Table Storage esetén nem támogatott [cognitive search](cognitive-search-concept-intro.md).
>

## <a name="basic-configuration-steps"></a>Alapszintű konfigurációs lépések
Az indexelők az adott adatforrások esetében egyedi funkciókat biztosítanak. Ezért az indexelő- vagy az adatforrás-konfiguráció egyes szempontjai az indexelő típusától függően változnak. Az alapvető felépítés és követelmények azonban minden indexelő esetében azonosak. Az alábbiakban az összes indexelőre érvényes lépések láthatóak.

### <a name="step-1-create-a-data-source"></a>1. lépés: Adatforrás létrehozása
Az indexelő beolvassa az adatforrás-kapcsolat egy *adatforrás* objektum. Az adatforrás-definíciót egy kapcsolati karakterláncot, és esetleg hitelesítő adatokat biztosít. Hívja a [adatforrás létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-data-source) REST API vagy [DataSource osztály](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource) az erőforrás létrehozásához.

Az adatforrások konfigurálása és kezelése az azokat használó indexelőktől függetlenül történik, ami azt jelenti, hogy egy adatforrást több indexelő is használhat egyidejűleg, egynél több index betöltésére.

### <a name="step-2-create-an-index"></a>2. lépés: Index létrehozása
Az indexelők automatizálni tudják az adatfeldolgozáshoz kapcsolódó bizonyos feladatokat, de az indexek létrehozása nem tartozik ezek közé. Előfeltételként olyan előre meghatározott indexre van szükség, amelynek mezői egyeznek a külső adatforrás mezőivel. Mezők meg kell egyeznie a nevét és adattípusát. További információk az indexek strukturálásáról: [(Azure Search REST API) Index létrehozása](https://docs.microsoft.com/rest/api/searchservice/Create-Index) vagy [osztály Index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index). A mezőtársításokkal kapcsolatos további információkért lásd [az Azure Search indexelők mezőleképezéseivel](search-indexer-field-mappings.md) foglalkozó témakört.

> [!Tip]
> Az indexelők nem tudnak indexet létrehozni Önnek, de a portál **Adatok importálása** varázslója a segítségére lehet ebben. A legtöbb esetben a varázsló következtetni tud az indexsémára a forrás meglévő metaadataiból, és előállít egy olyan előzetes indexsémát, amely beágyazott módon szerkeszthető mindaddig, amíg a varázsló aktív. Miután létrejött az index a szolgáltatásban, a további szerkesztés a portálon a legtöbb esetben új mezők hozzáadására van korlátozva. A varázsló használatát érdemes megfontolnia az indexek létrehozásakor, de az áttekintésükkor nem. A gyakorlati tanuláshoz végezze el a [portál útmutatójában](search-get-started-portal.md) foglalt lépéseket.

### <a name="step-3-create-and-schedule-the-indexer"></a>3. lépés: Az indexelő létrehozása és ütemezése
Az indexelő definíciója egy szerkezet, amely egyesíti az adatfeldolgozáshoz kapcsolódó összes elemet. Szükséges elemek a következők: adatforrás és index. Nem kötelező elemek közé tartozik egy ütemezés és a mező-leképezések. Mezőt leképezés csak megadása nem kötelező, ha forrás mezők és az index mezőire egyértelműen felel meg. Az indexelők egy másik szolgáltatásból is hivatkozhatnak egy adatforrásra, ha az adott adatforrás ugyanabból az előfizetésből származik. További információk az indexelők strukturálásáról: [Indexelő létrehozása (Azure Search REST API)](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer).

<a id="RunIndexer"></a>

## <a name="run-indexers-on-demand"></a>Az indexelők igény szerinti futtatása

Bár a közös indexelő ütemezése, az indexelő is elindítható, az adatkészletet a a [futtatása paranccsal](https://docs.microsoft.com/rest/api/searchservice/run-indexer):

    POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=2017-11-11
    api-key: [Search service admin key]

> [!NOTE]
> API futtatása sikeresen adja vissza, az indexelő meghívási be van ütemezve, de a tényleges feldolgozást aszinkron módon történik. 

Az indexelő állapota a portálon vagy lekérése az indexelő állapotának API-n keresztül figyelheti. 

<a name="GetIndexerStatus"></a>

## <a name="get-indexer-status"></a>Az indexelő állapotának beolvasása

Kérheti, hogy az indexelő keresztül állapotát és végrehajtási előzményeinek a [indexelő állapotának beolvasása parancs](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status):


    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2017-11-11
    api-key: [Search service admin key]

A válasz teljes indexelő állapot, az utolsó (vagy a folyamatban lévő) az indexelő meghívása és az indexelő legutóbbi indítások előzményeit tartalmazza.

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

Végrehajtási előzmények akár a 50 utolsó befejezett végrehajtások, amely rendezi a rendszer fordított időrendben (így a legújabb végrehajtása a válaszban hamarabb elérik) tartalmazza.

## <a name="next-steps"></a>További lépések
Az alapok megismerése után következő lépés a követelmények és az egyes adatforrástípusokra jellemző feladatok áttekintése.

* [Azure SQL Database vagy SQL Server egy Azure virtuális gépen](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
* [Azure Table Storage](search-howto-indexing-azure-tables.md)
* [CSV-blobok indexelése az Azure Search Blob indexelőjével](search-howto-index-csv-blobs.md)
* [JSON-blobok indexelése az Azure Search Blob indexelőjével](search-howto-index-json-blobs.md)
