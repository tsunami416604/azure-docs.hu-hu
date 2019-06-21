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
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: jingwang
ms.openlocfilehash: 62845557f33fd9c4f3c2ec4e239213c75101955d
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67275982"
---
# <a name="copy-data-to-and-from-sql-server-using-azure-data-factory"></a>Adatok másolása, és az SQL Serverről az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki a Data Factory szolgáltatás használ:"]
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

>[!NOTE]
>Az SQL Server **[Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017)** jelenleg nem támogatja az összekötő által. Megkerülő megoldásként használhatja [általános ODBC-összekötő](connector-odbc.md) és az SQL Server ODBC-illesztőt. Hajtsa végre a [Ez az útmutató](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=sql-server-2017) ODBC illesztőprogram letöltési és kapcsolati karakterlánc konfigurációkkal.

## <a name="prerequisites"></a>Előfeltételek

Használja az adatok másolása az SQL Server-adatbázis, amely nem érhető el nyilvánosan, meg kell egy helyi Integration Runtime beállítása. Lásd: [helyi Integration Runtime](create-self-hosted-integration-runtime.md) részleteivel. Az integrációs modul biztosítja egy beépített SQL Server adatbázis-illesztőprogramját, ezért nem kell manuálisan telepítenie az összes illesztőprogram példatípust az adatok a/az SQL Server-adatbázis.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok segítségével határozhatók meg a Data Factory-entitások adott, az SQL Server adatbázis-összekötő-tulajdonságokkal kapcsolatos részletekért.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Az SQL Server-alapú társított szolgáltatás a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot kell beállítani: **SqlServer** | Igen |
| connectionString |Az SQL-hitelesítés vagy a Windows-hitelesítés használatával az SQL Server-adatbázishoz való csatlakozáshoz szükséges connectionString információkat adják meg. Tekintse meg a következő minták.<br/>Ez a mező jelölhetnek egy SecureString tárolja biztonságos helyen a Data Factoryban. Jelszó is helyezheti az Azure Key Vaultban, és ha az SQL-hitelesítés lekéréses a `password` konfigurációs ki a kapcsolati karakterláncot. A táblázat alatti a JSON-példa és [Store hitelesítő adatokat az Azure Key Vaultban](store-credentials-in-key-vault.md) további részleteket a cikkben. |Igen |
| userName |Ha Windows-hitelesítést használ, adja meg a felhasználónevet. Példa: **domainname\\felhasználónév**. |Nem |
| password |Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. Ez a mező megjelölése tárolja biztonságos helyen a Data Factory, a SecureString vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). |Nem |
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

**2. példa: SQL-hitelesítés használata az Azure Key Vaultban jelszóval**

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

**3. példa: Windows-hitelesítés használatával**

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

Az adatok másolása a/SQL Server-adatbázis, a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adatkészlet értékre kell állítani: **SqlServerTable** | Igen |
| tableName |A tábla vagy nézet az adatbázis SQL Server-példány, amelyre a társított szolgáltatás neve hivatkozik. | Nincs forrás, a fogadó Igen |

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

> [!TIP]
> További információ a támogatott írási viselkedéseket, konfigurációk és az ajánlott eljárás a [ajánlott eljárás az adatok betöltése az SQL Server](#best-practice-for-loading-data-into-sql-server).

Adatok másolása az SQL Server, állítsa be a fogadó típusa a másolási tevékenység **SqlSink**. A következő tulajdonságok támogatottak a másolási tevékenység **fogadó** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység fogadó type tulajdonsága értékre kell állítani: **SqlSink** | Igen |
| writeBatchSize |Az SQL-táblába beilleszti sorok száma **kötegenként**.<br/>Engedélyezett értékek a következők: egész szám (sorok száma). Alapértelmezés szerint a Data Factory a megfelelő kötegméret sor mérete alapján dinamikus meghatározásához. |Nem |
| writeBatchTimeout |Várjon, amíg a kötegelt insert művelet befejezését, mielőtt azt az időkorlátot.<br/>Engedélyezett értékek a következők: időtartam. Példa: "00: 30:00" (30 perc). |Nem |
| preCopyScript |Adjon meg egy SQL-lekérdezést az SQL Server adatainak írása előtt hajtsa végre a másolási tevékenység. Azt fogja csak egyszer hívhatók példányonkénti futtatni. Ez a tulajdonság segítségével törölje az előre betöltött adatokat. |Nem |
| sqlWriterStoredProcedureName |A tárolt eljárást, amely meghatározza, hogyan alkalmazhatja a forrásadatok cél táblába neve.<br/>Megjegyzés: Ez a tárolt eljárás lesz **kötegenként meghívása**. Ha azt szeretné, hogy csak egyszer fut, és nem a forrásadatokat, például törlés/truncate, használja a művelet elvégzéséhez `preCopyScript` tulajdonság. |Nem |
| storedProcedureParameters |A tárolt eljárás paraméterei.<br/>Engedélyezett értékek a következők: név-érték párokat. Nevek és a kis-és a paraméterek meg kell egyeznie a neveket és a kis-és nagybetűhasználatot, a tárolt eljárás paraméterértékeinek. |Nem |
| sqlWriterTableType |Adjon meg egy tábla típusú név a tárolt eljárás használható. Másolási tevékenység egy ideiglenes táblát a tábla típusú elérhetővé teszi az adatok áthelyezését. Tárolt eljárás kód majd egyesítheti az adatok másolását a adatokkal. |Nem |

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

**2. példa: egy tárolt eljárás meghívása során másolása**

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

## <a name="best-practice-for-loading-data-into-sql-server"></a>Ajánlott eljárás az adatok betöltése az SQL Server

Adatok másolása az SQL Server esetén szükség lehet különböző írási viselkedés:

- **[Hozzáfűzés](#append-data)** : csak a rendelkező új rekordok; forrás adatok
- **[Upsert](#upsert-data)** : Beszúrások és frissítések; egyaránt rendelkező forrás adatok
- **[Írja felül](#overwrite-entire-table)** : Töltse be újra az egész táblát, minden alkalommal, amikor; szeretnék
- **[Az egyéni logikát írási](#write-data-with-custom-logic)** : További feldolgozás előtt a végső beszúrási a céltáblázatba történő van szükségem.

Tekintse meg a rendre szakaszok konfigurálásával az ADF és az ajánlott eljárásokat.

### <a name="append-data"></a>Adatok hozzáfűzése

Ez a fogadó SQL Server-összekötő alapértelmezett viselkedését, és tegye az ADF **tömeges Beszúrás** hatékonyan írni a táblában. Egyszerűen konfigurálható a forrás és fogadó ennek megfelelően a másolási tevékenység.

### <a name="upsert-data"></a>Adatok beszúrása és frissítése (upsert)

**E beállítás** (javasolt, ha különösen a nagy mennyiségű adat másolása rendelkezik): a **a legtöbb nagy teljesítményű megközelítés** upsert ehhez a rendszer a következő: 

- Először is kihasználhatja a [ideiglenes tábla](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables) a tömeges betöltés összes rekordot a másolási tevékenység használatával. Ideiglenes táblák végzett műveletek nem kerülnek naplózásra, mivel rekordok millióit, betöltheti másodpercek alatt.
- Egy tárolt eljárási tevékenység végrehajtása a alkalmazni az ADF- [EGYESÍTÉSE](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) (vagy a beszúrási/frissítés) utasítás, és használja a temp táblát az összes forrás frissít vagy szúr be egy tranzakcióként életű könyvtárgyorsítótárból mértékének csökkentésével, és olyan műveletek naplózására. A tárolt eljárási tevékenység végén ideiglenes táblára, készen áll a következő upsert ciklusig levágja. 

Tegyük fel, az Azure Data Factory, létrehozhat rendelkező folyamatot egy **másolási tevékenység** a kapcsolt egy **Stored Procedure-tevékenység** sikeres művelet. A korábbi adatokat másol a forrásadattárba a egy adatbázis ideiglenes táblába, például: " **##UpsertTempTable**" adatkészletben levő table neveként majd az utóbbi meghívja az ideiglenes táblából a forrásadatok egyesíteni a céloldali tábla, egy tárolt eljárás és törli az ideiglenes tábla.

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

Az adatbázis határoz meg egy tárolt eljárás egyesítési logikával, például a következő, az a fenti tárolt eljárási tevékenység mutatott. Feltéve, hogy a cél **Marketing** három oszlopokat tartalmazó táblába: **ProfileID**, **állapot**, és **kategória**, és végezze el az upsert alapján a **ProfileID** oszlop.

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

**II. lehetőség:** lehetőségként választhatja [másolási tevékenység belül tárolt eljárás meghívása](#invoking-stored-procedure-for-sql-sink), miközben Megjegyzés: Ez a megközelítés a forrástábla helyett tömeges támaszkodva minden egyes sorára hajtja végre az alapértelmezett módszer is beilleszthet a másolási tevékenység így azt nem fér el a nagy méretű upsert.

### <a name="overwrite-entire-table"></a>Írja felül a teljes tábla

Konfigurálható **preCopyScript** tulajdonság a másolási tevékenység fogadó ebben az esetben minden másolási tevékenység futtatása, ADF végrehajtja a parancsfájl először futtassa a másolat illessze be az adatokat. Például a teljes tábla felülírja a legújabb adatokkal, is megadhat egy parancsfájlt, amely először törölje az összes rekordot a forrás az új adatok tömeges-betöltés előtt.

### <a name="write-data-with-custom-logic"></a>Egyéni logikát az adatok írása

Hasonló leírtak szerint [Upsert adatok](#upsert-data) szakaszban további feldolgozás előtt a végső beszúrási a forrásadatok a céltáblázatba történő, alkalmaznia kell is egy) a nagyméretű méretezési egy ideiglenes táblát betöltési, akkor egy tárolt meghívása az eljárás, vagy b) tárolt eljárás meghívása másolása során.

## <a name="invoking-stored-procedure-for-sql-sink"></a> Az SQL-fogadó tárolt eljárás meghívása

Példatípust az adatok az SQL Server-adatbázisba, is konfigurálni és meghívni a felhasználó által megadott tárolt eljárást a további paraméterek.

> [!TIP]
> Tárolt eljárás meghívása dolgozza fel az adatok sor soronként tömeges művelet, amely nem ajánlott nagy méretű példány helyett. További információkat talál a [ajánlott eljárás az adatok betöltése az SQL Server](#best-practice-for-loading-data-into-sql-server).

Beépített másolási mechanizmust nem, a célt szolgálják, amikor egy tárolt eljárást használhatja például a további feldolgozás előtt a végső beszúrási a forrásadatok a céltáblázatba történő alkalmazásához. Extra feldolgozási példákat egyesítési oszlop, keresse meg a további értéket, és annak több tábla.

A következő minta bemutatja, hogyan egy tárolt eljárást az upsert ehhez az SQL Server-adatbázisban egy táblába. Tegyük fel, amelyek bemeneti és a fogadó **Marketing** tábla minden egyes háromoszloposak: **ProfileID**, **állapot**, és **kategória**. Hajtsa végre az upsert alapján a **ProfileID** oszlopot, és csak egy adott kategória alkalmazhatja azt.

**Kimeneti adatkészlet:** a "tableName" kell lennie a ugyanazon tábla típusú paraméter neve a tárolt eljárás (lásd az alábbi tárolt eljárás szkriptet).

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

Adja meg a **SQL-fogadó** a másolási tevékenység a következő szakaszban.

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

Az adatbázis határoz meg a tárolt eljárás, amelynek a neve megegyezik a **SqlWriterStoredProcedureName**. Kezeli a bemeneti adatokat az adott forrásból származnak, és azt a kimeneti tábla egyesít. A tárolt eljárást a táblázat típusú paraméter neve legyen ugyanaz, mint a **tableName** adatkészletben történő definiálása okozza.

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

Az adatbázis sqlWriterTableType definiálhatja a táblatípus ugyanazzal a névvel. Figyelje meg, hogy a tábla típusú sémát a sémát, a bemeneti adatok által visszaadott megegyezőnek kell lennie.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL，
    [Category] [varchar](256) NOT NULL
)
```

A tárolt eljárás szolgáltatás kihasználja [Table-Valued paraméterek](https://msdn.microsoft.com/library/bb675163.aspx).

## <a name="data-type-mapping-for-sql-server"></a>Adattípus-leképezés az SQL server

Példatípust az adatok, vagy az SQL Server, a következő hozzárendeléseket használják az SQL Server-adattípusok Azure Data Factory-közbenső adattípusok. Lásd: [séma és adatok írja be a hozzárendelések](copy-activity-schema-and-type-mapping.md) megismerheti, hogyan másolási tevékenység leképezi a forrás séma és adatok típusa a fogadó.

| SQL Server data type | Data factory közbenső adattípus |
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
> A típusok képezi le közbenső tizedes tört szám típus jelenleg ADF támogatja a pontosság 28 legfeljebb. Ha adatok 28-nál nagyobb pontossággal rendelkezik, érdemes lehet alakítandó karakterláncot az SQL-lekérdezésben.

## <a name="troubleshooting-connection-issues"></a>Kapcsolati problémák hibaelhárítása

1. Konfigurálja az SQL Server távoli kapcsolatokat fogadjon. Indítsa el a **SQL Server Management Studio**, kattintson a jobb gombbal **kiszolgáló**, és kattintson a **tulajdonságok**. Válassza ki **kapcsolatok** csoportot a listából, és ellenőrzés **a kiszolgáló távoli kapcsolatok engedélyezése**.

    ![Távoli kapcsolatok engedélyezése](media/copy-data-to-from-sql-server/AllowRemoteConnections.png)

    Lásd: [konfigurálja a távelérési kiszolgálói konfigurációs beállítás](https://msdn.microsoft.com/library/ms191464.aspx) a részletes lépéseket.

2. Indítsa el a **SQL Server Configuration Manager**. Bontsa ki a **SQL Server hálózati konfigurációja** , és válassza ki a példány **MSSQLSERVER protokolljai**. A jobb oldali ablaktáblában protokollok kell megjelennie. Kattintson a jobb gombbal a TCP/IP engedélyezése **TCP/IP** kattintva **engedélyezése**.

    ![Engedélyezze a TCP/IP használatát](./media/copy-data-to-from-sql-server/EnableTCPProptocol.png)

    Lásd: [engedélyezheti vagy tilthatja le a kiszolgálói hálózati protokoll](https://msdn.microsoft.com/library/ms191294.aspx) kapcsolatos részletekért és a TCP/IP-protokoll engedélyezése alternatív módszert is.

3. Ugyanebben a ablakban kattintson duplán **TCP/IP** elindításához **TCP/IP-tulajdonságok** ablak.
4. Váltson a **IP-címek** fülre. Legörgetve találja meg **IPAll** szakaszban. Jegyezze fel a **TCP-Port** (alapértelmezett érték a **1433-as**).
5. Hozzon létre egy **szabály a Windows tűzfal** ezen a porton keresztül bejövő adatforgalmát engedélyező a gépen.  
6. **Kapcsolat ellenőrzése**: Szeretne csatlakozni az SQL Server teljesen minősített nevet, használja az SQL Server Management Studio egy másik gépről. Például: `"<machine>.<domain>.corp.<company>.com,1433"`.

## <a name="next-steps"></a>További lépések
A másolási tevékenység az Azure Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md##supported-data-stores-and-formats).
