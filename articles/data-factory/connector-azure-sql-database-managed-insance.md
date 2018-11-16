---
title: Adatok másolása és- tárolókról az Azure SQL Database felügyelt példánya Azure Data Factory használatával |} A Microsoft Docs
description: Ismerje meg és-tárolókról az Azure SQL Database felügyelt példánya Azure Data Factory használatával az adatok áthelyezése.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: jingwang
ms.openlocfilehash: 561e672436c38cd0b3e637b794662483fc630676
ms.sourcegitcommit: 275eb46107b16bfb9cf34c36cd1cfb000331fbff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2018
ms.locfileid: "51706721"
---
# <a name="copy-data-to-and-from-azure-sql-database-managed-instance-using-azure-data-factory"></a>Adatok másolása és az Azure SQL Database felügyelt példányába Azure Data Factory használatával

Ez a cikk ismerteti, hogyan használja a másolási tevékenység az Azure Data Factoryban az adatok másolásához a kezdő és a egy Azure SQL Database felügyelt példányába. Épül a [másolási tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

## <a name="supported-capabilities"></a>Támogatott képességek

Adatok másolása az Azure SQL Database felügyelt példánya a bármely támogatott fogadó adattárba, vagy bármely támogatott forrásadattárból adatok másolása a felügyelt példányhoz. A másolási tevékenység által, források és fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Pontosabban az Azure SQL Database felügyelt példányába összekötő támogatja:

- Másolja az adatokat az **SQL** vagy **Windows** hitelesítést.
- Forrásként SQL-lekérdezést vagy tárolt eljárás használatával adatok beolvasása.
- Fogadóként adatok hozzáfűzése a célként megadott táblája vagy egyéni logikával tárolt eljárás meghívása másolása során.

## <a name="prerequisites"></a>Előfeltételek

Adatok másolása az Azure SQL Database felügyelt példánya a virtuális hálózaton található a használandó kell ugyanabban a vnetben az adatbázist elérő helyi Integration Runtime beállítása. Lásd: [helyi Integration Runtime](create-self-hosted-integration-runtime.md) részleteivel.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Az alábbi szakaszok nyújtanak, amelyek meghatározzák az adott Data Factory-entitások Azure SQL Database felügyelt példányába összekötő-tulajdonságokkal kapcsolatos részletekért.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Az Azure SQL Database felügyelt példányain társított szolgáltatás a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonság értékre kell állítani: **SqlServer** | Igen |
| kapcsolati Sztringje |Adja meg a connectionString adatokat szeretne csatlakozni a felügyelt példányhoz az SQL-hitelesítés vagy a Windows-hitelesítés szükséges. Tekintse meg a következő mintát. Ez a mező megjelölése tárolja biztonságos helyen a Data Factory, a SecureString vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). |Igen |
| Felhasználónév |Ha Windows-hitelesítést használ, adja meg a felhasználónevet. Példa: **domainname\\felhasználónév**. |Nem |
| jelszó |Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. Ez a mező megjelölése tárolja biztonságos helyen a Data Factory, a SecureString vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). |Nem |
| connectVia | A [Integration Runtime](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. A helyi Integration Runtime ugyanabban a vnetben, mint a felügyelt példány üzembe helyezése. |Igen |

>[!TIP]
>Ha nyomja le az "UserErrorFailedToConnectToSqlServer", hibakód: Hiba történt, és üzenet például a "a munkamenet korlátot, az adatbázis XXX elérte.", és adja hozzá `Pooling=false` a kapcsolati karakterláncot, és próbálkozzon újra.

**1. példa: SQL-hitelesítés használata**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**2. példa: Windows-hitelesítés használatával**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=True;"
            },
             "userName": "<domain\\username>",
             "password": {
                "type": "SecureString",
                "value": "<password>"
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

Szakaszok és adatkészletek definiálását tulajdonságainak teljes listájáért tekintse meg az adatkészletek a cikk. Ez a szakasz az Azure SQL Database felügyelt példányába adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Adatok másolása Azure blobból vagy az Azure SQL Database felügyelt példányába, állítsa be a type tulajdonság, az adatkészlet **SqlServerTable**. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adatkészlet értékre kell állítani: **SqlServerTable** | Igen |
| tableName |A tábla vagy nézet a database-példányban, amelyre a társított szolgáltatás neve hivatkozik. | Nincs forrás, a fogadó Igen |

**Példa**

```json
{
    "name": "SQLServerDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<Managed Instance linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz az Azure SQL Database felügyelt példányába forrásaként és fogadó által támogatott tulajdonságok listáját tartalmazza.

### <a name="azure-sql-database-managed-instance-as-source"></a>Az Azure SQL Database felügyelt példányain forrásként

Adatok másolása az Azure SQL Database felügyelt példányába, állítsa be a forrás típusaként a másolási tevékenység **SqlSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrása type tulajdonsága értékre kell állítani: **SqlSource** | Igen |
| sqlReaderQuery |Az egyéni SQL-lekérdezés segítségével olvassa el az adatokat. Példa: `select * from MyTable`. |Nem |
| sqlReaderStoredProcedureName |A tárolt eljárást, amely adatokat olvas be a forrás-tábla neve. Az utolsó SQL-utasítást a tárolt eljárás a SELECT utasítással kell lennie. |Nem |
| storedProcedureParameters |A tárolt eljárás paraméterei.<br/>Engedélyezett értékek a következők: név-érték párokat. Nevek és a kis-és a paraméterek meg kell egyeznie a neveket és a kis-és nagybetűhasználatot, a tárolt eljárás paraméterértékeinek. |Nem |

**Tudnivalók**

- Ha a **sqlReaderQuery** van megadva a SqlSource, a másolási tevékenység fut ez a lekérdezés a felügyelt példány forrás, az adatok beolvasásához. Másik lehetőségként megadhat egy tárolt eljárást megadásával a **sqlReaderStoredProcedureName** és **storedProcedureParameters** (Ha a tárolt eljárás paraméter szükséges).
- Ha "sqlReaderQuery" vagy "sqlReaderStoredProcedureName" tulajdonság nincs megadva, a az adatkészlet JSON "struktúra" szakaszban megadott oszlopok segítségével lekérdezést (`select column1, column2 from mytable`) a felügyelt példány futtatásához. Ha az adatkészlet definíciója nem rendelkezik a "szerkezet", az összes oszlop ki van jelölve, a táblából.

**Példa: SQL-lekérdezés használatával**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Managed Instance input dataset name>",
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

**Példa: Egy tárolt eljárás használatával**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Managed Instance input dataset name>",
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

**A tárolt eljárás definíciója**

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

### <a name="azure-sql-database-managed-instance-as-sink"></a>Az Azure SQL Database felügyelt példánya pedig a fogadó

Adatok másolása az Azure SQL Database felügyelt példányába, állítsa a fogadó típusa a másolási tevékenység **SqlSink**. A következő tulajdonságok támogatottak a másolási tevékenység **fogadó** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység fogadó type tulajdonsága értékre kell állítani: **SqlSink** | Igen |
| WriteBatchSize |Amikor a puffer mérete eléri a writeBatchSize adatok beszúrása SQL-táblát.<br/>Engedélyezett értékek a következők: egész szám (sorok száma). |Nem (alapértelmezett: 10000) |
| writeBatchTimeout |Várjon, amíg a kötegelt insert művelet befejezését, mielőtt azt az időkorlátot.<br/>Engedélyezett értékek a következők: időtartam. Példa: "00: 30:00" (30 perc). |Nem |
| preCopyScript |Adja meg a felügyelt példány az adatok írása előtt hajtsa végre a másolási tevékenység egy SQL-lekérdezést. Azt fogja csak egyszer hívhatók példányonkénti futtatni. Ez a tulajdonság segítségével törölje az előre betöltött adatokat. |Nem |
| sqlWriterStoredProcedureName |A tárolt eljárást, amely meghatározza, hogyan alkalmazhatja a forrásadatok céloldali táblához, pl. do upserts vagy a saját üzleti logika átalakító neve. <br/><br/>Megjegyzés: Ez a tárolt eljárás lesz **kötegenként meghívása**. Ha azt szeretné, hogy csak egyszer fut, és nem a forrásadatokat, például törlés/truncate, használja a művelet elvégzéséhez `preCopyScript` tulajdonság. |Nem |
| storedProcedureParameters |A tárolt eljárás paraméterei.<br/>Engedélyezett értékek a következők: név-érték párokat. Nevek és a kis-és a paraméterek meg kell egyeznie a neveket és a kis-és nagybetűhasználatot, a tárolt eljárás paraméterértékeinek. |Nem |
| sqlWriterTableType |Adjon meg egy tábla típusú név a tárolt eljárás használható. Másolási tevékenység egy ideiglenes táblát a tábla típusú elérhetővé teszi az adatok áthelyezését. Tárolt eljárás kód majd egyesítheti az adatok másolását a adatokkal. |Nem |

> [!TIP]
> Adatok másolása az Azure SQL Database felügyelt példányába, amikor a másolási tevékenység adatokat fűz hozzá a fogadó tábla alapértelmezés szerint. Az UPSERT vagy további üzleti logikát használja a tárolt eljárást az SqlSink. További részletek a [a fogadó SQL tárolt eljárás meghívása](#invoking-stored-procedure-for-sql-sink).

**1. példa: Adatok hozzáfűzése**

```json
"activities":[
    {
        "name": "CopyToSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Managed Instance output dataset name>",
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

**2. példa: Egy tárolt eljárás meghívása során az upsert másolása**

További részletek a [a fogadó SQL tárolt eljárás meghívása](#invoking-stored-procedure-for-sql-sink).

```json
"activities":[
    {
        "name": "CopyToSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Managed Instance output dataset name>",
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

Ez a szakasz azt szemlélteti, amely adatokat másol a forrástábla nem identitásoszlop az identity oszlopot tartalmazó táblát.

**Forrástábla**

```sql
create table dbo.SourceTbl
(
       name varchar(100),
       age int
)
```

**Céltábla**

```sql
create table dbo.TargetTbl
(
       identifier int identity(1,1),
       name varchar(100),
       age int
)
```

Figyelje meg, hogy a céloldali tábla rendelkezik-e egy identitásoszlop.

**Forrás adatkészlet JSON-definíció**

```json
{
    "name": "SampleSource",
    "properties": {
        "type": " SqlServerTable",
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

**Cél adatkészlet JSON-definíció**

```json
{
    "name": "SampleTarget",
    "properties": {
        "structure": [
            { "name": "name" },
            { "name": "age" }
        ],
        "type": "SqlServerTable",
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

Figyelje meg, hogy a forrás és cél táblaként eltérő sémával rendelkezik (cél van egy további oszlop identitás). Ebben az esetben meg kell adnia **struktúra** tulajdonság a célként megadott adatkészlet definíciójában, amely nem tartalmazza az identitásoszlop.

## <a name="invoking-stored-procedure-for-sql-sink"></a> Az SQL-fogadó tárolt eljárás meghívása

Ha másol adatokat az Azure SQL Database felügyelt példányába, egy felhasználó által megadott tárolt eljárás sikerült konfigurálhatók és további paraméterek hívása.

Tárolt eljárás is használható, ha a beépített másolási mechanizmust erre a célra nem használhatók. Ha az upsert (insert és update), vagy további feldolgozás (egyesítés oszlopok keresése további értékeket szúr be a több táblákat, stb.) kell tenni a forrásadatok a céloldali tábla utolsó beszúrási előtt általában szolgál.

A következő minta bemutatja, hogyan hajtsa végre az upsert egy táblába a felügyelt példányon a tárolt eljárás használatával. Feltéve, hogy a bemeneti adatokat és a fogadó "Marketing" tábla minden egyes három oszlopot tartalmaz: ProfileID, állapotát és kategória. Hajtsa végre a "ProfileID" oszlop alapján upsert, és csak egy adott kategóriát a alkalmazni.

**Kimeneti adatkészlet**

```json
{
    "name": "SQLServerDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<Managed Instance linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "Marketing"
        }
    }
}
```

Adja meg az SqlSink szakaszban a másolási tevékenység a következőképpen.

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

Az adatbázis SqlWriterStoredProcedureName definiálhatja a tárolt eljárás ugyanazzal a névvel. A kimeneti táblába bemeneti adatokat a megadott forrás és a merge kezel. A tábla típusú a tárolt eljárás paraméter neve ugyanaz, mint a "tableName" adatkészletben definiált kell lennie.

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

Az adatbázis sqlWriterTableType definiálhatja a táblatípus ugyanazzal a névvel. Figyelje meg, hogy a tábla típusú sémát a sémát, a bemeneti adatok által visszaadott megegyezőnek kell lennie.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL，
    [Category] [varchar](256) NOT NULL，
)
```

A tárolt eljárás szolgáltatás kihasználja [Table-Valued paraméterek](https://msdn.microsoft.com/library/bb675163.aspx).

>[!NOTE]
>Ha pénzt és pénz adattípus meghívása tárolt eljárás által írt, értékek lehet kerekíteni. Adja meg a megfelelő adattípusokat TVP a decimális helyett pénzt és pénz csökkentése érdekében. 

## <a name="data-type-mapping-for-azure-sql-database-managed-instance"></a>Az Azure SQL Database felügyelt példányába adattípus-leképezés

Példatípust az adatok a/az Azure SQL Database felügyelt példányába, a következő hozzárendeléseket használják a felügyelt példány adattípusok Azure Data Factory-közbenső adattípusok. Lásd: [séma és adatok írja be a hozzárendelések](copy-activity-schema-and-type-mapping.md) megismerheti, hogyan másolási tevékenység leképezi a forrás séma és adatok típusa a fogadó.

| Az Azure SQL Database felügyelt példányain adattípus | Data factory közbenső adattípus |
|:--- |:--- |
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
| tinyint |Int16 |
| UniqueIdentifier |GUID |
| varbinary |Byte] |
| varchar |Karakterlánc, Char] |
| xml |Xml |

## <a name="next-steps"></a>További lépések
A másolási tevékenység az Azure Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md##supported-data-stores-and-formats).
