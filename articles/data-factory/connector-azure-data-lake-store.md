---
title: Adatok másolása Azure Data Lake Storage Gen1
description: Megtudhatja, hogyan másolhat adatok a támogatott forrás-adattárakból a Azure Data Lake Storeba, vagy Data Lake Storeról a támogatott fogadó áruházakba Data Factory használatával.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/12/2020
ms.openlocfilehash: 50e88d43d159ba5ac8f7b6c196c9843faad9eaf1
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79246226"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen1-using-azure-data-factory"></a>Adatok másolása Azure Data Lake Storage Gen1 a Azure Data Factory használatával

> [!div class="op_single_selector" title1="Válassza ki a használt Azure Data Factory verzióját:"]
> * [1-es verzió](v1/data-factory-azure-datalake-connector.md)
> * [Aktuális verzió](connector-azure-data-lake-store.md)

Ez a cikk az adatok Azure Data Lake Storage Gen1ba való másolásának módját ismerteti. A Azure Data Factoryről a [bevezető cikkben](introduction.md)olvashat bővebben.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez az Azure Data Lake Storage Gen1-összekötő a következő tevékenységek esetén támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md) 
- [Adatfolyam hozzárendelése](concepts-data-flow-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)
- [Tevékenység törlése](delete-activity.md)

Pontosabban, ezzel az összekötővel a következőket teheti:

- Másolja a fájlokat az alábbi hitelesítési módszerek egyikével: egyszerű szolgáltatásnév vagy felügyelt identitások az Azure-erőforrásokhoz.
- Fájlok másolása másként, vagy fájlok elemzése vagy készítése a [támogatott fájlformátumokkal és tömörítési kodekekkel](supported-file-formats-and-compression-codecs.md).
- [ACL](#preserve-acls-to-data-lake-storage-gen2) -ek megőrzése Azure Data Lake Storage Gen2ba való másoláskor.

> [!IMPORTANT]
> Ha az adatokat a saját üzemeltetésű integrációs modul használatával másolja, konfigurálja a vállalati tűzfalat úgy, hogy engedélyezze a kimenő forgalmat a 443-es porton `<ADLS account name>.azuredatalakestore.net` és `login.microsoftonline.com/<tenant>/oauth2/token`. Az utóbbi az Azure biztonsági jogkivonat szolgáltatás, amelyhez az integrációs modulnak kommunikálnia kell a hozzáférési token beszerzéséhez.

## <a name="get-started"></a>Első lépések

> [!TIP]
> Az Azure Data Lake Store-összekötő használatáról a következő témakörben olvashat: az [adatAzure Data Lake Storeba való betöltés](load-azure-data-lake-store.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban információt talál a Azure Data Lake Storera vonatkozó Data Factory entitások definiálásához használt tulajdonságokról.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A Azure Data Lake Store társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A `type` tulajdonságot **AzureDataLakeStore**értékre kell beállítani. | Igen |
| dataLakeStoreUri | Az Azure Data Lake Store-fiókkal kapcsolatos információk. Ez az információ a következő formátumok egyikét veszi figyelembe: `https://[accountname].azuredatalakestore.net/webhdfs/v1` vagy `adl://[accountname].azuredatalakestore.net/`. | Igen |
| subscriptionId | Az Azure-előfizetés azonosítója, amelyhez a Data Lake Store fiók tartozik. | A fogadó megadása kötelező |
| resourceGroupName | Az Azure-erőforráscsoport neve, amelyhez a Data Lake Store fiók tartozik. | A fogadó megadása kötelező |
| connectVia | Az adattárhoz való csatlakozáshoz használt [integrációs](concepts-integration-runtime.md) modul. Az Azure Integration Runtime vagy egy saját üzemeltetésű integrációs modult is használhat, ha az adattár egy magánhálózaton található. Ha ez a tulajdonság nincs megadva, a rendszer az alapértelmezett Azure Integration Runtime-t használja. |Nem |

### <a name="use-service-principal-authentication"></a>Egyszerű szolgáltatás hitelesítésének használata

Az egyszerű szolgáltatás hitelesítésének használatához kövesse az alábbi lépéseket.

1. Regisztráljon egy alkalmazás-entitást a Azure Active Directoryban, és adja meg Data Lake Store számára a hozzáférést. A részletes lépésekért lásd: [szolgáltatások közötti hitelesítés](../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Jegyezze fel a következő értékeket, mert a társított szolgáltatás definiálásához használja:

    - Alkalmazásazonosító
    - Alkalmazáskulcs
    - Bérlőazonosító

2. Adja meg a szolgáltatásnév megfelelő engedélyét. Tekintse át, hogyan működik az engedély Data Lake Storage Gen1 a [Azure Data Lake Storage Gen1 hozzáférés-vezérlésében](../data-lake-store/data-lake-store-access-control.md#common-scenarios-related-to-permissions).

    - **Forrásként**: az **adatkezelőben** > a **hozzáféréshez**legalább **végrehajtási** engedélyt kell adnia az összes felsőbb rétegbeli mappához, beleértve a gyökeret is, valamint **olvasási** engedéllyel kell rendelkeznie a másolandó fájlokhoz. Dönthet úgy, hogy felveszi **ezt a mappát és minden gyermeket** a rekurzív értékre, és hozzá kívánja adni **hozzáférési engedélyként és alapértelmezett engedély-bejegyzést**. A fiók szintű hozzáférés-vezérlés (IAM) nem követelmény.
    - **Fogadóként:** az **adatkezelőben** > a **hozzáféréshez**legalább **végrehajtási** engedélyt kell adnia minden felsőbb rétegbeli mappához, beleértve a gyökeret is, valamint **írási** engedéllyel a fogadó mappához. Dönthet úgy, hogy felveszi **ezt a mappát és minden gyermeket** a rekurzív értékre, és hozzá kívánja adni **hozzáférési engedélyként és alapértelmezett engedély-bejegyzést**. Ha Azure Integration Runtime-t használ a másoláshoz (a forrás és a fogadó is a felhőben van), a IAM-ban legalább az **olvasói** szerepkört kell biztosítania ahhoz, hogy Data Factory felderítse a Data Lake Store régióját. Ha el szeretné kerülni ezt a IAM-szerepkört, explicit módon [hozzon létre egy Azure Integration Runtime](create-azure-integration-runtime.md#create-azure-ir) -t Data Lake Store helyével. Ha például a Data Lake Store Nyugat-Európában található, hozzon létre egy "Nyugat-európai" nevű Azure Integration Runtime-t a helyhez. Társítsa őket a Data Lake Store társított szolgáltatáshoz az alábbi példában látható módon.

A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| servicePrincipalId | Adja meg az alkalmazás ügyfél-azonosítót. | Igen |
| servicePrincipalKey | Adja meg az alkalmazáskulcsot. A mező megjelölése `SecureString`ként, hogy biztonságosan tárolja azt a Data Factoryban, vagy [hivatkozzon a Azure Key Vaultban tárolt titkos kulcsra](store-credentials-in-key-vault.md). | Igen |
| tenant | Adja meg a bérlői adatokat, például a tartománynevet vagy a bérlő AZONOSÍTÓját, amely alatt az alkalmazás található. Kérheti a viszi az egérmutatót az Azure portal jobb felső sarkában. | Igen |

**Példa**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identity"></a>Felügyelt identitások használata az Azure-erőforrások hitelesítéséhez

Az adatok előállítója [felügyelt identitással társítható az Azure-erőforrásokhoz](data-factory-service-identity.md), amely az adott adatelőállítót képviseli. Ezt a felügyelt identitást közvetlenül használhatja Data Lake Store hitelesítéshez, hasonlóan a saját egyszerű szolgáltatásnév használatához is. Ez lehetővé teszi, hogy ez a kijelölt gyár hozzáférjen az adatokhoz, és másolja azokat Data Lake Storeba.

Ha felügyelt identitásokat szeretne használni az Azure-erőforrások hitelesítéséhez, kövesse az alábbi lépéseket.

1. [Kérje le az adat-előállító által felügyelt személyazonossági adatokat](data-factory-service-identity.md#retrieve-managed-identity) úgy, hogy átmásolja a "szolgáltatás Identity Application id" értékét a gyári környezettel együtt.

2. Adja meg a felügyelt identitás hozzáférését Data Lake Storehoz. Tekintse át, hogyan működik az engedély Data Lake Storage Gen1 a [Azure Data Lake Storage Gen1 hozzáférés-vezérlésében](../data-lake-store/data-lake-store-access-control.md#common-scenarios-related-to-permissions).

    - **Forrásként**: az **adatkezelőben** > a **hozzáféréshez**legalább **végrehajtási** engedélyt kell adnia az összes felsőbb rétegbeli mappához, beleértve a gyökeret is, valamint **olvasási** engedéllyel kell rendelkeznie a másolandó fájlokhoz. Dönthet úgy, hogy felveszi **ezt a mappát és minden gyermeket** a rekurzív értékre, és hozzá kívánja adni **hozzáférési engedélyként és alapértelmezett engedély-bejegyzést**. A fiók szintű hozzáférés-vezérlés (IAM) nem követelmény.
    - **Fogadóként:** az **adatkezelőben** > a **hozzáféréshez**legalább **végrehajtási** engedélyt kell adnia minden felsőbb rétegbeli mappához, beleértve a gyökeret is, valamint **írási** engedéllyel a fogadó mappához. Dönthet úgy, hogy felveszi **ezt a mappát és minden gyermeket** a rekurzív értékre, és hozzá kívánja adni **hozzáférési engedélyként és alapértelmezett engedély-bejegyzést**. Ha Azure Integration Runtime-t használ a másoláshoz (a forrás és a fogadó is a felhőben van), a IAM-ban legalább az **olvasói** szerepkört kell biztosítania ahhoz, hogy Data Factory felderítse a Data Lake Store régióját. Ha el szeretné kerülni ezt a IAM-szerepkört, explicit módon [hozzon létre egy Azure Integration Runtime](create-azure-integration-runtime.md#create-azure-ir) -t Data Lake Store helyével. Társítsa őket a Data Lake Store társított szolgáltatáshoz az alábbi példában látható módon.

Azure Data Factory a társított szolgáltatás általános Data Lake Store információi mellett nem kell tulajdonságokat megadnia.

**Példa**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját az [adatkészletek](concepts-datasets-linked-services.md) című cikkben találja. 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

A következő tulajdonságok támogatottak Azure Data Lake Store Gen1 a Format-alapú adatkészlet `location` beállításai alatt:

| Tulajdonság   | Leírás                                                  | Kötelező |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Az adatkészletben lévő `location` alatt található Type tulajdonságot **AzureDataLakeStoreLocation**értékre kell állítani. | Igen      |
| folderPath | Egy mappa elérési útja. Ha a mappák szűréséhez helyettesítő karaktert szeretne használni, hagyja ki ezt a beállítást, és határozza meg a tevékenység forrásának beállításai között. | Nem       |
| fileName   | A fájlnév a megadott folderPath alatt. Ha helyettesítő karaktert szeretne használni a fájlok szűréséhez, hagyja ki ezt a beállítást, és határozza meg a tevékenység forrásának beállításai között. | Nem       |

**Példa**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<ADLS Gen1 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureDataLakeStoreLocation",
                "folderPath": "root/folder/subfolder"
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

A tevékenységek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját lásd: [folyamatok](concepts-pipelines-activities.md). Ez a szakasz a Azure Data Lake Store forrás és a fogadó által támogatott tulajdonságok listáját tartalmazza.

### <a name="azure-data-lake-store-as-source"></a>Az Azure Data Lake Store mint forrás

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

A következő tulajdonságok támogatottak Azure Data Lake Store Gen1 a Format-alapú másolási forrás `storeSettings` beállítások területén:

| Tulajdonság                 | Leírás                                                  | Kötelező                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | A `storeSettings` alatti Type tulajdonságot **AzureDataLakeStoreReadSettings**értékre kell állítani. | Igen                                           |
| recursive                | Azt jelzi, hogy az adatok olvasható rekurzív módon az almappák vagy csak a megadott mappába. Ha a rekurzív értéke TRUE (igaz), a fogadó pedig egy fájl alapú tároló, akkor a fogadó nem másol vagy hoz létre üres mappát vagy almappát. Az engedélyezett értékek: **true** (alapértelmezett) és **false (hamis**). | Nem                                            |
| wildcardFolderPath       | A mappa elérési útja helyettesítő karakterekkel a forrás mappák szűréséhez. <br>Az engedélyezett helyettesítő karakterek `*` (nulla vagy több karakternek felelnek meg) és `?` (a nulla vagy egy karakternek felel meg). A `^` használatával elkerülheti, hogy a tényleges mappanév tartalmaz-e helyettesítő karaktert, vagy a menekülési karakter belsejében. <br>További példákat a [mappák és a fájlok szűrésére szolgáló példákban](#folder-and-file-filter-examples)talál. | Nem                                            |
| wildcardFileName         | A forrásfájl szűréséhez a megadott folderPath/wildcardFolderPath helyettesítő karaktereket tartalmazó fájlnév. <br>Az engedélyezett helyettesítő karakterek `*` (nulla vagy több karakternek felelnek meg) és `?` (a nulla vagy egy karakternek felel meg). A `^` használatával elkerülheti, hogy a tényleges mappanév tartalmaz-e helyettesítő karaktert, vagy a menekülési karakter belsejében. További példákat a [mappák és a fájlok szűrésére szolgáló példákban](#folder-and-file-filter-examples)talál. | Igen, ha `fileName` nincs megadva az adatkészletben |
| modifiedDatetimeStart    | A fájlok szűrése a legutóbb módosított attribútum alapján. A fájlok akkor vannak kiválasztva, ha az utolsó módosítás ideje a `modifiedDatetimeStart` és `modifiedDatetimeEnd`közötti időtartományon belül van. Az időpontot az UTC időzónára alkalmazza a "2018-12-01T05:00:00Z" formátumban. <br> A tulajdonságok lehetnek NULL értékűek, ami azt jelenti, hogy a rendszer nem alkalmazza a file Attribute szűrőt az adatkészletre. Ha `modifiedDatetimeStart` datetime értékkel rendelkezik, de `modifiedDatetimeEnd` NULL értékű, akkor azok a fájlok vannak kiválasztva, amelyek utolsó módosítási attribútuma nagyobb vagy egyenlő, mint a DateTime érték. Ha `modifiedDatetimeEnd` datetime értékkel rendelkezik, de `modifiedDatetimeStart` NULL értékű, akkor azok a fájlok vannak kiválasztva, amelyek utolsó módosítási attribútuma kisebb a DateTime értéknél. | Nem                                            |
| modifiedDatetimeEnd      | Ugyanaz, mint a fenti.                                               | Nem                                            |
| maxConcurrentConnections | A tárolási tárolóhoz való kapcsolódáshoz szükséges kapcsolatok száma egyidejűleg. Csak akkor kell megadni, ha az egyidejű kapcsolódást szeretné korlátozni az adattárral. | Nem                                            |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromADLSGen1",
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
                    "type": "AzureDataLakeStoreReadSettings",
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

### <a name="azure-data-lake-store-as-sink"></a>Az Azure Data Lake Store mint fogadó

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

A következő tulajdonságok támogatottak Azure Data Lake Store Gen1 a Format-alapú másolási fogadó `storeSettings` beállításai alatt:

| Tulajdonság                 | Leírás                                                  | Kötelező |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | A `storeSettings` alatti Type tulajdonságot **AzureDataLakeStoreWriteSettings**értékre kell állítani. | Igen      |
| copyBehavior             | A másolási viselkedés határozza meg, ha a forrás fájlok fájlalapú adattárból.<br/><br/>Engedélyezett értékek a következők:<br/><b>-PreserveHierarchy (alapértelmezett)</b>: megőrzi a fájl-hierarchiát a célmappában. A forrásfájl relatív elérési útja a forrás mappájához azonos a célfájl relatív elérési útjával.<br/><b>-FlattenHierarchy</b>: a forrás mappából származó összes fájl a célmappa első szintjén van. A cél fájlok automatikusan létrehozott névvel rendelkeznek. <br/><b>-MergeFiles</b>: az összes fájlt egyesíti a forrás mappájából egy fájlba. A fájl neve meg van adva, az egyesített fájlnév-e a megadott néven. Ellenkező esetben egy automatikusan létrehozott nevét. | Nem       |
| expiryDateTime | Megadja az írott fájlok lejárati idejét. Az idő az UTC időpontra lesz alkalmazva "2020-03-01T08:00:00Z" formátumban. Alapértelmezés szerint NULL értékű, ami azt jelenti, hogy az írott fájlok soha nem jártak le. | Nem |
| maxConcurrentConnections | Az adattárhoz való kapcsolódáshoz szükséges kapcsolatok száma egyszerre. Csak akkor kell megadni, ha az egyidejű kapcsolódást szeretné korlátozni az adattárral. | Nem       |

**Példa**

```json
"activities":[
    {
        "name": "CopyToADLSGen1",
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
                    "type": "AzureDataLakeStoreWriteSettings",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Példák a mappák és a fájlok szűrésére

Ez a szakasz a mappa elérési útjának és fájlnevének a helyettesítő karakteres szűrőkkel való viselkedését írja le.

| folderPath | fileName | recursive | A forrás mappa szerkezete és a szűrő eredménye (a **félkövérrel szedett** fájlok beolvasása)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (Üres, alapértelmezett használata) | false | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**file1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**fájl2. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;fájl3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*` | (Üres, alapértelmezett használata) | true | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**file1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**fájl2. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**fájl3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*` | `*.csv` | false | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**file1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;fájl3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*` | `*.csv` | true | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**file1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**fájl3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |

### <a name="examples-of-behavior-of-the-copy-operation"></a>Példák a másolási művelet viselkedésére

Ez a szakasz a másolási művelet eredményét mutatja `recursive` és `copyBehavior` értékek különböző kombinációi esetén.

| recursive | copyBehavior | Forrás mappastruktúra | Eredményül kapott cél |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A célként megadott Mappa1 ugyanazzal a struktúrával jön létre, mint a forrás:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A cél mappa1 jön létre az alábbi struktúra használatával: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan generált neve a file1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan generált neve a Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan generált neve a fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan generált neve a File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan generált neve a File5 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A cél mappa1 jön létre az alábbi struktúra használatával: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1 + Fájl2 + fájl3 + File4 + File5 tartalma egyetlen fájlba van egyesítve egy automatikusan létrehozott fájlnévvel. |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A cél mappa1 jön létre az alábbi struktúra használatával:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/><br/>A fájl3, a File4 és a File5 Subfolder1 nem vesznek fel. |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A cél mappa1 jön létre az alábbi struktúra használatával:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan generált neve a file1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan generált neve a Fájl2<br/><br/>A fájl3, a File4 és a File5 Subfolder1 nem vesznek fel. |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A cél mappa1 jön létre az alábbi struktúra használatával:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1 + Fájl2 tartalma egyetlen fájlba van egyesítve, amely automatikusan létrehozott fájlnevet tartalmaz. automatikusan létrehozott nevet, a file1 kiszolgálón<br/><br/>A fájl3, a File4 és a File5 Subfolder1 nem vesznek fel. |

## <a name="preserve-acls-to-data-lake-storage-gen2"></a>ACL-ek megőrzése Data Lake Storage Gen2

>[!TIP]
>Az adatok Azure Data Lake Storage Gen1ból a Gen2-be való másolásához lásd: [adatok másolása Azure Data Lake Storage Gen1ról a Gen2](load-azure-data-lake-storage-gen2-from-gen1.md) -be a Azure Data Factory használatával a lépésenkénti és ajánlott eljárásokhoz.

Ha a hozzáférés-vezérlési listákat (ACL-eket) az adatfájlokkal együtt szeretné replikálni Data Lake Storage Gen1ról Data Lake Storage Gen2ra, tekintse meg az [ACL-ek megőrzése Data Lake Storage Gen1ról](copy-activity-preserve-metadata.md#preserve-acls)című témakört.

## <a name="mapping-data-flow-properties"></a>Adatfolyam-tulajdonságok leképezése

A leképezési adatfolyamban lévő adatok átalakításakor a Azure Data Lake Storage Gen1 JSON-, Avro-, tagolt szöveg-vagy parketta-formátumú fájlokat is olvashat és írhat. További információ: a [forrás-átalakítás](data-flow-source.md) és a fogadó [transzformációja](data-flow-sink.md) a leképezési adatfolyam funkcióban.

### <a name="source-transformation"></a>Forrás-átalakítás

A forrás-átalakításban egy tárolóból, mappából vagy különálló fájlból is elolvasható Azure Data Lake Storage Gen1. A **forrás beállításai** lapon kezelheti a fájlok olvasásának módját. 

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

A fogadó átalakításban megírhatja Azure Data Lake Storage Gen1 tárolóba vagy mappájába. a **Beállítások** lapon kezelheti a fájlok írásának módját.

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

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | Az adatkészlet Type tulajdonságát **AzureDataLakeStoreFile**értékre kell állítani. |Igen |
| folderPath | A Data Lake Store mappájának elérési útja. Ha nincs megadva, a legfelső szintű mutat. <br/><br/>A helyettesítő karakteres szűrő használata támogatott. Az engedélyezett helyettesítő karakterek `*` (nulla vagy több karakternek felelnek meg) és `?` (a nulla vagy egy karakternek felel meg). A `^` használatával elkerülheti, hogy a tényleges mappanév tartalmaz-e helyettesítő karaktert, vagy a menekülési karakter belsejében. <br/><br/>Például: gyökérmappa/almappa/. További példákat a [mappák és a fájlok szűrésére szolgáló példákban](#folder-and-file-filter-examples)talál. |Nem |
| fileName | A megadott "folderPath" alatt lévő fájlok neve vagy helyettesítő karakteres szűrője. Ez a tulajdonság értékét nem adja meg, ha az adatkészlet mutat a mappában lévő összes fájlt. <br/><br/>A Filter (szűrő) esetében az engedélyezett helyettesítő karakterek `*` (a nulla vagy több karakternek felelnek meg) és a `?` (a nulla vagy egy karakternek felel meg).<br/>– 1. példa: `"fileName": "*.csv"`<br/>– 2. példa: `"fileName": "???20180427.txt"`<br/>A `^` használatával elkerülheti, hogy a tényleges fájlnév helyettesítő karakterrel vagy a menekülési karakterrel rendelkezik-e a belsejében.<br/><br/>Ha nincs megadva fájlnév a kimeneti adatkészlethez, és a **preserveHierarchy** nincs megadva a tevékenység fogadójában, a másolási tevékenység automatikusan létrehozza a fájlnevet a következő mintával: "*adat. [ tevékenység futtatási azonosítója GUID]. [GUID if FlattenHierarchy]. [formátum, ha konfigurálva]. [tömörítés, ha be van állítva]* ", például" az "adat. 0a405f8a-93ff-4c6f-B3BE-f69616f1df7a. txt. gz". Ha egy táblázatos forrásból másol egy tábla nevét egy lekérdezés helyett, akkor a név minta a következő: " *[Table Name]. [ Format]. [tömörítés, ha konfigurálva]* ", például" sajáttábla. csv ". |Nem |
| modifiedDatetimeStart | A fájlok szűrése a legutóbb módosított attribútum alapján. A fájlok akkor vannak kiválasztva, ha az utolsó módosítás ideje a `modifiedDatetimeStart` és `modifiedDatetimeEnd`közötti időtartományon belül van. Az időpontot az UTC időzónára alkalmazza a "2018-12-01T05:00:00Z" formátumban. <br/><br/> Ha ezt a beállítást szeretné elvégezni, nagy mennyiségű fájl használata esetén a rendszer az adatáthelyezés általános teljesítményét érinti. <br/><br/> A tulajdonságok lehetnek NULL értékűek, ami azt jelenti, hogy a rendszer nem alkalmazza a file Attribute szűrőt az adatkészletre. Ha `modifiedDatetimeStart` datetime értékkel rendelkezik, de `modifiedDatetimeEnd` NULL értékű, akkor azok a fájlok vannak kiválasztva, amelyek utolsó módosítási attribútuma nagyobb vagy egyenlő, mint a DateTime érték. Ha `modifiedDatetimeEnd` datetime értékkel rendelkezik, de `modifiedDatetimeStart` NULL értékű, akkor azok a fájlok vannak kiválasztva, amelyek utolsó módosítási attribútuma kisebb a DateTime értéknél.| Nem |
| modifiedDatetimeEnd | A fájlok szűrése a legutóbb módosított attribútum alapján. A fájlok akkor vannak kiválasztva, ha az utolsó módosítás ideje a `modifiedDatetimeStart` és `modifiedDatetimeEnd`közötti időtartományon belül van. Az időpontot az UTC időzónára alkalmazza a "2018-12-01T05:00:00Z" formátumban. <br/><br/> Ha ezt a beállítást szeretné elvégezni, nagy mennyiségű fájl használata esetén a rendszer az adatáthelyezés általános teljesítményét érinti. <br/><br/> A tulajdonságok lehetnek NULL értékűek, ami azt jelenti, hogy a rendszer nem alkalmazza a file Attribute szűrőt az adatkészletre. Ha `modifiedDatetimeStart` datetime értékkel rendelkezik, de `modifiedDatetimeEnd` NULL értékű, akkor azok a fájlok vannak kiválasztva, amelyek utolsó módosítási attribútuma nagyobb vagy egyenlő, mint a DateTime érték. Ha `modifiedDatetimeEnd` datetime értékkel rendelkezik, de `modifiedDatetimeStart` NULL értékű, akkor azok a fájlok vannak kiválasztva, amelyek utolsó módosítási attribútuma kisebb a DateTime értéknél.| Nem |
| format | Ha fájlokat szeretne másolni a fájl alapú tárolók között (bináris másolás), ugorja át a formátum szakaszt mind a bemeneti, mind a kimeneti adatkészlet-definíciókban.<br/><br/>Ha a fájlokat egy adott formátummal szeretné elemezni vagy előállítani, a következő fájlformátum-típusok támogatottak: **Szövegformátum**, **JsonFormat**, **AvroFormat**, **OrcFormat**és **ParquetFormat**. A **Type (típus** ) tulajdonságot állítsa a **Format** értékre a következő értékek egyikére. További információ: [szöveg formátuma](supported-file-formats-and-compression-codecs-legacy.md#text-format), JSON- [Formátum](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Avro formátum](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [ork-formátum](supported-file-formats-and-compression-codecs-legacy.md#orc-format)és [parketta formátuma](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) . |Nem (csak a bináris másolás esetén) |
| compression | Adja meg a típus és az adatok tömörítési szintje. További információ: [támogatott fájlformátumok és tömörítési kodekek](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>A támogatott típusok a **gzip**, a **deflate**, a **BZip2**és a **ZipDeflate**.<br/>A támogatott szintek **optimálisak** és **leggyorsabbak**. |Nem |

>[!TIP]
>Egy mappa összes fájljának másolásához csak a **folderPath** kell megadni.<br>Egy adott névvel rendelkező egyetlen fájl másolásához adja meg a **folderPath** mappát, és nevezze el a **fájlnevet** .<br>Ha egy mappában lévő fájlok egy részhalmazát szeretné másolni, akkor a **folderPath** és a **fájlnevet** egy helyettesítő szűrővel kell megadnia. 

**Példa**

```json
{
    "name": "ADLSDataset",
    "properties": {
        "type": "AzureDataLakeStoreFile",
        "linkedServiceName":{
            "referenceName": "<ADLS linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "datalake/myfolder/",
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

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának `type` tulajdonságát **AzureDataLakeStoreSource**értékre kell állítani. |Igen |
| recursive | Azt jelzi, hogy az adatok olvasható rekurzív módon az almappák vagy csak a megadott mappába. Ha a `recursive` értéke TRUE (igaz), és a fogadó egy fájl alapú tároló, akkor a fogadó nem másol vagy hoz létre üres mappát vagy almappát. Az engedélyezett értékek: **true** (alapértelmezett) és **false (hamis**). | Nem |
| maxConcurrentConnections | Az adattárhoz való kapcsolódáshoz szükséges kapcsolatok száma egyszerre. Csak akkor kell megadni, ha az egyidejű kapcsolódást szeretné korlátozni az adattárral. | Nem |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ADLS Gen1 input dataset name>",
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
                "type": "AzureDataLakeStoreSource",
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

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenység fogadójának `type` tulajdonságát **AzureDataLakeStoreSink**értékre kell állítani. |Igen |
| copyBehavior | A másolási viselkedés határozza meg, ha a forrás fájlok fájlalapú adattárból.<br/><br/>Engedélyezett értékek a következők:<br/><b>-PreserveHierarchy (alapértelmezett)</b>: megőrzi a fájl-hierarchiát a célmappában. A forrásfájl relatív elérési útja a forrás mappájához azonos a célfájl relatív elérési útjával.<br/><b>-FlattenHierarchy</b>: a forrás mappából származó összes fájl a célmappa első szintjén van. A cél fájlok automatikusan létrehozott névvel rendelkeznek. <br/><b>-MergeFiles</b>: az összes fájlt egyesíti a forrás mappájából egy fájlba. A fájl neve meg van adva, az egyesített fájlnév-e a megadott néven. Ellenkező esetben a fájl neve automatikusan létrejön. | Nem |
| maxConcurrentConnections | Az adattárhoz való kapcsolódáshoz szükséges kapcsolatok száma egyszerre. Csak akkor kell megadni, ha az egyidejű kapcsolódást szeretné korlátozni az adattárral. | Nem |

**Példa**

```json
"activities":[
    {
        "name": "CopyToADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ADLS Gen1 output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDataLakeStoreSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

## <a name="next-steps"></a>Következő lépések

A Azure Data Factory a másolási tevékenység által forrásként és nyelőként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
