<properties
    pageTitle="Indexelők az Azure Search szolgáltatásban | Microsoft Azure | Üzemeltetett felhőalapú keresési szolgáltatás"
    description="Feltérképezi az Azure SQL Database, a DocumentDB vagy az Azure Storage tárolókat a kereshető adatok kinyeréséhez és az Azure Search-index feltöltéséhez."
    services="search"
    documentationCenter=""
    authors="HeidiSteen"
    manager="jhubbard"
    editor=""
    tags="azure-portal"/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/08/2016"
    ms.author="heidist"/>


# Indexelők az Azure Search szolgáltatásban
> [AZURE.SELECTOR]
- [Áttekintés](search-indexer-overview.md)
- [Portál](search-import-data-portal.md)
- [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)
- [DocumentDB](../documentdb/documentdb-search-indexer.md)
- [Blob Storage (előzetes verzió)](search-howto-indexing-azure-blob-storage.md)
- [Table Storage (előzetes verzió)](search-howto-indexing-azure-tables.md)

Az Azure Search szolgáltatás **indexelője** egy olyan webbejáró, amely külső adatforrásból nyer ki kereshető adatokat és metaadatokat, illetve az index és az adatforrás közötti, mezőről mezőre haladó leképezések alapján tölti fel az indexet. Ezt a megközelítést néha „lekérési modellnek” is nevezik, mivel a szolgáltatás anélkül végzi az adatok lekérését, hogy az adatokat az indexbe küldő kód írására szükség lenne.

Az indexelőt használhatja kizárólag adatfeldolgozásra, illetve olyan kombinált műveletekben is, amelyek részeként az indexelő használatával csak az index bizonyos mezőinek betöltése történik meg.

Az indexelők futtatása történhet igény szerint vagy ismétlődő adatfrissítési ütemterv alapján, akár 15 perces gyakorisággal is. Az ennél gyakoribb frissítésekhez olyan leküldési modellre van szükség, amely egyszerre frissíti az adatokat az Azure Search szolgáltatásban és a külső adatforrásban is.

## Indexelők létrehozásának és kezelésének módszerei

Az olyan, mindenki számára elérhető indexelők esetében, mint az Azure SQL vagy a DocumentDB, az indexelők létrehozása és kezelése a következő módszerekkel történhet:

- [Portál > Adatok importálása varázsló ](search-get-started-portal.md)
- [Szolgáltatás REST API-ja](https://msdn.microsoft.com/library/azure/dn946891.aspx)
- [.NET SDK](https://msdn.microsoft.com/library/azure/microsoft.azure.search.iindexersoperations.aspx)

Az olyan előzetes verziójú indexelők, mint az Azure Blob vagy Table storage használatához kód és előzetes verziójú API-k (például az [Azure Search előzetes verziójú REST API-ja](search-api-indexers-2015-02-28-preview.md)) szükségesek. A portálon történő eszközfelügyelet általában nem érhető el az előzetes verziójú funkciók esetében.

## Alapszintű konfigurációs lépések

Az indexelők az adott adatforrások esetében egyedi funkciókat biztosítanak. Ezért az indexelő- vagy az adatforrás-konfiguráció egyes szempontjai az indexelő típusától függően változnak. Az alapvető felépítés és követelmények azonban minden indexelő esetében azonosak. Az alábbiakban az összes indexelőre érvényes lépések láthatóak.

### 1. lépés: Index létrehozása

Az indexelők képesek egyes, az adatfeldolgozással kapcsolatos feladat automatikussá tételére, de az index létrehozása nem tartozik ezek közé. Előfeltételként olyan előre meghatározott indexre van szükség, amelynek mezői egyeznek a külső adatforrás mezőivel. További információk az indexek strukturálásáról: [Index létrehozása (Azure Search REST API)](https://msdn.microsoft.com/library/azure/dn798941.aspx).

### 2. lépés: Adatforrás létrehozása

Az indexelők olyan **adatforrásokból** kérnek le adatokat, amelyek például kapcsolati karakterláncokat tartalmaznak. Jelenleg a következő adatforrások támogatottak:

- [Azure SQL Database vagy SQL Server egy Azure virtuális gépen](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)
- [DocumentDB](../documentdb/documentdb-search-indexer.md)
- [Azure Blob Storage (előzetes verzió)](search-howto-indexing-azure-blob-storage.md) – szöveg kinyerésére szolgál PDF, Office-dokumentum, HTML vagy XML formátumokból
- [Azure Table Storage (előzetes verzió)](search-howto-indexing-azure-tables.md)

Az adatforrások konfigurálása és kezelése az azokat használó indexelőktől függetlenül történik, ami azt jelenti, hogy egy adatforrást több indexelő is használhat egyidejűleg, egynél több index betöltésére. 

### 3. lépés: Az indexelő létrehozása és ütemezése

Az indexelődefiníció egy olyan konstrukció, amely megadja az indexet, az adatforrást és az ütemezést. Az indexelők egy másik szolgáltatásból is hivatkozhatnak egy adatforrásra, ha az adott adatforrás ugyanabból az előfizetésből származik. További információk az indexelők strukturálásáról: [Indexelő létrehozása (Azure Search REST API)](https://msdn.microsoft.com/library/azure/dn946899.aspx).

## Következő lépések

Az alapok megismerése után következő lépés a követelmények és az egyes adatforrástípusokra jellemző feladatok áttekintése.

- [Azure SQL Database vagy SQL Server egy Azure virtuális gépen](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)
- [DocumentDB](../documentdb/documentdb-search-indexer.md)
- [Azure Blob Storage (előzetes verzió)](search-howto-indexing-azure-blob-storage.md) – szöveg kinyerésére szolgál PDF, Office-dokumentum, HTML vagy XML formátumokból
- [Azure Table Storage (előzetes verzió)](search-howto-indexing-azure-tables.md)
- [CSV-blobok indexelése az Azure Search Blob indexelőjével (előzetes verzió)](search-howto-index-csv-blobs.md)
- [JSON-blobok indexelése az Azure Search Blob indexelőjével (előzetes verzió)](search-howto-index-json-blobs.md)




<!--HONumber=Sep16_HO4-->


