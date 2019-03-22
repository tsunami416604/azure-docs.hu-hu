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
ms.openlocfilehash: 7d95ae1f750c59c121e998c6f51f9439b1b0339a
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58287094"
---
# <a name="data-import-overview---azure-search"></a>Adatok importálása áttekintése – Azure Search

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

Minden egyes módszert szeretné megismerni, lásd: [a rövid útmutató: A PowerShell és a REST API használatával az Azure Search-index létrehozása](search-create-index-rest-api.md) vagy [a rövid útmutató: Az Azure Search-index létrehozása C# ](search-import-data-dotnet.md).

<a name="indexing-actions"></a>

### <a name="indexing-actions-upload-merge-mergeorupload-delete"></a>Indexelő műveletek: feltöltés, egyesítés, mergeOrUpload, törlése

A dokumentum alapon, indexelési művelet típusát adja meg, hogy a dokumentum fel kell tölteni a teljes körű, egyesített meglévő dokumentum tartalommal vagy törölt szabályozhatja.

A REST API-ban ki az Azure Search-index végponti URL-cím a JSON-kéréstörzsekkel HTTP POST-kérelmet. A "value" tömbben található minden JSON-objektum tartalmazza a dokumentum kulcsát, és adja meg az indexelési művelet hozzáadások, a frissítések, vagy a dokumentumok tartalmának törlése. A kód példa: [dokumentumok betöltése](search-create-index-rest-api.md#load-documents).

A .NET SDK-csomag fel az adatokat egy `IndexBatch` objektum. Egy `IndexBatch` magában foglalja egy gyűjteményét `IndexAction` objektumok, amelyek mindegyike tartalmaz egy olyan dokumentumot vagy tulajdonságot, amely az Azure Search az adott dokumentumon végrehajtandó műveletet. A kód példa: [IndexBatch létrehozása](search-import-data-dotnet.md#construct-indexbatch).


| @search.action | Leírás | Az egyes dokumentumok kötelező mezői | Megjegyzések |
| -------------- | ----------- | ---------------------------------- | ----- |
| `upload` |Az `upload` művelet működése hasonló az „upsert” (frissítés/beszúrás) műveletéhez, ahol a rendszer az új dokumentumot beilleszti, ha pedig már létező dokumentumról van szó, akkor frissíti/kicseréli azt. |billentyű, továbbá a meghatározni kívánt egyéb mezők |Létező dokumentum frissítése/cseréje esetén a kérésben nem megadott mezők beállítása a következő lesz: `null`. Ez történik abban az esetben is, ha a mező korábban nem null értékre lett beállítva. |
| `merge` |Egy meglévő dokumentumot frissít a megadott mezőkkel. Ha a dokumentum nem található az indexben, az egyesítés meg fog hiúsulni. |billentyű, továbbá a meghatározni kívánt egyéb mezők |A rendszer az egyesítési művelet során megadott mezőkre cseréli a dokumentum meglévő mezőit. A .NET SDK-ban, ez típusú mezőket tartalmaz `DataType.Collection(DataType.String)`. A REST API-ban, ez típusú mezőket tartalmaz `Collection(Edm.String)`. Ha például a dokumentum egy `["budget"]` értékű `tags` mezőt tartalmaz, és egyesítést hajt végre a `tags` mező `["economy", "pool"]` értékével, a `tags` mező végső értéke `["economy", "pool"]` lesz. Nem pedig a következő lesz: `["budget", "economy", "pool"]`. |
| `mergeOrUpload` |Ha az indexben már létezik az adott kulccsal ellátott dokumentum, ezen művelet viselkedése hasonló lesz a `merge` műveletéhez. Ha nem létezik ilyen dokumentum, a művelet viselkedése az `upload` új dokumentum esetében mutatott viselkedésének fog megfelelni. |billentyű, továbbá a meghatározni kívánt egyéb mezők |- |
| `delete` |Eltávolítja a megadott dokumentumot az indexből. |csak billentyű |A rendszer figyelmen kívül hagyja a kulcsmezőn kívül megadott mezőket. Ha egyetlen mezőt kíván eltávolítani a dokumentumból, e helyett használja a `merge` műveletet, és a mező számára explicit módon adja meg a null értéket. |

## <a name="decide-which-indexing-action-to-use"></a>A használni kívánt indexelési művelet megadása
A .NET SDK-t, (feltöltés, egyesítés, törlése és mergeOrUpload) használata az adatok importálásához. Attól függően, hogy az alábbi műveletek közül melyiket választja ki, az egyes dokumentumok esetében csak bizonyos mezők lesznek kötelezően megjelenítendők:


### <a name="formulate-your-query"></a>A lekérdezés meghatározása
Kétféleképpen [keresheti meg az indexet a REST API használatával](https://docs.microsoft.com/rest/api/searchservice/Search-Documents). Az egyik lehetőség egy HTTP POST kérés kiadása azon a helyen, ahol a lekérdezési paraméterek vannak meghatározva a kéréstörzs JSON-objektumában. A másik lehetőség egy HTTP GET kérés kiadása azon a helyen, ahol a lekérdezési paraméterek vannak meghatározva a kérés URL-címén belül. A lekérdezési paraméterek méretének tekintetében a POST több [enyhe korlátozással](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) rendelkezik, mint a GET. Éppen ezért a POST használatát javasoljuk, hacsak nem állnak fenn olyan speciális körülmények, amelyek a GET használatát kényelmesebbé tennék.

A POST és a GET esetében egyaránt meg kell majd adnia a *szolgáltatás nevét*, az *index nevét*, valamint a megfelelő *API-verziót* (a jelen dokumentum kiadásakor érvényes API-verzió: `2017-11-11`) a kérés URL-címében. A GET esetében a lekérdezési paramétereket az URL-cím végén található *lekérdezési sztringben* kell megadni. Az URL-cím formátuma alább látható:

    https://[service name].search.windows.net/indexes/[index name]/docs?[query string]&api-version=2017-11-11

A POST esetében a formátum ugyanez, azzal a kiegészítéssel, hogy a lekérdezési sztring paraméterei között csak az API-verzió szerepel.


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
