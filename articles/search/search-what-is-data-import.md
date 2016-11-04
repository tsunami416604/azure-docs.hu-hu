---
title: Adatfeltöltés az Azure Search szolgáltatásban | Microsoft Docs
description: Megismerkedhet az adatfeltöltéssel az Azure Search szolgáltatás indexébe.
services: search
documentationcenter: ''
author: ashmaka
manager: ''
editor: ''
tags: ''

ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 08/29/2016
ms.author: ashmaka

---
# Adatfeltöltés az Azure Search szolgáltatásba
> [!div class="op_single_selector"]
> * [Áttekintés](search-what-is-data-import.md)
> * [.NET](search-import-data-dotnet.md)
> * [REST](search-import-data-rest-api.md)
> 
> 

## Adatfeltöltési modellek az Azure Search szolgáltatásban
Az Azure Search-index adatokkal történő feltöltésének kétféle módja létezik. Az első lehetőség az adatok manuális elküldése az indexbe az Azure Search [REST API](search-import-data-rest-api.md)-jának vagy [.NET SDK](search-import-data-dotnet.md)-jának használatával. A második lehetőség egy [támogatott adatforrás átirányítása](search-indexer-overview.md) az Azure Search-indexbe, majd az adatok Search-szolgáltatásba történő automatikus elküldésének engedélyezése az Azure Search számára.

Ez az útmutató kizárólag az adatfeltöltés leküldéses modelljének használatával foglalkozik (ezt kizárólag a [REST API](search-import-data-rest-api.md) és a [.NET SDK](search-import-data-dotnet.md) támogatja), a leküldéses modellről azonban az alábbiakban többet is megtudhat.

### Adatok elküldése egy indexbe
Ez a megközelítés a programozás útján az Azure Search szolgáltatásba történő adatküldésre hivatkozik, amelynek révén azt kereshetővé teszi. Nagyon alacsony késleltetési követelményekkel rendelkező alkalmazások esetében (ha például arra van szükség, hogy a keresési műveletek szinkronizálva legyenek a dinamikus leltáradatbázissal) kizárólag a leküldéses modell használható.

Az adatokat az indexbe a [REST API](https://msdn.microsoft.com/library/azure/dn798930.aspx) vagy a [.NET SDK](search-import-data-dotnet.md) használatával küldheti el. A portálon keresztül történő adatleküldéshez jelenleg nincsenek támogató eszközök.

Ez a megközelítés rugalmasabb a lekéréses modellnél, mivel dokumentumokat feltölthet egyedileg, illetve kötegek formájában is (kötegenként legfeljebb 1000 darabot vagy 16 MB-t, amelyik a kisebb). A leküldéses modell az Azure Search szolgáltatásba történő dokumentumfeltöltést – annak helyétől függetlenül – szintén lehetővé teszi.

### Adatok lekérése indexbe
A lekéréses modell feltérképezi a támogatott adatforrást, majd automatikusan feltölti az adatokat az Azure Search-indexbe. Az új dokumentumok felismerésén kívül az indexelők a meglévő dokumentumok módosításainak és a törléseinek nyomon követésével küszöbölik ki az aktív adatkezelés szükségességét az indexben.

Az Azure Search szolgáltatásban ez a képesség az *indexelőkön* keresztül valósul meg, ami jelenleg a [Blob Storage (előnézet)](search-howto-indexing-azure-blob-storage.md), a [DocumentDB](http://aka.ms/documentdb-search-indexer), az [Azure SQL Database és az Azure virtuális gépekhez készült SQL Server](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md) számára érhető el.

Az indexelő funkció az [Azure portálon](search-import-data-portal.md) és a [REST API](https://msdn.microsoft.com/library/azure/dn946891.aspx) részeként van közzétéve.

<!--HONumber=Sep16_HO4-->


