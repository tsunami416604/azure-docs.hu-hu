---
title: "Adatok másolása az Azure SQL Data Warehouse Data Factory használatával |} Microsoft Docs"
description: "Útmutató: adatok másolása az Azure SQL Data Warehouse támogatott forrás áruházakból (vagy) az SQL Data Warehouse támogatott fogadó áruházak Data Factory használatával."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2017
ms.author: jingwang
ms.openlocfilehash: fb67c70d1e85307c38a185e2b47729880880d55b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="copy-data-to-or-from-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Másolja a adatok vagy az Azure SQL Data Warehouse Azure Data Factory használatával
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1. verzió – Általánosan elérhető](v1/data-factory-azure-sql-data-warehouse-connector.md)
> * [2. verzió – Előzetes verzió](connector-azure-sql-data-warehouse.md)

Ez a cikk ismerteti, hogyan használható a másolási tevékenység során az Azure Data Factory és az Azure SQL Data Warehouse-adatok másolása. Buildekről nyújtanak a [másolása tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [Azure SQL Data Warehouse-összekötő a V1](v1/data-factory-azure-sql-data-warehouse-connector.md).

## <a name="supported-scenarios"></a>Támogatott esetek

A/az Azure SQL Data Warehouse-adatok másolása bármely támogatott fogadó adattár, vagy bármely támogatott forrás adattár az Azure SQL Data Warehouse-adatok másolása. Adattároló források/mosdók, a másolási tevékenység által támogatott listájáért lásd: a [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Pontosabban az Azure SQL Data Warehouse-összekötő támogatja:

- SQL-hitelesítéssel adatok másolását.
- Forrásként SQL-lekérdezést vagy tárolt eljárás használatával adatok beolvasása.
- Adatokat betöltése, a fogadó, **PolyBase** vagy a tömeges beszúrás. Az előző van **ajánlott** másolási teljesítmény növelése érdekében.

## <a name="getting-started"></a>Bevezetés
A másolási tevékenység során .NET SDK, a Python SDK, az Azure PowerShell, a REST API-t vagy a Azure Resource Manager-sablon használatával hozhat létre egy folyamatot. Lásd: [másolási tevékenység oktatóanyag](quickstart-create-data-factory-dot-net.md) hozzon létre egy folyamatot a másolási tevékenység részletes útmutatóját.

A következő szakaszok részletesen bemutatják, amely segítségével határozza meg a Data Factory tartozó entitások és Azure SQL Data Warehouse-összekötő tulajdonságait.

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok

Azure SQL Data Warehouse kapcsolódó szolgáltatás támogatott a következő tulajdonságokkal:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot kell beállítani: **AzureSqlDW** | Igen |
| connectionString |Adja meg a connectionString tulajdonság az Azure SQL Data Warehouse-példány való kapcsolódáshoz szükséges adatokat. Csak az alapszintű hitelesítést is támogatja. Ez a mező megjelölése a SecureString. |Igen |
| connectVia | A [integrációs futásidejű](concepts-integration-runtime.md) csatlakozni az adattárolóhoz használandó. Használhat Azure integrációs futásidejű vagy Self-hosted integrációs futásidejű (amennyiben az adattároló magánhálózaton található). Ha nincs megadva, akkor használja az alapértelmezett Azure integrációs futásidejű. |Nem |


> [!IMPORTANT]
> Konfigurálása [Azure SQL Data Warehouse tűzfal](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) és az adatbázis-kiszolgálót [a kiszolgálóhoz való hozzáféréshez Azure-szolgáltatások engedélyezése](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Ezenkívül ha a másolt adatok az Azure SQL Data Warehouse a külső Azure többek között a helyszíni adatforrásokból Self-hosted integrációs futtatási idő mellett, konfigurálni a megfelelő IP-címtartományt, a gép, amely adatokat küld az Azure SQL Data Adatraktár.

**Példa**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
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

Szakaszok és meghatározása adatkészletek esetében elérhető tulajdonságok teljes listájáért tekintse meg az adatkészletek cikket. Ez a témakör az Azure SQL Data Warehouse dataset által támogatott tulajdonságokról.

Adatok másolása az Azure SQL Data Warehouse/állítsa be a type tulajdonságot az adathalmaz **AzureSqlDWTable**. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adathalmaz értékre kell állítani: **AzureSqlDWTable** | Igen |
| tableName |A tábla vagy nézet társított szolgáltatásnak Azure SQL Data Warehouse-példány neve hivatkozik. | Igen |

**Példa**

```json
{
    "name": "AzureSQLDWDataset",
    "properties":
    {
        "type": "AzureSqlDWTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Data Warehouse linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és a rendelkezésre álló tevékenységek meghatározó tulajdonságok teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a témakör az Azure SQL Data Warehouse-forrás és a fogadó által támogatott tulajdonságokról.

### <a name="azure-sql-data-warehouse-as-source"></a>Az SQL Data Warehouse forrásaként

Adatok másolása az Azure SQL Data Warehouse, állítsa be a forrás típusa a másolási tevékenység **SqlDWSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakasz:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot a másolási tevékenység forrás értékre kell állítani: **SqlDWSource** | Igen |
| sqlReaderQuery |Az egyéni SQL-lekérdezés segítségével adatokat olvasni. Példa: `select * from MyTable`. |Nem |
| sqlReaderStoredProcedureName |A tárolt eljárás, amely adatokat olvas a forrástábla neve. Az utolsó SQL-utasítás a következő tárolt eljárást a SELECT utasítással kell lennie. |Nem |
| storedProcedureParameters |A tárolt eljárás paramétereit.<br/>Két érték engedélyezett: a név/érték párok. Nevét és a kis-és a paraméterek meg kell egyeznie a nevek és a kis-és nagybetűhasználat a tárolt eljárás paramétereit. |Nem |

**Vegye figyelembe a következő szempontok:**

- Ha a **sqlReaderQuery** van megadva a SqlSource, a másolási tevékenység fut ez a lekérdezés az adatok lekérdezése az Azure SQL Data Warehouse forrás. Másik lehetőségként megadhat tárolt eljárás megadásával a **sqlReaderStoredProcedureName** és **storedProcedureParameters** (Ha a tárolt eljárás paraméterek fogadja el).
- Ha nem adja meg "sqlReaderQuery" vagy "sqlReaderStoredProcedureName", az adatkészlet JSON "structure" szakaszában meghatározott oszlopokat segítségével olyan lekérdezést (`select column1, column2 from mytable`) az Azure SQL Data Warehouse futtatásához. Az adatkészlet-definícióban nem rendelkezik a "structure", ha minden kiválasztott oszlop. a táblából.
- Amikor **sqlReaderStoredProcedureName**, továbbra is meg kell adnia egy helyőrző **tableName** az adatkészlet JSON tulajdonság.

**Példa: SQL lekérdezést használva.**

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL DW input dataset name>",
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
                "type": "SqlDWSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Példa: a tárolt eljárás használatával**

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL DW input dataset name>",
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
                "type": "SqlDWSource",
                "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
                "storedProcedureParameters": {
                    "stringData": { "value": "str3" },
                    "identifier": { "value": "$$Text.Format('{0:yyyy}', <datetime parameter>)", "type": "Int"}
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**A tárolt eljárás definíciója:**

```sql
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
     select *
     from dbo.UnitTestSrcTable
     where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="azure-sql-data-warehouse-as-sink"></a>Az SQL Data Warehouse mint fogadó

Adatok másolása az Azure SQL Data Warehouse, állítsa a fogadó típusa a másolási tevékenység **SqlDWSink**. A következő tulajdonságok támogatottak a másolási tevékenység **fogadó** szakasz:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot a másolási tevékenység forrás értékre kell állítani: **SqlDWSink** | Igen |
| allowPolyBase |Azt jelzi, hogy (ha alkalmazható), a PolyBase használata helyett BULKINSERT mechanizmus. <br/><br/> **Az ajánlott módszer az adatok betöltése az SQL Data Warehouse PolyBase a használata.** Lásd: [használja a PolyBase az adatok betöltése az Azure SQL Data Warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse) szakaszban a korlátozások és részleteit.<br/><br/>Két érték engedélyezett: **igaz** (alapértelmezett), és **hamis**.  |Nem |
| kapcsolódó polyBaseSettings |Egy csoport, amely tulajdonságok megadott, amikor a **allowPolybase** tulajdonsága **igaz**. |Nem |
| rejectValue |Megadja a szám vagy is el kell utasítani, mielőtt a lekérdezés nem sikerült sorokat százalékát.<br/><br/>További információ a PolyBase utasítsa el a beállítások elemre a **argumentumok** szakasza [külső tábla létrehozása (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx) témakör. <br/><br/>Engedélyezett értékek: 0 (alapértelmezés), 1, 2,... |Nem |
| rejectType |Határozza meg, hogy a rejectValue beállítás konstans értéket vagy százalékában van megadva.<br/><br/>Két érték engedélyezett: **érték** (alapértelmezett), és **százalékos**. |Nem |
| rejectSampleValue |Mielőtt a PolyBase újraszámítja a visszautasított sorok százalékát beolvasandó sorok számát határozza meg.<br/><br/>Engedélyezett értékek: 1, 2,... |Igen, ha **rejectType** van **százalékos aránya** |
| useTypeDefault |Megadja, hogyan legyen kezelve tagolt szövegfájlok a hiányzó értékeket, amikor a PolyBase kér le adatokat a szövegfájlból.<br/><br/>Ezt a tulajdonságot, az argumentumok ismertető részben olvashat [létrehozása külső FÁJLFORMÁTUM (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx).<br/><br/>Két érték engedélyezett: **igaz**, **hamis** (alapértelmezett). |Nem |
| WriteBatchSize |Szúr be az SQL-tábla adatokat, amikor a puffer mérete eléri writeBatchSize. Csak akkor, ha a rendszer nem használja a PolyBase vonatkozik.<br/><br/>Két érték engedélyezett: egész szám (sorok száma). |Nem (alapértelmezett beállítás 10000) |
| writeBatchTimeout |Várakozási idő a kötegelt beszúrási művelet befejezését, mielőtt azt az időkorlátot. Csak akkor, ha a rendszer nem használja a PolyBase vonatkozik.<br/><br/>Két érték engedélyezett: timespan. Példa: "00: 30:00" (30 perc). |Nem |
| preCopyScript |Adjon meg egy SQL-lekérdezés végrehajtása előtt az adatok írása az Azure SQL Data Warehouse minden egyes futtatásához a másolási tevékenység. Ez a tulajdonság segítségével törölje az előre betöltött adatokat. |Nem |(#repeatability során-másolási). |A lekérdezési utasítást. |Nem |

**Példa**

```json
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true,
    "polyBaseSettings":
    {
        "rejectType": "percentage",
        "rejectValue": 10.0,
        "rejectSampleValue": 100,
        "useTypeDefault": true
    }
}
```

További való betöltése az SQL Data Warehouse hatékonyan a következő szakaszban a PolyBase segítségével.

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>Adatok betöltése az Azure SQL Data Warehouse PolyBase segítségével

Használatával  **[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide)**  egy hatékony módszer a nagy mennyiségű adatok betöltését az Azure SQL Data Warehouse nagy átviteli sebességgel. A teljesítmény a nagy nyereség helyett az alapértelmezett BULKINSERT mechanizmus a PolyBase használatával tekintheti meg. Lásd: [teljesítmény hivatkozási szám másolása](copy-activity-performance.md#performance-reference) a részletes összehasonlítását. A használati esetek bemutatóért lásd: [1 TB-os betöltése az Azure SQL Data Warehouse a 15 perc Azure Data Factory](connector-azure-sql-data-warehouse.md).

* Ha a forrás adatok **Azure Blob vagy az Azure Data Lake Store**, és a formátuma nem kompatibilis a PolyBase, közvetlenül másolhatja az Azure SQL Data Warehouse PolyBase használatával. Lásd:  **[közvetlen másolása a PolyBase használatával](#direct-copy-using-polybase)**  adatokkal.
* Ha a forrás-tárolót és formátum eredetileg nem támogatott a PolyBase által, használhatja a  **[előkészített másolása a PolyBase használatával](#staged-copy-using-polybase)**  inkább a beállítást. Is biztosít, nagyobb átviteli sebesség automatikusan adatok PolyBase-kompatibilis formátumra való konvertálása, és az adatok tárolása az Azure Blob Storage tárolóban. Majd betölti az SQL Data Warehouse-adatok.

### <a name="direct-copy-using-polybase"></a>A PolyBase használatával közvetlen másolása

SQL Data Warehouse PolyBase közvetlenül támogatja Azure-Blob és az Azure Data Lake Store (egyszerű szolgáltatásnév) forrásként, és az adott fájl formátum követelményeinek. Ha a forrásadatok megfelel a jelen szakaszban bemutatott, közvetlenül átmásolhatja forrás adattár az Azure SQL Data Warehouse PolyBase használatával. Ellenkező esetben használhatja [előkészített másolása a PolyBase használatával](#staged-copy-using-polybase).

> [!TIP]
> Adatok másolása Data Lake Store az SQL Data Warehouse hatékonyan, további információhoz [Azure Data Factory megkönnyíti még és kényelmes elvégzésével nyújt betekintést az adatokat, az SQL Data Warehouse szolgáltatással Data Lake Store használatakor](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/).

A feltételeknek nem felel meg, ha az Azure Data Factory ellenőrzi a beállításait, és automatikusan visszaáll az adatátvitelt jelölik a BULKINSERT mechanizmus.

1. **Forrás társított szolgáltatás** típusa: **AzureStorage** vagy **AzureDataLakeStore**.
2. A **bemeneti adatkészlet** típusa: **AzureBlob** vagy **AzureDataLakeStoreFile**, és írja be a format `type` tulajdonságai **OrcFormat** , vagy **szöveges** , a következő beállításokat:

   1. `rowDelimiter`kell  **\n** .
   2. `nullValue`értéke **üres karakterlánc** (""), vagy `treatEmptyAsNull` értéke **igaz**.
   3. `encodingName`értéke **utf-8**, amely **alapértelmezett** érték.
   4. `escapeChar`, `quoteChar`, `firstRowAsHeader`, és `skipLineCount` nincs megadva.
   5. `compression`lehet **tömörítés**, **GZip**, vagy **Deflate**.

    ```json
    "typeProperties": {
       "folderPath": "<blobpath>",
       "format": {
           "type": "TextFormat",
           "columnDelimiter": "<any delimiter>",
           "rowDelimiter": "\n",
           "nullValue": "",
           "encodingName": "utf-8"
       },
       "compression": {
           "type": "GZip",
           "level": "Optimal"
       }
    },
    ```

3. Nincs nincs `skipHeaderLineCount` beállítás alatt **BlobSource** vagy **AzureDataLakeStore** a másolási tevékenységhez, a folyamat.
4. Nincs nincs `sliceIdentifierColumnName` beállítás alatt **SqlDWSink** a másolási tevékenységhez, a folyamat. (A PolyBase garantálja, hogy minden adat frissül, vagy nem frissül, az egyszeri futtatás. Eléréséhez **ismételhetőség**, használhat `sqlWriterCleanupScript`).
5. Nincs nincs `columnMapping` használatban lévő a kapcsolódó, a másolási tevékenység.

```json
"activities":[
    {
        "name": "CopyFromAzureBlobToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "BlobDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "SqlDwSink",
                "allowPolyBase": true
            }
        }
    }
]
```

### <a name="staged-copy-using-polybase"></a>A PolyBase használatával előkészített másolása

A forrásadatok nem felel meg az előző szakaszban bemutatott, amikor az adatok másolását az átmeneti átmeneti Azure Blob Storage (nem lehet a prémium szintű Storage) keresztül is engedélyezheti. Ebben az esetben Azure Data Factory automatikusan végez átalakítások adatok formátuma elégíteni PolyBase, majd adatok betöltése az SQL Data Warehouse PolyBase segítségével, és majd adattisztítási az ideiglenes adatokat a Blob-tároló az adatokat. Lásd: [előkészített másolási](copy-activity-performance.md#staged-copy) átmeneti Azure Blob keresztül az adatok másolásának működéséről, általában a részletekért.

Ez a funkció használatához hozzon létre egy [Azure Storage társított szolgáltatásnak](connector-azure-blob-storage.md#linked-service-properties) , amely hivatkozik, amely rendelkezik az átmeneti a blob storage Azure Storage-fiókot, majd adja meg a `enableStaging` és `stagingSettings` látható módon a másolási tevékenység tulajdonságai a következő kódot:

```json
"activities":[
    {
        "name": "CopyFromSQLServerToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "SQLServerDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
            },
            "sink": {
                "type": "SqlDwSink",
                "allowPolyBase": true
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": "MyStagingBlob"
            }
        }
    }
]
```

## <a name="best-practices-when-using-polybase"></a>A PolyBase használata esetén ajánlott eljárások

Az alábbi szakaszokban további gyakorlati tanácsok a meglévők közül ismertetett [ajánlott eljárások az Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-best-practices.md).

### <a name="required-database-permission"></a>Adatbázis szükséges engedéllyel

PolyBase használatához igényel az adatok betöltése az SQL Data Warehouse használt felhasználó rendelkezik-e a ["Vezérlő" engedély](https://msdn.microsoft.com/library/ms191291.aspx) a célként megadott adatbázison. Egyik módja, hogy, hogy a felhasználó hozzáadása "db_owner" szerepkör tagjaként. Útmutató a következő ehhez [ebben a szakaszban](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

### <a name="row-size-and-data-type-limitation"></a>Írja be korlátozás sorméret és adatok

A Polybase terhelések esetén egyre korlátozódik betöltése sort is kisebb, mint **1 MB** és VARCHR(MAX), NVARCHAR(MAX) vagy VARBINARY(MAX) nem tölthető be. Tekintse meg [Itt](../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Ha 1 MB-nál nagyobb méretű sorokat tartalmazó forrásadatok, érdemes lehet a forrástáblákból függőleges felosztása több kis megfelelően, ha azok legnagyobb sor mérete nem haladja meg a határértéket. A kisebb táblák majd lehet betölteni. a PolyBase használatával, és egyesíti az Azure SQL Data Warehouse.

### <a name="sql-data-warehouse-resource-class"></a>Az SQL Data Warehouse erőforrás osztály

Lehetséges legjobb teljesítmény elérése érdekében fontolja meg a felhasználói adatok betöltése az SQL Data Warehouse polybase használt nagyobb erőforrásosztály hozzárendelése. Útmutató a következő ehhez [módosíthatja a felhasználói erőforrás osztály példa](../sql-data-warehouse/sql-data-warehouse-develop-concurrency.md#changing-user-resource-class-example).

### <a name="tablename-in-azure-sql-data-warehouse"></a>az Azure SQL Data Warehouse táblanév

A következő táblázat példákat tartalmaz a megadása a **tableName** tulajdonságot az adatkészlet JSON-séma-és táblanevet különböző kombinációjához.

| Megadott adatbázissémát | Tábla neve | tableName JSON tulajdonsága |
| --- | --- | --- |
| dbo |Táblanév |MyTable vagy dbo. MyTable vagy [dbo]. [MyTable] |
| dbo1 |Táblanév |dbo1. MyTable vagy [dbo1]. [MyTable] |
| dbo |My.Table |[My.Table] vagy [dbo]. [My.Table] |
| dbo1 |My.Table |[dbo1]. [My.Table] |

A következő hibát látja, ha problémát a tableName tulajdonsághoz megadott érték lehet. Lásd a megfelelő módon adhatja meg a tableName JSON tulajdonság értékei a táblázatban.

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

### <a name="columns-with-default-values"></a>Az alapértelmezett értékekkel oszlopok

A Data Factory PolyBase szolgáltatás jelenleg csak az azonos számú oszlopot, mint a céltábla fogad el. Tegyük fel például, négy oszlopokkal rendelkező táblát rendelkezik, és az egyik legyen alapértelmezett értékkel van definiálva. A bemeneti adatok továbbra is tartalmaznia kell a négy oszlopot. A 3-oszlop a bemeneti adatkészletet biztosító eredményezné az alábbihoz hasonló hiba:

```
All columns of the table must be specified in the INSERT BULK statement.
```

NULL érték az alapértelmezett érték egy speciális formája, amely. Ha az oszlop nullázható, az adott oszlop a bemeneti adatokat (a blob) üres lehet (nem hiányzik a bemeneti adatkészlet). A PolyBase NULL számukra az Azure SQL Data Warehouse szúrja be.

## <a name="data-type-mapping-for-azure-sql-data-warehouse"></a>Adattípus-hozzárendelése az Azure SQL Data Warehouse

A/az Azure SQL Data Warehouse-adatok másolásakor a következő leképezéseit segítségével az Azure SQL Data Warehouse adattípusok Azure Data Factory ideiglenes adattípusok. Lásd: [séma- és írja be a leképezéseket](copy-activity-schema-and-type-mapping.md) hogyan másolási tevékenység van leképezve a séma- és adatok típusa a fogadó tájékozódhat.

| Az Azure SQL Data Warehouse-adattípus | Data factory ideiglenes adattípus |
|:--- |:--- |
| bigint |Int64 |
| Bináris |Byte] |
| bit |Logikai érték |
| Karakter |Karakterlánc, Char] |
| Dátum |Dátum és idő |
| Dátum és idő |Dátum és idő |
| datetime2 |Dátum és idő |
| datetimeoffset |DateTimeOffset |
| Decimális |Decimális |
| A FILESTREAM attribútum (varbinary(max)) |Byte] |
| Lebegőpontos |Dupla |
| Kép |Byte] |
| int |Int32 |
| pénz |Decimális |
| nchar |Karakterlánc, Char] |
| ntext |Karakterlánc, Char] |
| Numerikus |Decimális |
| nvarchar |Karakterlánc, Char] |
| valós |Egyetlen |
| ROWVERSION |Byte] |
| smalldatetime |Dátum és idő |
| smallint |Int16 |
| kis pénz típusú értéknél |Decimális |
| sql_variant |Objektum * |
| Szöveg |Karakterlánc, Char] |
| time |A TimeSpan |
| időbélyeg |Byte] |
| tinyint |Bájt |
| egyedi azonosító |GUID |
| varbinary |Byte] |
| varchar |Karakterlánc, Char] |
| xml |XML |

## <a name="next-steps"></a>Következő lépések
Támogatott források és mosdók által a másolási tevékenység során az Azure Data Factory adattárolókhoz listájáért lásd: [adattárolókhoz támogatott](copy-activity-overview.md##supported-data-stores-and-formats).