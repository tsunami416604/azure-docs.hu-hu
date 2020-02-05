---
title: Keresés az Azure Blob Storage-tartalmakon
titleSuffix: Azure Cognitive Search
description: Megtudhatja, hogyan indexelheti az Azure Blob Storaget, és hogyan kinyerheti a dokumentumokat az Azure Cognitive Search használatával.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 1c2bac06f2526260fb290b63e5aa559a1e2337b4
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77020624"
---
# <a name="how-to-index-documents-in-azure-blob-storage-with-azure-cognitive-search"></a>Dokumentumok indexelése az Azure Blob Storage az Azure-ban Cognitive Search

Ez a cikk bemutatja, hogyan használható az Azure Cognitive Search az Azure Blob Storage-ban tárolt dokumentumok (például PDF-fájlok, Microsoft Office dokumentumok és számos más gyakori formátum) indexeléséhez. Első lépésként ismerteti a blob-indexelő beállításának és konfigurálásának alapjait. Ezt követően mélyebben megismerheti a viselkedéseket és a valószínűleg felmerülő forgatókönyveket.

<a name="SupportedFormats"></a>

## <a name="supported-document-formats"></a>Támogatott dokumentumformátumok
A blob-indexelő a következő dokumentum-formátumokból tud szöveget kinyerni:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="setting-up-blob-indexing"></a>BLOB-indexelés beállítása
Beállíthat egy Azure Blob Storage indexelő a használatával:

* [Azure Portal](https://ms.portal.azure.com)
* Azure Cognitive Search [REST API](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)
* Azure Cognitive Search [.net SDK](https://aka.ms/search-sdk)

> [!NOTE]
> Néhány funkció (például a mezők leképezése) még nem érhető el a portálon, és programozott módon kell használni őket.
>

Itt a REST API használatával mutatjuk be a folyamatot.

### <a name="step-1-create-a-data-source"></a>1\. lépés: Adatforrás létrehozása
Az adatforrás meghatározza az adatokhoz való hozzáféréshez szükséges hitelesítő adatokat, valamint a szabályzatokat az adatok változásainak hatékony azonosításához (új, módosított vagy törölt sorok). Egy adatforrást több indexelő is használhat ugyanabban a keresési szolgáltatásban.

A blob-indexeléshez az adatforrásnak a következő szükséges tulajdonságokkal kell rendelkeznie:

* a **Name** a keresési szolgáltatásban található adatforrás egyedi neve.
* a **típusnak** `azureblob`nak kell lennie.
* a **hitelesítő adatok** a Storage-fiókhoz tartozó kapcsolatok sztringjét `credentials.connectionString` paraméterként biztosítják. A részletekért lásd: az alábbi [hitelesítő adatok megadása](#Credentials) .
* a **Container** egy tárolót határoz meg a Storage-fiókban. Alapértelmezés szerint a tárolóban lévő összes blob beolvasható. Ha a blobokat csak egy adott virtuális könyvtárban szeretné indexelni, megadhatja a könyvtárat a választható **lekérdezési** paraméter használatával.

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

További információ a Create DataSource API-ról: [adatforrás létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-data-source).

<a name="Credentials"></a>
#### <a name="how-to-specify-credentials"></a>Hitelesítő adatok megadása ####

A blob-tároló hitelesítő adatait az alábbi módszerek egyikével adhatja meg:

- **Teljes hozzáférésű Storage-fiók kapcsolati karakterlánca**: `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>` a Azure Portal kapcsolati karakterláncát a Storage-fiók panel > Beállítások > kulcsok (klasszikus Storage-fiókok esetében) vagy a beállítások > hozzáférési kulcsok (Azure Resource Manager Storage-fiókok esetében) lehetőségre kattintva érheti el.
- A **Storage-fiók megosztott hozzáférésének aláírása** (SAS) kapcsolati karakterlánca: `BlobEndpoint=https://<your account>.blob.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=b&sp=rl` az SAS-nek szerepelnie kell a listán, és olvasási engedéllyel kell rendelkeznie a tárolók és objektumok (ebben az esetben Blobok).
-  **Tároló közös hozzáférésének aláírása**: `ContainerSharedAccessUri=https://<your storage account>.blob.core.windows.net/<container name>?sv=2016-05-31&sr=c&sig=<the signature>&se=<the validity end time>&sp=rl` az SAS-nek a tárolóban szerepelnie kell a listával és az olvasási engedéllyel.

További információ a Storage közös hozzáférésű aláírásáról: a [közös hozzáférésű aláírások használata](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
> Ha SAS hitelesítő adatokat használ, az adatforráshoz tartozó hitelesítő adatokat rendszeresen frissítenie kell megújított aláírásokkal a lejárat megakadályozása érdekében. Ha az SAS hitelesítő adatai lejárnak, az indexelő a `Credentials provided in the connection string are invalid or have expired.`hoz hasonló hibaüzenettel fog működni.  

### <a name="step-2-create-an-index"></a>2\. lépés: Index létrehozása
Az index meghatározza a dokumentumok, attribútumok és más, a keresési élményt formáló szerkezetek mezőit.

Az alábbi módon hozhat létre egy kereshető `content` mezőt tartalmazó indexet a blobokból kinyert szöveg tárolásához:   

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

További információk az indexek létrehozásáról: [create index](https://docs.microsoft.com/rest/api/searchservice/create-index)

### <a name="step-3-create-an-indexer"></a>3\. lépés: indexelő létrehozása
Az indexelő összekapcsolja az adatforrást a cél keresési indexszel, és az Adatfrissítés automatizálására szolgáló ütemtervet biztosít.

Miután létrehozta az indexet és az adatforrást, készen áll az indexelő létrehozására:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "blob-indexer",
      "dataSourceName" : "blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

Ez az indexelő két óránként fut (az ütemezett időköz értéke "PT2H"). Az indexelő 30 percenkénti futtatásához állítsa az intervallumot "PT30M" értékre. A legrövidebb támogatott időköz 5 perc. Az ütemterv nem kötelező – ha nincs megadva, az indexelő csak egyszer fut a létrehozáskor. Az indexelő igény szerinti futtatása azonban bármikor elvégezhető.   

Az indexelő API létrehozásával kapcsolatos további információkért tekintse meg az [Indexelő létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-indexer)című leírást.

Az indexelő-ütemtervek definiálásával kapcsolatos további információkért lásd: [Az Azure Cognitive Search indexelő szolgáltatásának beosztása](search-howto-schedule-indexers.md).

<a name="how-azure-search-indexes-blobs"></a>

## <a name="how-azure-cognitive-search-indexes-blobs"></a>Hogyan indexeli a blobokat az Azure Cognitive Search

Az [Indexelő konfigurációjától](#PartsOfBlobToIndex)függően a blob indexelő csak a tárolási metaadatokat tudja indexelni (ez akkor hasznos, ha csak a metaadatokat érdekli, és nem szükséges a Blobok tartalmának indexelése), a tárolási és tartalmi metaadatokat, illetve a metaadatokat és a szöveges tartalmakat is. Alapértelmezés szerint az indexelő kibontja a metaadatokat és a tartalmakat is.

> [!NOTE]
> Alapértelmezés szerint a strukturált tartalmat, például a JSON-t vagy a CSV-t tartalmazó Blobok egyetlen darab szövegként vannak indexelve. Ha a JSON-és CSV-blobokat strukturált módon szeretné indexelni, további információért lásd: [JSON-Blobok indexelése](search-howto-index-json-blobs.md) és [CSV-Blobok indexelése](search-howto-index-csv-blobs.md) .
>
> Az összetett vagy beágyazott dokumentumok (például a ZIP-archívumok vagy a mellékleteket tartalmazó beágyazott Outlook-e-mailes Word-dokumentumok) is egyetlen dokumentumként vannak indexelve.

* A dokumentum szöveges tartalma egy `content`nevű karakterlánc-mezőbe van kibontva.

> [!NOTE]
> Az Azure Cognitive Search az árképzési szinttől függően Kinyeri a szöveg mennyiségét: 32 000 karakter az ingyenes szinthez, 64 000 az alapszintű, 4 000 000 a standard, a 8 000 000 a standard S2 és az 16 000 000 standard S3-hoz. A rendszer figyelmeztetést tartalmaz az indexelő állapotának a csonkolt dokumentumokra adott válaszában.  

* A blobon (ha van ilyen) a felhasználó által megadott metaadat-tulajdonságok szó szerint vannak kibontva. Vegye figyelembe, hogy ehhez az indexben meg kell adni egy olyan mezőt, amelynek a neve megegyezik a blob metaadat-kulcsával. Ha például a blob `High`értékkel rendelkező `Sensitivity` metaadat-kulcsával rendelkezik, akkor a keresési indexben meg kell adnia egy `Sensitivity` nevű mezőt, amely a `High`értékkel lesz feltöltve.
* A Blobok szabványos metaadat-tulajdonságainak kibontása a következő mezőkbe történik:

  * **metaadatok\_storage\_neve** (EDM. String) – a blob fájlneve. Ha például blob-/My-Container/My-Folder/subfolder/Resume.pdf rendelkezik, a mező értéke `resume.pdf`.
  * **metaadatok\_storage\_elérési út** (EDM. String) – a blob teljes URI-ja, beleértve a Storage-fiókot is. Például: `https://myaccount.blob.core.windows.net/my-container/my-folder/subfolder/resume.pdf`
  * **metaadatok\_storage\_tartalom\_típus** (EDM. String) – a blob feltöltéséhez használt kód által megadott tartalomtípus. Például: `application/octet-stream`.
  * **metaadatok\_storage\_utolsó\_módosítva** (EDM. DateTimeOffset) – a blob utolsó módosításának időbélyege. Az Azure Cognitive Search ezt az időbélyeget használja a módosított Blobok azonosításához, hogy ne legyenek újraindexelve a kezdeti indexelés után.
  * **metaadatok\_storage\_méret** (EDM. Int64) – blob mérete bájtban.
  * **metaadatok\_storage\_tartalom\_MD5** (EDM. String) – a blob tartalmának MD5 kivonata, ha van ilyen.
  * **metaadatok\_storage\_sas\_token** (EDM. String) – ideiglenes sas-token, amelyet az [egyéni képességek](cognitive-search-custom-skill-interface.md) használhatnak a blobhoz való hozzáféréshez. Ezt a jogkivonatot nem szabad a későbbi használat céljából tárolni, mivel az lejáró lehet.

* Az egyes dokumentum-formátumokra jellemző metaadat-tulajdonságokat a rendszer kinyeri az [itt](#ContentSpecificMetadata)felsorolt mezőkbe.

Nem kell mezőket meghatároznia a keresési index fenti tulajdonságaihoz – csak rögzítse az alkalmazáshoz szükséges tulajdonságokat.

> [!NOTE]
> Gyakran előfordul, hogy a meglévő index mezőinek neve eltér a dokumentumok kinyerése során generált mezők neveitől. A mező- **hozzárendelések** segítségével leképezheti az Azure Cognitive Search által megadott tulajdonságokat a keresési index mezőinek neveihez. Ekkor megjelenik egy példa a mező-hozzárendelésekre, amelyeket az alábbiakban használ.
>
>

<a name="DocumentKeys"></a>
### <a name="defining-document-keys-and-field-mappings"></a>A dokumentumok kulcsainak és a mezők hozzárendelésének meghatározása
Az Azure Cognitive Search a dokumentum kulcsa egyedileg azonosít egy dokumentumot. Minden keresési indexnek pontosan egy EDM. String típusú Key mezővel kell rendelkeznie. A Key mezőt kötelező megadni az indexhez hozzáadott összes dokumentumhoz (ez valójában az egyetlen kötelező mező).  

Alaposan gondolja át, hogy melyik kibontott mező legyen leképezve az index Key mezőjére. A jelöltek a következők:

* **metaadatok\_storage\_neve** – ez lehet egy kényelmes jelölt, de vegye figyelembe, hogy 1) a nevek nem egyediek, mivel előfordulhat, hogy az azonos nevű Blobok eltérő mappákban vannak, és 2) a név olyan karaktereket tartalmazhat, amelyek érvénytelenek a dokumentum kulcsaiban, például a kötőjeleket. Az érvénytelen karaktereket a `base64Encode` [mező leképezési funkciójával](search-indexer-field-mappings.md#base64EncodeFunction) kezelheti – ha ezt teszi, ne felejtse el kódolni a dokumentum kulcsait az API-hívásokban (például a lookup) való átadáskor. (Például a .NET-ben a [UrlTokenEncode metódust](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) használhatja erre a célra).
* **metaadatok\_storage\_elérési út** – a teljes elérési út használata biztosítja az egyediséget, de az elérési út nem tartalmaz `/` karaktert, amely [érvénytelen a dokumentum kulcsaiban](https://docs.microsoft.com/rest/api/searchservice/naming-rules).  A fentieknek megfelelően lehetősége van a kulcsok kódolására a `base64Encode` [függvény](search-indexer-field-mappings.md#base64EncodeFunction)használatával.
* Ha a fenti lehetőségek egyike sem működik, hozzáadhat egy egyéni metaadat-tulajdonságot a blobokhoz. Ez a beállítás azonban megköveteli a blob feltöltési folyamatát, hogy hozzáadja a metaadat-tulajdonságot az összes blobhoz. Mivel a kulcs egy kötelező tulajdonság, a tulajdonságot nem tartalmazó Blobok nem lesznek indexelve.

> [!IMPORTANT]
> Ha nincs explicit leképezés a kulcs mezőhöz az indexben, az Azure Cognitive Search automatikusan a kulcsot használja `metadata_storage_path`ként, és Base-64 kódolja a kulcs értékeit (a fenti második lehetőség).
>
>

Ebben a példában válassza ki a `metadata_storage_name` mezőt a dokumentum kulcsaként. Tegyük fel, hogy az indexnek van egy `key` nevű főmezője és egy mező `fileSize` a dokumentum méretének tárolásához. A lehető leggyorsabban, a következő mezők-hozzárendeléseket adja meg az indexelő létrehozásakor vagy frissítésekor:

    "fieldMappings" : [
      { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
      { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
    ]

A következő lépésekkel egyesítheti a mezőket, és engedélyezheti a kulcsok Base-64 kódolását egy meglévő indexelő számára:

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
> A mezők hozzárendelésével kapcsolatos további tudnivalókért tekintse meg [ezt a cikket](search-indexer-field-mappings.md).
>
>

<a name="WhichBlobsAreIndexed"></a>
## <a name="controlling-which-blobs-are-indexed"></a>A Blobok indexelésének szabályozása
Megadhatja, hogy mely Blobok indexelve legyenek, és melyeket a rendszer kihagyja.

### <a name="index-only-the-blobs-with-specific-file-extensions"></a>Csak a Blobok indexelése adott fájlkiterjesztések esetén
A `indexedFileNameExtensions` indexelő konfigurációs paraméterrel csak a megadott fájlnévkiterjesztések használatával indexelheti a blobokat. Az érték egy olyan karakterlánc, amely a fájlkiterjesztés vesszővel tagolt listáját tartalmazza (vezető ponttal). Például csak a érték indexeléséhez. PDF és. DOCX Blobok:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
    }

### <a name="exclude-blobs-with-specific-file-extensions"></a>Blobok kizárása adott fájlkiterjesztések esetén
A `excludedFileNameExtensions` konfigurációs paraméter használatával kizárhat olyan blobokat, amelyekben megadott fájlnévkiterjesztéssel rendelkezik az indexelésből. Az érték egy olyan karakterlánc, amely a fájlkiterjesztés vesszővel tagolt listáját tartalmazza (vezető ponttal). Például az összes blob indexeléséhez, kivéve a következővel:. PNG és. JPEG-bővítmények:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
    }

Ha `indexedFileNameExtensions` és `excludedFileNameExtensions` paraméterek is szerepelnek, az Azure Cognitive Search először a `indexedFileNameExtensions`, majd a `excludedFileNameExtensions`címen jelenik meg. Ez azt jelenti, hogy ha ugyanaz a fájlkiterjesztés szerepel mindkét listán, az indexelésből ki lesz zárva.

<a name="PartsOfBlobToIndex"></a>
## <a name="controlling-which-parts-of-the-blob-are-indexed"></a>Annak szabályozása, hogy a blob mely részei legyenek indexelve

Megadhatja, hogy a Blobok mely részei legyenek indexelve a `dataToExtract` konfigurációs paraméter használatával. A következő értékeket veheti fel:

* `storageMetadata` – azt adja meg, hogy csak a [szabványos blob-tulajdonságok és a felhasználó által megadott metaadatok](../storage/blobs/storage-properties-metadata.md) legyenek indexelve.
* `allMetadata` – megadja, hogy a rendszer indexeli a tárolási metaadatokat és a blob-tartalomból kinyert [tartalom típusú specifikus metaadatokat](#ContentSpecificMetadata) .
* `contentAndMetadata` – azt adja meg, hogy a blobból kinyert összes metaadat és szöveges tartalom indexelve legyen. Ez az alapértelmezett érték.

Ha például csak a tárolási metaadatokat szeretné indexelni, használja a következőt:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "dataToExtract" : "storageMetadata" } }
    }

### <a name="using-blob-metadata-to-control-how-blobs-are-indexed"></a>BLOB-metaadatok használata a Blobok indexelésének vezérléséhez

A fent ismertetett konfigurációs paraméterek az összes blobra érvényesek. Előfordulhat, hogy meg szeretné határozni, hogy az *egyes Blobok* hogyan legyenek indexelve. Ezt úgy teheti meg, hogy hozzáadja a következő blob metaadat-tulajdonságokat és-értékeket:

| Tulajdonság neve | Tulajdonság értéke | Magyarázat |
| --- | --- | --- |
| AzureSearch_Skip |igaz |Arra utasítja a blob indexelő, hogy teljesen kihagyja a blobot. Sem a metaadatok, sem a tartalom kibontása nem történt meg. Ez akkor hasznos, ha egy adott blob többször meghiúsul, és megszakítja az indexelési folyamatot. |
| AzureSearch_SkipContent |igaz |Ez egyenértékű a [fent](#PartsOfBlobToIndex) ismertetett `"dataToExtract" : "allMetadata"`-beállítással, amely egy adott blobra terjed ki. |

<a name="DealingWithErrors"></a>
## <a name="dealing-with-errors"></a>Hibák kezelése

Alapértelmezés szerint a blob-indexelő azonnal leáll, ha nem támogatott tartalomtípusú blobot (például egy képet) észlel. Természetesen használhatja a `excludedFileNameExtensions` paramétert bizonyos tartalomtípusok kihagyása érdekében. Előfordulhat azonban, hogy a blobokat a lehetséges tartalomtípusok előzetes ismerete nélkül kell indexelni. Ha nem támogatott tartalomtípust észlel, az indexelés folytatásához állítsa a `failOnUnsupportedContentType` konfigurációs paramétert `false`re:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false } }
    }

Egyes Blobok esetében az Azure Cognitive Search nem tudja meghatározni a tartalomtípust, vagy nem tud feldolgozni egy egyébként támogatott tartalomtípusú dokumentumot. Ha figyelmen kívül hagyja ezt a meghibásodási módot, állítsa false értékre a `failOnUnprocessableDocument` konfigurációs paramétert:

      "parameters" : { "configuration" : { "failOnUnprocessableDocument" : false } }

Az Azure Cognitive Search korlátozza az indexelt Blobok méretét. Ezek a korlátok az [Azure Cognitive Search szolgáltatási korlátaiban](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)vannak dokumentálva. A túlméretezett Blobok alapértelmezés szerint hibákként vannak kezelve. Ha azonban a `indexStorageMetadataOnlyForOversizedDocuments` konfigurációs paraméter értéke TRUE (igaz), akkor továbbra is indexelheti a túlméretezett Blobok tárolási metaadatait: 

    "parameters" : { "configuration" : { "indexStorageMetadataOnlyForOversizedDocuments" : true } }

Folytathatja az indexelést is, ha a hibák bármilyen feldolgozási ponton történnek, vagy a Blobok elemzése vagy a dokumentumok indexbe való felvétele során. Ha egy adott számú hibát szeretne figyelmen kívül hagyni, állítsa a `maxFailedItems` és `maxFailedItemsPerBatch` konfigurációs paramétereket a kívánt értékekre. Példa:

    {
      ... other parts of indexer definition
      "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
    }

## <a name="incremental-indexing-and-deletion-detection"></a>Növekményes indexelés és törlés észlelése
Ha úgy állítja be a blob-indexelő, hogy az egy adott időpontban fusson, akkor a blob `LastModified` timestamp által meghatározott módon csak a módosított blobokat indexeli.

> [!NOTE]
> Nem kell megadnia a változás-észlelési házirendet – a növekményes indexelés automatikusan engedélyezve van.

A dokumentumok törlésének támogatásához használjon "Soft Delete" megközelítést. Ha törli a blobokat, a megfelelő dokumentumokat nem távolítja el a rendszer a keresési indexből. Ehelyett használja a következő lépéseket:  

1. Vegyen fel egy egyéni metaadat-tulajdonságot a blobba, hogy jelezze az Azure Cognitive Search, hogy logikailag törölve van
2. Az adatforrásra vonatkozó törlési észlelési házirend konfigurálása
3. Miután az indexelő feldolgozta a blobot (ahogy azt az indexelő status API is mutatja), fizikailag törölheti a blobot.

Az alábbi szabályzat például egy olyan blobot tekint, amelyet törölni kell, ha a metaadatok tulajdonsága `IsDeleted` a `true`értékkel:

    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2019-05-06
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

## <a name="indexing-large-datasets"></a>Nagyméretű adathalmazok indexelése

Az indexelési Blobok időigényes folyamat lehet. Abban az esetben, ha több millió blobot tartalmaz az indexeléshez, felgyorsíthatja az indexelést, ha particionálja az adatait, és több indexelő használatával dolgozza fel az adatait párhuzamosan. Ezt a következőképpen állíthatja be:

- Az adatai particionálása több blob-tárolóba vagy virtuális mappába
- Állítson be több Azure Cognitive Search adatforrást, egy tárolót vagy egy mappát. Ha egy blob mappára szeretne mutatni, használja a `query` paramétert:

    ```
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" }
    }
    ```

- Hozzon létre egy megfelelő indexelő az egyes adatforrásokhoz. Az összes indexelő képes ugyanarra a cél keresési indexre mutatni.  

- A szolgáltatás egy keresési egysége egy adott időpontban képes futtatni egy indexelő szolgáltatást. Több indexelő létrehozása a fent leírtak szerint csak akkor hasznos, ha ténylegesen párhuzamosan futnak. Több indexelő párhuzamos futtatásához bővítse a keresési szolgáltatást a megfelelő számú partíció és replika létrehozásával. Ha például a Search szolgáltatás 6 keresési egységgel rendelkezik (például 2 partíciós x 3 replika), akkor 6 indexelő is futhat egyszerre, ami az indexelési teljesítmény hat szorzott növekedését eredményezi. A méretezéssel és a kapacitás megtervezésével kapcsolatos további tudnivalókért lásd: [erőforrások szintjeinek méretezése lekérdezési és indexelési feladatokhoz az Azure Cognitive Searchban](search-capacity-planning.md).

## <a name="indexing-documents-along-with-related-data"></a>Dokumentumok indexelése kapcsolódó adatokkal együtt

Előfordulhat, hogy az indexben több forrásból is össze kívánja állítani a dokumentumokat. Előfordulhat például, hogy a Blobok szövegét szeretné egyesíteni a Cosmos DBban tárolt egyéb metaadatokkal. A leküldéses indexelés API-t és a különböző indexelő elemeket is használhatja a keresési dokumentumok több részből való kiépítéséhez. 

Ahhoz, hogy működjön, minden indexelő és más összetevőnek meg kell egyeznie a dokumentum kulcsával. A témakörről további részleteket a [több Azure-adatforrások indexelését](https://docs.microsoft.com/azure/search/tutorial-multiple-data-sources)ismertető cikkben találhat. Részletes útmutatóért tekintse meg ezt a külső cikket: [dokumentumok összevonása az Azure Cognitive Searchban található egyéb információkkal](https://blog.lytzen.name/2017/01/combine-documents-with-other-data-in.html).

<a name="IndexingPlainText"></a>
## <a name="indexing-plain-text"></a>Egyszerű szöveg indexelése 

Ha az összes blob egyszerű szöveget tartalmaz ugyanabban a kódolásban, akkor a **szöveges elemzési mód**használatával jelentősen javíthatja az indexelési teljesítményt. A szöveges elemzési mód használatához állítsa a `parsingMode` Configuration tulajdonságot `text`re:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text" } }
    }

Alapértelmezés szerint a rendszer a `UTF-8` kódolást feltételezi. Másik kódolás megadásához használja a `encoding` Configuration tulajdonságot: 

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text", "encoding" : "windows-1252" } }
    }


<a name="ContentSpecificMetadata"></a>
## <a name="content-type-specific-metadata-properties"></a>Tartalomtípus-specifikus metaadatok tulajdonságai
Az alábbi táblázat összefoglalja az egyes dokumentumok formátumának feldolgozását, valamint ismerteti az Azure Cognitive Search által kinyert metaadatok tulajdonságait.

| Dokumentum formátuma/tartalmának típusa | Tartalomtípus-specifikus metaadatok tulajdonságai | Feldolgozás részletei |
| --- | --- | --- |
| HTML (text/html) |`metadata_content_encoding`<br/>`metadata_content_type`<br/>`metadata_language`<br/>`metadata_description`<br/>`metadata_keywords`<br/>`metadata_title` |HTML-jelölés és szöveg kinyerése |
| PDF (alkalmazás/PDF) |`metadata_content_type`<br/>`metadata_language`<br/>`metadata_author`<br/>`metadata_title` |Szöveg kinyerése, beleértve a beágyazott dokumentumokat is (képek nélkül) |
| DOCX (Application/vnd. openxmlformats-officedocument. WordprocessingML. Document) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Szöveg kinyerése, beleértve a beágyazott dokumentumokat is |
| DOC (alkalmazás/msword) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Szöveg kinyerése, beleértve a beágyazott dokumentumokat is |
| DOCM (Application/vnd. MS-Word. Document. macroenabled. 12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Szöveg kinyerése, beleértve a beágyazott dokumentumokat is |
| WORD XML (Application/vnd. MS-word2006ml) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Szalag XML-kódjának és kinyerésének szövege |
| WORD 2003 XML (Application/vnd. MS-WordML) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date` |Szalag XML-kódjának és kinyerésének szövege |
| XLSX (Application/vnd. openxmlformats-officedocument. SpreadsheetXML. Sheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Szöveg kinyerése, beleértve a beágyazott dokumentumokat is |
| XLS (Application/vnd. MS-Excel) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Szöveg kinyerése, beleértve a beágyazott dokumentumokat is |
| XLSM (Application/vnd. MS-Excel. Sheet. macroenabled. 12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Szöveg kinyerése, beleértve a beágyazott dokumentumokat is |
| PPTX (Application/vnd. openxmlformats-officedocument. presentationml. Presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Szöveg kinyerése, beleértve a beágyazott dokumentumokat is |
| PPT (Application/vnd. MS-PowerPoint) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Szöveg kinyerése, beleértve a beágyazott dokumentumokat is |
| PPTM (Application/vnd. MS-PowerPoint. Presentation. macroenabled. 12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Szöveg kinyerése, beleértve a beágyazott dokumentumokat is |
| MSG (Application/vnd. MS-Outlook) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_from_email`<br/>`metadata_message_to`<br/>`metadata_message_to_email`<br/>`metadata_message_cc`<br/>`metadata_message_cc_email`<br/>`metadata_message_bcc`<br/>`metadata_message_bcc_email`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_subject` |Szöveg kinyerése, beleértve a mellékleteket is. a `metadata_message_to_email`, a `metadata_message_cc_email` és a `metadata_message_bcc_email` karakterlánc-gyűjtemények, a többi mező pedig karakterlánc.|
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


## <a name="help-us-make-azure-cognitive-search-better"></a>Segítsen nekünk, hogy jobban megtegyük az Azure Cognitive Search
Ha a funkciókra vonatkozó kérések vagy ötletek vannak a tökéletesítésekhez, tudassa velünk a [UserVoice webhelyen](https://feedback.azure.com/forums/263029-azure-search/).
