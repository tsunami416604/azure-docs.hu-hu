---
title: "Adatok másolása az Azure SQL Database Data Factory használatával |} Microsoft Docs"
description: "Útmutató: adatok másolása az Azure SQL Database a támogatott forráshierarchiából adatokat áruházakból (vagy) az SQL-adatbázis támogatott fogadó adattárolókhoz Data Factory használatával."
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
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: e4d14f396b3a928975b671d10254cfbcc822a0d3
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="copy-data-to-or-from-azure-sql-database-by-using-azure-data-factory"></a>Másolja a adatok vagy az Azure SQL Database az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1. verzió – Általánosan elérhető](v1/data-factory-azure-sql-connector.md)
> * [2. verzió – Előzetes verzió](connector-azure-sql-database.md)

Ez a cikk ismerteti, hogyan használható a másolási tevékenység során az Azure Data Factory másolja az adatokat, a kezdő és egy Azure SQL Database adatbázist a. Buildekről nyújtanak a [másolása tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [Azure SQL Database-összekötőt a V1](v1/data-factory-azure-sql-connector.md).

## <a name="supported-capabilities"></a>Támogatott képességei

A/az Azure SQL Database-adatok másolása bármely támogatott fogadó adattár, vagy bármely támogatott forrás adattár adatok másolása az Azure SQL Database. Adattároló források/mosdók, a másolási tevékenység által támogatott listájáért lásd: a [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Pontosabban Ez az Azure SQL Database az összekötő támogatja:

- SQL-hitelesítéssel adatok másolását.
- Forrásként SQL-lekérdezést vagy tárolt eljárás használatával adatok beolvasása.
- A fogadó, mint Hozzáfűzés adatok, célként megadott táblája vagy egyéni logikával tárolt eljárás meghívása másolása során.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok részletesen bemutatják adat-előállító tartozó entitások meghatározásához az Azure SQL Database-összekötőhöz használt tulajdonságokat.

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok

A következő tulajdonságok támogatottak csatolt Azure SQL Database szolgáltatáshoz:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot kell beállítani: **AzureSqlDatabase** | Igen |
| connectionString |Adja meg az Azure SQL Database-példányt a connectionString tulajdonság való kapcsolódáshoz szükséges adatokat. Csak az alapszintű hitelesítést is támogatja. Ez a mező megjelölése a SecureString tárolja biztonságos helyen, a Data factoryban vagy [hivatkozik az Azure Key Vault tárolt titkos kulcs](store-credentials-in-key-vault.md). |Igen |
| connectVia | A [integrációs futásidejű](concepts-integration-runtime.md) csatlakozni az adattárolóhoz használandó. Használhat Azure integrációs futásidejű vagy Self-hosted integrációs futásidejű (amennyiben az adattároló magánhálózaton található). Ha nincs megadva, akkor használja az alapértelmezett Azure integrációs futásidejű. |Nem |

> [!IMPORTANT]
> Konfigurálása [Azure SQL Database-tűzfal](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) az adatbázis-kiszolgálót [a kiszolgálóhoz való hozzáféréshez Azure-szolgáltatások engedélyezése](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Emellett konfigurálhatja az adatokat az Azure SQL Database külső az Azure data Factory a helyszíni adatforrásokból beleértve önálló üzemeltetett integrációs futásidejű másolása, megfelelő IP-címtartományt, a gép, amely adatokat küld az Azure SQL Az adatbázis.

**Példa**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
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

Szakaszok és meghatározása adatkészletek esetében elérhető tulajdonságok teljes listájáért tekintse meg az adatkészletek cikket. Ez a témakör az Azure SQL Database dataset által támogatott tulajdonságokról.

Adatok másolása az Azure SQL Database vagy állítsa be a type tulajdonságot az adathalmaz **AzureSqlTable**. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adathalmaz értékre kell állítani: **AzureSqlTable** | Igen |
| tableName |A tábla vagy nézet társított szolgáltatásnak Azure SQL Database-példány neve hivatkozik. | Igen |

**Példa**

```json
{
    "name": "AzureSQLDbDataset",
    "properties":
    {
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Database linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és a rendelkezésre álló tevékenységek meghatározó tulajdonságok teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a témakör az Azure SQL Database-forrás és a fogadó által támogatott tulajdonságokról.

### <a name="azure-sql-database-as-source"></a>Az Azure SQL Database forrásaként

Adatok másolása az Azure SQL Database, állítsa be a forrás típusa a másolási tevékenység **SqlSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakasz:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot a másolási tevékenység forrás értékre kell állítani: **SqlSource** | Igen |
| sqlReaderQuery |Az egyéni SQL-lekérdezés segítségével adatokat olvasni. Példa: `select * from MyTable`. |Nem |
| sqlReaderStoredProcedureName |A tárolt eljárás, amely adatokat olvas a forrástábla neve. Az utolsó SQL-utasítás a következő tárolt eljárást a SELECT utasítással kell lennie. |Nem |
| storedProcedureParameters |A tárolt eljárás paramétereit.<br/>Két érték engedélyezett: a név/érték párok. Nevét és a kis-és a paraméterek meg kell egyeznie a nevek és a kis-és nagybetűhasználat a tárolt eljárás paramétereit. |Nem |

**Vegye figyelembe a következő szempontok:**

- Ha a **sqlReaderQuery** van megadva a SqlSource, a másolási tevékenység fut ez a lekérdezés az adatok lekérdezése az Azure SQL Database forrása. Másik lehetőségként megadhat tárolt eljárás megadásával a **sqlReaderStoredProcedureName** és **storedProcedureParameters** (Ha a tárolt eljárás paraméterek fogadja el).
- Ha nem adja meg "sqlReaderQuery" vagy "sqlReaderStoredProcedureName", az adatkészlet JSON "structure" szakaszában meghatározott oszlopokat segítségével olyan lekérdezést (`select column1, column2 from mytable`) az Azure SQL-adatbázis futtatásához. Az adatkészlet-definícióban nem rendelkezik a "structure", ha minden kiválasztott oszlop. a táblából.
- Amikor **sqlReaderStoredProcedureName**, továbbra is meg kell adnia egy helyőrző **tableName** az adatkészlet JSON tulajdonság.

**Példa: SQL lekérdezést használva.**

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
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
                "type": "SqlSource",
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
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
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
                "type": "SqlSource",
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

### <a name="azure-sql-database-as-sink"></a>Az Azure SQL Database, a fogadó

Adatok másolása az Azure SQL Database, állítsa be a fogadó típusa a másolási tevékenység **SqlSink**. A következő tulajdonságok támogatottak a másolási tevékenység **fogadó** szakasz:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység fogadó type tulajdonsága értékre kell állítani: **SqlSink** | Igen |
| writeBatchSize |Szúr be az SQL-tábla adatokat, amikor a puffer mérete eléri writeBatchSize.<br/>Két érték engedélyezett: egész szám (sorok száma). |Nem (alapértelmezett beállítás 10000) |
| writeBatchTimeout |Várakozási idő a kötegelt beszúrási művelet befejezését, mielőtt azt az időkorlátot.<br/>Két érték engedélyezett: timespan. Példa: "00: 30:00" (30 perc). |Nem |
| preCopyScript |Adjon meg egy SQL-lekérdezést a másolási tevékenység végrehajtása előtt az adatok írása az Azure SQL-adatbázisba. Akkor lesz csak egyszer hívható futtatása példányonként. Ez a tulajdonság segítségével törölje az előre betöltött adatokat. |Nem |
| sqlWriterStoredProcedureName |A tárolt eljárás, amely meghatározza, hogyan alkalmazhat forrásadatok a céloldali tábla, pl. do upserts vagy a saját üzleti logikát használó átalakító neve. <br/><br/>Megjegyzés: Ez a tárolt eljárás fog **kötegenként meghívott**. Ha művelet, amelynek csak egyszer futnak, és érinti a elvégezni a segítségével például törlés/truncate forrásadatok használja szeretné `preCopyScript` tulajdonság. |Nem |
| storedProcedureParameters |A tárolt eljárás paramétereit.<br/>Két érték engedélyezett: a név/érték párok. Nevét és a kis-és a paraméterek meg kell egyeznie a nevek és a kis-és nagybetűhasználat a tárolt eljárás paramétereit. |Nem |
| sqlWriterTableType |Adjon meg egy tábla típus a következő tárolt eljárás használható. Másolási tevékenység elérhetővé teszi az adatok áthelyezése egy ideiglenes táblát, amely a táblatípus. Tárolt eljárás kódot is majd egyesítheti az adatokat, a meglévő adatok másolásának. |Nem |

> [!TIP]
> Amikor adat másolása az Azure SQL Database, a másolási tevékenység hozzáfűzi adatokat a fogadó tábla alapértelmezés szerint. Egy UPSERT vagy további üzleti logika végrehajtásához használja a következő tárolt eljárást a SqlSink. Többet is megtudhat a [tárolt eljárás hívja meg az SQL gyűjtése](#invoking-stored-procedure-for-sql-sink).

**1. példa: adatok hozzáfűzése**

```json
"activities":[
    {
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "writeBatchSize": 100000
            }
        }
    }
]
```

**2. példa: meghívása tárolt eljárás során az upsert másolása**

Többet is megtudhat a [tárolt eljárás hívja meg az SQL gyűjtése](#invoking-stored-procedure-for-sql-sink).

```json
"activities":[
    {
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
                "sqlWriterTableType": "CopyTestTableType",
                "storedProcedureParameters": {
                    "identifier": { "value": "1", "type": "Int" },
                    "stringData": { "value": "str1" }
                }
            }
        }
    }
]
```

## <a name="identity-columns-in-the-target-database"></a>A céladatbázis azonosító oszlop

Ez a szakasz egy példát biztosít arra az adatok másolása a forrástábla nélkül azonosító oszlop azonosító oszlopot tartalmazó táblát.

**Forrástábla:**

```sql
create table dbo.SourceTbl
(
       name varchar(100),
       age int
)
```

**Céltábla:**

```sql
create table dbo.TargetTbl
(
       identifier int identity(1,1),
       name varchar(100),
       age int
)
```

Figyelje meg, hogy a céltábla rendelkezik-e az azonosító oszlop.

**Forrás adatkészlet JSON-definícióból**

```json
{
    "name": "SampleSource",
    "properties": {
        "type": " AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "TestIdentitySQL",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTbl"
        }
    }
}
```

**Cél adatkészlet JSON-definícióból**

```json
{
    "name": "SampleTarget",
    "properties": {
        "structure": [
            { "name": "name" },
            { "name": "age" }
        ],
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "TestIdentitySQL",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "TargetTbl"
        }
    }
}
```

Figyelje meg, hogy a forrás és cél táblázatként különböző sémája (cél rendelkezik egy olyan további oszlop identitású). Ilyen esetben meg kell adnia **struktúra** tulajdonság az a tároló adatkészlet-definícióban, amely nem tartalmazza az identitásoszlop.

## <a name="invoke-stored-procedure-from-sql-sink"></a>A fogadó SQL tárolt eljárás meghívása

Az Azure SQL-adatbázisba, a felhasználó által megadott tárolt adatok másolásakor eljárás is konfigurálva és további paraméterek meghívva.

A tárolt eljárás is használható, ha a beépített másolási mechanizmust erre a célra nem használhatók. Ez jellemzően akkor alkalmazzák, amikor upsert (insert + frissítés) vagy (az oszlop, további értékek, több táblák és hasonló szúrás keresésekor egyesítés) felesleges feldolgozási azért van szükség, a végső beszúrási a céltábla forrásadatok előtt.

A következő példa bemutatja, hogyan hajtsa végre az upsert egy táblába az Azure SQL-adatbázisban tárolt eljárás segítségével. Feltéve, hogy a bemeneti adatok és a fogadó "Marketing" tábla minden egyes három oszlopot tartalmaz: ProfileID, állapotát és kategória. Hajtsa végre a "ProfileID" oszlop alapján upsert, és csak egy adott konkrét kategóriával alkalmazni.

**Kimeneti adatkészlet**

```json
{
    "name": "AzureSQLDbDataset",
    "properties":
    {
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Database linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "Marketing"
        }
    }
}
```

Az alábbiak szerint adja meg a "SqlSink" szakaszban a másolási tevékenység.

```json
"sink": {
    "type": "SqlSink",
    "SqlWriterTableType": "MarketingType",
    "SqlWriterStoredProcedureName": "spOverwriteMarketing",
    "storedProcedureParameters": {
        "category": {
            "value": "ProductA"
        }
    }
}
```

Az adatbázisban azonos nevű, "SqlWriterStoredProcedureName" tárolt eljárás megadása. A kimeneti táblázatba kezelési bemeneti adatokat a megadott forrás, és az egyesítési. Figyelje meg, hogy a paraméter neve, a tárolt eljárás legyen ugyanaz, mint a "tableName" adatkészletben meghatározott.

```sql
CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @category varchar(256)
AS
BEGIN
  MERGE [dbo].[Marketing] AS target
  USING @Marketing AS source
  ON (target.ProfileID = source.ProfileID and target.Category = @category)
  WHEN MATCHED THEN
      UPDATE SET State = source.State
  WHEN NOT MATCHED THEN
      INSERT (ProfileID, State, Category)
      VALUES (source.ProfileID, source.State, source.Category)
END
```

Az adatbázis sqlWriterTableType, azonos nevű tábla típusának azonosítására. Figyelje meg, hogy a tábla típusú sémát a bemeneti adatok által visszaadott sémának megegyezőnek kell lennie.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL，
    [Category] [varchar](256) NOT NULL，
)
```

A tárolt eljárás szolgáltatás kihasználja [Table-Valued paraméterek](https://msdn.microsoft.com/library/bb675163.aspx).

## <a name="data-type-mapping-for-azure-sql-database"></a>Adattípus-hozzárendelése az Azure SQL Database

A/az Azure SQL Database adatok másolásakor a következő leképezéseit segítségével az Azure SQL Database adattípusok Azure Data Factory ideiglenes adattípusok. Lásd: [séma- és írja be a leképezéseket](copy-activity-schema-and-type-mapping.md) hogyan másolási tevékenység van leképezve a séma- és adatok típusa a fogadó tájékozódhat.

| Az Azure SQL adatbázis-adattípust | Data factory ideiglenes adattípus |
|:--- |:--- |
| bigint |Int64 |
| Bináris |Byte] |
| bit |Logikai |
| Karakter |Karakterlánc, Char] |
| dátum |DateTime |
| Dátum és idő |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |DateTimeOffset |
| Decimális |Decimális |
| A FILESTREAM attribútum (varbinary(max)) |Byte] |
| Lebegőpontos |Dupla |
| image |Byte] |
| int |Int32 |
| pénz |Decimális |
| nchar |Karakterlánc, Char] |
| ntext |Karakterlánc, Char] |
| Numerikus |Decimális |
| nvarchar |Karakterlánc, Char] |
| valós |Egyedülálló |
| ROWVERSION |Byte] |
| smalldatetime |DateTime |
| smallint |Int16 |
| kis pénz típusú értéknél |Decimális |
| sql_variant |Objektum * |
| Szöveg |Karakterlánc, Char] |
| time |TimeSpan |
| időbélyeg |Byte] |
| tinyint |Bájt |
| egyedi azonosító |GUID |
| varbinary |Byte] |
| varchar |Karakterlánc, Char] |
| xml |Xml |

## <a name="next-steps"></a>További lépések
Támogatott források és mosdók által a másolási tevékenység során az Azure Data Factory adattárolókhoz listájáért lásd: [adattárolókhoz támogatott](copy-activity-overview.md##supported-data-stores-and-formats).