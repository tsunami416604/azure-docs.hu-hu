---
title: Keresési index létrehozása
titleSuffix: Azure Cognitive Search
description: Bevezeti az Azure Cognitive Search indexelési fogalmait és eszközeit, beleértve a séma-definíciókat és a fizikai adatstruktúrát is.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/15/2020
ms.openlocfilehash: 3aa4a1917711f8997c282ba577c33e7a7f94472b
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88932882"
---
# <a name="create-a-basic-search-index-in-azure-cognitive-search"></a>Alapszintű keresési index létrehozása az Azure-ban Cognitive Search

Az Azure Cognitive Searchban a *keresési index* a teljes szöveges és szűrt lekérdezésekhez használt kereshető tartalmat tárolja. Az indexet egy séma határozza meg, és a szolgáltatásba menti, és az adatimportálás második lépésként történik. 

Az indexek *dokumentumokat*tartalmaznak. Elméletileg a dokumentumok az indexben kereshető adategységek. Előfordulhat, hogy egy kiskereskedő rendelkezik egy dokumentummal az egyes termékekhez, a hírekért felelős szervezet pedig minden cikkhez tartalmaz egy dokumentumot, és így tovább. Ezeket a fogalmakat több ismerős adatbázis-megfelelőnek is feltérképezheti: a *keresési index* egy *táblázatnak*felel meg, a *dokumentumok* pedig nagyjából egyenértékűek egy tábla *soraival* .

Az index fizikai szerkezetét a séma határozza meg, a "kereshető" jelölésű mezőkkel, ami az adott mezőhöz létrehozott fordított indexet eredményez. 

Létrehozhat egy indexet a következő eszközökkel és API-kkal:

* A Azure Portal használja az **index hozzáadása** vagy az **adatimportálás** varázslót.
* A [create index (REST API)](/rest/api/searchservice/create-index) használata
* A [.net SDK](./search-get-started-dotnet.md) használata

A portál eszközzel könnyebben tanulhat. A portál bizonyos adattípusokra vonatkozóan kényszeríti a követelményeket és a séma szabályait, például a teljes szöveges keresési képességek letiltását a numerikus mezőkön. Ha egy működőképes indextel rendelkezik, a JSON-definíció lekérése a szolgáltatásból a [Get index (REST API)](/rest/api/searchservice/get-index) használatával és a megoldáshoz való hozzáadásával átválthat a kódra.

## <a name="recommended-workflow"></a>Javasolt munkafolyamat

Az utolsó index kialakításának megérkezése egy iterációs folyamat. Gyakori, hogy először a portál használatával hozza létre a kezdeti indexet, majd váltson kód értékre, hogy az index a verziókövetés alá kerüljön.

1. Döntse el, hogy használhatja-e az [**importált adatimportálást**](search-import-data-portal.md). A varázsló teljes körű indexelő indexelést végez, ha a forrásadatok egy [támogatott adatforrás-típusból származnak az Azure-ban](search-indexer-overview.md#supported-data-sources).

1. Ha nem tudja használni az **adatimportálást**, kezdje az **index hozzáadása** lehetőséggel a séma definiálásához.

   ![Index hozzáadása parancs](media/search-what-is-an-index/add-index.png "Index hozzáadása parancs")

1. Adjon meg egy nevet és egy kulcsot, amellyel egyedileg azonosíthatja az egyes keresési dokumentumokat az indexben. A kulcs kötelező, és EDM. String típusúnak kell lennie. Az importálás során meg kell terveznie egy egyedi mező leképezését a forrásadatok között ebbe a mezőbe. 

   A portál egy mezőt biztosít `id` a kulcshoz. Az alapértelmezett érték felülbírálásához `id` hozzon létre egy új mezőt (például egy új mező definícióját `HotelId` ), majd jelölje ki a **kulcsban**.

   ![A szükséges tulajdonságok kitöltése](media/search-what-is-an-index//field-attributes.png "A szükséges tulajdonságok kitöltése")

1. További mezők hozzáadása. A portálon láthatja, hogy mely [mezőtulajdonságok](#index-attributes) érhetők el különböző adattípusokhoz. Ha most ismerkedik az index kialakításával, ez hasznos lehet.

   Ha a bejövő adattípusok hierarchikus jellegűek, a beágyazott struktúrákat a [komplex típus](search-howto-complex-data-types.md) adattípusa szerint kell kijelölni. A beépített mintavételi adatkészlet, a hotelek és az összetett típusok illusztrálása egy olyan címen (több almezőt tartalmaz), amely egy-az-egyhez kapcsolattal rendelkezik az egyes szolgáltatásokkal, valamint a szobák összetett gyűjteménye, ahol több szoba van társítva az egyes szolgáltatásokhoz. 

1. Az index létrehozása előtt rendeljen hozzá egy [elemzőt](#analyzers) a karakterlánc mezőihez. Ha engedélyezni szeretné az automatikus kiegészítést adott mezőkön, tegye ugyanezt a [javaslatokat](#suggesters) .

1. Kattintson a **Létrehozás** elemre a keresési szolgáltatásban található fizikai struktúrák létrehozásához.

1. Az index létrehozása után további parancsokat is megadhat a definíciók áttekintéséhez vagy további elemek hozzáadásához.

   ![Index hozzáadása lap, amely az attribútumokat adattípus szerint jeleníti meg](media/search-what-is-an-index//field-definitions.png "Index hozzáadása lap, amely az attribútumokat adattípus szerint jeleníti meg")

1. Töltse le az index sémát a [Get index (REST API)](/rest/api/searchservice/get-index) és egy webes tesztelési eszköz, például a [Poster](search-get-started-postman.md)használatával. Most már rendelkezik az index JSON-ábrázolásával, amelyet a kód számára is adaptálhat.

1. [Töltse be az indexet az adataival](search-what-is-data-import.md). Az Azure Cognitive Search JSON-dokumentumokat fogad el. Az adatok programozott módon történő betöltéséhez használhatja a Poster-t JSON-dokumentumokkal a kérelem hasznos adatai között. Ha az adatai nem könnyen használhatók JSON-ként, ez a lépés a legintenzívebb munkaerő. 

    Az indexek adatba való betöltését követően a meglévő mezők többségének szerkesztéséhez el kell dobnia és újra létre kell építenie egy indexet.

1. Kérdezze le az indexet, vizsgálja meg az eredményeket, és ismételje meg az indexelési sémát, amíg meg nem kezdődik a várt eredmények megtekintése. Az index lekérdezéséhez használhatja a [**Search Explorert**](search-explorer.md) vagy a Poster-t.

A fejlesztés során tervezze meg a gyakori újraépítést. Mivel a fizikai struktúrák a szolgáltatásban jönnek létre, az [indexek eldobása és](search-howto-reindex.md) újbóli létrehozása szükséges ahhoz, hogy a legtöbb módosítást egy meglévő mező határozza meg. Érdemes lehet az adatai egy részhalmazával dolgozni az Újraépítés gyorsabb elvégzése érdekében. 

> [!Tip]
> A portál megközelítése helyett a kód használata ajánlott az indexek tervezéséhez és az adatimportáláshoz. Alternatív megoldásként az olyan eszközök, mint [a Poster és a REST API](search-get-started-postman.md) hasznosak lehetnek a megvalósíthatósági teszteléshez, amikor a fejlesztési projektek még mindig korai fázisban vannak. A kérés törzsében növekményes módosításokat végezhet egy index definíciójában, majd a kérést elküldheti a szolgáltatásnak, hogy egy frissített séma használatával újra létrehozza az indexet.

## <a name="index-schema"></a>Index séma

Egy indexre van szükség ahhoz, hogy egy név és egy kijelölt kulcs mező (EDM. String) legyen a mezők gyűjteményében. A [*mezők gyűjteménye*](#fields-collection) általában az index legnagyobb része, ahol az egyes mezők neve, beírása és attribútuma engedélyezett viselkedésmódokkal, amelyek meghatározzák a használatuk módját. 

Más elemek közé tartoznak a [javaslatok](#suggesters), a [pontozási profilok](#scoringprofiles), a karakterláncok jogkivonatba való feldolgozásához használt [elemzők](#analyzers) , a nyelvi szabályok vagy az analizátor által támogatott egyéb jellemzők, valamint a [CORS](#corsoptions) -beállítások alapján.

```json
{
  "name": (optional on PUT; required on POST) "name_of_index",
  "fields": [
    {
      "name": "name_of_field",
      "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",
      "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),
      "filterable": true (default) | false,
      "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),
      "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),
      "key": true | false (default, only Edm.String fields can be keys),
      "retrievable": true (default) | false,
      "analyzer": "name_of_analyzer_for_search_and_indexing", (only if 'searchAnalyzer' and 'indexAnalyzer' are not set)
      "searchAnalyzer": "name_of_search_analyzer", (only if 'indexAnalyzer' is set and 'analyzer' is not set)
      "indexAnalyzer": "name_of_indexing_analyzer", (only if 'searchAnalyzer' is set and 'analyzer' is not set)
      "synonymMaps": [ "name_of_synonym_map" ] (optional, only one synonym map per field is currently supported)
    }
  ],
  "suggesters": [
    {
      "name": "name of suggester",
      "searchMode": "analyzingInfixMatching",
      "sourceFields": ["field1", "field2", ...]
    }
  ],
  "scoringProfiles": [
    {
      "name": "name of scoring profile",
      "text": (optional, only applies to searchable fields) {
        "weights": {
          "searchable_field_name": relative_weight_value (positive #'s),
          ...
        }
      },
      "functions": (optional) [
        {
          "type": "magnitude | freshness | distance | tag",
          "boost": # (positive number used as multiplier for raw score != 1),
          "fieldName": "...",
          "interpolation": "constant | linear (default) | quadratic | logarithmic",
          "magnitude": {
            "boostingRangeStart": #,
            "boostingRangeEnd": #,
            "constantBoostBeyondRange": true | false (default)
          },
          "freshness": {
            "boostingDuration": "..." (value representing timespan leading to now over which boosting occurs)
          },
          "distance": {
            "referencePointParameter": "...", (parameter to be passed in queries to use as reference location)
            "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
          },
          "tag": {
            "tagsParameter": "..." (parameter to be passed in queries to specify a list of tags to compare against target fields)
          }
        }
      ],
      "functionAggregation": (optional, applies only when functions are specified) 
        "sum (default) | average | minimum | maximum | firstMatching"
    }
  ],
  "analyzers":(optional)[ ... ],
  "charFilters":(optional)[ ... ],
  "tokenizers":(optional)[ ... ],
  "tokenFilters":(optional)[ ... ],
  "defaultScoringProfile": (optional) "...",
  "corsOptions": (optional) {
    "allowedOrigins": ["*"] | ["origin_1", "origin_2", ...],
    "maxAgeInSeconds": (optional) max_age_in_seconds (non-negative integer)
  },
  "encryptionKey":(optional){
    "keyVaultUri": "azure_key_vault_uri",
    "keyVaultKeyName": "name_of_azure_key_vault_key",
    "keyVaultKeyVersion": "version_of_azure_key_vault_key",
    "accessCredentials":(optional){
      "applicationId": "azure_active_directory_application_id",
      "applicationSecret": "azure_active_directory_application_authentication_key"
    }
  }
}
```

<a name="fields-collection"></a>

## <a name="fields-collection-and-field-attributes"></a>Mezők gyűjteménye és a mező attribútumai

A mezők névvel, a tárolt adat osztályozására szolgáló típussal és a mező használatának módját megadó attribútumokkal rendelkeznek.

### <a name="data-types"></a>Adattípusok

| Típus | Leírás |
|------|-------------|
| Edm.String |A teljes szöveges kereséshez (Word-Breaking, fakadóan stb.) tartozó szöveg, amely lehet jogkivonatos. |
| Collection(Edm.String) |A teljes szöveges keresés érdekében lehetőség van a sztringlista tokenekre bontására. Az egyes gyűjteményekben lévő elemek számának nincs elméleti felső korlátja, a 16 MB-os adattartalom-méretkorlát azonban a gyűjteményekre is érvényes. |
| Edm.Boolean |Igaz/hamis értékeket tartalmaz. |
| Edm.Int32 |32 bites egész számok. |
| Edm.Int64 |64 bites egész számok. |
| Edm.Double |Kétszeres pontosságú numerikus adatok. |
| Edm.DateTimeOffset |A dátum-és időértékek a OData v4 formátumban (például `yyyy-MM-ddTHH:mm:ss.fffZ` vagy) jelennek meg `yyyy-MM-ddTHH:mm:ss.fff[+/-]HH:mm` . |
| Edm.GeographyPoint |A pont egy konkrét földrajzi helyet jelöl. |

További információ: [támogatott adattípusok](/rest/api/searchservice/Supported-data-types).

<a name="index-attributes"></a>

### <a name="attributes"></a>Attribútumok

A mezőtulajdonságok határozzák meg egy mező használati módját, például hogy használva lesz-e teljes szöveges keresésben, kategorizált navigációban, rendezési műveletekben stb. 

A karakterlánc típusú mezők gyakran "kereshető" és "lekérhető" jelöléssel rendelkeznek. A keresési eredmények szűkítéséhez használt mezők közé tartoznak a "rendezhető", a "szűrhető" és a "sokrétű".

|Attribútum|Leírás|  
|---------------|-----------------|  
|kereshető |Kereshető – Teljes szöveges keresésre alkalmas, az indexelés során lexikai elemzés, például szavakra bontás végezhető rajta. Ha egy kereshető mező értékének például a „sunny day” szöveget adja meg, akkor az két különálló tokenre lesz bontva: „sunny” és „day”. További részletek az [A teljes szöveges keresés működése](search-lucene-query-architecture.md) című cikkben.|  
|szűrhető |Szűrhető – A $filter lekérdezések hivatkoznak rá. Az `Edm.String` vagy `Collection(Edm.String)` típusú szűrhető mezők nem lesznek szavakra bontva, ezért összehasonlítások csak pontos egyezésekre végezhetők. Ha egy ilyen mező értékének például a „sunny day” szöveget adja meg, akkor a `$filter=f eq 'sunny'` keresés nem talál egyezést, a `$filter=f eq 'sunny day'` viszont igen. |  
|rendezhető |Rendezhető – A rendszer alapértelmezés szerint érték szerint rendezi a találatokat, de a dokumentumok mezői alapján végzett rendezés is konfigurálható. A típusú mezők `Collection(Edm.String)` nem lehetnek "rendezve". |  
|kategorizálható |Kategorizálható – Általában akkor használják, ha a keresési eredményeket a kategóriánkénti találatok számával együtt kell bemutatni (például szállodák egy adott városban). Ez a lehetőség az `Edm.GeographyPoint` típusú mezők esetén nem használható. A `Edm.String` szűrhető, "rendezhető" vagy "sokrétű" típusú mezők legfeljebb 32 kilobájt hosszúságú lehetnek. Részletek az [Index létrehozása (REST API)](/rest/api/searchservice/create-index) című cikkben.|  
|kulcs |Kulcs – Az indexen belüli dokumentumok egyedi azonosítója. Kulcsmezőként egyetlen `Edm.String` típusú mezőt kell megadni.|  
|visszakereshetőnek |Lekérdezhető – Megadja, hogy a mező visszaadható-e egy keresési eredményben. Ez akkor hasznos, ha egy mező (például *nyereség*) szűrésre, rendezésre vagy értékelésre szolgál, de a végfelhasználó számára nem kell megjelennie. Ennek a tulajdonságnak az értéke `key` tulajdonságú mezők esetén csak `true` lehet.|  

Új mezők bármikor megadhatók, a meglévő meződefiníciók azonban az index élettartamára rögzülnek. A fejlesztők ezért általában egyszerű indexek létrehozására vagy ötletek kipróbálására használják a portált, vagy a portál oldalain néznek utána egy beállításnak. Egy index rendszeres kiegészítése hatékonyabb az index újraépítését megkönnyítő kódalapú megközelítéssel.

> [!NOTE]
> Az indexek létrehozásához használt API-k eltérő alapértelmezett viselkedéssel rendelkeznek. A [REST API](/rest/api/searchservice/Create-Index)-k esetében a legtöbb attribútum alapértelmezés szerint engedélyezve van (például a "kereshető" és a "lekérhető" igaz a karakterlánc mezőknél), és gyakran csak akkor kell beállítania őket, ha ki szeretné kapcsolni őket. A .NET SDK esetében az ellenkezője igaz. A nem kifejezetten beállított bármely tulajdonság esetében az alapértelmezett érték a megfelelő keresési viselkedés letiltása, kivéve, ha kifejezetten engedélyezi azt.

## `analyzers`

Az analizátorok elem beállítja a mezőhöz használandó nyelvi elemző nevét. Az elérhető elemzők tartományával kapcsolatos további információkért lásd: [elemzők hozzáadása egy Azure Cognitive Search indexhez](search-analyzers.md). Az elemzők csak kereshető mezőkkel használhatók. Ha az analizátor egy mezőhöz van rendelve, akkor nem módosítható, ha újra létrehozza az indexet.

## `suggesters`

A javaslat a séma azon szakasza, amely meghatározza, hogy az indexben mely mezők használhatók az automatikus vagy a beírásos lekérdezések támogatásához a keresésekben. A rendszer általában a részleges keresési karakterláncokat küldi el a [javaslatok (REST API)](/rest/api/searchservice/suggestions) számára, amíg a felhasználó begépel egy keresési lekérdezést, és az API a javasolt dokumentumok vagy kifejezések készletét adja vissza. 

A rendszer a javaslathoz hozzáadott mezőket használja a típus előtti keresési feltételek kiépítéséhez. Az összes keresési kifejezés az indexelés során jön létre, és külön tárolja őket. A javaslati struktúra létrehozásával kapcsolatos további információkért lásd: [javaslatok hozzáadása](index-add-suggesters.md).

## `corsOptions`

Az ügyféloldali JavaScriptek alapértelmezés szerint nem hívhatnak meg API-kat, mivel a böngésző megakadályozza az összes kereszthivatkozási kérelmet. Ha engedélyezni szeretné az adatforrások közötti lekérdezéseket az index számára, engedélyezze a CORS (több eredetű erőforrás-megosztás) a **corsOptions** attribútum beállításával. Biztonsági okokból csak a lekérdezési API-k támogatják a CORS. 

A következő beállítások állíthatók be a CORS:

+ **allowedOrigins** (kötelező): Ez azoknak a forrásoknak a listája, amelyek hozzáférést kapnak az indexhez. Ez azt jelenti, hogy az ezektől az eredettől kiszolgált JavaScript-kódok lehetővé teszik az index lekérdezését (feltéve, hogy a megfelelő API-kulcsot biztosítja). Az egyes eredetek általában az űrlapból származnak, `protocol://<fully-qualified-domain-name>:<port>` bár `<port>` gyakran kimaradnak. További részletekért tekintse meg a több [eredetű erőforrás-megosztás (wikipedia)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) című témakört.

  Ha engedélyezni szeretné az összes eredet elérését, a `*` **allowedOrigins** tömb egyetlen elemeként adja meg. *Ez az üzemi keresési szolgáltatások esetében nem ajánlott* , de gyakran hasznos a fejlesztéshez és a hibakereséshez.

+ **maxAgeInSeconds** (nem kötelező): a böngészők ezt az értéket használják annak meghatározására, hogy mennyi ideig (másodpercben) kell gyorsítótárazni a CORS elővizsgálati válaszokat. Ennek nem negatív egész számnak kell lennie. Minél nagyobb ez az érték, annál jobb lesz a teljesítmény, de minél hosszabb ideig tart a CORS-szabályzat módosításainak érvénybe léptetése. Ha nincs beállítva, a rendszer az alapértelmezett 5 perces időtartamot használja.

## `scoringProfiles`

A [pontozási profil](index-add-scoring-profiles.md) a séma olyan szakasza, amely egyéni pontozási viselkedést határoz meg, amely lehetővé teszi, hogy a keresési eredményekben magasabban megjelenjenek az elemek. A pontozási profilok mezőből származó súlyok és függvények alkotják. A használatához a lekérdezési karakterláncban meg kell adnia egy profilt név alapján.

Az alapértelmezett pontozási profil a háttérben működik, hogy kiszámítsa a keresési pontszámot az eredményhalmaz minden eleménél. Használhatja a belső, nem névre vonatkozó pontozási profilt. Azt is megteheti, hogy a **defaultScoringProfile** egyéni profilt használ alapértelmezettként, ha a lekérdezési karakterláncban nincs megadva egyéni profil.

<a name="index-size"></a>

## <a name="attributes-and-index-size-storage-implications"></a>Attribútumok és index mérete (tárolási vonzatok)

Az index méretét a feltöltött dokumentumok mérete határozza meg, valamint az indexelési konfiguráció, például a javaslatok belefoglalása, valamint az attribútumok egyéni mezőkhöz való beállítása. 

Az alábbi képernyőfelvételen az attribútumok különböző kombinációinak eredményeként létrejövő indexek tárolási mintáit mutatjuk be. Az index a **Real Estate Sample indexen**alapul, amelyet egyszerűen létrehozhat az adatimportálás varázsló segítségével. Bár az index sémái nem jelennek meg, az attribútumok az index neve alapján következtethető ki. Például a *Realestate-kereshető* index a "kereshető" attribútummal van kiválasztva, és semmi mást sem, a *Realestate* lekérhető indexben a "lekérhető" attribútum van kiválasztva, semmi más, és így tovább.

![Index mérete az attribútumok kijelölése alapján](./media/search-what-is-an-index/realestate-index-size.png "Index mérete az attribútumok kijelölése alapján")

Bár ezek az index-változatok mesterségesek, az attribútumok a tárolók befolyásolásának széles körű összehasonlítására is hivatkozhatnak. Megnövelhető az index mérete? Nem. Növeli a mezők hozzáadását egy **javaslathoz** az index méretének növelése érdekében? Igen.

A szűrőket és a rendezést támogató indexek a csak teljes szöveges keresést támogató indexek arányosan nagyobbak. Ennek az az oka, hogy a szűrési és rendezési műveletek pontos egyezéseket keresnek, és a Verbatim szöveges karakterláncok jelenlétét igénylik. Ezzel szemben a teljes szöveges lekérdezéseket támogató kereshető mezők fordított indexeket használnak, amelyek olyan jogkivonatokkal vannak feltöltve, amelyek kevesebb helyet foglalnak el, mint a teljes dokumentumok. 

> [!Note]
> A tárolási architektúra az Azure-Cognitive Search megvalósítási részletének minősül, és értesítés nélkül megváltozhat. Nincs garancia arra, hogy a jelenlegi viselkedés továbbra is fennmarad a jövőben.

## <a name="next-steps"></a>További lépések

Az index-összeállítás megismerésével a portálon folytathatja az első index létrehozását. Javasoljuk, hogy az **adatimportálás** varázslóval Kezdje a *Realestate-US-Sample* vagy a *Hotels-Sample* Hosted adatforrások kiválasztásával.

> [!div class="nextstepaction"]
> [Adatimportálási varázsló (portál)](search-get-started-portal.md)

Mindkét adatkészlet esetében a varázsló következtetheti az indexelési sémát, importálhatja az adatokat, és olyan kereshető indexet adhat kimenetként, amelyet a Search Explorer használatával tud lekérdezni. Keresse meg ezeket az adatforrásokat az **adatimportálás** varázsló **Kapcsolódás az adataihoz** lapján.

   ![Minta index létrehozása](media/search-what-is-an-index//import-wizard-sample-data.png "Minta index létrehozása")