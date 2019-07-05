---
title: Adatok másolása, és az SQL Serverről az Azure Data Factory használatával |} A Microsoft Docs
description: Tudnivalók az adatok áthelyezése a és a helyszíni SQL Server-adatbázist vagy egy Azure-beli virtuális gépen az Azure Data Factory használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: jingwang
ms.openlocfilehash: a6767c7c8931898c44fd748dbe4299b8ed23eb9c
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443293"
---
# <a name="copy-data-to-and-from-sql-server-by-using-azure-data-factory"></a>Adatok másolása, és az SQL Serverről az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki az Azure Data Factory használt verzióját:"]
> * [1-es verzió](v1/data-factory-sqlserver-connector.md)
> * [Aktuális verzió](connector-sql-server.md)

Ez a cikk ismerteti, hogyan használja a másolási tevékenység az Azure Data Factoryban az adatok másolásához a kezdő és a egy SQL Server-adatbázis. Épül a [másolási tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

## <a name="supported-capabilities"></a>Támogatott képességek

Adatokat másolja a kezdő és a egy SQL Server-adatbázis bármely támogatott fogadó adattárba. Másik lehetőségként másolhat bármely támogatott forrásadattárból adatokat egy SQL Server-adatbázis. A másolási tevékenység által források vagy fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Pontosabban az SQL Server-összekötő támogatja:

- SQL Server-verziók 2016, 2014, 2012, 2008 R2, 2008 és 2005.
- Adatok másolása az SQL- vagy Windows-hitelesítés használatával.
- Forrásként adatok beolvasása egy SQL-lekérdezést vagy tárolt eljárás használatával.
- Fogadóként adatok hozzáfűzése táblát vagy egy tárolt eljárást az egyéni logikát meghívása másolása során.

>[!NOTE]
>Az SQL Server [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017) nem támogatja ennek az összekötőnek most. Megkerülő megoldásként használható olyan [általános ODBC-összekötő](connector-odbc.md) és a egy SQL Server ODBC-illesztőt. Hajtsa végre a [Ez az útmutató](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=sql-server-2017) ODBC illesztőprogram letöltési és kapcsolati karakterlánc konfigurációkkal.

## <a name="prerequisites"></a>Előfeltételek

Adatok másolása az SQL Server-adatbázis, amely nem a nyilvánosan elérhető-e használni, szüksége egy saját üzemeltetésű integrációs modul beállításához. További információkért lásd: [saját üzemeltetésű integrációs modul](create-self-hosted-integration-runtime.md). Az integrációs modul egy beépített SQL Server adatbázis-illesztőprogram biztosít. Minden illesztőprogram manuális telepítése vagy az SQL Server-adatbázist az adatok másolásakor nincs szükségünk.

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok segítségével határozhatók meg a Data Factory-entitások adott és az SQL Server adatbázis-összekötő-tulajdonságokkal kapcsolatos részletekért.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A következő tulajdonságok támogatottak az SQL Server-alapú társított szolgáltatás:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot állítsa **SqlServer**. | Igen |
| connectionString |Adja meg **connectionString** információkat, amelyeket szeretne csatlakozni az SQL Server-adatbázis, SQL-hitelesítés vagy a Windows-hitelesítés használatával. Tekintse meg a következő minták.<br/>Jelölje meg a mező **SecureString** tárolja biztonságos helyen az Azure Data Factoryban. Is helyezheti a jelszót az Azure Key Vaultban. Ha SQL-hitelesítés, kérje le a `password` konfigurációs ki a kapcsolati karakterláncot. További információkért tekintse meg a tábla a következő példa a JSON és [Store hitelesítő adatokat az Azure Key Vaultban](store-credentials-in-key-vault.md). |Igen |
| userName |Adjon meg egy felhasználónevet, Windows-hitelesítés használata esetén. Például **domainname\\felhasználónév**. |Nem |
| password |Adja meg a felhasználói fiók a megadott felhasználónévhez tartozó jelszót. Jelölje meg a mező **SecureString** tárolja biztonságos helyen az Azure Data Factoryban. Is [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). |Nem |
| connectVia | Ez [integrációs modul](concepts-integration-runtime.md) az adattárban való kapcsolódásra szolgál. Egy saját üzemeltetésű integrációs modult vagy az Azure-beli integrációs modul is használhatja, ha az adattároló rendszer nyilvánosan elérhető-e. Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. |Nem |

>[!TIP]
>Ha eléri a következő hibakóddal: "UserErrorFailedToConnectToSqlServer" hiba történt, valamint egy üzenetet, és mint "a munkamenet korlátot, az adatbázis XXX és elérte az", adja hozzá a `Pooling=false` a kapcsolati karakterláncot, és próbálkozzon újra.

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

**2. példa: SQL-hitelesítés használata az Azure Key Vault-jelszóval**

```json
{
    "name": "SqlServerLinkedService",
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

Szakaszok és adatkészletek definiálását tulajdonságainak teljes listájáért tekintse meg az adatkészletek a cikk. Ez a szakasz az SQL Server-adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Az adatok másolása a kezdő és a egy SQL Server-adatbázis, a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adatkészlet értékre kell állítani **SqlServerTable**. | Igen |
| tableName |Ez a tulajdonság pedig a tábla vagy nézet az SQL Server adatbázis-példány, amelyre a társított szolgáltatás hivatkozik a neve. | Nincs forrás, a fogadó Igen |

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
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és a tevékenységek meghatározásához használható tulajdonságok teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz a forrás SQL Server és a fogadó által támogatott tulajdonságok listáját tartalmazza.

### <a name="sql-server-as-a-source"></a>SQL Server-adatforrásként

Adatok másolása az SQL Server, állítsa be a forrás típusaként a másolási tevékenység **SqlSource**. A másolási tevékenység forrás szakaszban a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrása típusa tulajdonságát állítsa **SqlSource**. | Igen |
| sqlReaderQuery |Az egyéni SQL-lekérdezés segítségével olvassa el az adatokat. Például: `select * from MyTable`. |Nem |
| sqlReaderStoredProcedureName |Ez a tulajdonság, amely adatokat olvas a forrástábla tárolt eljárás neve. Az utolsó SQL-utasítást a tárolt eljárás a SELECT utasítással kell lennie. |Nem |
| storedProcedureParameters |Ezeket a paramétereket a tárolt eljárás vonatkoznak.<br/>Megengedett értékek: neve vagy értéke párokat. A neveket és a kis-és a paraméterek meg kell egyeznie a neveket és a kis-és nagybetűhasználatot, a tárolt eljárás paraméterértékeinek. |Nem |

**Tudnivalók:**

- Ha **sqlReaderQuery** megadott **SqlSource**, a másolási tevékenység a lekérdezés fut az SQL Server adatforrás az adatok beolvasásához. Emellett megadhatja a tárolt eljárás megadásával **sqlReaderStoredProcedureName** és **storedProcedureParameters** Ha paramétereket fogadja tárolt eljárást.
- Ha nem adja meg vagy **sqlReaderQuery** vagy **sqlReaderStoredProcedureName**, a az adatkészlet JSON "struktúra" szakaszban megadott oszlopok lekérdezés létrehozására szolgálnak. A lekérdezés `select column1, column2 from mytable` fut az SQL Server. Ha az adatkészlet-definícióban nem tartalmaz "szerkezet", az összes oszlop ki van jelölve, a táblából.

**Példa: SQL-lekérdezés használata**

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

**Példa: Tárolt eljárás használata**

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

### <a name="sql-server-as-a-sink"></a>SQL Server fogadóként

> [!TIP]
> További információ a támogatott írási viselkedéseket, konfigurációk és ajánlott eljárásait [ajánlott eljárás az adatok betöltése az SQL Server](#best-practice-for-loading-data-into-sql-server).

Adatok másolása az SQL Server, állítsa be a fogadó típusa a másolási tevékenység **SqlSink**. A másolási tevékenység fogadó szakaszban a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység fogadó típusa tulajdonságát állítsa **SqlSink**. | Igen |
| writeBatchSize |Az SQL-táblába beszúrandó sorok számát *kötegenként*.<br/>Megengedett értékek: sorok számának egész számok. Alapértelmezés szerint az Azure Data Factory dinamikusan a sor mérete alapján megfelelő Köteg mérete határozza meg. |Nem |
| writeBatchTimeout |Ez a tulajdonság határozza meg a várakozási idő a kötegelt insert művelet befejezését, mielőtt azt az időkorlátot.<br/>Megengedett értékek: az időtartam számára. Például "00: 30:00" 30 percig. |Nem |
| preCopyScript |Ez a tulajdonság megadja a másolási tevékenység futtatása előtt írja az adatokat az SQL Server egy SQL-lekérdezést. Indítva csak egyszer futtatni példányonkénti. Ez a tulajdonság segítségével törölje az előre betöltött adatokat. |Nem |
| sqlWriterStoredProcedureName |Ez a név a tárolt eljárás, amely meghatározza, hogyan alkalmazhatja a forrásadatok a célként megadott táblába.<br/>A tárolt eljárás *kötegenként meghívása*. Egy műveletet, amely csak egyszer fut le, és nem tartalmazó adatforrásból, például törlés vagy csonkolása teheti a `preCopyScript` tulajdonság. |Nem |
| storedProcedureParameters |Ezeket a paramétereket használják a tárolt eljárást.<br/>Megengedett értékek: neve vagy értéke párokat. A neveket és a kis-és a paraméterek meg kell egyeznie a neveket és a kis-és nagybetűhasználatot, a tárolt eljárás paraméterértékeinek. |Nem |
| sqlWriterTableType |Ez a tulajdonság adja meg egy tábla írja be a tárolt eljárás használható. A másolási tevékenység egy ideiglenes táblát a tábla típusú elérhetővé teszi az adatok áthelyezését. Tárolt eljárás kód majd egyesítheti az éppen másolt adatokat a adatokkal. |Nem |

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

**2. példa: Tárolt eljárás meghívása során másolása**

További részletek a [a egy SQL-fogadó tárolt eljárás meghívása](#invoke-a-stored-procedure-from-a-sql-sink).

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

## <a name="best-practice-for-loading-data-into-sql-server"></a>Ajánlott eljárás az adatok betöltése az SQL Server

Adatok másolása az SQL Server esetén szükség lehet különböző írási viselkedés:

- [Hozzáfűzés](#append-data): A forrásadatok csak új rekordokat tartalmaz.
- [Upsert](#upsert-data): A forrásadatok beszúrások és frissítések is rendelkezik.
- [Írja felül](#overwrite-the-entire-table): Töltse be újra a teljes táblát minden alkalommal, amikor szeretnék.
- [Az egyéni logikát írási](#write-data-with-custom-logic): További feldolgozás előtt a végső beszúrási a céltáblázatba történő van szükségem.

Tekintse meg a megfelelő szakaszokat konfigurálása az Azure Data Factory és az ajánlott eljárásokat.

### <a name="append-data"></a>Adatok hozzáfűzése

Adatok hozzáfűzése az SQL Server fogadó összekötő alapértelmezett viselkedését. Az Azure Data Factory végzi a tömeges beszúrás hatékonyan írni a táblában. Konfigurálhatja a forrás és fogadó ennek megfelelően a másolási tevékenység.

### <a name="upsert-data"></a>Adatok beszúrása és frissítése (upsert)

**1. lehetőség:** Ha rendelkezik egy nagy mennyiségű adat másolása, egy upsert ehhez a következő módon használható: 

- Először egy [ideiglenes tábla](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables) a tömeges betöltés összes rögzíti a másolási tevékenység használatával. Nem naplózza az ideiglenes táblákra mutató műveletet, mert rekordok millióit, betöltheti másodpercek alatt.
- A tárolt eljárási tevékenység futtatása az Azure Data Factoryban a alkalmazni egy [EGYESÍTÉSE](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) vagy a beszúrási/frissítés utasítást. Összes forrás frissít vagy szúr be egy tranzakcióként használja az ideiglenes tábla. Ezzel a módszerrel adatváltások és naplózási műveletek száma csökken. A tárolt eljárási tevékenység végén az ideiglenes tábla levágása, készen áll a következő upsert ciklusig.

Tegyük fel, az Azure Data Factory, létrehozhat rendelkező folyamatot egy **másolási tevékenység** a kapcsolt egy **Stored Procedure-tevékenység**. A korábbi adatokat másol a forrásadattárba a adatbázis ideiglenes táblába, például **##UpsertTempTable**, a table neveként az adatkészletben. Ezután az utóbbi egy tárolt eljárást hív meg az ideiglenes táblából a forrásadatok egyesítse a céltábla és az ideiglenes tábla karbantartása.

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

Adja meg az adatbázis egyesítési logikával, például a következő példának, amely az előző tárolt eljárás tevékenység a mutatott tárolt eljárás. Tegyük fel, hogy a cél a **Marketing** három oszlopokat tartalmazó táblába: **ProfileID**, **állapot**, és **kategória**. Hajtsa végre az upsert alapján a **ProfileID** oszlop.

```sql
CREATE PROCEDURE [dbo].[spMergeData]
AS
BEGIN
    MERGE TargetTable AS target
    USING ##UpsertTempTable AS source
    ON (target.[ProfileID] = source.[ProfileID])
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT matched THEN
        INSERT ([ProfileID], [State], [Category])
      VALUES (source.ProfileID, source.State, source.Category);
    
    TRUNCATE TABLE ##UpsertTempTable
END
```

**2. lehetőség:** Is választhat [belül a másolási tevékenység tárolt eljárás meghívása](#invoke-a-stored-procedure-from-a-sql-sink). Ez a módszer minden egyes sor futtatja a forrástábla, mint az alapértelmezett módszer a másolási tevékenység, amely nem megfelelő, nagy léptékű upsert a tömeges beszúrás helyett.

### <a name="overwrite-the-entire-table"></a>Írja felül a teljes tábla

Konfigurálhatja a **preCopyScript** egy másolási tevékenység fogadó tulajdonságot. Ebben az esetben minden másolási tevékenység, amely futtatja, az Azure Data Factory futtatja először. Ezután futtatja a másolat illessze be az adatokat. A teljes tábla felülírja a legújabb adatokkal, adja meg például egy parancsfájlt, amely először törölje az összes rekordot tömeges, mielőtt az új adatok betöltése a forrásból.

### <a name="write-data-with-custom-logic"></a>Egyéni logikát az adatok írása

Az egyéni logikát az adatok írása a lépések hasonlóak ismertetett a [Upsert adatok](#upsert-data) szakaszban. Amikor a alkalmazni szeretne további feldolgozás előtt a céltáblázatba történő, nagy méretekben a forrásadatok a végső beszúrási teheti két dolog egyikét: 

- Tölti be az ideiglenes táblát, és ezután meghívja a tárolt eljárást. 
- Tárolt eljárás meghívása másolása során.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a> A egy SQL-fogadó tárolt eljárás meghívása

Adatok másolása az SQL Server-adatbázis esetén is konfigurálhatja és felhasználó által megadott tárolt eljárást a további paraméterek meghívása.

> [!TIP]
> Tárolt eljárás meghívása dolgozza fel az adatokat soronként helyett tömeges műveletet, amely nagyméretű példány esetében nem ajánlott. További információkat talál a [ajánlott eljárás az adatok betöltése az SQL Server](#best-practice-for-loading-data-into-sql-server).

Tárolt eljárás segítségével használhatja, ha a beépített másolási mechanizmusokkal nem szolgálnak ki erre a célra. Például akkor, ha további feldolgozás előtt a végső beszúrási a forrásadatok a céltáblázatba történő alkalmazásához. Extra feldolgozási példákat akkor, ha a kívánt oszlopok egyesítése, további értékek keresése és adatok beszúrása több táblából.

A következő minta bemutatja, hogyan egy tárolt eljárást az upsert ehhez az SQL Server-adatbázisban egy táblába. Tegyük fel, amely a bemeneti adatokat és a fogadó **Marketing** tábla minden egyes háromoszloposak: **ProfileID**, **állapot**, és **kategória**. Hajtsa végre az upsert alapján a **ProfileID** oszlopot, és csak egy adott kategória alkalmazhatja azt.

**Kimeneti adatkészlet:** A "tableName" az ugyanazon tábla típusú paraméter neve a tárolt eljárás, ahogyan a következő tárolt eljárás parancsfájl:

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

Adja meg a **SQL-fogadó** a másolási tevékenység a következő szakaszban:

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

Az adatbázis határoz meg a tárolt eljárás neve megegyezik a **SqlWriterStoredProcedureName**. Kezeli a bemeneti adatokat az adott forrásból származnak, és azt a kimeneti tábla egyesít. A tábla típusú a tárolt eljárás paraméter neve megegyezik a **tableName** adatkészletben történő definiálása okozza.

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

Az adatbázis határoz meg a táblatípus, amelynek a neve megegyezik **sqlWriterTableType**. A táblatípus sémája megegyezik a sémanévvel, a bemeneti adatokat adott vissza.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL，
    [Category] [varchar](256) NOT NULL
)
```

A tárolt eljárás szolgáltatás kihasználja [tábla értékű paraméterek](https://msdn.microsoft.com/library/bb675163.aspx).

## <a name="data-type-mapping-for-sql-server"></a>Az SQL Server adattípus-leképezés

Adatok másolása a kezdő és a SQL Server esetén a következő hozzárendeléseket szolgálnak az SQL Server adattípusok Azure Data Factory-közbenső adattípusok. Hogyan a másolási tevékenység leképezi a forrás séma és adatok típusa a fogadó kapcsolatban lásd: [séma és adatok írja be a hozzárendelések](copy-activity-schema-and-type-mapping.md).

| SQL Server data type | Az Azure Data Factory köztes adatok típusa |
|:--- |:--- |
| bigint |Int64 |
| binary |Byte[] |
| bit |Boolean |
| char |String, Char[] |
| date |DateTime |
| Datetime |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM attribute (varbinary(max)) |Byte[] |
| Float |Double |
| image |Byte[] |
| int |Int32 |
| money |Decimal |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numeric |Decimal |
| nvarchar |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Object |
| text |String, Char[] |
| time |TimeSpan |
| timestamp |Byte[] |
| tinyint |Int16 |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |Xml |

>[!NOTE]
> Az adattípusok alapján, amelyek ideiglenes tizedes típusra jelenleg Azure Data Factory támogatja a legfeljebb 28 pontosság. Ha 28-nál nagyobb pontosságú igénylő adatait, fontolja meg egy karakterláncot egy SQL-lekérdezést az átalakítás.

## <a name="troubleshoot-connection-issues"></a>Csatlakozási problémáinak hibaelhárítása

1. Konfigurálja az SQL Server-példány távoli kapcsolatokat fogadjon. Indítsa el **SQL Server Management Studio**, kattintson a jobb gombbal **kiszolgáló**, és válassza ki **tulajdonságok**. Válassza ki **kapcsolatok** csoportot a listából, és válassza ki a **engedélyezze a távoli kapcsolatokat ezen a kiszolgálón** jelölőnégyzetet.

    ![Távoli kapcsolatok engedélyezése](media/copy-data-to-from-sql-server/AllowRemoteConnections.png)

    Részletes lépéseiért lásd: [konfigurálja a távelérési kiszolgálói konfigurációs beállítás](https://msdn.microsoft.com/library/ms191464.aspx).

2. Indítsa el **SQL Server Configuration Manager**. Bontsa ki a **SQL Server hálózati konfigurációja** , és válassza ki a példány **MSSQLSERVER protokolljai**. Protokollok a jobb oldali ablaktáblán jelennek meg. Kattintson a jobb gombbal a TCP/IP engedélyezése **TCP/IP** és kiválasztásával **engedélyezése**.

    ![Engedélyezze a TCP/IP használatát](./media/copy-data-to-from-sql-server/EnableTCPProptocol.png)

    További információkat és más módon, hogy a TCP/IP protokoll: [engedélyezheti vagy tilthatja le a kiszolgálói hálózati protokoll](https://msdn.microsoft.com/library/ms191294.aspx).

3. Ugyanebben a ablakban kattintson duplán **TCP/IP** elindíthatja a **TCP/IP-tulajdonságok** ablak.
4. Váltson a **IP-címek** fülre. Legörgetve találja meg a **IPAll** szakaszban. Jegyezze fel a **TCP-Port**. Az alapértelmezett érték **1433-as**.
5. Hozzon létre egy **szabály a Windows tűzfal** ezen a porton keresztül bejövő adatforgalmát engedélyező a gépen. 
6. **Kapcsolat ellenőrzése**: Csatlakozhat az SQL Server teljes tartományneve, használja az SQL Server Management Studio egy másik gépről. Például: `"<machine>.<domain>.corp.<company>.com,1433"`.

## <a name="next-steps"></a>További lépések
A másolási tevékenység az Azure Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md##supported-data-stores-and-formats).
