---
title: Mi az Azure Search |} Microsoft Docs
description: "Az Azure Search egy teljes körűen felügyelt, üzemeltetett felhőalapú keresőszolgáltatás. További információ: a szolgáltatás áttekintését."
services: search
manager: jhubbard
author: HeidiSteen
documentationcenter: 
ms.assetid: 50bed849-b716-4cc9-bbbc-b5b34e2c6153
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 11/10/2017
ms.author: heidist
ms.openlocfilehash: 2fa637b2119e55f0ad8a0aec3926df11871e7a2a
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2018
---
# <a name="what-is-azure-search"></a>Mi az az Azure Search?
Az Azure Search olyan keresési,--szolgáltatás felhőalapú megoldás, amely lehetőséget nyújt a fejlesztők API-k, valamint eszközei egy hatékony keresési funkciók hozzáadása a webes, mobil és vállalati alkalmazások tartalma fölött.

Funkció van közzétéve, az egyszerű [REST API](/rest/api/searchservice/) vagy [.NET SDK](search-howto-dotnet-sdk.md) , amely elfedi rejlő összetettsége adatok beolvasása. API-k, mellett az Azure-portálon biztosít, felügyeleti és tartalomkezelési támogatja, prototípusának és lekérdezi-e az indexek eszközeivel. A szolgáltatás fut a felhőben, mert infrastruktúra és a rendelkezésre állás a Microsoft által felügyelt.

<a name="feature-drilldown"></a>

## <a name="feature-summary"></a>Szolgáltatás összegzése

| Kategória | Szolgáltatások |
|----------|----------|
|Teljes szöveges keresés és a szöveg elemzése | [Teljes szöveges keresés](search-lucene-query-architecture.md) egy elsődleges felhasználási eseténél legtöbb keresés-alapú alkalmazásokhoz. Lekérdezések egy támogatott szintaxis segítségével állapítható meg. <br/><br/>[**Egyszerű lekérdezés szintaxisát** ](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) biztosít a logikai operátorok, a kifejezés műveleti jeleket, a utótag operátorok, a sorrend operátorok.<br/><br/>[**Lucene lekérdezés szintaxisát** ](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) egyszerű szintaxist, az intelligens egyeztetésű keresési, közelségi kapcsolat keresési, kifejezés kiemelése és reguláris kifejezések kiterjesztésű összes műveletet tartalmaz.| 
| Adatintegráció | Az Azure Search-indexek forrásból, adatokat fogad, feltéve, egy JSON-adatszerkezet elküldve. <br/><br/> Szükség esetén a támogatott adatforrások az Azure-ban, használhat [ **indexelők** ](search-indexer-overview.md) automatikusan bejárható [Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), [Azure Cosmos DB](search-howto-index-cosmosdb.md), vagy [Azure Blob Storage tárolóban](search-howto-indexing-azure-blob-storage.md) szinkronizálása az search-index által az elsődleges adattároló tartalom. Az Azure Blob indexelők hajthat végre *dokumentum repedés* a [fő fájlformátumok indexelő](search-howto-indexing-azure-blob-storage.md), például HTML, Microsoft Office és PDF-dokumentumokat. |
| A nyelvi elemzés | Szöveg feldolgozása indexelési és keresési műveletek során használt elemzőkkel összetevői. Két típusa van. <br/><br/>[**Egyéni lexikális elemzőkkel** ](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search) fonetikus megfeleltetéssel összetett keresési lekérdezések és reguláris kifejezések használhatók. <br/><br/>[**Nyelvi elemzőkkel** ](https://docs.microsoft.com/rest/api/searchservice/language-support) Lucene vagy a Microsoft segítségével intelligens módon kezeli az adott nyelvű linguistics művelet tenses, a nemét, a szabálytalan többes számú parancsokat (például "egér" és "egerek"), a word deszerializálni összetételéhez, beleértve (a nyelveket, szóközök nélkül) szóhatároló, és több. |
| Földrajzi-keresés | Az Azure Search dolgozza fel a szűrőket, és földrajzi helyek jeleníti meg. Segítségével a felhasználók adatokba a keresési eredmény közelségbe kell kerülni a fizikai hely alapján. [A videó](https://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data) vagy [tekintse át ezt a mintát](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) további. |
| Felhasználói élményt nyújtó szolgáltatásokat | [**Keresési javaslatok** ](https://docs.microsoft.com/rest/api/searchservice/suggesters) található egy keresősáv begépelt lekérdezések engedélyezhető. Az indexben tényleges dokumentumok felhasználók meg részleges kereséshez bemeneti használata javasolt. <br/><br/>[**Jellemzőalapú navigációs** ](https://docs.microsoft.com/azure/search/search-faceted-navigation) keresztül egy lekérdezési paraméter engedélyezve van. Az Azure Search egy jellemzőalapú navigációs szerkezetben is használhatja a kategóriák listában mögötti kódban irányuló szűréséhez (például a szűrő katalóguselemek ár-tartomány vagy a márka) adja vissza. <br/><br/> [**Szűrők** ](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) segítségével jellemzőalapú navigációs beépítse az alkalmazás felhasználói felület javítása érdekében a lekérdezés létrehozását és szűrési felhasználói vagy fejlesztői megadott feltételek alapján. A szűrőkkel az OData-szintaxis használatával.<br/><br/> [**Kattintson a kijelölés** ](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) a keresési eredmények megfelelő kulcsszó szövegformázási vonatkozik. Kiválaszthatja, hogy melyik mezőkre visszaadása a kijelölt kódtöredékek.<br/><br/>[**Rendezés** ](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) érhető el a több mező a sémát indexeli keresztül, és majd váltható időben-keresés egyetlen paraméterrel.<br/><br/> [**Lapozás** ](search-pagination-page-layout.md) , és a keresési eredmények szabályozás egyszerű Azure Search nyújt, mint a keresési eredmények finom bennünket vezérlővel.  
| Találati pontosság | [**Egyszerű pontozási** ](/rest/api/searchservice/add-scoring-profiles-to-a-search-index) Azure Search egyik legfontosabb előnye van. A pontozási profil segítségével modell relevancia magukat a dokumentumok értékeinek függvényében. Például előfordulhat, hogy szeretné, hogy újabb termékek vagy kedvezményes termékek magasabb a keresési eredmények között megjelenik. Hozza létre a személyre szabott pontozó ügyfél keresési beállítások is nyomon követheti és tárolt címkék használatával pontozási profilokat is. |
| Figyelés és jelentéskészítés | [**Keresési forgalom analytics** ](search-traffic-analytics.md) gyűjtött és elemzése információkat kaphat a mi felhasználók vannak írja be a keresőmezőbe feloldásához. <br/><br/>Egy második, a késés és a sávszélesség-szabályozás a lekérdezések metrikákat, és hogy a jelentett, a portál lapjai nincs szükség további konfigurációra. Is könnyen figyelő index, és a dokumentumok száma, hogy a kapacitás szükség szerint módosíthatja. További információkért lásd: [felügyeleti szolgáltatás](search-manage.md) |
| Eszközök prototípusának és ellenőrzés | A portálon, használhatja a [ **adatok importálása varázsló** ](search-import-data-portal.md) indexelők, index designer passzív másolatot index, konfigurálása és [ **keresési ablak** ](search-explorer.md) lekérdezések tesztelése és pontosítsa a pontozási profil. A séma megtekintéséhez index is megnyithatja. |
| Infrastruktúra | A **magas rendelkezésre állású platform** service rendkívül megbízható keresési élményt biztosítja. Ha megfelelően méretezhető [Azure Search 99,9 %-os SLA-t kínál](https://azure.microsoft.com/support/legal/sla/search/v1_0/).<br/><br/> **Teljes felügyelt, méretezhető** megoldásként végpont Azure Search feltétlenül nincs infrastruktúra kezelése szükséges. A szolgáltatás további dokumentumtároló, magasabb lekérdezésekkel vagy mindkettő kezeli a két dimenziókat a skálázással az igényeinek megfelelően testre szabható.

## <a name="how-to-use-azure-search"></a>Azure Search használatával
### <a name="step-1-provision-service"></a>1. lépés: Kiépítése szolgáltatáshoz
Akkor is lépett fel az Azure Search szolgáltatás a [Azure-portálon](https://portal.azure.com/) vagy a [Azure erőforrás-kezelési API](/rest/api/searchmanagement/). Dönthet úgy, vagy az ingyenes szolgáltatás más előfizetők megosztott vagy egy [réteg fizetett](https://azure.microsoft.com/pricing/details/search/) , amely csak a szolgáltatás által használt erőforrások dedicates. A fizetett szinteken a két dimenzió egy szolgáltatás is skálázható: 

- Adja hozzá a replikák nehéz lekérdezésekkel a kapacitás növelése sikertelen volt.   
- Adja hozzá a partíciók több dokumentumokat tároló növelése sikertelen volt. 

Külön-külön kezelnek dokumentum tárolás és a lekérdezési teljesítményt, továbbá bármikor kalibrálhatja finanszírozása éles követelmények alapján.

### <a name="step-2-create-index"></a>2. lépés: Az index létrehozása
Mielőtt feltöltheti a kereshető tartalom, először meg kell határoznia az Azure Search-index. Az index olyan, mint egy adatbázis tábláinak, amely tartalmazza az adatokat és a keresési lekérdezések fogad el. Megadhatja a sémát indexeli a dokumentumok kívánja a keresést, az adatbázis mezőit hasonló struktúrájának megfelelően leképezni.

A séma létrehozott Azure-portálon vagy programozottan használatával lehet a [.NET SDK](search-howto-dotnet-sdk.md) vagy [REST API](/rest/api/searchservice/).

### <a name="step-3-index-data"></a>3. lépés: Az Index adatok
Index definiálása után készen áll a tartalom feltöltése. A lekérés és küldés modell is használhatja.

A lekéréses modell külső forrásból származó lekéri az adatokat. A támogatott *indexelők* , amely egyszerűsítésére és automatizálhatja az adatfeldolgozást, például a kapcsolódás, az olvasást és adatok szerializálása aspektusait. [Indexelő](/rest/api/searchservice/Indexer-operations) elérhető Azure Cosmos DB, az Azure SQL Database, Azure Blob Storage és egy Azure virtuális gép futó SQL-kiszolgálót. Az indexelő igény szerint vagy ütemezett Adatfrissítés konfigurálhatja.

A leküldési modelljével az SDK vagy a REST API-k, indexek frissített dokumentumok küldésére szolgáló keresztül valósul meg. A JSON formátum használatával szinte bármilyen adatkészletből adatok tolható ki. Lásd: [hozzáadása, update vagy delete dokumentumok](/rest/api/searchservice/addupdate-or-delete-documents) vagy [a .NET SDK használatával)](search-howto-dotnet-sdk.md) útmutatót az adatok betöltéséhez.

### <a name="step-4-search"></a>4. lépés: keresés
Index feltöltése, után is [keresési lekérdezések ki](/rest/api/searchservice/Search-Documents) való a REST API-t vagy a .NET SDK egyszerű HTTP-kérelmek használatával.

## <a name="how-azure-search-compares"></a>Hogyan Azure Search összehasonlítása

Az ügyfelek gyakran kérje meg, hogyan összehasonlítja az Azure Search keresési kapcsolatos megoldásait. A következő táblázat összefoglalja a fontosabb különbségeket.

| Képest | Fontos különbség |
|--|--|
|Bing | [Bing webes keresés API](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/) Bing.com indexei keres rá megfelelő feltételek elküldését. Indexek HTML, XML és egyéb webes tartalom nyilvános helyeken épülnek. [Bing egyéni keresés](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/) kínál webes ugyanazt a webbejáró technológiát egyes webhelyek hatóköre tartalomtípusokat.<br/><br/>Az Azure Search index meghatározása, adatokat és Ön a tulajdonosa, gyakran különböző forrásokból származó dokumentumokat töltődik keres. Az Azure Search néhány adatforrás keresztül webbejáró képességekkel rendelkezik [indexelők](search-indexer-overview.md), de a sémát indexeli a JSON-dokumentumában, amelyik megfelel a azokat egy egyetlen, összevont kereshető erőforrás tolható. |
|Adatbázis-keresés | [SQL Server teljes szöveges keresés](https://docs.microsoft.com/sql/relational-databases/search/full-text-search) az adatbázis-kezelő, az SQL-táblák belső tartalom van. <br/><br/>Az Azure Search heterogén forrásból származó tartalmat tárolja, és speciális szöveg például a nyelvi és egyéni elemzési feldolgozási funkciókat nyújtja. A [teljes szöveges keresés motor](search-lucene-query-architecture.md) az Azure Search épül Apache Lucene, iparági szabvány, az adatok beolvasása. <br/><br/>Erőforrás-használat egy másik nincsenek pontot. Természetes nyelvű keresési legtöbbször számításilag intenzív. Dedikált megoldásokhoz szervez keresési megőrzi a tranzakció-feldolgozást erőforrásokat. Externalizing keresés könnyen módosíthatja méretezési lekérdezés kötet kereséséhez.|
|Dedikált keresési megoldás | A helyszíni vagy felhőalapú szolgáltatási megoldások dedikált keresési megoldások teljes skálája funkciójú. Keresési technológiák általában kínálnak az indexelést és a lekérdezés folyamatok szabályozhatják, gazdagabb lekérdezés hozzáférést, és dimenziószáma és relevanciájának és irányuló és az intelligens keresési funkciók szintaxis, szűrés vezérlése. <br/><br/>Dedikált keresési megoldások tartalomhelyként a felhőalapú szolgáltatás, vagy önálló kiszolgálóként üzemeltetett a helyszínen vagy a virtuális gépen található. Egy felhőalapú szolgáltatás használata a megfelelő választás, ha azt szeretné egy [kulcsrakész megoldást minimális terhelés és karbantartási és állítható méretezési](#cloud-service-advantage). <br/><br/>Belül a felhő összeállítást a több szolgáltatók összehasonlíthatónak alapvető szolgáltatásokat, a teljes szöveges keresés, földrajzi-keresés és a keresési bemenet a félreérthetőség bizonyos szintű kezelésének képessége. Általában rendelkezik egy [speciális funkció](#feature-drilldown), vagy a könnyű, és a teljes API-k, eszközök és felügyeletéhez, amely meghatározza, hogy a legjobb térkihasználás érdekében egyszerűsége. |

Felhő szolgáltatóknál Azure Search teljes szöveges keresés munkaterhelések esetében felett tartalom tárolók és a Azure-adatbázisok elsősorban a keresést a adatok beolvasása és a tartalom navigációs használó alkalmazások esetében. Kulcs szintjeiről a következők:

+ Az indexelő rétegben Azure Adatintegráció (webbejárók)
+ Azure-portál a központi felügyeleti
+ Az Azure méretezés, a megbízhatóság és a világszínvonalú rendelkezésre állása
+ A nyelvi és egyéni elemzést követően elemzőkkel 56 nyelvű teli teljes szöveges kereséshez
+ [Alapvető szolgáltatásokat vonatkozó keresési-központú alkalmazások](#feature-drilldown): pontozási, értékkorlátozás, vonatkozó javaslatokat, szinonimák, földrajzi-keresés, és több.

> [!Note]
> A nem Azure-adatforrások teljes mértékben támogatottak, de indexelők, hanem több kód igényel ügyfélleküldéses módszer támaszkodnak. API-k használatával átadható az Azure Search-index bármely JSON dokumentumgyűjteményt.

A felhasználók között azokat az Azure Search szolgáltatások legszélesebb kihasználhatják közé tartozik a online katalógusok, üzleti programok és dokumentum felderítési alkalmazások.

## <a name="rest-api--net-sdk"></a>REST API-t |} .net SDK

Számos feladatot a portálon hajtható végre, amíg Azure Search keresési funkcióját integrálja a meglévő alkalmazások fejlesztőknek szól. A következő alkalmazásprogramozási felületek érhetők el.

|Platform |Leírás |
|-----|------------|
|[REST](/rest/api/searchservice/) | Bármely programozási platform és nyelvi, többek között a Xamarin, Java és JavaScript által támogatott HTTP-parancsok|
|[.NET SDK](search-howto-dotnet-sdk.md) | .NET-burkoló a REST API kínál hatékony kódolása a C# és a .NET-keretrendszer célzó felügyeltkód-nyelvek |

## <a name="free-trial"></a>Ingyenes próbalehetőség
Az Azure-előfizetők is [egy szolgáltatás ingyenes szint kiépítése](search-create-service-portal.md).

Ha az előfizető nem, akkor [szabad nyissa meg az Azure-fiók](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F). Jóváírásokat kap a próbálhatja ki a fizetős Azure-szolgáltatásokat. Azok lejárta után, megtarthatja a fiókot, és használjon [ingyenes Azure-szolgáltatásokat](https://azure.microsoft.com/free/). A hitelkártya soha nem feladata, kivéve, ha explicit módon a beállítások módosításához és engedélyezéséhez.

Alternatív megoldásként, [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F): az MSDN-előfizetés biztosít Önnek krediteket minden hónapban, fizetős Azure-szolgáltatásokat is használhat. 

## <a name="how-to-get-started"></a>Első lépések

1. A szolgáltatás létrehozása a [ingyenes szint](search-create-service-portal.md).

2. Legalább egy, az alábbi oktatóanyagok lépéseit. 

  + [A .NET SDK használatával](search-howto-dotnet-sdk.md) a felügyelt kódban a fő lépéseit mutatja be.  
  + [Ismerkedés a REST API-t a](https://github.com/Azure-Samples/search-rest-api-getting-started) ugyanazokat a lépéseket a REST API használatával jeleníti meg.  
  + [Az első index létrehozása a portálon](search-get-started-portal.md) beépített indexelő és prototípus funkciókat használ.   

A keresőmotorok adatok beolvasása a mobilalkalmazásokban, a weben, és a vállalati adatokat tárolja, a közös illesztőprogramok legyenek. Az Azure Search lehetővé teszi az eszközök egy keresési élményt biztosít a nagy kereskedelmi webhelyeken hasonló létrehozásához.

Ez a program manager Liam Cavanagh 9 perces videót megtudhatja, hogyan integrálható a keresőprogramok találatait is kihasználhatja az alkalmazás. Rövid bemutatók tér ki az Azure Search és egy tipikus munkafolyamat néz funkciói. 

>[!VIDEO https://channel9.msdn.com/Events/Connect/2016/138/player]
 
+ 0 – 3 perc magában foglalja az legfontosabb funkcióira és a használati eseteket.
+ 3 – 4 perc foglalkozik kiépítése szolgáltatáshoz. 
+ 4-6 percig adatok importálása varázsló használatával, a beépített ingatlan adatkészlet létrehozásához használt ismerteti.
+ 6 – 9 perc keresési ablak és különböző lekérdezések tartalmazza.


