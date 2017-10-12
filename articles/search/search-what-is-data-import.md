---
title: "Adatfeltöltés az Azure Search szolgáltatásba | Microsoft Docs"
description: "Megismerkedhet az adatfeltöltéssel az Azure Search szolgáltatás indexébe."
services: search
documentationcenter: 
author: ashmaka
manager: jhubbard
editor: 
tags: 
ms.assetid: aa8d47c1-4ae6-4209-a8ce-48d5a9474707
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 05/01/2017
ms.author: ashmaka
ms.openlocfilehash: 5a601b75ec67824e72d8736bc3c45f8e1231ca86
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="upload-data-to-azure-search"></a>Adatfeltöltés az Azure Search szolgáltatásba
> [!div class="op_single_selector"]
> * [Áttekintés](search-what-is-data-import.md)
> * [.NET](search-import-data-dotnet.md)
> * [REST](search-import-data-rest-api.md)
> 
> 

Az index adatokkal történő feltöltésére kétféle módszer létezik. Az első lehetőség az adatok manuális elküldése az indexbe az Azure Search [REST API](search-import-data-rest-api.md)-jának vagy [.NET SDK](search-import-data-dotnet.md)-jának használatával. A második lehetőség egy [támogatott adatforrás átirányítása](search-indexer-overview.md) az indexbe, majd az adatok automatikus lekérésének engedélyezése az Azure Search számára.

## <a name="push-data-to-an-index"></a>Adatok elküldése egy indexbe
Ez a megközelítés a programozás útján az Azure Search szolgáltatásba történő adatküldésre hivatkozik, amelynek révén azt kereshetővé teszi. Nagyon alacsony késleltetési követelményekkel rendelkező alkalmazások esetében (ha például arra van szükség, hogy a keresési műveletek szinkronizálva legyenek a dinamikus leltáradatbázissal) kizárólag a leküldéses modell használható.

Az adatokat az indexbe a [REST API](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents) vagy a [.NET SDK](search-import-data-dotnet.md) használatával küldheti el. A portálon keresztül történő adatleküldéshez jelenleg nincsenek támogató eszközök.

Ez a megközelítés rugalmasabb a lekéréses modellnél, mivel dokumentumokat feltölthet egyedileg, illetve kötegek formájában is (kötegenként legfeljebb 1000 darabot vagy 16 MB-t, amelyik a kisebb). A leküldéses modell az Azure Search szolgáltatásba történő dokumentumfeltöltést – annak helyétől függetlenül – szintén lehetővé teszi.

Az Azure Search által felismert adatformátum a JSON, és az adatkészletben lévő mindegyik dokumentumnak rendelkeznie kell az indexsémában meghatározott mezőkre leképezhető mezőkkel. 

## <a name="pull-data-into-an-index"></a>Adatok lekérése indexbe
A lekéréses modell feltérképezi a támogatott adatforrást, majd automatikusan feltölti az adatokat az indexbe. Az Azure Search szolgáltatásban ez a képesség az *indexelőkön* keresztül valósul meg, ami jelenleg a [Blob Storage](search-howto-indexing-azure-blob-storage.md), a [Table Storage](search-howto-indexing-azure-tables.md), az [Azure Cosmos DB](http://aka.ms/documentdb-search-indexer), az [Azure SQL Database és az Azure virtuális gépekhez készült SQL Server](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) számára érhető el. 

Az indexelők indexeket csatlakoztatnak az adatforrásokhoz (általában táblákhoz, nézetekhez vagy ezekkel egyenértékű struktúrákhoz), és leképezik a forrásmezőket a megfelelő mezőkre az indexben. A végrehajtás során a sorkészlet automatikusan át lesz alakítva JSON formátumba, és be lesz töltve a meghatározott indexbe. Minden indexelő támogatja az ütemezést, így meghatározhatja, hogy az adatok milyen gyakran legyenek frissítve. A legtöbb indexelő biztosít változáskövetési funkciókat, ha az adatforrás támogatja azokat. Az új dokumentumok felismerésén kívül az indexelők a meglévő dokumentumok módosításainak és a törléseinek nyomon követésével küszöbölik ki az aktív adatkezelés szükségességét az indexben. 

Az indexelő funkció az [Azure Portalon](search-import-data-portal.md), a [REST API](/rest/api/searchservice/Indexer-operations) és a [.NET SDK](/dotnet/api/microsoft.azure.search.indexersoperations) részeként van közzétéve. 

A portál használatának egyik előnye, hogy az Azure Search általában képes létrehozni egy alapértelmezett indexsémát a forrásadatkészlet metaadatainak kiolvasásával. A létrehozott indexet annak feldolgozásáig módosíthatja, azt követően azonban csak azok a sémamódosítások engedélyezettek, amelyekhez újraindexelés nem szükséges. Ha a végrehajtani kívánt módosítások közvetlen hatással vannak a sémára, újra kell építenie az indexet. 

Az index feltöltése után a **Keresési ablak** használható ellenőrzésre a portál parancssorában.

## <a name="query-an-index-using-search-explorer"></a>Index lekérdezése a Keresési ablak használatával

A dokumentumfeltöltés előzetes gyors ellenőrzésére használhatja a portálon a **Keresési ablakot**. A keresési ablak segítségével bármiféle kód írása nélkül kérdezheti le az indexeket. A keresési funkció alapértelmezett beállításokon alapul, mint az [egyszerű szintaxis](/rest/api/searchservice/simple-query-syntax-in-azure-search) és az alapértelmezett [searchMode lekérdezési paraméter](/rest/api/searchservice/search-documents). A rendszer az eredményeket JSON-formátumban adja vissza, így a teljes dokumentum vizsgálható.

> [!TIP]
> Számos [Azure Search kódminta](https://github.com/Azure-Samples/?utf8=%E2%9C%93&query=search) tartalmaz beágyazott vagy használatra kész adatkészleteket, így segítséget nyújt az első lépésekhez. A portál emellett egy mintaindexelőt és egy adatforrást is kínál, amely egy kisméretű ingatlan-adatkészletet tartalmaz („realestate-us-sample” néven). Amikor futtatja az előre konfigurált indexelőt a mintaadatforráson, a rendszer egy indexet hoz létre, és feltölti dokumentumokkal, amelyek a Keresési ablakban vagy egy erre írt kóddal lekérdezhetők.