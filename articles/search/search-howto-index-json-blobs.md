---
title: Keresés jsonblobok felett
titleSuffix: Azure Cognitive Search
description: Az Azure-beli JSON-blobok feltérképezése szöveges tartalomhoz az Azure Cognitive Search Blob indexelő használatával. Az indexelők automatizálják az adatok betöltését a kiválasztott adatforrások, például az Azure Blob storage számára.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 37fc78971124240077a59d4ad99aa06cc408dbae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74533976"
---
# <a name="how-to-index-json-blobs-using-a-blob-indexer-in-azure-cognitive-search"></a>JSON-blobok indexelése az Azure Cognitive Search szolgáltatásban

Ez a cikk bemutatja, hogyan konfigurálhat egy Azure Cognitive Search blob [indexelő](search-indexer-overview.md) strukturált tartalom kinyerése JSON-dokumentumok az Azure Blob storage-ban, és az Azure Cognitive Search kereshetővé teheti. Ez a munkafolyamat létrehoz egy Azure Cognitive Search indexet, és betölti azt a JSON-blobokból kinyert meglévő szöveggel. 

A [portál,](#json-indexer-portal)a [REST API-k](#json-indexer-rest)vagy a [.NET SDK](#json-indexer-dotnet) segítségével indexelheti a JSON-tartalmat. Minden megközelítés közös, hogy a JSON-dokumentumok egy Azure Storage-fiók blobtárolójában találhatók. A JSON-dokumentumok más, nem Azure-beli platformokról történő lenyomásával kapcsolatos útmutatásért tekintse meg [az Adatok importálása az Azure Cognitive Search alkalmazásban.](search-what-is-data-import.md)

JSON-blobok az Azure Blob storage általában vagy egy JSON-dokumentum (elemzési `json`mód) vagy json-entitások gyűjteménye. Gyűjtemények esetében a blob egy **tömbjól** formázott JSON-elemek (elemzési `jsonArray`mód). A blobok több különálló JSON-entitásból is állhatnak, amelyeket egy `jsonLines`új vonal választ el egymástól (az elemzési mód). A kérelemben szereplő **elemzési mód** paraméter határozza meg a kimeneti struktúrákat.

> [!NOTE]
> Több keresési dokumentum egyetlen blobból történő indexeléséről az [Egy-a-többhöz indexelés című](search-howto-index-one-to-many-blobs.md)témakörben talál további információt.

<a name="json-indexer-portal"></a>

## <a name="use-the-portal"></a>A portál használata

A JSON-dokumentumok indexelésének legegyszerűbb módja egy varázsló használata az [Azure Portalon.](https://portal.azure.com/) A metaadatok elemzésével az Azure blob tárolóban az [**Adatok importálása**](search-import-data-portal.md) varázsló hozhat létre egy alapértelmezett index, a forrásmezők a cél index mezők, és töltse be az indexet egyetlen műveletben. A forrásadatok méretétől és összetettségétől függően percek alatt teljes szöveges keresési indexet kaphat.

Azt javasoljuk, hogy használja ugyanazt a régiót vagy helyet az Azure Cognitive Search és az Azure Storage számára az alacsonyabb késés és a sávszélesség-díjak elkerülése érdekében.

### <a name="1---prepare-source-data"></a>1 - Forrásadatok előkészítése

[Jelentkezzen be az Azure Portalon,](https://portal.azure.com/) és [hozzon létre egy Blob-tárolót](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) az adatok tárolására. A nyilvános hozzáférési szint bármely érvényes értékére beállítható.

Az **Adatok importálása** varázslóban szüksége lesz a tárfiók nevére, a tároló nevére és egy hozzáférési kulcsra.

### <a name="2---start-import-data-wizard"></a>2 – Adatok importálása varázsló indítása

A keresési szolgáltatás Áttekintés lapján a varázslót a parancssávról [indíthatja el.](search-import-data-portal.md)

   ![Adatok importálása parancs a portálon](./media/search-import-data-portal/import-data-cmd2.png "Az Adatok importálása varázsló indítása")

### <a name="3---set-the-data-source"></a>3 - Az adatforrás beállítása

Az **adatforráslapon** a forrásnak **az Azure Blob Storage-nak**kell lennie, a következő specifikációkkal:

+ **A kibontandó adatoknak** *tartalomnak és metaadatoknak*kell lenniük. Ha ezt a beállítást választja, a varázsló indexsémát képez, és leképezi az importáláshoz tartozó mezőket.
   
+ **Az elemzési módot** *JSON , JSON* *tömb* vagy *JSON-vonalakra*kell állítani. 

  *A JSON* minden blobot egyetlen keresési dokumentumként fogalmaz meg, és a keresési eredmények között független elemként jelenik meg. 

  *A JSON-tömb* olyan blobokesetében, amelyek jól formázott JSON-adatokat tartalmaznak - a jól formázott JSON objektumok tömbjének felel meg, vagy olyan tulajdonsággal rendelkezik, amely objektumok tömbje, és azt szeretné, hogy minden elem önálló, független keresési dokumentumként legyen megfogalmazva. Ha a blobok összetettek, és nem választja *a JSON-tömböt,* a teljes blob egyetlen dokumentumként kerül betöltésre.

  *A JSON-vonalak* több JSON-entitásból álló blobok, amelyeket egy új vonal választ el egymástól, ahol azt szeretné, hogy minden entitás önálló független keresési dokumentumként legyen csuklós. Ha a blobok összetettek, és nem választja *a JSON-vonalak* elemzési módját, akkor a teljes blob egyetlen dokumentumként kerül betöltésre.
   
+ **A tárolótárolónak** meg kell adnia a tárfiókot és a tárolót, vagy egy kapcsolati karakterláncot, amely feloldódik a tárolóhoz. A Blob-szolgáltatásportál lapon kaphat kapcsolati karakterláncokat.

   ![Blob adatforrás-definíciója](media/search-howto-index-json/import-wizard-json-data-source.png)

### <a name="4---skip-the-enrich-content-page-in-the-wizard"></a>4 - A varázsló "Tartalom gazdagítása" oldalának kihagyása

Kognitív képességek hozzáadása (vagy gazdagítás) hozzáadása nem importálási követelmény. Ha csak egy adott szükség van [a ai-bővítés hozzáadása](cognitive-search-concept-intro.md) az indexelési folyamathoz, akkor hagyja ki ezt a lépést.

A lépés kihagyásához kattintson az oldal alján található kék gombokra a "Tovább" és a "Kihagyás" területen.

### <a name="5---set-index-attributes"></a>5 - Indexattribútumok beállítása

A **Tárgymutató** lapon meg kell jelennie az adattípusú mezők listájának és az indexattribútumok beállításához tartozó jelölőnégyzetek sorának. A varázsló metaadatok alapján és a forrásadatok mintavételezésével létrehozhat mezőket. 

Az attribútumoszlop tetején található jelölőnégyzetre kattintva tömegesen kiválaszthatja az attribútumokat. Válassza **a Beolvasható** és **a Kereshető** lehetőséget minden olyan mezőhöz, amelyet vissza kell küldeni egy ügyfélalkalmazásba, és teljes szöveges keresési feldolgozásnak kell alávetni. Észre fogja venni, hogy az egész számok nem teljes szövegvagy fuzzy kereshető (számok kiértékelése szó szerint, és gyakran hasznos a szűrők).

További információkért tekintse át az [indexattribútumok](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib) és [a nyelvi elemzők](https://docs.microsoft.com/rest/api/searchservice/language-support) leírását. 

Szánjon egy kis időbe, hogy áttekintse a beállításokat. A varázsló futtatása után fizikai adatstruktúrák jönnek létre, és ezeket a mezőket nem szerkesztheti az összes objektum eldobása és újbóli létrehozása nélkül.

   ![Blob indexdefiníciója](media/search-howto-index-json/import-wizard-json-index.png)

### <a name="6---create-indexer"></a>6 - Indexelő létrehozása

Teljesen megadva, a varázsló három különböző objektumot hoz létre a keresési szolgáltatásban. Az adatforrás-objektum és az indexobjektum elnevezett erőforrásként kerül mentésre az Azure Cognitive Search szolgáltatásban. Az utolsó lépés egy indexelő objektumot hoz létre. Az indexelő elnevezése lehetővé teszi, hogy önálló erőforrásként létezzen, amelyet az ugyanabban a varázslósorozatban létrehozott index- és adatforrásobjektumtól függetlenül ütemezhet és kezelhet.

Ha nem ismeri az indexelők, *indexelő* egy erőforrás az Azure Cognitive Search, amely feltérképezi a kereshető tartalom külső adatforrása. Az Adatok **importálása** varázsló kimenete egy indexelő, amely feltérképezi a JSON adatforrást, kinyeri a kereshető tartalmat, és importálja azt egy indexbe az Azure Cognitive Search.The output of the Import data wizard is an indexer that crawls your JSON data source, extracts searchable content, and imports it into an index on Azure Cognitive Search.

   ![Blob indexelő definíciója](media/search-howto-index-json/import-wizard-json-indexer.png)

Kattintson **az OK** gombra a varázsló futtatásához és az összes objektum létrehozásához. Az indexelés azonnal megkezdődik.

A portállapokon figyelheti az adatimportálást. A végrehajtási értesítések jelzik az indexelési állapotot és a feltöltött dokumentumok nagy számára. 

Ha az indexelés befejeződött, a [Kereséskezelővel](search-explorer.md) lekérdezheti az indexet.

> [!NOTE]
> Ha nem látja a várt adatokat, előfordulhat, hogy több mezőhöz további attribútumokat kell beállítania. Törölje az imént létrehozott indexet és indexelőt, majd lépjen újra a varázslóba, és módosítsa az 5. 

<a name="json-indexer-rest"></a>

## <a name="use-rest-apis"></a>REST API-k használata

A REST API segítségével indexelheti a JSON-blobokat, az Azure Cognitive Search összes indexelőjének közös háromrészes munkafolyamatát követve: hozzon létre egy adatforrást, hozzon létre egy indexet, hozzon létre egy indexelőt. A blobstorage-ból történő adatkinyerés akkor történik, amikor elküldi az Indexelő létrehozása kérelmet. Miután ez a kérés befejeződött, lesz egy lekérdezhető index. 

A szakasz végén megtekintheti a [REST példakódot,](#rest-example) amely bemutatja mindhárom objektum létrehozását. Ez a szakasz a [JSON-elemzési módokra](#parsing-modes), [az egyblobokra](#parsing-single-blobs), [a JSON-tömbökre](#parsing-arrays)és a [beágyazott tömbökre](#nested-json-arrays)vonatkozó részleteket is tartalmazza.

Kódalapú JSON indexeléshez használja a [Postman](search-get-started-postman.md) és a REST API-t a következő objektumok létrehozásához:

+ [Index](https://docs.microsoft.com/rest/api/searchservice/create-index)
+ [Adatforrás](https://docs.microsoft.com/rest/api/searchservice/create-data-source)
+ [Indexelő](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

A műveletek sorrendjéhez ilyen sorrendben kell létrehozni és meghívni az objektumokat. A portálmunkafolyamattal ellentétben a kódmegközelítéshez egy rendelkezésre álló indexre van szükség az **Indexelő létrehozása** kérésen keresztül küldött JSON-dokumentumok elfogadásához.

JSON-blobok az Azure Blob storage általában vagy egyetlen JSON-dokumentum vagy egy JSON "tömb". A blob indexelő az Azure Cognitive Search elemezheti bármelyik konstrukció, attól függően, hogyan állítja be az **elemzési mód** paraméter a kérelemben.

| JSON-dokumentum | elemzésmód | Leírás | Rendelkezésre állás |
|--------------|-------------|--------------|--------------|
| Blobonként egy | `json` | A JSON-blobokat egyetlen szövegrészeként elemzi. Minden JSON-blob egyetlen Azure Cognitive Search-dokumentummá válik. | Általánosan elérhető a [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) API és a [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK. |
| Több blobonként | `jsonArray` | JSON-tömböt elemez a blobban, ahol a tömb minden eleme külön Azure Cognitive Search-dokumentummá válik.  | Általánosan elérhető a [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) API és a [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK. |
| Több blobonként | `jsonLines` | Egy blobot elemez, amely több JSON-entitást (egy "tömböt") tartalmaz egy új vonalval elválasztva, ahol minden entitás különálló Azure Cognitive Search-dokumentummá válik. | Általánosan elérhető a [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) API és a [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK. |

### <a name="1---assemble-inputs-for-the-request"></a>1 - A kérelem bemenetének összeállítása

Minden kérelemhez meg kell adnia a szolgáltatás nevét és a rendszergazdai kulcsot az Azure Cognitive Search (a POST fejlécben), valamint a tárfiók nevét és kulcsblob storage. [A Postman](search-get-started-postman.md) segítségével HTTP-kérelmeket küldhet az Azure Cognitive Search szolgáltatásba.

Másolja a vágólapra a következő négy értéket, hogy beilleszthesse őket egy kérésbe:

+ Az Azure Cognitive Search szolgáltatás neve
+ Azure Cognitive Search felügyeleti kulcs
+ Azure Storage-tárfiók neve
+ Azure storage-fiók kulcsa

Ezeket az értékeket a portálon találja:

1. Az Azure Cognitive Search portállapjain másolja a keresési szolgáltatás URL-címét az Áttekintés lapról.

2. A bal oldali navigációs ablakban kattintson a **Kulcsok** elemre, majd másolja az elsődleges vagy másodlagos kulcsot (egyenértékűek).

3. Váltson a tárfiók portállapjaira. A bal oldali navigációs ablak **Beállítások**területén kattintson a **Hozzáférési kulcsok gombra.** Ez a lap a fiók nevét és a kulcsot is tartalmazza. Másolja a tárfiók nevét és a jegyzettömb egyik kulcsát.

### <a name="2---create-a-data-source"></a>2 - Adatforrás létrehozása

Ez a lépés az indexelő által használt adatforrás-kapcsolati adatokat tartalmazza. Az adatforrás egy elnevezett objektum az Azure Cognitive Search, amely megőrzi a kapcsolat adatait. Az adatforrás típusa `azureblob`, határozza meg, hogy az indexelő milyen adatkinyerési viselkedést hív meg. 

A szolgáltatásnév, a rendszergazdai kulcs, a tárfiók és a fiókkulcs helyőrzői érvényes értékeket helyettesítse.

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   

### <a name="3---create-a-target-search-index"></a>3 - Célkeresési index létrehozása 

Az indexelők indexsémával vannak párosítva. Ha az API-t (nem a portált használja), készítsen elő egy indexet előre, hogy megadhatja azt az indexelő műveletben.

Az index kereshető tartalmat tárol az Azure Cognitive Search szolgáltatásban. Index létrehozásához adjon meg egy sémát, amely meghatározza a dokumentum, attribútumok és a keresési élményt formáló egyéb konstrukciók mezőit. Ha olyan indexet hoz létre, amelynek mezőnevei és adattípusai megegyeznek a forrással, az indexelő megfelel a forrás- és célmezőknek, így a mezők kifejezett leképezése megteremti a munkát.

A következő példa egy [Index létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-index) kérelmet mutat be. Az index nek lesz `content` egy kereshető mezője a blobokból kinyert szöveg tárolására:   

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }


### <a name="4---configure-and-run-the-indexer"></a>4 - Az indexelő konfigurálása és futtatása

Az indexhez és az adatforráshoz, és az indexelő is egy elnevezett objektum, amelyet egy Azure Cognitive Search szolgáltatáshoz hoz létre és használ fel újra. Az indexelő létrehozására vonatkozó teljesen meghatározott kérelem a következőképpen nézhet ki:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } }
    }

Indexelő konfiguráció a kérelem törzsében van. Egy adatforrást és egy üres célindexet igényel, amely már létezik az Azure Cognitive Search szolgáltatásban. 

Az ütemezés és a paraméterek megadása nem kötelező. Ha kihagyja őket, az indexelő azonnal `json` fut, elemzési módként használva.

Ez az indexelő nem tartalmaz mezőleképezéseket. Az indexelő definícióján belül kihagyhatja a **mezőleképezéseket,** ha a forrás JSON-dokumentum tulajdonságai megegyeznek a célkeresési index mezőivel. 


### <a name="rest-example"></a>REST példa

Ez a szakasz az objektumok létrehozásához használt összes kérelem újrabedugni. Az összetevőkről a cikk előző szakaszaiban olvashat.

### <a name="data-source-request"></a>Adatforrás-kérelem

Minden indexelőnek olyan adatforrás-objektumra van szüksége, amely kapcsolati adatokat szolgáltat a meglévő adatokhoz. 

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }  


### <a name="index-request"></a>Indexkérelem

Minden indexelők igényel célindex, amely fogadja az adatokat. A kérelem törzse határozza meg az indexsémát, amely mezőkből áll, és amely támogatja a kívánt viselkedést egy kereshető indexben. Ennek az indexnek üresnek kell lennie az indexelő futtatásakor. 

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }


### <a name="indexer-request"></a>Indexelő-kérelem

Ez a kérés egy teljesen megadott indexelőt jelenít meg. Mezőleképezéseket tartalmaz, amelyek et a korábbi példákban kihagytak. Emlékezzünk vissza, hogy az "ütemezés", a "paraméterek" és a "fieldMappings" nem kötelező, amíg van egy rendelkezésre álló alapértelmezett. Az "ütemezés" elhagyása miatt az indexelő azonnal futni fog. A "parsingMode" elhagyása azt eredményezi, hogy az index a "json" alapértelmezett értéket használja.

Az indexelő létrehozása az Azure Cognitive Search elindítja az adatok importálását. Ez azonnal fut, és azt követően a menetrend, ha már biztosított egyet.

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } },
      "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
        ]
    }


<a name="json-indexer-dotnet"></a>

## <a name="use-net-sdk"></a>A .NET SDK használata

A .NET SDK teljes paritást a REST API-val. Azt javasoljuk, hogy tekintse át az előző REST API szakaszt a fogalmak, a munkafolyamat és a követelmények megismeréséhez. Ezután a következő .NET API referenciadokumentációban egy JSON indexelő felügyelt kódban való implementálódásához hivatkozhat.

+ [microsoft.azure.search.models.datasource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [microsoft.azure.search.models.datasourcetype](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [microsoft.azure.search.models.index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [microsoft.azure.search.models.indexer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

<a name="parsing-modes"></a>

## <a name="parsing-modes"></a>Elemzési módok

A JSON-blobok több űrlapot is felvehetnek. A JSON indexelő **parsingMode** paramétere határozza meg, hogyan elemeza a JSON blobtartalma tegyenlése és felépítése az Azure Cognitive Search indexében:

| elemzésmód | Leírás |
|-------------|-------------|
| `json`  | Minden blob ot egyetlen dokumentumként indexelhet. Ez az alapértelmezett beállítás. |
| `jsonArray` | Válassza ezt a módot, ha a blobok JSON-tömbökből állnak, és a tömb minden elemére szüksége van ahhoz, hogy az Azure Cognitive Search ben külön dokumentumká váljon. |
|`jsonLines` | Válassza ezt a módot, ha a blobok több JSON-entitásból állnak, amelyeket egy új sor választ el egymástól, és minden entitásnak külön dokumentummá kell válnia az Azure Cognitive Search szolgáltatásban. |

A dokumentum egyetlen elemként is felfogható a keresési eredmények között. Ha azt szeretné, hogy a tömb minden eleme független elemként `jsonArray` jelenjen meg a keresési eredmények között, használja a vagy `jsonLines` a lehetőséget.

Az indexelő definícióján belül lehetőség szerint [mezőleképezések](search-indexer-field-mappings.md) segítségével választhatja ki, hogy a forrás JSON-dokumentum mely tulajdonságai használhatók a célkeresési index feltöltéséhez. Elemzési `jsonArray` módban, ha a tömb alacsonyabb szintű tulajdonságként létezik, beállíthat egy dokumentumgyökeret, amely jelzi, hogy a tömb hol van elhelyezve a blobon belül.

> [!IMPORTANT]
> A használata `json` `jsonArray` esetén `jsonLines` , vagy elemzési módban az Azure Cognitive Search feltételezi, hogy az adatforrás ban lévő összes blob jsont tartalmaz. Ha a JSON és a nem JSON blobok keverékét kell támogatnia ugyanabban az adatforrásban, tudassa velünk [a UserVoice webhelyen.](https://feedback.azure.com/forums/263029-azure-search)


<a name="parsing-single-blobs"></a>

## <a name="parse-single-json-blobs"></a>Elemzés egyetlen JSON blobok

Alapértelmezés szerint [az Azure Cognitive Search blob indexelő](search-howto-indexing-azure-blob-storage.md) jesrizálja a JSON-blobokat egyetlen szövegtömbként. Gyakran meg szeretné őrizni a JSON-dokumentumok szerkezetét. Tegyük fel például, hogy a következő JSON-dokumentum mal van eltivelaz Azure Blob storage-ban:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13",
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

A blob indexelő elemzi a JSON-dokumentumot egyetlen Azure Cognitive Search-dokumentumba. Az indexelő betölt egy indexet a forrásból származó "text", "datePublished" és "tags" egyeztetésével az azonos nevű és beírt célindexmezőkkel.

Mint megjegyezte, a mező leképezések nem szükséges. Adott index "szöveg", "datePublished, és a "címkék" mezőket, a blob indexelő következtetni a helyes leképezés nélkül mező leképezés jelen van a kérelemben.

<a name="parsing-arrays"></a>

## <a name="parse-json-arrays"></a>Elemzés IJSON tömbök

Másik lehetőségként használhatja a JSON tömb beállítást. Ez a beállítás akkor hasznos, ha a blobok *jól formázott JSON-objektumok tömbjét*tartalmazzák, és azt szeretné, hogy minden elem külön Azure Cognitive Search-dokumentumká váljon. A következő JSON-blob ot tekintve például három különálló dokumentummal feltöltheti az Azure Cognitive Search indexét, amelyek mindegyike "id" és "text" mezőkkel van eljárva.  

    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]

JSON-tömb esetén az indexelő definíciójának a következő példához hasonlóan kell kinéznie. Figyelje meg, hogy az elemzési mód paraméter megadja az `jsonArray` elemzőt. A jsonblobok indexelésének két tömbspecifikus követelménya a megfelelő elemző megadása és a megfelelő adatbevitel.

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
    }

Ismét figyelje meg, hogy a mezőleképezések elhagyhatók. Feltételezve, hogy egy index azonos nevű "id" és a "szöveg" mezőket, a blob indexelő lehet következtetni a helyes leképezés nélkül explicit mező leképezési lista.

<a name="nested-json-arrays"></a>

## <a name="parse-nested-arrays"></a>Beágyazott tömbök elemzése
Egymásba ágyazott elemekkel rendelkező JSON-tömbök esetében megadhat a `documentRoot` értéket egy többszintű struktúra jelzésére. Ha például a blobok így néznek ki:

    {
        "level1" : {
            "level2" : [
                { "id" : "1", "text" : "Use the documentRoot property" },
                { "id" : "2", "text" : "to pluck the array you want to index" },
                { "id" : "3", "text" : "even if it's nested inside the document" }  
            ]
        }
    }

Ezzel a konfigurációval indexeli `level2` a tulajdonságban található tömböt:

    {
        "name" : "my-json-array-indexer",
        ... other indexer properties
        "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
    }

## <a name="parse-blobs-separated-by-newlines"></a>Új vonalakkal elválasztott elemzési blobok

Ha a blob több JSON-entitást tartalmaz egy új vonallal elválasztva, és azt szeretné, hogy minden elem külön Azure Cognitive Search-dokumentumká váljon, választhatja a JSON-vonalak beállítást. Például a következő blob (ahol három különböző JSON-entitások), feltöltheti az Azure Cognitive Search index három külön dokumentumot, mindegyik "id" és a "text" mezőket.

    { "id" : "1", "text" : "example 1" }
    { "id" : "2", "text" : "example 2" }
    { "id" : "3", "text" : "example 3" }

JSON-sorok esetén az indexelő definíciójának a következő példához hasonlóan kell kinéznie. Figyelje meg, hogy az elemzési mód paraméter megadja az `jsonLines` elemzőt. 

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonLines" } }
    }

Ismét figyelje meg, hogy a mezőleképezések elhagyhatók, hasonlóan az `jsonArray` elemzési módhoz.

## <a name="add-field-mappings"></a>Mezőleképezések hozzáadása

Ha a forrás- és célmezők nincsenek tökéletesen egyvonalban, a kérelemtörzsben definiálhat egy mezőleképezési szakaszt explicit mező-mező társításokhoz.

Jelenleg az Azure Cognitive Search nem indexelheti tetszőleges JSON-dokumentumokat közvetlenül, mert csak primitív adattípusokat, karakterlánc-tömböket és GeoJSON-pontokat támogat. A **mezőleképezések** segítségével azonban kiválaszthatja a JSON-dokumentum egyes részeit, és "felemelheti" azokat a keresési dokumentum legfelső szintű mezőibe. A mezőleképezések alapjairól az [Azure Cognitive Search indexelői mezőleképezésecímű témakörben](search-indexer-field-mappings.md)olvashat.

A JSON-dokumentum rakoncátora:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Tegyük fel, hogy keresési `text` index `Edm.String` `date` et `Edm.DateTimeOffset`a `tags` következő `Collection(Edm.String)`mezőkkel: típus , típus és típus . Figyelje meg a "datePublished" közötti `date` eltérést az index forrásában és mezőjében. A JSON-t a kívánt alakzatba való leképezéshez használja a következő mezőleképezések segítségével:

    "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
      ]

A leképezésekben szereplő forrásmezőnevek a [JSON-mutató](https://tools.ietf.org/html/rfc6901) jelölésével vannak megadva. A JSON-dokumentum gyökerére való hivatkozáshoz egy perjellel kezdi, majd a kívánt tulajdonságot (tetszőleges beágyazási szinten) választja ki a perjel-elválasztott elérési út használatával.

Az egyes tömbelemekre nulla alapú index használatával is hivatkozhat. Ha például a fenti példából szeretné kiválasztani a "címkék" tömb első elemét, használjon a következőhöz hasonló mezőleképezést:

    { "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }

> [!NOTE]
> Ha egy mezőleképezési útvonal forrásmezőneve olyan tulajdonságra hivatkozik, amely nem létezik a JSON-ban, a leképezés hiba nélkül kimarad. Ez azért történik, hogy más sémával (ez egy gyakori használati eset) rendelkező dokumentumokat támogasson. Mivel nincs érvényesítés, ügyelnie kell arra, hogy elkerülje az elírásokat a mezőleképezési specifikációban.
>
>

## <a name="see-also"></a>Lásd még

+ [Indexelők az Azure Cognitive Searchben](search-indexer-overview.md)
+ [Az Azure Blob Storage indexelése az Azure Cognitive Search segítségével](search-howto-index-json-blobs.md)
+ [CSV-blobok indexelése az Azure Cognitive Search blob indexelővel](search-howto-index-csv-blobs.md)
+ [Oktatóanyag: Félig strukturált adatok keresése az Azure Blob storage-ból](search-semi-structured-data.md)
