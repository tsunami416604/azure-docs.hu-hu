---
title: Indexelők az adatok importálás közbeni bejárásához
titleSuffix: Azure Cognitive Search
description: Feltérképezi az Azure SQL-adatbázist, az Azure Cosmos DB-t vagy az Azure Storage-ot a kereshető adatok kinyeréséhez és az Azure Cognitive Search index feltöltéséhez.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 2719bba0e88ba3125bd5ba163804e31885b286a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282990"
---
# <a name="indexers-in-azure-cognitive-search"></a>Indexelők az Azure Cognitive Searchben

Az Azure Cognitive Search *indexelője* egy feltérképező robot, amely kereshető adatokat és metaadatokat bont ki egy külső Azure-adatforrásból, és feltölti az index és az adatforrás közötti mező-mező leképezések alapján az indexet. Ezt a módszert néha "lekéréses modellnek" is nevezik, mivel a szolgáltatás anélkül kéri be az adatokat, hogy olyan kódot kellene írnia, amely adatokat ad hozzá az indexhez.

Az indexelők adatforrástípusokon vagy platformokon alapulnak, az Azure-beli SQL Server, a Cosmos DB, az Azure Table Storage és a Blob Storage egyedi indexelőivel. A Blob storage indexelők további tulajdonságokkal rendelkeznek a blobtartalom-típusokhoz.

Az indexelőt használhatja kizárólag adatfeldolgozásra, illetve olyan kombinált műveletekben is, amelyek részeként az indexelő használatával csak az index bizonyos mezőinek betöltése történik meg.

Az indexelők igény szerint vagy ismétlődő adatfrissítési ütemezéssel futtathatók, amely öt percenként olyan gyakran fut. A gyakoribb frissítések hez olyan leküldéses modellre van szükség, amely egyidejűleg frissíti az adatokat az Azure Cognitive Search és a külső adatforrásban.

## <a name="approaches-for-creating-and-managing-indexers"></a>Indexelők létrehozásának és kezelésének módszerei

Az indexelők létrehozása és kezelése a következő módszerekkel történhet:

* [Portál > adatok importálása varázsló](search-import-data-portal.md)
* [Szolgáltatás REST API-ja](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations)

Az új indexelőket először előzetes verziójú funkcióként vezetjük be. Az előzetes verziójú funkciók API-kban (REST és .NET) kerülnek bemutatásra, majd később, azok általánosan elérhetővé tétele után integráljuk őket a portál rendszerével. Új indexelő kiértékelése esetén érdemes számolni azzal, hogy kódírásra is sor fog kerülni.

## <a name="permissions"></a>Engedélyek

Az indexelőkkel kapcsolatos összes művelethez, beleértve az állapotra vagy definíciókra vonatkozó GET-kérelmeket, [rendszergazdai api-kulcs](search-security-api-keys.md)szükséges. 

<a name="supported-data-sources"></a>

## <a name="supported-data-sources"></a>Támogatott adatforrások

Az indexelők feltérképezik az azure-beli adattárolókat.

* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
* [Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md) (előzetes verzióban)
* [Azure Table Storage](search-howto-indexing-azure-tables.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [SQL Server az Azure Virtual Machines szolgáltatásban](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)
* [SQL felügyelt példányok az Azure-ban](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)

## <a name="basic-configuration-steps"></a>Alapszintű konfigurációs lépések
Az indexelők az adott adatforrások esetében egyedi funkciókat biztosítanak. Ezért az indexelő- vagy az adatforrás-konfiguráció egyes szempontjai az indexelő típusától függően változnak. Az alapvető felépítés és követelmények azonban minden indexelő esetében azonosak. Az alábbiakban az összes indexelőre érvényes lépések láthatóak.

### <a name="step-1-create-a-data-source"></a>1. lépés: Adatforrás létrehozása
Az indexelő adatforrás-kapcsolatot szerez be egy *adatforrás-objektumból.* Az adatforrás-definíció csatlakozási karakterláncot és esetleg hitelesítő adatokat biztosít. Hívja meg az adatforrás REST-API vagy [DataSource osztály](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource) [létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-data-source) az erőforrás létrehozásához.

Az adatforrások konfigurálása és kezelése az azokat használó indexelőktől függetlenül történik, ami azt jelenti, hogy egy adatforrást több indexelő is használhat egyidejűleg, egynél több index betöltésére.

### <a name="step-2-create-an-index"></a>2. lépés: Index létrehozása
Az indexelők automatizálni tudják az adatfeldolgozáshoz kapcsolódó bizonyos feladatokat, de az indexek létrehozása nem tartozik ezek közé. Előfeltételként olyan előre meghatározott indexre van szükség, amelynek mezői egyeznek a külső adatforrás mezőivel. A mezőknek név és adattípus szerint kell egyezniük. Az index strukturálásáról további információt az [Index létrehozása (Azure Cognitive Search REST API)](https://docs.microsoft.com/rest/api/searchservice/Create-Index) vagy az Index osztály című [témakörben](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index)talál. A mezőtársításokkal kapcsolatban a [Mezőleképezések az Azure Cognitive Search indexelőiben található területenként olvashat.](search-indexer-field-mappings.md)

> [!Tip]
> Az indexelők nem tudnak indexet létrehozni Önnek, de a portál **Adatok importálása** varázslója a segítségére lehet ebben. A legtöbb esetben a varázsló következtetni tud az indexsémára a forrás meglévő metaadataiból, és előállít egy olyan előzetes indexsémát, amely beágyazott módon szerkeszthető mindaddig, amíg a varázsló aktív. Miután létrejött az index a szolgáltatásban, a további szerkesztés a portálon a legtöbb esetben új mezők hozzáadására van korlátozva. A varázsló használatát érdemes megfontolnia az indexek létrehozásakor, de az áttekintésükkor nem. A gyakorlati tanuláshoz végezze el a [portál útmutatójában](search-get-started-portal.md) foglalt lépéseket.

### <a name="step-3-create-and-schedule-the-indexer"></a>3. lépés: Az indexelő létrehozása és ütemezése
Az indexelő definíciója egy olyan konstrukció, amely az adatbetöltéshez kapcsolódó összes elemet egyesíti. A szükséges elemek közé tartozik az adatforrás és az index. A választható elemek ütemezést és mezőleképezéseket tartalmaznak. A mezőleképezés csak akkor választható, ha a forrásmezők és az indexmezők egyértelműen megfelelnek. Az indexelő strukturálásáról további információt az [Indexelő létrehozása (Azure Cognitive Search REST API) című témakörben](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer)talál.

<a id="RunIndexer"></a>

## <a name="run-indexers-on-demand"></a>Indexelők futtatása igény szerint

Bár gyakori az indexelés ütemezése, az indexelő igény szerint is meghívható a [Futtatás paranccsal:](https://docs.microsoft.com/rest/api/searchservice/run-indexer)

    POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=2019-05-06
    api-key: [Search service admin key]

> [!NOTE]
> Ha az API futtatása sikeresen visszatér, az indexelő meghívása ütemezve van, de a tényleges feldolgozás aszinkron módon történik. 

Figyelheti az indexelő állapotát a portálon vagy a Get Indexer Status API-n keresztül. 

<a name="GetIndexerStatus"></a>

## <a name="get-indexer-status"></a>Indexelő állapotának beszereznie

Az indexelő állapotának beolvasása parancs segítségével lekérheti az indexelő állapotát és végrehajtási [előzményeit:](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)


    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2019-05-06
    api-key: [Search service admin key]

A válasz tartalmazza az indexelő általános állapotát, az utolsó (vagy folyamatban lévő) indexelő meghívást és a legutóbbi indexelő-meghívások előzményeit.

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

A végrehajtási előzmények legfeljebb 50 legutóbbi befejezett végrehajtást tartalmaznak, amelyek fordított időrendi sorrendben vannak rendezve (így a legutóbbi végrehajtás az első a válaszban).

## <a name="next-steps"></a>További lépések
Az alapok megismerése után következő lépés a követelmények és az egyes adatforrástípusokra jellemző feladatok áttekintése.

* [Azure SQL Database vagy SQL Server egy Azure virtuális gépen](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
* [Azure Table Storage](search-howto-indexing-azure-tables.md)
* [CSV-blobok indexelése az Azure Cognitive Search Blob indexelővel](search-howto-index-csv-blobs.md)
* [JSON-blobok indexelése az Azure Cognitive Search Blob indexelővel](search-howto-index-json-blobs.md)
