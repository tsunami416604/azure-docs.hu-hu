---
title: Adatok másolása az Azure SQL Data Warehouse |} Microsoft Docs
description: 'Útmutató: Azure SQL Data Warehouse Azure Data Factory használatával és a-adatok másolása'
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: d90fa9bd-4b79-458a-8d40-e896835cfd4a
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 709a178d99a34adb9c77086e55270fe41ed84551
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2018
---
# <a name="copy-data-to-and-from-azure-sql-data-warehouse-using-azure-data-factory"></a>Másolja az adatokat, és az Azure SQL Data Warehouse Azure Data Factory használatával
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1. verzió – Általánosan elérhető](data-factory-azure-sql-data-warehouse-connector.md)
> * [2. verzió – Előzetes verzió](../connector-azure-sql-data-warehouse.md)

> [!NOTE]
> Ez a cikk a Data Factory általánosan elérhető 1. verziójára vonatkozik. Lásd a 2-es verziójának a Data Factory szolgáltatásnak, amely jelenleg előzetes verzióban érhető, használatakor [Azure SQL Data Warehouse-összekötőt, a V2](../connector-azure-sql-data-warehouse.md).

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység során az Azure Data Factory adatok áthelyezése az Azure SQL Data Warehouse és a. Buildekről nyújtanak a [adatok mozgása tevékenységek](data-factory-data-movement-activities.md) cikk, amelynek során adatátvitel a másolási tevékenység az általános áttekintést.  

> [!TIP]
> A legjobb teljesítmény érdekében az adatok betöltése az Azure SQL Data Warehouse polybase szolgáltatást akkor használja. A [használja a PolyBase az adatok betöltése az Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) szakasz részleteket tartalmaz. A használati esetek bemutatóért lásd: [1 TB-os betöltése az Azure SQL Data Warehouse a 15 perc Azure Data Factory](data-factory-load-sql-data-warehouse.md).

## <a name="supported-scenarios"></a>Támogatott esetek
Adatokat másolhat **az Azure SQL Data Warehouse** tárolja a következő adatokat:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

Adatok másolása a következő adatokat tárolja **az Azure SQL Data Warehouse**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!TIP]
> Adat másolása az SQL Server vagy az Azure SQL Database az Azure SQL Data Warehouse, ha a tábla nem létezik a tárolási célhelye, amikor Data Factory automatikusan a tábla az SQL Data Warehouse segítségével létrehozható a tábla sémája a forrás-tárolóban. Lásd: [tábla létrehozásához automatikus](#auto-table-creation) részleteiről.

## <a name="supported-authentication-type"></a>Támogatott hitelesítési típushoz
Az Azure SQL Data Warehouse összekötő alapszintű hitelesítés támogatása.

## <a name="getting-started"></a>Első lépések
A másolási tevékenység, amely helyezi át az adatokat az Azure SQL Data Warehouse és a különböző eszközök/API-k használatával létrehozhat egy folyamatot.

A legegyszerűbben úgy, hogy hozzon létre egy folyamatot, amely másolja az adatokat és a Azure SQL Data Warehouse-hoz adatok másolása varázsló használatával. Lásd: [oktatóanyag: adatok betöltése az SQL Data Warehouse Data Factory](../../sql-data-warehouse/sql-data-warehouse-load-with-data-factory.md) létrehozásával egy folyamatot, az adatok másolása varázsló segítségével gyorsan útmutatást.

Az alábbi eszközöket használhatja a folyamatokat létrehozni: **Azure-portálon**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager sablon**, **.NET API**, és **REST API**. Lásd: [másolási tevékenység oktatóanyag](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) hozzon létre egy folyamatot a másolási tevékenység részletes útmutatóját.

Akár az eszközök vagy API-k, hajtsa végre a következő lépésekkel hozza létre egy folyamatot, amely mozgatja az adatokat a forrás-tárolóban a fogadó tárolóban:

1. Hozzon létre egy **adat-előállító**. Egy adat-előállító tartalmazhat egy vagy több folyamatok. 
2. Hozzon létre **összekapcsolt szolgáltatások** bemeneti és kimeneti adatok csatolásához tárolja a a data factory. Például ha a másolt adatok az az Azure blob storage egy Azure SQL data warehouse, hoz létre a Azure storage-fiók és az Azure SQL data warehouse összekapcsolása a data factory két társított szolgáltatások. Az Azure SQL Data Warehouse jellemző csatolt szolgáltatás tulajdonságait, lásd: [szolgáltatástulajdonságok kapcsolódó](#linked-service-properties) szakasz. 
3. Hozzon létre **adatkészletek** a másolási művelet bemeneti és kimeneti adatok. A példában az előző lépésben említett hozzon létre egy adatkészlet adja meg a blob-tároló és a bemeneti adatokat tartalmazó mappát. És hoz létre, ha meg szeretné adni a tábla az Azure SQL data warehouse, amely tárolja az adatokat a blob-tároló átmásolja egy másik DataSet adatkészletben. Az Azure SQL Data Warehouse jellemző adatkészlet tulajdonságai, lásd: [adatkészlet tulajdonságai](#dataset-properties) szakasz.
4. Hozzon létre egy **csővezeték** , amely fogad egy bemeneti adatkészlet és egy kimeneti adatkészletet másolási tevékenységgel. A korábban említett példában BlobSource forrás-és SqlDWSink akár használhatja a fogadó a másolási tevékenységhez. Ehhez hasonlóan az Azure Blob Storage másolása az Azure SQL Data Warehouse, használható SqlDWSource és BlobSink a másolási tevékenység. Az Azure SQL Data Warehouse adott tevékenység Tulajdonságok másolása, lásd: [tevékenység Tulajdonságok másolása](#copy-activity-properties) szakasz. További részletek a tárolóban használatáról a forrás vagy a fogadó a hivatkozásra a adattároló az előző szakaszban.

A varázsló használatakor a Data Factory entitások (összekapcsolt szolgáltatások adatkészletek és a feldolgozási sor) JSON-definíciók automatikusan létrejönnek. Eszközök/API-k (kivéve a .NET API-t) használata esetén adja meg a Data Factory entitások a JSON formátum használatával.  Adatok másolása az Azure SQL Data Warehouse használandó adat-előállító entitások JSON-definíciók minták, lásd: [JSON példák](#json-examples-for-copying-data-to-and-from-sql-data-warehouse) című szakaszát.

A következő szakaszok részletesen bemutatják, amely segítségével határozza meg a Data Factory tartozó entitások az Azure SQL Data Warehouse JSON-tulajdonságok:

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok
A következő táblázat a JSON-elemek szerepelnek Azure SQL Data Warehouse kapcsolódó szolgáltatásra vonatkozó leírást.

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| type |A type tulajdonságot kell beállítani: **AzureSqlDW** |Igen |
| connectionString |Adja meg a connectionString tulajdonság az Azure SQL Data Warehouse-példány való kapcsolódáshoz szükséges adatokat. Csak az alapszintű hitelesítést is támogatja. |Igen |

> [!IMPORTANT]
> Konfigurálása [Azure SQL Database-tűzfal](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) és az adatbázis-kiszolgálót [a kiszolgálóhoz való hozzáféréshez Azure-szolgáltatások engedélyezése](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Ezenkívül az adatokat az Azure SQL Data Warehouse a külső Azure többek között a helyszíni adatforrásokból a data factory átjáróval másolása, az IP-címtartományt a gép, amely adatokat küld az Azure SQL Data Warehouse konfigurálni.

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai
Szakaszok & meghatározása adatkészletek esetében elérhető tulajdonságok teljes listáját lásd: a [adatkészletek létrehozása](data-factory-create-datasets.md) cikk. Például struktúra, a rendelkezésre állás és a házirend a DataSet adatkészlet JSON hasonlítanak minden adatkészlet esetében (Azure SQL, az Azure blob, Azure-tábla, stb.).

A typeProperties szakasz más adatkészlet egyes típusai és információkat nyújt azokról az adattárban adatok helyét. A **typeProperties** szakasz az adatkészlet típusú **AzureSqlDWTable** tulajdonságai a következők:

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| tableName |A tábla vagy nézet, az Azure SQL Data Warehouse-adatbázis hivatkozik a társított szolgáltatás neve. |Igen |

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
Szakaszok & rendelkezésre álló tevékenységek meghatározó tulajdonságok teljes listáját lásd: a [létrehozása folyamatok](data-factory-create-pipelines.md) cikk. Például a nevét, leírását, valamint bemeneti és kimeneti táblák és házirend tulajdonságai minden típusú tevékenységek érhetők el.

> [!NOTE]
> A másolási tevékenység során csak egy bemenettel rendelkezik, és csak egy kimenetet.

Mivel a tevékenység typeProperties szakaszában elérhető tulajdonságok tevékenységek minden típusának függenek. A másolási tevékenység során két érték források és mosdók típusától függően.

### <a name="sqldwsource"></a>SqlDWSource
Ha a forrás típusa van **SqlDWSource**, a következő tulajdonságok érhetők el **typeProperties** szakasz:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| sqlReaderQuery |Az egyéni lekérdezés segítségével adatokat olvasni. |SQL-lekérdezési karakterlánc. Például: Válasszon * from tábla. |Nem |
| sqlReaderStoredProcedureName |A tárolt eljárás, amely adatokat olvas a forrástábla neve. |A tárolt eljárás neve. Az utolsó SQL-utasítás a következő tárolt eljárást a SELECT utasítással kell lennie. |Nem |
| storedProcedureParameters |A tárolt eljárás paramétereit. |A név/érték párok. Nevét és a kis-és a paraméterek meg kell egyeznie a nevek és a kis-és nagybetűhasználat a tárolt eljárás paramétereit. |Nem |

Ha a **sqlReaderQuery** van megadva a SqlDWSource, a másolási tevékenység fut ez a lekérdezés az adatok lekérdezése az Azure SQL Data Warehouse forrás.

Másik lehetőségként megadhat tárolt eljárás megadásával a **sqlReaderStoredProcedureName** és **storedProcedureParameters** (Ha a tárolt eljárás paraméterek fogadja el).

Ha nem ad meg sqlReaderQuery vagy sqlReaderStoredProcedureName, az adatkészlet JSON struktúrában szakaszában meghatározott oszlopokat futtatni az Azure SQL Data Warehouse-lekérdezés összeállításához használt. Példa: `select column1, column2 from mytable`. Az adatkészlet-definícióban nem rendelkezik a struktúra, ha minden kiválasztott oszlop. a táblából.

#### <a name="sqldwsource-example"></a>SqlDWSource – példa

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
| sqlWriterCleanupScript |Adja meg egy lekérdezést a másolási tevékenység végrehajtása úgy, hogy egy adott szelet adatait. További információkért lásd: [ismételhetőség szakasz](#repeatability-during-copy). |A lekérdezési utasítást. |Nem |
| allowPolyBase |Azt jelzi, hogy (ha alkalmazható), a PolyBase használata helyett BULKINSERT mechanizmus. <br/><br/> **Az ajánlott módszer az adatok betöltése az SQL Data Warehouse PolyBase a használata.** Lásd: [használja a PolyBase az adatok betöltése az Azure SQL Data Warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse) szakaszban a korlátozások és részleteit. |True (Igaz) <br/>Hamis (alapértelmezés) |Nem |
| polyBaseSettings |Egy csoport, amely tulajdonságok megadott, amikor a **allowPolybase** tulajdonsága **igaz**. |&nbsp; |Nem |
| rejectValue |Megadja a szám vagy is el kell utasítani, mielőtt a lekérdezés nem sikerült sorokat százalékát. <br/><br/>További információ a PolyBase utasítsa el a beállítások elemre a **argumentumok** szakasza [külső tábla létrehozása (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx) témakör. |0 (alapértelmezés), 1, 2... |Nem |
| rejectType |Határozza meg, hogy a rejectValue beállítás konstans értéket vagy százalékában van megadva. |Érték (alapértelmezett), százalékos aránya |Nem |
| rejectSampleValue |Mielőtt a PolyBase újraszámítja a visszautasított sorok százalékát beolvasandó sorok számát határozza meg. |1, 2, … |Igen, ha **rejectType** van **százalékos aránya** |
| useTypeDefault |Megadja, hogyan legyen kezelve tagolt szövegfájlok a hiányzó értékeket, amikor a PolyBase kér le adatokat a szövegfájlból.<br/><br/>Ezt a tulajdonságot, az argumentumok ismertető részben olvashat [létrehozása külső FÁJLFORMÁTUM (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx). |IGAZ, hamis (alapértelmezés) |Nem |
| WriteBatchSize |Adatok beszúrása a SQL táblázatba, amikor a puffer mérete eléri writeBatchSize |Egész szám (sorok száma) |Nem (alapértelmezett: 10000) |
| writeBatchTimeout |Várakozási idő a kötegelt beszúrási művelet befejezését, mielőtt azt az időkorlátot. |A TimeSpan<br/><br/> Példa: "00: 30:00" (30 perc). |Nem |

#### <a name="sqldwsink-example"></a>SqlDWSink – példa

```JSON
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true
}
```

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>Adatok betöltése az Azure SQL Data Warehouse PolyBase segítségével
Használatával **[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide)** egy hatékony módszer a nagy mennyiségű adatok betöltését az Azure SQL Data Warehouse nagy átviteli sebességgel. A teljesítmény a nagy nyereség helyett az alapértelmezett BULKINSERT mechanizmus a PolyBase használatával tekintheti meg. Lásd: [teljesítmény hivatkozási szám másolása](data-factory-copy-activity-performance.md#performance-reference) a részletes összehasonlítását. A használati esetek bemutatóért lásd: [1 TB-os betöltése az Azure SQL Data Warehouse a 15 perc Azure Data Factory](data-factory-load-sql-data-warehouse.md).

* Ha a forrás adatok **Azure Blob vagy az Azure Data Lake Store**, és a formátuma nem kompatibilis a PolyBase, közvetlenül másolhatja az Azure SQL Data Warehouse PolyBase használatával. Lásd: **[közvetlen másolása a PolyBase használatával](#direct-copy-using-polybase)** adatokkal.
* Ha a forrás-tárolót és formátum eredetileg nem támogatott a PolyBase által, használhatja a **[előkészített másolása a PolyBase használatával](#staged-copy-using-polybase)** inkább a beállítást. Is biztosít, nagyobb átviteli sebesség automatikusan adatok PolyBase-kompatibilis formátumra való konvertálása, és az adatok tárolása az Azure Blob Storage tárolóban. Majd betölti az SQL Data Warehouse-adatok.

Állítsa be a `allowPolyBase` tulajdonságot **igaz** az Azure Data Factoryben az adatok másolása az Azure SQL Data Warehouse polybase szolgáltatást akkor használja a következő példában látható módon. AllowPolyBase értéke igaz, amikor a PolyBase konkrét tulajdonságok használatával megadhatja a `polyBaseSettings` tulajdonságcsoport. Tekintse meg a [SqlDWSink](#SqlDWSink) szakasz kapcsolódó polyBaseSettings használható tulajdonságokról vonatkozó további információért.

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

### <a name="direct-copy-using-polybase"></a>A PolyBase használatával közvetlen másolása
SQL Data Warehouse PolyBase közvetlenül támogatja Azure-Blob és az Azure Data Lake Store (egyszerű szolgáltatásnév) forrásként, és az adott fájl formátum követelményeinek. Ha a forrásadatok megfelel a jelen szakaszban bemutatott, közvetlenül átmásolhatja forrás adattár az Azure SQL Data Warehouse PolyBase használatával. Ellenkező esetben használhatja [előkészített másolása a PolyBase használatával](#staged-copy-using-polybase).

> [!TIP]
> Adatok másolása Data Lake Store az SQL Data Warehouse hatékonyan, további információhoz [Azure Data Factory megkönnyíti még és kényelmes elvégzésével nyújt betekintést az adatokat, az SQL Data Warehouse szolgáltatással Data Lake Store használatakor](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/).

A feltételeknek nem felel meg, ha az Azure Data Factory ellenőrzi a beállításait, és automatikusan visszaáll az adatátvitelt jelölik a BULKINSERT mechanizmus.

1. **Forrás társított szolgáltatás** típusa: **AzureStorage** vagy **szolgáltatás egyszerű hitelesítéssel AzureDataLakeStore**.  
2. A **bemeneti adatkészlet** típusa: **AzureBlob** vagy **AzureDataLakeStore**, és írja be a format `type` tulajdonságai **OrcFormat**, **ParquetFormat**, vagy **szöveges** , a következő beállításokat:

   1. `rowDelimiter` kell **\n**.
   2. `nullValue` értéke **üres karakterlánc** (""), vagy `treatEmptyAsNull` értéke **igaz**.
   3. `encodingName` értéke **utf-8**, amely **alapértelmezett** érték.
   4. `escapeChar`, `quoteChar`, `firstRowAsHeader`, és `skipLineCount` nincs megadva.
   5. `compression` lehet **tömörítés**, **GZip**, vagy **Deflate**.

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

3. Nincs nincs `skipHeaderLineCount` beállítás alatt **BlobSource** vagy **AzureDataLakeStore** a másolási tevékenységhez, a folyamat.
4. Nincs nincs `sliceIdentifierColumnName` beállítás alatt **SqlDWSink** a másolási tevékenységhez, a folyamat. (A PolyBase garantálja, hogy minden adat frissül, vagy nem frissül, az egyszeri futtatás. Eléréséhez **ismételhetőség**, használhat `sqlWriterCleanupScript`).
5. Nincs nincs `columnMapping` használatban lévő a kapcsolódó, a másolási tevékenység.

### <a name="staged-copy-using-polybase"></a>A PolyBase használatával előkészített másolása
A forrásadatok nem felel meg az előző szakaszban bemutatott, amikor az adatok másolását az átmeneti átmeneti Azure Blob Storage (nem lehet a prémium szintű Storage) keresztül is engedélyezheti. Ebben az esetben Azure Data Factory automatikusan végez átalakítások adatok formátuma elégíteni PolyBase, akkor az SQL Data Warehouse-, illetve utolsó karbantartás az adatok betöltése a PolyBase segítségével az adatok ideiglenes adatait a blobtárolóból. Lásd: [előkészített másolási](data-factory-copy-activity-performance.md#staged-copy) átmeneti Azure Blob keresztül az adatok másolásának működéséről, általában a részletekért.

> [!NOTE]
> Ha az Azure SQL Data Warehouse PolyBase használatával tárolja az adatok másolását egy helyszíni adatokból, és átmeneti, ha az adatkezelési átjáró verziója nem éri el 2.4, JRE (Java Runtime Environment) szükséges az átjáró gépen, amely használatával a forrásadatok megfelelő formátumba. Javasoljuk, hogy az ilyen függőségi elkerülése érdekében az átjárót a legújabb verzióra frissít.
>

Ez a funkció használatához hozzon létre egy [Azure Storage társított szolgáltatásnak](data-factory-azure-blob-connector.md#azure-storage-linked-service) , amely hivatkozik, amely rendelkezik az átmeneti a blob storage Azure Storage-fiókot, majd adja meg a `enableStaging` és `stagingSettings` látható módon a másolási tevékenység tulajdonságai a következő kódot:

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
Az alábbi szakaszokban további gyakorlati tanácsok a meglévők közül ismertetett [ajánlott eljárások az Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-best-practices.md).

### <a name="required-database-permission"></a>Adatbázis szükséges engedéllyel
PolyBase használatához igényel az adatok betöltése az SQL Data Warehouse használt felhasználó rendelkezik-e a ["Vezérlő" engedély](https://msdn.microsoft.com/library/ms191291.aspx) a célként megadott adatbázison. Egyik módja, hogy, hogy a felhasználó hozzáadása "db_owner" szerepkör tagjaként. Útmutató a következő ehhez [ebben a szakaszban](../../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

### <a name="row-size-and-data-type-limitation"></a>Írja be korlátozás sorméret és adatok
A Polybase terhelések esetén egyre korlátozódik betöltése sort is kisebb, mint **1 MB** és VARCHR(MAX), NVARCHAR(MAX) vagy VARBINARY(MAX) nem tölthető be. Tekintse meg [Itt](../../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Ha 1 MB-nál nagyobb méretű sorokat tartalmazó forrásadatok, érdemes lehet a forrástáblákból függőleges felosztása több kis megfelelően, ha azok legnagyobb sor mérete nem haladja meg a határértéket. A kisebb táblák majd lehet betölteni. a PolyBase használatával, és egyesíti az Azure SQL Data Warehouse.

### <a name="sql-data-warehouse-resource-class"></a>Az SQL Data Warehouse erőforrás osztály
Lehetséges legjobb teljesítmény elérése érdekében fontolja meg a felhasználói adatok betöltése az SQL Data Warehouse polybase használt nagyobb erőforrásosztály hozzárendelése. Útmutató a következő ehhez [módosíthatja a felhasználói erőforrás osztály példa](../../sql-data-warehouse/sql-data-warehouse-develop-concurrency.md).

### <a name="tablename-in-azure-sql-data-warehouse"></a>az Azure SQL Data Warehouse táblanév
A következő táblázat példákat tartalmaz a megadása a **tableName** tulajdonságot az adatkészlet JSON-séma-és táblanevet különböző kombinációjához.

| Megadott adatbázissémát | Tábla neve | tableName JSON tulajdonsága |
| --- | --- | --- |
| dbo |Táblanév |MyTable vagy dbo. MyTable vagy [dbo]. [MyTable] |
| dbo1 |Táblanév |dbo1. MyTable vagy [dbo1]. [MyTable] |
| dbo |My.Table |[My.Table] vagy [dbo]. [My.Table] |
| dbo1 |My.Table |[dbo1].[My.Table] |

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

## <a name="auto-table-creation"></a>Automatikus tábla létrehozásához
Ha másolása varázsló segítségével adatok másolása az SQL Server vagy az Azure SQL Database az Azure SQL Data warehouse-ba, és a táblázat, amely megfelel a következő forrástábla nem szerepel a rendeltetési tárolási, adat-előállító automatikusan létrehozásához a tábla az adatraktár u a forrás táblaséma folyamata.

Adat-előállító hoz létre a tábla a céltár a tábla néven a forrás-tárolóban. Az adattípusok oszlopok a következő típusleképezéshez alapján választják ki. Ha szükséges, az azonosított inkompatibilitásokat javításához a forrás- és tárolók közötti típuskonverziók hajt végre. Ciklikus multiplexelés elosztása is használja.

| Forrás SQL-adatbázis oszlop típusa | Cél SQL DW oszlop típusa (méretének korlátozása) |
| --- | --- |
| Int | Int |
| BigInt | BigInt |
| SmallInt | SmallInt |
| TinyInt | TinyInt |
| bit | bit |
| Decimális | Decimális |
| Numerikus | Decimális |
| Lebegőpontos | Lebegőpontos |
| pénz | pénz |
| Real | Real |
| Kis pénz típusú értéknél | Kis pénz típusú értéknél |
| Bináris | Bináris |
| varbinary | Varbinary (legfeljebb 8000) |
| Dátum | Dátum |
| DateTime | DateTime |
| DateTime2 | DateTime2 |
| Time | Time |
| DateTimeOffset | DateTimeOffset |
| SmallDateTime | SmallDateTime |
| Szöveg | Varchar (legfeljebb 8000) |
| NText | NVarChar (legfeljebb 4000) |
| Kép | VarBinary (legfeljebb 8000) |
| UniqueIdentifier | UniqueIdentifier |
| Karakter | Karakter |
| NChar | NChar |
| VarChar | VarChar (legfeljebb 8000) |
| NVarChar | NVarChar (legfeljebb 4000) |
| Xml | Varchar (legfeljebb 8000) |

[!INCLUDE [data-factory-type-repeatability-for-sql-sources](../../../includes/data-factory-type-repeatability-for-sql-sources.md)]

## <a name="type-mapping-for-azure-sql-data-warehouse"></a>Írja be az Azure SQL Data Warehouse leképezése
Ahogyan az a [adatok mozgása tevékenységek](data-factory-data-movement-activities.md) cikk, a másolási tevékenység eseményforrás-típusnak gyűjtése típusa a következő 2. lépés – a módszert használja az automatikus típuskonverziók hajtja végre:

1. A natív eseményforrás-típusnak átalakítása .NET-típusa
2. .NET-típus konvertálása natív a fogadó típusa

Ha adatok áthelyezése az Azure SQL Data Warehouse &, a következő megfeleltetéseket használ az SQL-típus a .NET-típus, és ez fordítva is igaz.

Leképezése nem ugyanaz, mint a [SQL Server adattípus-hozzárendelése az ADO.NET](https://msdn.microsoft.com/library/cc716729.aspx).

| SQL Server adatbázismotor típusa | .NET-keretrendszer típusa |
| --- | --- |
| bigint |Int64 |
| Bináris |Byte] |
| bit |Logikai |
| Karakter |Karakterlánc, Char] |
| dátum |DateTime |
| Dátum és idő |DateTime |
| datetime2 |DateTime |
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
| valós |Önálló |
| ROWVERSION |Byte] |
| smalldatetime |DateTime |
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
| xml |Xml |

A másolási tevékenység definíciójának fogadó adatkészletből oszlopok forrás adatkészletből oszlopokat is leképezheti. További információkért lásd: [Azure Data Factory dataset oszlopai leképezési](data-factory-map-columns.md).

## <a name="json-examples-for-copying-data-to-and-from-sql-data-warehouse"></a>Adatok másolása és az SQL Data Warehouse JSON példák
Az alábbi példák megadják minta JSON-definíciókat tartalmazzon, segítségével hozzon létre egy folyamatot [Azure-portálon](data-factory-copy-activity-tutorial-using-azure-portal.md) vagy [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Adatok másolása az Azure SQL Data warehouse-bA és az Azure Blob Storage mutatnak. Azonban az adatok átmásolhatók **közvetlenül** a forrásokban, sem a megadott nyelő [Itt](data-factory-data-movement-activities.md#supported-data-stores-and-formats) a másolási tevékenység során az Azure Data Factory használatával.

### <a name="example-copy-data-from-azure-sql-data-warehouse-to-azure-blob"></a>Példa: Adatok másolása az Azure SQL Data Warehouse az Azure-Blobba
A minta a következő adat-előállító entitások határozza meg:

1. A társított szolgáltatás típusa [AzureSqlDW](#linked-service-properties).
2. A társított szolgáltatás típusa [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Bemeneti [dataset](data-factory-create-datasets.md) típusú [AzureSqlDWTable](#dataset-properties).
4. Egy kimeneti [dataset](data-factory-create-datasets.md) típusú [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [csővezeték](data-factory-create-pipelines.md) a másolási tevékenység által használt [SqlDWSource](#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

A minta idősorozat (óránkénti, napi stb) adatainak másolása az Azure SQL Data Warehouse-adatbázis egy táblából egy blobba óránként. A mintákat a következő szakaszok ismertetik ezeket a mintákat használt JSON-tulajdonságok.

**A társított szolgáltatásnak Azure SQL Data Warehouse:**

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
**Az Azure Blob storage társított szolgáltatásnak:**

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
**Az SQL Data Warehouse bemeneti adatkészletet:**

A minta azt feltételezi, hogy létrehozott egy tábla "MyTable" az Azure SQL Data Warehouse és egy "timestampcolumn" nevű adatsorozat időadatok oszlopot tartalmaz.

"External" beállítása: "true" arról tájékoztatja a Data Factory szolgáltatásnak, hogy az adatkészlet külső data factoryval való és adat-előállító tevékenység nem hozzák.

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
**Az Azure Blob kimeneti adatkészlet:**

Adatot ír egy új blob minden órában (gyakoriság: óra, időköz: 1). A mappa elérési útját a BLOB a szelet által feldolgozott kezdési ideje alapján dinamikusan történik. A mappa elérési útját használja, év, hónap, nap és a kezdési idő órában részeit.

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

**Másolási tevékenység során a folyamat SqlDWSource és BlobSink:**

A feldolgozási sor tartalmazza a másolási tevékenység, amely a bemeneti és kimeneti adatkészletek használatára van konfigurálva, és óránkénti futásra nem ütemezték. Az adatcsatorna JSON-definícióból a **forrás** típusúra **SqlDWSource** és **fogadó** típusúra **BlobSink**. A megadott SQL-lekérdezést a **SqlReaderQuery** tulajdonság kiválasztása az adatok másolása az elmúlt órában.

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
> A példában **sqlReaderQuery** a SqlDWSource van megadva. A másolási tevékenység fut ez a lekérdezés az adatok lekérdezése az Azure SQL Data Warehouse forrás.
>
> Másik lehetőségként megadhat tárolt eljárás megadásával a **sqlReaderStoredProcedureName** és **storedProcedureParameters** (Ha a tárolt eljárás paraméterek fogadja el).
>
> Ha nem ad meg sqlReaderQuery vagy sqlReaderStoredProcedureName, az adatkészlet JSON struktúrában szakaszában meghatározott oszlopokat segítségével (válassza Oszlop1, column2 from tábla)-lekérdezés összeállításához az Azure SQL Data Warehouse futtatásához. Az adatkészlet-definícióban nem rendelkezik a struktúra, ha minden kiválasztott oszlop. a táblából.
>
>

### <a name="example-copy-data-from-azure-blob-to-azure-sql-data-warehouse"></a>Példa: Adatok másolása az Azure Blob az Azure SQL Data Warehouse
A minta a következő adat-előállító entitások határozza meg:

1. A társított szolgáltatás típusa [AzureSqlDW](#linked-service-properties).
2. A társított szolgáltatás típusa [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Bemeneti [dataset](data-factory-create-datasets.md) típusú [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Egy kimeneti [dataset](data-factory-create-datasets.md) típusú [AzureSqlDWTable](#dataset-properties).
5. A [csővezeték](data-factory-create-pipelines.md) a másolási tevékenység által használt [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) és [SqlDWSink](#copy-activity-properties).

A minta másolatok idősorozat adatok (óránként, naponta, stb.) az Azure blob-egy táblához, az Azure SQL Data Warehouse-adatbázis használati ideje minden órában. A mintákat a következő szakaszok ismertetik ezeket a mintákat használt JSON-tulajdonságok.

**A társított szolgáltatásnak Azure SQL Data Warehouse:**

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
**Az Azure Blob storage társított szolgáltatásnak:**

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
**Az Azure Blob bemeneti adatkészletet:**

Adatok van felvett egy új blobból minden órában (gyakoriság: óra, időköz: 1). A mappa elérési útját és nevét a BLOB dinamikusan értékeli ki a kezdési időt a szelet által feldolgozott alapján. A mappa elérési útját használja év, hónap és nap részét kezdési idejét, valamint fájl nevét a kezdő időpontja óra részét. "external": "true" beállítás arról értesíti az, hogy ezt a táblázatot az adat-előállítóban külső, és egy tevékenység adat-előállító nem hozzák a Data Factory szolgáltatásnak.

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
**Az SQL Data Warehouse kimeneti adatkészlet:**

A minta másolja az adatokat az Azure SQL Data Warehouse "MyTable" nevű tábla. A tábla létrehozása az Azure SQL Data Warehouse az azonos számú oszlopot tartalmaz a Blob CSV-fájl várt. Új sorok hozzáadásakor a tábla minden órában.

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
**Másolási tevékenység során a folyamat BlobSource és SqlDWSink:**

A feldolgozási sor tartalmazza a másolási tevékenység, amely a bemeneti és kimeneti adatkészletek használatára van konfigurálva, és óránkénti futásra nem ütemezték. Az adatcsatorna JSON-definícióból a **forrás** típusúra **BlobSource** és **fogadó** típusúra **SqlDWSink**.

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
Útmutatást megtalálja az [1 TB-os betöltése az Azure SQL Data Warehouse a 15 perc Azure Data Factory](data-factory-load-sql-data-warehouse.md) és [adatok betöltése az Azure Data Factory](../../sql-data-warehouse/sql-data-warehouse-get-started-load-with-azure-data-factory.md) cikk az Azure SQL Data Warehouse-dokumentációban.

## <a name="performance-and-tuning"></a>Teljesítmény- és hangolása
Lásd: [másolási tevékenység teljesítmény- és hangolása útmutató](data-factory-copy-activity-performance.md) tájékozódhat az kulcsfontosságú szerepet játszik adatátvitelt jelölik a (másolási tevékenység során) az Azure Data Factory és különböző módokon optimalizálhatja azt, hogy hatás teljesítményét.
