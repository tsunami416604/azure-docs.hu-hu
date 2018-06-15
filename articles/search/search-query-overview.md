---
title: Lekérdezések az Azure Search szolgáltatásban | Microsoft Docs
description: Létrehozhat keresési lekérdezést az Azure Search szolgáltatásban, a keresési eredmények szűrését és rendezését pedig keresési paraméterek használatával végezheti el.
author: brjohnstmsft
manager: jlembicz
ms.author: brjohnst
services: search
ms.service: search
ms.topic: conceptual
ms.date: 11/13/2017
ms.openlocfilehash: 50e27cbc485259ea5ce744ba71feee5e90293bcb
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
ms.locfileid: "31792909"
---
# <a name="queries-in-azure-search"></a>Lekérdezések az Azure Search szolgáltatásban
> [!div class="op_single_selector"]
> * [Áttekintés](search-query-overview.md)
> * [Portál](search-explorer.md)
> * [.NET](search-query-dotnet.md)
> * [REST](search-query-rest-api.md)
> 
> 

Az Azure Search számára keresési kérések elküldésekor az alkalmazás keresőmezőjébe írt tényleges szavak mellett számos paraméter is megadható. A lekérdezési paraméterek segítségével jobban kezelhető a [teljes szöveges keresés](search-lucene-query-architecture.md).

Az alábbi lista röviden ismerteti az Azure Search lekérdezési paramétereinek gyakori alkalmazásait. A lekérdezési paraméterek és azok viselkedésének teljes leírását a [REST API](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) és a [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters#microsoft_azure_search_models_searchparameters#properties_summary) részletes információi között találja.

## <a name="types-of-queries"></a>A lekérdezések típusai
Az Azure Search számos lehetőséget kínál rendkívül hatékony lekérdezések végrehajtására. Az itt használt két fő lekérdezési típus: `search` és `filter`. A `search` lekérdezés egy vagy több kifejezésre keres rá az index összes *searchable* (kereshető) mezőjében, működése pedig hasonló a Google vagy a Bing keresőmotoréhoz. A `filter` lekérdezés egy logikai kifejezés kiértékelését végzi el az index összes *filterable* (szűrhető) mezőjén. A `search` lekérdezésektől eltérően, a `filter` lekérdezéseknek egy mező pontos tartalmával kell megegyezniük, ami azt jelenti, hogy a karakterláncot tartalmazó mezők esetében a rendszer megkülönbözteti a kis- és nagybetűket.

A keresések és a szűrések együtt vagy külön-külön is alkalmazhatók. Ha együtt használja azokat, a rendszer a szűrőt először a teljes indexre alkalmazza, majd annak eredményein hajtja végre a keresést. A szűrők éppen ezért hasznosak a lekérdezés teljesítményének javítására, mivel általuk lecsökkenthető a keresési lekérdezés által feldolgozandó dokumentumok köre.

A szűrőkifejezések szintaxisa az [Odata szűrési nyelv](https://docs.microsoft.com/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search) alkészlete. A keresési lekérdezések esetében használható az [egyszerűsített szintaxis](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search) vagy az alább tárgyalt [Lucene lekérdezési szintaxis](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search) is.

### <a name="simple-query-syntax"></a>Egyszerű lekérdezési szintaxis
Az [egyszerű lekérdezési szintaxis](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search) az Azure Search szolgáltatásban használt alapértelmezett lekérdezési nyelv. Az egyszerű lekérdezési szintaxis számos gyakori keresési operátort támogat, például az AND, az OR, a NOT, a kifejezés, az utótag és a sorrend operátorokat.

### <a name="lucene-query-syntax"></a>Lucene lekérdezési szintaxis
A [Lucene lekérdezési szintaxis](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search) lehetővé teszi az [Apache Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) részeként kifejlesztett, széles körben átvett, kifejező lekérdezési nyelv használatát.

Ezen lekérdezési szintaxis használatával könnyedén szert tehet a következő képességekre: [mező-hatáskörű lekérdezések](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_fields), [intelligens keresés](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_fuzzy), [közelségi keresés](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_proximity), [kifejezés-kiemelés](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_termboost), [reguláris kifejezésekkel végzett keresés](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_regex), [helyettesítő karakterekkel végzett keresés](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_wildcard), [szintaxis-alapok](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_syntax) és [logikai operátorokkal végzett lekérdezések](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_boolean).

## <a name="ordering-results"></a>Az eredmények rendezése
A keresési lekérdezés eredményeinek fogadásakor kérheti, hogy az Azure Search az eredményeket egy adott mezőben lévő érték szerint rendezve szolgáltassa. Alapértelmezés szerint az Azure Search a keresési eredményeket az egyes dokumentumok keresési pontszáma szent rangsorolja, amelyet a [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf) állományból származtat.

Ha azt szeretné, hogy az Azure Search az eredményeket a keresési pontszámtól eltérő érték szerint rendezve adja vissza, erre a célra az `orderby` keresési paramétert használhatja. Az `orderby` paraméter értékét megadhatja úgy, hogy az tartalmazza a mezőneveket és a [`geo.distance()` függvény](https://docs.microsoft.com/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search) meghívásait a térinformatikai értékek esetében. Az egyes kifejezések után írt `asc` jellel az eredményeket növekvő sorrendben, `desc` jellel pedig csökkenő sorrendben kérheti le. Alapértelmezés szerint a rangsorolás növekvő sorrendben történik.

## <a name="paging"></a>Lapozás
A keresési eredmények lapozása az Azure Search segítségével könnyen megvalósítható. A `top` és `skip` paraméterek használatával zökkenőmentesen adhat ki olyan keresési kéréseket, amelyek segítségével a keresési eredmények teljes készletét kezelhető, rendezett alkészletek formájában fogadhatja, így lehetővé téve a helyes keresési felhasználói gyakorlat egyszerű kialakítását. Az eredmények kisebb alkészleteinek fogadásakor a keresési eredmények teljes készletében lévő dokumentumok darabszámát is megkaphatja.

A keresési eredmények lapozásáról további információkat a [Keresési eredmények lapozása az Azure Search szolgáltatásban](search-pagination-page-layout.md) című cikkben talál.

## <a name="hit-highlighting"></a>Találatok kiemelése
Az Azure Search szolgáltatás `highlight`, `highlightPreTag` és `highlightPostTag` paramétereinek használatával egyszerűen kiemelhető a keresési lekérdezésnek megfelelő keresési eredmények pontos köre. Megadhatja, hogy mely *searchable* (kereshető) mezők esetében kívánja bekapcsolni az egyező szöveg kiemelését, valamint az Azure által visszaadott egyező szöveg elejére és végére hozzáfűzni kívánt karakterlánc-címkéket.

## <a name="try-out-query-syntax"></a>Lekérdezési szintaxis tesztelése

A szintaxisbeli különbségek megértésének legjobb módja, ha lekérdezéseket küld el, és áttekinti az eredményeket.

+ A [Keresési ablak](search-explorer.md) használata az Azure Portalon. [A mintaindex](search-get-started-portal.md) üzembe helyezésével perceken belül lekérdezheti az indexet a portál eszközeivel.

+ A Telerik Fiddler vagy a Chrome Postman használatával lekérdezéseket küldhet a keresési szolgáltatásba feltöltött indexekhez. Mindkét eszköz támogatja a HTTP-végpontok felé indított REST-hívásokat. 