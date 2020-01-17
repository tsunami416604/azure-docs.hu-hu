---
title: Adatmásolás és átalakítás Azure Data Lake Storage Gen2
description: Megtudhatja, hogyan másolhat adatok Azure Data Lake Storage Gen2ba és onnan, és hogyan alakíthatja át a Azure Data Lake Storage Gen2 adatait Azure Data Factory használatával.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/16/2020
ms.openlocfilehash: 7845e381c5a8851683edf6b955d40070bd4e0c30
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2020
ms.locfileid: "76122270"
---
# <a name="copy-and-transform-data-in-azure-data-lake-storage-gen2-using-azure-data-factory"></a>Azure Data Lake Storage Gen2 adatmásolása és átalakítása a Azure Data Factory használatával

A Azure Data Lake Storage Gen2 (ADLS Gen2) az [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md)-ba beépített Big Data-elemzésekhez rendelt képességek összessége. Az adataihoz a fájlrendszer és az objektum tárolási paradigma segítségével is felhasználhatja a felületet.

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok másolásához és a Azure Data Lake Storage Gen2ához, valamint az adatfolyamatok használata az adatok Azure Data Lake Storage Gen2ban való átalakításához. A Azure Data Factoryről a [bevezető cikkben](introduction.md)olvashat bővebben.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez az Azure Data Lake Storage Gen2-összekötő a következő tevékenységek esetén támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Adatfolyam hozzárendelése](concepts-data-flow-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)
- [Tevékenység törlése](delete-activity.md)

Másolási tevékenység esetén az alábbi összekötővel rendelkezhet:

- Adatok másolása a-ből/-ból Azure Data Lake Storage Gen2 a fiók kulcsa, a szolgáltatásnév vagy a felügyelt identitások használatával az Azure-erőforrások hitelesítéséhez.
- Másolja a fájlokat a-ként, vagy elemezze vagy készítsen fájlokat a [támogatott fájlformátumokkal és tömörítési kodekekkel](supported-file-formats-and-compression-codecs.md).
- A [fájl metaadatainak megőrzése a másolás során](#preserve-metadata-during-copy).
- [ACL](#preserve-metadata-during-copy) -ek megőrzése Azure Data Lake Storage Gen1 másolásakor.

>[!IMPORTANT]
>Ha engedélyezi a **megbízható Microsoft-szolgáltatások számára a Storage-fiók elérésének engedélyezése** beállítást az Azure Storage-tűzfal beállításainál, és az Azure Integration Runtime használatával szeretne csatlakozni a Data Lake Storage Gen2hoz, akkor a ADLS Gen2hoz [felügyelt identitás-hitelesítést](#managed-identity) kell használnia.

>[!TIP]
>Ha engedélyezi a hierarchikus névteret, a blob és a Data Lake Storage Gen2 API-k között jelenleg nem áll rendelkezésre együttműködési képesség. Ha a "ErrorCode = FilesystemNotFound" hibaüzenet jelenik meg a "a megadott fájlrendszer nem létezik" üzenettel, azt a rendszer a blob API-n keresztül létrehozott megadott fogadó fájlrendszer okozta, máshol nem Data Lake Storage Gen2 API-t. A probléma megoldásához adjon meg egy olyan nevű új fájlrendszert, amely nem létezik blob-tároló neveként. Ezután Data Factory automatikusan létrehozza az adott fájlrendszert az Adatmásolás során.

## <a name="get-started"></a>Az első lépések

>[!TIP]
>Az Data Lake Storage Gen2-összekötő használatáról a következő témakörben olvashat: az [adatAzure Data Lake Storage Gen2ba való betöltés](load-azure-data-lake-storage-gen2.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban információt talál a Data Lake Storage Gen2ra vonatkozó Data Factory entitások definiálásához használt tulajdonságokról.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Az Azure Data Lake Storage Gen2-összekötő a következő hitelesítési típusokat támogatja. A részletekért tekintse meg a megfelelő részeket:

- [Fiók kulcsának hitelesítése](#account-key-authentication)
- [Egyszerű szolgáltatás hitelesítése](#service-principal-authentication)
- [Felügyelt identitások az Azure-erőforrások hitelesítéséhez](#managed-identity)

>[!NOTE]
>Ha az adatok SQL Data Warehouseba való betöltéséhez a forrás-Data Lake Storage Gen2 Virtual Network végponttal van konfigurálva, akkor a következőt kell használnia a felügyelt identitások hitelesítéséhez:. Tekintse meg a [felügyelt identitás hitelesítése](#managed-identity) szakaszt további konfigurálási előfeltételekkel.

### <a name="account-key-authentication"></a>Fiók kulcsának hitelesítése

A Storage-fiók kulcsos hitelesítésének használatához a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A Type tulajdonságot **AzureBlobFS**értékre kell beállítani. |Igen |
| url | Az Data Lake Storage Gen2 végpontja `https://<accountname>.dfs.core.windows.net`mintázatával. | Igen |
| accountKey | Data Lake Storage Gen2hoz tartozó fiók kulcsa. Megjelöli ezt a mezőt SecureString, hogy biztonságosan tárolja Data Factoryban, vagy [hivatkozjon a Azure Key Vault tárolt titkos kulcsra](store-credentials-in-key-vault.md). |Igen |
| Connectvia tulajdonsággal | Az adattárhoz való csatlakozáshoz használt [integrációs](concepts-integration-runtime.md) modul. Az Azure Integration Runtime vagy egy saját üzemeltetésű integrációs modult is használhat, ha az adattár egy magánhálózaton található. Ha ez a tulajdonság nincs megadva, a rendszer az alapértelmezett Azure Integration Runtime-t használja. |Nem |

>[!NOTE]
>A másodlagos ADLS-fájlrendszer végpontja nem támogatott a fiók kulcsos hitelesítésének használatakor. Más hitelesítési típusokat is használhat.

**Példa**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
            "accountkey": { 
                "type": "SecureString", 
                "value": "<accountkey>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="service-principal-authentication"></a>Egyszerű szolgáltatásnév hitelesítése

Az egyszerű szolgáltatás hitelesítésének használatához kövesse az alábbi lépéseket.

1. Az alkalmazás [regisztrálása Azure ad-Bérlővel](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant)című cikk lépéseit követve regisztrálja az alkalmazás entitásait Azure Active Directoryban (Azure ad). Jegyezze fel a következő értékeket, amelyeket a társított szolgáltatás definiálásához használ:

    - Alkalmazásazonosító
    - Alkalmazás kulcsa
    - Bérlőazonosító

2. Adja meg a szolgáltatásnév megfelelő engedélyét. A [fájlok és könyvtárak hozzáférés-vezérlési listájáról](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories) Data Lake Storage Gen2 az engedélyek működésével kapcsolatos példák című részben.

    - **Forrásként**: Storage Explorerban legalább **végrehajtási** engedélyt kell adnia az összes felsőbb rétegbeli mappához és a fájlrendszerhez, valamint **olvasási** engedéllyel kell rendelkeznie a másolandó fájlokhoz. Másik lehetőségként a hozzáférés-vezérlés (IAM) mezőben adja meg legalább a **Storage blob-Adatolvasó** szerepkört.
    - **Fogadóként:** Storage Explorerban legalább **végrehajtási** engedélyt kell adnia az összes felsőbb rétegbeli mappához és a fájlrendszerhez, valamint **írási** engedéllyel a fogadó mappához. Másik lehetőségként a hozzáférés-vezérlés (IAM) mezőben adja meg legalább a **Storage blob adatközreműködői** szerepkört.

>[!NOTE]
>Ha Data Factory felhasználói felületet használ a létrehozáshoz, és az egyszerű szolgáltatás nem a "Storage blob-Adatolvasó/közreműködői" szerepkörrel van beállítva a IAM-ban, akkor a kapcsolódás tesztelése **vagy a mappák** tallózása/navigálása során válassza a "kapcsolódás a fájl elérési útjához" vagy a "Tallózás a megadott elérési úttal" lehetőséget, és adjon meg egy elérési utat a folytatáshoz.

A társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A Type tulajdonságot **AzureBlobFS**értékre kell beállítani. |Igen |
| url | Az Data Lake Storage Gen2 végpontja `https://<accountname>.dfs.core.windows.net`mintázatával. | Igen |
| servicePrincipalId | Határozza meg az alkalmazás ügyfél-AZONOSÍTÓját. | Igen |
| servicePrincipalKey | Az alkalmazás kulcsának meghatározása. A mező megjelölése `SecureString`ként, hogy biztonságosan tárolja azt a Data Factoryban. Vagy [hivatkozhat a Azure Key Vaultban tárolt titkos kulcsra](store-credentials-in-key-vault.md)is. | Igen |
| Bérlő | Adja meg a bérlői adatokat (tartománynevet vagy bérlői azonosítót), amely alatt az alkalmazás található. Lekéri a Azure Portal jobb felső sarkában lévő egér fölé. | Igen |
| Connectvia tulajdonsággal | Az adattárhoz való csatlakozáshoz használt [integrációs](concepts-integration-runtime.md) modul. Az Azure Integration Runtime vagy egy saját üzemeltetésű integrációs modult is használhat, ha az adattár egy magánhálózaton található. Ha nincs megadva, a rendszer az alapértelmezett Azure Integration Runtime-t használja. |Nem |

**Példa**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>" 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identity"></a>Felügyelt identitások az Azure-erőforrások hitelesítéséhez

Az adatok előállítója [felügyelt identitással társítható az Azure-erőforrásokhoz](data-factory-service-identity.md), amely az adott adatelőállítót képviseli. Ezt a felügyelt identitást közvetlenül használhatja Data Lake Storage Gen2 hitelesítéshez, hasonlóan a saját egyszerű szolgáltatásnév használatához is. Lehetővé teszi a kijelölt gyár számára az adatok elérését és másolását a Data Lake Storage Gen2.

Az Azure erőforrás-hitelesítéshez használt felügyelt identitások használatához kövesse az alábbi lépéseket.

1. [Kérje le a Data Factory felügyelt identitás adatait](data-factory-service-identity.md#retrieve-managed-identity) úgy, hogy átmásolja a **felügyelt identitás objektum azonosítóját** a gyári értékkel együtt.

2. Adja meg a felügyelt identitás megfelelő engedélyét. A [fájlok és könyvtárak hozzáférés-vezérlési listájáról](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)a Data Lake Storage Gen2ban talál példákat az engedélyek működéséről.

    - **Forrásként**: Storage Explorerban legalább **végrehajtási** engedélyt kell adnia az összes felsőbb rétegbeli mappához és a fájlrendszerhez, valamint **olvasási** engedéllyel kell rendelkeznie a másolandó fájlokhoz. Másik lehetőségként a hozzáférés-vezérlés (IAM) mezőben adja meg legalább a **Storage blob-Adatolvasó** szerepkört.
    - **Fogadóként:** Storage Explorerban legalább **végrehajtási** engedélyt kell adnia az összes felsőbb rétegbeli mappához és a fájlrendszerhez, valamint **írási** engedéllyel a fogadó mappához. Másik lehetőségként a hozzáférés-vezérlés (IAM) mezőben adja meg legalább a **Storage blob adatközreműködői** szerepkört.

>[!NOTE]
>Ha Data Factory felhasználói felületet használ a létrehozáshoz, és a felügyelt identitás nincs beállítva a "Storage blob-Adatolvasó/közreműködői" szerepkörrel a IAM-ban, akkor a kapcsolódás vagy a Tallózás/Navigálás mappák között válassza a "kapcsolódás a fájl elérési útjához" vagy a "Tallózás a megadott elérési úttal" lehetőséget, és adjon meg egy elérési utat **olvasási + végrehajtás** engedéllyel.

>[!IMPORTANT]
>Ha a Data Lake Storage Gen2ba való betöltését a SQL Data Warehouseba, a Data Lake Storage Gen2 felügyelt identitás hitelesítésének használatakor az [útmutató](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage) 1. és 2. lépését is végrehajtja, akkor a SQL Database-kiszolgáló Azure Active Directory (Azure ad) és 2) regisztrálásával adja hozzá a Storage blob adatközreműködői szerepkört a SQL Database-kiszolgálóhoz; a REST-et a Data Factory kezeli. Ha a Data Lake Storage Gen2 egy Azure Virtual Network-végponttal van konfigurálva, akkor az adatoknak a használatával történő betöltéséhez használja a felügyelt identitás-hitelesítést.

A társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A Type tulajdonságot **AzureBlobFS**értékre kell beállítani. |Igen |
| url | Az Data Lake Storage Gen2 végpontja `https://<accountname>.dfs.core.windows.net`mintázatával. | Igen |
| Connectvia tulajdonsággal | Az adattárhoz való csatlakozáshoz használt [integrációs](concepts-integration-runtime.md) modul. Az Azure Integration Runtime vagy egy saját üzemeltetésű integrációs modult is használhat, ha az adattár egy magánhálózaton található. Ha nincs megadva, a rendszer az alapértelmezett Azure Integration Runtime-t használja. |Nem |

**Példa**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját lásd: [adatkészletek](concepts-datasets-linked-services.md).

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

A következő tulajdonságok támogatottak Data Lake Storage Gen2 a Format-alapú adatkészlet `location` beállításai területen:

| Tulajdonság   | Leírás                                                  | Szükséges |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Az adatkészletben lévő `location` alatt található Type tulajdonságot **AzureBlobFSLocation**értékre kell állítani. | Igen      |
| Fájlrendszer | A Data Lake Storage Gen2 fájlrendszer neve.                              | Nem       |
| folderPath | Az adott fájlrendszerben található mappa elérési útja. Ha a mappák szűréséhez helyettesítő karaktert szeretne használni, hagyja ki ezt a beállítást, és határozza meg a tevékenység forrásának beállításai között. | Nem       |
| fileName   | A fájl neve a megadott fileSystem + folderPath. Ha helyettesítő karaktert szeretne használni a fájlok szűréséhez, hagyja ki ezt a beállítást, és határozza meg a tevékenység forrásának beállításai között. | Nem       |

**Példa**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Data Lake Storage Gen2 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobFSLocation",
                "fileSystem": "filesystemname",
                "folderPath": "folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját a [másolási tevékenységek konfigurációi](copy-activity-overview.md#configuration) és a [folyamatok és tevékenységek](concepts-pipelines-activities.md)című részben találja. Ez a szakasz a Data Lake Storage Gen2 forrás és a fogadó által támogatott tulajdonságok listáját tartalmazza.

### <a name="azure-data-lake-storage-gen2-as-a-source-type"></a>Azure Data Lake Storage Gen2 a forrás típusaként

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

A következő tulajdonságok támogatottak Data Lake Storage Gen2 a Format-alapú másolási forrás `storeSettings` beállításai területen:

| Tulajdonság                 | Leírás                                                  | Szükséges                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | A `storeSettings` alatti Type tulajdonságot **AzureBlobFSReadSettings**értékre kell állítani. | Igen                                           |
| rekurzív                | Azt jelzi, hogy az adatok rekurzív módon olvashatók-e az almappákból, vagy csak a megadott mappából. Ha a rekurzív értéke TRUE (igaz), a fogadó pedig egy fájl alapú tároló, akkor a fogadó nem másol vagy hoz létre üres mappát vagy almappát. Az engedélyezett értékek: **true** (alapértelmezett) és **false (hamis**). | Nem                                            |
| wildcardFolderPath       | A mappa elérési útja a forrás mappák szűréséhez az adatkészletben konfigurált megadott fájlrendszerben helyettesítő karakterekkel. <br>Az engedélyezett helyettesítő karakterek `*` (nulla vagy több karakternek felelnek meg) és `?` (a nulla vagy egy karakternek felel meg). A `^` használatával elkerülheti, hogy a tényleges mappanév tartalmaz-e helyettesítő karaktert, vagy a menekülési karakter belsejében. <br>További példákat a [mappák és a fájlok szűrésére szolgáló példákban](#folder-and-file-filter-examples)talál. | Nem                                            |
| wildcardFileName         | A forrásfájl szűréséhez a megadott fájlrendszer + folderPath/wildcardFolderPath helyettesítő karakterekkel rendelkező fájlnév. <br>Az engedélyezett helyettesítő karakterek `*` (nulla vagy több karakternek felelnek meg) és `?` (a nulla vagy egy karakternek felel meg). A `^` használatával elkerülheti, hogy a tényleges mappanév tartalmaz-e helyettesítő karaktert, vagy a menekülési karakter belsejében. További példákat a [mappák és a fájlok szűrésére szolgáló példákban](#folder-and-file-filter-examples)talál. | Igen, ha `fileName` nincs megadva az adatkészletben |
| modifiedDatetimeStart    | A fájlok szűrése a legutóbb módosított attribútum alapján. A fájlok akkor vannak kiválasztva, ha az utolsó módosítás ideje a `modifiedDatetimeStart` és `modifiedDatetimeEnd`közötti időtartományon belül van. Az időpontot az UTC időzónára alkalmazza a "2018-12-01T05:00:00Z" formátumban. <br> A tulajdonságok lehetnek NULL értékűek, ami azt jelenti, hogy a rendszer nem alkalmazza a file Attribute szűrőt az adatkészletre. Ha `modifiedDatetimeStart` datetime értékkel rendelkezik, de `modifiedDatetimeEnd` NULL értékű, az azt jelenti, hogy azok a fájlok vannak kiválasztva, amelyek utolsó módosítási attribútuma nagyobb vagy egyenlő, mint a DateTime érték. Ha `modifiedDatetimeEnd` datetime értékkel rendelkezik, de `modifiedDatetimeStart` NULL értékű, az azt jelenti, hogy azok a fájlok vannak kiválasztva, amelyek utolsó módosítási attribútuma kisebb a DateTime értéknél. | Nem                                            |
| modifiedDatetimeEnd      | Ugyanaz, mint a fenti.                                               | Nem                                            |
| maxConcurrentConnections | A tárolási tárolóhoz való kapcsolódáshoz szükséges kapcsolatok száma egyidejűleg. Csak akkor kell megadni, ha az egyidejű kapcsolódást szeretné korlátozni az adattárral. | Nem                                            |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AzureBlobFSReadSettings",
                    "recursive": true,
                    "wildcardFolderPath": "myfolder*A",
                    "wildcardFileName": "*.csv"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-data-lake-storage-gen2-as-a-sink-type"></a>Azure Data Lake Storage Gen2 fogadó típusként

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

A következő tulajdonságok támogatottak Data Lake Storage Gen2 a Format-alapú másolási fogadó `storeSettings` beállításai területen:

| Tulajdonság                 | Leírás                                                  | Szükséges |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | A `storeSettings` alatti Type tulajdonságot **AzureBlobFSWriteSettings**értékre kell állítani. | Igen      |
| copyBehavior             | Meghatározza a másolási viselkedést, ha a forrás fájl-alapú adattárból származó fájlok.<br/><br/>Az engedélyezett értékek a következők:<br/><b>-PreserveHierarchy (alapértelmezett)</b>: megőrzi a fájl-hierarchiát a célmappában. A forrásfájl relatív elérési útja a forrás mappájához azonos a célfájl relatív elérési útjával.<br/><b>-FlattenHierarchy</b>: a forrás mappából származó összes fájl a célmappa első szintjén van. A célként megadott fájlok automatikusan generált névvel rendelkeznek. <br/><b>-MergeFiles</b>: az összes fájlt egyesíti a forrás mappájából egy fájlba. Ha meg van adva a fájl neve, az egyesített fájl neve a megadott név. Ellenkező esetben ez egy automatikusan létrehozott fájl neve. | Nem       |
| maxConcurrentConnections | Az adattárhoz való kapcsolódáshoz szükséges kapcsolatok száma egyszerre. Csak akkor kell megadni, ha az egyidejű kapcsolódást szeretné korlátozni az adattárral. | Nem       |

**Példa**

```json
"activities":[
    {
        "name": "CopyToADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Parquet output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "ParquetSink",
                "storeSettings":{
                    "type": "AzureBlobFSWriteSettings",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Példák a mappák és a fájlok szűrésére

Ez a szakasz a mappa elérési útjának és fájlnevének a helyettesítő karakteres szűrőkkel való viselkedését írja le.

| folderPath | fileName | rekurzív | A forrás mappa szerkezete és a szűrő eredménye (a **félkövérrel szedett** fájlok beolvasása)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (Üres, alapértelmezett használata) | hamis | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**file1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**fájl2. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;fájl3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*` | (Üres, alapértelmezett használata) | igaz | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**file1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**fájl2. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**fájl3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*` | `*.csv` | hamis | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**file1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;fájl3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*` | `*.csv` | igaz | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**file1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**fájl3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |

### <a name="some-recursive-and-copybehavior-examples"></a>Néhány rekurzív és copyBehavior példa

Ez a szakasz a rekurzív és copyBehavior értékek különböző kombinációinak másolási műveletének eredményét írja le.

| rekurzív | copyBehavior | Forrás mappa szerkezete | Eredményül kapott cél |
|:--- |:--- |:--- |:--- |
| igaz |preserveHierarchy | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A célként megadott Mappa1 ugyanazzal a struktúrával jön létre, mint a forrás:<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| igaz |flattenHierarchy | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A célként megadott Mappa1 a következő szerkezettel jön létre: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan generált neve a file1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan generált neve a Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan generált neve a fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan generált neve a File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan generált neve a File5 |
| igaz |mergeFiles | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A célként megadott Mappa1 a következő szerkezettel jön létre: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1 + Fájl2 + fájl3 + File4 + File5 tartalma egyetlen fájlba van egyesítve egy automatikusan létrehozott fájlnévvel. |
| hamis |preserveHierarchy | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A célként megadott Mappa1 a következő szerkezettel jön létre: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/><br/>A fájl3, a File4 és a File5 Subfolder1 nem kerül felvételre. |
| hamis |flattenHierarchy | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A célként megadott Mappa1 a következő szerkezettel jön létre: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan generált neve a file1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan generált neve a Fájl2<br/><br/>A fájl3, a File4 és a File5 Subfolder1 nem kerül felvételre. |
| hamis |mergeFiles | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A célként megadott Mappa1 a következő szerkezettel jön létre: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1 + Fájl2 tartalom egyetlen fájlba van egyesítve, és automatikusan létrehozott fájlnevet tartalmaz. a file1 automatikusan generált neve<br/><br/>A fájl3, a File4 és a File5 Subfolder1 nem kerül felvételre. |

## <a name="preserve-metadata-during-copy"></a>Metaadatok megőrzése a másolás során

Ha fájlokat másol az Amazon S3/Azure Blob/Azure Data Lake Storage Gen2ról Azure Data Lake Storage Gen2/Azure Blobba, megtarthatja a fájl metaadatainak megőrzését az adatokkal együtt. További információ: a [metaadatok megőrzése](copy-activity-preserve-metadata.md#preserve-metadata).

## <a name="preserve-acls-from-data-lake-storage-gen1"></a>ACL-ek megőrzése Data Lake Storage Gen1

>[!TIP]
>Az adatok Azure Data Lake Storage Gen1ból a Gen2-be való másolásához lásd: [adatok másolása Azure Data Lake Storage Gen1ról a Gen2](load-azure-data-lake-storage-gen2-from-gen1.md) -be a Azure Data Factory használatával a lépésenkénti és ajánlott eljárásokhoz.

Ha Azure Data Lake Storage Gen1ról másol fájlokat a Gen2, megadhatja, hogy a POSIX hozzáférés-vezérlési listákat (ACL-eket) az adatokkal együtt őrizze meg. További információ: [ACL-ek megőrzése Data Lake Storage Gen1ról Gen2](copy-activity-preserve-metadata.md#preserve-acls).

## <a name="mapping-data-flow-properties"></a>Adatfolyam-tulajdonságok leképezése

A leképezési adatfolyamban lévő adatok átalakításakor a Azure Data Lake Storage Gen2 JSON-, Avro-, tagolt szöveg-vagy parketta-formátumú fájlokat is olvashat és írhat. További információ: a [forrás-átalakítás](data-flow-source.md) és a fogadó [transzformációja](data-flow-sink.md) a leképezési adatfolyam funkcióban.

### <a name="source-transformation"></a>Forrás-átalakítás

A forrás-átalakításban egy tárolóból, mappából vagy különálló fájlból is elolvasható Azure Data Lake Storage Gen2. A **forrás beállításai** lapon kezelheti a fájlok olvasásának módját. 

![Forrás beállításai](media/data-flow/sourceOptions1.png "Forrás beállításai")

**Helyettesítő karakter elérési útja:** A helyettesítő karakterek használatakor a rendszer az ADF-et az egyes egyező mappákon és fájlokon keresztül, egyetlen forrás-átalakításon keresztüli hurokra utasítja. Ez egy hatékony módszer több fájl feldolgozására egyetlen folyamaton belül. Több helyettesítő karakterből álló mintázatot is hozzáadhat, ha a meglévő helyettesítő karakter fölé viszi a kurzort.

A forrás tárolóban válasszon egy olyan fájlt, amely megfelel a mintának. Az adatkészletben csak tároló adható meg. A helyettesítő karakteres elérési útnak ezért a gyökérmappa elérési útját is tartalmaznia kell.

Helyettesítő karakteres példák:

* ```*``` a karakterek tetszőleges halmazát jelöli
* a ```**``` rekurzív címtár-beágyazást jelöl
* ```?``` egy karaktert cserél le
* ```[]``` a zárójelben szereplő további karakterek egyikének felel meg

* ```/data/sales/**/*.csv``` lekéri az összes CSV-fájlt a/Data/Sales alatt
* ```/data/sales/20??/**``` beolvassa az összes fájlt a 20. században
* ```/data/sales/2004/*/12/[XY]1?.csv``` beolvassa az összes CSV-fájlt a 2004-as verzióban, amely egy kétjegyű számú X vagy Y előtaggal kezdődik.

**Partíció gyökerének elérési útja:** Ha a forrásban particionált mappák vannak ```key=value``` formátumban (például Year = 2019), akkor a partíciós mappa legfelső szintjét hozzárendelheti az adatfolyami adatfolyamban lévő oszlop neveként.

Először állítson be egy helyettesítő karaktert, amely tartalmazza az összes olyan elérési utat, amely a particionált mappák és az elolvasni kívánt levél fájlok.

![Partíciós forrásfájl beállításai](media/data-flow/partfile2.png "Partíciós fájl beállítása")

A partíció gyökerének elérési útja beállítással határozhatja meg, hogy a mappa szerkezete milyen felső szintű legyen. Amikor az adatelőnézet használatával tekinti meg az adatai tartalmát, látni fogja, hogy az ADF hozzáadja az egyes mappák szintjein talált feloldott partíciókat.

![Partíció gyökerének elérési útja](media/data-flow/partfile1.png "Partíciós gyökér elérési útjának előnézete")

**Fájlok listája:** Ez egy beállított fájl. Hozzon létre egy szövegfájlt, amely tartalmazza a feldolgozandó relatív elérési út fájljainak listáját. Mutasson erre a szövegfájlra.

**A fájl nevét tároló oszlop:** Tárolja a forrásfájl nevét az adataiban található oszlopban. Itt adjon meg egy új oszlopnevet a fájlnév karakterláncának tárolásához.

**Befejezés után:** Ha az adatfolyam futtatása után semmit nem kíván végrehajtani a forrásfájlban, törölje a forrásfájlt, vagy helyezze át a forrásfájlt. Az áthelyezés elérési útjai relatívak.

Ha a forrásfájlokat másik helyre szeretné áthelyezni a feldolgozás után, először válassza a "áthelyezés" lehetőséget a fájl művelethez. Ezután állítsa be a "from" könyvtárat. Ha nem használ helyettesítő karaktereket az elérési úthoz, akkor a "from" beállítás lesz a forrás mappájával megegyező mappa.

Ha a forrás elérési útja helyettesítő karakterrel rendelkezik, a szintaxis az alábbihoz hasonlóan fog kinézni:

```/data/sales/20??/**/*.csv```

A "from" lehetőséget adhatja meg

```/data/sales```

És "to"

```/backup/priorSales```

Ebben az esetben a rendszer a/Data/Sales alatt forrásozott összes fájlt áthelyezi a/backup/priorSales.

> [!NOTE]
> A fájl műveletei csak akkor futnak, amikor egy folyamaton belül indítja el az adatfolyamatot (a folyamat hibakeresési vagy végrehajtási futtatása), amely az adatfolyamok végrehajtása tevékenységet használja egy folyamaton belül. A fájl műveletei *nem* futnak adatfolyam-hibakeresési módban.

**Szűrés legutóbb módosítva:** A feldolgozás alatt álló fájlokat szűrheti, ha az utolsó módosítás dátumát megadja. Az összes dátum-idő UTC. 

### <a name="sink-properties"></a>Fogadó tulajdonságai

A fogadó átalakításban megírhatja Azure Data Lake Storage Gen2 tárolóba vagy mappájába. a **Beállítások** lapon kezelheti a fájlok írásának módját.

![fogadó beállításai](media/data-flow/file-sink-settings.png "fogadó beállításai")

**Törölje a mappát:** Meghatározza, hogy a célmappa törlődik-e az adatírás előtt.

**Fájlnév beállítás:** Meghatározza, hogy a célfájl hogyan legyen elnevezve a célmappában. A fájlnév beállításai a következők:
   * **Default (alapértelmezett**): a Spark használatával a fájlok a részek alapértelmezett értékei alapján megtalálhatók.
   * **Minta**: adjon meg egy mintát, amely egy partíción lévő kimeneti fájlokat sorolja fel. Például a **hitelek [n]. csv** hozza létre a loans1. csv, a loans2. csv fájlt stb.
   * **/Partíció**: adjon meg egy fájlnevet partícióként.
   * **Oszlopbeli adatként**: állítsa a kimeneti fájlt egy oszlop értékére. Az elérési út az adatkészlet-tárolóhoz viszonyítva nem a célmappához képest.
   * **Kimenet egyetlen fájlba**: a particionált kimeneti fájlok egyetlen elnevezett fájlba kombinálhatók. Az elérési út az adatkészlet mappájához képest relatív. Vegye figyelembe, hogy a saját egyesítési művelete valószínűleg meghiúsulhat a csomópont méretétől függően. Ez a beállítás nagyméretű adatkészletek esetén nem ajánlott.

**Összes idézet:** Meghatározza, hogy az idézőjelek közé kell-e foglalni az összes értéket

## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [keresési tevékenységet](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>GetMetadata tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [GetMetaData tevékenységet](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>Tevékenység tulajdonságainak törlése

A tulajdonságok részleteinek megismeréséhez tekintse meg a [tevékenység törlése](delete-activity.md) lehetőséget.

## <a name="legacy-models"></a>Örökölt modellek

>[!NOTE]
>A következő modellek továbbra is támogatottak a visszamenőleges kompatibilitás érdekében. Azt javasoljuk, hogy használja a fenti szakaszban említett új modellt, és az ADF authoring felhasználói felülete átvált az új modell generálására.

### <a name="legacy-dataset-model"></a>Örökölt adatkészlet-modell

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | Az adatkészlet Type tulajdonságát **AzureBlobFSFile**értékre kell állítani. |Igen |
| folderPath | A Data Lake Storage Gen2 mappájának elérési útja. Ha nincs megadva, a gyökérre mutat. <br/><br/>A helyettesítő karakteres szűrő használata támogatott. Az engedélyezett helyettesítő karakterek `*` (nulla vagy több karakternek felelnek meg) és `?` (a nulla vagy egy karakternek felel meg). A `^` segítségével elkerülheti, ha a tényleges mappanév helyettesítő karakterrel rendelkezik, vagy a menekülési karakter belül van. <br/><br/>Példák: fájlrendszer/mappa/. További példákat a [mappák és a fájlok szűrésére szolgáló példákban](#folder-and-file-filter-examples)talál. |Nem |
| fileName | A megadott "folderPath" alatt lévő fájlok neve vagy helyettesítő karakteres szűrője. Ha nem ad meg értéket ehhez a tulajdonsághoz, az adatkészlet a mappában található összes fájlra mutat. <br/><br/>A Filter (szűrő) esetében az engedélyezett helyettesítő karakterek `*` (a nulla vagy több karakternek felelnek meg) és a `?` (a nulla vagy egy karakternek felel meg).<br/>– 1. példa: `"fileName": "*.csv"`<br/>– 2. példa: `"fileName": "???20180427.txt"`<br/>Ha a tényleges fájlnév helyettesítő karakterrel rendelkezik, vagy a escape-karakter be van zárva, használja a `^`.<br/><br/>Ha nincs megadva fájlnév a kimeneti adatkészlethez, és a **preserveHierarchy** nincs megadva a tevékenység fogadójában, a másolási tevékenység automatikusan létrehozza a fájlnevet a következő mintával: "*adat. [ tevékenység futtatási azonosítója GUID]. [GUID if FlattenHierarchy]. [formátum, ha konfigurálva]. [tömörítés, ha be van állítva]* ", például" az "adat. 0a405f8a-93ff-4c6f-B3BE-f69616f1df7a. txt. gz". Ha egy táblázatos forrásból másol egy lekérdezés helyett egy tábla nevét, a név minta a következő: " *[Table Name]. [ Format]. [tömörítés, ha konfigurálva]* ", például" sajáttábla. csv ". |Nem |
| modifiedDatetimeStart | A fájlok szűrése a legutóbb módosított attribútum alapján. A fájlok akkor vannak kiválasztva, ha az utolsó módosítás ideje a `modifiedDatetimeStart` és `modifiedDatetimeEnd`közötti időtartományon belül van. Az időpontot az UTC időzónára alkalmazza a "2018-12-01T05:00:00Z" formátumban. <br/><br/> Ha ezt a beállítást szeretné elvégezni, nagy mennyiségű fájl használata esetén a rendszer az adatáthelyezés általános teljesítményét érinti. <br/><br/> A tulajdonságok lehetnek NULL értékűek, ami azt jelenti, hogy a rendszer nem alkalmazza a file Attribute szűrőt az adatkészletre. Ha `modifiedDatetimeStart` datetime értékkel rendelkezik, de `modifiedDatetimeEnd` NULL értékű, akkor azok a fájlok vannak kiválasztva, amelyek utolsó módosítási attribútuma nagyobb vagy egyenlő, mint a DateTime érték. Ha `modifiedDatetimeEnd` datetime értékkel rendelkezik, de `modifiedDatetimeStart` NULL értékű, akkor azok a fájlok vannak kiválasztva, amelyek utolsó módosítási attribútuma kisebb a DateTime értéknél.| Nem |
| modifiedDatetimeEnd | A fájlok szűrése a legutóbb módosított attribútum alapján. A fájlok akkor vannak kiválasztva, ha az utolsó módosítás ideje a `modifiedDatetimeStart` és `modifiedDatetimeEnd`közötti időtartományon belül van. Az időpontot az UTC időzónára alkalmazza a "2018-12-01T05:00:00Z" formátumban. <br/><br/> Ha ezt a beállítást szeretné elvégezni, nagy mennyiségű fájl használata esetén a rendszer az adatáthelyezés általános teljesítményét érinti. <br/><br/> A tulajdonságok lehetnek NULL értékűek, ami azt jelenti, hogy a rendszer nem alkalmazza a file Attribute szűrőt az adatkészletre. Ha `modifiedDatetimeStart` datetime értékkel rendelkezik, de `modifiedDatetimeEnd` NULL értékű, akkor azok a fájlok vannak kiválasztva, amelyek utolsó módosítási attribútuma nagyobb vagy egyenlő, mint a DateTime érték. Ha `modifiedDatetimeEnd` datetime értékkel rendelkezik, de `modifiedDatetimeStart` NULL értékű, akkor azok a fájlok vannak kiválasztva, amelyek utolsó módosítási attribútuma kisebb a DateTime értéknél.| Nem |
| formátum | Ha fájlokat szeretne másolni a fájl alapú tárolók között (bináris másolás), ugorja át a formátum szakaszt a bemeneti és a kimeneti adatkészlet-definíciókban is.<br/><br/>Ha a fájlokat egy adott formátummal szeretné elemezni vagy előállítani, a következő fájlformátum-típusok támogatottak: **Szövegformátum**, **JsonFormat**, **AvroFormat**, **OrcFormat**és **ParquetFormat**. A **Type (típus** ) tulajdonságot állítsa a **Format** értékre a következő értékek egyikére. További információ: [szöveg formátuma](supported-file-formats-and-compression-codecs-legacy.md#text-format), JSON- [Formátum](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Avro formátum](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [ork-formátum](supported-file-formats-and-compression-codecs-legacy.md#orc-format)és [parketta formátuma](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) . |Nem (csak bináris másolási forgatókönyv esetén) |
| tömörítés | Adja meg az adattömörítés típusát és szintjét. További információ: [támogatott fájlformátumok és tömörítési kodekek](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>A támogatott típusok a **gzip**, a **deflate**, a **BZip2**és a **ZipDeflate**.<br/>A támogatott szintek **optimálisak** és **leggyorsabbak**. |Nem |

>[!TIP]
>Egy mappa összes fájljának másolásához csak a **folderPath** kell megadni.<br>Egy adott névvel rendelkező egyetlen fájl másolásához adja meg a **folderPath** mappát, és nevezze el a **fájlnevet** .<br>Ha egy mappában lévő fájlok egy részhalmazát szeretné másolni, akkor a **folderPath** és a **fájlnevet** egy helyettesítő szűrővel kell megadnia. 

**Példa**

```json
{
    "name": "ADLSGen2Dataset",
    "properties": {
        "type": "AzureBlobFSFile",
        "linkedServiceName": {
            "referenceName": "<Azure Data Lake Storage Gen2 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "myfilesystem/myfolder",
            "fileName": "*",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

### <a name="legacy-copy-activity-source-model"></a>Örökölt másolási tevékenység forrásának modellje

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának Type tulajdonságát **AzureBlobFSSource**értékre kell állítani. |Igen |
| rekurzív | Azt jelzi, hogy az adatok rekurzív módon olvashatók-e az almappákból, vagy csak a megadott mappából. Ha a rekurzív értéke TRUE (igaz), a fogadó pedig egy fájl alapú tároló, akkor a fogadó nem másol vagy hoz létre üres mappát vagy almappát.<br/>Az engedélyezett értékek: **true** (alapértelmezett) és **false (hamis**). | Nem |
| maxConcurrentConnections | Az adattárhoz való kapcsolódáshoz szükséges kapcsolatok száma egyszerre. Csak akkor kell megadni, ha az egyidejű kapcsolódást szeretné korlátozni az adattárral. | Nem |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ADLS Gen2 input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "AzureBlobFSSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="legacy-copy-activity-sink-model"></a>Örökölt másolási tevékenység fogadó modellje

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység fogadójának Type tulajdonságát **AzureBlobFSSink**értékre kell állítani. |Igen |
| copyBehavior | Meghatározza a másolási viselkedést, ha a forrás fájl-alapú adattárból származó fájlok.<br/><br/>Az engedélyezett értékek a következők:<br/><b>-PreserveHierarchy (alapértelmezett)</b>: megőrzi a fájl-hierarchiát a célmappában. A forrásfájl relatív elérési útja a forrás mappájához azonos a célfájl relatív elérési útjával.<br/><b>-FlattenHierarchy</b>: a forrás mappából származó összes fájl a célmappa első szintjén van. A célként megadott fájlok automatikusan generált névvel rendelkeznek. <br/><b>-MergeFiles</b>: az összes fájlt egyesíti a forrás mappájából egy fájlba. Ha meg van adva a fájl neve, az egyesített fájl neve a megadott név. Ellenkező esetben ez egy automatikusan létrehozott fájl neve. | Nem |
| maxConcurrentConnections | Az adattárhoz való kapcsolódáshoz szükséges kapcsolatok száma egyszerre. Csak akkor kell megadni, ha az egyidejű kapcsolódást szeretné korlátozni az adattárral. | Nem |

**Példa**

```json
"activities":[
    {
        "name": "CopyToADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ADLS Gen2 output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureBlobFSSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

## <a name="next-steps"></a>Következő lépések

A Data Factory a másolási tevékenység által forrásként és nyelőként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
