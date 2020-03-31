---
title: Adatok másolása az Azure SQL Data Warehouse-ba/onnan
description: Megtudhatja, hogy miként másolhat adatokat az Azure SQL Data Warehouse-ba/onnan az Azure Data Factory használatával
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: d90fa9bd-4b79-458a-8d40-e896835cfd4a
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 2df49e65603573e4a3adcdda0635982252e70b18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80130821"
---
# <a name="copy-data-to-and-from-azure-sql-data-warehouse-using-azure-data-factory"></a>Adatok másolása az Azure SQL Data Warehouse-ba és onnan az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-azure-sql-data-warehouse-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-azure-sql-data-warehouse.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, olvassa el az [Azure SQL Data Warehouse-összekötő a V2 alkalmazásban című témakört.](../connector-azure-sql-data-warehouse.md)

Ez a cikk bemutatja, hogyan használhatja a másolási tevékenység et az Azure Data Factory adatok áthelyezése/ az Azure SQL Data Warehouse.This article explain how to use the Copy Activity in Azure Data Factory to move data to/from Azure SQL Data Warehouse. Az [adatmozgatási tevékenységek](data-factory-data-movement-activities.md) cikkre épül, amely általános áttekintést nyújt az adatmozgásról a másolási tevékenységgel.

> [!TIP]
> A legjobb teljesítmény elérése érdekében a PolyBase segítségével töltse be az adatokat az Azure SQL Data Warehouse-ba. A [PolyBase használata adatok betöltéséhez az Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) szakasz részletesen tartalmazza. A használati eset forgatókönyve: [1 TB betöltése az Azure SQL Data Warehouse-ba 15 percen belül az Azure Data Factory segítségével](data-factory-load-sql-data-warehouse.md)című témakörben található.

## <a name="supported-scenarios"></a>Támogatott esetek
Az Azure **SQL Data Warehouse-ból** adatokat másolhat a következő adattárakba:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

A következő adattárakból másolhat adatokat **az Azure SQL Data Warehouse-ba:**

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!TIP]
> Adatok másolásakor az SQL Server vagy az Azure SQL Database az Azure SQL Data Warehouse, ha a tábla nem létezik a céltárolóban, a Data Factory automatikusan létrehozhatja a táblát az SQL Data Warehouse a séma a tábla a forrás adattárban. A részleteket az [Automatikus tábla létrehozása.](#auto-table-creation)

## <a name="supported-authentication-type"></a>Támogatott hitelesítési típus
Az Azure SQL Data Warehouse-összekötő támogatja az alapszintű hitelesítést.

## <a name="getting-started"></a>Első lépések
Létrehozhat egy folyamatot egy másolási tevékenységgel, amely az adatokat áthelyezi/onnan egy Azure SQL Data Warehouse-ba különböző eszközök/API-k használatával.

Az Azure SQL Data Warehouse-ba és az Azure SQL Data Warehouse-ból az adatokat másoló folyamat létrehozásának legegyszerűbb módja az adatok másolása varázsló használata. Tekintse meg [az oktatóanyagot: Adatok betöltése az SQL Data Warehouse-ba a Data Factory segítségével](../../sql-data-warehouse/sql-data-warehouse-load-with-data-factory.md) egy gyors forgatókönyvet a folyamat másolása az adatok másolása varázslóval.

A következő eszközökkel is létrehozhat egy folyamatot: **Visual Studio,** **Azure PowerShell**, **Azure Resource Manager sablon**, **.NET API**és REST **API.** Lásd: [Tevékenység-oktatóanyag másolása](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) című témakörben részletes útmutatást talál egy másolási tevékenységgel rendelkező folyamat létrehozásához.

Függetlenül attól, hogy az eszközöket vagy API-kat használja, a következő lépéseket hajthatja végre egy olyan folyamat létrehozásához, amely adatokat helyezi át a forrásadattárból a fogadó adattárába:

1. Hozzon létre egy **adat-előállító**. Az adat-előállító egy vagy több folyamatot tartalmazhat. 
2. **Összekapcsolt szolgáltatások** létrehozása a bemeneti és kimeneti adattárak és az adat-előállító összekapcsolására. Ha például adatokat másol egy Azure blobstorage-ból egy Azure SQL-adattárházba, két összekapcsolt szolgáltatást hoz létre az Azure storage-fiók és az Azure SQL-adattárház és az adat-előállító összekapcsolására. Az Azure SQL Data Warehouse-ra jellemző kapcsolt szolgáltatástulajdonságokról lásd: [csatolt szolgáltatástulajdonságok](#linked-service-properties) szakasz. 
3. **Adatkészletek** létrehozása a másolási művelet bemeneti és kimeneti adatainak ábrázolására. Az utolsó lépésben említett példában hozzon létre egy adatkészletet a blob tároló és a bemeneti adatokat tartalmazó mappa megadásához. És hozzon létre egy másik adatkészletet, amely megadja a táblát az Azure SQL-adattárházban, amely a blob storage-ból másolt adatokat tartalmazza. Az Azure SQL Data Warehouse-ra jellemző adatkészlet-tulajdonságokat lásd: [adatkészlet tulajdonságai](#dataset-properties) szakasz.
4. Hozzon létre egy **folyamatot** egy másolási tevékenységgel, amely egy adatkészletet bemenetként, egy adatkészletet pedig kimenetként vesz fel. A korábban említett példában a BlobSource-ot forrásként, az SqlDWSink-et pedig a másolási tevékenység fogadójaként használja. Hasonlóképpen, ha az Azure SQL Data Warehouse-ból az Azure Blob Storage-ba másolja, az SqlDWSource és a BlobSink a másolási tevékenység. Az Azure SQL Data Warehouse-ra jellemző másolási tevékenységtulajdonságokról a [Másolási tevékenység tulajdonságai](#copy-activity-properties) című szakaszban található. Az adattár forrásként vagy fogadóként való használatáról az adattár előző szakaszában található hivatkozásra kattintva.

A varázsló használatakor a Data Factory entitásokhoz (csatolt szolgáltatások, adatkészletek és a folyamat) json-definíciók automatikusan létrejönnek. Eszközök/API-k használatakor (a .NET API kivételével) ezeket a Data Factory entitásokat a JSON formátum használatával definiálhatja. Az Azure SQL Data Warehouse-ba az adatok másolására használt Data Factory-entitások JSON-definícióival rendelkező mintákat lásd: A cikk [JSON-példái](#json-examples-for-copying-data-to-and-from-sql-data-warehouse) című részében.

A következő szakaszok az Azure SQL Data Warehouse-ra jellemző Data Factory-entitások definiálására használt JSON-tulajdonságok részleteit ismertetik:

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai
Az alábbi táblázat az Azure SQL Data Warehouse csatolt szolgáltatásra jellemző JSON-elemek leírását tartalmazza.

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| type |A típustulajdonságot a következőre kell állítani: **AzureSqlDW** |Igen |
| connectionString (kapcsolati karakterlánc) |Adja meg az Azure SQL Data Warehouse-példányhoz való csatlakozáshoz szükséges információkat a connectionString tulajdonsághoz. Csak az alapfokú hitelesítés támogatott. |Igen |

> [!IMPORTANT]
> Konfigurálja az [Azure SQL Database Firewall-t](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) és az adatbázis-kiszolgálót, hogy [az Azure Services hozzáférhessen a kiszolgálóhoz.](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) Továbbá ha adatokat másol az Azure SQL Data Warehouse-ba az Azure-on kívülről, beleértve a helyszíni adatforrások adat-elő-factory gateway, konfigurálja a megfelelő IP-címtartománya a gép, amely adatokat küld az Azure SQL Data Warehouse.

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai
Az adatkészletek definiálására szolgáló & tulajdonságok teljes listáját az [Adatkészletek létrehozása](data-factory-create-datasets.md) című cikkben olvashat. A json-i adatkészletek például a struktúra, a rendelkezésre állás és a szabályzat hasonlóak az összes adatkészlettípushoz (Azure SQL, Azure blob, Azure table stb.).

A typeProperties szakasz az adatkészlet egyes típusainál eltérő, és tájékoztatást nyújt az adatok helyéről az adattárban. Az **AzureSqlDWTable** típusú adatkészlet **typeProperties** szakasza a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| tableName |A tábla vagy a nézet neve az Azure SQL Data Warehouse adatbázisban, amelyre a csatolt szolgáltatás hivatkozik. |Igen |

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
A tevékenységek definiálására rendelkezésre álló szakaszok & tulajdonságok teljes listáját a [Folyamatok létrehozása](data-factory-create-pipelines.md) című cikkben olvashat. Tulajdonságok, például név, leírás, bemeneti és kimeneti táblák és házirend állnak rendelkezésre minden típusú tevékenységek.

> [!NOTE]
> A Másolási tevékenység csak egy bemenetet vesz igénybe, és csak egy kimenetet hoz létre.

Mivel a tevékenység typeProperties szakaszában elérhető tulajdonságok az egyes tevékenységtípusoktól függően változnak. Másolási tevékenység esetén a források és a fogadók típusától függően változnak.

### <a name="sqldwsource"></a>SqlDWForrás
Ha a forrás **SqlDWSource**típusú, a következő tulajdonságok érhetők el a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| sqlReaderQuery |Az adatok olvasásához használja az egyéni lekérdezést. |SQL lekérdezési karakterlánc. Például: válassza a * lehetőséget a MyTable táblából. |Nem |
| sqlReaderStoredProcedureName |Annak a tárolt eljárásnak a neve, amely adatokat olvas be a forrástáblából. |A tárolt eljárás neve. Az utolsó SQL utasításnak SELECT utasításnak kell lennie a tárolt eljárásban. |Nem |
| storedProcedureParameters |A tárolt eljárás paraméterei. |Név/érték párok. A paraméterek nevének és burkolatának meg kell egyeznie a tárolt eljárásparamétereinek nevével és burkolatával. |Nem |

Ha az **sqlReaderQuery** meg van adva az SqlDWSource-hoz, a másolási tevékenység futtatja ezt a lekérdezést az Azure SQL Data Warehouse forrásában az adatok lekérni.

Másik lehetőségként megadhatja a tárolt eljárást az **sqlReaderStoredProcedureName** és a **storedProcedureParameters** megadásával (ha a tárolt eljárás paramétereket vesz igénybe).

Ha nem adja meg sem az sqlReaderQuery vagy az sqlReaderStoredProcedureName értéket, a JSON adatkészlet struktúraszakaszában definiált oszlopok at az Azure SQL Data Warehouse-on futtatandó lekérdezés létrehozásához használja a rendszer. Példa: `select column1, column2 from mytable`. Ha az adatkészlet-definíció nem rendelkezik a szerkezettel, akkor a program az összes oszlopot kijelöli a táblából.

#### <a name="sqldwsource-example"></a>Példa SqlDWSource

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
**A tárolt eljárás meghatározása:**

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

### <a name="sqldwsink"></a>SqlDWsink
**Az SqlDWSink** a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| sqlWriterCleanupScript |Adjon meg egy lekérdezést a Másolási tevékenységhez, hogy egy adott szelet adatait töröljék. További részletek az [ismételhetőségi szakaszban.](#repeatability-during-copy) |Lekérdezési utasítás. |Nem |
| allowPolyBase |Azt jelzi, hogy a POLYBase-t (adott esetben) kell-e használni a BULKINSERT mechanizmus helyett. <br/><br/> **A PolyBase használata az ajánlott módja az adatok SQL Data Warehouse-ba való betöltésének.** A korlátozásokért és részletekért olvassa el [a PolyBase használata az Adatok azure SQL Data Warehouse szakaszba való betöltéséhez](#use-polybase-to-load-data-into-azure-sql-data-warehouse) című témakört. |True (Igaz) <br/>False (alapértelmezett) |Nem |
| poliBaseSettings |Tulajdonságok olyan csoportja, amely akkor adható meg, ha az **allowPolybase** tulajdonság **értéke true .** |&nbsp; |Nem |
| rejectValue |Megadja a lekérdezés sikertelensedése előtt elutasítható sorok számát vagy százalékos arányát. <br/><br/>További információ a PolyBase elutasítási beállításairól a [CREATE EXTERNAL TABLE (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx) témakör **Argumentumok** szakaszában olvashat bővebben. |0 (alapértelmezett), 1, 2, ... |Nem |
| rejectType |Itt adható meg, hogy a rejectValue beállítás literális értékként vagy százalékként legyen megadva. |Érték (alapértelmezett), Százalék |Nem |
| rejectSampleValue |Azt határozza meg, hogy a PolyBase hány sort kell beolvasnia, mielőtt a PolyBase újraszámítja az elutasított sorok százalékos arányát. |1, 2, ... |Igen, ha a **rejectType** **százalék** |
| useTypeDefault |Itt adható meg, hogyan kezelje a tagolt szövegfájlokban hiányzó értékeket, amikor a PolyBase adatokat olvas le a szövegfájlból.<br/><br/>Erről a tulajdonságról a [CREATE EXTERNAL FILE FORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx)argumentumok szakaszában olvashat bővebben. |Igaz, Hamis (alapértelmezett) |Nem |
| writeBatchSize |Adatok beszúrása az SQL táblába, amikor a puffer mérete eléri a writeBatchSize-ot |Egész szám (sorok száma) |Nem (alapértelmezett: 10000) |
| writeBatchTimeout |Várjon időt a kötegbehelyezési művelet befejezésére, mielőtt az időtúljárna. |időtartomány<br/><br/> Példa: "00:30:00" (30 perc). |Nem |

#### <a name="sqldwsink-example"></a>Példa SqlDWSink

```JSON
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true
}
```

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>Adatok betöltése az Azure SQL Data Warehouse-ba a PolyBase használatával
A **[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide)** használata hatékony módja annak, hogy nagy mennyiségű adatot töltsen be az Azure SQL Data Warehouse-ba nagy átviteli sebességgel. Az átviteli érték nagy nyereségét az alapértelmezett BULKINSERT mechanizmus helyett a PolyBase használatával láthatja. Lásd [a másolási teljesítmény hivatkozási számát](data-factory-copy-activity-performance.md#performance-reference) részletes összehasonlítással. A használati eset forgatókönyve: [1 TB betöltése az Azure SQL Data Warehouse-ba 15 percen belül az Azure Data Factory segítségével](data-factory-load-sql-data-warehouse.md)című témakörben található.

* Ha a forrásadatok az Azure Blob ban vagy az **Azure Data Lake Store-ban**vannak, és a formátum kompatibilis a PolyBase-nel, közvetlenül másolhat az Azure SQL Data Warehouse-ba a PolyBase használatával. Lásd: **[Közvetlen másolás a PolyBase használatával](#direct-copy-using-polybase)** a részletekkel együtt.
* Ha a PolyBase eredetileg nem támogatja a forrásadat-tárolót és -formátumot, használhatja helyette a **[Szakaszos másolás a PolyBase](#staged-copy-using-polybase)** használatával szolgáltatást. Emellett jobb átviteli forgalmat biztosít azáltal, hogy automatikusan konvertálja az adatokat PolyBase-kompatibilis formátumba, és tárolja az adatokat az Azure Blob storage-ban. Ezután betölti az adatokat az SQL Data Warehouse.It then loads data into SQL Data Warehouse.

Állítsa `allowPolyBase` be a tulajdonság **ot igaz,** ahogy az az alábbi példában az Azure Data Factory használatával PolyBase adatok másolása az Azure SQL Data Warehouse. Ha az allowPolyBase értéket igaz értékre állítja, `polyBaseSettings` a tulajdonságcsoport használatával megadhatja a PolyBase adott tulajdonságait. a polyBaseSettings használatával használható tulajdonságokról az [SqlDWSink](#sqldwsink) szakaszban olvashat.

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

### <a name="direct-copy-using-polybase"></a>Közvetlen másolás a PolyBase használatával
Az SQL Data Warehouse PolyBase közvetlenül támogatja az Azure Blobot és az Azure Data Lake Store-t (egyszerű szolgáltatás használatával) forrásként és adott fájlformátum-követelményekkel. Ha a forrásadatok megfelelnek az ebben a szakaszban leírt feltételeknek, közvetlenül másolhatja a forrásadattárból az Azure SQL Data Warehouse-ba a PolyBase használatával. Ellenkező esetben a Szakaszos másolás a [PolyBase használatával](#staged-copy-using-polybase)is használható.

> [!TIP]
> Ha hatékonyan szeretné átmásolni az adatokat a Data Lake Store áruházból az SQL Data Warehouse szolgáltatásba, az Azure Data Factory további információimég [egyszerűbbé és kényelmesebbé teszik az adatokból származó információk feltárását a Data Lake Store SQL Data Warehouse használatával történő használata során.](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/)

Ha a követelmények nem teljesülnek, az Azure Data Factory ellenőrzi a beállításokat, és automatikusan visszaáll az adatáthelyezés BULKINSERT mechanizmusára.

1. **A forráshoz csatolt szolgáltatás** típusa: **AzureStorage** vagy **AzureDataLakeStore egyszerű szolgáltatáshitelesítéssel.**
2. A **bemeneti adatkészlet** típusa: **AzureBlob** vagy **AzureDataLakeStore**, `type` és a tulajdonságok alatti formátumtípus: **OrcFormat**, **ParquetFormat**vagy **TextFormat** a következő konfigurációkkal:

   1. `rowDelimiter`**\n**.
   2. `nullValue`**üres karakterlánc** ("" ) `treatEmptyAsNull` értékre van állítva, vagy **igaz**értékre van állítva.
   3. `encodingName`**utf-8**értékre van állítva, ami az **alapértelmezett** érték.
   4. `escapeChar`, `quoteChar` `firstRowAsHeader`, `skipLineCount` és nincs megadva.
   5. `compression`lehet **nincs tömörítés,** **GZip**vagy **Deflate**.

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

3. Nincs `skipHeaderLineCount` beállítás a **BlobSource** vagy **az AzureDataLakeStore** a folyamat másolása tevékenység.
4. Nincs `sliceIdentifierColumnName` beállítás az **SqlDWSink** a másolási tevékenység a folyamatban. (PolyBase garantálja, hogy az összes adat frissül, vagy semmi sem frissül egyetlen futtatásban. Az **ismételhetőség**érdekében használhatja a ). `sqlWriterCleanupScript`
5. Nincs `columnMapping` használata a társított másolási tevékenységben.

### <a name="staged-copy-using-polybase"></a>Szakaszos másolás a PolyBase használatával
Ha a forrásadatok nem felelnek meg az előző szakaszban bevezetett feltételeknek, engedélyezheti az adatok másolását egy köztes átmeneti Azure Blob Storage-on keresztül (nem lehet prémium szintű storage). Ebben az esetben az Azure Data Factory automatikusan elvégzi az átalakításokat az adatokon, hogy megfeleljen a PolyBase adatformátum-követelményeinek, majd a PolyBase segítségével adatokat tölt be az SQL Data Warehouse-ba, és végül a Blob storage-ból származó ideiglenes adatok karbantartása. Tekintse meg [a Szakaszos másolás](data-factory-copy-activity-performance.md#staged-copy) t, hogy az átmeneti Azure Blobon keresztül történő másolás általában hogyan működik.

> [!NOTE]
> Ha adatokat másol egy helyszíni adattárból az Azure SQL Data Warehouse-ba polybase és átmeneti használatával, ha az adatkezelési átjáró verziója 2.4 alatt van, jre (Java runtime környezet) szükséges az átjárógépen, amely a forrás átalakítására szolgál adatokat megfelelő formátumba kell alakítani. Javasoljuk, hogy frissítse az átjárót a legújabbra, hogy elkerülje az ilyen függőségeket.
>

A szolgáltatás használatához hozzon létre egy [Azure Storage-kapcsolt szolgáltatást,](data-factory-azure-blob-connector.md#azure-storage-linked-service) amely az Azure Storage-fiókra hivatkozik, amely rendelkezik az ideiglenes blobtár-tárral, majd adja meg a `enableStaging` másolási tevékenység `stagingSettings` tulajdonságait a következő kódban látható módon:

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

## <a name="best-practices-when-using-polybase"></a>Gyakorlati tanácsok a PolyBase használatakor
Az alábbi szakaszok további gyakorlati tanácsokat nyújtanak az Azure SQL Data Warehouse ajánlott eljárások ban [említettekhez.](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-best-practices.md)

### <a name="required-database-permission"></a>Szükséges adatbázis-engedély
A PolyBase használatához az SQL Data Warehouse-ba történő adatok betöltéséhez használt felhasználónak [a céladatbázis "CONTROL" engedéllyel](https://msdn.microsoft.com/library/ms191291.aspx) rendelkezik. Ennek egyik módja, ha a felhasználót a "db_owner" szerepkör tagjaként adja hozzá. Ismerje meg, hogyan kell ezt csinálni [az alábbi szakaszban](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

### <a name="row-size-and-data-type-limitation"></a>Sorméret és adattípus korlátozása
A polibázis terhelése **i1 MB-nál** kisebb sorok betöltésére korlátozódik, és nem tölthető be a VARCHR(MAX), az NVARCHAR(MAX) vagy a VARBINARY(MAX) sorokba. Lásd [itt](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Ha 1 MB-nál nagyobb méretű forrásadatokkal rendelkezik, érdemes lehet a forrástáblákat függőlegesen több kisebbre osztani, ahol a legnagyobb sorméret nem haladja meg a korlátot. A kisebb táblák ezután betölthetők a PolyBase használatával, és egyesíthetők az Azure SQL Data Warehouse-ban.

### <a name="sql-data-warehouse-resource-class"></a>SQL Data Warehouse erőforrásosztály
A lehető legjobb átviteli kapacitás elérése érdekében fontolja meg, hogy nagyobb erőforrásosztályt rendeljen hozzá az SQL Data Warehouse-ba a PolyBase-en keresztül az adatok betöltéséhez használt felhasználóhoz. Ebből megtudhatja, hogyan teheti ezt meg [a Felhasználói erőforrásosztály módosítása példában.](../../sql-data-warehouse/sql-data-warehouse-develop-concurrency.md)

### <a name="tablename-in-azure-sql-data-warehouse"></a>tableName az Azure SQL Data Warehouse-ban
Az alábbi táblázat példákat mutat be arra vonatkozóan, hogyan adható meg a **TableName** tulajdonság a JSON adatkészletben a séma és a táblanév különböző kombinációihoz.

| ADATBÁZIS-séma | Tábla neve | tableName JSON tulajdonság |
| --- | --- | --- |
| Dbo között |Saját táblázat |MyTable vagy dbo. MyTable vagy [dbo]. [MyTable] |
| dbo1 |Saját táblázat |dbo1. MyTable vagy [dbo1]. [MyTable] |
| Dbo között |Saját tábla |[My.Table] vagy [dbo]. [Saját.Table] |
| dbo1 |Saját tábla |[dbo1]. [Saját.Table] |

Ha a következő hibaüzenet jelenik meg, az a tableName tulajdonsághoz megadott értékkel kapcsolatos probléma lehet. A tableName JSON tulajdonság értékeinek megadásához a táblázatban található.

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

### <a name="columns-with-default-values"></a>Oszlopok alapértelmezett értékekkel
Jelenleg a Data Factory PolyBase szolgáltatása csak ugyanannyi oszlopot fogad el, mint a céltáblában. Mondja, van egy négy oszlopból álló táblázata, és az egyik alapértelmezett értékkel van definiálva. A bemeneti adatoknak továbbra is négy oszlopot kell tartalmazniuk. A háromoszlopos bemeneti adatkészlet megadása a következő höz hasonló hibaüzenetet eredményezne:

```
All columns of the table must be specified in the INSERT BULK statement.
```
A NULL érték az alapértelmezett érték egy speciális formája. Ha az oszlop nullázható, az adott oszlop bemeneti adatai (blobban) üresek lehetnek (nem hiányozhatnak a bemeneti adatkészletből). A PolyBase null értéket szúr be számukra az Azure SQL Data Warehouse-ban.

## <a name="auto-table-creation"></a>Automatikus táblalétrehozása
Ha a Másolás varázslóval másolja az adatokat az SQL Server vagy az Azure SQL Database adatbázisából az Azure SQL Data Warehouse-ba, és a forrástáblának megfelelő tábla nem létezik a céltárolóban, a Data Factory automatikusan létrehozhatja a táblát az adattárban a forrástábla sémája használatával.

A Data Factory ugyanazzal a táblanévvel hozza létre a táblát a céltárolóban a forrásadattárban. Az oszlopok adattípusai a következő típusleképezés alapján kerülnek kiválasztásra. Szükség esetén típuskonverziókat hajt végre a forrás- és a céltárolók közötti inkompatibilitás okának kijavítása érdekében. Körfelbés táblaeloszlást is használ.

| Forrás SQL-adatbázis oszloptípusa | Cél SQL DW oszloptípusa (méretkorlátozás) |
| --- | --- |
| Int | Int |
| BigInt között | BigInt között |
| SmallInt között | SmallInt között |
| TinyInt között | TinyInt között |
| Kicsit | Kicsit |
| Decimal | Decimal |
| Numerikus | Decimal |
| Float | Float |
| Money | Money |
| Valódi | Valódi |
| SmallMoney (kispénz) | SmallMoney (kispénz) |
| Bináris | Bináris |
| Varbináris | Varbinary (8000-ig) |
| Dátum | Dátum |
| DateTime | DateTime |
| DateTime2 (Dátumidő2) | DateTime2 (Dátumidő2) |
| Time | Time |
| DateTimeOffset (Dátumidő-eltolás) | DateTimeOffset (Dátumidő-eltolás) |
| SmallDateTime (kisdátumidő) | SmallDateTime (kisdátumidő) |
| Szöveg | Varchar (8000-ig) |
| NText | NVarChar (4000-ig) |
| Kép | VarBinary (8000-ig) |
| Egyedi azonosító | Egyedi azonosító |
| Char | Char |
| NChar | NChar |
| Varchar | VarChar (8000-ig) |
| NVarChar | NVarChar (4000-ig) |
| Xml | Varchar (8000-ig) |

[!INCLUDE [data-factory-type-repeatability-for-sql-sources](../../../includes/data-factory-type-repeatability-for-sql-sources.md)]

## <a name="type-mapping-for-azure-sql-data-warehouse"></a>Típusleképezés az Azure SQL Data Warehouse-hoz
Az [adatmozgatási tevékenységekről](data-factory-data-movement-activities.md) szóló cikkben említettek szerint a Másolás tevékenység automatikus típuskonverziót hajt végre a forrástípusokról a fogadótípusokra a következő kétlépéses megközelítéssel:

1. Konvertálás natív forrástípusokból .NET-típussá
2. Konvertálás .NET típusból natív fogadótípussá

Amikor adatokat helyez át & az Azure SQL Data Warehouse-ból, a következő leképezések sql-típusról .NET típusra szolgálnak, és fordítva.

A leképezés megegyezik a [ADO.NET SQL Server adattípus-leképezésével.](https://msdn.microsoft.com/library/cc716729.aspx)

| SQL Server adatbázis-kezelő motor típusa | .NET keretrendszer típusa |
| --- | --- |
| bigint |Int64 |
| binary |Bájt[] |
| bit |Logikai |
| Char |Karakterlánc, Karakter |
| dátum |DateTime |
| Datetime |DateTime |
| datetime2 |DateTime |
| Dátumidő-eltolás |DateTimeOffset (Dátumidő-eltolás) |
| Decimal |Decimal |
| FILESTREAM attribútum (varbinary(max)) |Bájt[] |
| Float |Double |
| image |Bájt[] |
| int |Int32 |
| Pénzt |Decimal |
| nchar |Karakterlánc, Karakter |
| nszöveg |Karakterlánc, Karakter |
| numerikus |Decimal |
| nvarchar |Karakterlánc, Karakter |
| real |Egyirányú |
| rowversion (sorverzió) |Bájt[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| kis pénz |Decimal |
| sql_variant |Objektum * |
| szöveg |Karakterlánc, Karakter |
| time |időtartam |
| időbélyeg |Bájt[] |
| tinyint |Bájt |
| uniqueidentifier |Guid |
| varbinary között |Bájt[] |
| varchar |Karakterlánc, Karakter |
| xml |Xml |

A forrásadatkészletből oszlopokat is leképezhet a fogadó-adatkészletből a másolási tevékenységdefinícióban. További információt az [Adatkészlet-oszlopok leképezése az Azure Data Factoryban című témakörben talál.](data-factory-map-columns.md)

## <a name="json-examples-for-copying-data-to-and-from-sql-data-warehouse"></a>JSON-példák az SQL Data Warehouse-ba és az SQL Data Warehouse-ból történő másoláshoz
Az alábbi példák minta JSON-definíciókat tartalmaznak, amelyek segítségével folyamatot hozhat létre a [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy az [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)használatával. Bemutatják, hogyan másolhat adatokat az Azure SQL Data Warehouse és az Azure Blob Storage. Azonban az adatok **közvetlenül** másolhatók bármelyik forrásból bármelyik fogadók [az itt](data-factory-data-movement-activities.md#supported-data-stores-and-formats) megadott az Azure Data Factory másolási tevékenység használatával.

### <a name="example-copy-data-from-azure-sql-data-warehouse-to-azure-blob"></a>Példa: Adatok másolása az Azure SQL Data Warehouse-ból az Azure Blobba
A minta a következő Data Factory entitásokat határozza meg:

1. [AzureSqlDW](#linked-service-properties)típusú összekapcsolt szolgáltatás.
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)típusú kapcsolt szolgáltatás.
3. [AzureSqlDWTable](#dataset-properties)típusú bemeneti [adatkészlet.](data-factory-create-datasets.md)
4. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)típusú kimeneti [adatkészlet.](data-factory-create-datasets.md)
5. [SqlDWSource](#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)programot használó másolási tevékenységgel rendelkező [folyamat.](data-factory-create-pipelines.md)

A minta az Azure SQL Data Warehouse-adatbázis egy táblájából óránként idomásolja az idősorokat (óránként, naponta stb.). Az ezekben a mintákban használt JSON-tulajdonságokat a mintákat követő szakaszok ismertetik.

**Azure SQL Data Warehouse kapcsolt szolgáltatás:**

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
**Azure Blob storage-hoz csatolt szolgáltatás:**

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
**Azure SQL Data Warehouse bemeneti adatkészlet:**

A minta feltételezi, hogy létrehozott egy "MyTable" táblát az Azure SQL Data Warehouse-ban, és tartalmaz egy "timestampcolumn" nevű oszlopot az idősorozat-adatokhoz.

"külső" beállítás: az "igaz" tájékoztatja a Data Factory szolgáltatást, hogy az adatkészlet az adat-előállítón kívül található, és nem az adat-előállító tevékenység által előállított.

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
**Azure Blob kimeneti adatkészlet:**

Az adatok óránként egy új blobba (gyakoriság: óra, időköz: 1) kerül beírásra. A blob mappaelérési útja dinamikusan kiértékelve a feldolgozás alatt álló szelet kezdési időpontja alapján történik. A mappa elérési útja a kezdési időpont év-, hónap-, nap- és órarészeit használja.

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

**Tevékenység másolása egy folyamatban az SqlDWSource és a BlobSink segítségével:**

A folyamat tartalmaz egy másolási tevékenységet, amely a bemeneti és kimeneti adatkészletek használatára van konfigurálva, és óránként i. A folyamat JSON-definíciójában a **forrástípus** **SqlDWSource-ra** van állítva, **a fogadó** típusa pedig **BlobSink**. Az **SqlReaderQuery** tulajdonsághoz megadott SQL-lekérdezés kiválasztja a másolni kívánt adatokat az elmúlt órában.

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
> A példában az **sqlReaderQuery** az SqlDWSource-hoz van megadva. A másolási tevékenység futtatja ezt a lekérdezést az Azure SQL Data Warehouse forrásból az adatok lekérdezéséhez.
>
> Másik lehetőségként megadhatja a tárolt eljárást az **sqlReaderStoredProcedureName** és a **storedProcedureParameters** megadásával (ha a tárolt eljárás paramétereket vesz igénybe).
>
> Ha nem adja meg sem az sqlReaderQuery, sem az sqlReaderStoredProcedureName értéket, a JSON adatkészlet struktúraszakaszában definiált oszlopok at egy lekérdezés létrehozásához (select column1, column2 a mytable-ből) az Azure SQL Data Warehouse-ból való futtatáshoz. Ha az adatkészlet-definíció nem rendelkezik a szerkezettel, akkor a program az összes oszlopot kijelöli a táblából.
>
>

### <a name="example-copy-data-from-azure-blob-to-azure-sql-data-warehouse"></a>Példa: Adatok másolása az Azure Blobból az Azure SQL Data Warehouse-ba
A minta a következő Data Factory entitásokat határozza meg:

1. [AzureSqlDW](#linked-service-properties)típusú összekapcsolt szolgáltatás.
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)típusú kapcsolt szolgáltatás.
3. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)típusú bemeneti [adatkészlet.](data-factory-create-datasets.md)
4. [AzureSqlDWTable](#dataset-properties)típusú kimeneti [adatkészlet.](data-factory-create-datasets.md)
5. [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) és [SqlDWSink](#copy-activity-properties)programot használó másolási tevékenységgel rendelkező [folyamat.](data-factory-create-pipelines.md)

A minta óránként idomásolja az idősorozat-adatokat (óránként, naponta stb.) az Azure blobból egy táblába az Azure SQL Data Warehouse adatbázisában. Az ezekben a mintákban használt JSON-tulajdonságokat a mintákat követő szakaszok ismertetik.

**Azure SQL Data Warehouse kapcsolt szolgáltatás:**

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
**Azure Blob storage-hoz csatolt szolgáltatás:**

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
**Azure Blob bemeneti adatkészlet:**

Az adatokat óránként veszi fel egy új blob (gyakoriság: óra, időköz: 1). A blob mappaelérési útja és fájlneve dinamikusan kiértékelésre kerül a feldolgozás alatt álló szelet kezdési időpontja alapján. A mappa elérési útja a kezdési időpont év, hónap és nap részét, a fájlnév pedig a kezdési időpont órarészét használja. "külső": az "igaz" beállítás tájékoztatja a Data Factory szolgáltatást, hogy ez a tábla az adat-előállítón kívül található, és nem az adat-előállító tevékenység által előállított.

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
**Azure SQL Data Warehouse kimeneti adatkészlet:**

A minta az adatokat egy "MyTable" nevű táblába másolja az Azure SQL Data Warehouse-ban. Hozza létre a táblát az Azure SQL Data Warehouse-ban ugyanannyi oszloppal, mint ablob CSV-fájl. A rendszer óránként új sorokat ad a táblához.

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
**Tevékenység másolása egy folyamatban a BlobSource és az SqlDWSink segítségével:**

A folyamat tartalmaz egy másolási tevékenységet, amely a bemeneti és kimeneti adatkészletek használatára van konfigurálva, és óránként i. A folyamat JSON-definíciójában a **forrástípus** **BlobSource** lesz állítva, **a fogadó** típusa pedig **SqlDWSink**.

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
A forgatókönyv: [1 TB betöltése az Azure SQL Data Warehouse 15 percen belül az Azure Data Factory](data-factory-load-sql-data-warehouse.md) és az Adatok [betöltése](../../sql-data-warehouse/sql-data-warehouse-get-started-load-with-azure-data-factory.md) az Azure Data Factory cikkaz Azure SQL Data Warehouse dokumentációjában.

## <a name="performance-and-tuning"></a>Teljesítmény és hangolás
A [Tevékenység teljesítményének másolása & hangolási útmutatóban](data-factory-copy-activity-performance.md) megismerést talál az adatok (másolási tevékenység) azure Data Factory ban az adatmozgatás (másolási tevékenység) teljesítményét befolyásoló legfontosabb tényezőkről, valamint az optimalizálás különböző módjairól.
