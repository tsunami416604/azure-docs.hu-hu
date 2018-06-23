---
title: Adatok másolása az Azure Data Factory használatához SQL Server |} Microsoft Docs
description: További tudnivalók áthelyezni az adatokat, vagy a helyszíni SQL Server-adatbázist vagy egy Azure virtuális gép Azure Data Factory használatával.
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
ms.date: 06/22/2018
ms.author: jingwang
ms.openlocfilehash: cca35d75f6d5560a621d377ae544eeba41434962
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/22/2018
ms.locfileid: "36316379"
---
# <a name="copy-data-to-and-from-sql-server-using-azure-data-factory"></a>Másolja az adatokat, és az SQL-kiszolgáló Azure Data Factory használatával
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1. verzió – Általánosan elérhető](v1/data-factory-sqlserver-connector.md)
> * [2. verzió – Előzetes verzió](connector-sql-server.md)

Ez a cikk ismerteti, hogyan használható a másolási tevékenység során az Azure Data Factory másolja az adatokat, a kezdő és egy SQL Server-adatbázis a. Buildekről nyújtanak a [másolása tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [SQL Server-összekötőt a V1](v1/data-factory-sqlserver-connector.md).

## <a name="supported-capabilities"></a>Támogatott képességei

Adatok másolása az/SQL Server-adatbázis bármely támogatott fogadó adattárolóhoz, vagy adatok másolása az bármely támogatott forrás adattár az SQL Server-adatbázist. Adattároló források/mosdók, a másolási tevékenység által támogatott listájáért lásd: a [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Pontosabban az SQL Server-összekötő támogatja:

- SQL Server 2016, 2014, 2012, 2008 R2, 2008 és 2005 verziója
- Adatok másolása **SQL** vagy **Windows** hitelesítés.
- Forrásként SQL-lekérdezést vagy tárolt eljárás használatával adatok beolvasása.
- A fogadó, mint Hozzáfűzés adatok, célként megadott táblája vagy egyéni logikával tárolt eljárás meghívása másolása során.

## <a name="prerequisites"></a>Előfeltételek

Szeretné használni az adatok másolása az SQL Server-adatbázis, amely nincs nyilvánosan elérhető, akkor be kell állítania egy Self-hosted integrációs futásidejű. Lásd: [Self-hosted integrációs futásidejű](create-self-hosted-integration-runtime.md) cikkben alább. Az integrációs futásidejű biztosít egy beépített SQL Server adatbázis-illesztőprogramját, ezért nem, manuálisan kell telepítenie minden olyan illesztőprogram /, az SQL Server-adatbázis az adatok másolásakor kell.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok részletesen bemutatják adat-előállító entitások adott SQL Server adatbázis-összekötővel meghatározásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok

Kapcsolódó SQL Server szolgáltatás támogatott a következő tulajdonságokkal:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot kell beállítani: **SqlServer** | Igen |
| connectionString |Az SQL-hitelesítéssel vagy a Windows-hitelesítés SQL Server-adatbázishoz való kapcsolódáshoz szükséges connectionString információkat adják meg. Tekintse meg a következő mintát, és adhat hozzá további tulajdonságok pl. AlwaysOn felvenni kiegészítése. Ez a mező megjelölése a SecureString tárolja biztonságos helyen, a Data factoryban vagy [hivatkozik az Azure Key Vault tárolt titkos kulcs](store-credentials-in-key-vault.md). |Igen |
| Felhasználónév |Adja meg a felhasználónevet, ha a Windows-hitelesítést használ. Példa: **tartománynév\\felhasználónév**. |Nem |
| jelszó |Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. Ez a mező megjelölése a SecureString tárolja biztonságos helyen, a Data factoryban vagy [hivatkozik az Azure Key Vault tárolt titkos kulcs](store-credentials-in-key-vault.md). |Nem |
| connectVia | A [integrációs futásidejű](concepts-integration-runtime.md) csatlakozni az adattárolóhoz használandó. Használhatja Self-hosted integrációs futásidejű vagy Azure integrációs futásidejű (ha az adattároló nyilvánosan elérhető). Ha nincs megadva, akkor használja az alapértelmezett Azure integrációs futásidejű. |Nem |

**1. példa: SQL-hitelesítés használatával**

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

**2. példa: a Windows-hitelesítés használatával**

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

Szakaszok és meghatározása adatkészletek esetében elérhető tulajdonságok teljes listájáért tekintse meg az adatkészletek cikket. Ez a témakör az SQL Server dataset által támogatott tulajdonságokról.

Adatok másolása, vagy az SQL Server-adatbázis, az adatkészlet típus tulajdonságának beállítása **SqlServerTable**. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adathalmaz értékre kell állítani: **SqlServerTable** | Igen |
| tableName |A tábla vagy nézet, amelyre a társított szolgáltatás SQL Server-adatbázispéldány neve hivatkozik. | Igen |

**Példa**

```json
{
    "name": "SQLServerDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<SQL Server linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és a rendelkezésre álló tevékenységek meghatározó tulajdonságok teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a témakör az SQL Server-forrás és a fogadó által támogatott tulajdonságokról.

### <a name="sql-server-as-source"></a>SQL Server forrásaként

Adatok másolása az SQL Server, állítsa be a forrás típusa a másolási tevékenység **SqlSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakasz:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot a másolási tevékenység forrás értékre kell állítani: **SqlSource** | Igen |
| sqlReaderQuery |Az egyéni SQL-lekérdezés segítségével adatokat olvasni. Példa: `select * from MyTable`. |Nem |
| sqlReaderStoredProcedureName |A tárolt eljárás, amely adatokat olvas a forrástábla neve. Az utolsó SQL-utasítás a következő tárolt eljárást a SELECT utasítással kell lennie. |Nem |
| storedProcedureParameters |A tárolt eljárás paramétereit.<br/>Két érték engedélyezett: a név/érték párok. Nevét és a kis-és a paraméterek meg kell egyeznie a nevek és a kis-és nagybetűhasználat a tárolt eljárás paramétereit. |Nem |

**Vegye figyelembe a következő szempontok:**

- Ha a **sqlReaderQuery** van megadva a SqlSource, a másolási tevékenység során ez a lekérdezés fut az SQL Server forrás az adatok eléréséhez. Másik lehetőségként megadhat tárolt eljárás megadásával a **sqlReaderStoredProcedureName** és **storedProcedureParameters** (Ha a tárolt eljárás paraméterek fogadja el).
- Ha nem adja meg "sqlReaderQuery" vagy "sqlReaderStoredProcedureName", az adatkészlet JSON "structure" szakaszában meghatározott oszlopokat segítségével olyan lekérdezést (`select column1, column2 from mytable`) az SQL Server futtatásához. Az adatkészlet-definícióban nem rendelkezik a "structure", ha minden kiválasztott oszlop. a táblából.
- Amikor **sqlReaderStoredProcedureName**, továbbra is meg kell adnia egy helyőrző **tableName** az adatkészlet JSON tulajdonság.

**Példa: SQL lekérdezést használva.**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Server input dataset name>",
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
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Server input dataset name>",
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

### <a name="sql-server-as-sink"></a>SQL Server, a fogadó

Adatok másolása az SQL Server, állítsa be a fogadó típusa a másolási tevékenység **SqlSink**. A következő tulajdonságok támogatottak a másolási tevékenység **fogadó** szakasz:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység fogadó type tulajdonsága értékre kell állítani: **SqlSink** | Igen |
| writeBatchSize |Szúr be az SQL-tábla adatokat, amikor a puffer mérete eléri writeBatchSize.<br/>Két érték engedélyezett: egész szám (sorok száma). |Nem (alapértelmezett: 10000) |
| writeBatchTimeout |Várakozási idő a kötegelt beszúrási művelet befejezését, mielőtt azt az időkorlátot.<br/>Két érték engedélyezett: timespan. Példa: "00: 30:00" (30 perc). |Nem |
| preCopyScript |Adja meg a másolási tevékenység végrehajtása előtt az adatok írása az SQL Server egy SQL-lekérdezést. Akkor lesz csak egyszer hívható futtatása példányonként. Ez a tulajdonság segítségével törölje az előre betöltött adatokat. |Nem |
| sqlWriterStoredProcedureName |A tárolt eljárás, amely meghatározza, hogyan alkalmazhat forrásadatok a céloldali tábla, pl. do upserts vagy a saját üzleti logikát használó átalakító neve. <br/><br/>Megjegyzés: Ez a tárolt eljárás fog **kötegenként meghívott**. Ha művelet, amelynek csak egyszer futnak, és érinti a elvégezni a segítségével például törlés/truncate forrásadatok használja szeretné `preCopyScript` tulajdonság. |Nem |
| storedProcedureParameters |A tárolt eljárás paramétereit.<br/>Két érték engedélyezett: a név/érték párok. Nevét és a kis-és a paraméterek meg kell egyeznie a nevek és a kis-és nagybetűhasználat a tárolt eljárás paramétereit. |Nem |
| sqlWriterTableType |Adjon meg egy tábla típus a következő tárolt eljárás használható. Másolási tevékenység elérhetővé teszi az adatok áthelyezése egy ideiglenes táblát, amely a táblatípus. Tárolt eljárás kódot is majd egyesítheti az adatokat, a meglévő adatok másolásának. |Nem |

> [!TIP]
> Amikor adat másolása az SQL Server, a másolási tevékenység hozzáfűzi adatokat a fogadó tábla alapértelmezés szerint. Egy UPSERT vagy további üzleti logika végrehajtásához használja a következő tárolt eljárást a SqlSink. Többet is megtudhat a [tárolt eljárás hívja meg az SQL gyűjtése](#invoking-stored-procedure-for-sql-sink).

**1. példa: adatok hozzáfűzése**

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
                "referenceName": "<SQL Server output dataset name>",
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
                "referenceName": "<SQL Server output dataset name>",
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

Ez a szakasz azt mutatja, hogy adatokat másol a forrástábla nem azonosító oszlop az azonosító oszlopot tartalmazó táblát.

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

**Cél adatkészlet JSON-definícióból**

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

Figyelje meg, hogy a forrás és cél táblázatként különböző sémája (cél rendelkezik egy olyan további oszlop identitású). Ilyen esetben meg kell adnia **struktúra** tulajdonság az a tároló adatkészlet-definícióban, amely nem tartalmazza az identitásoszlop.

## <a name="invoking-stored-procedure-for-sql-sink"></a> A fogadó SQL tárolt eljárás meghívása

Adatok másolása az SQL Server-adatbázisba, amikor egy felhasználó által megadott tárolt eljárás nem sikerült konfigurálni és további paraméterek meghívva.

A tárolt eljárás is használható, ha a beépített másolási mechanizmust erre a célra nem használhatók. Ez jellemzően akkor alkalmazzák, amikor upsert (insert + frissítés) vagy (az oszlop, további értékek, több táblák és hasonló szúrás keresésekor egyesítés) felesleges feldolgozási azért van szükség, a végső beszúrási a céltábla forrásadatok előtt.

A következő példa bemutatja, hogyan egy upsert tegye az SQL Server-adatbázisban egy táblába a tárolt eljárás használatával. Feltéve, hogy a bemeneti adatok és a fogadó "Marketing" tábla minden egyes három oszlopot tartalmaz: ProfileID, állapotát és kategória. Hajtsa végre a "ProfileID" oszlop alapján upsert, és csak egy adott konkrét kategóriával alkalmazni.

**Kimeneti adatkészlet**

```json
{
    "name": "SQLServerDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<SQL Server linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "Marketing"
        }
    }
}
```

Az alábbiak szerint adja meg a SqlSink szakasz a másolási tevékenység.

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

Az adatbázis SqlWriterStoredProcedureName, adja meg a tárolt eljárás ugyanazzal a névvel. A kimeneti táblázatba kezelési bemeneti adatokat a megadott forrás, és az egyesítési. Figyelje meg, hogy a paraméter neve, a tárolt eljárás legyen ugyanaz, mint a "tableName" adatkészletben meghatározott.

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

## <a name="data-type-mapping-for-sql-server"></a>Adattípus-leképezés az SQL server

Ha másol adatokat az/SQL Server, a következő megfeleltetéseket segítségével az SQL Server adattípusok Azure Data Factory ideiglenes adattípusokat. Lásd: [séma- és írja be a leképezéseket](copy-activity-schema-and-type-mapping.md) hogyan másolási tevékenység van leképezve a séma- és adatok típusa a fogadó tájékozódhat.

| SQL Server-adattípus | Data factory ideiglenes adattípus |
|:--- |:--- |
| bigint |Int64 |
| Bináris |Byte] |
| bit |Logikai |
| karakter |Karakterlánc, Char] |
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
| numerikus |Decimális |
| nvarchar |Karakterlánc, Char] |
| valós |Önálló |
| ROWVERSION |Byte] |
| smalldatetime |DateTime |
| smallint |Int16 |
| kis pénz típusú értéknél |Decimális |
| sql_variant |Objektum * |
| szöveg |Karakterlánc, Char] |
| time |A TimeSpan |
| időbélyeg |Byte] |
| tinyint |Int16 |
| egyedi azonosító |GUID |
| varbinary |Byte] |
| varchar |Karakterlánc, Char] |
| xml |Xml |

## <a name="troubleshooting-connection-issues"></a>Kapcsolati problémák elhárítása

1. Konfigurálja az SQL Server távoli kapcsolatokat fogadjon. Indítsa el **SQL Server Management Studio**, kattintson a jobb gombbal **server**, és kattintson a **tulajdonságok**. Válassza ki **kapcsolatok** csoportot a listából, és ellenőrzés **a kiszolgáló távoli kapcsolatok engedélyezése a**.

    ![Távoli kapcsolatok engedélyezése](media/copy-data-to-from-sql-server/AllowRemoteConnections.png)

    Lásd: [konfigurálja a távelérési kiszolgálói konfigurációs beállítás megadása](https://msdn.microsoft.com/library/ms191464.aspx) a részletes lépéseket.

2. Indítsa el **SQL Server Konfigurációkezelő**. Bontsa ki a **SQL Server hálózati konfigurációja** , és válassza ki a példány **MSSQLSERVER protokolljai**. A jobb oldali ablaktáblában protokollok kell megjelennie. Engedélyezze a TCP/IP protokollt kattintson a jobb gombbal **TCP/IP** elemre kattintva **engedélyezése**.

    ![Engedélyezze a TCP/IP protokollt](./media/copy-data-to-from-sql-server/EnableTCPProptocol.png)

    Lásd: [engedélyezheti vagy tilthatja le a hálózati protokoll](https://msdn.microsoft.com/library/ms191294.aspx) részleteit és más módon, hogy a TCP/IP-protokoll.

3. Az azonos ablakban kattintson duplán **TCP/IP** indítása **TCP/IP-tulajdonságok** ablak.
4. Váltás a **IP-címek** fülre. Görgessen le lásd: **IPAll** szakasz. Jegyezze fel a ** TCP-Port ** (alapértelmezett érték a **1433**).
5. Hozzon létre egy **szabály a Windows tűzfal** ezen a porton keresztül bejövő adatforgalmat engedélyezi a számítógépen.  
6. **Ellenőrizze a kapcsolat**: teljesen minősített nevet az SQL Serverhez való kapcsolódáshoz használja az SQL Server Management Studio egy másik gépről. Például: `"<machine>.<domain>.corp.<company>.com,1433"`.


## <a name="next-steps"></a>További lépések
Támogatott források és mosdók által a másolási tevékenység során az Azure Data Factory adattárolókhoz listájáért lásd: [adattárolókhoz támogatott](copy-activity-overview.md##supported-data-stores-and-formats).
