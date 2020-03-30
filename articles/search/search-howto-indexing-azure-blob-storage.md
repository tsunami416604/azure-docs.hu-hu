---
title: Keresés az Azure Blob tártartalma felett
titleSuffix: Azure Cognitive Search
description: Ismerje meg, hogyan indexelhet azure blob storage és kibont szöveget dokumentumok az Azure Cognitive Search.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 5df1198e6681431738f886eb7c3ad549936eab1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067646"
---
# <a name="how-to-index-documents-in-azure-blob-storage-with-azure-cognitive-search"></a>Dokumentumok indexelése az Azure Blob Storage szolgáltatásban az Azure Cognitive Search segítségével

Ez a cikk bemutatja, hogyan használhatja az Azure Cognitive Search index elmutató dokumentumok (például PDF-ek, Microsoft Office-dokumentumok és számos más gyakori formátumok) az Azure Blob storage-ban tárolt dokumentumok indexeléséhez. Először ismerteti a blob indexelő beállításának és konfigurálásának alapjait. Ezután kínál egy mélyebb feltárása viselkedések és forgatókönyvek akkor valószínűleg találkozás.

<a name="SupportedFormats"></a>

## <a name="supported-document-formats"></a>Támogatott dokumentumformátumok
A blob indexelő a következő dokumentumformátumokból nyerheti ki a szöveget:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="setting-up-blob-indexing"></a>Blob indexelésbeállítása
Az Azure Blob Storage indexelőt a következő használatával állíthatja be:

* [Azure-portál](https://ms.portal.azure.com)
* Azure Cognitive Search [REST API](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)
* Azure Cognitive Search [.NET SDK](https://aka.ms/search-sdk)

> [!NOTE]
> Egyes szolgáltatások (például mezőleképezések) még nem érhetők el a portálon, és programozott módon kell használni őket.
>

Itt bemutatjuk a folyamatot a REST API használatával.

### <a name="step-1-create-a-data-source"></a>1. lépés: Adatforrás létrehozása
Az adatforrás határozza meg, hogy mely adatokat kell indexelni, az adatok eléréséhez szükséges hitelesítő adatokat, valamint az adatok (új, módosított vagy törölt sorok) változásainak hatékony azonosításához szükséges házirendeket. Az adatforrást ugyanabban a keresési szolgáltatásban több indexelő is használhatja.

A blob indexeléséhez az adatforrásnak a következő szükséges tulajdonságokkal kell rendelkeznie:

* **a név** a keresési szolgáltatáson belüli adatforrás egyedi neve.
* **a típusnak** kell lennie. `azureblob`
* **hitelesítő adatok** a tárfiók kapcsolati karakterláncát `credentials.connectionString` biztosítják paraméterként. A részletekért olvassa el Az alábbi [Hitelesítő adatok megadása.](#Credentials)
* **tárolót** a tárfiókban. Alapértelmezés szerint a tárolón belüli összes blob visszakereshető. Ha csak egy adott virtuális könyvtárban szeretné indexelni a blobokat, megadhatja a könyvtárat a választható **lekérdezési** paraméter használatával.

Adatforrás létrehozása:

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
    }   

Az Adatforrás létrehozása API-ról az [Adatforrás létrehozása című](https://docs.microsoft.com/rest/api/searchservice/create-data-source)témakörben van további.

<a name="Credentials"></a>
#### <a name="how-to-specify-credentials"></a>Hitelesítő adatok megadása ####

A blobtároló hitelesítő adatait az alábbi módok egyikén adja meg:

- **Teljes hozzáférésű tárfiók-kapcsolati karakterlánc:** `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>` A kapcsolati karakterláncot az Azure Portalról szerezheti be, ha a storage-fiók panelre navigál, > a Beállítások > kulcsok (klasszikus tárfiókok esetén) vagy a Settings > Access kulcsok (Az Azure Resource Manager-tárfiókok esetében).
- **Tárfiók megosztott hozzáférés-aláírás** (SAS) kapcsolati karakterlánc: `BlobEndpoint=https://<your account>.blob.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=b&sp=rl` A SAS rendelkeznie kell a lista és olvasási engedélyek a tárolók és objektumok (blobok ebben az esetben).
-  **Tároló közös hozzáférési aláírása:** `ContainerSharedAccessUri=https://<your storage account>.blob.core.windows.net/<container name>?sv=2016-05-31&sr=c&sig=<the signature>&se=<the validity end time>&sp=rl` A SAS rendelkeznie kell a lista és olvasási engedélyeket a tárolón.

A tárolási megosztott hozzáférési aláírásokról a [Megosztott hozzáférésű aláírások használata (Közös hozzáférésű aláírások használata) (Közös hozzáférésű aláírások használata) (Közös hozzáférésű aláírások használata) (Közös hozzáférésű aláírások használata) (Közös hozzáférésű aláírások használata) témakörben talál](../storage/common/storage-dotnet-shared-access-signature-part-1.md)további információt.

> [!NOTE]
> Ha SAS-hitelesítő adatokat használ, rendszeresen frissítenie kell az adatforrás hitelesítő adatait a megújított aláírásokkal, hogy megakadályozza azok lejáratát. Ha a SAS-hitelesítő adatok lejárnak, az indexelő a következőhöz `Credentials provided in the connection string are invalid or have expired.`hasonló hibaüzenettel sikertelen lesz.  

### <a name="step-2-create-an-index"></a>2. lépés: Index létrehozása
Az index meghatározza a dokumentum, attribútumok és egyéb szerkezetek mezőit, amelyek a keresési élményt alakítják.

A következőképpen hozhat létre indexet `content` kereshető mezővel a blobokból kinyert szöveg tárolásához:   

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }

Az indexek létrehozásáról az [Index létrehozása című](https://docs.microsoft.com/rest/api/searchservice/create-index) témakörben találja a további

### <a name="step-3-create-an-indexer"></a>3. lépés: Indexelő létrehozása
Az indexelő egy adatforrást egy célkeresési indexhez kapcsol össze, és ütemezést biztosít az adatfrissítés automatizálásához.

Az index és az adatforrás létrehozása után készen áll az indexelő létrehozására:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "blob-indexer",
      "dataSourceName" : "blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

Ez az indexelő kétóránként fog futni (az ütemezési időköz "PT2H" értékre van állítva). Ha 30 percenként szeretne indexelőt futtatni, állítsa az intervallumot "PT30M" értékre. A legrövidebb támogatott időköz 5 perc. Az ütemezés nem kötelező – ha nincs megadva, az indexelő csak egyszer fut, amikor létrehozták. Az indexelőt azonban bármikor futtathatja igény szerint.   

Az Indexelő létrehozása API-val kapcsolatos további részletekért olvassa el az [Indexelő létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-indexer)című.

Az indexelő-ütemezések meghatározásáról az [Azure Cognitive Search indexelőinek ütemezése](search-howto-schedule-indexers.md)című témakörben olvashat bővebben.

<a name="how-azure-search-indexes-blobs"></a>

## <a name="how-azure-cognitive-search-indexes-blobs"></a>Hogyan indexeli az Azure Cognitive Search a blobokat?

Az [indexelő konfigurációjától](#PartsOfBlobToIndex)függően a blobindexelő csak a tárolási metaadatokat indexelheti (akkor hasznos, ha csak a metaadatok érdekelnek, és nem kell indexelni a blobok tartalmát), a tárolási és tartalmi metaadatokat, illetve a metaadatokat és a szöveges tartalmat. Alapértelmezés szerint az indexelő metaadatokat és tartalmat is kibont.

> [!NOTE]
> Alapértelmezés szerint a strukturált tartalommal rendelkező blobok, például a JSON vagy a CSV egyetlen szövegrészeként vannak indexelve. Ha a JSON- és CSV-blobokat strukturált módon szeretné indexálni, további információt a [JSON-blobok indexelése](search-howto-index-json-blobs.md) és [a CSV-blobok indexelése](search-howto-index-csv-blobs.md) című témakörben talál.
>
> Egy összetett vagy beágyazott dokumentum (például zip-archívum vagy beágyazott Outlook-e-mail mellékleteket tartalmazó Word-dokumentum) is egyetlen dokumentumként lesz indexelve.

* A dokumentum szöveges tartalmát a program egy `content`karakterláncmezőbe bontja ki.

> [!NOTE]
> Az Azure Cognitive Search korlátozza, hogy mennyi szöveget bont ki a tarifacsomagtól függően: 32 000 karakter az ingyenes szinthez, 64 000 alapszintű, 4 millió standard, 8 millió standard S2 és 16 millió standard S3. A csonkított dokumentumok indexelő állapotválaszában figyelmeztetés szerepel.  

* A blobon található, felhasználó által megadott metaadat-tulajdonságok, ha vannak ilyenek, szó szerint kinyerve. Vegye figyelembe, hogy ehhez meg kell határozni egy mezőt az indexben, amelynek neve megegyezik a blob metaadatkulcsának nevével. Ha például a blob nak van `Sensitivity` egy `High`értékkel rendelkező metaadatkulcsa, meg kell határoznia egy nevű mezőt `Sensitivity` a keresési indexben, és a program feltölti az értékkel. `High`
* A szabványos blob metaadat-tulajdonságok a következő mezőkbe kerülnek kinyerésre:

  * **\_\_metaadat-tároló neve** (Edm.String) – a blob fájlneve. Ha például van egy blob /my-container/my-folder/subfolder/resume.pdf, akkor `resume.pdf`a mező értéke a .
  * **\_\_metaadat-tárolási útvonal** (Edm.String) – a blob teljes URI-ja, beleértve a tárfiókot is. Például: `https://myaccount.blob.core.windows.net/my-container/my-folder/subfolder/resume.pdf`
  * **\_metaadat-tároló\_tartalomtípusa\_** (Edm.String) – a blob feltöltéséhez használt kód által megadott tartalomtípus. Például: `application/octet-stream`.
  * **\_metaadat-tároló\_\_utolsó módosítása** (Edm.DateTimeOffset) – a blob utolsó módosított időbélyege. Az Azure Cognitive Search ezt az időbélyeget használja a megváltozott blobok azonosítására, hogy elkerülje a kezdeti indexelés után mindent újraindexelése.
  * **\_\_metaadat-tároló mérete** (Edm.Int64) – blob mérete bájtban.
  * **metaadat-tároló\_\_tartalom\_md5** (Edm.String) - MD5 kivonat a blob tartalom, ha elérhető.
  * **metaadat-tároló\_\_\_sas token** (Edm.String) – egy ideiglenes SAS-jogkivonat, amely [egyéni képességek](cognitive-search-custom-skill-interface.md) által használható a blob eléréséhez. Ezt a jogkivonatot nem szabad tárolni későbbi használatra, mert előfordulhat, hogy lejár.

* Az egyes dokumentumformátumokra jellemző metaadat-tulajdonságok az [itt](#ContentSpecificMetadata)felsorolt mezőkbe kerülnek.

Nem kell mezőket definiálnia a keresési indexben a fenti tulajdonságok mindegyikéhez – csak rögzítse az alkalmazáshoz szükséges tulajdonságokat.

> [!NOTE]
> A meglévő indexben lévő mezőnevek gyakran eltérnek a dokumentumkinyerés során létrehozott mezőnevektől. **A mezőleképezések** segítségével leképezheti az Azure Cognitive Search által biztosított tulajdonságneveket a keresési indexben szereplő mezőnevekhez. Az alábbiakban megjelenik egy példa a mezőleképezések használatára.
>
>

<a name="DocumentKeys"></a>
### <a name="defining-document-keys-and-field-mappings"></a>Dokumentumkulcsok és mezőleképezések definiálása
Az Azure Cognitive Search, a dokumentum kulcs egyedileg azonosítja a dokumentumot. Minden keresési indexnek pontosan egy Edm.String típusú kulcsmezővel kell rendelkeznie. A kulcsmező minden, az indexhez hozzáadott bizonylathoz szükséges (valójában ez az egyetlen szükséges mező).  

Alaposan gondolja át, hogy melyik kibontott mezőnek kell leképeznie az index kulcsmezőjére. A jelöltek a következők:

* **\_\_metaadat-tároló neve** – ez lehet egy kényelmes jelölt, de vegye figyelembe, hogy 1) a nevek nem lehetegyedi, mivel előfordulhat, hogy a blobok azonos nevű különböző mappákban, és 2) a név tartalmazhat karaktereket, amelyek érvénytelenek a dokumentumkulcsok, például kötőjelek. Az érvénytelen karaktereket a `base64Encode` [mezőleképezési funkcióval](search-indexer-field-mappings.md#base64EncodeFunction) is megoldhatja – ha ezt teszi, ne felejtse el kódolni a dokumentumkulcsokat, amikor api-hívásokban, például a lookupban adja át őket. (Például a .NET használhatja az [UrlTokenEncode metódust](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) erre a célra).
* **metaadat-tárolási\_\_útvonal** – a teljes elérési út használata biztosítja az egyediséget, de az elérési út egyértelműen érvénytelen karaktereket tartalmaz `/` a [dokumentumkulcsban.](https://docs.microsoft.com/rest/api/searchservice/naming-rules)  Mint fent, lehetősége van a gombok kódolására a `base64Encode` [funkció](search-indexer-field-mappings.md#base64EncodeFunction)használatával.
* Ha a fenti lehetőségek egyike sem működik, egyéni metaadat-tulajdonságot adhat a blobokhoz. Ez a beállítás azonban megköveteli, hogy a blob feltöltési folyamat a metaadat-tulajdonság hozzáadása az összes blobok. Mivel a kulcs egy kötelező tulajdonság, minden blobok, amelyek nem rendelkeznek ezzel a tulajdonsággal nem lesz indexelt.

> [!IMPORTANT]
> Ha nincs explicit leképezése a kulcsmező az indexben, az Azure Cognitive Search automatikusan használja `metadata_storage_path` a kulcs és a base-64 kódolja a kulcsértékeket (a második lehetőség a fenti).
>
>

Ebben a példában válasszuk `metadata_storage_name` ki a mezőt dokumentumkulcsként. Tételezzük fel azt is, hogy `key` az indexben van egy elnevezett kulcsmező és egy mező `fileSize` a dokumentumméret tárolására. Ha igény szerint szeretné bedrótozni a dolgokat, adja meg a következő mezőleképezéseket az indexelő létrehozásakor vagy frissítésekor:

    "fieldMappings" : [
      { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
      { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
    ]

Az összes összehozásához az alábbiakban adhat hozzá mezőleképezéseket, és engedélyezheti a kulcsok 64-es alapú kódolását egy meglévő indexelőhöz:

    PUT https://[service name].search.windows.net/indexers/blob-indexer?api-version=2019-05-06
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
> A mezőleképezésekről a [cikkben](search-indexer-field-mappings.md)olvashat bővebben.
>
>

<a name="WhichBlobsAreIndexed"></a>
## <a name="controlling-which-blobs-are-indexed"></a>Az indexelt blobok szabályozása
Szabályozhatja, hogy mely blobok vannak indexelve, és melyek et hagyja ki.

### <a name="index-only-the-blobs-with-specific-file-extensions"></a>Csak a adott fájlkiterjesztésű blobok indexelése
Csak a blobokat indexelheti a megadott fájlnév-kiterjesztésekkel az `indexedFileNameExtensions` indexelő konfigurációs paraméterhasználatával. Az érték egy vesszővel tagolt fájlkiterjesztéseket tartalmazó karakterlánc (sortávolsággal). Ha például csak atindexeli a t. PDF és a . DOCX blobok, tegye a következőket:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
    }

### <a name="exclude-blobs-with-specific-file-extensions"></a>Adott fájlkiterjesztésű blobok kizárása
A konfigurációs paraméter használatával kizárhatja az adott `excludedFileNameExtensions` fájlnévkiterjesztésű blobokat az indexelésből. Az érték egy vesszővel tagolt fájlkiterjesztéseket tartalmazó karakterlánc (sortávolsággal). Például az összes blob indexeléséhez, kivéve azokat, amelyek a. PNG és . JPEG-bővítményeket, tegye a következőket:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
    }

Ha `indexedFileNameExtensions` mindkettő `excludedFileNameExtensions` és paraméter van jelen, az `indexedFileNameExtensions`Azure `excludedFileNameExtensions`Cognitive Search először a , majd a. Ez azt jelenti, hogy ha ugyanaz a fájlkiterjesztés mindkét listában megtalálható, akkor az ki lesz zárva az indexelésből.

<a name="PartsOfBlobToIndex"></a>
## <a name="controlling-which-parts-of-the-blob-are-indexed"></a>A blob mely részeinek indexelése

A `dataToExtract` konfigurációs paraméter rel szabályozhatja, hogy a blobok mely részei kerülnek indexelésre. A következő értékeket veheti igénybe:

* `storageMetadata`- azt adja meg, hogy csak a [szabványos blob-tulajdonságok és a felhasználó által megadott metaadatok](../storage/blobs/storage-properties-metadata.md) indexelésre kerülnek.
* `allMetadata`- meghatározza, hogy a tárolási metaadatok és a blobtartalomból kinyert [tartalomtípus-specifikus metaadatok](#ContentSpecificMetadata) indexelve vannak.- specifies that storage metadata and the content-type specific metadata extracted from the blob content are indexed.
* `contentAndMetadata`- meghatározza, hogy a blobból kinyert összes metaadat és szöveges tartalom indexelve van.- specifies that all metadata and textual content extracted from the blob are indexed. Ez az alapértelmezett érték.

Ha például csak a tárolási metaadatokat szeretné indexálni, használja a következőket:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "dataToExtract" : "storageMetadata" } }
    }

### <a name="using-blob-metadata-to-control-how-blobs-are-indexed"></a>Blob metaadatok használata a blobok indexelésének szabályozására

A fent leírt konfigurációs paraméterek minden blobra vonatkoznak. Előfordulhat, hogy szeretné szabályozni, hogy *az egyes blobok* indexelése. Ezt a blob metaadat-tulajdonságainak és értékeinek hozzáadásával teheti meg:

| Tulajdonság neve | Tulajdonság értéke | Magyarázat |
| --- | --- | --- |
| AzureSearch_Skip |"igaz" |Arra utasítja a blob indexelő, hogy teljesen hagyja ki a blobot. Sem metaadatok, sem tartalomkinyerés i. kísérlet nem történik meg. Ez akkor hasznos, ha egy adott blob sikertelen többször, és megszakítja az indexelési folyamatot. |
| AzureSearch_SkipContent |"igaz" |Ez megegyezik `"dataToExtract" : "allMetadata"` a [fent](#PartsOfBlobToIndex) leírt hatókör egy adott blob. |

<a name="DealingWithErrors"></a>
## <a name="dealing-with-errors"></a>A hibák kezelése

Alapértelmezés szerint a blob indexelő leáll, amint találkozik egy blob egy nem támogatott tartalomtípus (például egy kép). Természetesen használhatja a `excludedFileNameExtensions` paramétert bizonyos tartalomtípusok kihagyására. Előfordulhat azonban, hogy előre kell indexelnie a blobokat anélkül, hogy előre ismerné az összes lehetséges tartalomtípust. Ha nem támogatott tartalomtípus esetén folytatni szeretné az `failOnUnsupportedContentType` indexelést, állítsa a konfigurációs paramétert a következőre: `false`

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false } }
    }

Egyes blobok esetén az Azure Cognitive Search nem tudja meghatározni a tartalomtípust, vagy nem tudja feldolgozni az egyébként támogatott tartalomtípusú dokumentumokat. A hibamód figyelmen `failOnUnprocessableDocument` kívül hagyásához állítsa a konfigurációs paramétert false értékűre:

      "parameters" : { "configuration" : { "failOnUnprocessableDocument" : false } }

Az Azure Cognitive Search korlátozza az indexelt blobok méretét. Ezeket a korlátokat az [Azure Cognitive Search szolgáltatáskorlátai](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)dokumentálják. A túlméretes blobokat a rendszer alapértelmezés szerint hibaként kezeli. A túlméretes blobok tárolási metaadatait azonban továbbra `indexStorageMetadataOnlyForOversizedDocuments` is indexelheti, ha a konfigurációs paramétert true értékre állítja: 

    "parameters" : { "configuration" : { "indexStorageMetadataOnlyForOversizedDocuments" : true } }

Folytathatja az indexelést is, ha hibák történnek a feldolgozás bármely pontján, akár blobok elemzésével, akár dokumentumok indexhez való hozzáadása közben. Adott számú hiba figyelmen kívül `maxFailedItems` `maxFailedItemsPerBatch` hagyásához állítsa a és a konfigurációs paramétereket a kívánt értékekre. Példa:

    {
      ... other parts of indexer definition
      "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
    }

## <a name="incremental-indexing-and-deletion-detection"></a>Növekményes indexelés és törlés észlelése

Ha beállítja a blob indexelő egy ütemezés szerint fut, csak a módosított blobok, `LastModified` a blob időbélyege határozza meg.

> [!NOTE]
> Nem kell megadnia a változásészlelési házirendet – a növekményes indexelés automatikusan engedélyezve van.

A dokumentumok törlésének támogatásához használja a "helyreállítható törlés" megközelítést. Ha véglegesen törli a blobokat, a megfelelő dokumentumok nem törlődnek a keresési indexből.

A "soft delete" módszer kétféleképpen valósítható meg. Mindkettőt az alábbiakban ismertetjük.

### <a name="native-blob-soft-delete-preview"></a>Natív blob helyreállítható törlése (előzetes verzió)

> [!IMPORTANT]
> A natív blob ideiglenes törlése előzetes verzióban érhető el. Az előzetes verzió funkció szolgáltatásszint-szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) A [REST API 2019-05-06-Preview verziója](https://docs.microsoft.com/azure/search/search-api-preview) biztosítja ezt a funkciót. Jelenleg nincs portál- vagy .NET SDK-támogatás.

> [!NOTE]
> A natív blob helyreállítható törlési szabályzat használataesetén az indexben lévő dokumentumok dokumentumkulcsainak blobtulajdonságnak vagy blobmetaadatnak kell lenniük.

Ebben a módszerben az Azure Blob storage által kínált [natív blob helyreállítható törlési](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) funkciót fogja használni. Ha a natív blob helyreállítható törlése engedélyezve van a tárfiókban, az adatforrás natív helyreállítható törlési házirend-készlettel rendelkezik, és az indexelő talál egy blobot, amely átlett váltva egy helyreállíthatóan törölt állapotba, az indexelő eltávolítja a dokumentumot az indexből. A natív blob helyreállítható törlési szabályzat a blobok indexelésekaz Azure Data Lake Storage Gen2 indexelések.

Ehhez a következő lépések szükségesek:
1. Engedélyezze [a natív, helyreállítható törlést az Azure Blob storage számára.](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) Azt javasoljuk, hogy az adatmegőrzési szabályzatot az indexelő időközeütemezésnél sokkal magasabb értékre állítja. Így ha probléma merül fel az indexelő futtatásakor, vagy ha nagy számú dokumentumot indexel, rengeteg idő áll az indexelő számára, hogy végül feldolgozza a helyreállíthatóan törölt blobokat. Az Azure Cognitive Search indexelők csak akkor töröl egy dokumentumot az indexből, ha feldolgozza a blobot, miközben az egy helyreállíthatóan törölt állapotban van.
1. Konfiguráljon egy natív blob lágy törlésészlelési házirendet az adatforráson. Erre mutat példát az alábbi ábra. Mivel ez a funkció előzetes verzióban érhető el, az előzetes REST API-t kell használnia.
1. Futtassa az indexelőt, vagy állítsa be az indexelőt ütemezés szerint. Amikor az indexelő fut, és feldolgozza a bloba a dokumentum törlődik az indexből.

    ```
    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2019-05-06-Preview
    Content-Type: application/json
    api-key: [admin key]
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : null },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.NativeBlobSoftDeleteDeletionDetectionPolicy"
        }
    }
    ```

#### <a name="reindexing-undeleted-blobs"></a>A nem törölt blobok újraindexelése

Ha töröl egy blobot az Azure Blob storage-ból a natív helyreállítható törlés engedélyezve van a tárfiókban, a blob átvált egy helyreállíthatóan törölt állapotba, így a megőrzési időszakon belül törölheti a blobot. Ha egy Azure Cognitive Search adatforrás rendelkezik egy natív blob helyreállítható törlési szabályzattal, és az indexelő feldolgoz egy helyreállíthatóan törölt blobot, eltávolítja a dokumentumot az indexből. Ha ezt a blobot később törlik, az indexelő nem mindig indexeli újra a blobot. Ennek az az oka, hogy az indexelő határozza `LastModified` meg, hogy mely blobok indexa blobok a blob időbélyege alapján. Ha egy helyreállíthatóan törölt `LastModified` blob törlésre kerül, az időbélyeg nem frissül, `LastModified` így ha az indexelő már feldolgozta a blobokat a nem törölt blobnál újabb időbélyegekkel, akkor nem indexeli újra a nem törölt blobot. Győződjön meg arról, hogy egy nem törölt blob újraindexelt, frissítenie kell a blob időbélyegét. `LastModified` Ennek egyik módja a blob metaadatainak újramentése. Nem kell módosítania a metaadatokat, de a metaadatok újramentése frissíti a blob időbélyegét, `LastModified` hogy az indexelő tudja, hogy újra indexelnie kell ezt a blobot.

### <a name="soft-delete-using-custom-metadata"></a>Ideiglenes törlés egyéni metaadatok használatával

Ebben a módszerben egy blob metaadatait fogja használni annak jelzésére, hogy egy dokumentumot mikor kell eltávolítani a keresési indexből.

Ehhez a következő lépések szükségesek:

1. Adjon hozzá egy egyéni metaadat-kulcs-érték pár a blob jelzi, hogy az Azure Cognitive Search, hogy logikailag törölt.
1. Lágy törlési oszlopészlelési házirend konfigurálása az adatforráson. Erre mutat példát az alábbi ábra.
1. Miután az indexelő feldolgozta a blobot, és törölte a dokumentumot az indexből, törölheti a blobot az Azure Blob storage számára.

A következő házirend például úgy véli, hogy egy blob `IsDeleted` ot törölni `true`kell, ha rendelkezik egy metaadat-tulajdonsággal, amelynek értéke:

    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : null },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",     
            "softDeleteColumnName" : "IsDeleted",
            "softDeleteMarkerValue" : "true"
        }
    }

#### <a name="reindexing-undeleted-blobs"></a>A nem törölt blobok újraindexelése

Ha beállít egy ideiglenes encikó-észlelési házirendet az adatforráson, majd hozzáadja az egyéni metaadatokat egy blobhoz a jelölőértékkel, majd futtatja az indexelőt, az indexelő eltávolítja a dokumentumot az indexből. Ha szeretné újraindexelni a dokumentumot, egyszerűen módosítsa a blob lágy törlési metaadat-értékét, és futtassa újra az indexelőt.

## <a name="indexing-large-datasets"></a>Nagy adatkészletek indexelése

Az indexelési blobok időigényes folyamat lehet. Azokban az esetekben, ahol több millió blobok indexelés, felgyorsíthatja az indexelés az adatok particionálásával, és több indexelők az adatok feldolgozása párhuzamosan. Ezt a következőképpen állíthatja be:

- Az adatok particionálása több blobtárolóra vagy virtuális mappára
- Állítson be több Azure Cognitive Search adatforrást, tárolónként vagy mappánként egyet. Ha egy blobmappára szeretne `query` mutatni, használja a következő paramétert:

    ```
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" }
    }
    ```

- Hozzon létre egy megfelelő indexelőt minden adatforráshoz. Az összes indexelők pont ugyanerre a cél keresési index.  

- A szolgáltatás ban egy keresési egység egy indexelőt futtathat egy adott időpontban. Több indexelők létrehozása a fent leírt csak akkor hasznos, ha ténylegesen párhuzamosan futnak. Több indexelő párhuzamos futtatásához horizontális felskálázás a keresési szolgáltatás megfelelő számú partíció és replikák létrehozásával. Ha például a keresési szolgáltatás 6 keresési egységgel rendelkezik (például 2 partíció x 3 replikával), akkor 6 indexelő futhat egyidejűleg, ami hatszoros növekedést eredményez az indexelési átviteli értékben. A méretezésről és a kapacitástervezésről az [Azure Cognitive Search lekérdezési és indexelési számítási feladatok erőforrásszintjeinek méretezése.](search-capacity-planning.md)

## <a name="indexing-documents-along-with-related-data"></a>Dokumentumok indexelése a kapcsolódó adatokkal együtt

Előfordulhat, hogy az indexben több forrásból származó dokumentumokat szeretne "összeállítani". Előfordulhat például, hogy a blobokból származó szöveget a Cosmos DB-ben tárolt más metaadatokkal szeretné egyesíteni. Akár a push indexelési API-t és a különböző indexelőket is használhatja a keresési dokumentumok több részből történő összeállításához. 

Ahhoz, hogy ez működjön, minden indexelőnek és egyéb összetevőnek meg kell állapodnia a dokumentumkulcsról. A témakörtovábbi részleteiért tekintse meg [több Azure-adatforrás indexelését.](https://docs.microsoft.com/azure/search/tutorial-multiple-data-sources) Részletes útmutatást ebben a külső cikkben olvashat: [Dokumentumok kombinálása más adatokkal az Azure Cognitive Search alkalmazásban.](https://blog.lytzen.name/2017/01/combine-documents-with-other-data-in.html)

<a name="IndexingPlainText"></a>
## <a name="indexing-plain-text"></a>Egyszerű szöveg indexelése 

Ha az összes blob egyszerű szöveget tartalmaz ugyanabban a kódolásban, a szövegelemzési mód használatával jelentősen javíthatja az **indexelési teljesítményt.** Szövegelemzési mód használatához állítsa `parsingMode` a konfigurációs tulajdonságot a következőre: `text`

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text" } }
    }

Alapértelmezés szerint `UTF-8` a program feltételezi a kódolást. Másik kódolás megadásához használja `encoding` a konfigurációs tulajdonságot: 

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text", "encoding" : "windows-1252" } }
    }


<a name="ContentSpecificMetadata"></a>
## <a name="content-type-specific-metadata-properties"></a>Tartalomtípus-specifikus metaadatok tulajdonságai
Az alábbi táblázat összefoglalja az egyes dokumentumformátumok feldolgozását, és ismerteti az Azure Cognitive Search által kinyert metaadat-tulajdonságokat.

| Dokumentum formátuma / tartalomtípusa | Tartalomtípus-specifikus metaadatok tulajdonságai | Részletek feldolgozása |
| --- | --- | --- |
| HTML (szöveg/html) |`metadata_content_encoding`<br/>`metadata_content_type`<br/>`metadata_language`<br/>`metadata_description`<br/>`metadata_keywords`<br/>`metadata_title` |HTML-jelölés lebontása és szöveg kibontása |
| PDF (alkalmazás/pdf) |`metadata_content_type`<br/>`metadata_language`<br/>`metadata_author`<br/>`metadata_title` |Szöveg kinyerése, beleértve a beágyazott dokumentumokat is (a képek kivételével) |
| DOCX (alkalmazás/vnd.openxmlformats-officedocument.wordprocessingml.document) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Szöveg kinyerése, beleértve a beágyazott dokumentumokat is |
| DOC (alkalmazás/mkard) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Szöveg kinyerése, beleértve a beágyazott dokumentumokat is |
| DOCM (alkalmazás/vnd.ms-word.document.macroenabled.12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Szöveg kinyerése, beleértve a beágyazott dokumentumokat is |
| WORD XML (alkalmazás/vnd.ms-word2006ml) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |XML-jelölés lebontása és szöveg kibontása |
| WORD 2003 XML (alkalmazás/vnd.ms-wordml) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date` |XML-jelölés lebontása és szöveg kibontása |
| XLSX (alkalmazás/vnd.openxmlformats-officedocument.spreadsheetml.sheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Szöveg kinyerése, beleértve a beágyazott dokumentumokat is |
| XLS (alkalmazás/vnd.ms-excel) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Szöveg kinyerése, beleértve a beágyazott dokumentumokat is |
| XLSM (alkalmazás/vnd.ms-excel.sheet.macroenabled.12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Szöveg kinyerése, beleértve a beágyazott dokumentumokat is |
| PPTX (alkalmazás/vnd.openxmlformats-officedocument.presentationml.presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Szöveg kinyerése, beleértve a beágyazott dokumentumokat is |
| PPT (alkalmazás/vnd.ms-powerpoint) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Szöveg kinyerése, beleértve a beágyazott dokumentumokat is |
| PPTM (alkalmazás/vnd.ms-powerpoint.presentation.macroenabled.12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Szöveg kinyerése, beleértve a beágyazott dokumentumokat is |
| MSG (alkalmazás/vnd.ms-outlook) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_from_email`<br/>`metadata_message_to`<br/>`metadata_message_to_email`<br/>`metadata_message_cc`<br/>`metadata_message_cc_email`<br/>`metadata_message_bcc`<br/>`metadata_message_bcc_email`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_subject` |Szöveg kibontása, beleértve a mellékleteket is. `metadata_message_to_email`, `metadata_message_cc_email` `metadata_message_bcc_email` és karakterlánc-gyűjtemények, a többi mező karakterlánc.|
| ODT (alkalmazás/vnd.oasis.opendocument.text) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Szöveg kinyerése, beleértve a beágyazott dokumentumokat is |
| ODS (alkalmazás/vnd.oasis.opendocument.spreadsheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Szöveg kinyerése, beleértve a beágyazott dokumentumokat is |
| ODP (alkalmazás/vnd.oasis.opendocument.presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`title` |Szöveg kinyerése, beleértve a beágyazott dokumentumokat is |
| ZIP (alkalmazás/zip) |`metadata_content_type` |Szöveg kinyerése az archívum összes dokumentumából |
| GZ (alkalmazás/gzip) |`metadata_content_type` |Szöveg kinyerése az archívum összes dokumentumából |
| EPUB (alkalmazás/epub+zip) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_title`<br/>`metadata_description`<br/>`metadata_language`<br/>`metadata_keywords`<br/>`metadata_identifier`<br/>`metadata_publisher` |Szöveg kinyerése az archívum összes dokumentumából |
| XML (alkalmazás/xml) |`metadata_content_type`<br/>`metadata_content_encoding`<br/> |XML-jelölés lebontása és szöveg kibontása |
| JSON (alkalmazás/json) |`metadata_content_type`<br/>`metadata_content_encoding` |Szöveg kinyerése<br/>MEGJEGYZÉS: Ha több dokumentummezőt kell kinyernie egy JSON-blobból, olvassa el [a JSON-blobok indexelése](search-howto-index-json-blobs.md) a részleteket. |
| EML (üzenet/rfc822) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_creation_date`<br/>`metadata_subject` |Szöveg kibontása mellékletekkel együtt |
| RTF (alkalmazás/rtf) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_page_count`<br/>`metadata_word_count`<br/> | Szöveg kinyerése|
| Egyszerű szöveg (szöveg/egyszerű) |`metadata_content_type`<br/>`metadata_content_encoding`<br/> | Szöveg kinyerése|


## <a name="help-us-make-azure-cognitive-search-better"></a>Segítsen nekünk az Azure Cognitive Search jobbá tenni
Ha van funkció kérések vagy ötletek et fejlesztések, tudassa velünk a mi [UserVoice oldalon](https://feedback.azure.com/forums/263029-azure-search/).
