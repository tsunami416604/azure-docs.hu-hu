---
title: Adatok másolása SQL Serverba és onnan a Azure Data Factory használatával | Microsoft Docs
description: Ismerje meg, hogyan helyezheti át a helyszíni vagy egy Azure-beli virtuális gépen lévő SQL Server-adatbázis adatait Azure Data Factory használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: jingwang
ms.openlocfilehash: 5dcbb2c25511277eaf46d6c9f4afc007a180f8a6
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68827873"
---
# <a name="copy-data-to-and-from-sql-server-by-using-azure-data-factory"></a>Adatok másolása SQL Serverba és onnan a Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki a használt Azure Data Factory verzióját:"]
> * [1-es verzió](v1/data-factory-sqlserver-connector.md)
> * [Aktuális verzió](connector-sql-server.md)

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok SQL Server-adatbázisba való másolásához. A másolási [tevékenység](copy-activity-overview.md) áttekintő cikkében található, amely a másolási tevékenység általános áttekintését jeleníti meg.

## <a name="supported-capabilities"></a>Támogatott képességek

A és a rendszerből másolhatja át az adatait egy SQL Server adatbázisba bármely támogatott fogadó adattárba. Vagy bármely támogatott forrás adattárból átmásolhat egy SQL Server adatbázisba. A másolási tevékenység által források vagy fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Pontosabban, ez az SQL Server-összekötő a következőket támogatja:

- SQL Server verziók 2016, 2014, 2012, 2008 R2, 2008 és 2005.
- Adatok másolása SQL vagy Windows-hitelesítés használatával.
- Forrásként egy SQL-lekérdezés vagy tárolt eljárás használatával beolvashatja az adatokból.
- Fogadóként, az adatsorok hozzáfűzésével vagy egy tárolt eljárás meghívásával egyéni logikával a másolás során.

[SQL Server Express LocalDB](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-express-localdb?view=sql-server-2017) nem támogatott.

>[!NOTE]
>Ez [](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017) az összekötő jelenleg nem támogatja az SQL Server Always Encryptedt. A megoldáshoz használhat egy [általános ODBC](connector-odbc.md) -összekötőt és egy SQL Server ODBC-illesztőt. Kövesse [ezt az útmutatót](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=sql-server-2017) az ODBC-illesztőprogram letöltésével és a kapcsolatok karakterlánc-beállításaival.

## <a name="prerequisites"></a>Előfeltételek

Ha olyan SQL Server-adatbázisból szeretne Adatmásolást használni, amely nyilvánosan nem érhető el, be kell állítania egy saját üzemeltetésű integrációs modult. További információ: [saját](create-self-hosted-integration-runtime.md)üzemeltetésű integrációs modul. Az Integration Runtime egy beépített SQL Server adatbázis-illesztőt biztosít. Nem kell manuálisan telepítenie az illesztőprogramokat a SQL Server adatbázisból történő másolásakor.

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük a SQL Server adatbázis-összekötőhöz tartozó Data Factory entitások definiálásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A SQL Server társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A Type tulajdonságot **SQLServer**értékre kell beállítani. | Igen |
| connectionString |Itt adhatja meg az SQL-hitelesítés vagy a Windows-hitelesítés használatával a SQL Server-adatbázishoz való kapcsolódáshoz szükséges **ConnectionString** -információkat. Tekintse át a következő mintákat.<br/>A mező megjelölése **SecureString** -ként, hogy biztonságosan tárolja Azure Data Factoryban. A jelszó Azure Key Vaultban is elhelyezhető. Ha SQL-hitelesítéssel rendelkezik, húzza `password` ki a konfigurációt a kapcsolatok sztringből. További információ: a táblázatot követő JSON-példa és a [hitelesítő adatok tárolása Azure Key Vaultban](store-credentials-in-key-vault.md). |Igen |
| userName |Windows-hitelesítés használata esetén adja meg a felhasználónevet. Ilyen például a **domainname\\username**. |Nem |
| password |Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. A mező megjelölése **SecureString** -ként, hogy biztonságosan tárolja Azure Data Factoryban. Vagy [hivatkozhat a Azure Key Vaultban tárolt titkos kulcsra](store-credentials-in-key-vault.md)is. |Nem |
| connectVia | Ez [](concepts-integration-runtime.md) az integrációs modul az adattárhoz való kapcsolódásra szolgál. Használhat saját üzemeltetésű integrációs modult vagy az Azure Integration runtimet, ha az adattár nyilvánosan elérhető. Ha nincs megadva, a rendszer az alapértelmezett Azure Integration Runtime-t használja. |Nem |

>[!TIP]
>Ha hibát észlelt a "UserErrorFailedToConnectToSqlServer" hibakódmal, és egy olyan üzenet, mint például "az adatbázis munkamenet-korlátja XXX és elérte," adja hozzá `Pooling=false` a kapcsolati karakterláncot, és próbálkozzon újra.

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

**2. példa: SQL-hitelesítés használata jelszóval Azure Key Vault**

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

Szakaszok és adatkészletek definiálását tulajdonságainak teljes listájáért tekintse meg az adatkészletek a cikk. Ez a szakasz a SQL Server adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Az adatok SQL Server adatbázisba való másolásához a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | Az adatkészlet Type tulajdonságát **SqlServerTable**értékre kell állítani. | Igen |
| tableName |Ez a tulajdonság annak a táblának vagy nézetnek a neve, amelyre a társított szolgáltatás a SQL Server adatbázis-példányában hivatkozik. | Nincs forrás, a fogadó Igen |

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

A tevékenységek definiálásához használható csoportok és tulajdonságok teljes listáját a [folyamatok](concepts-pipelines-activities.md) című cikkben találja. Ez a szakasz a SQL Server forrás és a fogadó által támogatott tulajdonságok listáját tartalmazza.

### <a name="sql-server-as-a-source"></a>SQL Server forrásként

Az adatok SQL Serverból való másolásához állítsa a forrás típusát a másolás tevékenység **SqlSource**. A másolási tevékenység forrása szakasz a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának Type tulajdonságát **SqlSource**értékre kell állítani. | Igen |
| sqlReaderQuery |Az egyéni SQL-lekérdezés segítségével olvassa el az adatokat. Például: `select * from MyTable`. |Nem |
| sqlReaderStoredProcedureName |Ez a tulajdonság annak a tárolt eljárásnak a neve, amely beolvassa az adatokat a forrás táblából. Az utolsó SQL-utasítást a tárolt eljárás a SELECT utasítással kell lennie. |Nem |
| storedProcedureParameters |Ezek a paraméterek a tárolt eljáráshoz tartoznak.<br/>Megengedett értékek: neve vagy értéke párokat. A paraméterek neveinek és burkolatának meg kell egyeznie a tárolt eljárás paramétereinek nevével és házával. |Nem |

**Megjegyzés:**

- Ha a **SqlSource** **sqlReaderQuery** van megadva, a másolási tevékenység lefuttatja ezt a lekérdezést a SQL Server forráson az adatkéréshez. Megadhat egy tárolt eljárást is a **sqlReaderStoredProcedureName** és a **storedProcedureParameters** megadásával, ha a tárolt eljárás paraméterekkel rendelkezik.
- Ha nem ad meg **sqlReaderQuery** vagy **sqlReaderStoredProcedureName**, az adatkészlet JSON-fájljának "struktúra" szakaszában meghatározott oszlopok a lekérdezések létrehozásához használatosak. A lekérdezés `select column1, column2 from mytable` a SQL Serveron fut. Ha az adatkészlet definíciója nem rendelkezik "Structure" értékkel, az összes oszlop ki van választva a táblából.

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
> További információ a támogatott írási viselkedésekről, konfigurációkról és ajánlott eljárásokról az [adatok SQL Serverba](#best-practice-for-loading-data-into-sql-server)való betöltéséhez ajánlott eljárások közül.

Az adatSQL Serverba való másoláshoz állítsa a fogadó típust a másolási tevékenység **SqlSink**. A másolási tevékenység fogadója szakasz a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység fogadójának Type tulajdonságát **SqlSink**értékre kell állítani. | Igen |
| writeBatchSize |A kötegekben az SQL-táblába beszúrandósorok száma.<br/>Az engedélyezett értékek a sorok számának egésze. Alapértelmezés szerint a Azure Data Factory dinamikusan meghatározza a megfelelő batch-méretet a sor mérete alapján. |Nem |
| writeBatchTimeout |Ez a tulajdonság határozza meg, hogy a kötegelt beszúrási művelet várakozási ideje befejeződjön, mielőtt időtúllépés történt.<br/>Az engedélyezett értékek a TimeSpan vonatkoznak. Egy példa: "00:30:00" 30 percig. |Nem |
| preCopyScript |Ez a tulajdonság határozza meg a másolási tevékenység futtatásához szükséges SQL-lekérdezést, mielőtt az adatSQL Serverba írna. A rendszer csak egyszer hívja meg a példányt. Ennek a tulajdonságnak a használatával törölheti az előre feltöltött adatkészleteket. |Nem |
| sqlWriterStoredProcedureName | Annak a tárolt eljárásnak a neve, amely meghatározza, hogy a forrásadatok hogyan alkalmazhatók a célként megadott táblába. <br/>Ezt a tárolt eljárást *Batch*-ként kell meghívni. Olyan műveletekhez, amelyek csak egyszer futnak, és nem kell megtenniük a forrásadatokat, például törlés vagy csonkítás, használja `preCopyScript` a tulajdonságot. | Nem |
| storedProcedureTableTypeParameterName |A tárolt eljárásban megadott tábla típusú paraméter neve.  |Nem |
| sqlWriterTableType |A tárolt eljárásban használandó táblanév neve. A másolási tevékenység lehetővé teszi az áthelyezett adatáthelyezést egy ideiglenes táblában, amely ebben a táblázatban szerepel. A tárolt eljárási kód ezután egyesítheti a folyamatban lévő adattal másolt adatfájlokat. |Nem |
| storedProcedureParameters |A tárolt eljárás paraméterei.<br/>Az engedélyezett értékek név és érték párok. Nevek és a kis-és a paraméterek meg kell egyeznie a neveket és a kis-és nagybetűhasználatot, a tárolt eljárás paraméterértékeinek. | Nem |

**1. példa: Adathozzáfűzés**

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

**2. példa: Tárolt eljárás meghívása a másolás során**

További információ az SQL-fogadó [tárolt eljárásának](#invoke-a-stored-procedure-from-a-sql-sink)meghívásáról.

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
                "storedProcedureTableTypeParameterName": "MyTable",
                "sqlWriterTableType": "MyTableType",
                "storedProcedureParameters": {
                    "identifier": { "value": "1", "type": "Int" },
                    "stringData": { "value": "str1" }
                }
            }
        }
    }
]
```

## <a name="best-practice-for-loading-data-into-sql-server"></a>Ajánlott eljárás az adatSQL Serverba való betöltéshez

Amikor az Adatmásolás SQL Serverba történik, különböző írási viselkedésre lehet szükség:

- [Hozzáfűzés](#append-data): A forrásadatok csak új rekordokkal rendelkeznek.
- [Upsert](#upsert-data): A forrásadatok a lapkákkal és a frissítésekkel is rendelkeznek.
- [Felülírás](#overwrite-the-entire-table): Minden alkalommal újra szeretném tölteni a teljes dimenzió táblát.
- [Írás Egyéni logikával](#write-data-with-custom-logic): További feldolgozásra van szükség a végső Beszúrás előtt a céltáblaba.

Tekintse meg a megfelelő részeit, hogy miként konfigurálható a Azure Data Factory és az ajánlott eljárásokban.

### <a name="append-data"></a>Adathozzáfűzés

Az adathozzáfűzés a SQL Server fogadó összekötő alapértelmezett viselkedése. A Azure Data Factory egy tömeges beszúrást végez, hogy hatékonyan írjon a táblába. A forrást és a fogadót ennek megfelelően állíthatja be a másolási tevékenységbe.

### <a name="upsert-data"></a>Adatok beszúrása és frissítése (upsert)

**1. lehetőség:** Ha nagy mennyiségű adattal szeretne másolni, a következő megközelítéssel hajthat végre egy upsert: 

- Először használjon egy [ideiglenes táblázatot](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables) az összes rekord tömeges betöltéséhez a másolási tevékenység használatával. Mivel a rendszer nem naplózza az ideiglenes táblák műveleteit, másodpercek alatt több millió rekordot is betölt.
- Egy tárolt eljárási tevékenység futtatása a Azure Data Factoryban [egyesítési](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) vagy INSERT/Update utasítás alkalmazásához. A temp táblát használja forrásként az összes frissítés vagy Beszúrás egyetlen tranzakcióként való végrehajtásához. Így csökken a kerekítési és a naplózási műveletek száma. A tárolt eljárási tevékenység végén a temp tábla lerövidíthető, hogy készen álljon a következő upsert ciklusra.

Azure Data Factory például létrehozhat egy **másolási tevékenységgel** rendelkező folyamatot egy **tárolt eljárási tevékenységgel**. A korábbi adatokat a forrás tárolójából egy adatbázis ideiglenes táblájába másolta, például: **# #UpsertTempTable**, mint a tábla neve az adatkészletben. Ezután az utóbbi egy tárolt eljárást hív meg, hogy egyesítse a forrás adatait a temp táblából a célként megadott táblába, és törölje a temp táblát.

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

Az adatbázisában Definiáljon egy EGYESÍTÉSi logikával ellátott tárolt eljárást, például az alábbi példát, amely az előző tárolt eljárási tevékenységből mutat. Tegyük fel, hogy a cél a három oszlopból álló **értékesítési** tábla: **ProfileID**, **állapot**és **Kategória**. A upsert a **ProfileID** oszlop alapján végezze el.

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

**2. lehetőség:** Azt is megteheti, hogy [egy tárolt eljárást hív meg a másolási tevékenységen belül](#invoke-a-stored-procedure-from-a-sql-sink). Ez a módszer a forrás tábla minden egyes sorát futtatja ahelyett, hogy a másolási tevékenységben a tömeges beszúrást használja alapértelmezett megközelítésként, ami nem megfelelő a nagyméretű upsert.

### <a name="overwrite-the-entire-table"></a>A teljes táblázat felülírása

A **preCopyScript** tulajdonságot egy másolási tevékenység fogadójában is konfigurálhatja. Ebben az esetben minden futó másolási tevékenységnél először Azure Data Factory futtatja a parancsfájlt. Ezután futtatja a másolatot az adatbeszúráshoz. Ha például felül szeretné írni a teljes táblázatot a legfrissebb adatokkal, adjon meg egy parancsfájlt, hogy először törölje az összes rekordot, mielőtt tömegesen betölti az új adatokat a forrásból.

### <a name="write-data-with-custom-logic"></a>Az adatírás egyéni logikával

Az egyéni logikával történő adatírás lépései hasonlóak az [Upsert](#upsert-data) -adatszakaszban leírt lépésekhez. Ha további feldolgozást kell alkalmaznia a forrásadatok végső beszúrása előtt a céltáblaba, nagy méret esetén a következő két dolog közül választhat: 

- Töltsön be egy ideiglenes táblába, majd hívja meg a tárolt eljárást. 
- Tárolt eljárás meghívása a másolás során.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a>Tárolt eljárás meghívása egy SQL-gyűjtőből

Amikor Adatmásolást végez egy SQL Server adatbázisba, a felhasználó által megadott tárolt eljárást is konfigurálhatja és meghívhatja további paraméterekkel. A tárolt eljárás funkció kihasználja a [tábla értékű paraméterek](https://msdn.microsoft.com/library/bb675163.aspx)előnyeit.

> [!TIP]
> A tárolt eljárás meghívása az adatsort soronként dolgozza fel egy tömeges művelet helyett, amelyet nem ajánlott nagyméretű másolásra használni. További információ az [adatok SQL Serverba való betöltésével kapcsolatos ajánlott eljárásokról](#best-practice-for-loading-data-into-sql-server).

Tárolt eljárás használható, ha a beépített másolási mechanizmusok nem szolgálják ki a célt. Ilyen eset például, ha további feldolgozást szeretne alkalmazni a forrásadatok végső beszúrása előtt a céltáblaba. Néhány további feldolgozási példa az oszlopok egyesítéséhez, további értékek kereséséhez és az adatok több táblába való beszúrásához.

Az alábbi minta azt mutatja be, hogyan használható egy tárolt eljárás egy upsert a SQL Server-adatbázis egyik táblájába. Tegyük fel, hogy a bemeneti adatok és a fogadó **marketing** tábla mindhárom oszlopból áll: **ProfileID**, **állapot**és **Kategória**. A upsert a **ProfileID** oszlop alapján végezze el, és csak a "producta" nevű adott kategóriára alkalmazza.

1. Az adatbázisában adja meg a tábla típusát a **sqlWriterTableType**megegyező névvel. A tábla típusának sémája megegyezik a bemeneti adatok által visszaadott sémával.

    ```sql
    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL，
        [Category] [varchar](256) NOT NULL
    )
    ```

2. Az adatbázisban adja meg a tárolt eljárást ugyanazzal a névvel, mint a **SqlWriterStoredProcedureName**. Kezeli a megadott forrásból származó bemeneti adatokat, és egyesíti a kimeneti táblába. A tárolt eljárásban szereplő tábla típusának neve megegyezik az adatkészletben definiált **Táblanév** .

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

3. A Azure Data Factory a másolási tevékenységben adja meg az **SQL** -fogadó szakaszt a következőképpen:

    ```json
    "sink": {
        "type": "SqlSink",
        "SqlWriterStoredProcedureName": "spOverwriteMarketing",
        "storedProcedureTableTypeParameterName": "Marketing",
        "SqlWriterTableType": "MarketingType",
        "storedProcedureParameters": {
            "category": {
                "value": "ProductA"
            }
        }
    }
    ```

## <a name="data-type-mapping-for-sql-server"></a>SQL Server adattípusának leképezése

A és a rendszerből a SQL Serverba másolt adatokból az alábbi leképezések használatosak SQL Server adattípusokból Azure Data Factory köztes adattípusokra. Ha szeretné megtudni, hogyan képezi le a másolási tevékenység a forrás sémát és az adattípust a fogadóra, tekintse meg a [séma-és adattípus](copy-activity-schema-and-type-mapping.md)-leképezéseket

| SQL Server adattípus | Azure Data Factory időközi adattípus |
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
> A decimális ideiglenes típusra leképezett adattípusok esetében jelenleg Azure Data Factory támogatja a pontosságot akár 28-ra. Ha a 28-nál nagyobb pontosságot igénylő adatmennyiségre van szüksége, érdemes lehet egy SQL-lekérdezésben szereplő sztringre konvertálni.

## <a name="troubleshoot-connection-issues"></a>A kapcsolatok problémáinak elhárítása

1. Konfigurálja a SQL Server-példányt a távoli kapcsolatok fogadására. Indítsa el **SQL Server Management Studio**, kattintson a jobb gombbal a **kiszolgáló**elemre, majd válassza a **Tulajdonságok**lehetőséget. Válassza a listában a **kapcsolatok** lehetőséget, majd jelölje be a **távoli kapcsolatok engedélyezése a kiszolgálón** jelölőnégyzetet.

    ![Távoli kapcsolatok engedélyezése](media/copy-data-to-from-sql-server/AllowRemoteConnections.png)

    A részletes lépésekért lásd: [a távelérési kiszolgáló konfigurációs beállításának konfigurálása](https://msdn.microsoft.com/library/ms191464.aspx).

2. **SQL Server konfigurációkezelő**elindítása. Bontsa ki **SQL Server hálózati konfigurációt** a kívánt példányhoz, és válassza az **MSSQLSERVER protokollokat**. A protokollok a jobb oldali ablaktáblán jelennek meg. A TCP/IP engedélyezéséhez kattintson a jobb gombbal a **TCP/IP** elemre, és válassza az **Engedélyezés**lehetőséget.

    ![TCP/IP engedélyezése](./media/copy-data-to-from-sql-server/EnableTCPProptocol.png)

    További információ és a TCP/IP protokoll engedélyezésének alternatív módjai: [kiszolgálói hálózati protokoll engedélyezése vagy letiltása](https://msdn.microsoft.com/library/ms191294.aspx).

3. Ugyanebben az ablakban kattintson duplán a **TCP/IP** elemre a **TCP/IP tulajdonságok** ablak elindításához.
4. Váltson az **IP-címek** lapra. Görgessen le a **IPAll** szakasz megtekintéséhez. Jegyezze fel a **TCP-portot**. Az alapértelmezett érték a **1433**.
5. Hozzon létre egy szabályt a számítógépen a **Windows tűzfal** számára, hogy engedélyezze a bejövő forgalmat ezen a porton keresztül. 
6. A **kapcsolatok ellenőrzése**: Ha teljes névvel szeretne csatlakozni a SQL Serverhoz, használja a SQL Server Management Studio egy másik gépről. Például: `"<machine>.<domain>.corp.<company>.com,1433"`.

## <a name="next-steps"></a>További lépések
A Azure Data Factory a másolási tevékenység által forrásként és nyelőként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md##supported-data-stores-and-formats).
