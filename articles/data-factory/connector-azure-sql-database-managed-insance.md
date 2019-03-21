---
title: Adatok másolása és az Azure SQL Database felügyelt példány az Azure Data Factory használatával |} A Microsoft Docs
description: 'Útmutató: adatok áthelyezése és az Azure SQL Database felügyelt példány az Azure Data Factory használatával.'
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: jingwang
ms.openlocfilehash: 782027f19d4e82f26fc1265f25b86223386d7182
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57903385"
---
# <a name="copy-data-to-and-from-azure-sql-database-managed-instance-by-using-azure-data-factory"></a>Adatok másolása és az Azure SQL Database felügyelt példány az Azure Data Factory használatával

Ez a cikk ismerteti, hogyan használja a másolási tevékenység az Azure Data Factoryban az adatok másolásához, és az Azure SQL Database felügyelt példányába. Épül a [másolási tevékenység áttekintéséből](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

## <a name="supported-capabilities"></a>Támogatott képességek

Másolhat adatokat az Azure SQL Database felügyelt példányain bármely támogatott fogadó adattárba. Is másolhatja adatokat bármely támogatott forrás adattárolóból a felügyelt példányhoz. A másolási tevékenység által forrásként és fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Pontosabban az Azure SQL Database felügyelt példányába összekötő támogatja:

- Adatok másolása az SQL- vagy Windows-hitelesítés használatával.
- Forrásként adatok beolvasása egy SQL-lekérdezést vagy tárolt eljárás használatával.
- Fogadóként adatok hozzáfűzése táblát vagy egy tárolt eljárást az egyéni logikát meghívása másolása során.

Az SQL Server [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017) jelenleg nem támogatja. 

## <a name="prerequisites"></a>Előfeltételek

Adatok másolása az Azure SQL Database felügyelt példánya, amely egy virtuális hálózaton található a használ, állítsa be egy saját üzemeltetésű integrációs modult, hogy hozzáférhessen az adatbázishoz. További információkért lásd: [saját üzemeltetésű integrációs modul](create-self-hosted-integration-runtime.md).

Ha üzembe helyezi a saját üzemeltetésű integrációs modult a felügyelt példány ugyanazon a virtuális hálózaton található, ellenőrizze, hogy az integrációs modult tartalmazó számítógépen, mint a felügyelt példány egy másik alhálózatot. A saját üzemeltetésű integrációs modult egy másik virtuális hálózatban, mint a felügyelt példány üzembe helyezése, ha egy virtuális hálózatok közötti társviszony vagy egy virtuális hálózat virtuális hálózati kapcsolat is használhatja. További információkért lásd: [alkalmazását az Azure SQL Database felügyelt példányába való csatlakozás](../sql-database/sql-database-managed-instance-connect-app.md).

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok segítségével határozhatók meg a Data Factory-entitások adott és az Azure SQL Database felügyelt példányába összekötő-tulajdonságokkal kapcsolatos részletekért.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Az Azure SQL Database felügyelt példányába társított szolgáltatás a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot állítsa **SqlServer**. | Igen. |
| kapcsolati Sztringje |Ez a tulajdonság határozza meg a connectionString információkat, amelyeket a példányhoz való csatlakozáshoz a felügyelt SQL-hitelesítés vagy a Windows-hitelesítés használatával. További információkért lásd az alábbi példákat. <br/>Ez a mező jelölhetnek egy SecureString tárolja biztonságos helyen a Data Factoryban. Jelszó is helyezheti az Azure Key Vaultban, és ha az SQL-hitelesítés lekéréses a `password` konfigurációs ki a kapcsolati karakterláncot. A táblázat alatti a JSON-példa és [Store hitelesítő adatokat az Azure Key Vaultban](store-credentials-in-key-vault.md) további részleteket a cikkben. |Igen. |
| Felhasználónév |Ez a tulajdonság egy felhasználónevét határozza meg, ha a Windows-hitelesítést használ. Például **domainname\\felhasználónév**. |Nem. |
| jelszó |Ez a tulajdonság határozza meg a felhasználói fiók a megadott felhasználónévhez tartozó jelszót. Válassza ki **SecureString** a connectionString információkat tárolja biztonságos helyen a Data Factoryban való vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). |Nem. |
| connectVia | Ez [integrációs modul](concepts-integration-runtime.md) az adattárban való kapcsolódásra szolgál. A felügyelt példányt az azonos virtuális hálózatba a saját üzemeltetésű integrációs modul üzembe helyezése. |Igen. |

>[!TIP]
>Láthatja, hogy a hiba kódja "UserErrorFailedToConnectToSqlServer" üzenetet, például "a munkamenet korlátot, az adatbázis XXX, és elérte." Ez a hiba akkor fordul elő, ha hozzá `Pooling=false` a kapcsolati karakterláncot, és próbálkozzon újra.

**1. példa: SQL-hitelesítés használata**

```json
{
    "name": "AzureSqlMILinkedService",
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

**2. példa: SQL-hitelesítés használata az Azure Key Vaultban jelszó**

```json
{
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;"
            },
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**3. példa: Windows-hitelesítés használata**

```json
{
    "name": "AzureSqlMILinkedService",
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

Tekintse meg az adatkészletek cikk szakaszok és adatkészletek definiálásához rendelkezésre álló tulajdonságok teljes listáját. Ez a szakasz az Azure SQL Database felügyelt példányába adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Adatok másolása és az Azure SQL Database felügyelt példányába, állítsa be a type tulajdonság, az adatkészlet **SqlServerTable**. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adatkészlet értékre kell állítani **SqlServerTable**. | Igen. |
| tableName |Ez a tulajdonság az a név a tábla vagy nézet, amelyre a társított szolgáltatás hivatkozik az adatbázis példányában. | A forrás nem. Igen, a fogadó. |

**Példa**

```json
{
    "name": "AzureSqlMIDataset",
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

Szakaszok és a tevékenységek meghatározásához használható tulajdonságok teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz az Azure SQL Database felügyelt példányába forrásaként és fogadó által támogatott tulajdonságok listáját tartalmazza.

### <a name="azure-sql-database-managed-instance-as-a-source"></a>Az Azure SQL Database felügyelt példányain forrásként

Adatok másolása az Azure SQL Database felügyelt példányába, állítsa be a forrás típusaként a másolási tevékenység **SqlSource**. A másolási tevékenység forrás szakaszban a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrása típusa tulajdonságát állítsa **SqlSource**. | Igen. |
| sqlReaderQuery |Ez a tulajdonság az egyéni SQL-lekérdezést használ adatokat olvasni. Például: `select * from MyTable`. |Nem. |
| sqlReaderStoredProcedureName |Ez a tulajdonság, amely adatokat olvas a forrástábla tárolt eljárás neve. Az utolsó SQL-utasítást a tárolt eljárás a SELECT utasítással kell lennie. |Nem. |
| storedProcedureParameters |Ezeket a paramétereket a tárolt eljárás vonatkoznak.<br/>Megengedett értékek: neve vagy értéke párokat. A neveket és a kis-és a paraméterek meg kell egyeznie a neveket és a kis-és nagybetűhasználatot, a tárolt eljárás paraméterértékeinek. |Nem. |

Vegye figyelembe a következő szempontokat:

- Ha **sqlReaderQuery** megadott **SqlSource**, a másolási tevékenység fut ez a lekérdezés a felügyelt példány forrás, az adatok beolvasásához. Emellett megadhatja a tárolt eljárás megadásával **sqlReaderStoredProcedureName** és **storedProcedureParameters** Ha paramétereket fogadja tárolt eljárást.
- Ha nem adja meg vagy a **sqlReaderQuery** vagy **sqlReaderStoredProcedureName** tulajdonság, az adatkészlet JSON-lekérdezés összeállításához használt "struktúra" szakaszában meghatározott oszlopokat. A lekérdezés `select column1, column2 from mytable` fut a következő felügyelt példányt. Ha az adatkészlet-definícióban nem tartalmaz "szerkezet", az összes oszlop ki van jelölve, a táblából.

**Példa: SQL-lekérdezés használata**

```json
"activities":[
    {
        "name": "CopyFromAzureSqlMI",
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

**Példa: Tárolt eljárás használata**

```json
"activities":[
    {
        "name": "CopyFromAzureSqlMI",
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

### <a name="azure-sql-database-managed-instance-as-a-sink"></a>Az Azure SQL Database felügyelt példányain fogadóként

Adatok másolása az Azure SQL Database felügyelt példányába, állítsa a fogadó típusa a másolási tevékenység **SqlSink**. A másolási tevékenység fogadó szakaszban a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység fogadó típusa tulajdonságát állítsa **SqlSink**. | Igen. |
| WriteBatchSize |Ez a tulajdonság végez adatbeszúrást az SQL-táblát a pufferméret writeBatchSize elérésekor.<br/>Megengedett értékek: sorok számának egész számok. |Nem (alapértelmezett: 10,000). |
| writeBatchTimeout |Ez a tulajdonság határozza meg a várakozási idő a kötegelt insert művelet befejezését, mielőtt azt az időkorlátot.<br/>Megengedett értékek: az időtartam. Például "00: 30:00," vagyis 30 perc. |Nem. |
| preCopyScript |Ez a tulajdonság határozza meg, hogy a másolási tevékenység végrehajtása előtt az adatok írása a következő felügyelt példányt az SQL-lekérdezést. Indítva csak egyszer futtatni példányonkénti. Ez a tulajdonság használatával előre betöltött adatok törlése. |Nem. |
| sqlWriterStoredProcedureName |Ez a név a tárolt eljárás, amely meghatározza, hogyan alkalmazhatja a forrásadatok a célként megadott táblába. Eljárások példák upserts vagy átalakítások ehhez használatával saját üzleti logikája szerint. <br/><br/>A tárolt eljárás *kötegenként meghívása*. Egy műveletet, amely csak egyszer fut le, és nem tartalmazó adatforrásból, például törlés vagy csonkolása teheti a `preCopyScript` tulajdonság. |Nem. |
| storedProcedureParameters |Ezeket a paramétereket használják a tárolt eljárást.<br/>Megengedett értékek: neve vagy értéke párokat. A neveket és a kis-és a paraméterek meg kell egyeznie a neveket és a kis-és nagybetűhasználatot, a tárolt eljárás paraméterértékeinek. |Nem. |
| sqlWriterTableType |Ez a tulajdonság adja meg egy tábla írja be a tárolt eljárás használható. A másolási tevékenység egy ideiglenes táblát a tábla típusú elérhetővé teszi az adatok áthelyezését. Tárolt eljárás kód majd egyesítheti az éppen másolt adatokat a adatokkal. |Nem. |

> [!TIP]
> Ha az adatok Azure SQL Database felügyelt példányába történő másolását követően a másolási tevékenység adatokat fűz hozzá a fogadó tábla alapértelmezés szerint. Az upsert vagy további üzleti logikát használja a tárolt eljárást az SqlSink. További információkért lásd: [a egy SQL-fogadó tárolt eljárás meghívása](#invoke-a-stored-procedure-from-a-sql-sink).

**1. példa: Adatok hozzáfűzése**

```json
"activities":[
    {
        "name": "CopyToAzureSqlMI",
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

**2. példa: Tárolt eljárás meghívása során az upsert másolása**

További részletek a [a egy SQL-fogadó tárolt eljárás meghívása](#invoke-a-stored-procedure-from-a-sql-sink).

```json
"activities":[
    {
        "name": "CopyToAzureSqlMI",
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

Az alábbi példa adatokat másol az identity oszlopot tartalmazó táblát egy forrás nincs identity oszlopot tartalmazó táblából.

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

Figyelje meg, hogy a forrás- és table eltérő sémával rendelkezik. A céltábla tartalmaz azonosító oszlopot. Ebben a forgatókönyvben adja meg a célként megadott adatkészlet definíciójában, amely nem tartalmazza az identitásoszlop "struktúra" tulajdonság.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a> A egy SQL-fogadó tárolt eljárás meghívása

Adatok átmásolja az Azure SQL Database felügyelt példányába, amikor egy tárolt eljárást konfigurálhatók és további paraméterek, Ön által megadott hívása.

Tárolt eljárás segítségével használhatja, ha a beépített másolási mechanizmusokkal nem szolgálnak ki erre a célra. Ha az upsert (frissítés + insert) vagy az extra feldolgozási kell elvégezni a forrásadatok a céloldali tábla utolsó beszúrási előtt általában szolgál. További feldolgozás feladatokat, köztük a további értéket, és annak több tábla keresésekor oszlopok egyesítése tartalmazhatnak.

A következő minta bemutatja, hogyan hajtsa végre az upsert egy táblába a következő felügyelt példányt a tárolt eljárás használatával. A minta azt feltételezi, hogy a bemeneti adatokat és a fogadó "Marketing" tábla is három oszlopot: ProfileID, állapotát és kategória. Hajtsa végre az upsert ProfileID oszlop alapján, és csak egy adott kategóriát a alkalmazni.

**Kimeneti adatkészlet**

```json
{
    "name": "AzureSqlMIDataset",
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

Adja meg az SqlSink szakaszban másolási tevékenység a következő:

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

Az adatbázis SqlWriterStoredProcedureName definiálhatja a tárolt eljárás ugyanazzal a névvel. Kezeli a bemeneti adatokat az adott forrásból származnak, és azt a kimeneti tábla egyesít. A tábla típusú a tárolt eljárás paraméter neve megegyezik a "tableName" az adatkészletben definiált.

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
      VALUES (source.ProfileID, source.State, source.Category);
END
```

Az adatbázis sqlWriterTableType definiálhatja a táblatípus ugyanazzal a névvel. A táblatípus sémája megegyezik a sémanévvel, a bemeneti adatokat adott vissza.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL，
    [Category] [varchar](256) NOT NULL
)
```

A tárolt eljárás szolgáltatás kihasználja [tábla értékű paraméterek](https://msdn.microsoft.com/library/bb675163.aspx).

## <a name="data-type-mapping-for-azure-sql-database-managed-instance"></a>Az Azure SQL Database felügyelt példányába adattípus-leképezés

Ha és az Azure SQL Database felügyelt példánya a rendszer az adatokat másolja, a következő hozzárendeléseket a rendszer az Azure SQL Database felügyelt példányába adattípusok Azure Data Factory közbenső adattípusok használja. Című cikk nyújt tájékoztatást, hogy a másolási tevékenység leképezi a forrás séma és adatok típusa a fogadó, [séma és adatok írja be a hozzárendelések](copy-activity-schema-and-type-mapping.md).

| Az Azure SQL Database felügyelt példányain adattípus | Az Azure Data Factory köztes adatok típusa |
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
| sql_variant |Objektum |
| szöveg |Karakterlánc, Char] |
| time |Időtartam |
| időbélyeg |Byte] |
| tinyint |Int16 |
| UniqueIdentifier |GUID |
| varbinary |Byte] |
| varchar |Karakterlánc, Char] |
| xml |XML |

>[!NOTE]
> Az adattípusok alapján, amelyek ideiglenes tizedes típusra jelenleg Azure Data Factory támogatja a legfeljebb 28 pontosság. Ha 28-nál nagyobb pontosságú igénylő adatait, fontolja meg egy karakterláncot egy SQL-lekérdezést az átalakítás.

## <a name="next-steps"></a>További lépések
A másolási tevékenység az Azure Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md##supported-data-stores-and-formats).
