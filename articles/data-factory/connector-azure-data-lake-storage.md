---
title: Adatok másolása és átalakítása az Azure Data Lake Storage Gen2 szolgáltatásban
description: Megtudhatja, hogyan másolhat adatokat az Azure Data Lake Storage Gen2-be és onnan, és hogyan alakíthatja át az adatokat az Azure Data Lake Storage Gen2 szolgáltatásban az Azure Data Factory használatával.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/08/2020
ms.openlocfilehash: a061df302680488377c3006dcef57b818a101d67
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011531"
---
# <a name="copy-and-transform-data-in-azure-data-lake-storage-gen2-using-azure-data-factory"></a>Adatok másolása és átalakítása az Azure Data Lake Storage Gen2 szolgáltatásban az Azure Data Factory használatával

Az Azure Data Lake Storage Gen2 (ADLS Gen2) az Azure [Blob storageba](../storage/blobs/storage-blobs-introduction.md)beépített big data-elemzéshez szükséges képességek összeskészlete. Segítségével kapcsolatba léphet az adatokkal a fájlrendszer és az objektumtárolási paradigmák használatával.

Ez a cikk bemutatja, hogyan használhatja a másolási tevékenység et az Azure Data Factory adatok másolásához és az Azure Data Lake Storage Gen2-be, és az Adatfolyam használatával alakítja át az adatokat az Azure Data Lake Storage Gen2-ben. Az Azure Data Factory ról a [bevezető cikkben](introduction.md)olvashat.

>[!TIP]
>A Data Lake vagy az adattárház áttelepítési forgatókönyv, további információ [az Azure Data Factory segítségével áttelepíteni az adatokat a data lake vagy adattárház az Azure-ba.](data-migration-guidance-overview.md)

## <a name="supported-capabilities"></a>Támogatott képességek

Ez az Azure Data Lake Storage Gen2-összekötő a következő tevékenységek esetén támogatott:

- [Tevékenység másolása](copy-activity-overview.md) [támogatott forrás/fogadó mátrixcal](copy-activity-overview.md)
- [Adatfolyam leképezése](concepts-data-flow-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)
- [Tevékenység törlése](delete-activity.md)

A másolási tevékenységhez ezzel az összekötővel a következőket teheti:

- Adatok másolása az Azure Data Lake Storage Gen2-ből az Azure-erőforrások hitelesítéséhez fiókkulcs, egyszerű szolgáltatás vagy felügyelt identitások használatával.
- Fájlok másolása vagy elemzése vagy [létrehozása támogatott fájlformátumokkal és tömörítési kodekekkel.](supported-file-formats-and-compression-codecs.md)
- [A fájl metaadatainak megőrzése másolás közben](#preserve-metadata-during-copy).
- [Az ACL-ek megőrzése](#preserve-acls) az Azure Data Lake Storage Gen1/Gen2 szolgáltatásból történő másoláskor.

>[!IMPORTANT]
>Ha engedélyezi a **Megbízható Microsoft-szolgáltatások hozzáférését ehhez a tárfiókhoz** az Azure Storage tűzfalbeállításaiban, és az Azure-integrációs futásidejű használatával szeretne csatlakozni a Data Lake Storage Gen2 szolgáltatáshoz, felügyelt [identitás-hitelesítést](#managed-identity) kell használnia az ADLS Gen2 szolgáltatáshoz.


## <a name="get-started"></a>Bevezetés

>[!TIP]
>A Data Lake Storage Gen2-összekötő használatának áttekintése az [Adatok betöltése az Azure Data Lake Storage Gen2 szolgáltatásba](load-azure-data-lake-storage-gen2.md)című témakörben található.

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok a Data Lake Storage Gen2-re jellemző Data Factory-entitások definiálására használt tulajdonságokkal kapcsolatos információkat tartalmaznak.

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai

Az Azure Data Lake Storage Gen2 összekötő a következő hitelesítési típusokat támogatja. A részleteket lásd a megfelelő szakaszokban:

- [Fiókkulcs hitelesítése](#account-key-authentication)
- [Egyszerű szolgáltatás hitelesítése](#service-principal-authentication)
- [Felügyelt identitások az Azure-erőforrások hitelesítéséhez](#managed-identity)

>[!NOTE]
>Ha a PolyBase használatával adatokat tölt be az SQL Data Warehouse-ba, ha a forrás Data Lake Storage Gen2 virtuális hálózati végponttal van konfigurálva, a PolyBase által megkövetelt felügyelt identitás-hitelesítést kell használnia. Tekintse meg a [felügyelt identitás-hitelesítésszakaszt](#managed-identity) további konfigurációs előfeltételekkel.

### <a name="account-key-authentication"></a>Fiókkulcs hitelesítése

A tárfiók kulcsának hitelesítéséhez a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A típustulajdonságot **AzureBlobFS-re**kell állítani. |Igen |
| url | A Data Lake Storage Gen2 végpontja a. `https://<accountname>.dfs.core.windows.net` | Igen |
| fiókkulcs | A Data Lake Storage Gen2 fiókkulcsa. Jelölje meg ezt a mezőt SecureStringként a Data Factory biztonságos tárolásához, vagy [hivatkozzon az Azure Key Vaultban tárolt titkos fájlokra.](store-credentials-in-key-vault.md) |Igen |
| connectVia | Az adattárhoz való csatlakozáshoz használandó [integrációs futásidő.](concepts-integration-runtime.md) Használhatja az Azure-integrációs runtime vagy egy saját üzemeltetésű integrációs runtime, ha az adattár egy magánhálózat. Ha ez a tulajdonság nincs megadva, az alapértelmezett Azure-integrációs futásidejű használatos. |Nem |

>[!NOTE]
>A másodlagos ADLS fájlrendszer végpontja nem támogatott a fiókkulcs-hitelesítés használatakor. Más hitelesítési típusokat is használhat.

**Példa:**

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

Az egyszerű szolgáltatáshitelesítés használatához kövesse az alábbi lépéseket.

1. Regisztráljon egy alkalmazásentitást az Azure Active Directoryban (Azure AD) az [alkalmazás regisztrálása egy Azure AD-bérlővel](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant)című lépés lépéseit követve. Jegyezze fel a következő értékeket, amelyek segítségével definiálja a csatolt szolgáltatást:

    - Alkalmazásazonosító
    - Alkalmazáskulcs
    - Bérlőazonosító

2. Adja meg az egyszerű szolgáltatás megfelelő engedélyét. Példák a Data Lake Storage Gen2 engedélyműködésére [a fájlok és könyvtárak hozzáférés-vezérlési listáiból](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)

    - **Forrásként:** A Tárolókezelőben adjon meg legalább **végrehajtási** engedélyt az összes felsőbb szintű mappára és a fájlrendszerre, valamint **olvasási** engedélyt a másolandó fájlokhoz. Másik lehetőségként a hozzáférés-vezérlés (IAM), legalább a **Storage Blob Data Reader** szerepkört.
    - **Fogadóként**: A Tárolókezelőben adjon meg legalább **végrehajtási** engedélyt az összes felsőbb szintű mappára és a fájlrendszerre, valamint **írási** engedélyt a fogadó mappához. Másik lehetőségként a hozzáférés-vezérlés (IAM), legalább a **Storage Blob Data Contributor** szerepkört.

>[!NOTE]
>Ha a Data Factory felhasználói felületét használja a létrehozáshoz, és az egyszerű szolgáltatás nincs beállítva a "Storage Blob Data Reader/Contributor" szerepkörrel az IAM-ben, a kapcsolat tesztelése vagy a mappák böngészése/navigálása során válassza a "Kapcsolat tesztelése a fájl elérési úthoz" vagy a "Tallózás a megadott elérési útról" lehetőséget, és adjon meg egy exportálási útvonalat **olvasási + végrehajtás** engedéllyel a folytatáshoz.

Ezek a tulajdonságok támogatottak a csatolt szolgáltatás számára:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A típustulajdonságot **AzureBlobFS-re**kell állítani. |Igen |
| url | A Data Lake Storage Gen2 végpontja a. `https://<accountname>.dfs.core.windows.net` | Igen |
| servicePrincipalId | Adja meg az alkalmazás ügyfélazonosítóját. | Igen |
| servicePrincipalKey | Adja meg az alkalmazás kulcsát. Jelölje meg ezt `SecureString` a mezőt a Data Factory biztonságos tárolásához. Vagy [hivatkozhat az Azure Key Vaultban tárolt titkos fájlokra.](store-credentials-in-key-vault.md) | Igen |
| Bérlő | Adja meg a bérlői adatokat (tartománynév vagy bérlőazonosító), amely alatt az alkalmazás található. Az egér egér relai mozgatórészének az Azure Portal jobb felső sarkában való lebegtetésével. | Igen |
| connectVia | Az adattárhoz való csatlakozáshoz használandó [integrációs futásidő.](concepts-integration-runtime.md) Használhatja az Azure-integrációs runtime vagy egy saját üzemeltetésű integrációs runtime, ha az adattár egy magánhálózat. Ha nincs megadva, az alapértelmezett Azure-integrációs futásidejű használatos. |Nem |

**Példa:**

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

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a>Felügyelt identitások az Azure-erőforrások hitelesítéséhez

Az adat-előállító [azure-erőforrások felügyelt identitással](data-factory-service-identity.md)társítható, amely ezt az adott adat-előállítót jelöli. Ezt a felügyelt identitást közvetlenül használhatja a Data Lake Storage Gen2 hitelesítéshez, hasonlóan a saját egyszerű szolgáltatáshoz. Lehetővé teszi, hogy ez a kijelölt gyár adatokat és adatokat másoljon a Data Lake Storage Gen2-hez.

Felügyelt identitások azure-erőforrás-hitelesítéshez, kövesse az alábbi lépéseket.

1. [A Data Factory által felügyelt identitásadatok lekérése](data-factory-service-identity.md#retrieve-managed-identity) a gyárral együtt létrehozott **felügyelt identitásobjektum-azonosító** értékének másolásával.

2. Adja meg a felügyelt identitás megfelelő engedélyt. A [fájlok és könyvtárak hozzáférés-vezérlési listáiból](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)példákat talál arra vonatkozóan, hogyan működik az engedély a Data Lake Storage Gen2 szolgáltatásban.

    - **Forrásként:** A Tárolókezelőben adjon meg legalább **végrehajtási** engedélyt az összes felsőbb szintű mappára és a fájlrendszerre, valamint **olvasási** engedélyt a másolandó fájlokhoz. Másik lehetőségként a hozzáférés-vezérlés (IAM), legalább a **Storage Blob Data Reader** szerepkört.
    - **Fogadóként**: A Tárolókezelőben adjon meg legalább **végrehajtási** engedélyt az összes felsőbb szintű mappára és a fájlrendszerre, valamint **írási** engedélyt a fogadó mappához. Másik lehetőségként a hozzáférés-vezérlés (IAM), legalább a **Storage Blob Data Contributor** szerepkört.

>[!NOTE]
>Ha a Data Factory felhasználói felületét használja a létrehozáshoz, és a felügyelt identitás nincs beállítva a "Storage Blob Data Reader/Contributor" szerepkörrel az IAM-ben, a kapcsolat tesztelése vagy a mappák böngészése/navigálása során válassza a "Kapcsolat tesztelése a fájl elérési útjához" vagy a "Tallózás a megadott elérési útról" lehetőséget, és adjon meg egy **olvasási + végrehajtási** engedéllyel rendelkező elérési utat a folytatáshoz.

>[!IMPORTANT]
>Ha a PolyBase használatával tölti be az adatokat a Data Lake Storage Gen2-ből az SQL Data Warehouse-ba, a Data Lake Storage Gen2 felügyelt identitáshitelesítésének használatakor győződjön meg arról, hogy [az útmutató](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage) 1. a többit a Data Factory kezeli. Ha a Data Lake Storage Gen2 egy Azure virtuális hálózati végponttal van konfigurálva, a PolyBase használatával adatokat kell betölteni belőle, a PolyBase által igényelt felügyelt identitás-hitelesítést kell használnia.

Ezek a tulajdonságok támogatottak a csatolt szolgáltatás számára:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A típustulajdonságot **AzureBlobFS-re**kell állítani. |Igen |
| url | A Data Lake Storage Gen2 végpontja a. `https://<accountname>.dfs.core.windows.net` | Igen |
| connectVia | Az adattárhoz való csatlakozáshoz használandó [integrációs futásidő.](concepts-integration-runtime.md) Használhatja az Azure-integrációs runtime vagy egy saját üzemeltetésű integrációs runtime, ha az adattár egy magánhálózat. Ha nincs megadva, az alapértelmezett Azure-integrációs futásidejű használatos. |Nem |

**Példa:**

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

Az adatkészletek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját az Adatkészletek című [témakörben található.](concepts-datasets-linked-services.md)

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

A data lake storage gen2 a `location` formátumalapú adatkészlet beállításai alatt a következő tulajdonságokat támogatja:

| Tulajdonság   | Leírás                                                  | Kötelező |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Az adatkészlet `location` típustulajdonságát **AzureBlobFSLocation**beállításra kell állítani. | Igen      |
| Fájlrendszer | A Data Lake Storage Gen2 fájlrendszer neve.                              | Nem       |
| folderPath | Az adott fájlrendszer alatti mappa elérési útja. Ha helyettesítő karakterrel szeretné szűrni a mappákat, hagyja ki ezt a beállítást, és adja meg a tevékenységforrás-beállításokban. | Nem       |
| fileName   | A fájlnév a megadott fileSystem + folderPath alatt. Ha helyettesítő karakterrel szeretné szűrni a fájlokat, hagyja ki ezt a beállítást, és adja meg a tevékenységforrás-beállításokban. | Nem       |

**Példa:**

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

A tevékenységek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját a [Tevékenységkonfigurációk](copy-activity-overview.md#configuration) és folyamatok és tevékenységek másolása című témakörben [található.](concepts-pipelines-activities.md) Ez a szakasz a Data Lake Storage Gen2 forrás és fogadó által támogatott tulajdonságok listáját tartalmazza.

### <a name="azure-data-lake-storage-gen2-as-a-source-type"></a>Az Azure Data Lake Storage Gen2 forrástípusként

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

A következő tulajdonságok a Data Lake `storeSettings` Storage Gen2 szolgáltatásban, a formátumalapú másolási forrás beállításaiban támogatottak:

| Tulajdonság                 | Leírás                                                  | Kötelező                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | A típustulajdonságot `storeSettings` az **AzureBlobFSReadSettings**beállításra kell állítani. | Igen                                           |
| Rekurzív                | Azt jelzi, hogy az adatok olvasása rekurzív anameddig az almappákból vagy csak a megadott mappából történik. Ha a rekurzív érték igaz, és a fogadó fájlalapú tároló, a program nem másolja vagy hozza létre az üres mappát vagy almappát a fogadóban. Az engedélyezett értékek **igazak** (alapértelmezettek) és **hamisak.** | Nem                                            |
| helyettesítő mappaelérési út       | Az adatkészletben a forrásmappák szűrésére konfigurált, az adott fájlrendszerben helyettesítő karakterekkel rendelkező mappaelérési út. <br>Az engedélyezett `*` helyettesítő karakterek (nulla vagy `?` több karakternek felelnek meg) és (nulla vagy egy karakternek felelnek meg). Menekülésre használható, `^` ha a tényleges mappanévben helyettesítő karakter szerepel, vagy ez az escape-karakter van benne. <br>További példák a [Mappa- és fájlszűrő-példákban](#folder-and-file-filter-examples)találhatók. | Nem                                            |
| helyettesítő fájlneve         | A forrásfájlok szűréséhez helyettesítő karakterekkel rendelkező fájlnév a megadott fájlrendszerben + folderPath/wildcardFolderPath. <br>Az engedélyezett `*` helyettesítő karakterek (nulla vagy `?` több karakternek felelnek meg) és (nulla vagy egy karakternek felelnek meg). Menekülésre használható, `^` ha a tényleges mappanévben helyettesítő karakter szerepel, vagy ez az escape-karakter van benne. További példák a [Mappa- és fájlszűrő-példákban](#folder-and-file-filter-examples)találhatók. | Igen, `fileName` ha nincs megadva az adatkészletben |
| modifiedDatetimeStart    | A Fájlok szűrője az utolsó módosítás attribútum alapján. A fájlok akkor kerülnek kiválasztásra, ha az `modifiedDatetimeStart` `modifiedDatetimeEnd`utolsó módosítás idáig a között és a közötti időtartományon belül van. Az idő az UTC időzónára a "2018-12-01T05:00:00Z" formátumban kerül alkalmazásra. <br> A tulajdonságok null értékűek lehetnek, ami azt jelenti, hogy az adatkészletre nem alkalmaz nak fájlattribútum-szűrőt. Ha `modifiedDatetimeStart` dátumidő-értékkel `modifiedDatetimeEnd` rendelkezik, de NULL értékű, az azt jelenti, hogy azok a fájlok, amelyek utolsó módosított attribútuma nagyobb vagy egyenlő a datetime értékkel, ki vannak jelölve. Ha `modifiedDatetimeEnd` datetime értékkel `modifiedDatetimeStart` rendelkezik, de NULL értékű, az azt jelenti, hogy azok a fájlok, amelyek utolsó módosított attribútuma kisebb, mint a datetime érték, ki vannak jelölve. | Nem                                            |
| modifiedDatetimeEnd      | Ugyanaz, mint fent.                                               | Nem                                            |
| maxConcurrentConnections | A tárolóhoz egyidejűleg csatlakozó kapcsolatok száma. Csak akkor adja meg, ha korlátozni szeretné az egyidejű kapcsolatot az adattárhoz. | Nem                                            |

**Példa:**

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

### <a name="azure-data-lake-storage-gen2-as-a-sink-type"></a>Azure Data Lake Storage Gen2 fogadótípusként

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

A következő tulajdonságok a Data Lake `storeSettings` Storage Gen2 szolgáltatásban támogatottak a formátumalapú másolási fogadó beállításai között:

| Tulajdonság                 | Leírás                                                  | Kötelező |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | A típustulajdonságot `storeSettings` az **AzureBlobFSWriteSettings**beállításra kell állítani. | Igen      |
| copyBehavior (Másként)             | Azt a másolási viselkedést határozza meg, amikor a forrás fájlalapú adattárból származó fájlok.<br/><br/>Az engedélyezett értékek a következők:<br/><b>- MegőrzéseHierarchy (alapértelmezett)</b>: Megőrzi a fájlhierarchiát a célmappában. A forrásfájl forrásmappához viszonyított elérési útja megegyezik a célfájl nak a célmappához viszonyított elérési útvonalával.<br/><b>- Ahierarcha összeolvasztása</b>: A forrásmappából származó összes fájl a célmappa első szintjén található. A célfájlok nak automatikusan generált neve van. <br/><b>- MergeFiles</b>: Egyesíti az összes fájlt a forrás mappát egy fájlt. Ha a fájlnév meg van adva, az egyesített fájlnév a megadott név. Ellenkező esetben ez egy automatikusan létrehozott fájlnév. | Nem       |
| blockSizeInMB | Adja meg az ADLS Gen2-be történő adatíráshoz használt blokkméretet MB-ban. További információ [a blokkblobokról.](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) <br/>Az engedélyezett érték **4 és 100 MB között**van. <br/>Alapértelmezés szerint az ADF automatikusan meghatározza a blokk méretet a forrástár típusa és az adatok alapján. Az ADLS Gen2 nem bináris másolása esetén az alapértelmezett blokkméret 100 MB, hogy legbőszebb 4,95 TB-os adatokat szolgáljon fel. Előfordulhat, hogy nem optimális, ha az adatok nem nagy, különösen akkor, ha a saját üzemeltetésű integrációs futásidő gyenge hálózattal, ami a működési időtúl- vagy teljesítményprobléma. Explicit módon megadhatja a blokk mérete, míg biztosítja blockSizeInMB * 50000 elég nagy ahhoz, hogy tárolja az adatokat, különben másolási tevékenység futtatása sikertelen lesz. | Nem |
| maxConcurrentConnections | Az adattárhoz egyidejűleg csatlakozó kapcsolatok száma. Csak akkor adja meg, ha korlátozni szeretné az egyidejű kapcsolatot az adattárhoz. | Nem       |

**Példa:**

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

### <a name="folder-and-file-filter-examples"></a>Példák mappa- és fájlszűrőre

Ez a szakasz a mappa elérési útjának és a helyettesítő szűrőkkel rendelkező fájlnévnek a viselkedését ismerteti.

| folderPath | fileName | Rekurzív | A forrásmappa szerkezete és a szűrő eredménye (a **félkövérrel szedett** fájlok beolvasása)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (Üres, használja az alapértelmezett) | hamis | MappaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Fájl1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Fájl2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl5.csv<br/>Egy másikFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl6.csv |
| `Folder*` | (Üres, használja az alapértelmezett) | igaz | MappaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Fájl1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Fájl2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fájl3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fájl4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fájl5.csv**<br/>Egy másikFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl6.csv |
| `Folder*` | `*.csv` | hamis | MappaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Fájl1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl5.csv<br/>Egy másikFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl6.csv |
| `Folder*` | `*.csv` | igaz | MappaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Fájl1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fájl3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fájl5.csv**<br/>Egy másikFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl6.csv |

### <a name="some-recursive-and-copybehavior-examples"></a>Néhány rekurzív és copyBehavior példák

Ez a szakasz a rekurzív és copyBehavior értékek különböző kombinációinak másolási műveletének eredő viselkedését ismerteti.

| Rekurzív | copyBehavior (Másként) | Forrásmappa szerkezete | Eredményként kapott cél |
|:--- |:--- |:--- |:--- |
| igaz |megőrzési hierarchia | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl5 | A célmappa1 ugyanazzal a struktúrával jön létre, mint a forrás:<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl5 |
| igaz |összeolvasztási hierarchia | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl5 | A célmappa1 a következő struktúrával jön létre: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;fájl automatikusan létrehozott neve1<br/>&nbsp;&nbsp;&nbsp;&nbsp;fájl 2 automatikusan létrehozott neve<br/>&nbsp;&nbsp;&nbsp;&nbsp;fájl 3 automatikusan létrehozott neve<br/>&nbsp;&nbsp;&nbsp;&nbsp;fájl 4 automatikusan létrehozott neve<br/>&nbsp;&nbsp;&nbsp;&nbsp;fájl automatikusan létrehozott neve |
| igaz |mergeFiles | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl5 | A célmappa1 a következő struktúrával jön létre: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;A fájl1 + File2 + File3 + File4 + File5 tartalom egyetlen fájlba egyesül, automatikusan generált fájlnévvel. |
| hamis |megőrzési hierarchia | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl5 | A célmappa1 a következő struktúrával jön létre: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/><br/>A File3, File4 és File5 almappát nem veszi fel a rendszer. |
| hamis |összeolvasztási hierarchia | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl5 | A célmappa1 a következő struktúrával jön létre: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;fájl automatikusan létrehozott neve1<br/>&nbsp;&nbsp;&nbsp;&nbsp;fájl 2 automatikusan létrehozott neve<br/><br/>A File3, File4 és File5 almappát nem veszi fel a rendszer. |
| hamis |mergeFiles | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl5 | A célmappa1 a következő struktúrával jön létre: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;A Fájl1 + File2 tartalom egyetlen, automatikusan létrehozott fájlnévvel rendelkező fájlba egyesül. fájl automatikusan létrehozott neve1<br/><br/>A File3, File4 és File5 almappát nem veszi fel a rendszer. |

## <a name="preserve-metadata-during-copy"></a>Metaadatok megőrzése másolás közben

Amikor fájlokat másol az Amazon S3/Azure Blob/Azure Data Lake Storage Gen2-ről az Azure Data Lake Storage Gen2/Azure Blob szolgáltatásba, megőrizheti a fájl metaadatait az adatokkal együtt. További információ: [Metaadatok megőrzése](copy-activity-preserve-metadata.md#preserve-metadata).

## <a name="preserve-acls-from-data-lake-storage-gen1gen2"></a><a name="preserve-acls"></a>A Data Lake Storage Gen1/Gen2 rendszerből származó ACL-ok megőrzése

Amikor fájlokat másol az Azure Data Lake Storage Gen1/Gen2-ről a Gen2-be, megőrizheti a POSIX hozzáférés-vezérlési listákat (ACL-k) az adatokkal együtt. További információ a [Data Lake Storage Gen1/Gen2-től a Gen2-ig című csomag megőrzéséről.](copy-activity-preserve-metadata.md#preserve-acls)

>[!TIP]
>Az Azure Data Lake Storage Gen1-ből a Gen2-be történő adatok másolása általában az [Azure Data Lake Storage Gen1 adatainak másolása a Gen2-be az Azure Data Factory segítségével](load-azure-data-lake-storage-gen2-from-gen1.md) című témakörben található az átjárási és gyakorlati tanácsok értendők.

## <a name="mapping-data-flow-properties"></a>Adatfolyam tulajdonságainak leképezése

Amikor átalakítja az adatokat az adatfolyamban, az Azure Data Lake Storage Gen2-ből json, Avro, Detagolt szöveg vagy Parketta formátumban olvashat és írhat fájlokat. További információ: [forrásátalakítás](data-flow-source.md) és [a fogadó átalakítása](data-flow-sink.md) a leképezési adatfolyam-szolgáltatásban.

### <a name="source-transformation"></a>Forrás átalakítása

A forrásátalakítás, beolvasni egy tároló, mappa vagy egyéni fájl az Azure Data Lake Storage Gen2. A **Forrás beállítások** lapon kezelheti a fájlok olvasásának módját. 

![Forrásbeállítások](media/data-flow/sourceOptions1.png "Forrásbeállítások")

**Helyettesítő karakter elérési útja:** Helyettesítő karakterminta használatával az ADF arra utasítja az ADF-et, hogy minden egyező mappát és fájlt egyetlen forrásátalakítással haladjon át. Ez egy hatékony módja annak, hogy több fájlt dolgozzon fel egyetlen folyamaton belül. Adjon hozzá több helyettesítő karakteregyezési mintát a + jellel, amely akkor jelenik meg, amikor az egérmutatót a meglévő helyettesítő karakterminta fölé viszi.

A forrástárolóból válasszon egy olyan fájlsorozatot, amely megfelel a mintának. Csak tároló adható meg az adatkészletben. A helyettesítő karakter elérési útjának ezért tartalmaznia kell a mappa elérési útját a gyökérmappából.

Példák helyettesítő karakterre:

* ```*```Bármely karakterkészletet jelöl
* ```**```Rekurzív könyvtárbeágyazását jelöli
* ```?```Egy karakter lecserélése
* ```[]```A zárójelben lévő karakterek közül egynek felel meg

* ```/data/sales/**/*.csv```Az összes csv fájl bediákora a /data/sales
* ```/data/sales/20??/**/```Az összes akta a 20.
* ```/data/sales/*/*/*.csv```A csv-fájlok bekerülése két szintre a /data/sales alatt
* ```/data/sales/2004/*/12/[XY]1?.csv```Az összes csv fájl betöltője 2004-ben, x vagy Y előtaggal kezdve egy kétjegyű számmal

**Partíció gyökérelérési útja:** Ha a fájlforrásban ```key=value``` egy formátumú mappa (például year=2019), akkor a partíciómappafa legfelső szintjét hozzárendelheti egy oszlopnévhez az adatfolyam-adatfolyamban.

Először állítson be egy helyettesítő karaktert úgy, hogy tartalmazza az összes olyan elérési utat, amely a particionált mappa, valamint az olvasni kívánt levélfájlokat.

![Partícióforrásfájlbeállításai](media/data-flow/partfile2.png "Partíciófájl beállítása")

A Partíció gyökérelérési útjának beállításával határozhatja meg, hogy mi a mappastruktúra legfelső szintje. Amikor az adatok tartalmát egy adatelőnézeten keresztül tekinti meg, látni fogja, hogy az ADF hozzáadja az egyes mappaszintekben található feloldott partíciókat.

![Partíció gyökérelérési útja](media/data-flow/partfile1.png "Partíció gyökérelérési útjának előnézete")

**Fájlok listája:** Ez egy fájlkészlet. Hozzon létre egy szövegfájlt, amely tartalmazza a feldolgozandó relatív elérési útfájlok listáját. Mutasson erre a szövegfájlra.

**Oszlop a fájlnév tárolásához:** A forrásfájl nevét az adatok egy oszlopában tárolhatja. Itt adjon meg egy új oszlopnevet a fájlnév-karakterlánc tárolásához.

**A befejezés után:** Válassza ki, hogy az adatfolyam futtatása után nem végez semmit a forrásfájllal, törölje a forrásfájlt, vagy helyezze át a forrásfájlt. Az áthelyezés útvonalai relatívak.

Ha a forrásfájlokat egy másik helyre szeretné áthelyezni utófeldolgozás után, először válassza az "Áthelyezés" lehetőséget a fájlművelethez. Ezután állítsa be a "from" könyvtárat. Ha nem használ helyettesítő karaktereket az elérési úthoz, akkor a "kezdő" beállítás ugyanaz a mappa lesz, mint a forrásmappában.

Ha helyettesítő karakterrel rendelkező forráselérési úttal rendelkezik, a szintaxis az alábbi módon fog kinézni:

```/data/sales/20??/**/*.csv```

Megadhatja a "from"

```/data/sales```

És "a", mint

```/backup/priorSales```

Ebben az esetben a /data/sales alatt beszerzett összes fájl a /backup/priorSales könyvtárba kerül.

> [!NOTE]
> A fájlműveletek csak akkor futnak, ha az adatfolyamot egy folyamat-hibakeresésből (folyamathiba-hibakeresésből vagy végrehajtási futtatásból) indítja el, amely az adatfolyam-végrehajtás tevékenységet használja egy folyamatban. A fájlműveletek *nem* futnak adatfolyam-hibakeresési módban.

**Szűrés utolsó módosítás szerint:** Szűrheti a feldolgozandó fájlokat, ha megadja az utolsó módosítás dátumtartományát. Minden dátum-idő UTC-ben van. 

### <a name="sink-properties"></a>Fogadó tulajdonságai

A fogadó átalakítása, írhat egy tárolóba vagy mappába az Azure Data Lake Storage Gen2. a **Beállítások** lapon kezelheti a fájlok írásmódját.

![elfogadó beállításai](media/data-flow/file-sink-settings.png "elfogadó beállításai")

**Törölje a mappát:** Azt határozza meg, hogy a célmappa törlődjön-e az adatok írása előtt.

**Fájlnév beállítás:** Azt határozza meg, hogy a célfájlok hogyan lesznek elnevezve a célmappában. A fájlnév beállításai a következők:
   * **Alapértelmezett**: Engedélyezze, hogy a Spark a PART alapértelmezései alapján nevezze el a fájlokat.
   * **Minta**: Adjon meg egy mintát, amely partíciónként számba veszi a kimeneti fájlokat. Például **a hitelek[n].csv** hitelek1.csv, hitelek2.csv és így tovább.
   * **Partíciónként**: Partíciónként egy fájlnevet adjon meg.
   * **Oszlopban lévő adatokként:** Állítsa a kimeneti fájlt egy oszlop értékére. Az elérési út az adatkészlet-tárolóhoz viszonyítva van, nem a célmappához. Ha van egy mappaelérési út az adatkészletben, a rendszer felülbírálja azt.
   * **Kimenet egyetlen fájlba**: A particionált kimeneti fájlokat egyetlen elnevezett fájlba egyesítheti. Az elérési út az adatkészlet mappához viszonyítva van. Ne feledje, hogy a te egyesítési művelet a csomópont mérete alapján sikertelen lehet. Ez a beállítás nagy adatkészletek esetén nem ajánlott.

**Árajánlat az összes:** Azt határozza meg, hogy az összes értéket belemellékelje-e az idézőjelek közé.

## <a name="lookup-activity-properties"></a>A keresgaszíntevékenység tulajdonságai

A tulajdonságokrészleteinek megismeréséhez ellenőrizze a [Kereskövetési tevékenységet.](control-flow-lookup-activity.md)

## <a name="getmetadata-activity-properties"></a>GetMetadata tevékenység tulajdonságai

A tulajdonságokrészleteinek megismeréséhez jelölje be a [GetMetadata tevékenység](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>Tevékenységtulajdonságok törlése

A tulajdonságok részleteinek megismeréséhez jelölje be a [Tevékenység törlése jelölőnégyzetet.](delete-activity.md)

## <a name="legacy-models"></a>Örökölt modellek

>[!NOTE]
>A következő modellek továbbra is támogatottak, mint a visszamenőleges kompatibilitás. Javasoljuk, hogy a fenti szakaszokban említett új modellt használja, és az ADF szerzői felhasználói felülete áttért az új modell létrehozására.

### <a name="legacy-dataset-model"></a>Örökölt adatkészletmodell

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | Az adatkészlet típustulajdonságát **AzureBlobFSFile**típusú ra kell állítani. |Igen |
| folderPath | A Data Lake Storage Gen2 mappájának elérési útja. Ha nincs megadva, akkor a gyökérre mutat. <br/><br/>A helyettesítő karakter szűrő támogatott. Az engedélyezett `*` helyettesítő karakterek (nulla vagy `?` több karakternek felelnek meg) és (nulla vagy egy karakternek felelnek meg). A `^` menekülésre használható, ha a tényleges mappanévhez helyettesítő karakter szerepel, vagy ez az escape-karakter van benne. <br/><br/>Példák: fájlrendszer/mappa/. További példák a [Mappa- és fájlszűrő-példákban](#folder-and-file-filter-examples)találhatók. |Nem |
| fileName | A megadott "folderPath" alatt található fájlok neve vagy helyettesítő karaktere. Ha nem ad meg értéket ehhez a tulajdonsághoz, az adatkészlet a mappában lévő összes fájlra mutat. <br/><br/>Szűrő esetén a helyettesítő `*` karakterek megengedettek (nulla `?` vagy több karakternek felelnek meg) és (nulla vagy egy karakternek felelnek meg).<br/>- 1. példa:`"fileName": "*.csv"`<br/>- 2. példa:`"fileName": "???20180427.txt"`<br/>Menekülésre használható, `^` ha a tényleges fájlnév helyettesítő karakterrel rendelkezik, vagy ez az escape-karakter benne van.<br/><br/>Ha a fájlnév nincs megadva egy kimeneti adatkészlethez, és a **preserveHierarchy** nincs megadva a tevékenységfogadóban, a másolási tevékenység automatikusan létrehozza a fájlnevet a következő mintával: "*Data.[ tevékenység futtatási azonosító azonosítója GUID]. [GUID, ha összeolvasztási hierarchia]. [formátum, ha konfigurálva van]. [tömörítés, ha be van állítva]*", például "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz". Ha táblázatos forrásból egy táblanév vel másol unk lekérdezés helyett, a névminta a "*[táblanév].[ formátum]. [tömörítés, ha konfigurálva van]*", például "MyTable.csv". |Nem |
| modifiedDatetimeStart | A Fájlok szűrője az utolsó módosítás attribútum alapján. A fájlok akkor kerülnek kiválasztásra, ha az `modifiedDatetimeStart` `modifiedDatetimeEnd`utolsó módosítás idáig a között és a közötti időtartományon belül van. Az idő az UTC időzónára a "2018-12-01T05:00:00Z" formátumban kerül alkalmazásra. <br/><br/> Az adatok mozgásának általános teljesítményét befolyásolja, ha engedélyezi ezt a beállítást, ha hatalmas mennyiségű fájllal rendelkező fájlszűrőt szeretne végezni. <br/><br/> A tulajdonságok null értékűek lehetnek, ami azt jelenti, hogy az adatkészletre nem alkalmaz fájlattribútum-szűrőt. Ha `modifiedDatetimeStart` datetime értékkel `modifiedDatetimeEnd` rendelkezik, de NULL értékű, az azt jelenti, hogy azokat a fájlokat, amelyek utolsó módosított attribútuma nagyobb vagy egyenlő a datetime értékkel, ki vannak jelölve. Ha `modifiedDatetimeEnd` datetime értékkel `modifiedDatetimeStart` rendelkezik, de NULL értékű, az azt jelenti, hogy azok a fájlok, amelyek utolsó módosított attribútuma kisebb, mint a datetime érték, ki vannak jelölve.| Nem |
| modifiedDatetimeEnd | A Fájlok szűrője az utolsó módosítás attribútum alapján. A fájlok akkor kerülnek kiválasztásra, ha az `modifiedDatetimeStart` `modifiedDatetimeEnd`utolsó módosítás idáig a között és a közötti időtartományon belül van. Az idő az UTC időzónára a "2018-12-01T05:00:00Z" formátumban kerül alkalmazásra. <br/><br/> Az adatok mozgásának általános teljesítményét befolyásolja, ha engedélyezi ezt a beállítást, ha hatalmas mennyiségű fájllal rendelkező fájlszűrőt szeretne végezni. <br/><br/> A tulajdonságok null értékűek lehetnek, ami azt jelenti, hogy az adatkészletre nem alkalmaz fájlattribútum-szűrőt. Ha `modifiedDatetimeStart` datetime értékkel `modifiedDatetimeEnd` rendelkezik, de NULL értékű, az azt jelenti, hogy azokat a fájlokat, amelyek utolsó módosított attribútuma nagyobb vagy egyenlő a datetime értékkel, ki vannak jelölve. Ha `modifiedDatetimeEnd` datetime értékkel `modifiedDatetimeStart` rendelkezik, de NULL értékű, az azt jelenti, hogy azok a fájlok, amelyek utolsó módosított attribútuma kisebb, mint a datetime érték, ki vannak jelölve.| Nem |
| Formátum | Ha a fájlokat úgy szeretné másolni, ahogy a fájlalapú tárolók között (bináris másolat), hagyja ki a formátum szakaszt a bemeneti és a kimeneti adatkészlet-definíciókban is.<br/><br/>Ha meghatározott formátumú fájlokat szeretne elemezni vagy létrehozni, a következő fájlformátum-típusok támogatottak: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**és **ParquetFormat**. Állítsa a **formátum** alatti **típustulajdonságot** ezen értékek egyikére. További információ: [Text format](supported-file-formats-and-compression-codecs-legacy.md#text-format), [JSON format](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Avro format](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [ORC format](supported-file-formats-and-compression-codecs-legacy.md#orc-format)és [Parquet format](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) szakaszok. |Nem (csak bináris másolási forgatókönyv esetén) |
| tömörítés | Adja meg az adatok tömörítésének típusát és szintjét. További információt a [Támogatott fájlformátumok és tömörítési kodekek című témakörben talál.](supported-file-formats-and-compression-codecs-legacy.md#compression-support)<br/>A támogatott típusok: **GZip**, **Deflate,** **BZip2**és **ZipDeflate**.<br/>A támogatott szintek **optimálisak** és **leggyorsabbak.** |Nem |

>[!TIP]
>A mappa összes fájljának másolásához adja meg a csak **mappaPath** parancsot.<br>Egyetlen fájl adott nevű másolásához adja meg a **folderPath** mappát egy mappaellel, **és fájlnév** fájlnévvel.<br>Ha a fájlok egy részhalmazát egy mappába szeretné másolni, adja meg a **folderPath** mappát egy mapparészsel, **a fileName-t** helyettesítő karakterrel. 

**Példa:**

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

### <a name="legacy-copy-activity-source-model"></a>Örökölt másolási tevékenység forrásmodellje

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenységforrás típustulajdonságát **az AzureBlobFSSource mezőre**kell állítani. |Igen |
| Rekurzív | Azt jelzi, hogy az adatok olvasása rekurzív anameddig az almappákból vagy csak a megadott mappából történik. Ha a rekurzív érték igaz, és a fogadó fájlalapú tároló, a program nem másolja vagy hozza létre az üres mappát vagy almappát a fogadóban.<br/>Az engedélyezett értékek **igazak** (alapértelmezettek) és **hamisak.** | Nem |
| maxConcurrentConnections | Az adattárhoz egyidejűleg csatlakozó kapcsolatok száma. Csak akkor adja meg, ha korlátozni szeretné az egyidejű kapcsolatot az adattárhoz. | Nem |

**Példa:**

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

### <a name="legacy-copy-activity-sink-model"></a>Örökölt másolási tevékenység fogadójának modellje

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenység fogadójának típustulajdonságát **az AzureBlobFSSink**beállításra kell állítani. |Igen |
| copyBehavior (Másként) | Azt a másolási viselkedést határozza meg, amikor a forrás fájlalapú adattárból származó fájlok.<br/><br/>Az engedélyezett értékek a következők:<br/><b>- MegőrzéseHierarchy (alapértelmezett)</b>: Megőrzi a fájlhierarchiát a célmappában. A forrásfájl forrásmappához viszonyított elérési útja megegyezik a célfájl nak a célmappához viszonyított elérési útvonalával.<br/><b>- Ahierarcha összeolvasztása</b>: A forrásmappából származó összes fájl a célmappa első szintjén található. A célfájlok nak automatikusan generált neve van. <br/><b>- MergeFiles</b>: Egyesíti az összes fájlt a forrás mappát egy fájlt. Ha a fájlnév meg van adva, az egyesített fájlnév a megadott név. Ellenkező esetben ez egy automatikusan létrehozott fájlnév. | Nem |
| maxConcurrentConnections | Az adattárhoz egyidejűleg csatlakozó kapcsolatok száma. Csak akkor adja meg, ha korlátozni szeretné az egyidejű kapcsolatot az adattárhoz. | Nem |

**Példa:**

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

## <a name="next-steps"></a>További lépések

A Data Factory másolási tevékenysége által forrásként és fogadóként támogatott adattárak listáját a Támogatott adattárak című témakörben [tetszését.](copy-activity-overview.md#supported-data-stores-and-formats)
