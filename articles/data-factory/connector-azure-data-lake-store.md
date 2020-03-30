---
title: Adatok másolása az Azure Data Lake Storage Gen1 szolgáltatásba vagy onnan
description: Megtudhatja, hogyan másolhat adatokat a támogatott forrásadat-tárolókból az Azure Data Lake Store-ba, vagy a Data Lake Store-ból a támogatott fogadótárolókba a Data Factory használatával.
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
ms.openlocfilehash: a8ba8b212a504a8f8e4e29fbd50126189998e81a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065464"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen1-using-azure-data-factory"></a>Adatok másolása az Azure Data Lake Storage Gen1 szolgáltatásba vagy onnan az Azure Data Factory használatával

> [!div class="op_single_selector" title1="Válassza ki az Azure Data Factory által használt verziót:"]
> * [1-es verzió](v1/data-factory-azure-datalake-connector.md)
> * [Aktuális verzió](connector-azure-data-lake-store.md)

Ez a cikk bemutatja, hogyan másolhat adatokat az Azure Data Lake Storage Gen1-be és onnan. Az Azure Data Factory ról a [bevezető cikkben](introduction.md)olvashat.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez az Azure Data Lake Storage Gen1-összekötő a következő tevékenységek esetén támogatott:

- [Tevékenység másolása](copy-activity-overview.md) [támogatott forrás/fogadó mátrixcal](copy-activity-overview.md) 
- [Adatfolyam leképezése](concepts-data-flow-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)
- [Tevékenység törlése](delete-activity.md)

Pontosabban, ezzel a csatlakozóval:

- Fájlok másolása a következő hitelesítési módszerek egyikével: egyszerű szolgáltatás vagy felügyelt identitások az Azure-erőforrásokhoz.
- Fájlok másolása a megfelelő módon, illetve elemezése vagy létrehozása a [támogatott fájlformátumokat és tömörítési kodekeket.](supported-file-formats-and-compression-codecs.md)
- [Az ACL-ek megőrzése](#preserve-acls-to-data-lake-storage-gen2) az Azure Data Lake Storage Gen2 szolgáltatásba való másoláskor.

> [!IMPORTANT]
> Ha az adatokat a saját üzemeltetésű integrációs futásidő használatával másolja, konfigurálja a vállalati tűzfalat úgy, hogy lehetővé tegye a 443-as portra `<ADLS account name>.azuredatalakestore.net` irányuló `login.microsoftonline.com/<tenant>/oauth2/token` kimenő forgalmat. Ez utóbbi az Azure Security Token Service, amely az integrációs futásidejű kell kommunikálni a hozzáférési jogkivonat lekéréséhez.

## <a name="get-started"></a>Bevezetés

> [!TIP]
> Az Azure Data Lake Store-összekötő használatának áttekintése az [Adatok betöltése az Azure Data Lake Store-ba című témakörben található.](load-azure-data-lake-store.md)

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok az Azure Data Lake Store-ra jellemző Data Factory-entitások definiálására használt tulajdonságokkal kapcsolatos információkat tartalmaznak.

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai

Az Azure Data Lake Store-hoz csatolt szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A `type` tulajdonságot **Az AzureDataLakeStore beállításra**kell állítani. | Igen |
| dataLakeStoreUri | Az Azure Data Lake Store-fiókkal kapcsolatos információk. Ez az információ az alábbi `https://[accountname].azuredatalakestore.net/webhdfs/v1` formátumok `adl://[accountname].azuredatalakestore.net/`egyikét veszi fel: vagy . | Igen |
| subscriptionId | Az Azure-előfizetés-azonosító, amelyhez a Data Lake Áruház-fiók tartozik. | A mosogatóhoz szükséges |
| resourceGroupName | Az Azure erőforráscsoport neve, amelyhez a Data Lake Store-fiók tartozik. | A mosogatóhoz szükséges |
| connectVia | Az adattárhoz való csatlakozáshoz használandó [integrációs futásidő.](concepts-integration-runtime.md) Használhatja az Azure-integrációs runtime vagy egy saját üzemeltetésű integrációs runtime, ha az adattár található egy magánhálózat. Ha ez a tulajdonság nincs megadva, az alapértelmezett Azure-integrációs futásidejű használatos. |Nem |

### <a name="use-service-principal-authentication"></a>Egyszerű szolgáltatáshitelesítés használata

Az egyszerű szolgáltatáshitelesítés használatához kövesse az alábbi lépéseket.

1. Regisztráljon egy alkalmazásentitást az Azure Active Directoryban, és adjon hozzáférést a Data Lake Store-hoz. A részletes lépéseket a [Szolgáltatás-szolgáltatás hitelesítése című témakörben található.](../data-lake-store/data-lake-store-authenticate-using-active-directory.md) Jegyezze fel a következő értékeket, amelyek segítségével definiálja a csatolt szolgáltatást:

    - Alkalmazásazonosító
    - Alkalmazáskulcs
    - Bérlőazonosító

2. Adja meg az egyszerű szolgáltatás megfelelő engedélyét. Példák az engedélyek működéséről a Data Lake Storage Gen1 [hozzáférés-vezérlésből az Azure Data Lake Storage Gen1 szolgáltatásban.](../data-lake-store/data-lake-store-access-control.md#common-scenarios-related-to-permissions)

    - **Forrásként:** Az **Adatkezelő** > **Access**programban legalább **végrehajtási** engedélyt adhat az összes felsőbb szintű mappára, beleértve a gyökeret is, valamint **olvasási** engedélyt a másolandó fájlokhoz. Választhatja azt is, hogy hozzáadja ezt a **mappát és az összes gyermeket** rekurzív, és adja hozzá hozzáférési **engedélyként és alapértelmezett engedélybejegyzésként**. Nincs követelmény a fiókszintű hozzáférés-vezérlés (IAM).
    - **Fogadóként:** Az **Adatkezelő** > **Access**programban legalább **végrehajtási** engedélyt adhat az összes felsőbb szintű mappára, beleértve a gyökeret is, valamint **írási** engedélyt a fogadó mappához. Választhatja azt is, hogy hozzáadja ezt a **mappát és az összes gyermeket** rekurzív, és adja hozzá hozzáférési **engedélyként és alapértelmezett engedélybejegyzésként**. Ha egy Azure-integrációs futásidejű másolásra (mind a forrás, mind a fogadó a felhőben van), az IAM-ben legalább az **Olvasó** szerepkört adja meg annak érdekében, hogy a Data Factory észlelhesse a Data Lake Store régióját. Ha el szeretné kerülni ezt az IAM-szerepkört, explicit módon [hozzon létre egy Azure-integrációs futási időt](create-azure-integration-runtime.md#create-azure-ir) a Data Lake Store helyével. Ha például a Data Lake Store Nyugat-Európában található, hozzon létre egy Azure-integrációs futásidőt, amelynek helye "Nyugat-Európa" lesz. Társítsa őket a Data Lake Store csatolt szolgáltatásban az alábbi példában látható módon.

A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| servicePrincipalId | Adja meg az alkalmazás ügyfélazonosítóját. | Igen |
| servicePrincipalKey | Adja meg az alkalmazás kulcsát. Jelölje meg ezt `SecureString` a mezőt a Data Factory biztonságos tárolásához, vagy [hivatkozzon az Azure Key Vaultban tárolt titkos fájlokra.](store-credentials-in-key-vault.md) | Igen |
| Bérlő | Adja meg a bérlői adatokat, például a tartománynevet vagy a bérlőazonosítót, amely alatt az alkalmazás található. Az egér relamás, az Azure Portal jobb felső sarkában lévő egérrel érhető el. | Igen |

**Példa:**

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

### <a name="use-managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a>Felügyelt identitások használata az Azure-erőforrások hitelesítéséhez

Az adat-előállító [azure-erőforrások felügyelt identitással](data-factory-service-identity.md)társítható, amely ezt az adott adat-előállítót jelöli. Ezt a felügyelt identitást közvetlenül használhatja a Data Lake Store-hitelesítéshez, hasonlóan a saját egyszerű szolgáltatáshoz. Lehetővé teszi, hogy ez a kijelölt gyár adatokat és adatokat másoljon a Data Lake Store-ba vagy onnan.

Felügyelt identitások használatához az Azure-erőforrások hitelesítéséhez kövesse az alábbi lépéseket.

1. [Az adat-előállító felügyelt identitásadatok lekéréséhez](data-factory-service-identity.md#retrieve-managed-identity) másolja a gyárral együtt létrehozott "Szolgáltatásidentitás-alkalmazásazonosító" értékét.

2. Adja meg a felügyelt identitás hozzáférést data lake store. Példák az engedélyek működéséről a Data Lake Storage Gen1 [hozzáférés-vezérlésből az Azure Data Lake Storage Gen1 szolgáltatásban.](../data-lake-store/data-lake-store-access-control.md#common-scenarios-related-to-permissions)

    - **Forrásként:** Az **Adatkezelő** > **Access**programban legalább **végrehajtási** engedélyt adhat az összes felsőbb szintű mappára, beleértve a gyökeret is, valamint **olvasási** engedélyt a másolandó fájlokhoz. Választhatja azt is, hogy hozzáadja ezt a **mappát és az összes gyermeket** rekurzív, és adja hozzá hozzáférési **engedélyként és alapértelmezett engedélybejegyzésként**. Nincs követelmény a fiókszintű hozzáférés-vezérlés (IAM).
    - **Fogadóként:** Az **Adatkezelő** > **Access**programban legalább **végrehajtási** engedélyt adhat az összes felsőbb szintű mappára, beleértve a gyökeret is, valamint **írási** engedélyt a fogadó mappához. Választhatja azt is, hogy hozzáadja ezt a **mappát és az összes gyermeket** rekurzív, és adja hozzá hozzáférési **engedélyként és alapértelmezett engedélybejegyzésként**. Ha egy Azure-integrációs futásidejű másolásra (mind a forrás, mind a fogadó a felhőben van), az IAM-ben legalább az **Olvasó** szerepkört adja meg annak érdekében, hogy a Data Factory észlelhesse a Data Lake Store régióját. Ha el szeretné kerülni ezt az IAM-szerepkört, explicit módon [hozzon létre egy Azure-integrációs futási időt](create-azure-integration-runtime.md#create-azure-ir) a Data Lake Store helyével. Társítsa őket a Data Lake Store csatolt szolgáltatásban az alábbi példában látható módon.

Az Azure Data Factoryban nem kell megadnia a kapcsolódó szolgáltatás általános Data Lake Store-információin kívül egyetlen tulajdonságot sem.

**Példa:**

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

Az adatkészletek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját az [Adatkészletek](concepts-datasets-linked-services.md) című cikkben olvashatja. 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Az Azure Data Lake Store Gen1 `location` a következő tulajdonságokat támogatja a formátumalapú adatkészlet beállításai között:

| Tulajdonság   | Leírás                                                  | Kötelező |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Az adatkészlet `location` típustulajdonságát **Az AzureDataLakeStoreLocation**beállításra kell állítani. | Igen      |
| folderPath | A mappa elérési útja. Ha helyettesítő karakterrel szeretné szűrni a mappákat, hagyja ki ezt a beállítást, és adja meg a tevékenységforrás-beállításokban. | Nem       |
| fileName   | A fájlnév a megadott mappaPath. Ha helyettesítő karakterrel szeretné szűrni a fájlokat, hagyja ki ezt a beállítást, és adja meg a tevékenységforrás-beállításokban. | Nem       |

**Példa:**

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

A tevékenységek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját a [Folyamatok című témakörben található.](concepts-pipelines-activities.md) Ez a szakasz az Azure Data Lake Store-forrás és -fogadó által támogatott tulajdonságok listáját tartalmazza.

### <a name="azure-data-lake-store-as-source"></a>Az Azure Data Lake Store mint forrás

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Az Azure Data Lake Store Gen1 `storeSettings` a formátumalapú másolási forrás beállításai alatt a következő tulajdonságokat támogatja:

| Tulajdonság                 | Leírás                                                  | Kötelező                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | A típustulajdonságot `storeSettings` az **AzureDataLakeStoreReadSettings**beállításra kell állítani. | Igen                                           |
| Rekurzív                | Azt jelzi, hogy az adatok olvasása rekurzív anameddig az almappákból vagy csak a megadott mappából történik. Ha a rekurzív érték igaz, és a fogadó fájlalapú tároló, a program nem másolja vagy hozza létre az üres mappát vagy almappát a fogadóban. Az engedélyezett értékek **igazak** (alapértelmezettek) és **hamisak.** | Nem                                            |
| helyettesítő mappaelérési út       | A forrásmappák szűréséhez helyettesítő karakterekkel ellátott mappaelérési út. <br>Az engedélyezett `*` helyettesítő karakterek (nulla vagy `?` több karakternek felelnek meg) és (nulla vagy egy karakternek felelnek meg). Menekülésre használható, `^` ha a tényleges mappanévben helyettesítő karakter szerepel, vagy ez az escape-karakter van benne. <br>További példák a [Mappa- és fájlszűrő-példákban](#folder-and-file-filter-examples)találhatók. | Nem                                            |
| helyettesítő fájlneve         | A forrásfájlok szűréséhez helyettesítő karakterekkel rendelkező fájlnév a kívánt/helyettesítő folderFolderPath mappában. <br>Az engedélyezett `*` helyettesítő karakterek (nulla vagy `?` több karakternek felelnek meg) és (nulla vagy egy karakternek felelnek meg). Menekülésre használható, `^` ha a tényleges mappanévben helyettesítő karakter szerepel, vagy ez az escape-karakter van benne. További példák a [Mappa- és fájlszűrő-példákban](#folder-and-file-filter-examples)találhatók. | Igen, `fileName` ha nincs megadva az adatkészletben |
| modifiedDatetimeStart    | A Fájlok szűrője az utolsó módosítás attribútum alapján. A fájlok akkor kerülnek kiválasztásra, ha az `modifiedDatetimeStart` `modifiedDatetimeEnd`utolsó módosítás idáig a között és a közötti időtartományon belül van. Az idő az UTC időzónára a "2018-12-01T05:00:00Z" formátumban kerül alkalmazásra. <br> A tulajdonságok null értékűek lehetnek, ami azt jelenti, hogy az adatkészletre nem alkalmaz fájlattribútum-szűrőt. Ha `modifiedDatetimeStart` datetime értékkel `modifiedDatetimeEnd` rendelkezik, de NULL értékű, az azt jelenti, hogy azokat a fájlokat, amelyek utolsó módosított attribútuma nagyobb vagy egyenlő a datetime értékkel, ki vannak jelölve. Ha `modifiedDatetimeEnd` datetime értékkel `modifiedDatetimeStart` rendelkezik, de NULL értékű, az azt jelenti, hogy azok a fájlok, amelyek utolsó módosított attribútuma kisebb, mint a datetime érték, ki vannak jelölve. | Nem                                            |
| modifiedDatetimeEnd      | Ugyanaz, mint fent.                                               | Nem                                            |
| maxConcurrentConnections | A tárolóhoz egyidejűleg csatlakozó kapcsolatok száma. Csak akkor adja meg, ha korlátozni szeretné az egyidejű kapcsolatot az adattárhoz. | Nem                                            |

**Példa:**

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

Az Azure Data Lake Store Gen1 `storeSettings` a következő tulajdonságokat támogatja a formátumalapú példánygyűjtő beállításai alatt:

| Tulajdonság                 | Leírás                                                  | Kötelező |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | A típustulajdonságot `storeSettings` az **AzureDataLakeStoreWriteSettings**beállításra kell állítani. | Igen      |
| copyBehavior (Másként)             | Azt a másolási viselkedést határozza meg, amikor a forrás fájlalapú adattárból származó fájlok.<br/><br/>Az engedélyezett értékek a következők:<br/><b>- MegőrzéseHierarchy (alapértelmezett)</b>: Megőrzi a fájlhierarchiát a célmappában. A forrásfájl forrásmappához viszonyított elérési útja megegyezik a célfájl nak a célmappához viszonyított elérési útvonalával.<br/><b>- Ahierarcha összeolvasztása</b>: A forrásmappából származó összes fájl a célmappa első szintjén található. A célfájlok nak automatikusan generált neve van. <br/><b>- MergeFiles</b>: Egyesíti az összes fájlt a forrás mappát egy fájlt. Ha a fájlnév meg van adva, az egyesített fájlnév a megadott név. Ellenkező esetben ez egy automatikusan létrehozott fájlnév. | Nem       |
| lejáratDateTime | Az írott fájlok lejárati idejét adja meg. Az idő az UTC időre a "2020-03-01T08:00:00Z" formátumban kerül alkalmazásra. Alapértelmezés szerint NULL, ami azt jelenti, hogy az írott fájlok soha nem járnak le. | Nem |
| maxConcurrentConnections | Az adattárhoz egyidejűleg csatlakozó kapcsolatok száma. Csak akkor adja meg, ha korlátozni szeretné az egyidejű kapcsolatot az adattárhoz. | Nem       |

**Példa:**

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

### <a name="folder-and-file-filter-examples"></a>Példák mappa- és fájlszűrőre

Ez a szakasz a mappa elérési útjának és a helyettesítő szűrőkkel rendelkező fájlnévnek a viselkedését ismerteti.

| folderPath | fileName | Rekurzív | A forrásmappa szerkezete és a szűrő eredménye (a **félkövérrel szedett** fájlok beolvasása)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (Üres, használja az alapértelmezett) | hamis | MappaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Fájl1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Fájl2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl5.csv<br/>Egy másikFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl6.csv |
| `Folder*` | (Üres, használja az alapértelmezett) | igaz | MappaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Fájl1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Fájl2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fájl3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fájl4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fájl5.csv**<br/>Egy másikFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl6.csv |
| `Folder*` | `*.csv` | hamis | MappaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Fájl1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl5.csv<br/>Egy másikFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl6.csv |
| `Folder*` | `*.csv` | igaz | MappaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Fájl1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fájl3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fájl5.csv**<br/>Egy másikFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl6.csv |

### <a name="examples-of-behavior-of-the-copy-operation"></a>Példák a másolási művelet viselkedésére

Ez a szakasz a másolási művelet különböző kombinációinak `recursive` és `copyBehavior` értékeinek eredő viselkedését ismerteti.

| Rekurzív | copyBehavior (Másként) | Forrásmappa szerkezete | Eredményként kapott cél |
|:--- |:--- |:--- |:--- |
| igaz |megőrzési hierarchia | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl5 | A célmappa1 ugyanazzal a struktúrával jön létre, mint a forrás:<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl5. |
| igaz |összeolvasztási hierarchia | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl5 | A célmappa1 a következő struktúrával jön létre: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;fájl automatikusan létrehozott neve1<br/>&nbsp;&nbsp;&nbsp;&nbsp;fájl 2 automatikusan létrehozott neve<br/>&nbsp;&nbsp;&nbsp;&nbsp;fájl 3 automatikusan létrehozott neve<br/>&nbsp;&nbsp;&nbsp;&nbsp;fájl 4 automatikusan létrehozott neve<br/>&nbsp;&nbsp;&nbsp;&nbsp;fájl automatikusan létrehozott neve |
| igaz |mergeFiles | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl5 | A célmappa1 a következő struktúrával jön létre: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;A fájl1 + File2 + File3 + File4 + File5 tartalom egyetlen fájlba egyesül, automatikusan generált fájlnévvel. |
| hamis |megőrzési hierarchia | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl5 | A célmappa1 a következő struktúrával jön létre:<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/><br/>A Fájl3, A Fájl4 és a File5 almappája nem kerül felvételre. |
| hamis |összeolvasztási hierarchia | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl5 | A célmappa1 a következő struktúrával jön létre:<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;fájl automatikusan létrehozott neve1<br/>&nbsp;&nbsp;&nbsp;&nbsp;fájl 2 automatikusan létrehozott neve<br/><br/>A Fájl3, A Fájl4 és a File5 almappája nem kerül felvételre. |
| hamis |mergeFiles | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl5 | A célmappa1 a következő struktúrával jön létre:<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;A Fájl1 + File2 tartalom egyetlen, automatikusan generált fájlnévvel rendelkező fájlba egyesül. fájl automatikusan létrehozott neve1<br/><br/>A Fájl3, A Fájl4 és a File5 almappája nem kerül felvételre. |

## <a name="preserve-acls-to-data-lake-storage-gen2"></a>A Data Lake Storage Gen2 rendszerhez tartozó ACL-ek megőrzése

>[!TIP]
>Az Azure Data Lake Storage Gen1-ből a Gen2-be történő adatok másolása általában az [Azure Data Lake Storage Gen1 adatainak másolása a Gen2-be az Azure Data Factory segítségével](load-azure-data-lake-storage-gen2-from-gen1.md) című témakörben található az átjárási és gyakorlati tanácsok értendők.

Ha a Data Lake Storage Gen1-ről a Data Lake Storage Gen2-re való frissítéskor a hozzáférés-vezérlési listákat (ACL-k) és az adatfájlokat szeretné replikálni, olvassa el az [ACL-ok megőrzése a Data Lake Storage Gen1 szolgáltatásból című témakört.](copy-activity-preserve-metadata.md#preserve-acls)

## <a name="mapping-data-flow-properties"></a>Adatfolyam tulajdonságainak leképezése

Amikor átalakítja az adatokat az adatfolyamban, olvashatés írhat fájlokat az Azure Data Lake Storage Gen1-ből JSON, Avro, Detagolt szöveg vagy Parketta formátumban. További információ: [forrásátalakítás](data-flow-source.md) és [a fogadó átalakítása](data-flow-sink.md) a leképezési adatfolyam-szolgáltatásban.

### <a name="source-transformation"></a>Forrás átalakítása

A forrásátalakítás, beolvasni egy tároló, mappa vagy egyéni fájl az Azure Data Lake Storage Gen1. A **Forrás beállítások** lapon kezelheti a fájlok olvasásának módját. 

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

A fogadó átalakítása, írhat egy tárolóba vagy mappába az Azure Data Lake Storage Gen1. a **Beállítások** lapon kezelheti a fájlok írásmódját.

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
| type | Az adatkészlet típustulajdonságát **AzureDataLakeStoreFile (AzureDataStoreFile)** típusú tulajdonságra kell állítani. |Igen |
| folderPath | A Data Lake Store mappájának elérési útja. Ha nincs megadva, akkor a gyökérre mutat. <br/><br/>A helyettesítő karakter szűrő támogatott. Az engedélyezett `*` helyettesítő karakterek (nulla vagy `?` több karakternek felelnek meg) és (nulla vagy egy karakternek felelnek meg). Menekülésre használható, `^` ha a tényleges mappanévben helyettesítő karakter szerepel, vagy ez az escape-karakter van benne. <br/><br/>Például: rootfolder/subfolder/. További példák a [Mappa- és fájlszűrő-példákban](#folder-and-file-filter-examples)találhatók. |Nem |
| fileName | A megadott "folderPath" alatt található fájlok neve vagy helyettesítő karaktere. Ha nem ad meg értéket ehhez a tulajdonsághoz, az adatkészlet a mappában lévő összes fájlra mutat. <br/><br/>Szűrő esetén a helyettesítő `*` karakterek megengedettek (nulla `?` vagy több karakternek felelnek meg) és (nulla vagy egy karakternek felelnek meg).<br/>- 1. példa:`"fileName": "*.csv"`<br/>- 2. példa:`"fileName": "???20180427.txt"`<br/>Menekülésre használható, `^` ha a tényleges fájlnévben helyettesítő karakter szerepel, vagy ez az escape-karakter van benne.<br/><br/>Ha a fájlnév nincs megadva egy kimeneti adatkészlethez, és a **preserveHierarchy** nincs megadva a tevékenységfogadóban, a másolási tevékenység automatikusan létrehozza a fájlnevet a következő mintával: "*Data.[ tevékenység futtatási azonosító azonosítója GUID]. [GUID, ha összeolvasztási hierarchia]. [formátum, ha konfigurálva van]. [tömörítés, ha be van állítva]*", például "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz". Ha táblázatos forrásból egy táblanév használatával másolunk lekérdezés helyett, a névminta a "*[táblanév].[ formátum]. [tömörítés, ha konfigurálva van]*", például "MyTable.csv". |Nem |
| modifiedDatetimeStart | A Fájlok szűrője az utolsó módosítás attribútum alapján. A fájlok akkor kerülnek kiválasztásra, ha az `modifiedDatetimeStart` `modifiedDatetimeEnd`utolsó módosítás idáig a között és a közötti időtartományon belül van. Az idő az UTC időzónára a "2018-12-01T05:00:00Z" formátumban kerül alkalmazásra. <br/><br/> Az adatok mozgásának általános teljesítményét befolyásolja, ha engedélyezi ezt a beállítást, ha hatalmas mennyiségű fájllal rendelkező fájlszűrőt szeretne végezni. <br/><br/> A tulajdonságok null értékűek lehetnek, ami azt jelenti, hogy az adatkészletre nem alkalmaz fájlattribútum-szűrőt. Ha `modifiedDatetimeStart` datetime értékkel `modifiedDatetimeEnd` rendelkezik, de NULL értékű, az azt jelenti, hogy azokat a fájlokat, amelyek utolsó módosított attribútuma nagyobb vagy egyenlő a datetime értékkel, ki vannak jelölve. Ha `modifiedDatetimeEnd` datetime értékkel `modifiedDatetimeStart` rendelkezik, de NULL értékű, az azt jelenti, hogy azok a fájlok, amelyek utolsó módosított attribútuma kisebb, mint a datetime érték, ki vannak jelölve.| Nem |
| modifiedDatetimeEnd | A Fájlok szűrője az utolsó módosítás attribútum alapján. A fájlok akkor kerülnek kiválasztásra, ha az `modifiedDatetimeStart` `modifiedDatetimeEnd`utolsó módosítás idáig a között és a közötti időtartományon belül van. Az idő az UTC időzónára a "2018-12-01T05:00:00Z" formátumban kerül alkalmazásra. <br/><br/> Az adatok mozgásának általános teljesítményét befolyásolja, ha engedélyezi ezt a beállítást, ha hatalmas mennyiségű fájllal rendelkező fájlszűrőt szeretne végezni. <br/><br/> A tulajdonságok null értékűek lehetnek, ami azt jelenti, hogy az adatkészletre nem alkalmaz fájlattribútum-szűrőt. Ha `modifiedDatetimeStart` datetime értékkel `modifiedDatetimeEnd` rendelkezik, de NULL értékű, az azt jelenti, hogy azokat a fájlokat, amelyek utolsó módosított attribútuma nagyobb vagy egyenlő a datetime értékkel, ki vannak jelölve. Ha `modifiedDatetimeEnd` datetime értékkel `modifiedDatetimeStart` rendelkezik, de NULL értékű, az azt jelenti, hogy azok a fájlok, amelyek utolsó módosított attribútuma kisebb, mint a datetime érték, ki vannak jelölve.| Nem |
| Formátum | Ha a fájlokat úgy szeretné másolni, ahogy a fájlalapú tárolók között (bináris másolat), hagyja ki a formátum szakaszt a bemeneti és kimeneti adatkészlet-definíciókban is.<br/><br/>Ha meghatározott formátumú fájlokat szeretne elemezni vagy létrehozni, a következő fájlformátum-típusok támogatottak: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**és **ParquetFormat**. Állítsa a **formátum** alatti **típustulajdonságot** ezen értékek egyikére. További információ: Text format , JSON format , Avro format , Orc format és [Parquet format](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) [(Szöveg formátum,](supported-file-formats-and-compression-codecs-legacy.md#text-format) [JSON formátum](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Avro formátum](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Orc format](supported-file-formats-and-compression-codecs-legacy.md#orc-format)) című szakasz. |Nem (csak bináris másolási forgatókönyv esetén) |
| tömörítés | Adja meg az adatok tömörítésének típusát és szintjét. További információt a [Támogatott fájlformátumok és tömörítési kodekek című témakörben talál.](supported-file-formats-and-compression-codecs-legacy.md#compression-support)<br/>A támogatott típusok: **GZip**, **Deflate,** **BZip2**és **ZipDeflate**.<br/>A támogatott szintek **optimálisak** és **leggyorsabbak.** |Nem |

>[!TIP]
>A mappa összes fájljának másolásához adja meg a csak **mappaPath** parancsot.<br>Ha egy adott nevű fájlt szeretne másolni, adja meg a **folderPath** mappát egy mappaellel, **a fájlnév** pedig a fájlnevet.<br>Ha a fájlok egy részhalmazát egy mappába szeretné másolni, adja meg a **folderPath** mappát egy mapparészsel, **a fileName-t** helyettesítő karakterrel. 

**Példa:**

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

### <a name="legacy-copy-activity-source-model"></a>Örökölt másolási tevékenység forrásmodellje

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A `type` másolási tevékenységforrás tulajdonságát **az AzureDataLakeStoreSource (AzureDataLakeStoreSource)** tulajdonságra kell állítani. |Igen |
| Rekurzív | Azt jelzi, hogy az adatok olvasása rekurzív anameddig az almappákból vagy csak a megadott mappából történik. Ha `recursive` igaz értékre van állítva, és a fogadó fájlalapú tároló, a program nem másolja vagy hozza létre az üres mappát vagy almappát a fogadóban. Az engedélyezett értékek **igazak** (alapértelmezettek) és **hamisak.** | Nem |
| maxConcurrentConnections | Az adattárhoz egyidejűleg csatlakozó kapcsolatok száma. Csak akkor adja meg, ha korlátozni szeretné az egyidejű kapcsolatot az adattárhoz. | Nem |

**Példa:**

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

### <a name="legacy-copy-activity-sink-model"></a>Örökölt másolási tevékenység fogadójának modellje

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A `type` másolási tevékenység fogadójának tulajdonságát **Az AzureDataLakeStoreSink**beállításra kell állítani. |Igen |
| copyBehavior (Másként) | Azt a másolási viselkedést határozza meg, amikor a forrás fájlalapú adattárból származó fájlok.<br/><br/>Az engedélyezett értékek a következők:<br/><b>- MegőrzéseHierarchy (alapértelmezett)</b>: Megőrzi a fájlhierarchiát a célmappában. A forrásfájl forrásmappához viszonyított elérési útja megegyezik a célfájl nak a célmappához viszonyított elérési útvonalával.<br/><b>- Ahierarcha összeolvasztása</b>: A forrásmappából származó összes fájl a célmappa első szintjén található. A célfájlok nak automatikusan generált neve van. <br/><b>- MergeFiles</b>: Egyesíti az összes fájlt a forrás mappát egy fájlt. Ha a fájlnév meg van adva, az egyesített fájlnév a megadott név. Ellenkező esetben a fájlnév automatikusan létrejön. | Nem |
| maxConcurrentConnections | Az adattárhoz egyidejűleg csatlakozó kapcsolatok száma. Csak akkor adja meg, ha korlátozni szeretné az egyidejű kapcsolatot az adattárhoz. | Nem |

**Példa:**

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

## <a name="next-steps"></a>További lépések

A forrásként támogatott és fogadóként az Azure Data Factory másolási tevékenysége által támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats)című témakörben tetszhet.
