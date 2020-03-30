---
title: Adatbetöltés importálása és adatbetöltés a keresési indexekben
titleSuffix: Azure Cognitive Search
description: Feltöltheti és feltöltheti az adatokat egy indexbe az Azure Cognitive Search-ben külső adatforrásokból.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: cc3f38e9bb96ce76263a3124f8bfdc49dc638bfd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282756"
---
# <a name="data-import-overview---azure-cognitive-search"></a>Adatimportálás – Azure Cognitive Search

Az Azure Cognitive Search alkalmazásban a lekérdezések a [keresési indexbe](search-what-is-an-index.md)betöltött és mentett tartalomfelett futnak. Ez a cikk az index feltöltésének két alapvető megközelítését vizsgálja: az adatok *leküldése* az indexbe programozott módon, vagy egy [Azure Cognitive Search indexelő](search-indexer-overview.md) egy támogatott adatforrásra való rámutatásaz adatok *lekérése.*

Mindkét megközelítés, a cél az adatok *betöltése* egy külső adatforrásból egy Azure Cognitive Search indexbe. Az Azure Cognitive Search lehetővé teszi, hogy hozzon létre egy üres indexet, de amíg nem lekérdezi vagy lekéri az adatokat, nem lekérdezhető.

## <a name="pushing-data-to-an-index"></a>Adatok elküldése egy indexbe
A leküldéses modell, amely et programozott módon küldi el az Azure Cognitive Search, a legrugalmasabb megközelítés. Először is nem korlátozza az adatforrás típusát. A JSON-dokumentumokból álló adatkészletek lelökhetők egy Azure Cognitive Search-indexbe, feltéve, hogy az adatkészlet minden egyes dokumentuma rendelkezik az indexsémában definiált mezőkre való leképezésmezőivel. Emellett nem korlátozza a végrehajtás gyakoriságát sem. Tetszőleges gyakorisággal továbbíthat módosításokat az indexeknek. Nagyon alacsony késleltetési követelményekkel rendelkező alkalmazások esetében (ha például arra van szükség, hogy a keresési műveletek szinkronizálva legyenek a dinamikus leltáradatbázissal) kizárólag a leküldéses modell használható.

Ez a megközelítés rugalmasabb a lekéréses modellnél, mivel dokumentumokat feltölthet egyedileg, illetve kötegek formájában is (kötegenként legfeljebb 1000 darabot vagy 16 MB-t, amelyik a kisebb). A leküldéses modell azt is lehetővé teszi, hogy dokumentumokat töltsön fel az Azure Cognitive Search, függetlenül attól, hogy hol az adatok.

### <a name="how-to-push-data-to-an-azure-cognitive-search-index"></a>Adatok leküldése az Azure Cognitive Search indexébe

Az alábbi API-kat használhatja egy vagy több dokumentum indexbe való betöltésére:

+ [Dokumentumok hozzáadása, frissítése vagy törlése (REST API)](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents)
+ [indexAction osztály](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexaction?view=azure-dotnet) vagy [indexBatch osztály](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet) 

A portálon keresztül történő adatleküldéshez jelenleg nincsenek támogató eszközök.

Az egyes módszertanok bemutatása: [Rövid útmutató: Azure Cognitive Search index létrehozása PowerShell](search-create-index-rest-api.md) vagy [C# rövid útmutató használatával: Hozzon létre egy Azure Cognitive Search indexet a .NET SDK használatával.](search-get-started-dotnet.md)

<a name="indexing-actions"></a>

### <a name="indexing-actions-upload-merge-mergeorupload-delete"></a>Indexelési műveletek: feltöltés, egyesítés, mergeOrUpload, törlés

Az indexelési művelet típusát dokumentumonként szabályozhatja, és megadhatja, hogy a dokumentumot teljes egészében fel kell-e tölteni, egyesíteni kell-e a meglévő dokumentumtartalommal, vagy törölni kell-e.

A REST API-ban http-posta-kérelmeket aD ki a JSON kérelem-szervek az Azure Cognitive Search index végpont URL-címére. Az "érték" tömbben minden JSON-objektum tartalmazza a dokumentum kulcsát, és meghatározza, hogy egy indexelési művelet hozzáadja, frissíti vagy törli-e a dokumentum tartalmát. Például a [Dokumentumok betöltése](search-get-started-dotnet.md#load-documents)című témakörben található.

A .NET SDK-ban csomagolja `IndexBatch` be az adatokat egy objektumba. Egy `IndexBatch` magában foglalja az `IndexAction` objektumok gyűjteménye, amelyek mindegyike tartalmaz egy dokumentumot, és egy tulajdonság, amely megmondja az Azure Cognitive Search, milyen műveletet kell végrehajtani a dokumentumon. Például a C# rövid útmutatóban például a [C# rövid útmutató.](search-get-started-dotnet.md)


| @search.action | Leírás | Az egyes dokumentumok kötelező mezői | Megjegyzések |
| -------------- | ----------- | ---------------------------------- | ----- |
| `upload` |Az `upload` művelet működése hasonló az „upsert” (frissítés/beszúrás) műveletéhez, ahol a rendszer az új dokumentumot beilleszti, ha pedig már létező dokumentumról van szó, akkor frissíti/kicseréli azt. |billentyű, továbbá a meghatározni kívánt egyéb mezők |Létező dokumentum frissítése/cseréje esetén a kérésben nem megadott mezők beállítása a következő lesz: `null`. Ez történik abban az esetben is, ha a mező korábban nem null értékre lett beállítva. |
| `merge` |Egy meglévő dokumentumot frissít a megadott mezőkkel. Ha a dokumentum nem található az indexben, az egyesítés meg fog hiúsulni. |billentyű, továbbá a meghatározni kívánt egyéb mezők |A rendszer az egyesítési művelet során megadott mezőkre cseréli a dokumentum meglévő mezőit. A . `DataType.Collection(DataType.String)` A REST API-ban ez `Collection(Edm.String)`a típusú mezőket foglalja magában. Ha például a dokumentum egy `["budget"]` értékű `tags` mezőt tartalmaz, és egyesítést hajt végre a `tags` mező `["economy", "pool"]` értékével, a `tags` mező végső értéke `["economy", "pool"]` lesz. Nem pedig a következő lesz: `["budget", "economy", "pool"]`. |
| `mergeOrUpload` |Ha az indexben már létezik az adott kulccsal ellátott dokumentum, ezen művelet viselkedése hasonló lesz a `merge` műveletéhez. Ha nem létezik ilyen dokumentum, a művelet viselkedése az `upload` új dokumentum esetében mutatott viselkedésének fog megfelelni. |billentyű, továbbá a meghatározni kívánt egyéb mezők |- |
| `delete` |Eltávolítja a megadott dokumentumot az indexből. |csak billentyű |A rendszer figyelmen kívül hagyja a kulcsmezőn kívül megadott mezőket. Ha egyetlen mezőt kíván eltávolítani a dokumentumból, e helyett használja a `merge` műveletet, és a mező számára explicit módon adja meg a null értéket. |

## <a name="decide-which-indexing-action-to-use"></a>A használni kívánt indexelési művelet megadása
Adatok importálása a .NET SDK használatával (feltöltés, egyesítés, törlés és mergeOrUpload). Attól függően, hogy az alábbi műveletek közül melyiket választja ki, az egyes dokumentumok esetében csak bizonyos mezők lesznek kötelezően megjelenítendők:


### <a name="formulate-your-query"></a>A lekérdezés meghatározása
Kétféleképpen [keresheti meg az indexet a REST API használatával](https://docs.microsoft.com/rest/api/searchservice/Search-Documents). Az egyik lehetőség egy HTTP POST kérés kiadása azon a helyen, ahol a lekérdezési paraméterek vannak meghatározva a kéréstörzs JSON-objektumában. A másik lehetőség egy HTTP GET kérés kiadása azon a helyen, ahol a lekérdezési paraméterek vannak meghatározva a kérés URL-címén belül. A lekérdezési paraméterek méretének tekintetében a POST több [enyhe korlátozással](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) rendelkezik, mint a GET. Éppen ezért a POST használatát javasoljuk, hacsak nem állnak fenn olyan speciális körülmények, amelyek a GET használatát kényelmesebbé tennék.

A POST és a GET esetében egyaránt meg kell majd adnia a *szolgáltatás nevét*, az *index nevét*, valamint a megfelelő *API-verziót* (a jelen dokumentum kiadásakor érvényes API-verzió: `2019-05-06`) a kérés URL-címében. A GET esetében a lekérdezési paramétereket az URL-cím végén található *lekérdezési sztringben* kell megadni. Az URL-cím formátuma alább látható:

    https://[service name].search.windows.net/indexes/[index name]/docs?[query string]&api-version=2019-05-06

A POST esetében a formátum ugyanez, azzal a kiegészítéssel, hogy a lekérdezési sztring paraméterei között csak az API-verzió szerepel.


## <a name="pulling-data-into-an-index"></a>Adatok lekérése indexbe
A lekéréses modell feltérképezi a támogatott adatforrást, majd automatikusan feltölti az adatokat az indexbe. Az Azure Cognitive Search, ez a képesség az *indexelők,* jelenleg elérhető az alábbi platformokon:

+ [Blob-tároló](search-howto-indexing-azure-blob-storage.md)
+ [Table Storage](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](https://aka.ms/documentdb-search-indexer)
+ [Azure SQL Database és az Azure virtuális gépeken futó SQL Server](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

Az indexelők indexeket csatlakoztatnak az adatforrásokhoz (általában táblákhoz, nézetekhez vagy ezekkel egyenértékű struktúrákhoz), és leképezik a forrásmezőket a megfelelő mezőkre az indexben. A végrehajtás során a sorkészlet automatikusan át lesz alakítva JSON formátumba, és be lesz töltve a meghatározott indexbe. Minden indexelő támogatja az ütemezést, így meghatározhatja, hogy az adatok milyen gyakran legyenek frissítve. A legtöbb indexelő biztosít változáskövetési funkciókat, ha az adatforrás támogatja azokat. Az új dokumentumok felismerésén kívül az indexelők a meglévő dokumentumok módosításainak és a törléseinek nyomon követésével küszöbölik ki az aktív adatkezelés szükségességét az indexben. 


### <a name="how-to-pull-data-into-an-azure-cognitive-search-index"></a>Adatok bekérése az Azure Cognitive Search indexébe

Az indexelő funkció az [Azure Portalon](search-import-data-portal.md), a [REST API](/rest/api/searchservice/Indexer-operations) és a [.NET SDK](/dotnet/api/microsoft.azure.search.indexersoperationsextensions) részeként van közzétéve. 

A portál használatának előnye, hogy az Azure Cognitive Search általában alapértelmezett indexsémát hozhat létre a forrásadatkészlet metaadatainak olvasásával. A létrehozott indexet annak feldolgozásáig módosíthatja, azt követően azonban csak azok a sémamódosítások engedélyezettek, amelyekhez újraindexelés nem szükséges. Ha a végrehajtani kívánt módosítások közvetlen hatással vannak a sémára, újra kell építenie az indexet. 

## <a name="verify-data-import-with-search-explorer"></a>Adatimportálás ellenőrzése a Keresővel

A dokumentum feltöltésének előzetes ellenőrzésének gyors módja a **Kereső** használata a portálon. A keresési ablak segítségével bármiféle kód írása nélkül kérdezheti le az indexeket. A keresési élmény az alapértelmezett beállításokon alapul, például az [egyszerű szintaxison](/rest/api/searchservice/simple-query-syntax-in-azure-search) és az alapértelmezett [searchMode lekérdezési paraméteren.](/rest/api/searchservice/search-documents) A rendszer az eredményeket JSON-formátumban adja vissza, így a teljes dokumentum vizsgálható.

> [!TIP]
> Számos [Azure Cognitive Search kódminták](https://github.com/Azure-Samples/?utf8=%E2%9C%93&query=search) beágyazott vagy könnyen elérhető adatkészletek, kínál egy egyszerű módja annak, hogy az első lépésekhez. A portál emellett egy mintaindexelőt és egy adatforrást is kínál, amely egy kisméretű ingatlan-adatkészletet tartalmaz („realestate-us-sample” néven). Amikor az előre konfigurált indexelőt a minta adatforráson futtatja, létrejön egy index, és olyan dokumentumokkal töltődik be, amelyek et a ztán is lelehet kérdezni a Kereséskezelőben vagy az Ön által írt kóddal.

## <a name="see-also"></a>Lásd még

+ [Az indexelő áttekintése](search-indexer-overview.md)
+ [Útmutató a Portal használatához: indexek létrehozása, betöltése és lekérdezése](search-get-started-portal.md)
