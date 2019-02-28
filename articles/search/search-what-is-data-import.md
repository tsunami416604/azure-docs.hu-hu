---
title: Adatbetöltési search-indexbe – Azure Search adatok importálása
description: Töltse fel, és a külső adatforrásokból származó adatokat feltölteni az Azure Search-index.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 85a2810e8ab8de5ad2967aaf17f421d871368063
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2019
ms.locfileid: "56958456"
---
# <a name="indexing-external-data-for-queries-in-azure-search"></a>Lekérdezések az Azure Search külső adatok indexelése

Az Azure Search szolgáltatásban lekérdezések betölti és a tartalmakon futnak egy [search-index](search-what-is-an-index.md). Ez a cikk az index feltöltése két alapvető módszerét vizsgálja: *leküldéses* az indexbe az adatok programozott módon, vagy egy [Azure Search-indexelőt](search-indexer-overview.md) egy támogatott adatforrásra,  *a lekéréses* az adatok.

Bármelyik módszert használja, a cél, hogy *adatok betöltése* egy külső adatforrásból az Azure Search-index. Az Azure Search lehetővé teszi, akkor hozzon létre egy üres indexet, de leküldéses vagy adatok lekérése bele, amíg azt nem lekérdezhető.

## <a name="pushing-data-to-an-index"></a>Adatok elküldése egy indexbe
A legrugalmasabb módszer a leküldéses modell, amelyet arra használnak, hogy programozott módon adatokat küldjön az Azure Searchnek. Először is nem korlátozza az adatforrás típusát. Bármely JSON-dokumentumokból álló adatkészlet továbbítható az Azure Search-indexnek, feltéve hogy az adatkészlet minden dokumentuma tartalmaz olyan mezőket, amelyek az indexsémában meghatározott mezőkhöz vannak hozzárendelve. Emellett nem korlátozza a végrehajtás gyakoriságát sem. Tetszőleges gyakorisággal továbbíthat módosításokat az indexeknek. Nagyon alacsony késleltetési követelményekkel rendelkező alkalmazások esetében (ha például arra van szükség, hogy a keresési műveletek szinkronizálva legyenek a dinamikus leltáradatbázissal) kizárólag a leküldéses modell használható.

Ez a megközelítés rugalmasabb a lekéréses modellnél, mivel dokumentumokat feltölthet egyedileg, illetve kötegek formájában is (kötegenként legfeljebb 1000 darabot vagy 16 MB-t, amelyik a kisebb). A leküldéses modell az Azure Search szolgáltatásba történő dokumentumfeltöltést – annak helyétől függetlenül – szintén lehetővé teszi.

### <a name="how-to-push-data-to-an-azure-search-index"></a>Adatok elküldése egy Azure Search-indexbe

Az alábbi API-kat használhatja egy vagy több dokumentum indexbe való betöltésére:

+ [Dokumentumok hozzáadása, frissítése vagy törlése (REST API)](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents)
+ [indexAction osztály](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexaction?view=azure-dotnet) vagy [indexBatch osztály](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet) 

A portálon keresztül történő adatleküldéshez jelenleg nincsenek támogató eszközök.

Az egyes módszertanok ismertetésével kapcsolatban lásd az [Adatok importálása a REST használatával](search-import-data-rest-api.md) vagy az [Adatok importálása a .NET használatával](search-import-data-dotnet.md) témaköröket.


## <a name="pulling-data-into-an-index"></a>Adatok lekérése indexbe
A lekéréses modell feltérképezi a támogatott adatforrást, majd automatikusan feltölti az adatokat az indexbe. Az Azure Search szolgáltatásban ez a képesség az *indexelőkön* keresztül valósul meg, ami jelenleg az alábbi platformokon érhető el:

+ [Blob Storage](search-howto-indexing-azure-blob-storage.md)
+ [Table Storage](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](https://aka.ms/documentdb-search-indexer)
+ [Azure SQL Database és az Azure virtuális gépeken futó SQL Server](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

Az indexelők indexeket csatlakoztatnak az adatforrásokhoz (általában táblákhoz, nézetekhez vagy ezekkel egyenértékű struktúrákhoz), és leképezik a forrásmezőket a megfelelő mezőkre az indexben. A végrehajtás során a sorkészlet automatikusan át lesz alakítva JSON formátumba, és be lesz töltve a meghatározott indexbe. Minden indexelő támogatja az ütemezést, így meghatározhatja, hogy az adatok milyen gyakran legyenek frissítve. A legtöbb indexelő biztosít változáskövetési funkciókat, ha az adatforrás támogatja azokat. Az új dokumentumok felismerésén kívül az indexelők a meglévő dokumentumok módosításainak és a törléseinek nyomon követésével küszöbölik ki az aktív adatkezelés szükségességét az indexben. 


### <a name="how-to-pull-data-into-an-azure-search-index"></a>Adatok lekérése Azure Search-indexbe

Az indexelő funkció az [Azure Portalon](search-import-data-portal.md), a [REST API](/rest/api/searchservice/Indexer-operations) és a [.NET SDK](/dotnet/api/microsoft.azure.search.indexersoperationsextensions) részeként van közzétéve. 

A portál használatának egyik előnye, hogy az Azure Search általában képes létrehozni egy alapértelmezett indexsémát a forrásadatkészlet metaadatainak kiolvasásával. A létrehozott indexet annak feldolgozásáig módosíthatja, azt követően azonban csak azok a sémamódosítások engedélyezettek, amelyekhez újraindexelés nem szükséges. Ha a végrehajtani kívánt módosítások közvetlen hatással vannak a sémára, újra kell építenie az indexet. 

## <a name="verify-data-import-with-search-explorer"></a>Adatok importálásának ellenőrzése a keresési ablakkal

A dokumentum feltölt egy előzetes ellenőrzés végrehajtásához egy gyors módja **keresési ablak** a portálon. A keresési ablak segítségével bármiféle kód írása nélkül kérdezheti le az indexeket. A keresési funkció alapértelmezett beállításokon alapul, mint az [egyszerű szintaxis](/rest/api/searchservice/simple-query-syntax-in-azure-search) és az alapértelmezett [searchMode lekérdezési paraméter](/rest/api/searchservice/search-documents). A rendszer az eredményeket JSON-formátumban adja vissza, így a teljes dokumentum vizsgálható.

> [!TIP]
> Számos [Azure Search kódminta](https://github.com/Azure-Samples/?utf8=%E2%9C%93&query=search) tartalmaz beágyazott vagy használatra kész adatkészleteket, így segítséget nyújt az első lépésekhez. A portál emellett egy mintaindexelőt és egy adatforrást is kínál, amely egy kisméretű ingatlan-adatkészletet tartalmaz („realestate-us-sample” néven). Ha futtatja az előre konfigurált indexelőt a mintaadatforráson, egy index, és a dokumentumokkal, amelyek lekérdezhetők a keresési ablakban vagy által írt kóddal.

## <a name="see-also"></a>Lásd még

+ [Az indexelő áttekintése](search-indexer-overview.md)
+ [Útmutató a Portal használatához: indexek létrehozása, betöltése és lekérdezése](search-get-started-portal.md)
