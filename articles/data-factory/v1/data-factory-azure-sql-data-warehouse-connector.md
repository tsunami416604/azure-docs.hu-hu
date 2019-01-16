---
title: Adatok másolása és- tárolókról az Azure SQL Data Warehouse |} A Microsoft Docs
description: Ismerje meg, hogyan másolhat adatokat és-tárolókról az Azure SQL Data Warehouse az Azure Data Factory használatával
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: d90fa9bd-4b79-458a-8d40-e896835cfd4a
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 72a666db6157300942b966b88d9c3369495b9fd4
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2019
ms.locfileid: "54331234"
---
# <a name="copy-data-to-and-from-azure-sql-data-warehouse-using-azure-data-factory"></a>Adatok másolása és az Azure SQL Data Warehouse az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verzió](data-factory-azure-sql-data-warehouse-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-azure-sql-data-warehouse.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a jelenlegi verzió a Data Factory szolgáltatás használ, tekintse meg [a v2-ben az Azure SQL Data Warehouse-összekötő](../connector-azure-sql-data-warehouse.md).

Ez a cikk azt ismerteti, hogy a másolási tevékenység az Azure Data Factory használata adatok áthelyezéséhez és-tárolókról az Azure SQL Data Warehouse. Épül a [adattovábbítási tevékenységek](data-factory-data-movement-activities.md) című cikket, amely megadja az adatok áthelyezését a másolási tevékenységgel rendelkező általános áttekintése.

> [!TIP]
> A legjobb teljesítmény érdekében, az adatok betöltése az Azure SQL Data Warehouse a polybase szolgáltatást akkor használja. A [bA a PolyBase használatával az adatok betöltése az Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) szakasz részletesen. A használati esetek, olvassa [1 TB adat betöltése az Azure SQL Data Warehouse-bA az Azure Data Factoryvel 15 perc alatt](data-factory-load-sql-data-warehouse.md).

## <a name="supported-scenarios"></a>Támogatott esetek
Adatokat másolja **Azure SQL Data Warehouse-ból** tárolja, a következő adatokat:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

A következő adattárakból származó adatokat másolja **az Azure SQL Data Warehouse**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!TIP]
> Példatípust az adatok az SQL Server vagy az Azure SQL Database az Azure SQL Data Warehouse, ha a tábla nem létezik a cél tárolójában, a Data Factory automatikusan hozhat létre a tábla az SQL Data Warehouse használatával a tábla sémája forrásadattárként. Lásd: [tábla létrehozásához automatikus](#auto-table-creation) részleteiről.

## <a name="supported-authentication-type"></a>Támogatott hitelesítési típus
Az Azure SQL Data Warehouse összekötő alapszintű hitelesítés támogatása.

## <a name="getting-started"></a>Első lépések
Létrehozhat egy folyamatot egy másolási tevékenységgel az adatok áthelyezéséhez és-tárolókról az Azure SQL Data Warehouse különböző eszközök/API-k használatával.

Hozzon létre egy folyamatot, amely átmásolja az adatokat és-tárolókról az Azure SQL Data Warehouse legegyszerűbb módja, hogy az adatok másolása varázslóval. Lásd: [oktatóanyag: Adatok betöltése az SQL Data Warehouse a Data Factory](../../sql-data-warehouse/sql-data-warehouse-load-with-data-factory.md) gyors bemutató létrehozása egy folyamatot az adatok másolása varázsló használatával.

A következő eszközök használatával hozzon létre egy folyamatot: **Az Azure portal**, **Visual Studio**, **Azure PowerShell-lel**, **Azure Resource Manager-sablon**, **.NET API**, és  **REST API-val**. Lásd: [másolási tevékenység oktatóanyagát](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) egy másolási tevékenységgel ellátott adatcsatorna létrehozása a részletes útmutatóját.

Az eszközök vagy az API-kat használja, hogy létrehoz egy folyamatot, amely a helyez át adatokat egy forrásadattárból egy fogadó adattárba a következő lépéseket fogja végrehajtani:

1. Hozzon létre egy **adat-előállító**. Adat-előállító egy vagy több folyamattal is tartalmazhat. 
2. Hozzon létre **társított szolgáltatásokat** mutató hivatkozást a bemeneti és kimeneti adatokat tárolja a data factoryjához. Például ha másolt adatokat egy Azure blob storage-ból egy Azure SQL data warehouse, hoz létre az Azure storage-fiókot és az Azure SQL data warehouse-bA összekapcsolása a data factory két társított szolgáltatást. Társított szolgáltatás tulajdonságait, konkrétan az Azure SQL Data Warehouse, lásd: [társított szolgáltatások tulajdonságai](#linked-service-properties) szakaszban. 
3. Hozzon létre **adatkészletek** , amely a másolási művelet bemeneti és kimeneti adatokat jelöli. A példában az előző lépésben említett megadja a blobtárolót és a bemeneti adatokat tartalmazó mappát egy adatkészletet hoz létre. És a egy másik tábla megadása az Azure SQL data warehouse, amely tárolja az adatokat a blob storage-tárhelyéből adatkészletet hoz létre. Az Azure SQL Data Warehouse adott adatkészlet tulajdonságai, lásd: [adatkészlet tulajdonságai](#dataset-properties) szakaszban.
4. Hozzon létre egy **folyamat** egy másolási tevékenységgel, amely egy adatkészletet bemenetként, és a egy adatkészletet pedig kimenetként. A példában azt korábban említettük használhat BlobSource a forrás-és SqlDWSink fogadóként a másolási tevékenység. Hasonlóképpen, az Azure SQL Data Warehouse-ból az Azure Blob Storage másolása, SqlDWSource és a használata BlobSink a másolási tevékenység. Másolási tevékenység tulajdonságai az Azure SQL Data Warehouse jellemző, lásd: [másolási tevékenység tulajdonságai](#copy-activity-properties) szakaszban. A forrás vagy a fogadó adattár használatát részletekért kattintson a hivatkozásra az adattár az előző szakaszban.

A varázsló használatakor a rendszer automatikusan létrehozza a Data Factory-entitásokat (társított szolgáltatások, adatkészletek és folyamat) JSON-definíciói az Ön számára. Eszközök/API-k (kivéve a .NET API) használatakor adja meg a Data Factory-entitások a JSON formátumban. A Data Factory-entitások, amelyek és- tárolókról az Azure SQL Data Warehouse az adatok másolása JSON-definíciói minták, lásd: [JSON példák](#json-examples-for-copying-data-to-and-from-sql-data-warehouse) című szakaszát.

Az alábbi szakaszok nyújtanak az Azure SQL Data Warehouse adott Data Factory-entitások definiálásához használt JSON-tulajdonságokkal kapcsolatos részletekért:

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai
Az alábbi táblázatban az adott Azure SQL Data Warehouse társított szolgáltatás JSON-elemek leírását.

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| type |A type tulajdonságot kell beállítani: **AzureSqlDW** |Igen |
| kapcsolati Sztringje |Adja meg a connectionString tulajdonság az Azure SQL Data Warehouse-példányhoz való kapcsolódáshoz szükséges adatokat. Csak az alapszintű hitelesítést is támogatja. |Igen |

> [!IMPORTANT]
> Konfigurálása [Azure SQL Database-tűzfal](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) és a kiszolgáló [a kiszolgálóhoz való hozzáféréshez Azure-szolgáltatások engedélyezése](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Emellett az adatokat az Azure SQL Data Warehouse kívül az Azure például a data factory-átjáró a helyszíni adatforrásokból származó másolása, beállítható, megfelelő IP-címtartományt, a gép, amely adatokat küld az Azure SQL Data Warehouse.

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai
Szakaszok & adatkészletek definiálását tulajdonságainak teljes listáját lásd: a [adatkészletek létrehozása](data-factory-create-datasets.md) cikk. Például a szerkezetet, rendelkezésre állást és szabályzatát adatkészlet JSON szakaszok hasonlóak az összes adatkészlet esetében (az Azure SQL, az Azure blob-, az Azure table-, stb.).

A typeProperties szakasz eltérő az egyes adatkészlet, és az adattárban lévő adatok helyére vonatkozó információkat. A **typeProperties** szakasz az adatkészlet típusa **AzureSqlDWTable** tulajdonságai a következők:

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| tableName |A tábla vagy nézet, amelyre a társított szolgáltatás hivatkozik az Azure SQL Data Warehouse-adatbázis neve. |Igen |

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
Szakaszok & definiálását tevékenységek tulajdonságainak teljes listáját lásd: a [folyamatok létrehozása](data-factory-create-pipelines.md) cikk. Tulajdonságok, mint például a nevét, leírását, bemeneti és kimeneti táblák és a házirend az összes típusú tevékenységek érhetők el.

> [!NOTE]
> A másolási tevékenység csak egy bemenettel rendelkezik, és csak egy kimenetet.

Mivel a tevékenység a typeProperties szakasz tulajdonságai tevékenységek minden típusának számától függ. A másolási tevékenységhez azok változhat a forrásként és fogadóként típusú is.

### <a name="sqldwsource"></a>SqlDWSource
Ha a forrás típusa van **SqlDWSource**, a következő tulajdonságok érhetők el a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| sqlReaderQuery |Az egyéni lekérdezés segítségével olvassa el az adatokat. |SQL-lekérdezési karakterláncot. Például: válassza ki * from tábla. |Nem |
| sqlReaderStoredProcedureName |A tárolt eljárást, amely adatokat olvas be a forrás-tábla neve. |A tárolt eljárás neve. Az utolsó SQL-utasítást a tárolt eljárás a SELECT utasítással kell lennie. |Nem |
| storedProcedureParameters |A tárolt eljárás paraméterei. |Név-érték párokat. Nevek és a kis-és a paraméterek meg kell egyeznie a neveket és a kis-és nagybetűhasználatot, a tárolt eljárás paraméterértékeinek. |Nem |

Ha a **sqlReaderQuery** van megadva a SqlDWSource, a másolási tevékenység a lekérdezés fut az Azure SQL Data Warehouse forrás, az adatok beolvasásához.

Másik lehetőségként megadhat egy tárolt eljárást megadásával a **sqlReaderStoredProcedureName** és **storedProcedureParameters** (Ha a tárolt eljárás paraméter szükséges).

Ha nem ad meg sqlReaderQuery vagy sqlReaderStoredProcedureName, az adatkészlet JSON struktúra szakaszában meghatározott oszlopokat futtatásához az Azure SQL Data Warehouse-lekérdezés összeállításához használja. Példa: `select column1, column2 from mytable`. Ha az adatkészlet definíciója nem rendelkezik a struktúrát, az összes oszlop ki van jelölve, a táblából.

#### <a name="sqldwsource-example"></a>SqlDWSource example

```JSON
"source": {
    "type": "SqlDWSource",
    "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
    "storedProcedureParameters": {
        "stringData": { "value": "str3" },
        "identifier": { "value": "$$Text.Format('{0:yyyy}', SliceStart)", "type": "Int"}
    }
}
```
**A tárolt eljárás definíciója:**

```SQL
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

### <a name="sqldwsink"></a>SqlDWSink
**SqlDWSink** támogatja a következő tulajdonságokkal:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| sqlWriterCleanupScript |Adjon meg egy lekérdezést a másolási tevékenység végrehajtásához úgy, hogy az adott szeletre vonatkozó adatok törlődnek. További információkért lásd: [ismételhetőség szakasz](#repeatability-during-copy). |A lekérdezési utasítást. |Nem |
| allowPolyBase |Azt jelzi, hogy (ha van ilyen), a PolyBase használata helyett BULKINSERT mechanizmust. <br/><br/> **Az ajánlott módszer az adatok betöltése az SQL Data Warehouse-bA a PolyBase.** Lásd: [bA a PolyBase használatával az adatok betöltése az Azure SQL Data Warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse) korlátozások és a részletek a következő szakaszban. |True (Igaz) <br/>FALSE (alapértelmezett) |Nem |
| polyBaseSettings |Egy csoport tulajdonságok is lehet megadni, ha a **allowPolybase** tulajdonsága **igaz**. |&nbsp; |Nem |
| rejectValue |Megadja a szám vagy százalékos aránya, amelyek is vissza kell utasítani, mielőtt a lekérdezés nem sikerült sorokat. <br/><br/>További információ a PolyBase visszautasítási lehetőségeit a a **argumentumok** szakaszában [CREATE EXTERNAL TABLE (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx) témakör. |0 (alapértelmezett), 1, 2... |Nem |
| rejectType |Itt adhatja meg, e a rejectValue kapcsoló Szövegkonstansérték vagy százalékban megadva. |Érték (alapértelmezett), százalékos aránya |Nem |
| rejectSampleValue |Mielőtt a PolyBase újraszámítja a visszautasított sorok aránya beolvasandó sorok számát határozza meg. |1, 2, … |Igen, ha **rejectType** van **százalékos aránya** |
| useTypeDefault |Itt adhatja meg, hogyan szeretné kezelni a PolyBase kér le adatokat a szövegfájl elválasztójellel tagolt szöveges fájlok a hiányzó értékeket.<br/><br/>További tudnivalók a ezt a tulajdonságot a következő argumentumok szakaszában [CREATE EXTERNAL FILE FORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx). |TRUE, False (alapértelmezett) |Nem |
| WriteBatchSize |Adatok beszúrása SQL-táblát, amikor a puffer mérete eléri a writeBatchSize |Egész szám (sorok száma) |Nem (alapértelmezett: 10000) |
| writeBatchTimeout |Várjon, amíg a kötegelt insert művelet befejezését, mielőtt azt az időkorlátot. |Időtartam<br/><br/> Példa: "00: 30:00" (30 perc). |Nem |

#### <a name="sqldwsink-example"></a>SqlDWSink példa

```JSON
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true
}
```

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>Adatok betöltése az Azure SQL Data Warehouse a PolyBase használatával
Használatával **[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide)** nagy mennyiségű adat betöltése az Azure SQL Data Warehouse-bA és nagy teljesítményű, hatékony módja. Átviteli sebesség nagyobb nyereség láthatja az alapértelmezett BULKINSERT mechanizmus helyett a PolyBase használatával. Lásd: [teljesítmény hivatkozási szám másolása](data-factory-copy-activity-performance.md#performance-reference) a részletes összehasonlító. A használati esetek, olvassa [1 TB adat betöltése az Azure SQL Data Warehouse-bA az Azure Data Factoryvel 15 perc alatt](data-factory-load-sql-data-warehouse.md).

* Ha a forrásadatok **Azure Blob- vagy Azure Data Lake Store**, és kompatibilis a polybase-zel formátuma, közvetlenül lehet másolni az Azure SQL Data Warehouse a PolyBase használatával. Lásd: **[közvetlen másolása a PolyBase](#direct-copy-using-polybase)** adatokkal.
* Ha a forrásadattár és formátum eredetileg nem támogatott a PolyBase által, használhatja a **[szakaszos Másolás a PolyBase](#staged-copy-using-polybase)** funkció ehelyett. Is, nagyobb átviteli sebességet biztosít automatikusan adatok PolyBase-kompatibilis formátumra való konvertálása, és az adatok tárolása az Azure Blob storage-ban. Majd betölti adatokat az SQL Data Warehouse-bA.

Állítsa be a `allowPolyBase` tulajdonságot **igaz** az Azure Data Factoryben az adatok másolása az Azure SQL Data Warehouse-bA a PolyBase használatával az alábbi példában látható módon. AllowPolyBase értéke igaz, ha a PolyBase segítségével konkrét tulajdonságok is megadhat a `polyBaseSettings` tulajdonságcsoport. Tekintse meg a [SqlDWSink](#SqlDWSink) szakasz kapcsolódó polyBaseSettings használható tulajdonságokkal kapcsolatos információk.

```JSON
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

### <a name="direct-copy-using-polybase"></a>A PolyBase közvetlen másolása
Az SQL Data Warehouse PolyBase közvetlenül támogatja az Azure Blob- és az Azure Data Lake Store (az egyszerű szolgáltatás használatával) forrásként, és az adott fájl formátuma követelményeinek. Ha a forrásadatok megfelel a jelen szakaszban ismertetett feltételeknek, közvetlenül másolja át az Azure SQL Data Warehouse a PolyBase. Ellenkező esetben használhatja [szakaszos Másolás a PolyBase](#staged-copy-using-polybase).

> [!TIP]
> Adatok másolása az Data Lake Store az SQL Data Warehouse hatékonyan, ismerje meg alaposabban [Azure Data Factory segítségével még egyszerűbben és kényelmes megoldás az adatok alapján tárhat fel, ha a Data Lake Store használatával az SQL Data Warehouse](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/).

A követelmények nem teljesülnek, ha az Azure Data Factory ellenőrzi a beállításokat, és automatikusan visszavált az adatok áthelyezése a BULKINSERT mechanizmusa.

1. **Forrás társított szolgáltatás** típusa: **AzureStorage** vagy **az egyszerű szolgáltatásnév hitelesítése AzureDataLakeStore**.
2. A **bemeneti adatkészlet** típusa: **Azure BLOB** vagy **AzureDataLakeStore**, és írja be a format `type` tulajdonságai **OrcFormat**, **ParquetFormat**, vagy **TextFormat** az alábbi konfigurációkkal:

    1. `rowDelimiter` meg kell **\n**.
    2. `nullValue` értéke **üres karakterlánc** (""), vagy `treatEmptyAsNull` értékre van állítva **igaz**.
    3. `encodingName` értéke **utf-8**, amely **alapértelmezett** érték.
    4. `escapeChar`, `quoteChar`, `firstRowAsHeader`, és `skipLineCount` nincs megadva.
    5. `compression` lehet **tömörítés nélküli**, **GZip**, vagy **Deflate**.

    ```JSON
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

3. Nincs nem `skipHeaderLineCount` menüpont **BlobSource** vagy **AzureDataLakeStore** a folyamat másolási tevékenysége számára.
4. Nincs nem `sliceIdentifierColumnName` menüpont **SqlDWSink** a folyamat másolási tevékenysége számára. (A PolyBase garantálja, hogy az összes adatainak frissítése, vagy semmi sem egyetlen futtatással módosul. Eléréséhez **ismételhetőség**, használhat `sqlWriterCleanupScript`).
5. Nincs nem `columnMapping` használatban van a kapcsolódó, a másolási tevékenység.

### <a name="staged-copy-using-polybase"></a>A PolyBase szakaszos másolás
Ha a forrásadatok nem felel meg az előző szakaszban bemutatott, másolhatja az adatokat egy átmeneti előkészítési Azure Blob Storage (nem lehet a Premium Storage) keresztül is engedélyezheti. Ebben az esetben az Azure Data Factory automatikusan végrehajtja az átalakítások a PolyBase az adatok formátuma követelményeinek, majd az adatok betöltése az SQL Data Warehouse-ba, vagy egy legutóbbi törölje a PolyBase használatával az adatok az ideiglenes adatokat a Blob storage-ból. Lásd: [szakaszos Másolás](data-factory-copy-activity-performance.md#staged-copy) adatmásolás keresztül az Azure Blob átmeneti működése általában részleteiért.

> [!NOTE]
> Amikor másolása adatok és a egy a helyszíni adatok tárolása az Azure SQL Data Warehouse a PolyBase, és átmeneti, ha a Data Management Gateway verzió 2.4 alább, a JRE (Java-futtatókörnyezet) megléte szükséges a az átjárót tartalmazó számítógépen, amely a forrásadatok átalakítására használatos megfelelő formátumba. Javasoljuk, hogy az átjáró a legújabb verzióra történő elkerülheti az ilyen függőségi.
>

Ez a funkció használatához hozzon létre egy [Azure Storage társított szolgáltatás](data-factory-azure-blob-connector.md#azure-storage-linked-service) hivatkozik, amely az Azure Storage-fiókot, amely rendelkezik az átmeneti blob storage, majd adja meg a `enableStaging` és `stagingSettings` , ahogyan a másolási tevékenység tulajdonságai a következő kódot:

```json
"activities":[
{
    "name": "Sample copy activity from SQL Server to SQL Data Warehouse via PolyBase",
    "type": "Copy",
    "inputs": [{ "name": "OnpremisesSQLServerInput" }],
    "outputs": [{ "name": "AzureSQLDWOutput" }],
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
Az alábbi szakaszok nyújtanak további ajánlott eljárásokat, és amelyekre hivatkozunk; ennek megfelelően [ajánlott eljárások az Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-best-practices.md).

### <a name="required-database-permission"></a>Adatbázishoz szükséges engedélyt
A PolyBase használatához szükséges adatok betöltése az SQL Data Warehouse-ba, használja a felhasználó rendelkezik a ["CONTROL" engedély](https://msdn.microsoft.com/library/ms191291.aspx) a céladatbázison. Érhet el, amely egyik módja az, hogy a felhasználó hozzáadása "db_owner" szerepkör tagjaként. Ismerje meg, hogyan valósítható meg a következő [ebben a szakaszban](../../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

### <a name="row-size-and-data-type-limitation"></a>Sor mérete és az adatok írja be a korlátozás
A Polybase-betöltések korlátozódnak betöltése sorok mindkét kisebb, mint **1 MB** és VARCHR(MAX), NVARCHAR(MAX) vagy VARBINARY(MAX) nem tölthető be. Tekintse meg [Itt](../../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Ha a forrásadatok 1 MB-nál nagyobb méretű sorokat tartalmazó, érdemes a forrástáblákhoz függőlegesen felosztása több kis azokat, amennyiben azok legnagyobb sor mérete nem haladja meg a korlátot. A kisebb méretű táblák majd lehet betölteni. a PolyBase, és az Azure SQL Data Warehouse egyesíti.

### <a name="sql-data-warehouse-resource-class"></a>Az SQL Data Warehouse erőforrásosztály
A legjobb teljesítmény elérése érdekében érdemes nagyobb erőforrásosztályt hozzárendelni a felhasználói adatok betöltése az SQL Data Warehouse polybase használt. Ismerje meg, hogyan valósítható meg a következő [módosítása egy felhasználói erőforrás osztály példa](../../sql-data-warehouse/sql-data-warehouse-develop-concurrency.md).

### <a name="tablename-in-azure-sql-data-warehouse"></a>az Azure SQL Data Warehouse táblanév
Az alábbi táblázat példákkal szolgál, adja meg a **tableName** az adatkészlet JSON-t a séma-és táblanevet különböző kombinációit tulajdonsága.

| Adatbázis-séma | Tábla neve | Táblanév JSON-tulajdonság |
| --- | --- | --- |
| dbo |Táblanév |Táblanév vagy a dbo. Táblanév vagy [dbo]. [Táblanév] |
| dbo1 |Táblanév |dbo1. Táblanév vagy [dbo1]. [Táblanév] |
| dbo |My.Table |[My.Table] vagy [dbo]. [My.Table] |
| dbo1 |My.Table |[dbo1].[My.Table] |

Ha a következő hibát látja, annak oka az lehet a tableName tulajdonsághoz megadott érték problémáját. Tekintse meg a táblázat tartalmazza a megfelelő módszer az adja meg az értékeket a tableName JSON-tulajdonságot.

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

### <a name="columns-with-default-values"></a>Az alapértelmezett értékekkel oszlopok
PolyBase szolgáltatás a Data Factory jelenleg csak az azonos számú oszlopot, mint a céltábla fogad el. Tegyük fel, rendelkezésére a négy oszlopot tartalmazó táblát, és ezek közül az egyik alapértelmezett értékkel van definiálva. A bemeneti adatok továbbra is tartalmaznia kell a négy oszlopot. 3 oszlopos bemeneti adatkészlet nyújtó eredményezné az alábbihoz hasonló hibát:

```
All columns of the table must be specified in the INSERT BULK statement.
```
Alapértelmezett érték formája értéke NULL. Ha az oszlop nullázható, a bemeneti adatokat (a blob) az adott oszlop üres lehet (nem hiányzik a bemeneti adatkészlet). A PolyBase NULL számukra az Azure SQL Data warehouse szúr be.

## <a name="auto-table-creation"></a>Automatikus táblázat létrehozása
Ha használja a másolás varázsló adatok másolása az SQL Server vagy az Azure SQL Database az Azure SQL Data Warehouse és a tábla, amely megfelel a forrástábla nem létezik a cél tárolójában, a Data Factory automatikusan létrehozhat a tábla az adatraktár által u a forrás-táblaséma folyamata.

A Data Factory a tábla a ugyanazon tábla neve a source data Store céltár hoz létre. Az adattípusok oszlopok esetében a következő adattípus-hozzárendelés alapján választják ki. Ha szükséges, típuskonverziók inkompatibilitások kijavítása érdekében bármely forrás-és cél között hajt végre. Ciklikus időszeletelés a táblaelosztással is használ.

| Forrás SQL-adatbázis oszlop típusa | Cél SQL DW oszlop típusa (korlátozás) |
| --- | --- |
| Int | Int |
| BigInt | BigInt |
| SmallInt | SmallInt |
| TinyInt | TinyInt |
| bit | bit |
| Tizedes tört | Tizedes tört |
| Numerikus | Tizedes tört |
| Lebegőpontos | Lebegőpontos |
| költséget takaríthat meg | költséget takaríthat meg |
| Real | Real |
| SmallMoney | SmallMoney |
| Bináris | Bináris |
| varbinary | Varbinary (legfeljebb 8000-es) |
| Dátum | Dátum |
| DateTime | DateTime |
| DateTime2 | DateTime2 |
| Time | Time |
| DateTimeOffset | DateTimeOffset |
| SmallDateTime | SmallDateTime |
| Szöveg | Varchar (legfeljebb 8000-es) |
| NText | NVarChar (legfeljebb 4000-es) |
| Kép | VarBinary (legfeljebb 8000-es) |
| UniqueIdentifier | UniqueIdentifier |
| CHAR | CHAR |
| NChar | NChar |
| VarChar | VarChar (legfeljebb 8000-es) |
| NVarChar | NVarChar (legfeljebb 4000-es) |
| Xml | Varchar (legfeljebb 8000-es) |

[!INCLUDE [data-factory-type-repeatability-for-sql-sources](../../../includes/data-factory-type-repeatability-for-sql-sources.md)]

## <a name="type-mapping-for-azure-sql-data-warehouse"></a>Írja be az Azure SQL Data Warehouse-leképezés
Említetteknek megfelelően az [adattovábbítási tevékenységek](data-factory-data-movement-activities.md) a cikkben a másolási tevékenység végzi az automatikus típuskonverziók a fogadó-típusokat az alábbi 2. lépés – a módszert használja a forrás típusa:

1. A natív forrástípusok átalakítása typ .NET
2. A .NET-típusból átalakítása natív fogadó típusa

Áthelyezésekor adatokat, és az Azure SQL Data Warehouse-ból, a következő hozzárendeléseket használják az SQL-típus .NET típusát, és ez fordítva is igaz.

A leképezés megegyezik a [SQL Server adattípus-leképezés az ADO.NET](https://msdn.microsoft.com/library/cc716729.aspx).

| SQL Server adatbázismotor típusa | .NET-keretrendszer típusa |
| --- | --- |
| bigint |Int64 |
| Bináris |Byte] |
| bit |Logikai |
| CHAR |Karakterlánc, Char] |
| dátum |DateTime |
| Dátum és idő |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |DateTimeOffset |
| tizedes tört |tizedes tört |
| A FILESTREAM attribútum (varbinary(max)) |Byte] |
| Lebegőpontos |Dupla |
| image |Byte] |
| int |Int32 |
| költséget takaríthat meg |tizedes tört |
| nchar |Karakterlánc, Char] |
| ntext |Karakterlánc, Char] |
| numerikus |tizedes tört |
| nvarchar |Karakterlánc, Char] |
| valódi |Önálló |
| ROWVERSION |Byte] |
| smalldatetime |DateTime |
| smallint |Int16 |
| pénz |tizedes tört |
| sql_variant |Objektum * |
| szöveg |Karakterlánc, Char] |
| time |Időtartam |
| időbélyeg |Byte] |
| tinyint |Bájt |
| UniqueIdentifier |GUID |
| varbinary |Byte] |
| varchar |Karakterlánc, Char] |
| xml |Xml |

A másolási tevékenységhez tartozó definíció a fogadó-adatkészlet-oszlop a forrásadatkészlet oszlopok is leképezheti. További információkért lásd: [az Azure Data Factoryban adatkészletoszlopok leképezése](data-factory-map-columns.md).

## <a name="json-examples-for-copying-data-to-and-from-sql-data-warehouse"></a>JSON-példák az adatok másolása, és az SQL Data Warehouse-ból
Az alábbi példák megadják példa JSON-definíciók, amelyek segítségével létrehoz egy folyamatot használatával [az Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) vagy [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy [Azure PowerShell-lel](data-factory-copy-activity-tutorial-using-powershell.md). Adatok másolása az Azure SQL Data Warehouse és az Azure Blob Storage mutatnak. Azonban az adatok átmásolhatók **közvetlenül** bármelyik források a conditions stated above fogadóként valamelyik [Itt](data-factory-data-movement-activities.md#supported-data-stores-and-formats) a másolási tevékenységgel az Azure Data Factoryban.

### <a name="example-copy-data-from-azure-sql-data-warehouse-to-azure-blob"></a>Példa: Adatok másolása az Azure SQL Data warehouse-bA az Azure Blob
A minta az alábbi Data Factory-entitások határozza meg:

1. A társított szolgáltatás típusa [AzureSqlDW](#linked-service-properties).
2. A társított szolgáltatás típusa [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Egy bemeneti [adatkészlet](data-factory-create-datasets.md) típusú [AzureSqlDWTable](#dataset-properties).
4. Kimenet [adatkészlet](data-factory-create-datasets.md) típusú [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [folyamat](data-factory-create-pipelines.md) másolási tevékenységgel, amely használja [SqlDWSource](#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

A minta idősorozat-(óránként, naponta, stb) adatokat másol az Azure SQL Data Warehouse-adatbázisban lévő táblából egy blobba óránként. Ezek a minták a használt JSON-tulajdonságokat a minták a következő szakaszok ismertetik.

**Az Azure SQL Data Warehouse-beli társított szolgáltatást:**

```JSON
{
  "name": "AzureSqlDWLinkedService",
  "properties": {
    "type": "AzureSqlDW",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Az Azure Blob storage-beli társított szolgáltatást:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Az Azure SQL Data Warehouse bemeneti adatkészlet:**

A minta azt feltételezi, létrehozott egy táblát "MyTable" az Azure SQL Data Warehouse és a egy idősorozat-adatok a "timestampcolumn" nevű oszlopot tartalmaz.

Beállítás az "external": "true" tájékoztatja a Data Factory szolgáltatásban, hogy az adatkészletet a data factory a külső, és nem hozzák az adat-előállító adott tevékenységéhez.

```JSON
{
  "name": "AzureSqlDWInput",
  "properties": {
    "type": "AzureSqlDWTable",
    "linkedServiceName": "AzureSqlDWLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```
**Azure blobkimeneti adatkészlet:**

Adatokat írt egy új blob minden órában (frequency: óra, időköz: 1). A mappa elérési útját a BLOB a feldolgozás alatt álló szelet kezdő időpontja alapján dinamikusan kiértékeli. A mappa elérési útját használja, év, hónap, nap és óra részei a kezdési időpontot.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Másolási tevékenységgel rendelkező SqlDWSource és BlobSink folyamatot:**

A folyamat egy másolási tevékenység, amely a bemeneti és kimeneti adatkészleteket használatára van konfigurálva, és óránként ütemezett tartalmazza. A folyamat JSON-definíciót a **forrás** típusa **SqlDWSource** és **fogadó** típusa **BlobSink**. A megadott SQL-lekérdezést a **SqlReaderQuery** tulajdonság kiválasztja az adatokat másolni az elmúlt órában.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[
      {
        "name": "AzureSQLDWtoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureSqlDWInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlDWSource",
            "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
        "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```
> [!NOTE]
> A példában **sqlReaderQuery** a SqlDWSource van megadva. A másolási tevékenység a lekérdezés fut az Azure SQL Data Warehouse forrás, az adatok beolvasásához.
>
> Másik lehetőségként megadhat egy tárolt eljárást megadásával a **sqlReaderStoredProcedureName** és **storedProcedureParameters** (Ha a tárolt eljárás paraméter szükséges).
>
> Ha nem ad meg sqlReaderQuery vagy sqlReaderStoredProcedureName, az oszlopok az adatkészlet JSON struktúra szakaszban meghatározott lekérdezés összeállításához kell használni (select Oszlop1, from tábla column2) futtatásához az Azure SQL Data Warehouse szolgálnak. Ha az adatkészlet definíciója nem rendelkezik a struktúrát, az összes oszlop ki van jelölve, a táblából.
>
>

### <a name="example-copy-data-from-azure-blob-to-azure-sql-data-warehouse"></a>Példa: Adatok másolása az Azure-Blobból Azure SQL Data warehouse-bA
A minta az alábbi Data Factory-entitások határozza meg:

1. A társított szolgáltatás típusa [AzureSqlDW](#linked-service-properties).
2. A társított szolgáltatás típusa [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Egy bemeneti [adatkészlet](data-factory-create-datasets.md) típusú [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Kimenet [adatkészlet](data-factory-create-datasets.md) típusú [AzureSqlDWTable](#dataset-properties).
5. A [folyamat](data-factory-create-pipelines.md) másolási tevékenységgel, amely használja [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) és [SqlDWSink](#copy-activity-properties).

A minta másolatokat idősorozat-adatok (például óránként, naponta stb.) az Azure-blobból egy táblát az Azure SQL Data Warehouse-adatbázis óránként. Ezek a minták a használt JSON-tulajdonságokat a minták a következő szakaszok ismertetik.

**Az Azure SQL Data Warehouse-beli társított szolgáltatást:**

```JSON
{
  "name": "AzureSqlDWLinkedService",
  "properties": {
    "type": "AzureSqlDW",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Az Azure Blob storage-beli társított szolgáltatást:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Azure blobbemeneti adatkészlet:**

Adatok felülettől új blob minden órában (frequency: óra, időköz: 1). A mappa elérési útját és nevét a BLOB dinamikusan a feldolgozás alatt álló szelet kezdő időpontja alapján értékeli ki. A mappa elérési útjának év, hónap és nap részét a kezdési időpont és fájlnevet a kezdő időpontja óra részét használja. "external": "true" beállítással, hogy ez a táblázat a data factory a külső, és nem egy adat-előállító tevékenység által előállított arról tájékoztatja a Data Factory szolgáltatásban.

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": "\n"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```
**Az Azure SQL Data Warehouse kimeneti adatkészlet:**

A minta adatokat másol egy Azure SQL Data Warehouse "MyTable" nevű táblát. A tábla létrehozása az Azure SQL Data Warehouse az azonos számú oszlopot az a Blob CSV-fájl tartalmazza a várt módon. Új sorok hozzáadódnak a tábla minden órában.

```JSON
{
  "name": "AzureSqlDWOutput",
  "properties": {
    "type": "AzureSqlDWTable",
    "linkedServiceName": "AzureSqlDWLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
**A BlobSource és SqlDWSink a folyamat másolási tevékenysége:**

A folyamat egy másolási tevékenység, amely a bemeneti és kimeneti adatkészleteket használatára van konfigurálva, és óránként ütemezett tartalmazza. A folyamat JSON-definíciót a **forrás** típusa **BlobSource** és **fogadó** típusa **SqlDWSink**.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[
      {
        "name": "AzureBlobtoSQLDW",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSqlDWOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource",
            "blobColumnSeparators": ","
          },
          "sink": {
            "type": "SqlDWSink",
            "allowPolyBase": true
          }
        },
        "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```
Egy bemutatóért lásd: a lásd [1 TB adat betöltése az Azure SQL Data Warehouse-bA az Azure Data Factoryvel 15 perc alatt](data-factory-load-sql-data-warehouse.md) és [adatok betöltése az Azure Data Factory](../../sql-data-warehouse/sql-data-warehouse-get-started-load-with-azure-data-factory.md) a cikk az Azure SQL Data Warehouse-dokumentáció.

## <a name="performance-and-tuning"></a>Teljesítmény és finomhangolás
Lásd: [másolási tevékenységek teljesítményéhez és teljesítményhangolási útmutatóból](data-factory-copy-activity-performance.md) megismerheti a kulcsfontosságú szerepet játszik az adatáthelyezés (másolási tevékenység) az Azure Data Factory és a különféle módokon optimalizálhatja azt, hogy hatással lehet a teljesítményre.
