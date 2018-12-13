---
title: Az Azure Blob storage tartalmának az Azure Search – a teljes szöveges keresés
description: Tudnivalók az Azure Blob Storage indexelése és szöveg kinyerése és az Azure Search a dokumentumokat.
ms.date: 10/17/2018
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: c73a802cd67c9ecb94482cfcd6aac51fc8bbc19e
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53317474"
---
# <a name="indexing-documents-in-azure-blob-storage-with-azure-search"></a>Dokumentumok indexelése az Azure Blob Storage, az Azure Search szolgáltatással
Ez a cikk bemutatja, hogyan használható az Azure Search index dokumentumok (például PDF-, Microsoft Office-dokumentumok, és számos egyéb gyakori formátum) az Azure Blob storage-ban tárolja. Első lépésként beállítása és konfigurálása a blob indexelőjével alapjait ismerteti. Ezután egy mélyebb feltárása viselkedéseket, kínál, és esetekben valószínűleg találkozik.

## <a name="supported-document-formats"></a>A dokumentum formátumokat támogatja
A blob indexelőjével szöveget vonhat ki a következő dokumentum formátumok:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="setting-up-blob-indexing"></a>Blob-indexelés beállítása
Beállíthat egy Azure Blob Storage indexelő használatával:

* [Azure Portal](https://ms.portal.azure.com)
* Az Azure Search [REST API-val](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)
* Azure Search [.NET SDK](https://aka.ms/search-sdk)

> [!NOTE]
> Egyes funkciók (például a mező-leképezések) még nem állnak rendelkezésre a portálon, és programozott módon kell használni.
>
>

Itt bemutatjuk a folyamatot a REST API használatával.

### <a name="step-1-create-a-data-source"></a>1. lépés: Adatforrás létrehozása
Egy adatforrás indexelése, az adatokat, és a házirendek hatékony azonosításához az adatok (az új, módosított vagy törölt sor) módosítása eléréséhez szükséges hitelesítő adatok megadása Egy adatforrás több indexelők az ugyanazt a keresési szolgáltatást használhatják.

A blob-indexelés, az adatforrást a következő szükséges tulajdonságokat kell rendelkeznie:

* **név** az adatforrásban a keresési szolgáltatás belül egyedi neve.
* **típus** kell `azureblob`.
* **hitelesítő adatok** biztosít, mint a tárfiók kapcsolati sztringje a `credentials.connectionString` paraméter. Lásd: [hitelesítő adatok megadása](#Credentials) alábbi részleteket.
* **tároló** megadja egy tárolót a storage-fiókban. Alapértelmezés szerint a tárolóban lévő összes blobot is lekérhető. Ha csak egy adott virtuális könyvtárat a blobok index, megadhatja a címtárhoz nem kötelező **lekérdezés** paraméter.

Adatforrás létrehozása:

    POST https://[service name].search.windows.net/datasources?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
    }   

Adatforrás létrehozása API további információkért lásd: [adatforrás létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-data-source).

<a name="Credentials"></a>
#### <a name="how-to-specify-credentials"></a>Hitelesítő adatok megadása ####

A hitelesítő adatokat megadhatja a blob-tároló, a következő módszerek egyikével:

- **Teljes hozzáférés tárfiók kapcsolati sztringje**: `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>`. Megtekintheti a kapcsolati karakterláncot az Azure Portalról a storage-fiók paneljére lépve > Beállítások > kulcsok (a klasszikus tárfiókokkal) vagy a beállítások > hozzáférési kulcsok (a storage-fiókok Azure Resource Manager).
- **Storage-fiók közös hozzáférésű jogosultságkód** (SAS) kapcsolódási karakterlánc: `BlobEndpoint=https://<your account>.blob.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=b&sp=rl` A SAS kell van a listában, és olvasási jogosultságokkal tárolókkal és objektumokkal (ebben az esetben blobok).
-  **Tároló közös hozzáférésű jogosultságkód**: `ContainerSharedAccessUri=https://<your storage account>.blob.core.windows.net/<container name>?sv=2016-05-31&sr=c&sig=<the signature>&se=<the validity end time>&sp=rl` Az SAS a lista kell, és olvasási engedéllyel a tároló.

További információ a megosztott tároló eléréséhez aláírások című témakörben talál [közös hozzáférési aláírások használatával](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
> Ha SAS-hitelesítő adatokat használja, szüksége lesz az adatforrás hitelesítő adatainak frissítése rendszeresen történik, hogy a lejárati idejük megújított jogosultságkódokkal. Ha lejár az SAS-hitelesítő adatokat, az indexelő hasonló hibaüzenettel meghiúsul `Credentials provided in the connection string are invalid or have expired.`.  

### <a name="step-2-create-an-index"></a>2. lépés: Index létrehozása
Az index egy dokumentumot, az attribútumokat, adja meg a mezőket, és egyéb szerkezetek, amelyek formázhatja a keresési élmény.

Hozzon létre egy kereshető indexet a következőképpen `content` tárolására blobok kinyert szöveget mező:   

    POST https://[service name].search.windows.net/indexes?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }

Indexek létrehozásával kapcsolatos további információkért lásd: [Index létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-index)

### <a name="step-3-create-an-indexer"></a>3. lépés: Indexelő létrehozása
Az indexelő adatforráshoz kapcsolódik a cél keresési indexhez, és biztosít az Adatfrissítés automatizálásához ütemezés szerint.

Az index és az adatforrás létrehozása után készen áll az indexelő létrehozása:

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "blob-indexer",
      "dataSourceName" : "blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

Az indexelő kétóránként (ütemezési időköz beállítása "PT2H") fog futni. Az indexelők futtatásához a 30 percenként, a "PT30M" időközt beállítani. A legrövidebb támogatott időköz 5 perc. Az ütemezés nem kötelező, ha nincs megadva, az indexelő futása csak egyszer, amikor létrejön. Azonban bármikor egy indexelő igény szerinti is futtathatja.   

Az indexelő API létrehozása a további részletekért tekintse meg [indexelő létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

## <a name="how-azure-search-indexes-blobs"></a>Hogyan indexeli az Azure Search a blobok

Attól függően, a [az indexelő konfigurációjának](#PartsOfBlobToIndex), a blob indexelőjével indexelésére használhatja, csak tárolási metaadatok (hasznos, ha csak a metaadatokat és a blobok a tartalom indexelése nem szükséges), tároló és a tartalom metaadatainak, vagy mindkét metaadatok és szöveges tartalma. Alapértelmezés szerint az indexelő kinyeri a metaadatokat és a tartalom.

> [!NOTE]
> Alapértelmezés szerint strukturálatlan tartalom használatával például JSON vagy CSV-blobok indexelésének, egy olyan adattömb szöveg. Ha azt szeretné, JSON-t és a CSV-blobok indexelése strukturált módon, [indexelő JSON-blobok](search-howto-index-json-blobs.md) és [indexelő CSV-blobok](search-howto-index-csv-blobs.md) előzetes verziójú funkciók.
>
> Egy összetett vagy beágyazott dokumentum (például a ZIP-archívumot vagy egy Word-dokumentumot a mellékleteket tartalmazó beágyazott Outlookos e-mail cím) is indexelt egyetlen dokumentumként.

* A dokumentum a szöveges tartalom kinyert nevű karakterlánc típusú `content`.

> [!NOTE]
> Az Azure Search korlátozza mennyi szöveg bontja ki a tarifacsomagot függően: 32000 karakterek az ingyenes szinten, 64 000 alapszintű és Standard, a Standard S2 és a Standard S3 szinten 4 millió. Figyelmeztetés az indexelő állapotválasz csonkolt dokumentumok szerepel.  

* A blob megtalálható a felhasználó által megadott metaadat-tulajdonságot az esetleges vonjuk pontosan.
* Standard szintű blob metaadat-tulajdonságot bontja be a következő mezőket:

  * **metaadatok\_tárolási\_neve** (Edm.String) – a blob fájlnevét. Például ha egy blob /my-container/my-folder/subfolder/resume.pdf, ez a mező értéke `resume.pdf`.
  * **metaadatok\_tárolási\_elérési útja** (Edm.String) – a teljes URI-ját a blob, beleértve a storage-fiókot. Például: `https://myaccount.blob.core.windows.net/my-container/my-folder/subfolder/resume.pdf`
  * **metaadatok\_tárolási\_tartalom\_típus** (Edm.String) – a kódot, amellyel feltöltése a blob által megadott tartalomtípus. Például: `application/octet-stream`.
  * **metaadatok\_tárolási\_utolsó\_módosított** (Edm.DateTimeOffset) - utolsó módosítás a BLOB időbélyegző. Az Azure Search az időbélyeg módosított blobok elkerülése érdekében a kezdeti indexelése követően újraindexelés mindent azonosítására használ.
  * **metaadatok\_tárolási\_mérete** (Edm.Int64) – a blob mérete bájtban.
  * **metaadatok\_tárolási\_tartalom\_md5** (Edm.String) – a blob tartalmát, ha elérhető MD5-kivonat.
* Minden egyes dokumentum formátumban jellemző metaadat-tulajdonságot ki kell olvasni a mezőkben szereplő [Itt](#ContentSpecificMetadata).

Nem kell mezők az összes fenti tulajdonságait a search-index a – csak rögzítése szüksége lesz az alkalmazás tulajdonságait.

> [!NOTE]
> Gyakran előfordul a mezők nevét a meglévő index a dokumentum kibontása során létrehozott mezőnevek eltérő lesz. Használhat **mezőleképezéseivel** leképezni a mezők nevét a keresési index Azure Search által biztosított tulajdonság nevét. Látni fogja a mező-leképezések használja az alábbi példát.
>
>

<a name="DocumentKeys"></a>
### <a name="defining-document-keys-and-field-mappings"></a>A dokumentum kulcsok és a mező-leképezések
Az Azure Search szolgáltatásban a dokumentumkulcsot egyedileg azonosítja az egy dokumentumot. Minden keresési index Edm.String típusú pontosan egy kulcsmező kell rendelkeznie. A kulcs mező kitöltése kötelező, egyes dokumentumok, amelyek az index (azt a ténylegesen az egyetlen kötelezően kitöltendő mező) ad hozzá.  

Alaposan fontolja meg melyik kinyert mező leképezhető kulcsmező az index. A jelöltek a következők:

* **metaadatok\_tárolási\_neve** – Ez lehet egy kényelmes jelölt, de vegye figyelembe, hogy (1) a nevek nem feltétlenül egyedi, ugyanazzal a névvel található különböző mappákban, (2) a név blobok rendelkezik, előfordulhat, hogy nincs karaktereket tartalmazhat. Érvénytelen a dokumentum kulcsok, például kötőjelek szerepelhetnek. Kezelhető, érvénytelen karakterek használatával a `base64Encode` [leképezési függvény mező](search-indexer-field-mappings.md#base64EncodeFunction) – Ha így tesz, a dokumentum kulcsok kódolása, amikor például a keresési API-t ad át őket meghívja a vágólapra. (Például a .NET-ben is használhatja a [UrlTokenEncode metódus](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) erre a célra).
* **metaadatok\_tárolási\_elérési** – a teljes elérési útja alapján biztosítja az egyedi-e, de az elérési út mindenképp tartalmaz `/` karaktereket [egy dokumentum kulcs érvénytelen](https://docs.microsoft.com/rest/api/searchservice/naming-rules).  A fenti, a kódolási a kulcsok használatával lehetősége van a `base64Encode` [függvény](search-indexer-field-mappings.md#base64EncodeFunction).
* A fenti lehetőségek egyike sem működik, ha a blobokat is hozzáadhat egy egyéni metaadat-tulajdonságot. Ezt a beállítást, azonban szükség van a blob feltöltési folyamat összes BLOB hozzáadása a metaadat-tulajdonságot. Mivel a kulcsot kötelező tulajdonság, az összes tulajdonság nem rendelkező blobok indexelése sikertelen lesz.

> [!IMPORTANT]
> Ha nincs explicit leképezés a kulcsmező az indexben, az Azure Search automatikusan használja `metadata_storage_path` a kulcs és a base-64 kódolja a kulcs értékeit (a második lehetőség a fenti).
>
>

Ebben a példában most válassza ki a `metadata_storage_name` mezőt dokumentumkulcsként. Azt is feltételezzük az index tartozik, a kulcs mező nevű `key` és a egy mezőben `fileSize` tárolására a dokumentumok méretétől. A kábelezést dolgot fel a igény szerint, adja meg a következő mező-leképezések létrehozásakor vagy frissítésekor az indexelő:

    "fieldMappings" : [
      { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
      { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
    ]

Ahhoz, hogy ez minden egy helyen, a következő hogyan mező-leképezések hozzáadása, és engedélyezze a meglévő indexelő kulcsok base-64 kódolását:

    PUT https://[service name].search.windows.net/indexers/blob-indexer?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      "dataSourceName" : " blob-datasource ",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "fieldMappings" : [
        { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
        { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
      ]
    }

> [!NOTE]
> További információ a mezőmegfeleltetésről, lásd: [Ez a cikk](search-indexer-field-mappings.md).
>
>

<a name="WhichBlobsAreIndexed"></a>
## <a name="controlling-which-blobs-are-indexed"></a>Melyik blobok indexelésének szabályozása
Szabályozhatja, hogy melyik blobok indexelésének, és amelyeket a rendszer kihagyja.

### <a name="index-only-the-blobs-with-specific-file-extensions"></a>Csak a meghatározott fájlnév-kiterjesztésű blobok indexelése
Csak a blobok használatával adja meg a fájlnév-kiterjesztések indexelheti a `indexedFileNameExtensions` indexelő konfigurációs paraméter. Érték (a vezető pont) fájlkiterjesztések vesszővel tagolt listáját tartalmazó karakterlánc. Például, hogy csak az index a. PDF-fájlt, és. Blobok DOCX, tegye a következőt:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
    }

### <a name="exclude-blobs-with-specific-file-extensions"></a>Blobok meghatározott fájlnév-kiterjesztésű kizárása
Meghatározott fájlnév-kiterjesztéseket blobok kizárása az indexelés a `excludedFileNameExtensions` konfigurációs paraméter. Érték (a vezető pont) fájlkiterjesztések vesszővel tagolt listáját tartalmazó karakterlánc. Például, hogy minden BLOB, kivéve az index a. PNG és. JPEG-bővítményeket, tegye a következőt:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
    }

Ha mindkét `indexedFileNameExtensions` és `excludedFileNameExtensions` paraméterek jelen, első megvizsgálja az Azure Search `indexedFileNameExtensions`, majd a `excludedFileNameExtensions`. Ez azt jelenti, hogy ha ugyanolyan fájlkiterjesztéssel megtalálható a mindkét listák, ki lesz zárva az indexelés.

<a name="PartsOfBlobToIndex"></a>
## <a name="controlling-which-parts-of-the-blob-are-indexed"></a>A blob mely részei indexelt szabályozása

Mely részei a blobok indexelésének segítségével szabályozhatja a `dataToExtract` konfigurációs paraméter. Is igénybe vehet a következő értékeket:

* `storageMetadata` -Adja meg, hogy csak a [szokásos blob tulajdonságainak és metaadatainak felhasználó által megadott](../storage/blobs/storage-properties-metadata.md) indexelt.
* `allMetadata` -Adja meg a tárolási metaadatokat és a [a content-type adott metaadatokat](#ContentSpecificMetadata) kinyert tartalom indexelése a blobból.
* `contentAndMetadata` -Adja meg, hogy minden metaadat és a blob kinyert szöveges tartalom indexelve. Ez az alapértelmezett érték.

Ha például csak a tárolási metaadatokat indexelje, használja:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "dataToExtract" : "storageMetadata" } }
    }

### <a name="using-blob-metadata-to-control-how-blobs-are-indexed"></a>Blob metaadatok segítségével szabályozhatja a blobok indexelésének módját

A fenti konfigurációs paraméterek összes BLOB vonatkoznak. Egyes esetekben előfordulhat, hogy szeretne szabályozni hogyan *az egyes blobok* indexelt. Ehhez a következő blob metaadat-tulajdonságait és értékeit hozzáadása:

| Tulajdonság neve | Tulajdonság értéke | Magyarázat |
| --- | --- | --- |
| AzureSearch_Skip |"true" |Arra utasítja a blob indexelőjével teljesen kihagyja a blobot. Sem a metaadatok, sem a tartalom kivonása kísérlet történik. Ez akkor hasznos, ha egy adott blob ismételten meghiúsul, és megszakítja az indexelési folyamat. |
| AzureSearch_SkipContent |"true" |Ez megfelel a `"dataToExtract" : "allMetadata"` beállítása az itt ismertetett [fent](#PartsOfBlobToIndex) hatóköre egy adott blob. |

<a name="DealingWithErrors"></a>
## <a name="dealing-with-errors"></a>Hibák kezelése

Alapértelmezés szerint a blob indexelőjével leállítja a lehető legrövidebb időn belül nem támogatott tartalom-típust (például egy kép) rendelkező tapasztal. Természetesen használhat a `excludedFileNameExtensions` paraméter kihagyása bizonyos tartalomtípusok. Azonban szükség lehet index blobok minden lehetséges tartalomtípusok előzetes ismerete nélkül. Folytatni, amikor a tartalom típusa nem támogatott a rendszer észlelt, állítsa be a `failOnUnsupportedContentType` konfigurációs paraméter `false`:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false } }
    }

Az egyes blobok az Azure Search nem tudja megállapítani a tartalom típusa, vagy nem lehet feldolgozni egy dokumentumot, ellenkező esetben támogatott tartalom típusa. Ez a hiba mód figyelmen kívül, állítsa be a `failOnUnprocessableDocument` konfigurációs paraméter false értékre:

      "parameters" : { "configuration" : { "failOnUnprocessableDocument" : false } }

Az Azure Search korlátozza az indexelt blobok méretének. Ezek a korlátok vannak dokumentálva [az Azure Search szolgáltatási korlátok](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity). Hibák túl nagy méretű blobok alapértelmezés szerint kell kezelni. Azonban akkor is továbbra is a tárolási metaadatokat indexelje a túl nagy méretű blobok Ha `indexStorageMetadataOnlyForOversizedDocuments` konfigurációs paraméter igaz: 

    "parameters" : { "configuration" : { "indexStorageMetadataOnlyForOversizedDocuments" : true } }

Folytathatja az indexelés Ha hiba történik a feldolgozás, a blobok elemzése közben, vagy a dokumentum indexbe való hozzáadása közben bármikor. Egy bizonyos számú hiba figyelmen kívül, állítsa be a `maxFailedItems` és `maxFailedItemsPerBatch` konfigurációs paramétereket a kívánt értékeket. Példa:

    {
      ... other parts of indexer definition
      "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
    }

## <a name="incremental-indexing-and-deletion-detection"></a>Növekményes indexelést és a törlés észlelése
Egy blob indexelőjével ütemezés szerint futtatni beállításakor azt reindexes csak a módosított blobok határoz meg a blob `LastModified` időbélyegző.

> [!NOTE]
> Adja meg a változásészlelési házirend nem kell – az indexelő növekményes automatikusan engedélyezve lesz az Ön számára.

Támogatja a dokumentumok törlését, használja a "helyreállítható törlés" megközelítést. Ha törli a blobokat a végleges, megfelelő dokumentumok nem törlődik a search-index. Ehelyett használja az alábbi lépéseket:  

1. Adja hozzá a metaadatok egyéni tulajdonságot jelzi, hogy logikailag törölné a rendszer az Azure Search blob
2. Az adatforrás egy helyreállítható törlési szabályzat konfigurálása
3. Az indexelő a blob (ahogy azt a az indexelő állapotának API) rendelkezik feldolgozását követően fizikailag is törölje a blobot

Például a következő házirendet figyelembe veszi a blob metaadat-tulajdonságot, ha a törlendő `IsDeleted` értékkel `true`:

    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",     
            "softDeleteColumnName" : "IsDeleted",
            "softDeleteMarkerValue" : "true"
        }
    }   

## <a name="indexing-large-datasets"></a>Az indexelő nagyméretű adatkészletek

-Blobok indexelése az időigényes folyamat lehet. Azokban az esetekben, amelyekben több millió, a blobok indexelése felgyorsíthatja az adatok particionálását, és több indexelők használatával az adatok párhuzamos feldolgozásához általi indexelés. Itt látható, hogyan lehet ezt lehet beállítani:

- Az adatok particionálása több blob-tárolók vagy virtuális mappák
- Több Azure Search az adatforrásokat, egy tároló vagy a mappa egy beállítása. A blob mappa átirányítása, használja a `query` paramétert:

    ```
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" }
    }
    ```

- Hozzon létre egy megfelelő indexelő minden adatforrás. Az azonos cél keresési indexhez minden indexelő is mutat.  

- A szolgáltatás egy keresési egység egy indexelő futtathatja egy adott időpontban. Csak akkor hasznos, ha ténylegesen párhuzamos futtatása több indexelő létrehozása a fent leírtak szerint. Párhuzamos több indexelők futtatásához, a horizontális felskálázáshoz a keresési szolgáltatás létrehozása a megfelelő számú partíciókat és -replikákat. Például ha a keresési szolgáltatás 6 keresési egységek (például a 2 partíció x 3 replikák), majd 6 indexelők is futhat egyszerre, egy six-fold az indexelési teljesítmény növekedését eredményezi. Méretezés és a kapacitástervezés kapcsolatos további információkért lásd: [erőforrásszintek méretezése a lekérdezés és a számítási feladatok indexeléséhez az Azure Search](search-capacity-planning.md).

## <a name="indexing-documents-along-with-related-data"></a>Az indexelő dokumentumokhoz kapcsolódó adatokkal együtt

Előfordulhat, hogy szeretné "összeállítás" dokumentumok az index több forrásból. Például érdemes egyesíteni a szöveget a blobok az egyéb Cosmos DB-ben tárolt metaadatok. Építse fel a dokumentumok keresése a több részből is használhatja a push API indexelő különböző indexelők együtt. 

A funkció működéséhez az összes indexelőre és más összetevők kell egyeztetni a dokumentumkulcsot. Részletes útmutatót szeretne olvassa el a külső: [Dokumentumok egyesítése más adatokkal az Azure Search ](http://blog.lytzen.name/2017/01/combine-documents-with-other-data-in.html).

<a name="IndexingPlainText"></a>
## <a name="indexing-plain-text"></a>Az indexelő egyszerű szöveg 

Ha az összes BLOB ugyanazon kódolás egyszerű szöveget tartalmaz, jelentősen növelheti az indexelési teljesítmény használatával **elemzési mód szöveg**. Elemzési mód szöveg használatához állítsa a `parsingMode` konfigurációs tulajdonság `text`:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text" } }
    }

Alapértelmezés szerint a `UTF-8` kódolás feltételezi. Egy másik kódolást megadásához használja a `encoding` konfigurációs tulajdonság: 

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text", "encoding" : "windows-1252" } }
    }


<a name="ContentSpecificMetadata"></a>
## <a name="content-type-specific-metadata-properties"></a>Tartalom típusa – is szolgáltatásspecifikus metaadatokat tulajdonságai
Az alábbi táblázat foglalja össze az egyes végzett feldolgozást, és az Azure Search által kinyert metaadatok tulajdonságait ismerteti.

| Formát / tartalom típusa | A Content-type adott metaadat-tulajdonságot | Feldolgozás részletei |
| --- | --- | --- |
| HTML (`text/html`) |`metadata_content_encoding`<br/>`metadata_content_type`<br/>`metadata_language`<br/>`metadata_description`<br/>`metadata_keywords`<br/>`metadata_title` |Sáv HTML-kód és a szöveg kinyerése |
| PDF (`application/pdf`) |`metadata_content_type`<br/>`metadata_language`<br/>`metadata_author`<br/>`metadata_title` |Bontsa ki a szöveget, beleértve a beágyazott dokumentumok (kivéve a képek) |
| DOCX (application/vnd.openxmlformats-officedocument.wordprocessingml.document) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Bontsa ki a szöveget, beleértve a beágyazott dokumentumok |
| DOKUMENTUM (alkalmazás/msword) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Bontsa ki a szöveget, beleértve a beágyazott dokumentumok |
| XLSX (application/vnd.openxmlformats-officedocument.spreadsheetml.sheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Bontsa ki a szöveget, beleértve a beágyazott dokumentumok |
| XLS (alkalmazás/vnd.ms – excel) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Bontsa ki a szöveget, beleértve a beágyazott dokumentumok |
| PPTX (application/vnd.openxmlformats-officedocument.presentationml.presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Bontsa ki a szöveget, beleértve a beágyazott dokumentumok |
| PPT (application/vnd.ms-powerpoint) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Bontsa ki a szöveget, beleértve a beágyazott dokumentumok |
| MSG (alkalmazás/vnd.ms – outlook) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_message_bcc`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_subject` |Többek között a mellékleteket, szöveg kinyerése |
| A ZIP (alkalmazás/zip) |`metadata_content_type` |Az archívumban található összes dokumentum szöveg kinyerése |
| XML (application/xml) |`metadata_content_type`</br>`metadata_content_encoding`</br> |Sáv XML-címkével, és a szöveg kinyerése |
| JSON (alkalmazás/json) |`metadata_content_type`</br>`metadata_content_encoding` |Szöveg kinyerése<br/>MEGJEGYZÉS: Ha több dokumentumot mezők kinyerése egy JSON-blobját van szüksége, tekintse meg [indexelő JSON-blobok](search-howto-index-json-blobs.md) részletekért |
| EML (üzenet/rfc822) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_creation_date`<br/>`metadata_subject` |Többek között a mellékleteket, szöveg kinyerése |
| RTF (alkalmazás/rtf) |`metadata_content_type`</br>`metadata_author`</br>`metadata_character_count`</br>`metadata_creation_date`</br>`metadata_page_count`</br>`metadata_word_count`</br> | Szöveg kinyerése|
| Egyszerű szöveg (text/plain) |`metadata_content_type`</br>`metadata_content_encoding`</br> | Szöveg kinyerése|


## <a name="help-us-make-azure-search-better"></a>Segítsen jobbá Azure Search
Ha a funkcióra vonatkozó javaslata vagy ötlete van javításai, ossza meg velünk az a [UserVoice webhelyén](https://feedback.azure.com/forums/263029-azure-search/).
