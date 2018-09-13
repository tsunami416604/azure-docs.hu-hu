---
title: Adatok másolása az Azure Data Factory használatával az SQL Server / |} A Microsoft Docs
description: Ismerje meg és-tárolókról, amely a helyszíni SQL Server-adatbázist vagy egy Azure virtuális Gépen az Azure Data Factory használatával az adatok áthelyezése.
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
ms.date: 09/12/2018
ms.author: jingwang
ms.openlocfilehash: b0e9f72bad685d569b4a09baecec8cebc33fefde
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "44717896"
---
# <a name="copy-data-to-and-from-sql-server-using-azure-data-factory"></a>Adatok másolása, és az SQL Serverről az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verzió](v1/data-factory-sqlserver-connector.md)
> * [Aktuális verzió](connector-sql-server.md)

Ez a cikk ismerteti, hogyan használja a másolási tevékenység az Azure Data Factoryban az adatok másolásához a kezdő és a egy SQL Server-adatbázisból. Épül a [másolási tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

## <a name="supported-capabilities"></a>Támogatott képességek

Adatok másolása a/SQL Server-adatbázis bármely támogatott fogadó adattárba, vagy bármely támogatott forrásadattárból adatok másolása az SQL Server-adatbázishoz. A másolási tevékenység által, források és fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Pontosabban az SQL Server-összekötő támogatja:

- SQL Server 2016, 2014, 2012, 2008 R2, 2008 és 2005 verzióra
- Másolja az adatokat az **SQL** vagy **Windows** hitelesítést.
- Forrásként SQL-lekérdezést vagy tárolt eljárás használatával adatok beolvasása.
- Fogadóként adatok hozzáfűzése a célként megadott táblája vagy egyéni logikával tárolt eljárás meghívása másolása során.

## <a name="prerequisites"></a>Előfeltételek

Használja az adatok másolása az SQL Server-adatbázis, amely nem érhető el nyilvánosan, meg kell egy helyi Integration Runtime beállítása. Lásd: [helyi Integration Runtime](create-self-hosted-integration-runtime.md) részleteivel. Az integrációs modul biztosítja egy beépített SQL Server adatbázis-illesztőprogramját, ezért nem kell manuálisan telepítenie az összes illesztőprogram példatípust az adatok a/az SQL Server-adatbázis.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok segítségével határozhatók meg a Data Factory-entitások adott, az SQL Server adatbázis-összekötő-tulajdonságokkal kapcsolatos részletekért.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Az SQL Server-alapú társított szolgáltatás a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonság értékre kell állítani: **SqlServer** | Igen |
| kapcsolati Sztringje |Az SQL-hitelesítés vagy a Windows-hitelesítés használatával az SQL Server-adatbázishoz való csatlakozáshoz szükséges connectionString információkat adják meg. Tekintse meg a következő mintát. Ez a mező megjelölése tárolja biztonságos helyen a Data Factory, a SecureString vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). |Igen |
| Felhasználónév |Ha Windows-hitelesítést használ, adja meg a felhasználónevet. Példa: **domainname\\felhasználónév**. |Nem |
| jelszó |Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. Ez a mező megjelölése tárolja biztonságos helyen a Data Factory, a SecureString vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). |Nem |
| connectVia | A [Integration Runtime](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. (Ha az adattár nyilvánosan hozzáférhető) használhatja a helyi Integration Runtime vagy az Azure integrációs modul. Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. |Nem |

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

Szakaszok és adatkészletek definiálását tulajdonságainak teljes listájáért tekintse meg az adatkészletek a cikk. Ez a szakasz az adatkészletet az SQL Server által támogatott tulajdonságok listáját tartalmazza.

Adatok másolása Azure blobból vagy az SQL Server-adatbázist, állítsa be a type tulajdonság, az adatkészlet **SqlServerTable**. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adatkészlet értékre kell állítani: **SqlServerTable** | Igen |
| tableName |A tábla vagy nézet az adatbázis SQL Server-példány, amelyre a társított szolgáltatás neve hivatkozik. | Igen |

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

Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz az SQL Server-adatforrások és adatfogadók által támogatott tulajdonságok listáját tartalmazza.

### <a name="sql-server-as-source"></a>SQL Server forrásként

Adatok másolása az SQL Server, állítsa be a forrás típusaként a másolási tevékenység **SqlSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrása type tulajdonsága értékre kell állítani: **SqlSource** | Igen |
| sqlReaderQuery |Az egyéni SQL-lekérdezés segítségével olvassa el az adatokat. Példa: `select * from MyTable`. |Nem |
| sqlReaderStoredProcedureName |A tárolt eljárást, amely adatokat olvas be a forrás-tábla neve. Az utolsó SQL-utasítást a tárolt eljárás a SELECT utasítással kell lennie. |Nem |
| storedProcedureParameters |A tárolt eljárás paraméterei.<br/>Engedélyezett értékek a következők: név-érték párokat. Nevek és a kis-és a paraméterek meg kell egyeznie a neveket és a kis-és nagybetűhasználatot, a tárolt eljárás paraméterértékeinek. |Nem |

**Tudnivalók:**

- Ha a **sqlReaderQuery** van megadva a SqlSource, a másolási tevékenység a lekérdezés fut az SQL Server adatforrás az adatok beolvasásához. Másik lehetőségként megadhat egy tárolt eljárást megadásával a **sqlReaderStoredProcedureName** és **storedProcedureParameters** (Ha a tárolt eljárás paraméter szükséges).
- Ha nem ad meg "sqlReaderQuery" vagy "sqlReaderStoredProcedureName", a az adatkészlet JSON "struktúra" szakaszban megadott oszlopok segítségével lekérdezést (`select column1, column2 from mytable`) az SQL Server futtatásához. Ha az adatkészlet definíciója nem rendelkezik a "szerkezet", az összes oszlop ki van jelölve, a táblából.
- Ha használ **sqlReaderStoredProcedureName**, továbbra is meg kell adnia egy helyőrző **tableName** tulajdonságot az adatkészlet JSON.

**Példa: SQL-lekérdezés használatával**

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

**Példa: tárolt eljárás használatával**

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

### <a name="sql-server-as-sink"></a>SQL Server pedig a fogadó

Adatok másolása az SQL Server, állítsa be a fogadó típusa a másolási tevékenység **SqlSink**. A következő tulajdonságok támogatottak a másolási tevékenység **fogadó** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység fogadó type tulajdonsága értékre kell állítani: **SqlSink** | Igen |
| WriteBatchSize |Amikor a puffer mérete eléri a writeBatchSize adatok beszúrása SQL-táblát.<br/>Engedélyezett értékek a következők: egész szám (sorok száma). |Nem (alapértelmezett: 10000) |
| writeBatchTimeout |Várjon, amíg a kötegelt insert művelet befejezését, mielőtt azt az időkorlátot.<br/>Engedélyezett értékek a következők: időtartam. Példa: "00: 30:00" (30 perc). |Nem |
| preCopyScript |Adjon meg egy SQL-lekérdezést az SQL Server adatainak írása előtt hajtsa végre a másolási tevékenység. Azt fogja csak egyszer hívhatók példányonkénti futtatni. Ez a tulajdonság segítségével törölje az előre betöltött adatokat. |Nem |
| sqlWriterStoredProcedureName |A tárolt eljárást, amely meghatározza, hogyan alkalmazhatja a forrásadatok céloldali táblához, pl. do upserts vagy a saját üzleti logika átalakító neve. <br/><br/>Megjegyzés: Ez a tárolt eljárás lesz **kötegenként meghívása**. Ha azt szeretné, hogy csak egyszer fut, és nem a forrásadatokat, például törlés/truncate, használja a művelet elvégzéséhez `preCopyScript` tulajdonság. |Nem |
| storedProcedureParameters |A tárolt eljárás paraméterei.<br/>Engedélyezett értékek a következők: név-érték párokat. Nevek és a kis-és a paraméterek meg kell egyeznie a neveket és a kis-és nagybetűhasználatot, a tárolt eljárás paraméterértékeinek. |Nem |
| sqlWriterTableType |Adjon meg egy tábla típusú név a tárolt eljárás használható. Másolási tevékenység egy ideiglenes táblát a tábla típusú elérhetővé teszi az adatok áthelyezését. Tárolt eljárás kód majd egyesítheti az adatok másolását a adatokkal. |Nem |

> [!TIP]
> Ha az adatok másolása az SQL Server, a másolási tevékenység adatokat fűz hozzá a fogadó tábla alapértelmezés szerint. Az UPSERT vagy további üzleti logikát használja a tárolt eljárást az SqlSink. További részletek a [a fogadó SQL tárolt eljárás meghívása](#invoking-stored-procedure-for-sql-sink).

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

**2. példa: egy tárolt eljárás meghívása során az upsert másolása**

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

Ez a szakasz azt szemlélteti, amely adatokat másol a forrástábla nem identitásoszlop az identity oszlopot tartalmazó táblát.

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

Adatok másolása az SQL Server database-be, amikor egy felhasználó által meghatározott sikerült konfigurálhatók és további paraméterek meghívása tárolt eljárás.

Tárolt eljárás is használható, ha a beépített másolási mechanizmust erre a célra nem használhatók. Ha az upsert (insert és update), vagy további feldolgozás (egyesítés oszlopok keresése további értékeket szúr be a több táblákat, stb.) kell tenni a forrásadatok a céloldali tábla utolsó beszúrási előtt általában szolgál.

A következő minta bemutatja, hogyan egy tárolt eljárást az upsert ehhez az SQL Server-adatbázisban egy táblába. Feltéve, hogy a bemeneti adatokat és a fogadó "Marketing" tábla minden egyes három oszlopot tartalmaz: ProfileID, állapotát és kategória. Hajtsa végre a "ProfileID" oszlop alapján upsert, és csak egy adott kategóriát a alkalmazni.

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

## <a name="data-type-mapping-for-sql-server"></a>Adattípus-leképezés az SQL server

Példatípust az adatok, vagy az SQL Server, a következő hozzárendeléseket használják az SQL Server-adattípusok Azure Data Factory-közbenső adattípusok. Lásd: [séma és adatok írja be a hozzárendelések](copy-activity-schema-and-type-mapping.md) megismerheti, hogyan másolási tevékenység leképezi a forrás séma és adatok típusa a fogadó.

| SQL Server-adatok típusa | Data factory közbenső adattípus |
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

## <a name="troubleshooting-connection-issues"></a>Kapcsolati problémák elhárítása

1. Konfigurálja az SQL Server távoli kapcsolatokat fogadjon. Indítsa el a **SQL Server Management Studio**, kattintson a jobb gombbal **kiszolgáló**, és kattintson a **tulajdonságok**. Válassza ki **kapcsolatok** csoportot a listából, és ellenőrzés **a kiszolgáló távoli kapcsolatok engedélyezése**.

    ![Távoli kapcsolatok engedélyezése](media/copy-data-to-from-sql-server/AllowRemoteConnections.png)

    Lásd: [konfigurálja a távelérési kiszolgálói konfigurációs beállítás](https://msdn.microsoft.com/library/ms191464.aspx) a részletes lépéseket.

2. Indítsa el a **SQL Server Configuration Manager**. Bontsa ki a **SQL Server hálózati konfigurációja** , és válassza ki a példány **MSSQLSERVER protokolljai**. A jobb oldali ablaktáblában protokollok kell megjelennie. Kattintson a jobb gombbal a TCP/IP engedélyezése **TCP/IP** kattintva **engedélyezése**.

    ![Engedélyezze a TCP/IP használatát](./media/copy-data-to-from-sql-server/EnableTCPProptocol.png)

    Lásd: [engedélyezheti vagy tilthatja le a kiszolgálói hálózati protokoll](https://msdn.microsoft.com/library/ms191294.aspx) kapcsolatos részletekért és a TCP/IP-protokoll engedélyezése alternatív módszert is.

3. Ugyanebben a ablakban kattintson duplán **TCP/IP** elindításához **TCP/IP-tulajdonságok** ablak.
4. Váltson a **IP-címek** fülre. Legörgetve találja meg **IPAll** szakaszban. Jegyezze fel a ** TCP-Port ** (alapértelmezett érték a **1433-as**).
5. Hozzon létre egy **szabály a Windows tűzfal** ezen a porton keresztül bejövő adatforgalmát engedélyező a gépen.  
6. **Kapcsolat ellenőrzése**: szeretne csatlakozni az SQL Server teljesen minősített nevet, használja az SQL Server Management Studio egy másik gépről. Például: `"<machine>.<domain>.corp.<company>.com,1433"`.


## <a name="next-steps"></a>További lépések
A másolási tevékenység az Azure Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md##supported-data-stores-and-formats).
