---
title: "Az Azure Search Azure Blob Storage indexelése"
description: "Útmutató: Azure Blob Storage indexelése és az Azure Search dokumentumok szöveg kinyerése"
services: search
documentationcenter: 
author: chaosrealm
manager: pablocas
editor: 
ms.assetid: 2a5968f4-6768-4e16-84d0-8b995592f36a
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 07/22/2017
ms.author: eugenesh
ms.openlocfilehash: 97c1fc602ba27472fed2f11fd634e617ae9c636f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="indexing-documents-in-azure-blob-storage-with-azure-search"></a>Az Azure Blob Storage tárolóban az Azure Search dokumentumok indexelő
Ez a cikk bemutatja, hogyan használható az Azure Search index dokumentumok (például PDF-fájlok, a Microsoft Office-dokumentumok, és számos egyéb gyakori formátumok) az Azure Blob Storage tárolóban tárolja. Első lépésként beállítása és konfigurálása a blob indexelő használatának alapjait ismerteti. Ezt követően viselkedésmódok mélyebb feltárása kínál, és forgatókönyvek lehetséges hibát.

## <a name="supported-document-formats"></a>A dokumentum formátumokat támogatja
A blob indexelő lehet szöveg kinyerése a következő dokumentum formátumok:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="setting-up-blob-indexing"></a>Blobindexelés beállítása
Beállíthat egy Azure Blob Storage indexelő használatával:

* [Azure Portal](https://ms.portal.azure.com)
* Az Azure Search [REST API-n](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)
* Az Azure Search [.NET SDK-val](https://aka.ms/search-sdk)

> [!NOTE]
> Egyes szolgáltatások (például mező leképezéseket) még nem állnak rendelkezésre a portálon, és programozott módon kell használni.
>
>

Itt bemutatjuk, a folyamat a REST API használatával.

### <a name="step-1-create-a-data-source"></a>1. lépés: Adatforrás létrehozása
Egy adatforrás indexelésre, az adatokat, és a házirendek hatékony azonosításához az adatoknak (új, módosított vagy törölt sorai) eléréséhez szükséges hitelesítő adatok megadása Egy adatforrás több indexelő ugyanazt a keresési szolgáltatást használhatják.

Az adatforrás blobindexelés, a következő kötelező tulajdonságok kell rendelkeznie:

* **név** az adatforrás a keresőszolgáltatása belül egyedi neve.
* **típus** kell `azureblob`.
* **hitelesítő adatok** nyújt a tárolási fiók kapcsolati karakterláncot, mint a `credentials.connectionString` paraméter. Lásd: [hitelesítő adatok megadása](#Credentials) alábbi részleteket.
* **tároló** határozza meg a tárfiók egy tárolót. Alapértelmezés szerint valamennyi BLOB a tárolóban található lekérhető. Ha szeretné index blobot, amely egy adott virtuális könyvtár, megadhatja a nem kötelező könyvtárhoz **lekérdezés** paraméter.

Adatforrás létrehozása:

    POST https://[service name].search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
    }   

Az adatforrás létrehozása API bővebben lásd: [adatforrás létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-data-source).

<a name="Credentials"></a>
#### <a name="how-to-specify-credentials"></a>Hitelesítő adatok megadása ####

Megadhatja a hitelesítő adatokat a blob-tároló az alábbi módszerek valamelyikével:

- **Teljes hozzáférés tárolási fiók kapcsolati karakterlánc**: `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>`. Letölthető a kapcsolati karakterláncot az Azure-portálon nyissa meg a storage-fiók panelen > Beállítások > kulcsok (a klasszikus tárfiókokkal) vagy a beállítások > hozzáférési kulcsokkal (az Azure Resource Manager storage-fiókok).
- **Tárolási fiók közös hozzáférésű jogosultságkódot** (SAS) kapcsolódási karakterlánc: `BlobEndpoint=https://<your account>.blob.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=b&sp=rl` a SAS van a listája, és meg kell-e olvasási engedéllyel a tárolók és objektumok (ebben az esetben blobok).
-  **Tároló közös hozzáférésű jogosultságkódot**: `ContainerSharedAccessUri=https://<your storage account>.blob.core.windows.net/<container name>?sv=2016-05-31&sr=c&sig=<the signature>&se=<the validity end time>&sp=rl` a SAS van a listája, és meg kell-e olvasási engedéllyel a tárolóra.

További információt a tárhelyen megosztott hozzáférési aláírásokkal, lásd: [megosztott hozzáférési aláírásokkal használatával](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
> Ha SAS-hitelesítő adatokat használ, akkor az adatforrás hitelesítő adatainak frissítése rendszeresen megújított aláírásokkal, hogy megakadályozza a lejárati idejük. Ha SAS-hitelesítőadatait lejár, az indexelő hasonló hibaüzenettel meghiúsul `Credentials provided in the connection string are invalid or have expired.`.  

### <a name="step-2-create-an-index"></a>2. lépés: Index létrehozása
Az index a dokumentumban, attribútumok, megadja azokat a mezőket, és más, a keresés alakul szerkezetek tapasztalnak.

Hozzon létre egy kereshető indexet a következőképpen `content` mező a szöveg kibontani a blobok tárolására:   

    POST https://[service name].search.windows.net/indexes?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }

Indexek létrehozásával kapcsolatban bővebben lásd: [Index létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-index)

### <a name="step-3-create-an-indexer"></a>3. lépés:, Hozzon létre egy indexelőt
Az indexelő csatlakoznak az adatforrás egy cél search-index, és automatizálhatja az adatfrissítési ütemezés biztosít.

Az index és az adatforrás létrehozása után készen áll az indexelő létrehozásához:

    POST https://[service name].search.windows.net/indexers?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "blob-indexer",
      "dataSourceName" : "blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

Az indexelő minden két órában (ütemezési időköz értéke "PT2H") fog futni. Az indexelő 30 percenként "PT30M" intervallum be. A legrövidebb támogatott időköz értéke 5 perc. Az ütemezés nem kötelező – Ha nincs megadva, az indexelő futása csak egyszer, amikor létrejön. Azonban bármikor indexelő igény szerinti is futtathatja.   

Hozzon létre indexelő API további részletekért tekintse meg [létrehozása indexelő](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

## <a name="how-azure-search-indexes-blobs"></a>Hogyan indexeli az Azure Search a blobok

Attól függően a [indexelő konfigurációs](#PartsOfBlobToIndex), a blob indexelő indexelheti csak a tárolási metaadatok (akkor hasznos, ha csak az Ön számára legfontosabb a metaadatok, és nem szükséges a BLOB tartalmát indexelésre), tárolási és metaadatok, vagy egyaránt metaadatait és tartalmát szöveges tartalmat. Alapértelmezés szerint az indexelő metaadatok és a tartalmat bontja ki.

> [!NOTE]
> Alapértelmezés szerint blobok JSON vagy a fürt megosztott kötetei szolgáltatás például tartalmazó strukturált indexeli, egyetlen adattömb szöveg. Ha azt szeretné, blobok JSON és a fürt megosztott kötetei szolgáltatás indexelésre strukturált módon, lásd: [indexelő JSON-blobok](search-howto-index-json-blobs.md) és [indexelő CSV-blobok](search-howto-index-csv-blobs.md) az előzetes funkciók.
>
> Egy összetett vagy beágyazott dokumentum (például ZIP-archívum létrehozása, vagy egy Word-dokumentumot beágyazott Outlook e-mail mellékleteket tartalmazó) is indexelt egyetlen-dokumentumként.

* A dokumentum a szöveges tartalom kibontása nevű mezőnek `content`.

> [!NOTE]
> Az Azure Search korlátozza, hogy milyen mértékű szöveg bontja ki az árképzési szint függően: 32000 karakterek szabad réteg, 64 000 az alapszintű és a 4 millió Standard, a Standard S2 és a Standard S3 rétege számára. Az indexelő állapotválasz csonkolt dokumentumokra vonatkozó figyelmeztetés tartalmazza.  

* A blob megtalálható a felhasználó által megadott metaadat-tulajdonságainak bármilyen kibontása pontosan.
* Standard blob metaadat-tulajdonságainak ki kell olvasni a következő mezőibe:

  * **metaadatok\_tárolási\_neve** (Edm.String) – a blob fájlnevét. Például ha egy blob /my-container/my-folder/subfolder/resume.pdf, ez a mező értéke `resume.pdf`.
  * **metaadatok\_tárolási\_elérési** (Edm.String) – a teljes URI-azonosítója a blob, beleértve a tárfiók. Például: `https://myaccount.blob.core.windows.net/my-container/my-folder/subfolder/resume.pdf`
  * **metaadatok\_tárolási\_tartalom\_típus** (Edm.String) – a blob feltöltése használt kód által megadott tartalomtípus. Például: `application/octet-stream`.
  * **metaadatok\_tárolási\_utolsó\_módosított** (Edm.DateTimeOffset) - utolsó módosítás a BLOB időbélyegző. Az Azure Search az időbélyegző módosított blobok elkerüléséhez követően a kezdeti indexelő újraindexelés mindent azonosítására használ.
  * **metaadatok\_tárolási\_mérete** (Edm.Int64) - blob mérete bájtban.
  * **metaadatok\_tárolási\_tartalom\_md5** (Edm.String) - MD5 kivonatoló a blobtartalom, ha elérhető.
* Minden egyes dokumentum formátuma jellemző metaadat-tulajdonságainak ki kell olvasni a mezőkbe felsorolt [Itt](#ContentSpecificMetadata).

Nem kell mezők a fenti tulajdonságokat a search-index – csak rögzíteni kell az alkalmazás tulajdonságait.

> [!NOTE]
> A meglévő index a mezőnevek gyakran, a dokumentum kibontási során mezőnevek eltérő lesz. Használhat **hozzárendelések mezőben** megfeleltetni az Azure Search a mezőnevek az search-index által biztosított nevei. Látni fogja a mező hozzárendelések alábbi példát.
>
>

<a name="DocumentKeys"></a>
### <a name="defining-document-keys-and-field-mappings"></a>Dokumentum kulcsok és a mezők leképezésének meghatározása
Az Azure Search a dokumentum kulcs egyedileg azonosít egy dokumentumot. Minden keresési index Edm.String típusú pontosan egy kulcsmező kell rendelkeznie. A kulcs mező kitöltése kötelező, minden egyes dokumentumhoz, hogy az index (ténylegesen az egyetlen mező) lett hozzáadva.  

Alaposan gondolja át melyik kibontott mezőt az index a következő kulcsmező kell hozzárendelését. A deduplikációra kijelölt a következők:

* **metaadatok\_tárolási\_neve** – Ez lehet hasznos jelöltként, de vegye figyelembe, hogy 1.) a nevek nem feltétlenül egyedi, előfordulhat, hogy blobok azonos nevű másik mappa, és a 2.) a neve, a dokumentum kulcsok, például kötőjelek érvénytelen karaktereket tartalmazhat. Akkor is foglalkozik érvénytelen karakterek használatával a `base64Encode` [leképezési függvény mezőben](search-indexer-field-mappings.md#base64EncodeFunction) – Ha így tesz, ne felejtse el a dokumentum kulcsok kódolására, amikor például a keresési meghívja átadja őket az API-ban. (Például a .NET használhatja a [UrlTokenEncode metódus](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) erre a célra).
* **metaadatok\_tárolási\_elérési** – a teljes elérési útja a biztosítja a egyediségi, de az elérési út mindenképpen tartalmaz `/` karakterek, amelyek [egy dokumentum kulcs érvénytelen](https://docs.microsoft.com/rest/api/searchservice/naming-rules).  A fenti, lehetősége van a kulcsok használatával kódolási a `base64Encode` [függvény](search-indexer-field-mappings.md#base64EncodeFunction).
* Ha a fenti lehetőségek egyike sem tudja alkalmazni, a blobok is hozzáadhat egy egyéni metaadat-tulajdonságnak. Ezt a beállítást, azonban szükséges a blob feltöltési folyamat adott metaadat-tulajdonságnak hozzáadása az összes BLOB. Mivel a kulcsot kötelező tulajdonság, összes BLOB, amelyek nem rendelkeznek az adott tulajdonsághoz indexelése sikertelen lesz.

> [!IMPORTANT]
> Ha az index a következő kulcsmező nem explicit leképezést, automatikusan használja-e Azure Search `metadata_storage_path` a kulcs és a base-64 kódolja kulcsértékei (a második lehetőség fenti).
>
>

Ehhez a példához most válassza ki a `metadata_storage_name` mezőt dokumentumkulcsként. Tételezzük is fel, hogy az index nevű kulcs mezőt tartalmaz `key` és mező `fileSize` a dokumentum méretének tárolásához. Hozzá kell fűznie a folyamatot a kívánt módon működjenek, adja meg a következő mező megfeleltetéseket létrehozásakor vagy frissítésekor az indexelő:

    "fieldMappings" : [
      { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
      { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
    ]

Ennek érdekében minden együtt, és ez hogyan hozzáadhat mező leképezések és base 64 kódolás kulcsok a meglévő indexelőt engedélyezése:

    PUT https://[service name].search.windows.net/indexers/blob-indexer?api-version=2016-09-01
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
> Mező hozzárendelések kapcsolatos további információkért lásd: [Ez a cikk](search-indexer-field-mappings.md).
>
>

<a name="WhichBlobsAreIndexed"></a>
## <a name="controlling-which-blobs-are-indexed"></a>Mely blobok indexelt vezérlése
Szabályozhatja, hogy mely blobok indexelt, és amely kimarad.

### <a name="index-only-the-blobs-with-specific-file-extensions"></a>Csak a meghatározott fájlnév-kiterjesztésű blobok index
Csak a fájlnév-kiterjesztések használatával adja meg a blobok indexelheti a `indexedFileNameExtensions` indexelő konfigurációs paraméter. Az érték a kívánt fájlkiterjesztéseket (a kezdő pont) vesszővel tagolt listáját tartalmazó karakterlánc. Például, hogy az index csak a. PDF és. Blobok DOCX, tegye a következőket:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
    }

### <a name="exclude-blobs-with-specific-file-extensions"></a>Blobok meghatározott fájlnév-kiterjesztésű kizárása
Kizárhat meghatározott fájlnév-kiterjesztések blobok használatával indexelésének a `excludedFileNameExtensions` konfigurációs paraméter. Az érték a kívánt fájlkiterjesztéseket (a kezdő pont) vesszővel tagolt listáját tartalmazó karakterlánc. Például, hogy az összes blobot, kivéve az index a. PNG és. JPEG-bővítményeket, tegye a következőket:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
    }

Ha mindkét `indexedFileNameExtensions` és `excludedFileNameExtensions` paraméterek szerepelnek, az Azure Search először ellenőrzi, hogy az `indexedFileNameExtensions`, majd a `excludedFileNameExtensions`. Ez azt jelenti, hogy ha ugyanazon fájl kiterjesztése könyvtárban található, akkor nem kerülnek bele az indexelő.

### <a name="dealing-with-unsupported-content-types"></a>Nem támogatott tartalomtípus kezelésével

Alapértelmezés szerint a blob indexelője, amint fordul egy blobot a tartalom nem támogatott típust (például egy képet). Természetesen használhatja a `excludedFileNameExtensions` paraméter kihagyását bizonyos tartalomtípusokat. Azonban szükség lehet index blobok minden lehetséges tartalomtípusokat előre ismerete nélkül. Folytatni, amikor a rendszer észlelt egy nem támogatott tartalomtípus, állítsa be a `failOnUnsupportedContentType` konfigurációs paramétert `false`:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false } }
    }

### <a name="ignoring-parsing-errors"></a>A rendszer figyelmen kívül hagyja az elemzési hibák

Az Azure Search dokumentum kibontási logika nem tökéletes megoldás, ezért néha nem támogatott tartalomtípus dokumentumok például elemzésére. DOCX vagy. PDF-FÁJLT. Ha nem szeretné, hogy zavaró, ebben az esetben indexelő használatához állítsa be a `maxFailedItems` és `maxFailedItemsPerBatch` néhány ésszerű értéket konfigurációs paramétereket. Példa:

    {
      ... other parts of indexer definition
      "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
    }

<a name="PartsOfBlobToIndex"></a>
## <a name="controlling-which-parts-of-the-blob-are-indexed"></a>A blob részeket indexelt vezérlése

A blobok részeket indexelt segítségével szabályozhatja a `dataToExtract` konfigurációs paraméter. Ez a következő értékeket veheti:

* `storageMetadata`-határozza meg, hogy csak a [szabványos blob tulajdonságait és a felhasználó által megadott metaadatok](../storage/blobs/storage-properties-metadata.md) indexelt.
* `allMetadata`-határozza meg, hogy tárolási metaadatai és a [tartalomtípus adott metaadatokat](#ContentSpecificMetadata) kibontott tartalmat indexelt a blobból.
* `contentAndMetadata`-határozza meg, hogy minden metaadat és a blob kinyert szöveges tartalom indexelt. Ez az alapértelmezett érték.

Például csak a tárolási metaadatok indexelése, használja:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "dataToExtract" : "storageMetadata" } }
    }

### <a name="using-blob-metadata-to-control-how-blobs-are-indexed"></a>A blob metaadatai használatával határozzák meg, hogyan blobok

A fenti konfigurációs paraméterek összes BLOB vonatkozik. Egyes esetekben előfordulhat, hogy szeretne szabályozni hogyan *egyes blobok* indexelt. Ehhez adja hozzá a következő blob metaadatai tulajdonságok és értékek:

| Tulajdonság neve | Tulajdonság értéke | Magyarázat |
| --- | --- | --- |
| AzureSearch_Skip |"true" |Arra utasítja a blob indexelő teljesen kihagyja a blob. Sem a metaadatok, sem a tartalom kivonása kísérlet történik. Ez akkor hasznos, ha egy adott blob ismételten meghiúsul, és megszakítja az indexelési folyamat. |
| AzureSearch_SkipContent |"true" |Ez megegyezik a `"dataToExtract" : "allMetadata"` ismertetett beállítás [fent](#PartsOfBlobToIndex) egy adott blob hatókörébe. |

## <a name="incremental-indexing-and-deletion-detection"></a>Növekményes indexelő és törlési észlelése
Beállításakor egy blob indexelő ütemezés szerint futtatni, azt újra indexek csak a módosított blobok a blob alapján `LastModified` időbélyegző.

> [!NOTE]
> Nem kell megadnia a módosítás szabályzat – növekményes indexelő engedélyezve van, automatikusan.

Egy "helyreállítható törlésre" módszert használja, törlésével dokumentumok támogatása érdekében. Ha törli a végleges blobokat, megfelelő dokumentumok nem törlődik a search-index. Ehelyett kövesse az alábbi lépéseket:  

1. Egy egyéni metaadat-tulajdonságnak hozzáadása a blob jelzi az Azure Search logikailag el kell-e hagyni
2. Az adatforrás egy helyreállítható törlési szabályzat konfigurálása
3. Miután az indexelő feldolgozta-e a blobot (eredményobjektumokról az indexelő állapot API), a blob fizikailag törlése

Például a következő házirendet úgy ítéli meg, egy blobot törölve lesz, ha olyan metadata tulajdonsággal rendelkezik `IsDeleted` értékű `true`:

    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2016-09-01
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

## <a name="indexing-large-datasets"></a>Az indexelő nagy adatkészletek

Blobok indexelő időigényes folyamat lehet. A több millió indexelésre blobok esetében esetben felgyorsíthatja az adatok particionálása és az adatok párhuzamos feldolgozásához használt több indexelők indexelő. Itt látható, hogyan állíthat be ezt:

- Az adatok particionálása több blob tárolók vagy virtuális mappák
- Állítson be több Azure keresési adatforrások, egy tárolót vagy mappa egy. A blob mappára mutat, használja a `query` paraméter:

    ```
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" }
    }
    ```

- Hozzon létre egy megfelelő indexelőt minden adatforrás. Az indexelő a azonos cél keresési indexe is mutathat.  

- A szolgáltatás egy keresési egység egy indexelő futtathatja egy adott időpontban. Csak akkor hasznos, ha ténylegesen párhuzamos futtatása több indexelők létrehozása a fent leírt módon. Több indexelők párhuzamosan futó, nem megfelelő számú partíciókat és a replikák létrehozásával méretezése a keresési szolgáltatáshoz. Például ha a keresési szolgáltatás 6 keresési egységek (például 2 partíció x 3 replikák), majd 6 indexelők is futtatható egyidejűleg, egy six-fold az indexelési teljesítmény növekedését eredményezi. Méretezés és a kapacitástervezést kapcsolatos további információkért lásd: [erőforrás szintjeinek lekérdezési és indexelési munkaterhelések az Azure Search méretezése](search-capacity-planning.md).

## <a name="indexing-documents-along-with-related-data"></a>Az indexelő dokumentumok kapcsolódó adatokkal együtt

Érdemes lehet "összeállítása" dokumentumok az indexben több forrásból. Érdemes lehet például más Cosmos DB tárolt metaadatok blobok szöveg egyesíteni. Az indexelő API különböző indexelők együtt leküldéses segítségével még több részeiből dokumentumok keresése kialakításához. 

Ennek működéséhez minden indexelők és más olyan összetevők kell kidolgozni a dokumentum kulcs. Részletes útmutató, tekintse meg a külső cikket: [dokumentumok egyesíthető más adatokat az Azure Search ](http://blog.lytzen.name/2017/01/combine-documents-with-other-data-in.html).

<a name="IndexingPlainText"></a>
## <a name="indexing-plain-text"></a>Az indexelő egyszerű szöveg 

Ha a blobok az azonos kódolással egyszerű szöveges tartalmaz, akkor jelentősen növelheti indexelési teljesítmény használatával **mód elemzése szöveg**. Szöveg elemzésekor mód használatához állítsa a `parsingMode` konfigurációs tulajdonság `text`:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text" } }
    }

Alapértelmezés szerint a `UTF-8` feltételezett kódolását. Egy másik kódolást megadásához használja a `encoding` konfigurációs tulajdonság: 

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text", "encoding" : "windows-1252" } }
    }


<a name="ContentSpecificMetadata"></a>
## <a name="content-type-specific-metadata-properties"></a>Tartalom típusra vonatkozó metaadatokhoz tulajdonságai
Az alábbi táblázat foglalja össze az egyes végzett feldolgozás, és ismerteti az Azure Search által kibontott metaadat-tulajdonságainak.

| Dokumentum formátuma / tartalomtípus | Adott metaadatokat tartalomtípus-tulajdonságok | Feldolgozási részletek |
| --- | --- | --- |
| HTML (`text/html`) |`metadata_content_encoding`<br/>`metadata_content_type`<br/>`metadata_language`<br/>`metadata_description`<br/>`metadata_keywords`<br/>`metadata_title` |Sáv HTML-kódot, és bontsa ki a szöveget |
| PDF (`application/pdf`) |`metadata_content_type`<br/>`metadata_language`<br/>`metadata_author`<br/>`metadata_title` |Bontsa ki a szöveget, beleértve a beágyazott dokumentumok (kivéve a lemezképek) |
| DOCX (application/vnd.openxmlformats-officedocument.wordprocessingml.document) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Bontsa ki a szöveget, beleértve a beágyazott dokumentumok |
| DOC (application/msword) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Bontsa ki a szöveget, beleértve a beágyazott dokumentumok |
| XLSX (application/vnd.openxmlformats-officedocument.spreadsheetml.sheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Bontsa ki a szöveget, beleértve a beágyazott dokumentumok |
| XLS (kérelem/vnd.ms-excel) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Bontsa ki a szöveget, beleértve a beágyazott dokumentumok |
| PPTX (application/vnd.openxmlformats-officedocument.presentationml.presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Bontsa ki a szöveget, beleértve a beágyazott dokumentumok |
| PPT (kérelem/vnd.ms-powerpoint) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Bontsa ki a szöveget, beleértve a beágyazott dokumentumok |
| ÜZENET (kérelem/vnd.ms-outlook) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_message_bcc`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_subject` |Bontsa ki a szöveget, beleértve a mellékletek |
| ZIP (kérelem/zip) |`metadata_content_type` |Az archívumban található összes dokumentum szöveg kinyerése |
| XML (application/xml) |`metadata_content_type`</br>`metadata_content_encoding`</br> |XML-címke sáv és szöveg |
| JSON (application/json) |`metadata_content_type`</br>`metadata_content_encoding` |Szöveg<br/>Megjegyzés: Ha több dokumentum mező kinyerése JSON blob van szüksége, tekintse meg [indexelő JSON-blobok](search-howto-index-json-blobs.md) részletek |
| EML (üzenet/rfc822) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_creation_date`<br/>`metadata_subject` |Bontsa ki a szöveget, beleértve a mellékletek |
| RTF (kérelem/rtf) |`metadata_content_type`</br>`metadata_author`</br>`metadata_character_count`</br>`metadata_creation_date`</br>`metadata_page_count`</br>`metadata_word_count`</br> | Szöveg|
| Egyszerű szöveges (egyszerű szöveg) |`metadata_content_type`</br>`metadata_content_encoding`</br> | Szöveg|


## <a name="help-us-make-azure-search-better"></a>Segítsen az Azure Search továbbfejlesztésében
Ha a szolgáltatás-kérelmek vagy ötleteket javításai, ossza meg velünk a a [UserVoice webhelyén](https://feedback.azure.com/forums/263029-azure-search/).
