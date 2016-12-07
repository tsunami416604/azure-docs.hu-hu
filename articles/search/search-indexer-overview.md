---
title: "Indexelők az Azure Search szolgáltatásban | Microsoft Docs"
description: "Feltérképezi az Azure SQL Database, a DocumentDB vagy az Azure Storage tárolókat a kereshető adatok kinyeréséhez és az Azure Search-index feltöltéséhez."
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: 34a7694c-8fd9-46b1-8900-cefdd7236323
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 10/27/2016
ms.author: heidist
translationtype: Human Translation
ms.sourcegitcommit: 2cddd262f912efff029bb05f0f3f3551409167ed
ms.openlocfilehash: 4c969499f562d8cef9c09ebd909508b1c0542c57

---

# <a name="indexers-in-azure-search"></a>Indexelők az Azure Search szolgáltatásban
> [!div class="op_single_selector"]
>
> * [Áttekintés](search-indexer-overview.md)
> * [Portál](search-import-data-portal.md)
> * [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
> * [DocumentDB](../documentdb/documentdb-search-indexer.md)
> * [Blob Storage (előzetes verzió)](search-howto-indexing-azure-blob-storage.md)
> * [Table Storage (előzetes verzió)](search-howto-indexing-azure-tables.md)
>
>

Az Azure Search szolgáltatás **indexelője** egy olyan webbejáró, amely külső adatforrásból nyer ki kereshető adatokat és metaadatokat, illetve az index és az adatforrás közötti, mezőről mezőre haladó leképezések alapján tölti fel az indexet. Ezt a megközelítést néha „lekérési modellnek” is nevezik, mivel a szolgáltatás anélkül végzi az adatok lekérését, hogy az adatokat az indexbe küldő kód írására szükség lenne.

Az indexelőt használhatja kizárólag adatfeldolgozásra, illetve olyan kombinált műveletekben is, amelyek részeként az indexelő használatával csak az index bizonyos mezőinek betöltése történik meg.

Az indexelők futtatása történhet igény szerint vagy ismétlődő adatfrissítési ütemterv alapján, akár 15 perces gyakorisággal is. Az ennél gyakoribb frissítésekhez olyan leküldési modellre van szükség, amely egyszerre frissíti az adatokat az Azure Search szolgáltatásban és a külső adatforrásban is.

## <a name="approaches-for-creating-and-managing-indexers"></a>Indexelők létrehozásának és kezelésének módszerei
Az olyan, mindenki számára elérhető indexelők esetében, mint az Azure SQL vagy a DocumentDB, az indexelők létrehozása és kezelése a következő módszerekkel történhet:

* [Portál > Adatok importálása varázsló ](search-get-started-portal.md)
* [Szolgáltatás REST API-ja](https://msdn.microsoft.com/library/azure/dn946891.aspx)
* [.NET SDK](https://msdn.microsoft.com/library/azure/microsoft.azure.search.iindexersoperations.aspx)

## <a name="basic-configuration-steps"></a>Alapszintű konfigurációs lépések
Az indexelők az adott adatforrások esetében egyedi funkciókat biztosítanak. Ezért az indexelő- vagy az adatforrás-konfiguráció egyes szempontjai az indexelő típusától függően változnak. Az alapvető felépítés és követelmények azonban minden indexelő esetében azonosak. Az alábbiakban az összes indexelőre érvényes lépések láthatóak.

### <a name="step-1-create-an-index"></a>1. lépés: Index létrehozása
Az indexelők képesek egyes, az adatfeldolgozással kapcsolatos feladat automatikussá tételére, de az index létrehozása nem tartozik ezek közé. Előfeltételként olyan előre meghatározott indexre van szükség, amelynek mezői egyeznek a külső adatforrás mezőivel. További információk az indexek strukturálásáról: [Index létrehozása (Azure Search REST API)](https://msdn.microsoft.com/library/azure/dn798941.aspx).

### <a name="step-2-create-a-data-source"></a>2. lépés: Adatforrás létrehozása
Az indexelők olyan **adatforrásokból** kérnek le adatokat, amelyek például kapcsolati karakterláncokat tartalmaznak. Jelenleg a következő adatforrások támogatottak:

* [Azure SQL Database vagy SQL Server egy Azure virtuális gépen](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [DocumentDB](../documentdb/documentdb-search-indexer.md)
* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md) – szöveg kinyerésére szolgál PDF, Office-dokumentum, HTML- vagy XML formátumokból
* [Azure Table Storage](search-howto-indexing-azure-tables.md)

Az adatforrások konfigurálása és kezelése az azokat használó indexelőktől függetlenül történik, ami azt jelenti, hogy egy adatforrást több indexelő is használhat egyidejűleg, egynél több index betöltésére.

### <a name="step-3create-and-schedule-the-indexer"></a>3. lépés: Az indexelő létrehozása és ütemezése
Az indexelődefiníció egy olyan konstrukció, amely megadja az indexet, az adatforrást és az ütemezést. Az indexelők egy másik szolgáltatásból is hivatkozhatnak egy adatforrásra, ha az adott adatforrás ugyanabból az előfizetésből származik. További információk az indexelők strukturálásáról: [Indexelő létrehozása (Azure Search REST API)](https://msdn.microsoft.com/library/azure/dn946899.aspx).

## <a name="next-steps"></a>Következő lépések
Az alapok megismerése után következő lépés a követelmények és az egyes adatforrástípusokra jellemző feladatok áttekintése.

* [Azure SQL Database vagy SQL Server egy Azure virtuális gépen](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [DocumentDB](../documentdb/documentdb-search-indexer.md)
* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md) – szöveg kinyerésére szolgál PDF, Office-dokumentum, HTML- vagy XML formátumokból
* [Azure Table Storage](search-howto-indexing-azure-tables.md)
* [CSV-blobok indexelése az Azure Search Blob indexelőjével (előzetes verzió)](search-howto-index-csv-blobs.md)
* [JSON-blobok indexelése az Azure Search Blob indexelőjével (előzetes verzió)](search-howto-index-json-blobs.md)



<!--HONumber=Nov16_HO3-->


