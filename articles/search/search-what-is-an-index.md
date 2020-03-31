---
title: Indexdefiníció és fogalmak létrehozása
titleSuffix: Azure Cognitive Search
description: Az Azure Cognitive Search indexelési kifejezései és fogalmai, beleértve az összetevőket és a fizikai struktúrát is.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: d2b8b2fecbf85e6590294f1fbd7ff2a4453b9e87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282782"
---
# <a name="create-a-basic-index-in-azure-cognitive-search"></a>Alapindex létrehozása az Azure Cognitive Search szolgáltatásban

Az Azure Cognitive Search, az *index* egy állandó tárolja a *dokumentumok* és egyéb konstrukciók használt szűrt és teljes szöveges keresés egy Azure Cognitive Search szolgáltatás. Fogalmilag a dokumentum a kereshető adatok egyetlen egységét tartalmazza az indexben. Az elektronikus kereskedelemmel foglalkozó ügyfelek például minden egyes értékesített áru, egy hírközlő szervezet pedig minden egyes cikk esetében rendelkezhet dokumentumokkal. Ezen fogalmak ismertebb, adatbázisbeli megfelelőivel élve: az *index* koncepcionálisan egy *táblához* hasonlít, a *dokumentumok* pedig nagyjából a tábla *sorainak* felelnek meg.

Index hozzáadásakor vagy feltöltésekor az Azure Cognitive Search fizikai struktúrákat hoz létre a megadott séma alapján. Ha például az index egy mezője kereshetőként van megjelölve, akkor a mezőhöz fordított index jön létre. Később, amikor dokumentumokat ad hozzá vagy tölt fel, vagy keresési lekérdezéseket küld az Azure Cognitive Search szolgáltatásba, kéréseket küld egy adott indexnek a keresési szolgáltatásban. A dokumentumértékekkel rendelkező mezők betöltését *indexelésnek* vagy adatbetöltésnek nevezzük.

Indexet létrehozhat a portálon, a [REST API-ban](search-create-index-rest-api.md)vagy a [.NET SDK-ban.](search-create-index-dotnet.md)

## <a name="recommended-workflow"></a>Ajánlott munkafolyamat

A megfelelő indexkialakítás rakoncátrálást általában több iterációval lehet elérni. Az eszközök és API-k kombinációjával gyorsan véglegesítheti a tervet.

1. Határozza meg, hogy használhat-e [indexelőt.](search-indexer-overview.md#supported-data-sources) Ha a külső adatok a támogatott adatforrások egyike, az [**Adatok importálása**](search-import-data-portal.md) varázslóval prototípust hozhat ki és tölthet be.

2. Ha nem tudja használni **az Adatok importálása**lehetőséget, akkor is [létrehozhat kezdeti indexet a portálon,](search-create-index-portal.md)mezőket, adattípusokat adhat hozzá, és attribútumokat adhat hozzá az **Index hozzáadása** lap vezérlőivel. A portál megmutatja, hogy mely attribútumok érhetők el a különböző adattípusokhoz. Ha most jön az index tervezés, ez hasznos.

   ![Indexlap hozzáadása az attribútumokat adattípus szerint megjelenítő lapon](media/search-create-index-portal/field-attributes.png "Indexlap hozzáadása az attribútumokat adattípus szerint megjelenítő lapon")
  
   Ha a **Létrehozás**gombra kattint, az indexet támogató összes fizikai struktúra létrejön a keresési szolgáltatásban.

3. Töltse le az indexsémát a [Get Index REST API](https://docs.microsoft.com/rest/api/searchservice/get-index) és egy webes tesztelő eszköz, például a [Postman](search-get-started-postman.md)segítségével. Most már rendelkezik a portálon létrehozott index JSON-ábrázolását. 

   Ezen a ponton kódalapú megközelítésre vált. A portál nem alkalmas iterációra, mert nem szerkeszthető egy már létrehozott index. A többi feladathoz azonban használhatja a Postman és a REST feladatokat.

4. [Töltse be az indexet adatokkal.](search-what-is-data-import.md) Az Azure Cognitive Search elfogadja a JSON-dokumentumokat. Az adatok programozott betöltéséhez használhatja a Postman t a JSON-dokumentumokat a kérelem hasznos adatában. Ha az adatok nem könnyen kifejezhetőJSON-ként, ez a lépés lesz a leginkább munkaigényes.

5. Lekérdezi az indexet, vizsgálja meg az eredményeket, és tovább itefedse az indexsémát, amíg meg nem kezdi a várt eredmények megtekintését. A [**Kereséskezelő**](search-explorer.md) vagy a Postman segítségével lekérdezheti az indexet.

6. Továbbra is használja a kódot, hogy iterálni át a design.  

Mivel a szolgáltatásban fizikai struktúrák jönnek létre, [az indexek elvetése és újbóli létrehozása](search-howto-reindex.md) szükséges, amikor lényeges módosításokat hajt végre egy meglévő meződefiníción. Ez azt jelenti, hogy a fejlesztés során meg kell terveznie a gyakori újraépítéseket. Érdemes lehet az adatok egy részhalmazával dolgozni, hogy az újraépítések gyorsabban menjenek. 

Az iteratív tervezéshez a kód, nem pedig a portálos megközelítés ajánlott. Ha a portálindex-definícióra támaszkodik, minden egyes újraépítésnél ki kell töltenie az indexdefiníciót. Alternatív megoldásként az olyan eszközök, mint [a Postman és a REST API,](search-get-started-postman.md) hasznosak a koncepcióigazolásos teszteléshez, amikor a fejlesztési projektek még korai fázisban vannak. Növekményes módosításokat hajthat végre egy indexdefiníción egy kérelemtörzsben, majd elküldheti a kérést a szolgáltatásnak, hogy hozzon létre egy indexet egy frissített sémával.

## <a name="components-of-an-index"></a>Az index összetevői

Sematikusan egy Azure Cognitive Search index a következő elemekből áll. 

A [*mezőgyűjtemény*](#fields-collection) általában az index legnagyobb része, ahol minden mező neve, beírása és hozzárendelt viselkedése megengedett viselkedéssel rendelkezik, amely meghatározza a használat módját. Egyéb elemek közé tartozik [a javaslatindítók,](#suggesters) [pontozási profilok](#scoring-profiles), [elemzők](#analyzers) összetevőivel a testreszabás támogatása érdekében, [CORS](#cors) és [titkosítási kulcs](#encryption-key) beállítások.

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

## <a name="fields-collection-and-field-attributes"></a>Mezők gyűjteményének és mezőattribútumainak gyűjtése

A séma meghatározásakor az index minden egyes mezőjéhez nevet, típust és attribútumokat kell rendelni. Az adott mezőben található adatok osztályozása a mező típusa szerint történik. Az egyes mezők használati módjának megadásához attribútumokat állítunk be. Az itt megadható típusokat és attribútumokat az alábbi tábla sorolja fel.

### <a name="data-types"></a>Adattípusok
| Típus | Leírás |
| --- | --- |
| *Edm.String* |A teljes szöveges kereséshez tetszés szerint tokenizálható szöveg (szótörés, eredő stb.). |
| *Collection(Edm.String)* |A teljes szöveges keresés érdekében lehetőség van a sztringlista tokenekre bontására. Az egyes gyűjteményekben lévő elemek számának nincs elméleti felső korlátja, a 16 MB-os adattartalom-méretkorlát azonban a gyűjteményekre is érvényes. |
| *Edm.Boolean* |Igaz/hamis értékeket tartalmaz. |
| *Edm.Int32* |32 bites egész számok. |
| *Edm.Int64* |64 bites egész számok. |
| *Edm.Double* |Kétszeres pontosságú numerikus adatok. |
| *Edm.DateTimeOffset* |Az OData V4 formátumban (például `yyyy-MM-ddTHH:mm:ss.fffZ` vagy `yyyy-MM-ddTHH:mm:ss.fff[+/-]HH:mm`) képviselt dátumidő-értékek |
| *Edm.GeographyPoint* |A pont egy konkrét földrajzi helyet jelöl. |

Az Azure Cognitive Search által támogatott [adattípusokról](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types)itt talál részletesebb információkat.

### <a name="index-attributes"></a>Indexattribútumok

Az indexben pontosan egy mezőnek kell lennie a **kijelölt kulcsmezőnek,** amely egyedileg azonosítja az egyes dokumentumokat.

Más attribútumok határozzák meg, hogyan használja a mezőt az alkalmazásban. A **kereshető** attribútum például minden olyan mezőhöz hozzá van rendelve, amelyet a teljes szöveges keresésnek tartalmaznia kell. 

Az index létrehozásához használt API-k eltérő alapértelmezett viselkedéssel rendelkeznek. A [REST API-k](https://docs.microsoft.com/rest/api/searchservice/Create-Index)esetében a legtöbb attribútum alapértelmezés szerint engedélyezve van (például a **kereshető** és **visszakereshető** karakterláncmezőkre igaz), és gyakran csak akkor kell beállítaniőket, ha ki szeretné őket kapcsolni. A .NET SDK esetében ennek az ellenkezője igaz. Minden olyan tulajdonságnál, amelyet nem állít be kifejezetten, az alapértelmezett beállítás a megfelelő keresési viselkedés letiltása, kivéve, ha kifejezetten engedélyezi azt.

| Attribútum | Leírás |
| --- | --- |
| `key` |Az egyes dokumentumok egyedi azonosítóját megadó sztring, amelyet a dokumentumok keresésére használunk. Minden egyes indexnek egy kulccsal kell rendelkeznie. A kulcs kizárólag egyetlen mező lehet, annak típusa pedig Edm.String kell legyen. |
| `retrievable` |Megadja, hogy az adott mező visszaadható-e egy keresési eredményben. |
| `filterable` |Lehetővé teszi az adott mező szűrőlekérdezésekben történő használatát. |
| `Sortable` |Lehetővé teszi egy lekérdezés számára, hogy az adott mezőt használja egy rendezés alapjaként. |
| `facetable` |Lehetővé teszi, hogy egy mezőt [egy felületes navigációs](search-faceted-navigation.md) struktúrában használja a felhasználó saját irányítású szűréséhez. Általában olyan ismétlődő értékeket tartalmazó mezők, amelyek dokumentumok csoportosítására használhatók (például adott márkához vagy szolgáltatási kategóriához tartozó dokumentumok esetében). |
| `searchable` |Azt jelzi, hogy az adott mező teljes szöveges keresésre alkalmas. |

## <a name="index-size"></a>Index mérete

Az index méretét a feltöltött dokumentumok mérete, valamint az indexkonfiguráció határozza meg, például az, hogy szerepel-e javaslatajánló, és hogyan állítja be az attribútumokat az egyes mezőkhöz. A következő képernyőkép az attribútumok különböző kombinációiból származó indextárolási mintákat mutatja be.

Az index a [beépített ingatlanminta](search-get-started-portal.md) adatforráson alapul, amelyet indexelhet és lekérdezhet a portálon. Bár az indexsémák nem jelennek meg, az attribútumok az index neve alapján következtethetők. Például, *ingatlan-kereshető* index a **kereshető** attribútum kiválasztva, és semmi más, *realestate-visszakereshető* index a **visszakereshető** attribútum kiválasztva, és semmi más, és így tovább.

![Indexmérete az attribútumkijelölés alapján](./media/search-what-is-an-index/realestate-index-size.png "Indexmérete az attribútumkijelölés alapján")

Bár ezek az indexváltozatok mesterségesek, az attribútumok tárolási hatásának széles körű összehasonlításához hivatkozhatunk rájuk. A **visszakereshető** beállítás növeli az index méretét? Nem. A mezők **javaslatajánlóhoz** való hozzáadása növeli az index méretét? Igen.

A szűrőt és rendezést támogató indexek arányosan nagyobbak, mint a teljes szöveges keresést támogató indexek. A szűrési és rendezési műveletek pontos egyezéseket keresnek, és érintetlen dokumentumok jelenlétét igénylik. Ezzel szemben a teljes szöveges és intelligens kereséseket támogató kereshető mezők fordított indexeket használnak, amelyek tokenizált kifejezésekkel vannak feltöltve, amelyek kevesebb helyet foglalnak el, mint a teljes dokumentumok. 

> [!Note]
> A tárolási architektúra az Azure Cognitive Search implementációs részleteinek számít, és előzetes értesítés nélkül változhat. Nincs garancia arra, hogy a jelenlegi viselkedés a jövőben is megmarad.

## <a name="suggesters"></a>Javaslattevők
A javaslatajánló a séma azon szakasza, amely meghatározza, hogy az index mely mezői használhatók az automatikus kiegészítés vagy a befuttatáselőtti lekérdezések támogatására a keresésekben. A részleges keresési karakterláncok általában a [javaslatok (REST API)](https://docs.microsoft.com/rest/api/searchservice/suggestions) számára kerülnek elküldésre, miközben a felhasználó keresési lekérdezést ír be, és az API a javasolt dokumentumok vagy kifejezések készletét adja vissza. 

A javaslatajánlóhoz hozzáadott mezők a típus-ahead keresési kifejezések létrehozásához használatosak. Az összes keresési kifejezés az indexelés során jön létre, és külön tárolja őket. A javaslatajánlói struktúra létrehozásáról a [Javaslatjavasolók hozzáadása című](index-add-suggesters.md)témakörben talál további információt.

## <a name="scoring-profiles"></a>Pontozási profilok

A [pontozási profil](index-add-scoring-profiles.md) a séma egy olyan szakasza, amely meghatározza az egyéni pontozási viselkedéseket, amelyek segítségével befolyásolhatja, hogy mely elemek jelennek meg magasabban a keresési eredményekközött. A pontozási profilok mezősúlyokból és funkciókból állnak. A használatukhoz a lekérdezési karakterláncban név szerint kell megadni a profilt.

Az alapértelmezett pontozási profil a színfalak mögött működik, hogy kiszámítsa a keresési pontszámot az eredményhalmaz minden elemére. Használhatja a belső, névtelen pontozási profilt. Másik lehetőségként állítsa be **a defaultScoringProfile-t,** hogy egy egyéni profilt használjon alapértelmezettként, és akkor hívja meg, ha a lekérdezési karakterláncban nincs megadva egyéni profil.

## <a name="analyzers"></a>Elemzők

Az analizátorok elem beállítja a mezőhöz használandó nyelvi elemző nevét. Az elemzők elérhető tartományáról további információt az [Elemzők hozzáadása az Azure Cognitive Search indexhez című témakörben](search-analyzers.md)talál. Az elemzők csak kereshető mezőkkel használhatók. Miután az analizátor hozzá van rendelve egy mezőhöz, az csak akkor módosítható, ha újraépíti az indexet.

## <a name="cors"></a>CORS

Az ügyféloldali JavaScript alapértelmezés szerint nem tud API-kat hívni, mivel a böngésző megakadályozza az összes kereszteredetű kérelmet. Ha engedélyezni szeretné a kereszteredetű lekérdezéseket az indexhez, engedélyezze a CORS (Cross-Origin Resource Sharing) beállítást a **corsOptions** attribútum beállításával. Biztonsági okokból csak a lekérdezési API-k támogatják a CORS-t. 

A CORS-hoz a következő beállítások adhatók meg:

+ **allowedOrigins** (kötelező): Ez az eredeti eredetek listája, amelyek hozzáférést kapnak az indexhez. Ez azt jelenti, hogy az ezekből az eredetből kiszolgált JavaScript-kódok lekérdezhetik az indexet (feltéve, hogy a megfelelő api-kulcs). Minden origó általában `protocol://<fully-qualified-domain-name>:<port>` az `<port>` űrlapból származik, bár gyakran kimaradnak. További részletekért lásd [a több forrásból származó erőforrások megosztását (Wikipedia).](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing)

  Ha engedélyezni szeretné a hozzáférést az `*` összes eredethez, adja meg egyetlen elemként az **allowedOrigins** tömbben. *Ez nem ajánlott éles keresési szolgáltatások,* de gyakran hasznos a fejlesztés és a hibakeresés.

+ **maxAgeInSeconds** (nem kötelező): A böngészők ezt az értéket használják a CORS elővizsgálati válaszok gyorsítótárazási időtartamának meghatározásához (másodpercben). Ennek nem negatív egész számnak kell lennie. Minél nagyobb ez az érték, annál jobb lesz a teljesítmény, de minél tovább tart a CORS-házirend módosításai érvénybe léptetése. Ha nincs beállítva, a rendszer 5 perces alapértelmezett időtartamot használ.

## <a name="encryption-key"></a>Titkosítási kulcs

Bár az összes Azure Cognitive Search-index alapértelmezés szerint a Microsoft által felügyelt kulcsok használatával van titkosítva, az indexek beállíthatók úgy, hogy a Key **Vaultban ügyfél által felügyelt kulcsokkal titkosíthatók** legyenek. További információ: [Titkosítási kulcsok kezelése az Azure Cognitive Search szolgáltatásban.](search-security-manage-encryption-keys.md)

## <a name="next-steps"></a>További lépések

Az index összetételének megértésével folytathatja a portálon az első index létrehozásához.

> [!div class="nextstepaction"]
> [Index hozzáadása (portál)](search-create-index-portal.md)
