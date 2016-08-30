<properties
    pageTitle="Indexelők az Azure Search szolgáltatásban | Microsoft Azure | Üzemeltetett felhőalapú keresési szolgáltatás"
    description="Feltérképezi az Azure SQL Database, a DocumentDB vagy az Azure Storage tárolókat a kereshető adatok kinyeréséhez és az Azure Search-index feltöltéséhez."
    services="search"
    documentationCenter=""
    authors="HeidiSteen"
    manager="mblythe"
    editor=""
    tags="azure-portal"/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="04/14/2016"
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

Az indexelők olyan **adatforrásokból** kérnek le adatokat, amelyek például kapcsolati karakterláncokat tartalmaznak. Jelenleg a következő adatforrások támogatottak:

- [Azure SQL-adatbázis](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md) (vagy SQL Server egy Azure virtuális gépen)
- [DocumentDB](../documentdb/documentdb-search-indexer.md)
- [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md) (Jelenleg előzetes verzióban. Szöveges állomány kinyerésére PDF, Office-dokumentum, HTML, XMF formátumokból.)
- [Azure Table Storage](search-howto-indexing-azure-tables.md) (Jelenleg előzetes verzióban)

Az adatforrások konfigurálása és kezelése az azokat használó indexelőktől függetlenül történik, ami azt jelenti, hogy egy adatforrást több indexelő is használhat egyidejűleg, egynél több index betöltésére. 

Az indexelők és az adatforrások felügyeletét a [.NET SDK](https://msdn.microsoft.com/library/azure/microsoft.azure.search.iindexersoperations.aspx) és a [szolgáltatás REST API-ja](https://msdn.microsoft.com/library/azure/dn946891.aspx) egyaránt támogatja. 

Az indexelők konfigurálását a portálon az **Adatok importálása** varázsló használata közben is elvégezheti. Gyors üzembe helyezési oktatóanyagot az [Ismerkedés az Azure Search szolgáltatással a portálon](search-get-started-portal) részben talál, ahol a varázslót használó index létrehozására és betöltésére mintaadatokat és DocumentDB-indexelőt alkalmazunk.






<!--HONumber=jun16_HO2-->


