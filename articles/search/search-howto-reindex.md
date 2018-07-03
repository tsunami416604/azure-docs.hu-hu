---
title: Az Azure Search index vagy frissítés kereshető tartalom újraépítése |} A Microsoft Docs
description: Adja hozzá az új elemeket, frissítse a meglévő elemeket vagy dokumentumok vagy elavult dokumentumok Újraépítés teljes vagy részleges növekményes indexelő frissítése az Azure Search-index törlése.
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: heidist
ms.openlocfilehash: 374e7601169647f0eb7d3a214cf15567b7b11090
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2018
ms.locfileid: "34641424"
---
# <a name="how-to-rebuild-an-azure-search-index"></a>Hogyan lehet Azure Search-index újraépítése

Megváltozik egy index struktúrája módosítása a fizikai kifejezést az Azure Search szolgáltatásban az index újraépítése. Ezzel szemben index frissítése a tartalmat csak frissítés közreműködő külső adatforrásból a legújabb módosításainak életbe léptetéséhez. Ez a cikk az indexek frissítése szerkezetileg és érdemben iránya biztosít.

A szolgáltatási szintű olvasási és írási engedélyek szükségesek index frissítéseit. Programozott módon meghívhatja REST vagy a .NET API-k teljes rebuild vagy növekményes indexelését a tartalmat, a frissítési beállítások megadása paraméterekkel. 

Frissítéséhez az index általában az igény szerinti. Azonban a forrás-specifikus segítségével indexek [indexelők](search-indexer-overview.md), egy beépített ütemezési is használhatja. A scheduler támogatja a dokumentum frissítése, ahányszor függetlenül időköz és szüksége van a minta legfeljebb 15 percenként. A gyorsabb frissítési gyakoriság szükséges index frissítések terjesztése manuálisan, például egy double-írási tranzakció, mind a külső adatforrást, és az Azure Search-index frissítése egyidejűleg keresztül.

## <a name="full-rebuilds"></a>Teljes újraépíteni

Számos különböző típusú frissítések egy teljes Újraépítés szükség. Teljesen újra kell építenie az indexet, adatok és a metaadatok, kiegészítve a külső adatforrásokból az index elszaporításával törlését jelenti. Programozott módon [törlése](https://docs.microsoft.com/rest/api/searchservice/delete-index), [létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-index), és [töltse be újra](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) újraépíteni az indexet. 

Utáni újraépítése, ne feledje, hogy ha Ön rendelkezik lett tesztelés lekérdezési minták és pontozási profilok, várható variation a lekérdezési eredmények között, ha a mögöttes tartalmak módosultak.

## <a name="when-to-rebuild"></a>Mikor érdemes újraépítése

A gyakori, teljes csomag újraépíti aktív fejlesztés során, amikor index sémák fluxus állapotban vannak.

| Módosítása | Építse újra állapota|
|--------------|---------------|
| Módosítsa a mező nevét a adattípus, vagy a [indexattribútumok](https://docs.microsoft.com/rest/api/searchservice/create-index) | Egy mező definíció általában költségével egyezik meg a Újraépítés büntetés kivételével ezek [indexattribútumok](https://docs.microsoft.com/rest/api/searchservice/create-index): lekérhető, SearchAnalyzer, SynonymMaps. Lekérhető, SearchAnalyzer és SynonymMaps attribútumok anélkül, hogy az index újraépítése kellene hozzáadhat existující Pole.|
| Adjon hozzá egy mezőt | Az újjáépítést nem szigorú követelmény. Meglévő indexelt dokumentumok adják meg az új mező értéke null. Egy jövőbeli reindex a forrásadatok értékeket cserélje le az Azure Search által hozzáadott null értékeket. |
| Mező törlése | Azure Search-index közvetlenül egy mező nem lehet törölni. Ehelyett az alkalmazás figyelmen kívül hagyása elkerülése érdekében használja a "törölt" mezőben kell rendelkeznie. Fizikailag a mező definíciója és annak tartalmát marad az index a következő alkalommal, amikor újra kell építeni az indexet egy sémát, amely az áttekinthetőség kedvéért kihagyja az adott mező használatával.|

> [!Note]
> Újjáépítést is el kell, ha úgy vált szinten. Ha később úgy dönt, a további kapacitást, nem nem helyszíni frissítését. Egy új szolgáltatás, létre kell hozni az új kapacitást időpontban, és teljesen új indexek kell elkészíteni az új szolgáltatás a. 

## <a name="partial-or-incremental-indexing"></a>Részleges vagy növekményes indexelése

Ha az index éles környezetben, kerülnek a figyelem középpontjába növekményes indexelést, általában a lekérdezésteljesítmény service szolgáltatások. Indexelő növekményes teljes vagy részleges, illetve egy csak tartalom folyamat, amely szinkronizálja a keresési index tartalmát meghatározó adatforrás állapotát tükröző tartalmát. Egy dokumentum felvett vagy törölt a forrás hozzáadása vagy törlése az indexbe. A kódban, hívja a [törlése dokumentumok hozzáadása, frissítése vagy](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) művelet vagy a .NET-megfelelője.

> [!Note]
> Indexelők, amelyek feltérképeznek külső adatforrások használatakor a forrásrendszerben lévő változáskövetést mechanizmusok használja vannak növekményes indexelő. A [Azure Blob storage](search-howto-indexing-azure-blob-storage.md#incremental-indexing-and-deletion-detection), amely egy `lastModified` mezővel. A [Azure Table storage](search-howto-indexing-azure-tables.md#incremental-indexing-and-deletion-detection), `timestamp` ugyanazt a célt szolgálja. Ehhez hasonlóan mindkét [Azure SQL Database indexelő](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows) és [Azure Cosmos DB-indexelő](search-howto-index-cosmosdb.md#indexing-changed-documents) mező a sor frissítések megjelölése. Az indexelők kapcsolatos további információkért lásd: [indexelő áttekintése](search-indexer-overview.md).


## <a name="see-also"></a>Lásd még

+ [Az indexelő áttekintése](search-indexer-overview.md)
+ [Index nagy mennyiségű nagy méretű adatkészletekhez](search-howto-large-index.md)
+ [Az indexelés a portálon](search-import-data-portal.md)
+ [Azure SQL Database-indexelő](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Cosmos DB-indexelő](search-howto-index-cosmosdb.md)
+ [Azure Blob Storage-indexelő](search-howto-indexing-azure-blob-storage.md)
+ [Azure Table Storage-indexelő](search-howto-indexing-azure-tables.md)
+ [Biztonság az Azure Search szolgáltatásban](search-security-overview.md)