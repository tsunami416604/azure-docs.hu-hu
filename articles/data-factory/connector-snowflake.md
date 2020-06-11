---
title: Adatok másolása a-ból vagy a hópehely-ba
description: Megtudhatja, hogyan másolhat adatokat a és a helyről a hópehely-be a Data Factory használatával.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/15/2020
ms.openlocfilehash: 74e2c452d229373d271225dcbb28359b6af1524d
ms.sourcegitcommit: eeba08c8eaa1d724635dcf3a5e931993c848c633
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/10/2020
ms.locfileid: "84670497"
---
# <a name="copy-data-from-and-to-snowflake-by-using-azure-data-factory"></a>Adatok másolása a-ből és a hópehely-ből a Azure Data Factory használatával

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok a és a hópehely típusú másolásához. A Azure Data Factoryről a [bevezető cikkben](introduction.md)olvashat bővebben.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez a hópehely összekötő a következő tevékenységek esetében támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrix-](copy-activity-overview.md) táblázattal
- [Keresési tevékenység](control-flow-lookup-activity.md)

Másolási tevékenység esetén ez a hópehely összekötő a következő funkciókat támogatja:

- Adatok másolása a hópehely-ból, amely a hópehely [másolatát használja a [Location]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html) paranccsal a legjobb teljesítmény eléréséhez.
- Másolja az adataikat a hópehely-ba, amely kihasználja a hópehely [másolását a [table]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html) parancsba a legjobb teljesítmény elérése érdekében. Támogatja a hópehely-t az Azure-ban.

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük azokat a tulajdonságokat, amelyek a hópehely-összekötőhöz tartozó Data Factory entitásokat határozzák meg.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A hópehely társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság         | Leírás                                                  | Kötelező |
| :--------------- | :----------------------------------------------------------- | :------- |
| típus             | A Type tulajdonságot a **hópehely**értékre kell beállítani.              | Yes      |
| connectionString | Konfigurálja a [teljes fiók nevét](https://docs.snowflake.net/manuals/user-guide/connecting.html#your-snowflake-account-name) (beleértve a régiót és a felhőalapú platformot azonosító további szegmenseket), a felhasználónevet, a jelszót, az adatbázist és a tárházat. A hópehely-példányhoz való kapcsolódáshoz válassza a JDBC kapcsolati karakterláncot. A jelszót Azure Key Vault is elvégezheti. További részletekért tekintse meg a táblázat alatti példákat, és [tárolja a hitelesítő adatokat Azure Key Vault](store-credentials-in-key-vault.md) cikkben.| Yes      |
| Connectvia tulajdonsággal       | Az adattárhoz való csatlakozáshoz használt [integrációs](concepts-integration-runtime.md) modul. Használhat Azure Integration Runtime vagy saját üzemeltetésű integrációs modult (ha az adattár egy magánhálózaton található). Ha nincs megadva, az alapértelmezett Azure Integration Runtime használja. | No       |

**Például**

```json
{
    "name": "SnowflakeLinkedService",
    "properties": {
        "type": "Snowflake",
        "typeProperties": {
            "connectionString": "jdbc:snowflake://<accountname>.snowflakecomputing.com/?user=<username>&password=<password>&db=<database>&warehouse=<warehouse>(optional)"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Jelszó Azure Key Vaultban:**

```json
{
    "name": "SnowflakeLinkedService",
    "properties": {
        "type": "Snowflake",
        "typeProperties": {
            "connectionString": "jdbc:snowflake://<accountname>.snowflakecomputing.com/?user=<username>&db=<database>&warehouse=<warehouse>(optional)",
            "password": {
                "type": "AzureKeyVaultSecret",
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>",
                    "type": "LinkedServiceReference"
                }, 
                "secretName": "<secretName>"
            }
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

A hópehely adatkészlet a következő tulajdonságokat támogatja:

| Tulajdonság  | Leírás                                                  | Kötelező                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| típus      | Az adatkészlet Type tulajdonságát **SnowflakeTable**értékre kell állítani. | Yes                         |
| séma | A séma neve. |Nem, forrás, igen, fogadó  |
| tábla | A tábla vagy nézet neve. |Nem, forrás, igen, fogadó  |

**Például**

```json
{
    "name": "SnowflakeDataset",
    "properties": {
        "type": "SnowflakeTable",
        "typeProperties": {
            "schema": "<Schema name for your Snowflake database>",
            "table": "<Table name for your Snowflake database>"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "linkedServiceName": {
            "referenceName": "<name of linked service>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját a [folyamatok](concepts-pipelines-activities.md) című cikkben találja. Ez a szakasz a hópehely forrás és a fogadó által támogatott tulajdonságok listáját tartalmazza.

### <a name="snowflake-as-the-source"></a>A hópehely forrása

A hópehely-összekötő a következőhöz tartozó [[Location]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html) parancsban használja a hópehely másolást a legjobb teljesítmény eléréséhez.

* Ha a fogadó adattár és a formátum natív módon támogatott a hópehely COPY paranccsal, a másolási tevékenység használatával közvetlenül másolhat a hópehely-ből a fogadóba. Részletekért lásd: [közvetlen másolás a hópehely-ból](#direct-copy-from-snowflake).
* Egyéb esetben a beépített, [szakaszos másolást használja a hópehely-ből](#staged-copy-from-snowflake).

Az adatok a hópehely-ből történő másolásához a másolási tevékenység **forrása** szakaszban a következő tulajdonságok támogatottak:

| Tulajdonság                     | Leírás                                                  | Kötelező |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| típus                         | A másolási tevékenység forrásának Type tulajdonságát **SnowflakeSource**értékre kell állítani. | Yes      |
| lekérdezés          | Meghatározza azt az SQL-lekérdezést, amely a hópehely adatok olvasására szolgál.<br>A tárolt eljárás végrehajtása nem támogatott. | No       |
| exportSettings | A hópehely adatok lekérésére használt speciális beállítások. Az által támogatott példányok konfigurálásához másolja az ADF-et az utasítás meghívásakor. | No       |
| ***Alatt `exportSettings` :*** |  |  |
| típus | Az exportálási parancs típusa **SnowflakeExportCopyCommand**értékre van állítva. | Yes |
| additionalCopyOptions | További másolási lehetőségek a kulcs-érték párok szótáraként megadva. Példák: MAX_FILE_SIZE, FELÜLÍRÁS. További információ a [hópehely másolási lehetőségeiről](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html#copy-options-copyoptions). | No |
| additionalFormatOptions | További fájlformátum-beállítások a Másolás parancshoz, amelyet a kulcs-érték párok szótára biztosít. Példák: DATE_FORMAT, TIME_FORMAT, TIMESTAMP_FORMAT. További információ a [hópehely formátum típusú beállításokról](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html#format-type-options-formattypeoptions). | No |

#### <a name="direct-copy-from-snowflake"></a>Közvetlen másolás a hópehely-ból

Ha a fogadó adattár és a formátum megfelel az ebben a szakaszban ismertetett feltételeknek, a másolási tevékenység használatával közvetlenül másolhat a hópehely-ből a fogadóba. Azure Data Factory ellenőrzi a beállításokat, és sikertelenül futtatja a másolási tevékenységet, ha a feltételek nem teljesülnek.

1. A fogadó **társított szolgáltatás** a **közös hozzáférésű aláírás** -hitelesítéssel rendelkező [**Azure Blob**](connector-azure-blob-storage.md) -típus.

2. A fogadó **adatformátuma** a következő konfigurációkkal rendelkező **parketta** vagy **tagolt szöveg**:

   - A **parketta** formátuma esetén a tömörítési kodek **egyike sem**, a **Snappy**vagy a **LZO**.
   - **Tagolt szöveges** formátum esetén:
     - `rowDelimiter`**\r\n**vagy egyetlen karakter.
     - `compression`nem lehet **tömörítés**, **gzip**, **bzip2**vagy **deflate**.
     - `encodingName`Alapértelmezés szerint marad, vagy az **UTF-8**értékre van állítva.
     - `quoteChar`**dupla idézőjel**, **szimpla idézőjel** vagy **üres karakterlánc** (nincs idézőjel karakter).
3. A másolási tevékenység forrása területen nincs `additionalColumns` megadva.
4. Nincs megadva az oszlop-hozzárendelés.

**Például**

```json
"activities":[
    {
        "name": "CopyFromSnowflake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Snowflake input dataset name>",
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
                "type": "SnowflakeSource",
                "sqlReaderQuery": "SELECT * FROM MyTable",
                "exportSettings": {
                    "type": "SnowflakeExportCopyCommand",
                    "additionalCopyOptions": {
                        "MAX_FILE_SIZE": "64000000",
                        "OVERWRITE": true
                    },
                    "additionalFormatOptions": {
                        "DATE_FORMAT": "'MM/DD/YYYY'"
                    }
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="staged-copy-from-snowflake"></a>Lépcsőzetes másolás a hópehely-ból

Ha a fogadó adattár vagy formátum nem kompatibilis natív módon a hópehely COPY paranccsal, ahogy azt az utolsó szakaszban is említettük, engedélyezze a beépített szakaszos másolást egy ideiglenes Azure Blob Storage-példányon keresztül. Az előkészített másolási szolgáltatással jobb átviteli sebesség-Data Factory exportálhat a hópehely-ből az átmeneti tárolóba, majd az adatok másolása a fogadóba, végül megtisztítja az átmeneti adatait az átmeneti tárolóból. Az adatok átmeneti használatával történő másolásával kapcsolatos részletekért lásd a [szakaszos másolást](copy-activity-performance-features.md#staged-copy) .

A szolgáltatás használatához hozzon létre egy [azure blob Storage társított szolgáltatást](connector-azure-blob-storage.md#linked-service-properties) , amely az Azure Storage-fiókra hivatkozik átmeneti előkészítésként. Ezután adja meg a `enableStaging` és a `stagingSettings` tulajdonságokat a másolási tevékenységben.

> [!NOTE]
>
> Az átmeneti Azure Blob társított szolgáltatásnak a hópehely MÁSOLÁSi parancs által megkövetelt közös hozzáférésű aláírás-hitelesítést kell használnia. 

**Például**

```json
"activities":[
    {
        "name": "CopyFromSnowflake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Snowflake input dataset name>",
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
                "type": "SnowflakeSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                },
                "path": "mystagingpath"
            }
        }
    }
]
```

### <a name="snowflake-as-sink"></a>Hópehely mint fogadó

A hópehely-összekötő a (z) [[table]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html) parancs alatt a legjobb teljesítmény eléréséhez használja a hópehely-példányt. Támogatja az Azure-beli hópehely-adatírást.

* Ha a forrás adattárat és a formátumot natív módon támogatja a hópehely COPY parancs, a másolási tevékenység használatával közvetlenül másolhatja a forrásról a hópehely-ra. Részletekért lásd: [közvetlen másolás a hópehely-](#direct-copy-to-snowflake)ba.
* Ellenkező esetben használja [a beépített szakaszos másolást a hópehely](#staged-copy-to-snowflake)-ra.

Ha az Adatmásolást a hópehely-ba szeretné másolni, a másolási **tevékenység** fogadója részben a következő tulajdonságokat támogatja:

| Tulajdonság          | Leírás                                                  | Kötelező                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| típus              | A másolási tevékenység fogadójának Type tulajdonságát **SnowflakeSink**értékre kell állítani. | Yes                                           |
| preCopyScript     | Adja meg a másolási tevékenység futtatásához szükséges SQL-lekérdezést, mielőtt az összes futtatáskor beírja az adatbevitelt a hópehely-ba. Ezzel a tulajdonsággal törölheti az előre feltöltött adatkészleteket. | No                                            |
| importSettings | *Az adatíráshoz használt speciális beállítások a hópehely-ban. Az által támogatott példányok konfigurálásához másolja az ADF-et az utasítás meghívásakor.* | *Nem* |
| ***Alatt `importSettings` :*** |                                                              |  |
| típus | Az importálási parancs típusa **SnowflakeImportCopyCommand**értékre van állítva. | Yes |
| additionalCopyOptions | További másolási lehetőségek a kulcs-érték párok szótáraként megadva. Példák: ON_ERROR, FORCE, LOAD_UNCERTAIN_FILES. További információ a [hópehely másolási lehetőségeiről](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html#copy-options-copyoptions). | No |
| additionalFormatOptions | További fájlformátum-beállítások a Másolás parancshoz, amelyet a kulcs-érték párok szótára biztosít. Példák: DATE_FORMAT, TIME_FORMAT, TIMESTAMP_FORMAT. További információ a [hópehely formátum típusú beállításokról](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html#format-type-options-formattypeoptions). | No |

#### <a name="direct-copy-to-snowflake"></a>Közvetlen másolás a hópehely-ba

Ha a forrás-adattár és-formátum megfelel az ebben a szakaszban ismertetett feltételeknek, a másolási tevékenység használatával közvetlenül másolhatja a forrásról a hópehely-ra. Azure Data Factory ellenőrzi a beállításokat, és sikertelenül futtatja a másolási tevékenységet, ha a feltételek nem teljesülnek.

1. A **forrásként társított szolgáltatás** az [**Azure Blob**](connector-azure-blob-storage.md) -típus **közös hozzáférésű aláírás** -hitelesítéssel.

2. A **forrásadatok formátuma** a következő konfigurációkkal rendelkező **parketta** vagy **tagolt szöveg** :

   - A **parketta** formátuma esetén a tömörítési kodek **egyike sem**vagy a **Snappy**.

   - **Tagolt szöveges** formátum esetén:
     - `rowDelimiter`**\r\n**vagy egyetlen karakter. Ha a sorok elválasztó karaktere nem "\r\n", akkor `firstRowAsHeader` **false értékűnek**kell lennie, és nincs `skipLineCount` megadva.
     - `compression`nem lehet **tömörítés**, **gzip**, **bzip2**vagy **deflate**.
     - `encodingName`Alapértelmezés szerint balra van állítva, vagy az "UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "BIG5", "EUC-JP", "EUC-KR", "GB18030", "ISO-2022-JP", "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "WINDOWS-1250", "WINDOWS-1251", "WINDOWS-1252", "WINDOWS-1253", "Windows-1254", "Windows-1255".
     - `quoteChar`**dupla idézőjel**, **szimpla idézőjel** vagy **üres karakterlánc** (nincs idézőjel karakter).

3. A másolási tevékenység forrása területen 

   -  `additionalColumns`nincs megadva.
   - Ha a forrás mappa, a `recursive` tulajdonságot igaz értékre kell beállítani.
   - `prefix`, `modifiedDateTimeStart` `modifiedDateTimeEnd` nincsenek megadva.

**Például**

```json
"activities":[
    {
        "name": "CopyToSnowflake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Snowflake output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SnowflakeSink",
                "importSettings": {
                    "type": "SnowflakeImportCopyCommand",
                    "copyOptions": {
                        "FORCE": "TRUE",
                        "ON_ERROR": "SKIP_FILE",
                    },
                    "fileFormatOptions": {
                        "DATE_FORMAT": "YYYY-MM-DD",
                    }
                }
            }
        }
    }
]
```

#### <a name="staged-copy-to-snowflake"></a>Előkészített másolás a hópehely-ba

Ha a fogadó adattár vagy formátum nem kompatibilis natív módon a hópehely COPY paranccsal, ahogy azt az utolsó szakaszban is említettük, engedélyezze a beépített szakaszos másolást egy ideiglenes Azure Blob Storage-példányon keresztül. Az előkészített másolási funkció jobb átviteli sebességet is biztosít – Data Factory automatikusan átalakítja az adatoknak a hópehely adatformátumra vonatkozó követelményeit. Ezután meghívja a másolás parancsot, hogy betöltse az adatbevitelt a hópehely-ba. Végezetül törli az ideiglenes adatait a blob Storage-ból. Az adatok átmeneti használatával történő másolásával kapcsolatos részletekért lásd a [szakaszos másolást](copy-activity-performance-features.md#staged-copy) .

A szolgáltatás használatához hozzon létre egy [azure blob Storage társított szolgáltatást](connector-azure-blob-storage.md#linked-service-properties) , amely az Azure Storage-fiókra hivatkozik átmeneti előkészítésként. Ezután adja meg a `enableStaging` és a `stagingSettings` tulajdonságokat a másolási tevékenységben.

> [!NOTE]
>
> Az átmeneti Azure Blob társított szolgáltatásnak a hópehely MÁSOLÁSi parancs által megkövetelt közös hozzáférésű aláírás-hitelesítést kell használnia.

**Például**

```json
"activities":[
    {
        "name": "CopyToSnowflake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Snowflake output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SnowflakeSink"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                },
                "path": "mystagingpath"
            }
        }
    }
]
```


## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [keresési tevékenységet](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Következő lépések

A forrásként és fogadóként támogatott adattárak listáját a Azure Data Factory másolási tevékenysége című részben tekintheti meg a [támogatott adattárak és-formátumok](copy-activity-overview.md#supported-data-stores-and-formats)című témakörben.
