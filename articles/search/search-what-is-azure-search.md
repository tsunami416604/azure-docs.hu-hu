---
title: Bevezetés az Azure Cognitive Search használatába
titleSuffix: Azure Cognitive Search
description: Az Azure Cognitive Search a Microsoft teljes körűen felügyelt felhőalapú keresési szolgáltatása. Olvassa el a szolgáltatásleírásokat, a fejlesztési munkafolyamatot, a más Microsoft keresési termékekkel való összehasonlítást és az első lépések módját.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 01/06/2020
ms.openlocfilehash: fee7c8eb73fe0bb7c9fd0bd9de9aa57bd8c40215
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77580651"
---
# <a name="what-is-azure-cognitive-search"></a>Mi az az Azure Cognitive Search?

Az Azure Cognitive Search[(korábbi nevén "Azure Search")](whats-new.md#new-service-name)egy szolgáltatásként kereshető felhőalapú megoldás, amely a fejlesztők számára API-kat és eszközöket biztosít a webes, mobil- és nagyvállalati alkalmazásokban található privát, heterogén tartalmakhoz való gazdag keresési élmény hozzáadásához. A kód vagy egy eszköz adatbetöltést (indexelést) hív meg az index létrehozásához és betöltéséhez. Szükség esetén kognitív képességeket is hozzáadhat az Indexelés során alkalmazott AI-folyamatok alkalmazásához. Ezzel új információkat és struktúrákat adhat hozzá, amelyek hasznosak a kereséshez és más forgatókönyvekhez.

A szolgáltatás másik oldalán az alkalmazáskód lekérdezési kérelmeket ad ki, és kezeli a válaszokat. A keresési élmény az Azure Cognitive Search funkcióinak használatával van definiálva az ügyfélben, és a lekérdezés végrehajtása egy megőrzött indexen keresztül történik, amelyet ön hoz létre, birtokol és tárol a szolgáltatásban.

![Azure Cognitive Search architektúra](media/search-what-is-azure-search/azure-search-diagram.svg "Azure Cognitive Search architektúra")

A funkciókat egy egyszerű [REST API-n](/rest/api/searchservice/) vagy [.NET SDK-n](search-howto-dotnet-sdk.md) keresztül tudja elérni, mely elfedi az információk kiolvasásának mögöttes komplexitását. Az API-k mellett az Azure Portal is nyújt támogatást a felügyelethez és a tartalomkezeléshez, például eszközöket kínál prototípus-készítéshez és az indexek lekérdezéséhez. Mivel a szolgáltatás a felhőben fut, az infrastruktúrát és a rendelkezésre állást a Microsoft felügyeli.

## <a name="when-to-use-azure-cognitive-search"></a>Mikor kell használni az Azure Cognitive Search szolgáltatást?

Az Azure Cognitive Search kiválóan alkalmas a következő alkalmazási forgatókönyvekhez:

+ Heterogén tartalomtípusok összevonása privát, egységes, kereshető indexbe. A lekérdezések mindig egy olyan indexen keresztül vannak, amelyet dokumentumokhoz hoz létre és tölt be, és az index mindig a felhőben található az Azure Cognitive Search szolgáltatásban. Az indexet bármilyen forrásból vagy platformról származó JSON-dokumentumok adatfolyamaival feltöltheti. Másik lehetőségként az Azure-ban beszerzett tartalom esetében egy *indexelő* használatával adatokat is behívhat egy indexbe. Index definíció ja, és a felügyeleti/tulajdonosi az Azure Cognitive Search használatának egyik fő oka.

+ A nyers tartalom nagy, nem differenciált szöveg, képfájlok vagy alkalmazásfájlok, például Az Azure-adatforrásokon, például az Azure Blob storage vagy a Cosmos DB office-tartalomtípusai. Az indexelés során kognitív képességeket alkalmazhat, hogy struktúrát adjon hozzá, vagy kibontsa a jelentést a kép- és alkalmazásfájlokból.

+ A kereséssel kapcsolatos funkciók egyszerű megvalósítása. Az Azure Cognitive Search API-k leegyszerűsítik a lekérdezések felépítését, a felületes navigációt, a szűrőket (beleértve a földrajzi térbeli keresést), a szinonimát leképezést, a typeahead lekérdezéseket és a relevancia-hangolást. A beépített funkciók segítségével kielégítheti a végfelhasználók nak a kereskedelmi webes keresőmotorokhoz hasonló keresési élményre vonatkozó elvárásait.

+ Strukturálatlan szöveg indexelése, illetve szöveg és információ kibontása képfájlokból. Az Azure Cognitive Search [AI-bővítési](cognitive-search-concept-intro.md) funkciója ad AI-feldolgozást egy indexelési folyamathoz. Néhány gyakori használati esetek közé tartozik az OCR a beolvasott dokumentum, az entitás felismerése és a kulcskifejezések kinyerése a nagy dokumentumokon, a nyelvfelismerés és a szövegfordítás, valamint a hangulatelemzés.

+ Az Azure Cognitive Search egyéni és nyelvi elemzőinek nyelvi követelményei tetszésszerint teljesülnek. Ha nem angol nyelvű tartalommal rendelkezik, az Azure Cognitive Search támogatja a Lucene-elemzőket és a Microsoft természetes nyelvi processzorait is. Az analizátorokat úgy is beállíthatja, hogy speciális feldolgozást érjenek el a nyers tartalomból, például kiszűrjék a mellékjeleket.

<a name="feature-drilldown"></a>

## <a name="feature-descriptions"></a>A szolgáltatások leírása

| Alapvető&nbsp;keresés&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Szolgáltatások |
|-------------------|----------|
|Szabad formátumú szöveges keresés | [**A teljes szöveges keresés**](search-lucene-query-architecture.md) a legtöbb keresésalapú alkalmazás elsődleges használati esete. A lekérdezéseket a támogatott szintaxisok segítségével állíthatja össze. <br/><br/>Az [**egyszerű lekérdezési szintaxis**](query-simple-syntax.md) logikai operátorokat, kifejezéskeresési operátorokat, utótag-operátorokat és prioritási operátorokat tartalmaz.<br/><br/>A [**Lucene lekérdezési szintaxis**](query-lucene-syntax.md) tartalmazza az egyszerű szintaxis összes operátorát, illetve támogatja emellett az intelligens keresést, a közelségi keresést, a kifejezés-kiemelést és a reguláris kifejezéseket.|
| Relevancia | [**Az egyszerű pontozás**](index-add-scoring-profiles.md) az Azure Cognitive Search egyik legfontosabb előnye. A pontozási profilok segítségével a relevancia magukban a dokumentumokban lévő értékeknek a függvényeként modellezhető. Beállíthatja például, hogy az újabb termékek vagy az akciós termékek magasabb prioritással jelenjenek meg a keresési eredményben. Emellett hozhat létre pontozási profilokat címkékkel, ha a külön nyomon követett és tárolt felhasználói keresési beállítások alapján személyre szabott pontozást szeretne nyújtani. |
| Földrajzi keresés | Az Azure Cognitive Search folyamatok, szűrők és megjeleníti a földrajzi helyeket. Ez lehetővé teszi a felhasználóknak, hogy az adatokat a keresési eredménynek egy fizikai helyhez való közelsége alapján tárják fel. További információért [nézze meg ezt a videót](https://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data) vagy [tekintse át ezt a mintát](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs). |
| Szűrők és a dista | A [**jellemzőalapú navigáció**](search-faceted-navigation.md) egy lekérdezési paraméterrel engedélyezhető. Az Azure Cognitive Search egy olyan sokoldalú navigációs struktúrát ad vissza, amelyet egy kategórialista mögötti kódként használhat, önirányított szűréshez (például a katalóguselemek ártartomány vagy márka szerinti szűréséhez). <br/><br/> A [**szűrők**](query-odata-filter-orderby-syntax.md) segítségével építhet be jellemzőalapú navigációt az alkalmazása felhasználói felületébe, továbbfejlesztheti a lekérdezésírást, valamint szűrhet felhasználó vagy fejlesztő által megadott feltételek alapján. Szűrőket OData-szintaxissal tud létrehozni. |
| Felhasználói élményt javító szolgáltatások | [**Az automatikus kiegészítés**](search-autocomplete-tutorial.md) engedélyezhető a keresősávban lévő, előreírt lekérdezésekhez. <br/><br/>A [**Keresési javaslatok**](https://docs.microsoft.com/rest/api/searchservice/suggesters) részben bevitt szövegeket is feldolgoz a keresősávban, de eredményként nem lekérdezési kifejezéseket, hanem tényleges dokumentumokat ad ki az indexben. <br/><br/>A [**Szinonimák**](search-synonyms.md) egyenértékű, a lekérdezés hatókörét implicit módon bővítő kifejezéseket társít a kereséshez, hogy ne a felhasználónak kelljen megadnia az alternatívákat. <br/><br/>A [**találatok kiemelése**](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) szövegformázást alkalmaz a keresési találatok egyező kulcsszavaira. Megadhatja, hogy mely mezők adjanak vissza kiemelt részeket.<br/><br/>A [**rendezés**](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) több mezőhöz is elérhető az indexsémán keresztül, majd a lekérdezéskor be- és kikapcsolható egyetlen keresési paraméter használatával.<br/><br/> A keresési eredmények [**lapozása**](search-pagination-page-layout.md) és szabályozása egyszerű az Azure Cognitive Search által a keresési eredmények felett kínált finoman hangolt vezérléssel.  <br/><br/>|

| AI&nbsp;gazdagodása&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;       | Szolgáltatások |
|-------------------|----------|
|AI-feldolgozás indexelés közben | A kép- és szövegelemzéshez szükséges [**AI-bővítés**](cognitive-search-concept-intro.md) egy indexelő folyamatra alkalmazható a szöveges információk nyers tartalomból való kinyeréséhez. A [beépített képességek](cognitive-search-predefined-skills.md) között szerepel például az optikai karakterfelismerés (beolvasott JPEG-fájlok kereshetővé tétele), az entitásfelismerés (cég vagy intézmény, név vagy hely azonosítása), valamint a kulcskifejezések felismerése. [Egyéni képességeket is kódolhat](cognitive-search-create-custom-skill-example.md), amelyeket a folyamathoz lehet kapcsolni. |
| Bővített tartalom tárolása elemzésre és fogyasztásra nem keresési forgatókönyvekben | [**A tudástároló (előzetes verzió)**](knowledge-store-concept-intro.md) a ai-alapú indexelés kiterjesztése. Az Azure storage háttérrendszerként mentheti az indexelés során létrehozott dúsításokat. Ezek az összetevők segítségével jobb skillseteket tervezhet, vagy amorf vagy kétértelmű adatokból alakzatot és struktúrát hozhat létre. Ezeka struktúrák, amelyek célzott konkrét számítási feladatok vagy felhasználók vetületei hozhat létre. Közvetlenül is elemezheti a kinyert adatokat, vagy betöltheti azokat más alkalmazásokba.<br/><br/> |
| Gyorsítótárazott tartalom | [**A növekményes bővítés (előzetes verzió)**](cognitive-search-incremental-indexing-conceptual.md) csak azadott szerkesztés által a folyamatra módosított dokumentumokra korlátozza a feldolgozást, a folyamat nem módosított részeinek gyorsítótárazott tartalmát használva. |

| Adatok&nbsp;importálása/indexelése | Szolgáltatások |
|----------------------------------|----------|
| Adatforrások | Az Azure Cognitive Search indexek fogadja el az adatokat bármilyen forrásból, feltéve, hogy a JSON-adatstruktúra ként van elküldve. <br/><br/> [**Az indexelők**](search-indexer-overview.md) automatizálják a támogatott Azure-adatforrások adatbetöltését, és kezelik a JSON-szerializálást. Csatlakozzon az [Azure SQL Database,](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) [az Azure Cosmos DB](search-howto-index-cosmosdb.md)vagy az Azure Blob [storage](search-howto-indexing-azure-blob-storage.md) hálózathoz az elsődleges adattárakban kereshető tartalom kinyeréséhez. Az Azure Blob-indexelők képesek *dokumentumfeltörést* végezni [szövegek kinyeréséhez a leggyakoribb fájlformátumokból](search-howto-indexing-azure-blob-storage.md), ideértve a Microsoft Office-, a PDF- és a HTML-dokumentumokat. |
| Hierarchikus és beágyazott adatstruktúrák | [**Összetett típusok**](search-howto-complex-data-types.md) és gyűjtemények lehetővé teszik, hogy gyakorlatilag bármilyen Típusú JSON-struktúra azure cognitive Search indexként modellezze. Az egy-a-többhöz és a többhöz számosság natív módon fejezhető ki gyűjtemények, összetett típusok és összetett típusok gyűjtemények révén.|
| Nyelvi elemzés | Az elemzők olyan összetevők, amelyekkel a rendszer szövegfeldolgozást végez az indexelési és keresési műveletek során. Két típusukat különböztetjük meg. <br/><br/>Az [**egyéni lexikai elemzőket**](index-add-custom-analyzers.md) a rendszer a fonetikus egyeztetést és reguláris kifejezéseket használó összetett keresési lekérdezésekhez alkalmazza. <br/><br/>A Lucene-tól vagy a Microsofttól származó [**nyelvi elemzők**](index-add-language-analyzers.md) a nyelvspecifikus funkcionalitást kezelik, ideértve például az igeidőt, a nemet, a szabálytalan többesszámú főneveket, a szóösszetételek lebontását és a szavakra bontást (a szóközt nem alkalmazó nyelveknél). <br/><br/>|


| Platform&nbsp;szint&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;| Szolgáltatások |
|-------------------|----------|
| Eszközök prototípus-készítéshez és vizsgálatokhoz | A portálon az [**Adatok importálása varázsló**](search-import-data-portal.md) segítségével konfigurálhatja az indexelőket, az indextervezővel új indexeket tervezhet, a [**Keresési ablakban**](search-explorer.md) pedig keresési kifejezéseket tesztelhet a pontozási profilok finomításához. Emellett bármelyik indexet megnyithatja a séma megtekintéséhez. |
| Monitorozás és diagnosztika | [**Engedélyezze,**](search-monitor-usage.md) hogy a figyelési funkciók túllépjenek a portálon mindig látható metrikákon. Emellett a rendszer alapértelmezés szerint gyűjti a lekérdezések másodpercenkénti számának, a késésnek és a leszabályozásnak a metrikáit, melyek megtekinthetők jelentésként a portállapokon.|
| Kiszolgálóoldali titkosítás | [**A Microsoft által felügyelt inaktív titkosítás**](search-security-overview.md#encrypted-transmission-and-storage) be van építve a belső tárolási rétegbe, és visszavonhatatlan. Az alapértelmezett titkosítást az [**ügyfél által felügyelt titkosítási kulcsokkal**](search-security-manage-encryption-keys.md)is kiegészítheti. Az Azure Key Vaultban létrehozott és felügyelt kulcsok az Azure Cognitive Search indexek és szinonimát leképezések titkosítására szolgálnak. |
| Infrastruktúra | A **nagy rendelkezésre állású platform** rendkívül megbízható keresési szolgáltatást szavatol. Megfelelő méretezés esetén az [Azure Cognitive Search 99,9%-os SLA-t kínál.](https://azure.microsoft.com/support/legal/sla/search/v1_0/)<br/><br/> **Teljes körűen felügyelt és méretezhető,** mint egy teljes körű megoldás, az Azure Cognitive Search egyáltalán nem igényel infrastruktúra-kezelést. A szolgáltatást két dimenzió méretezésével szabhatja az igényeihez: konfigurálhat nagyobb méretű dokumentumtárat vagy magasabb lekérdezés-feldolgozási kapacitást, vagy mindkettőt.<br/><br/>|

## <a name="how-to-use-azure-cognitive-search"></a>Az Azure Cognitive Search használata
### <a name="step-1-provision-service"></a>1. lépés: A szolgáltatás üzembe helyezése
Azure Cognitive Search szolgáltatást az [Azure Portalon](https://portal.azure.com/) vagy az [Azure Resource Management API-n](/rest/api/searchmanagement/)keresztül hozhat létre. Választhatja az ingyenes, más előfizetőkkel megosztott szolgáltatást, vagy választhat [fizetett csomagot](https://azure.microsoft.com/pricing/details/search/), mely dedikált erőforrásokat nyújt csak az Ön szolgáltatása számára. A fizetett csomagok esetében a szolgáltatást két dimenzióban méretezheti: 

- Replikák hozzáadásával növelheti az adott idő alatt feldolgozható lekérdezések számát.   
- Partíciók hozzáadásával növelheti a dokumentumtároláshoz elérhető tárterület méretét. 

A dokumentumtárolás és a lekérdezésfeldolgozási teljesítmény külön-külön való szabályozásával a szolgáltatást az egyedi környezete igényeihez szabhatja.

### <a name="step-2-create-index"></a>2. lépés: Index létrehozása
A kereshető tartalom feltöltése előtt meg kell határoznia egy Azure Cognitive Search indexet. Az index egy adatbázistáblához hasonló struktúra, mely tárolja az adatait, és képes keresési lekérdezések fogadására. Definiálhatja az indexsémát olyan leképezéssel, amely a keresendő dokumentumok struktúráját tükrözi, az adatbázisokban lévő mezőkhöz hasonlóan.

Hozhat létre sémát az Azure Portalon, illetve programozottan is a [.NET SDK](search-howto-dotnet-sdk.md) vagy a [REST API](/rest/api/searchservice/) használatával.

### <a name="step-3-load-data"></a>3. lépés: Adatok betöltése
Az index definiálása után készen áll a tartalmak feltöltésére. Választhat a küldéses és a lekéréses modell között.

A lekéréses modell beszerzi az adatokat a külső adatforrásokból. Ezt olyan *indexelők* segítségével végzi, amelyek leegyszerűsítik és automatizálják az adatbetöltés bizonyos részleteit, például az adatokhoz való kapcsolódást, illetve az adatok olvasását vagy szerializálását. Érhetők el [indexelők](/rest/api/searchservice/Indexer-operations) az Azure Cosmos DB, az Azure SQL Database és az Azure Blob Storage szolgáltatáshoz, valamint az Azure virtuális gépeken futó SQL Server-kiszolgálókhoz. Az indexelőket konfigurálhatja igény szerinti vagy ütemezett adatfrissítésre.

A küldéses modell az SDK-n vagy a REST API-kon keresztül vehető igénybe úgy, hogy elküldi a frissített dokumentumokat egy indexnek. Gyakorlatilag bármilyen adatkészletből küldhet adatokat JSON formátumban. Az adatok betöltéséről a [Dokumentumok hozzáadása, frissítése vagy törlése](/rest/api/searchservice/addupdate-or-delete-documents) és [A .NET SDK használata](search-howto-dotnet-sdk.md) című cikkben találhat útmutatást.

### <a name="step-4-search"></a>4. lépés: Keresés
Az index feltöltése után [keresési lekérdezéseket](search-query-overview.md) adhat ki a szolgáltatás végpontjára egyszerű HTTP-kérelmek használatával rest [API-val](/rest/api/searchservice/Search-Documents) vagy a [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations)használatával.

Lépjen [végig: Hozza létre az első keresőalkalmazást,](tutorial-csharp-create-first-app.md) majd bővítsen ki egy olyan weblapot, amely összegyűjti a felhasználói adatokat és kezeli az eredményeket. A [Postman](search-get-started-postman.md) segítségével interaktív REST-hívásokhoz vagy az Azure Portal beépített [Search Explorer](search-explorer.md) szolgáltatásához is használhatja egy meglévő index lekérdezéséhez.

## <a name="how-it-compares"></a>Összehasonlítás más keresési megoldásokkal

Az ügyfelek gyakran kérdezik, hogy az Azure Cognitive Search hogyan viszonyul más kereséssel kapcsolatos megoldásokhoz. Az alábbi táblázat összefoglalja a fő eltéréseket.

| Más szolgáltatás | Fő eltérések |
|-------------|-----------------|
|Bing | A [Bing Web Search API](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/) a Bing.com szolgáltatás indexeiben végez keresést az Ön által megadott kifejezés alapján. Ezek az indexek a nyilvános webhelyeken elérhető HTML, XML és más formátumú tartalmakból állnak. Ugyanerre az alapra épül a [Bing Custom Search](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/), amely ugyanezt a webes tartalmakhoz használható bejárási technológiát nyújtja, de egyéni webhelyekre alkalmazva.<br/><br/>Az Azure Cognitive Search megkeresi az Ön által megadott indexet, amely gyakran különböző forrásokból származó adatokkal és dokumentumokkal van feltöltve. Az Azure Cognitive Search [indexelőkön](search-indexer-overview.md)keresztül rendelkezik feltérképező képességekkel egyes adatforrásokhoz, de az indexsémának megfelelő JSON-dokumentumokat egyetlen, összevontkereshető erőforrásba is lelökheti. |
|Adatbázis-keresés | Számos adatbázis-platform tartalmaz beépített keresőket. Az SQL Server egy [teljes szöveges keresési funkciót](https://docs.microsoft.com/sql/relational-databases/search/full-text-search) tartalmaz. A Cosmos DB és a hasonló technológiák lekérdezhető indexekkel rendelkeznek. A keresési és tárolási funkciókat egyesítő termékek kiértékelésekor kihívást jelenthet a választás. Számos megoldás használja mindkettőt: DBMS a tároláshoz, és az Azure Cognitive Search speciális keresési funkciók.<br/><br/>A DBMS-kereséssel összehasonlítva az Azure Cognitive Search heterogén forrásokból származó tartalmakat tárol, és speciális szövegfeldolgozási funkciókat kínál, például nyelvészeti szempontból érzékeny szövegfeldolgozást (szüret, lemmatizálás, szóűrlapok) [56 nyelven.](https://docs.microsoft.com/rest/api/searchservice/language-support) Emellett támogatja a hibásan írt szavak automatikus javítását, a [szinonimákat](https://docs.microsoft.com/rest/api/searchservice/synonym-map-operations), a [javaslatokat](https://docs.microsoft.com/rest/api/searchservice/suggestions), a [pontozási vezérlőket](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), az [aspektusokat](https://docs.microsoft.com/azure/search/search-filters-facets) és az [egyéni lexikális elemzéseket](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search). Az Azure Cognitive Search [teljes szöveges keresőmotorja](search-lucene-query-architecture.md) az Apache Lucene-re épül, amely az információ-visszakeresés egyik iparági szabványa. Míg az Azure Cognitive Search fordított index formájában tartja meg az adatokat, ritkán helyettesíti a valódi adattárolást. További információért tekintse meg [ezt a fórumbejegyzést](https://stackoverflow.com/questions/40101159/can-azure-search-be-used-as-a-primary-database-for-some-data). <br/><br/>Az erőforrás-kihasználás is egy lényeges szempont ebben a kategóriában. Az indexelés és bizonyos lekérdezési műveletek általában nagy számítási terheléssel járnak. A keresések kihelyezése a DBMS-ből egy felhőbeli dedikált megoldásba segít megőrizni a rendszererőforrásokat tranzakciófeldolgozás céljából. Emellett a külső keresési megoldás használata megkönnyíti a lekérdezések mennyiségéhez igazított skálázást is.|
|Dedikált keresési megoldás | Ha úgy döntött, hogy egy teljes körű, dedikált keresési megoldást használ, utolsó lépésként kategorikusan össze kell hasonlítania a helyszíni megoldásokat és egy felhőalapú szolgáltatást. Számos keresési technológia ad lehetőséget az indexelés és a lekérdezési folyamat szabályozására, fejlettebb lekérdezési és szűrési szintaxis használatára, a rangsorolás és a relevancia vezérlésére, valamint önállóan irányított és intelligens keresés végrehajtására. <br/><br/>A felhőszolgáltatás a megfelelő választás, ha kulcsrakész, minimális fenntartási és karbantartási költségű, rugalmasan méretezhető megoldást keres. <br/><br/>A felhőmegoldásokon belül számos szolgáltató nyújt alapvető funkcionalitást teljes szöveges kereséssel, földrajzi kereséssel, valamint a keresési bemenetek bizonyos fokú félreérthetőségének kezelési képességével. Az ideális megoldást jellemzően az határozza meg, hogy milyen [speciális szolgáltatásokra](#feature-drilldown) van szüksége, illetve hogy mennyire egyszerű az API-k és az eszközök használata, valamint a felügyelet. |

A felhőszolgáltatók közül az Azure Cognitive Search a legerősebb az Azure-beli tartalomtárolók és adatbázisok teljes szöveges keresési munkaterhelései mellett, olyan alkalmazások esetében, amelyek elsősorban az információ-lekérésre és a tartalomnavigációra egyaránt támaszkodnak. 

A legfőbb előnyök közé tartoznak az alábbiak:

+ Azure-adatintegráció (webbejárók) az indexelő rétegben
+ Azure Portal központi felügyelethez
+ Az Azure ismert méretezhetősége, megbízhatósága és világszínvonalú rendelkezésre állása
+ A nyers adatok ai-feldolgozása, hogy még kereshetőbb legyen, beleértve a képekből származó szöveget, vagy a strukturálatlan tartalom mintáinak megtalálása.
+ Nyelvi és egyéni elemzési funkciók, teljes szöveges keresési elemzők 56 nyelvhez
+ [Keresésközpontú alkalmazásokban gyakran használt funkciók](#feature-drilldown): pontozás, jellemzőkezelés, javaslatok, szinonimák, földrajzi keresés és egyebek.

> [!Note]
> A rendszer teljes mértékben támogatja a nem Azure adatforrásokat is, de ezek nem indexelőkkel, hanem több kódot igénylő küldéses metódussal használhatók. API-k használatával bármely JSON-dokumentumgyűjtemény térhet el egy Azure Cognitive Search-index.

Ügyfeleink között az Azure Cognitive Search legtávolabbi funkcióit kitudják használni: online katalógusok, üzletági programok és dokumentumfelderítési alkalmazások.

## <a name="rest-api--net-sdk"></a>REST API | .NET SDK

Bár számos feladat hajtható végre a portálon, az Azure Cognitive Search azoknak a fejlesztőknek szól, akik szeretnék integrálni a keresési funkciókat a meglévő alkalmazásokba. Ehhez az alábbi programozási felületek vehetők igénybe.

|Platform |Leírás |
|-----|------------|
|[Többi](/rest/api/searchservice/) | HTTP-parancsok, melyek használhatók bármilyen programozási platformról vagy nyelven, ideértve például a Xamarin, a Java és a JavaScript platformot|
|[.NET SDK](search-howto-dotnet-sdk.md) | .NET-burkoló a REST API-hoz, mely hatékony fejlesztői munkát tesz lehetővé C# és más felügyelt kódú nyelveken, .NET-keretrendszerre épülő megoldások létrehozásához |

## <a name="free-trial"></a>Ingyenes próbaidőszak
Az Azure-előfizetők [üzembe helyezhetik a szolgáltatás ingyenes verzióját](search-create-service-portal.md).

Ha nincs még előfizetése, [ingyen létrehozhat egy Azure-fiókot](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F). A fiók létrehozásakor krediteket kap a fizetős Azure-szolgáltatások kipróbálásához. A kreditek felhasználása után megtarthatja a fiókját, és tovább használhatja azt az [ingyenes Azure-szolgáltatásokkal](https://azure.microsoft.com/free/). A bankkártyáját semmilyen költség nem terheli, hacsak Ön kifejezetten nem módosítja beállításait ennek engedélyezéséhez.

Másik lehetőségként [kihasználhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F): Az MSDN-előfizetés minden hónapban biztosít Önnek krediteket, amelyekkel fizetős Azure-szolgáltatásokat használhat. 

## <a name="how-to-get-started"></a>Első lépések

1. Hozzon létre egy [ingyenes szolgáltatást](search-create-service-portal.md). Az ingyenes szolgáltatás az összes rövid útmutató és oktatóanyag elvégzését lehetővé teszi.

2. Végezze el a [beépített eszközök indexelésre és lekérdezésre történő használatát ismertető oktatóanyagot](search-get-started-portal.md). Ennek keretében fontos fogalmakat sajátíthat el, és megismerkedhet a portál által szolgáltatott információkkal.

3. A .NET vagy a REST API használatával folytathatja a kódolást:

   + [A .NET SDK használata](search-howto-dotnet-sdk.md) – Bemutatja a felügyelt kódú megoldások fejlesztésének fő munkafolyamatát.  
   + [A REST API használata](https://github.com/Azure-Samples/search-rest-api-getting-started) – Ismerteti ugyanezeket a lépéseket a REST API használatával. Ezzel a rövid útmutatóval is hívhatrest API-kat postás vagy hegedűs: [Fedezze fel az Azure Cognitive Search REST API-kat.](search-get-started-postman.md)

## <a name="watch-this-video"></a>Videó megtekintése

A keresőmotor az a technológia, amelyet a mobilalkalmazások, a webhelyek és a vállalati adattárak általában használnak az információk lekéréséhez. Az Azure Cognitive Search eszközöket biztosít a nagy kereskedelmi webhelyekhez hasonló keresési élmény létrehozásához.

Ebben a 9 perces videóban megtudhatja Liam Cavanagh programmenedzsertől, hogy egy keresőmotor integrálása milyen előnyöket nyújt az alkalmazásának. A rövid bemutatók az Azure Cognitive Search fő funkcióit fedik le, és hogy néz ki egy tipikus munkafolyamat. 

>[!VIDEO https://channel9.msdn.com/Events/Connect/2016/138/player]
 
+ A 0–3. percben megismerheti a fő funkciókat és azok használati eseteit.
+ A 3–4. percben megismerheti a szolgáltatás üzembe helyezését. 
+ A 4–6. percben megismerheti az indexek Adatok importálása varázslóval való létrehozását a beépített adatkészlet használatával.
+ A 6–9. percben megismerheti a Keresési ablakot és a lekérdezések végrehajtását.
