---
title: BLOB-indexelő konfigurálása
titleSuffix: Azure Cognitive Search
description: Állítson be egy Azure Blob indexelő, amely automatizálja a Blobok tartalmának indexelését a teljes szöveges keresési műveletekhez az Azure Cognitive Searchban.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: e3419711c9a7358914f85574f6dbd5af29def1cf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91403610"
---
# <a name="how-to-configure-a-blob-indexer-in-azure-cognitive-search"></a>BLOB-indexelő konfigurálása az Azure-ban Cognitive Search

Ez a cikk bemutatja, hogyan használható az Azure Cognitive Search az Azure Blob Storage-ban tárolt szöveg-alapú dokumentumok (például PDF-fájlok, Microsoft Office dokumentumok és számos más gyakori formátum) indexeléséhez. Első lépésként ismerteti a blob-indexelő beállításának és konfigurálásának alapjait. Ezt követően mélyebben megismerheti a viselkedéseket és a valószínűleg felmerülő forgatókönyveket.

<a name="SupportedFormats"></a>

## <a name="supported-formats"></a>Támogatott formátumok

A blob-indexelő a következő dokumentum-formátumokból tud szöveget kinyerni:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="set-up-blob-indexing"></a>BLOB-indexelés beállítása

Beállíthat egy Azure Blob Storage indexelő a használatával:

* [Azure Portal](https://ms.portal.azure.com)
* Azure Cognitive Search [REST API](/rest/api/searchservice/Indexer-operations)
* Azure Cognitive Search [.net SDK](/dotnet/api/overview/azure/search)

> [!NOTE]
> Néhány funkció (például a mezők leképezése) még nem érhető el a portálon, és programozott módon kell használni őket.
>

Itt a REST API használatával mutatjuk be a folyamatot.

### <a name="step-1-create-a-data-source"></a>1. lépés: Adatforrás létrehozása

Az adatforrás meghatározza az adatokhoz való hozzáféréshez szükséges hitelesítő adatokat, valamint a szabályzatokat az adatok változásainak hatékony azonosításához (új, módosított vagy törölt sorok). Egy adatforrást több indexelő is használhat ugyanabban a keresési szolgáltatásban.

A blob-indexeléshez az adatforrásnak a következő szükséges tulajdonságokkal kell rendelkeznie:

* a **Name** a keresési szolgáltatásban található adatforrás egyedi neve.
* a **típusnak a következőnek** kell lennie: `azureblob`
* * * a hitelesítő adatok a Storage-fiókhoz tartozó kapcsolatok karakterláncát adják meg `credentials.connectionString` paraméterként. A részletekért lásd: az alábbi [hitelesítő adatok megadása](#Credentials) .
* a **Container** egy tárolót határoz meg a Storage-fiókban. Alapértelmezés szerint a tárolóban lévő összes blob beolvasható. Ha a blobokat csak egy adott virtuális könyvtárban szeretné indexelni, megadhatja a könyvtárat a választható **lekérdezési** paraméter használatával.

Adatforrás létrehozása:

```http
    POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
    }
```

További információ a Create DataSource API-ról: [adatforrás létrehozása](/rest/api/searchservice/create-data-source).

<a name="Credentials"></a>

#### <a name="how-to-specify-credentials"></a>Hitelesítő adatok megadása

A blob-tároló hitelesítő adatait az alábbi módszerek egyikével adhatja meg:

* **Felügyelt identitás-kapcsolatok karakterlánca**: `ResourceId=/subscriptions/<your subscription ID>/resourceGroups/<your resource group name>/providers/Microsoft.Storage/storageAccounts/<your storage account name>/;` 

  Ehhez a kapcsolódási karakterlánchoz nem szükséges a fiók kulcsa, de a [felügyelt identitás használatával kell megadnia egy Azure Storage-fiókhoz való kapcsolódás beállításának](search-howto-managed-identities-storage.md)utasításait.

* **Teljes hozzáférésű Storage-fiók kapcsolati sztringje**: `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>`

  A kapcsolati karakterláncot a Azure Portal a Storage-fiók panel > beállítások > kulcsok (klasszikus Storage-fiókok esetében) vagy a beállítások > hozzáférési kulcsok (Azure Resource Manager Storage-fiókok esetében) lehetőségre kattintva érheti el.

* A **Storage-fiók közös hozzáférésű aláírása** (SAS) kapcsolati karakterlánca:`BlobEndpoint=https://<your account>.blob.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=b&sp=rl`

  Az SAS-nek szerepelnie kell a listán, és olvasási engedéllyel kell rendelkeznie a tárolók és objektumok számára (ebben az esetben Blobok).

* **Tároló közös hozzáférésének aláírása**: `ContainerSharedAccessUri=https://<your storage account>.blob.core.windows.net/<container name>?sv=2016-05-31&sr=c&sig=<the signature>&se=<the validity end time>&sp=rl`

  Az SAS-nek a tárolóban szerepelnie kell a listához és az olvasáshoz szükséges engedélyekkel.

További információ a Storage közös hozzáférésű aláírásáról: a [közös hozzáférésű aláírások használata](../storage/common/storage-sas-overview.md).

> [!NOTE]
> Ha SAS hitelesítő adatokat használ, az adatforráshoz tartozó hitelesítő adatokat rendszeresen frissítenie kell megújított aláírásokkal a lejárat megakadályozása érdekében. Ha az SAS hitelesítő adatai lejárnak, az indexelő a következőhöz hasonló hibaüzenettel fog meghiúsulni: `Credentials provided in the connection string are invalid or have expired.` .  

### <a name="step-2-create-an-index"></a>2. lépés: Index létrehozása

Az index meghatározza a dokumentumok, attribútumok és más, a keresési élményt formáló szerkezetek mezőit.

Ebből a témakörből megtudhatja, hogyan hozhat létre egy kereshető mezőt tartalmazó indexet `content` a blobokból kinyert szöveg tárolásához:

```http
    POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }
```

További információ: [create index (REST API)](/rest/api/searchservice/create-index).

### <a name="step-3-create-an-indexer"></a>3. lépés: indexelő létrehozása

Az indexelő összekapcsolja az adatforrást a cél keresési indexszel, és az Adatfrissítés automatizálására szolgáló ütemtervet biztosít.

Miután létrehozta az indexet és az adatforrást, készen áll az indexelő létrehozására:

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "blob-indexer",
      "dataSourceName" : "blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }
```

Ez az indexelő két óránként fut (az ütemezett időköz értéke "PT2H"). Az indexelő 30 percenkénti futtatásához állítsa az intervallumot "PT30M" értékre. A legrövidebb támogatott időköz 5 perc. Az ütemterv nem kötelező – ha nincs megadva, az indexelő csak egyszer fut a létrehozáskor. Az indexelő igény szerinti futtatása azonban bármikor elvégezhető.   

További információ: [create Indexer (REST API)](/rest/api/searchservice/create-indexer). Az indexelő-ütemtervek definiálásával kapcsolatos további információkért lásd: [Az Azure Cognitive Search indexelő szolgáltatásának beosztása](search-howto-schedule-indexers.md).

<a name="how-azure-search-indexes-blobs"></a>

## <a name="how-blobs-are-indexed"></a>A Blobok indexelése

Az [Indexelő konfigurációjától](#PartsOfBlobToIndex)függően a blob indexelő csak a tárolási metaadatokat tudja indexelni (ez akkor hasznos, ha csak a metaadatokat érdekli, és nem szükséges a Blobok tartalmának indexelése), a tárolási és tartalmi metaadatokat, illetve a metaadatokat és a szöveges tartalmakat is. Alapértelmezés szerint az indexelő kibontja a metaadatokat és a tartalmakat is.

> [!NOTE]
> Alapértelmezés szerint a strukturált tartalmat, például a JSON-t vagy a CSV-t tartalmazó Blobok egyetlen darab szövegként vannak indexelve. Ha a JSON-és CSV-blobokat strukturált módon szeretné indexelni, további információért lásd: [JSON-Blobok indexelése](search-howto-index-json-blobs.md) és [CSV-Blobok indexelése](search-howto-index-csv-blobs.md) .
>
> Összetett vagy beágyazott dokumentum (például ZIP-archívum, beágyazott Outlook-e-mail-mellékleteket tartalmazó Word-dokumentum vagy a). A mellékleteket tartalmazó MSG-fájl is egyetlen dokumentumként van indexelve. Például az összes kép a mellékletekből kinyerve. A rendszer a normalized_images mezőben adja vissza az MSG-fájlt.

* A dokumentum szöveges tartalma kinyerve egy nevű karakterlánc-mezőbe `content` .

  > [!NOTE]
  > Az Azure Cognitive Search az árképzési szinttől függően Kinyeri a szöveg mennyiségét: 32 000 karakter az ingyenes szinthez, 64 000 az alapszintű, 4 000 000 a standard, a 8 000 000 a standard S2 és az 16 000 000 standard S3-hoz. A rendszer figyelmeztetést tartalmaz az indexelő állapotának a csonkolt dokumentumokra adott válaszában.  

* A blobon (ha van ilyen) a felhasználó által megadott metaadat-tulajdonságok szó szerint vannak kibontva. Vegye figyelembe, hogy ehhez az indexben meg kell adni egy olyan mezőt, amelynek a neve megegyezik a blob metaadat-kulcsával. Ha például a blob egy értékkel rendelkező metaadat-kulccsal rendelkezik `Sensitivity` `High` , akkor meg kell adnia egy nevű mezőt a `Sensitivity` keresési indexben, és az értékkel lesz feltöltve `High` .

* A Blobok szabványos metaadat-tulajdonságainak kibontása a következő mezőkbe történik:

  * **metaadat- \_ tároló \_ neve** (EDM. String) – a blob fájlneve. Ha például egy blob/My-Container/My-Folder/subfolder/resume.pdf, a mező értéke `resume.pdf` .

  * **metaadat- \_ tároló \_ elérési útja** (EDM. String) – a blob teljes URI-ja, beleértve a Storage-fiókot is. Például: `https://myaccount.blob.core.windows.net/my-container/my-folder/subfolder/resume.pdf`

  * **metaadatok \_ tárolási \_ tartalmának \_ típusa** (EDM. String) – tartalomtípus, amelyet a blob feltöltéséhez használt kód határoz meg. Például: `application/octet-stream`.

  * **metaadat- \_ tároló \_ utolsó \_ módosítása** (EDM. DateTimeOffset) – a blob utolsó módosításának időbélyege. Az Azure Cognitive Search ezt az időbélyeget használja a módosított Blobok azonosításához, hogy ne legyenek újraindexelve a kezdeti indexelés után.

  * **metaadatok \_ tárolási \_ mérete** (EDM. Int64) – blob mérete bájtban.

  * **metaadat- \_ tároló \_ tartalma \_ Md5** (EDM. String) – a blob tartalmának MD5 kivonata, ha van ilyen.

  * **metaadat- \_ tároló \_ sas- \_ tokenje** (EDM. String) – ideiglenes sas-token, amelyet az [egyéni képességek](cognitive-search-custom-skill-interface.md) használhatnak a blobhoz való hozzáféréshez. Ezt a jogkivonatot nem szabad a későbbi használat céljából tárolni, mivel az lejáró lehet.

* Az egyes dokumentum-formátumokra jellemző metaadat-tulajdonságokat a rendszer kinyeri az [itt](#ContentSpecificMetadata)felsorolt mezőkbe.

Nem kell mezőket meghatároznia a keresési index fenti tulajdonságaihoz – csak rögzítse az alkalmazáshoz szükséges tulajdonságokat.

> [!NOTE]
> Gyakran előfordul, hogy a meglévő index mezőinek neve eltér a dokumentumok kinyerése során generált mezők neveitől. A mező- **hozzárendelések** segítségével leképezheti az Azure Cognitive Search által megadott tulajdonságokat a keresési index mezőinek neveihez. Ekkor megjelenik egy példa a mező-hozzárendelésekre, amelyeket az alábbiakban használ.
>

<a name="DocumentKeys"></a>

### <a name="defining-document-keys-and-field-mappings"></a>A dokumentumok kulcsainak és a mezők hozzárendelésének meghatározása

Az Azure Cognitive Search a dokumentum kulcsa egyedileg azonosít egy dokumentumot. Minden keresési indexnek pontosan egy EDM. String típusú Key mezővel kell rendelkeznie. A Key mezőt kötelező megadni az indexhez hozzáadott összes dokumentumhoz (ez valójában az egyetlen kötelező mező).  

Alaposan gondolja át, hogy melyik kibontott mező legyen leképezve az index Key mezőjére. A jelöltek a következők:

* **metaadat- \_ tároló \_ neve** – ez lehet egy kényelmes jelölt, de vegye figyelembe, hogy 1) a nevek nem egyediek, mivel előfordulhat, hogy az azonos nevű Blobok eltérő mappákban találhatók, és 2) a név olyan karaktereket tartalmazhat, amelyek érvénytelenek a dokumentum kulcsaiban, például kötőjelek. Az érvénytelen karaktereket a `base64Encode` [mező-hozzárendelési függvény](search-indexer-field-mappings.md#base64EncodeFunction) használatával kezelheti – ha ezt teszi, ne felejtse el kódolni a dokumentum kulcsait, amikor azok API-hívásokban, például a kereséskor kerülnek továbbításra. (Például a .NET-ben a [UrlTokenEncode metódust](/dotnet/api/system.web.httpserverutility.urltokenencode) használhatja erre a célra).

* **metaadat- \_ tárolási \_ útvonal** – a teljes elérési út használata biztosítja az egyediséget, de az elérési út határozottan olyan karaktereket tartalmaz, `/` amelyek [érvénytelenek a dokumentum kulcsában](/rest/api/searchservice/naming-rules).  A fentieknek megfelelően lehetősége van a kulcsok kódolására a `base64Encode` [függvény](search-indexer-field-mappings.md#base64EncodeFunction)használatával.

* A harmadik lehetőség egy egyéni metaadat-tulajdonság hozzáadása a blobokhoz. Ez a beállítás azonban megköveteli, hogy a blob feltöltési folyamata hozzáadja az összes blobhoz a metaadat-tulajdonságot. Mivel a kulcs egy kötelező tulajdonság, a tulajdonságot nem tartalmazó Blobok nem lesznek indexelve.

> [!IMPORTANT]
> Ha nincs explicit leképezés a kulcs mezőhöz az indexben, az Azure Cognitive Search automatikusan a kulcsként `metadata_storage_path` és az alap-64 kódolással kódolja a kulcs értékeit (a fenti második lehetőség).
>
>

Ebben a példában válassza ki a `metadata_storage_name` mezőt a dokumentum kulcsaként. Tegyük fel, hogy az indexnek van egy nevű kulcs mezője `key` és egy mező a `fileSize` dokumentum méretének tárolásához. A lehető leggyorsabban, a következő mezők-hozzárendeléseket adja meg az indexelő létrehozásakor vagy frissítésekor:

```http
    "fieldMappings" : [
      { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
      { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
    ]
```

A következő lépésekkel egyesítheti a mezőket, és engedélyezheti a kulcsok Base-64 kódolását egy meglévő indexelő számára:

```http
    PUT https://[service name].search.windows.net/indexers/blob-indexer?api-version=2020-06-30
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
```

További információ: [mező-hozzárendelések és átalakítások](search-indexer-field-mappings.md).

#### <a name="what-if-you-need-to-encode-a-field-to-use-it-as-a-key-but-you-also-want-to-search-it"></a>Mi a teendő, ha olyan mezőt kell kódolni, amelyet kulcsként kíván használni, de azt is érdemes megkeresni?

Vannak olyan időpontok, amikor egy mező kódolású verzióját kell használnia, például metadata_storage_path kulcsként, de a mezőnek kereshetőnek kell lennie (kódolás nélkül). A probléma megoldásához rendelje azt két mezőhöz; az egyik, amelyet a kulcshoz fog használni, és egy másikat, amelyet a rendszer keresési célokra használ. Az alábbi példában a *kulcs* mező a kódolt elérési utat tartalmazza, az *elérési út* mező pedig nincs kódolva, és az index kereshető mezőként lesz használva.

```http
    PUT https://[service name].search.windows.net/indexers/blob-indexer?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "dataSourceName" : " blob-datasource ",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "fieldMappings" : [
        { "sourceFieldName" : "metadata_storage_path", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
        { "sourceFieldName" : "metadata_storage_path", "targetFieldName" : "path" }
      ]
    }
```
<a name="WhichBlobsAreIndexed"></a>

## <a name="index-by-file-type"></a>Index fájltípus szerint

Megadhatja, hogy mely Blobok indexelve legyenek, és melyeket a rendszer kihagyja.

### <a name="include-blobs-having-specific-file-extensions"></a>Adott fájlkiterjesztések tartalmazó Blobok belefoglalása

Az indexelő konfigurációs paraméterrel csak azokat a blobokat lehet indexelni, amelyeket az Ön által megadott fájlnévkiterjesztéssel használ `indexedFileNameExtensions` . Az érték egy olyan karakterlánc, amely a fájlkiterjesztés vesszővel tagolt listáját tartalmazza (vezető ponttal). Például csak a érték indexeléséhez. PDF és. DOCX Blobok:

```http
    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
    }
```

### <a name="exclude-blobs-having-specific-file-extensions"></a>Megadott fájlkiterjesztés-kiterjesztésű Blobok kizárása

A konfigurációs paraméter használatával kizárhat olyan blobokat, amelyek adott fájlnévkiterjesztéssel rendelkeznek az indexelésből `excludedFileNameExtensions` . Az érték egy olyan karakterlánc, amely a fájlkiterjesztés vesszővel tagolt listáját tartalmazza (vezető ponttal). Például az összes blob indexeléséhez, kivéve a következővel:. PNG és. JPEG-bővítmények:

```http
    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
    }
```

Ha mindkettő `indexedFileNameExtensions` és `excludedFileNameExtensions` paraméter szerepel, az Azure Cognitive Search először a következőt tekinti meg: `indexedFileNameExtensions` `excludedFileNameExtensions` . Ez azt jelenti, hogy ha ugyanaz a fájlkiterjesztés szerepel mindkét listán, az indexelésből ki lesz zárva.

<a name="PartsOfBlobToIndex"></a>

## <a name="index-parts-of-a-blob"></a>BLOB elemeinek indexe

Megadhatja, hogy a Blobok mely részei legyenek indexelve a `dataToExtract` konfigurációs paraméter használatával. A következő értékeket veheti fel:

* `storageMetadata` -azt adja meg, hogy csak a [szabványos blob-tulajdonságok és a felhasználó által megadott metaadatok](../storage/blobs/storage-blob-container-properties-metadata.md) legyenek indexelve.
* `allMetadata` -Megadja, hogy a rendszer indexeli a tárolási metaadatokat és a blob tartalmából kinyert [tartalom típusú specifikus metaadatokat](#ContentSpecificMetadata) .
* `contentAndMetadata` -Megadja, hogy a blobból kinyert összes metaadat és szöveges tartalom indexelve legyen. Ez az alapértelmezett érték.

Ha például csak a tárolási metaadatokat szeretné indexelni, használja a következőt:

```http
    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "dataToExtract" : "storageMetadata" } }
    }
```

### <a name="using-blob-metadata-to-control-how-blobs-are-indexed"></a>BLOB-metaadatok használata a Blobok indexelésének vezérléséhez

A fent ismertetett konfigurációs paraméterek az összes blobra érvényesek. Előfordulhat, hogy meg szeretné határozni, hogy az *egyes Blobok* hogyan legyenek indexelve. Ezt úgy teheti meg, hogy hozzáadja a következő blob metaadat-tulajdonságokat és-értékeket:

| Tulajdonság neve | Tulajdonság értéke | Magyarázat |
| --- | --- | --- |
| AzureSearch_Skip |igaz |Arra utasítja a blob indexelő, hogy teljesen kihagyja a blobot. Sem a metaadatok, sem a tartalom kibontása nem történt meg. Ez akkor hasznos, ha egy adott blob többször meghiúsul, és megszakítja az indexelési folyamatot. |
| AzureSearch_SkipContent |igaz |Ez egyenértékű a `"dataToExtract" : "allMetadata"` [fent](#PartsOfBlobToIndex) ismertetett beállítással egy adott blobon. |

## <a name="index-from-multiple-sources"></a>Index több forrásból

Előfordulhat, hogy az indexben több forrásból is össze kívánja állítani a dokumentumokat. Előfordulhat például, hogy a Blobok szövegét szeretné egyesíteni a Cosmos DBban tárolt egyéb metaadatokkal. A leküldéses indexelés API-t és a különböző indexelő elemeket is használhatja a keresési dokumentumok több részből való kiépítéséhez.

Ahhoz, hogy működjön, minden indexelő és más összetevőnek meg kell egyeznie a dokumentum kulcsával. A témakörről további részleteket a [több Azure-adatforrások](./tutorial-multiple-data-sources.md) és a jelen blogbejegyzések indexelését ismertető cikkben talál, és az [Azure Cognitive Searchban található egyéb adatokkal egyesítheti a dokumentumokat](https://blog.lytzen.name/2017/01/combine-documents-with-other-data-in.html).

## <a name="index-large-datasets"></a>Nagyméretű adathalmazok indexelése

Az indexelési Blobok időigényes folyamat lehet. Abban az esetben, ha több millió blobot tartalmaz az indexeléshez, felgyorsíthatja az indexelést, ha particionálja az adatait, és több indexelő használatával dolgozza fel az adatait párhuzamosan. Ezt a következőképpen állíthatja be:

* Az adatai particionálása több blob-tárolóba vagy virtuális mappába

* Állítson be több Azure Cognitive Search adatforrást, egy tárolót vagy egy mappát. Ha egy blob mappára szeretne mutatni, használja a következő `query` paramétert:

    ```json
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" }
    }
    ```

* Hozzon létre egy megfelelő indexelő az egyes adatforrásokhoz. Az összes indexelő képes ugyanarra a cél keresési indexre mutatni.  

* A szolgáltatás egy keresési egysége egy adott időpontban képes futtatni egy indexelő szolgáltatást. Több indexelő létrehozása a fent leírtak szerint csak akkor hasznos, ha ténylegesen párhuzamosan futnak. Több indexelő párhuzamos futtatásához bővítse a keresési szolgáltatást a megfelelő számú partíció és replika létrehozásával. Ha például a Search szolgáltatás 6 keresési egységgel rendelkezik (például 2 partíciós x 3 replika), akkor 6 indexelő is futhat egyszerre, ami az indexelési teljesítmény hat szorzott növekedését eredményezi. A méretezéssel és a kapacitás megtervezésével kapcsolatos további tudnivalókért tekintse meg [Az Azure Cognitive Search szolgáltatás kapacitásának módosítását](search-capacity-planning.md)ismertető témakört.

<a name="DealingWithErrors"></a>

## <a name="handle-errors"></a>Hibakezelés

Alapértelmezés szerint a blob-indexelő azonnal leáll, ha nem támogatott tartalomtípusú blobot (például egy képet) észlel. Természetesen használhatja a `excludedFileNameExtensions` paramétert bizonyos tartalomtípusok kihagyása érdekében. Előfordulhat azonban, hogy a blobokat a lehetséges tartalomtípusok előzetes ismerete nélkül kell indexelni. Ha nem támogatott tartalomtípust észlel, az indexelés folytatásához állítsa a `failOnUnsupportedContentType` konfigurációs paramétert a következőre `false` :

```http
    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false } }
    }
```

Egyes Blobok esetében az Azure Cognitive Search nem tudja meghatározni a tartalomtípust, vagy nem tud feldolgozni egy egyébként támogatott tartalomtípusú dokumentumot. A hiba módjának figyelmen kívül hagyásához állítsa hamis értékre a `failOnUnprocessableDocument` konfigurációs paramétert:

```http
      "parameters" : { "configuration" : { "failOnUnprocessableDocument" : false } }
```

Az Azure Cognitive Search korlátozza az indexelt Blobok méretét. Ezek a korlátok az [Azure Cognitive Search szolgáltatási korlátaiban](./search-limits-quotas-capacity.md)vannak dokumentálva. A túlméretezett Blobok alapértelmezés szerint hibákként vannak kezelve. Azonban továbbra is indexelheti a túl nagy méretű Blobok tárolási metaadatait, ha `indexStorageMetadataOnlyForOversizedDocuments` a konfigurációs paraméter igaz értékre van állítva:

```http
    "parameters" : { "configuration" : { "indexStorageMetadataOnlyForOversizedDocuments" : true } }
```

Folytathatja az indexelést is, ha a hibák bármilyen feldolgozási ponton történnek, vagy a Blobok elemzése vagy a dokumentumok indexbe való felvétele során. Ha egy adott számú hibát szeretne figyelmen kívül hagyni, állítsa a `maxFailedItems` és a `maxFailedItemsPerBatch` konfigurációs paramétereket a kívánt értékekre. Példa:

```http
    {
      ... other parts of indexer definition
      "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
    }
```

<a name="ContentSpecificMetadata"></a>

## <a name="content-type-specific-metadata-properties"></a>Tartalomtípus-specifikus metaadatok tulajdonságai

Az alábbi táblázat összefoglalja az egyes dokumentumok formátumának feldolgozását, valamint ismerteti az Azure Cognitive Search által kinyert metaadatok tulajdonságait.

| Dokumentum formátuma/tartalmának típusa | Kinyert metaadatok | Feldolgozás részletei |
| --- | --- | --- |
| HTML (text/html) |`metadata_content_encoding`<br/>`metadata_content_type`<br/>`metadata_language`<br/>`metadata_description`<br/>`metadata_keywords`<br/>`metadata_title` |HTML-jelölés és szöveg kinyerése |
| PDF (alkalmazás/PDF) |`metadata_content_type`<br/>`metadata_language`<br/>`metadata_author`<br/>`metadata_title` |Szöveg kinyerése, beleértve a beágyazott dokumentumokat is (képek nélkül) |
| DOCX (Application/vnd.openxmlformats-officedocument.wordprocessingml.document) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Szöveg kinyerése, beleértve a beágyazott dokumentumokat is |
| DOC (alkalmazás/msword) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Szöveg kinyerése, beleértve a beágyazott dokumentumokat is |
| DOCM (Application/vnd.ms-word.document. macroenabled. 12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Szöveg kinyerése, beleértve a beágyazott dokumentumokat is |
| WORD XML (Application/vnd. MS-word2006ml) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Szalag XML-kódjának és kinyerésének szövege |
| WORD 2003 XML (Application/vnd. MS-WordML) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date` |Szalag XML-kódjának és kinyerésének szövege |
| XLSX (Application/vnd. openxmlformats-officedocument. SpreadsheetXML. Sheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Szöveg kinyerése, beleértve a beágyazott dokumentumokat is |
| XLS (Application/vnd. MS-Excel) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Szöveg kinyerése, beleértve a beágyazott dokumentumokat is |
| XLSM (Application/vnd. MS-Excel. Sheet. macroenabled. 12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Szöveg kinyerése, beleértve a beágyazott dokumentumokat is |
| PPTX (Application/vnd. openxmlformats-officedocument. presentationml. Presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Szöveg kinyerése, beleértve a beágyazott dokumentumokat is |
| PPT (Application/vnd. MS-PowerPoint) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Szöveg kinyerése, beleértve a beágyazott dokumentumokat is |
| PPTM (Application/vnd. MS-PowerPoint. Presentation. macroenabled. 12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Szöveg kinyerése, beleértve a beágyazott dokumentumokat is |
| MSG (Application/vnd. MS-Outlook) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_from_email`<br/>`metadata_message_to`<br/>`metadata_message_to_email`<br/>`metadata_message_cc`<br/>`metadata_message_cc_email`<br/>`metadata_message_bcc`<br/>`metadata_message_bcc_email`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_subject` |Szöveg kinyerése, beleértve a mellékletekből kinyert szöveget is. `metadata_message_to_email``metadata_message_cc_email`és `metadata_message_bcc_email` karakterlánc-gyűjtemények, a többi mező sztring.|
| ODT (Application/vnd. Oasis. OpenDocument. Text) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Szöveg kinyerése, beleértve a beágyazott dokumentumokat is |
| ODS (Application/vnd. Oasis. OpenDocument. számolótábla) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Szöveg kinyerése, beleértve a beágyazott dokumentumokat is |
| ODP (Application/vnd. Oasis. OpenDocument. Presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`title` |Szöveg kinyerése, beleértve a beágyazott dokumentumokat is |
| ZIP (alkalmazás/zip) |`metadata_content_type` |Szöveg kinyerése az archívumban található összes dokumentumból |
| GZ (alkalmazás/gzip) |`metadata_content_type` |Szöveg kinyerése az archívumban található összes dokumentumból |
| EPUB (alkalmazás/EPUB + zip) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_title`<br/>`metadata_description`<br/>`metadata_language`<br/>`metadata_keywords`<br/>`metadata_identifier`<br/>`metadata_publisher` |Szöveg kinyerése az archívumban található összes dokumentumból |
| XML (alkalmazás/XML) |`metadata_content_type`<br/>`metadata_content_encoding`<br/> |Szalag XML-kódjának és kinyerésének szövege |
| JSON (alkalmazás/JSON) |`metadata_content_type`<br/>`metadata_content_encoding` |Szöveg kinyerése<br/>Megjegyzés: Ha több dokumentumot szeretne kinyerni egy JSON-blobból, tekintse meg a részletek a JSON-Blobok [indexelésével](search-howto-index-json-blobs.md) foglalkozó témakört. |
| EML (üzenet/rfc822) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_creation_date`<br/>`metadata_subject` |Szöveg kinyerése, beleértve a mellékleteket |
| RTF (alkalmazás/RTF) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_page_count`<br/>`metadata_word_count`<br/> | Szöveg kinyerése|
| Egyszerű szöveg (szöveg/egyszerű) |`metadata_content_type`<br/>`metadata_content_encoding`<br/> | Szöveg kinyerése|

## <a name="see-also"></a>Lásd még

* [Indexelők az Azure Cognitive Searchben](search-indexer-overview.md)
* [Blobok ismertetése a mesterséges intelligenciával](search-blob-ai-integration.md)
* [A blob indexelésének áttekintése](search-blob-storage-integration.md)