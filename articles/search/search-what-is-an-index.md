---
title: Index definíciójának és fogalmának létrehozása
titleSuffix: Azure Cognitive Search
description: Bevezetés az Azure Cognitive Search indexelési feltételeit és fogalmait, beleértve az összetevők és a fizikai struktúra részleteit.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: d2b8b2fecbf85e6590294f1fbd7ff2a4453b9e87
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "79282782"
---
# <a name="create-a-basic-index-in-azure-cognitive-search"></a>Alapszintű index létrehozása az Azure Cognitive Searchban

Az Azure Cognitive Searchban az *index* az Azure Cognitive Search szolgáltatásban szűrt és teljes szöveges kereséshez használt *dokumentumok* és egyéb szerkezetek állandó tárolója. Elméletileg a dokumentumok az indexben kereshető adategységek. Az elektronikus kereskedelemmel foglalkozó ügyfelek például minden egyes értékesített áru, egy hírközlő szervezet pedig minden egyes cikk esetében rendelkezhet dokumentumokkal. Ezen fogalmak ismertebb, adatbázisbeli megfelelőivel élve: az *index* koncepcionálisan egy *táblához* hasonlít, a *dokumentumok* pedig nagyjából a tábla *sorainak* felelnek meg.

Ha indexet ad hozzá vagy tölt fel, az Azure Cognitive Search fizikai struktúrákat hoz létre az Ön által megadott séma alapján. Ha például az index egy mezője kereshetőként van megjelölve, akkor az adott mezőhöz fordított index jön létre. Később, amikor dokumentumokat ad hozzá vagy tölt fel, vagy keresési lekérdezéseket küld az Azure Cognitive Searchba, a keresési szolgáltatás egy adott indexére küldi a kéréseket. A dokumentum típusú értékekkel rendelkező mezők *indexelési* vagy adatfeldolgozási szolgáltatásnak nevezzük.

Létrehozhat egy indexet a portálon, [REST API](search-create-index-rest-api.md)vagy [.net SDK](search-create-index-dotnet.md)-ban.

## <a name="recommended-workflow"></a>Javasolt munkafolyamat

A jobb oldali index kialakításának megérkezése általában több iteráción keresztül történik. Az eszközök és API-k kombinációjának használatával gyorsan véglegesítheti a kialakítást.

1. Állapítsa meg, hogy használható-e [Indexelő](search-indexer-overview.md#supported-data-sources). Ha a külső adatai a támogatott adatforrások egyike, akkor az [**adatimportálás**](search-import-data-portal.md) varázsló használatával prototípust és betöltést is beállíthat.

2. Ha nem tudja használni az **importálási adatmennyiséget**, akkor továbbra is [létrehozhat egy kezdeti indexet a portálon](search-create-index-portal.md), mezők hozzáadásával, adattípusokkal és attribútumok hozzárendelésével az **index hozzáadása** lap Vezérlők használatával. A portálon láthatja, hogy mely attribútumok érhetők el különböző adattípusokhoz. Ha most ismerkedik az index kialakításával, ez hasznos lehet.

   ![Index hozzáadása lap, amely az attribútumokat adattípus szerint jeleníti meg](media/search-create-index-portal/field-attributes.png "Index hozzáadása lap, amely az attribútumokat adattípus szerint jeleníti meg")
  
   Amikor a **Létrehozás**gombra kattint, az indexet támogató összes fizikai struktúra létrejön a keresési szolgáltatásban.

3. Töltse le az index sémát a [Get index REST API](https://docs.microsoft.com/rest/api/searchservice/get-index) és egy webes tesztelési eszköz, például a [Poster](search-get-started-postman.md)használatával. Most már rendelkezik a portálon létrehozott index JSON-ábrázolásával. 

   Ezen a ponton a kód alapú megközelítésre vált. A portál nem alkalmas az iterációhoz, mert már létrehozott index nem szerkeszthető. De a további feladatokhoz a Poster és a REST is használható.

4. [Töltse be az indexet az adataival](search-what-is-data-import.md). Az Azure Cognitive Search JSON-dokumentumokat fogad el. Az adatok programozott módon történő betöltéséhez használhatja a Poster-t JSON-dokumentumokkal a kérelem hasznos adatai között. Ha az adatai nem könnyen használhatók JSON-ként, ez a lépés a legintenzívebb munkaerő.

5. Kérdezze le az indexet, vizsgálja meg az eredményeket, és ismételje meg az indexelési sémát, amíg meg nem kezdődik a várt eredmények megtekintése. Az index lekérdezéséhez használhatja a [**Search Explorert**](search-explorer.md) vagy a Poster-t.

6. Folytassa a kód használatát a terv megismétléséhez.  

Mivel a fizikai struktúrák a szolgáltatásban jönnek létre, az [indexek eldobása és](search-howto-reindex.md) újbóli létrehozása akkor szükséges, ha egy meglévő mező definíciójában lényeges módosításokat végez. Ez azt jelenti, hogy a fejlesztés során érdemes megtervezni a gyakori újraépítést. Érdemes lehet az adatai egy részhalmazával dolgozni az Újraépítés gyorsabb elvégzése érdekében. 

A portál megközelítése helyett a kód használata javasolt az ismétlődő kialakításhoz. Ha a portálon az index definícióját használja, ki kell töltenie az index definícióját minden egyes Újraépítés során. Alternatív megoldásként az olyan eszközök, mint [a Poster és a REST API](search-get-started-postman.md) hasznosak lehetnek a megvalósíthatósági teszteléshez, amikor a fejlesztési projektek még mindig korai fázisban vannak. A kérés törzsében növekményes módosításokat végezhet egy index definíciójában, majd a kérést elküldheti a szolgáltatásnak, hogy egy frissített séma használatával újra létrehozza az indexet.

## <a name="components-of-an-index"></a>Index összetevői

Az Azure Cognitive Search index sematikusan a következő elemekből áll. 

A [*mezők gyűjteménye*](#fields-collection) általában az index legnagyobb része, ahol az egyes mezők neve, beírása és attribútuma engedélyezett viselkedésmódokkal, amelyek meghatározzák a használatuk módját. A további elemek közé tartoznak a [javaslatok](#suggesters), a [pontozási profilok](#scoring-profiles)és az összetevőkkel rendelkező [elemzők](#analyzers) , amelyek támogatják a testreszabást, a [CORS](#cors) és a [titkosítási kulcs](#encryption-key) beállításait.

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

A séma meghatározásakor az index minden egyes mezőjéhez nevet, típust és attribútumokat kell rendelni. Az adott mezőben található adatok osztályozása a mező típusa szerint történik. Az egyes mezők használati módjának megadásához attribútumokat állítunk be. Az itt megadható típusokat és attribútumokat az alábbi tábla sorolja fel.

### <a name="data-types"></a>Adattípusok
| Típus | Description |
| --- | --- |
| *Edm.String* |A teljes szöveges kereséshez (Word-Breaking, fakadóan stb.) tartozó szöveg, amely lehet jogkivonatos. |
| *Collection(Edm.String)* |A teljes szöveges keresés érdekében lehetőség van a sztringlista tokenekre bontására. Az egyes gyűjteményekben lévő elemek számának nincs elméleti felső korlátja, a 16 MB-os adattartalom-méretkorlát azonban a gyűjteményekre is érvényes. |
| *Edm.Boolean* |Igaz/hamis értékeket tartalmaz. |
| *Edm.Int32* |32 bites egész számok. |
| *Edm.Int64* |64 bites egész számok. |
| *Edm.Double* |Kétszeres pontosságú numerikus adatok. |
| *Edm.DateTimeOffset* |A dátum-és időértékek a OData v4 formátumban (például `yyyy-MM-ddTHH:mm:ss.fffZ` vagy) jelennek meg `yyyy-MM-ddTHH:mm:ss.fff[+/-]HH:mm` . |
| *Edm.GeographyPoint* |A pont egy konkrét földrajzi helyet jelöl. |

Az Azure Cognitive Search [által támogatott adattípusokkal](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types)kapcsolatos részletesebb információkat itt talál.

### <a name="index-attributes"></a>Indexattribútumok

Az index pontosan egy mezőjének a kijelölt **kulcs** mezőnek kell lennie, amely egyedileg azonosítja az egyes dokumentumokat.

Más attribútumok határozzák meg, hogyan használják a mezőket egy alkalmazásban. A **kereshető** attribútum például minden olyan mezőhöz hozzá van rendelve, amelynek szerepelnie kell egy teljes szöveges keresésben. 

Az indexek létrehozásához használt API-k eltérő alapértelmezett viselkedéssel rendelkeznek. A [REST API](https://docs.microsoft.com/rest/api/searchservice/Create-Index)-k esetében a legtöbb attribútum alapértelmezés szerint engedélyezve van (például **kereshető** és lekérdezhető, hogy a karakterlánc mezőinél **igaz legyen)** , és gyakran csak be kell állítani őket, ha ki szeretné kapcsolni őket. A .NET SDK esetében az ellenkezője igaz. A nem kifejezetten beállított bármely tulajdonság esetében az alapértelmezett érték a megfelelő keresési viselkedés letiltása, kivéve, ha kifejezetten engedélyezi azt.

| Attribútum | Leírás |
| --- | --- |
| `key` |Az egyes dokumentumok egyedi azonosítóját megadó sztring, amelyet a dokumentumok keresésére használunk. Minden egyes indexnek egy kulccsal kell rendelkeznie. A kulcs kizárólag egyetlen mező lehet, annak típusa pedig Edm.String kell legyen. |
| `retrievable` |Megadja, hogy az adott mező visszaadható-e egy keresési eredményben. |
| `filterable` |Lehetővé teszi az adott mező szűrőlekérdezésekben történő használatát. |
| `Sortable` |Lehetővé teszi egy lekérdezés számára, hogy az adott mezőt használja egy rendezés alapjaként. |
| `facetable` |Lehetővé teszi, hogy egy mező [csiszolt navigációs](search-faceted-navigation.md) struktúrában legyen felhasználva a felhasználó által irányított szűréshez. Általában olyan ismétlődő értékeket tartalmazó mezők, amelyek dokumentumok csoportosítására használhatók (például adott márkához vagy szolgáltatási kategóriához tartozó dokumentumok esetében). |
| `searchable` |Azt jelzi, hogy az adott mező teljes szöveges keresésre alkalmas. |

## <a name="index-size"></a>Index mérete

Az index méretét a feltöltött dokumentumok mérete határozza meg, valamint az indexelési konfiguráció, például a javaslatok belefoglalása, valamint az attribútumok egyéni mezőkhöz való beállítása. Az alábbi képernyőfelvételen az attribútumok különböző kombinációinak eredményeként létrejövő indexek tárolási mintáit mutatjuk be.

Az index a [beépített Real Estate Sample](search-get-started-portal.md) adatforráson alapul, amelyet a portálon indexelheti és lekérdezheti. Bár az index sémái nem jelennek meg, az attribútumok az index neve alapján következtethető ki. Például a *Realestate-kereshető* indexben a **kereshető** attribútum van kiválasztva, és semmi mást sem, a *Realestate* lekérhető index a **beolvasható** attribútum van kiválasztva, semmi más, és így tovább.

![Index mérete az attribútumok kijelölése alapján](./media/search-what-is-an-index/realestate-index-size.png "Index mérete az attribútumok kijelölése alapján")

Bár ezek az index-változatok mesterségesek, az attribútumok a tárolók befolyásolásának széles körű összehasonlítására is hivatkozhatnak. **Beállítja a** lekérdezhető növelési index méretét? Nem. Növeli a mezők hozzáadását egy **javaslathoz** az index méretének növelése érdekében? Igen.

A szűrőket és a rendezést támogató indexek arányosan nagyobbak, mint a csak teljes szöveges keresések. A pontos egyezések szűrése és rendezése – a sértetlen dokumentumok jelenlétének megkövetelése. Ezzel szemben a teljes szöveges és a zavaros keresést támogató kereshető mezők invertált indexeket használnak, amelyek olyan jogkivonatokkal vannak feltöltve, amelyek kevesebb helyet foglalnak el, mint a teljes dokumentumok. 

> [!Note]
> A tárolási architektúra az Azure-Cognitive Search megvalósítási részletének minősül, és értesítés nélkül megváltozhat. Nincs garancia arra, hogy a jelenlegi viselkedés továbbra is fennmarad a jövőben.

## <a name="suggesters"></a>Javaslattevők
A javaslat a séma azon szakasza, amely meghatározza, hogy az indexben mely mezők használhatók az automatikus vagy a beírásos lekérdezések támogatásához a keresésekben. A rendszer általában a részleges keresési karakterláncokat küldi el a [javaslatok (REST API)](https://docs.microsoft.com/rest/api/searchservice/suggestions) számára, amíg a felhasználó begépel egy keresési lekérdezést, és az API a javasolt dokumentumok vagy kifejezések készletét adja vissza. 

A rendszer a javaslathoz hozzáadott mezőket használja a típus előtti keresési feltételek kiépítéséhez. Az összes keresési kifejezés az indexelés során jön létre, és külön tárolja őket. A javaslati struktúra létrehozásával kapcsolatos további információkért lásd: [javaslatok hozzáadása](index-add-suggesters.md).

## <a name="scoring-profiles"></a>Pontozási profilok

A [pontozási profil](index-add-scoring-profiles.md) a séma olyan szakasza, amely egyéni pontozási viselkedést határoz meg, amely lehetővé teszi, hogy a keresési eredményekben magasabban megjelenjenek az elemek. A pontozási profilok mezőből származó súlyok és függvények alkotják. A használatához a lekérdezési karakterláncban meg kell adnia egy profilt név alapján.

Az alapértelmezett pontozási profil a háttérben működik, hogy kiszámítsa a keresési pontszámot az eredményhalmaz minden eleménél. Használhatja a belső, nem névre vonatkozó pontozási profilt. Azt is megteheti, hogy a **defaultScoringProfile** egyéni profilt használ alapértelmezettként, ha a lekérdezési karakterláncban nincs megadva egyéni profil.

## <a name="analyzers"></a>Elemzők

Az analizátorok elem beállítja a mezőhöz használandó nyelvi elemző nevét. Az elérhető elemzők tartományával kapcsolatos további információkért lásd: [elemzők hozzáadása egy Azure Cognitive Search indexhez](search-analyzers.md). Az elemzők csak kereshető mezőkkel használhatók. Ha az analizátor egy mezőhöz van rendelve, akkor nem módosítható, ha újra létrehozza az indexet.

## <a name="cors"></a>CORS

Az ügyféloldali JavaScriptek alapértelmezés szerint nem hívhatnak meg API-kat, mivel a böngésző megakadályozza az összes kereszthivatkozási kérelmet. Ha engedélyezni szeretné az adatforrások közötti lekérdezéseket az index számára, engedélyezze a CORS (több eredetű erőforrás-megosztás) a **corsOptions** attribútum beállításával. Biztonsági okokból csak a lekérdezési API-k támogatják a CORS. 

A következő beállítások állíthatók be a CORS:

+ **allowedOrigins** (kötelező): Ez azoknak a forrásoknak a listája, amelyek hozzáférést kapnak az indexhez. Ez azt jelenti, hogy az ezektől az eredettől kiszolgált JavaScript-kódok lehetővé teszik az index lekérdezését (feltéve, hogy a megfelelő API-kulcsot biztosítja). Az egyes eredetek általában az űrlapból származnak, `protocol://<fully-qualified-domain-name>:<port>` bár `<port>` gyakran kimaradnak. További részletekért tekintse meg a több [eredetű erőforrás-megosztás (wikipedia)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) című témakört.

  Ha engedélyezni szeretné az összes eredet elérését, a `*` **allowedOrigins** tömb egyetlen elemeként adja meg. *Ez az üzemi keresési szolgáltatások esetében nem ajánlott* , de gyakran hasznos a fejlesztéshez és a hibakereséshez.

+ **maxAgeInSeconds** (nem kötelező): a böngészők ezt az értéket használják annak meghatározására, hogy mennyi ideig (másodpercben) kell gyorsítótárazni a CORS elővizsgálati válaszokat. Ennek nem negatív egész számnak kell lennie. Minél nagyobb ez az érték, annál jobb lesz a teljesítmény, de minél hosszabb ideig tart a CORS-szabályzat módosításainak érvénybe léptetése. Ha nincs beállítva, a rendszer az alapértelmezett 5 perces időtartamot használja.

## <a name="encryption-key"></a>Titkosítási kulcs

Habár a Microsoft által felügyelt kulcsokkal az összes Azure Cognitive Search-index titkosítva van, az indexek úgy konfigurálhatók, hogy az **ügyfél által felügyelt kulcsokkal** legyenek titkosítva Key Vault. További információ: [titkosítási kulcsok kezelése az Azure Cognitive Searchban](search-security-manage-encryption-keys.md).

## <a name="next-steps"></a>További lépések

Az index-összeállítás megismerésével a portálon folytathatja az első index létrehozását.

> [!div class="nextstepaction"]
> [Index hozzáadása (portál)](search-create-index-portal.md)
