---
title: Indexelő az adatbejárás során az importálás során
titleSuffix: Azure Cognitive Search
description: Az Azure SQL Database, Azure Cosmos DB vagy Azure Storage bejárásával kinyerheti a kereshető adatokat, és feltöltheti az Azure Cognitive Search indexét.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 09a1b8e54726accf705515bf077f6a599f0f9b78
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74266920"
---
# <a name="indexers-in-azure-cognitive-search"></a>Indexelő az Azure Cognitive Search

Az Azure Cognitive Search *Indexelő* egy olyan webbejáró, amely a kereshető adatokat és metaadatokat kinyeri egy külső Azure-adatforrásból, és az index és az adatforrás közötti mező – mező leképezések alapján tölti fel az indexet. Ezt a módszert más néven "lekéréses modellnek" is nevezzük, mert a szolgáltatás olyan kódot kér le, amely nem rendelkezik olyan kóddal, amely az adott indexbe felveszi az adattípust.

Az indexelő az adatforrások típusain vagy platformokon alapulnak, és az Azure-ban, a Cosmos db, az Azure Table Storage és a blob Storage SQL Server indexelő egyedi indexekkel rendelkeznek. A blob Storage-indexelő a blob tartalomtípusokhoz tartozó további tulajdonságokkal rendelkeznek.

Az indexelőt használhatja kizárólag adatfeldolgozásra, illetve olyan kombinált műveletekben is, amelyek részeként az indexelő használatával csak az index bizonyos mezőinek betöltése történik meg.

Az Indexelő szolgáltatást igény szerint vagy ismétlődő adatfrissítési ütemezés szerint futtathatja, amely akár öt percenként is futtatható. A gyakoribb frissítések esetében olyan leküldéses modellre van szükség, amely egyszerre frissíti az Azure Cognitive Search és a külső adatforrás adatait is.

## <a name="approaches-for-creating-and-managing-indexers"></a>Indexelők létrehozásának és kezelésének módszerei

Az indexelők létrehozása és kezelése a következő módszerekkel történhet:

* [Portál > adatimportálás varázsló](search-import-data-portal.md)
* [Szolgáltatás REST API-ja](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations)

Az új indexelőket először előzetes verziójú funkcióként vezetjük be. Az előzetes verziójú funkciók API-kban (REST és .NET) kerülnek bemutatásra, majd később, azok általánosan elérhetővé tétele után integráljuk őket a portál rendszerével. Új indexelő kiértékelése esetén érdemes számolni azzal, hogy kódírásra is sor fog kerülni.

## <a name="permissions"></a>Engedélyek

Az indexelő rendszerhez kapcsolódó összes művelet, beleértve az állapot vagy a definíciók beszerzésére irányuló kéréseket, [rendszergazdai API-kulcs](search-security-api-keys.md)szükséges. 

<a name="supported-data-sources"></a>

## <a name="supported-data-sources"></a>Támogatott adatforrások

Az indexelő adattárakat térképez fel az Azure-ban.

* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
* [Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md) (előzetes verzió)
* [Azure Table Storage](search-howto-indexing-azure-tables.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [SQL Server az Azure Virtual Machines szolgáltatásban](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)
* [SQL felügyelt példányok az Azure-ban](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)

## <a name="basic-configuration-steps"></a>Alapszintű konfigurációs lépések
Az indexelők az adott adatforrások esetében egyedi funkciókat biztosítanak. Ezért az indexelő- vagy az adatforrás-konfiguráció egyes szempontjai az indexelő típusától függően változnak. Az alapvető felépítés és követelmények azonban minden indexelő esetében azonosak. Az alábbiakban az összes indexelőre érvényes lépések láthatóak.

### <a name="step-1-create-a-data-source"></a>1\. lépés: Adatforrás létrehozása
Az indexelő beolvassa az adatforrás-kapcsolatokat *egy adatforrás-objektumból* . Az adatforrás-definíció egy kapcsolati karakterláncot és esetleg hitelesítő adatokat biztosít. Az erőforrás létrehozásához hívja meg a [create datasource](https://docs.microsoft.com/rest/api/searchservice/create-data-source) REST API vagy az [DataSource osztályt](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource) .

Az adatforrások konfigurálása és kezelése az azokat használó indexelőktől függetlenül történik, ami azt jelenti, hogy egy adatforrást több indexelő is használhat egyidejűleg, egynél több index betöltésére.

### <a name="step-2-create-an-index"></a>2\. lépés: Index létrehozása
Az indexelők automatizálni tudják az adatfeldolgozáshoz kapcsolódó bizonyos feladatokat, de az indexek létrehozása nem tartozik ezek közé. Előfeltételként olyan előre meghatározott indexre van szükség, amelynek mezői egyeznek a külső adatforrás mezőivel. A mezőknek meg kell egyezniük a név és az adattípus alapján. Az indexek strukturálásával kapcsolatos további információkért lásd: [index létrehozása (Azure Cognitive Search REST API)](https://docs.microsoft.com/rest/api/searchservice/Create-Index) vagy [index osztály](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index). A mezők társításával kapcsolatos segítségért lásd: [mező-hozzárendelések az Azure Cognitive Search indexelő](search-indexer-field-mappings.md)szolgáltatásban.

> [!Tip]
> Az indexelők nem tudnak indexet létrehozni Önnek, de a portál **Adatok importálása** varázslója a segítségére lehet ebben. A legtöbb esetben a varázsló következtetni tud az indexsémára a forrás meglévő metaadataiból, és előállít egy olyan előzetes indexsémát, amely beágyazott módon szerkeszthető mindaddig, amíg a varázsló aktív. Miután létrejött az index a szolgáltatásban, a további szerkesztés a portálon a legtöbb esetben új mezők hozzáadására van korlátozva. A varázsló használatát érdemes megfontolnia az indexek létrehozásakor, de az áttekintésükkor nem. A gyakorlati tanuláshoz végezze el a [portál útmutatójában](search-get-started-portal.md) foglalt lépéseket.

### <a name="step-3-create-and-schedule-the-indexer"></a>3\. lépés: Az indexelő létrehozása és ütemezése
Az indexelő definíciója egy olyan szerkezet, amely az adatfeldolgozással kapcsolatos összes elemet egyesíti. A szükséges elemek közé tartozik az adatforrás és az index. A választható elemek közé tartozik az ütemterv és a mező leképezése. A mező-hozzárendelés csak akkor választható, ha a forrás mezők és az index mezők világosan megfelelnek. Az indexelők egy másik szolgáltatásból is hivatkozhatnak egy adatforrásra, ha az adott adatforrás ugyanabból az előfizetésből származik. Az indexelő strukturálásával kapcsolatos további információkért lásd: az [Indexelő létrehozása (Azure Cognitive Search REST API)](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer).

<a id="RunIndexer"></a>

## <a name="run-indexers-on-demand"></a>Indexelő futtatása igény szerint

Habár az indexelés ütemezését közösen is megteheti, az indexelő a [Run paranccsal](https://docs.microsoft.com/rest/api/searchservice/run-indexer)is igény szerint hívható meg:

    POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=2019-05-06
    api-key: [Search service admin key]

> [!NOTE]
> Ha az API futtatása sikeresen befejeződik, az indexelő meghívása ütemezve lett, de a tényleges feldolgozás aszinkron módon történik. 

Az indexelő állapotát a portálon vagy az indexelő status API-n keresztül követheti nyomon. 

<a name="GetIndexerStatus"></a>

## <a name="get-indexer-status"></a>Indexelő állapotának beolvasása

Az indexelő állapotának és végrehajtásának előzményeit az [Indexelő állapotának lekérése paranccsal](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)kérheti le:


    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2019-05-06
    api-key: [Search service admin key]

A válasz általános indexelő állapotot, az utolsó (vagy folyamatban lévő) indexelő meghívást, valamint a legutóbbi indexelő hívás előzményeit tartalmazza.

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

A végrehajtási előzmények legfeljebb a 50 legutóbbi befejezett végrehajtást tartalmazzák, amelyek fordított időrendi sorrendben vannak rendezve (így a legutóbbi végrehajtás a válaszban elsőként jelenik meg).

## <a name="next-steps"></a>Következő lépések
Az alapok megismerése után következő lépés a követelmények és az egyes adatforrástípusokra jellemző feladatok áttekintése.

* [Azure SQL Database vagy SQL Server egy Azure virtuális gépen](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
* [Azure Table Storage](search-howto-indexing-azure-tables.md)
* [CSV-Blobok indexelése az Azure Cognitive Search blob indexelő használatával](search-howto-index-csv-blobs.md)
* [JSON-Blobok indexelése az Azure Cognitive Search blob indexelő](search-howto-index-json-blobs.md)
