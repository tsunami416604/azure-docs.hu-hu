<properties
    pageTitle="Az Azure Search-index lekérdezése | Microsoft Azure | Üzemeltetett felhőalapú keresési szolgáltatás"
    description="Létrehozhat keresési lekérdezést az Azure Search szolgáltatásban, a keresési eredmények szűrését és rendezését pedig keresési paraméterek használatával végezheti el."
    services="search"
    documentationCenter=""
    authors="ashmaka"
/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# Az Azure Search-index lekérdezése
> [AZURE.SELECTOR]
- [Áttekintés](search-query-overview.md)
- [Portál](search-explorer.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)

Az Azure Search számára keresési kérések elküldésekor az alkalmazás keresőmezőjébe írt tényleges szavak mellett számos paraméter is megadható. Ezen lekérdezési paraméterek segítségével jobban kézben tartható a teljes szöveges keresés.

Az alábbi lista röviden ismerteti az Azure Search lekérdezési paramétereinek gyakori alkalmazásait. A lekérdezési paraméterek és azok viselkedésének teljes leírását a [REST API](https://msdn.microsoft.com/library/azure/dn798927.aspx) és a [.NET SDK](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.searchparameters_properties.aspx) részletes információi között találja.

## A lekérdezések típusai

Az Azure Search számos lehetőséget kínál rendkívül hatékony lekérdezések végrehajtására. Az itt használt két fő lekérdezési típus: `search` és `filter`. A `search` lekérdezés egy vagy több kifejezésre keres rá az index összes _searchable_ (kereshető) mezőjében, működése pedig hasonló a Google vagy a Bing keresőmotoréhoz. A `filter` lekérdezés egy logikai kifejezés kiértékelését végzi el az index összes _filterable_ (szűrhető) mezőjén. A `search` lekérdezésektől eltérően, a `filter` lekérdezéseknek egy mező pontos tartalmával kell megegyezniük, ami azt jelenti, hogy a karakterláncot tartalmazó mezők esetében a rendszer megkülönbözteti a kis- és nagybetűket.

A keresések és a szűrések együtt vagy külön-külön is alkalmazhatók. Ha együtt használja azokat, a rendszer a szűrőt először a teljes indexre alkalmazza, majd annak eredményein hajtja végre a keresést. A szűrők éppen ezért hasznosak a lekérdezés teljesítményének javítására, mivel általuk lecsökkenthető a keresési lekérdezés által feldolgozandó dokumentumok köre.

A szűrőkifejezések szintaxisa az [Odata szűrési nyelv](https://msdn.microsoft.com/library/azure/dn798921.aspx) alkészlete. A keresési lekérdezések esetében használható az [egyszerűsített szintaxis](https://msdn.microsoft.com/library/azure/dn798920.aspx) vagy az alább tárgyalt [Lucene lekérdezési szintaxis](https://msdn.microsoft.com/library/azure/mt589323.aspx) is.

### Egyszerű lekérdezési szintaxis
Az [egyszerű lekérdezési szintaxis](https://msdn.microsoft.com/library/azure/dn798920.aspx) az Azure Search szolgáltatásban használt alapértelmezett lekérdezési nyelv. Az egyszerű lekérdezési szintaxis számos gyakori keresési operátort támogat, például az AND, az OR, a NOT, a kifejezés, az utótag és a sorrend operátorokat.

### Lucene lekérdezési szintaxis
A [Lucene lekérdezési szintaxis](https://msdn.microsoft.com/library/azure/mt589323.aspx) lehetővé teszi az [Apache Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) részeként kifejlesztett, széles körben átvett, kifejező lekérdezési nyelv használatát.

Ezen lekérdezési szintaxis használatával könnyedén szert tehet a következő képességekre: [mező-hatáskörű lekérdezések](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_fields), [intelligens keresés](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_fuzzy), [közelségi keresés](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_proximity), [kifejezés-kiemelés](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_termboost), [reguláris kifejezésekkel végzett keresés](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_regex), [helyettesítő karakterekkel végzett keresés](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_wildcard), [szintaxis-alapok](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_syntax) és [logikai operátorokkal végzett lekérdezések](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_boolean).



## Az eredmények rendezése
A keresési lekérdezés eredményeinek fogadásakor kérheti, hogy az Azure Search az eredményeket egy adott mezőben lévő érték szerint rendezve szolgáltassa. Alapértelmezés szerint az Azure Search a keresési eredményeket az egyes dokumentumok keresési pontszáma szent rangsorolja, amelyet a [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf) állományból származtat.

Ha azt szeretné, hogy az Azure Search az eredményeket a keresési pontszámtól eltérő érték szerint rendezve adja vissza, erre a célra az `orderby` keresési paramétert használhatja. Az `orderby` paraméter értékét megadhatja úgy, hogy az tartalmazza a mezőneveket és a [`geo.distance()` függvény](https://msdn.microsoft.com/library/azure/dn798921.aspx) meghívásait a térinformatikai értékek esetében. Az egyes kifejezések után írt `asc` jellel az eredményeket növekvő sorrendben, `desc` jellel pedig csökkenő sorrendben kérheti le. Alapértelmezés szerint a rangsorolás növekvő sorrendben történik.

## Lapozás
A keresési eredmények lapozása az Azure Search segítségével könnyen megvalósítható. A `top` és `skip` paraméterek használatával zökkenőmentesen adhat ki olyan keresési kéréseket, amelyek segítségével a keresési eredmények teljes készletét kezelhető, rendezett alkészletek formájában fogadhatja, így lehetővé téve a helyes keresési felhasználói gyakorlat egyszerű kialakítását. Az eredmények kisebb alkészleteinek fogadásakor a keresési eredmények teljes készletében lévő dokumentumok darabszámát is megkaphatja.

A keresési eredmények lapozásáról további információkat a [Keresési eredmények lapozása az Azure Search szolgáltatásban](search-pagination-page-layout.md) című cikkben talál.


## Találatok kiemelése
Az Azure Search szolgáltatás `highlight`, `highlightPreTag` és `highlightPostTag` paramétereinek használatával egyszerűen kiemelhető a keresési lekérdezésnek megfelelő keresési eredmények pontos köre. Megadhatja, hogy mely _searchable_ (kereshető) mezők esetében kívánja bekapcsolni az egyező szöveg kiemelését, valamint az Azure által visszaadott egyező szöveg elejére és végére hozzáfűzni kívánt karakterlánc-címkéket.



<!--HONumber=sep16_HO1-->


