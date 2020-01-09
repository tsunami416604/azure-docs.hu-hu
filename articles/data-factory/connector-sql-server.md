---
title: Adatok másolása SQL Server
description: Ismerje meg, hogyan helyezheti át a helyszíni vagy egy Azure-beli virtuális gépen lévő SQL Server-adatbázis adatait Azure Data Factory használatával.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/24/2019
ms.openlocfilehash: 9c9064778f29e9f53f48d8be2f127fe51e936af4
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75444217"
---
# <a name="copy-data-to-and-from-sql-server-by-using-azure-data-factory"></a>Adatok másolása SQL Serverba és onnan a Azure Data Factory használatával

> [!div class="op_single_selector" title1="Válassza ki a használt Azure Data Factory verzióját:"]
> * [1-es verzió](v1/data-factory-sqlserver-connector.md)
> * [Aktuális verzió](connector-sql-server.md)

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok SQL Server-adatbázisba való másolásához. A másolási [tevékenység áttekintő](copy-activity-overview.md) cikkében található, amely a másolási tevékenység általános áttekintését jeleníti meg.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez az SQL Server-összekötő a következő tevékenységek esetén támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)

Az adatok másolása egy SQL Server adatbázisból bármely támogatott fogadó adattárba. Vagy bármely támogatott forrás adattárból átmásolhat egy SQL Server adatbázisba. A másolási tevékenység által forrásként vagy nyelőként támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblázatban tekintheti meg.

Pontosabban, ez az SQL Server-összekötő a következőket támogatja:

- SQL Server verziók 2016, 2014, 2012, 2008 R2, 2008 és 2005.
- Adatok másolása SQL vagy Windows-hitelesítés használatával.
- Forrásként egy SQL-lekérdezés vagy tárolt eljárás használatával beolvashatja az adatokból.
- Fogadóként, az adatsorok hozzáfűzésével vagy egy tárolt eljárás meghívásával egyéni logikával a másolás során.

[SQL Server Express LocalDB](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-express-localdb?view=sql-server-2017) nem támogatott.

>[!NOTE]
>Ez az összekötő jelenleg nem támogatja az SQL Server [Always Encryptedt](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017) . A megoldáshoz használhat egy [általános ODBC-összekötőt](connector-odbc.md) és egy SQL Server ODBC-illesztőt. Kövesse [ezt az útmutatót](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=sql-server-2017) az ODBC-illesztőprogram letöltésével és a kapcsolatok karakterlánc-beállításaival.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Az első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük a SQL Server adatbázis-összekötőhöz tartozó Data Factory entitások definiálásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A SQL Server társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A Type tulajdonságot **SQLServer**értékre kell beállítani. | Igen |
| connectionString |Itt adhatja meg az SQL-hitelesítés vagy a Windows-hitelesítés használatával a SQL Server-adatbázishoz való kapcsolódáshoz szükséges **ConnectionString** -információkat. Tekintse át a következő mintákat.<br/>A jelszó Azure Key Vaultban is elhelyezhető. SQL-hitelesítés esetén a `password` konfigurációt húzza ki a kapcsolatok karakterláncán kívülről. További információ: a táblázatot követő JSON-példa és a [hitelesítő adatok tárolása Azure Key Vaultban](store-credentials-in-key-vault.md). |Igen |
| userName (Felhasználónév) |Windows-hitelesítés használata esetén adja meg a felhasználónevet. Ilyen például a **tartománynév\\Felhasználónév**. |Nem |
| jelszó |Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. A mező megjelölése **SecureString** -ként, hogy biztonságosan tárolja Azure Data Factoryban. Vagy [hivatkozhat a Azure Key Vaultban tárolt titkos kulcsra](store-credentials-in-key-vault.md)is. |Nem |
| Connectvia tulajdonsággal | Ez az [integrációs](concepts-integration-runtime.md) modul az adattárhoz való kapcsolódásra szolgál. További tudnivalók az [Előfeltételek](#prerequisites) szakaszban olvashatók. Ha nincs megadva, a rendszer az alapértelmezett Azure Integration Runtime-t használja. |Nem |

>[!TIP]
>Ha hibát észlelt a "UserErrorFailedToConnectToSqlServer" hibakódmal, és egy olyan üzenet, mint például "az adatbázis munkamenet-korlátja XXX és elérte," adja hozzá `Pooling=false`t a kapcsolati karakterlánchoz, és próbálkozzon újra.

**1. példa: SQL-hitelesítés használata**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
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
            "connectionString": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;",
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
            "connectionString": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=True;",
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

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját az [adatkészletek](concepts-datasets-linked-services.md) című cikkben találja. Ez a szakasz a SQL Server adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Az adatok SQL Server adatbázisba való másolásához a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | Az adatkészlet Type tulajdonságát **SqlServerTable**értékre kell állítani. | Igen |
| séma | A séma neve. |Nem, forrás, igen, fogadó  |
| table | A tábla vagy nézet neve. |Nem, forrás, igen, fogadó  |
| tableName | A tábla/nézet neve a sémával. Ez a tulajdonság visszamenőleges kompatibilitás esetén támogatott. Az új számítási feladatokhoz használja a `schema` és a `table`. | Nem, forrás, igen, fogadó |

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
            "schema": "<schema_name>",
            "table": "<table_name>"
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
| sqlReaderQuery |Az egyéni SQL-lekérdezés használatával olvassa be az adatolvasást. Például: `select * from MyTable`. |Nem |
| sqlReaderStoredProcedureName |Ez a tulajdonság annak a tárolt eljárásnak a neve, amely beolvassa az adatokat a forrás táblából. Az utolsó SQL-utasításnak SELECT utasításnak kell lennie a tárolt eljárásban. |Nem |
| storedProcedureParameters |Ezek a paraméterek a tárolt eljáráshoz tartoznak.<br/>Az engedélyezett értékek név vagy érték párok. A paraméterek neveinek és burkolatának meg kell egyeznie a tárolt eljárás paramétereinek nevével és házával. |Nem |

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

**Példa: tárolt eljárás használata**

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
> További információ a támogatott írási viselkedésekről, konfigurációkról és ajánlott eljárásokról az [adatok SQL Serverba való betöltéséhez ajánlott](#best-practice-for-loading-data-into-sql-server)eljárások közül.

Az adatSQL Serverba való másoláshoz állítsa a fogadó típust a másolási tevékenység **SqlSink**. A másolási tevékenység fogadója szakasz a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység fogadójának Type tulajdonságát **SqlSink**értékre kell állítani. | Igen |
| writeBatchSize |A *kötegekben*az SQL-táblába beszúrandó sorok száma.<br/>Az engedélyezett értékek a sorok számának egésze. Alapértelmezés szerint a Azure Data Factory dinamikusan meghatározza a megfelelő batch-méretet a sor mérete alapján. |Nem |
| writeBatchTimeout |Ez a tulajdonság határozza meg, hogy a kötegelt beszúrási művelet várakozási ideje befejeződjön, mielőtt időtúllépés történt.<br/>Az engedélyezett értékek a TimeSpan vonatkoznak. Egy példa: "00:30:00" 30 percig. Ha nincs megadva érték, az időtúllépés alapértelmezett értéke "02:00:00". |Nem |
| preCopyScript |Ez a tulajdonság határozza meg a másolási tevékenység futtatásához szükséges SQL-lekérdezést, mielőtt az adatSQL Serverba írna. A rendszer csak egyszer hívja meg a példányt. Ennek a tulajdonságnak a használatával törölheti az előre feltöltött adatkészleteket. |Nem |
| sqlWriterStoredProcedureName | Annak a tárolt eljárásnak a neve, amely meghatározza, hogy a forrásadatok hogyan alkalmazhatók a célként megadott táblába. <br/>Ezt a tárolt eljárást batch-ként kell *meghívni*. Olyan műveletekhez, amelyek csak egyszer futnak, és nem szükségesek a forrásadatok, például a törlés vagy a csonkítás, használja a `preCopyScript` tulajdonságot. | Nem |
| storedProcedureTableTypeParameterName |A tárolt eljárásban megadott tábla típusú paraméter neve.  |Nem |
| sqlWriterTableType |A tárolt eljárásban használandó táblanév neve. A másolási tevékenység lehetővé teszi az áthelyezett adatáthelyezést egy ideiglenes táblában, amely ebben a táblázatban szerepel. A tárolt eljárási kód ezután egyesítheti a folyamatban lévő adattal másolt adatfájlokat. |Nem |
| storedProcedureParameters |A tárolt eljárás paraméterei.<br/>Az engedélyezett értékek név és érték párok. A paraméterek nevének és burkolatának meg kell egyeznie a tárolt eljárás paramétereinek nevével és házával. | Nem |
| tableOption | Meghatározza, hogy a rendszer automatikusan létrehozza-e a fogadó táblát, ha az nem létezik a forrásoldali séma alapján. Az automatikus tábla létrehozása nem támogatott, ha a fogadó a tárolt eljárást vagy a szakaszos másolást a másolási tevékenységben konfigurálja. Az engedélyezett értékek a következők: `none` (alapértelmezett), `autoCreate`. |Nem |

**1. példa: az adathozzáfűzés**

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
                "writeBatchSize": 100000,
                "tableOption": "autoCreate"
            }
        }
    }
]
```

**2. példa: tárolt eljárás meghívása másolás közben**

További információ az SQL-fogadó [tárolt eljárásának meghívásáról](#invoke-a-stored-procedure-from-a-sql-sink).

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

- [Hozzáfűzés](#append-data): a forrásadatok csak új rekordokkal rendelkeznek.
- [Upsert](#upsert-data): a saját forrásadatok lapkákat és frissítéseket is tartalmaz.
- [Felülírás](#overwrite-the-entire-table): minden alkalommal újra szeretném tölteni a teljes dimenzió táblát.
- [Írás Egyéni logikával](#write-data-with-custom-logic): további feldolgozásra van szükség a céltábla utolsó beszúrása előtt.

Tekintse meg a megfelelő részeit, hogy miként konfigurálható a Azure Data Factory és az ajánlott eljárásokban.

### <a name="append-data"></a>Adathozzáfűzés

Az adathozzáfűzés a SQL Server fogadó összekötő alapértelmezett viselkedése. A Azure Data Factory egy tömeges beszúrást végez, hogy hatékonyan írjon a táblába. A forrást és a fogadót ennek megfelelően állíthatja be a másolási tevékenységbe.

### <a name="upsert-data"></a>Adatok beszúrása és frissítése (upsert)

**1. lehetőség:** Ha nagy mennyiségű adattal szeretne másolni, a következő megközelítéssel hajthat végre egy upsert: 

- Először használjon egy [ideiglenes táblázatot](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables) az összes rekord tömeges betöltéséhez a másolási tevékenység használatával. Mivel a rendszer nem naplózza az ideiglenes táblák műveleteit, másodpercek alatt több millió rekordot is betölt.
- Egy tárolt eljárási tevékenység futtatása a Azure Data Factoryban [egyesítési](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) vagy INSERT/Update utasítás alkalmazásához. A temp táblát használja forrásként az összes frissítés vagy Beszúrás egyetlen tranzakcióként való végrehajtásához. Így csökken a kerekítési és a naplózási műveletek száma. A tárolt eljárási tevékenység végén a temp tábla lerövidíthető, hogy készen álljon a következő upsert ciklusra.

Azure Data Factory például létrehozhat egy **másolási tevékenységgel** rendelkező **folyamatot egy tárolt eljárási tevékenységgel**. A korábbi adatokat a forrás tárolójából egy adatbázis ideiglenes táblájába másolta, például: **# #UpsertTempTable**, mint a tábla neve az adatkészletben. Ezután az utóbbi egy tárolt eljárást hív meg, hogy egyesítse a forrás adatait a temp táblából a célként megadott táblába, és törölje a temp táblát.

![Beszúrás és frissítés](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

Az adatbázisában Definiáljon egy EGYESÍTÉSi logikával ellátott tárolt eljárást, például az alábbi példát, amely az előző tárolt eljárási tevékenységből mutat. Tegyük fel, hogy a cél a **marketing** tábla három oszloppal: **ProfileID**, **State**és **category**. A upsert a **ProfileID** oszlop alapján végezze el.

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

Az alábbi minta azt mutatja be, hogyan használható egy tárolt eljárás egy upsert a SQL Server-adatbázis egyik táblájába. Tegyük fel, hogy a bemeneti adatok és a fogadó **marketing** tábla mindhárom oszlopot tartalmaz: **ProfileID**, **State**és **category**. A upsert a **ProfileID** oszlop alapján végezze el, és csak a "producta" nevű adott kategóriára alkalmazza.

1. Az adatbázisában adja meg a tábla típusát a **sqlWriterTableType**megegyező névvel. A tábla típusának sémája megegyezik a bemeneti adatok által visszaadott sémával.

    ```sql
    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL，
        [Category] [varchar](256) NOT NULL
    )
    ```

2. Az adatbázisban adja meg a tárolt eljárást ugyanazzal a névvel, mint a **sqlWriterStoredProcedureName**. Kezeli a megadott forrásból származó bemeneti adatokat, és egyesíti a kimeneti táblába. A tárolt eljárásban szereplő tábla típusának neve megegyezik az adatkészletben definiált **Táblanév** .

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
        "sqlWriterStoredProcedureName": "spOverwriteMarketing",
        "storedProcedureTableTypeParameterName": "Marketing",
        "sqlWriterTableType": "MarketingType",
        "storedProcedureParameters": {
            "category": {
                "value": "ProductA"
            }
        }
    }
    ```

## <a name="data-type-mapping-for-sql-server"></a>SQL Server adattípusának leképezése

A és a rendszerből a SQL Serverba másolt adatokból az alábbi leképezések használatosak SQL Server adattípusokból Azure Data Factory köztes adattípusokra. Ha szeretné megtudni, hogyan képezi le a másolási tevékenység a forrás sémát és az adattípust a fogadóra, tekintse meg a [séma-és adattípus-leképezéseket](copy-activity-schema-and-type-mapping.md)

| SQL Server adattípus | Azure Data Factory időközi adattípus |
|:--- |:--- |
| bigint |Int64 |
| binary |Bájt [] |
| bit |Logikai |
| char |Karakterlánc, char [] |
| dátum |Dátum és idő |
| Dátum/idő |Dátum és idő |
| datetime2 |Dátum és idő |
| DateTimeOffset |DateTimeOffset |
| Decimális |Decimális |
| FILESTREAM attribútum (varbinary (max)) |Bájt [] |
| Lebegőpontos szám |Double |
| image |Bájt [] |
| int |Int32 |
| pénzt |Decimális |
| NCHAR |Karakterlánc, char [] |
| ntext |Karakterlánc, char [] |
| numerikus |Decimális |
| nvarchar |Karakterlánc, char [] |
| real |Önálló |
| ROWVERSION |Bájt [] |
| idő adattípusúra |Dátum és idő |
| smallint |Int16 |
| túlcsordulási |Decimális |
| sql_variant |Objektum |
| szöveg |Karakterlánc, char [] |
| time |időtartam |
| időbélyeg |Bájt [] |
| tinyint |Int16 |
| uniqueidentifier |GUID |
| varbinary |Bájt [] |
| varchar |Karakterlánc, char [] |
| xml |XML |

>[!NOTE]
> A decimális ideiglenes típusra leképezett adattípusok esetében jelenleg Azure Data Factory támogatja a pontosságot akár 28-ra. Ha a 28-nál nagyobb pontosságot igénylő adatmennyiségre van szüksége, érdemes lehet egy SQL-lekérdezésben szereplő sztringre konvertálni.

## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [keresési tevékenységet](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>GetMetadata tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [GetMetaData tevékenységet](control-flow-get-metadata-activity.md) 

## <a name="troubleshoot-connection-issues"></a>Kapcsolati problémák hibaelhárítása

1. Konfigurálja a SQL Server-példányt a távoli kapcsolatok fogadására. Indítsa el **SQL Server Management Studio**, kattintson a jobb gombbal a **kiszolgáló**elemre, majd válassza a **Tulajdonságok**lehetőséget. Válassza a listában a **kapcsolatok** lehetőséget, majd jelölje be a **távoli kapcsolatok engedélyezése a kiszolgálón** jelölőnégyzetet.

    ![Távoli kapcsolatok engedélyezése](media/copy-data-to-from-sql-server/AllowRemoteConnections.png)

    A részletes lépésekért lásd: [a távelérési kiszolgáló konfigurációs beállításának konfigurálása](https://msdn.microsoft.com/library/ms191464.aspx).

2. **SQL Server konfigurációkezelő**elindítása. Bontsa ki **SQL Server hálózati konfigurációt** a kívánt példányhoz, és válassza az **MSSQLSERVER protokollokat**. A protokollok a jobb oldali ablaktáblán jelennek meg. A TCP/IP engedélyezéséhez kattintson a jobb gombbal a **TCP/IP** elemre, és válassza az **Engedélyezés**lehetőséget.

    ![TCP/IP engedélyezése](./media/copy-data-to-from-sql-server/EnableTCPProptocol.png)

    További információ és a TCP/IP protokoll engedélyezésének alternatív módjai: [kiszolgálói hálózati protokoll engedélyezése vagy letiltása](https://msdn.microsoft.com/library/ms191294.aspx).

3. Ugyanebben az ablakban kattintson duplán a **TCP/IP** elemre a **TCP/IP tulajdonságok** ablak elindításához.
4. Váltson az **IP-címek** lapra. görgessen lefelé, és tekintse meg a **IPAll** szakaszt. Jegyezze fel a **TCP-portot**. Az alapértelmezett érték a **1433**.
5. Hozzon létre egy szabályt a számítógépen a **Windows tűzfal** számára, hogy engedélyezze a bejövő forgalmat ezen a porton keresztül. 
6. **Kapcsolat ellenőrzése**: Ha SQL Server szeretne csatlakozni egy teljes névvel, használja a SQL Server Management Studio egy másik gépről. Például: `"<machine>.<domain>.corp.<company>.com,1433"`.

## <a name="next-steps"></a>Következő lépések
A Azure Data Factory a másolási tevékenység által forrásként és nyelőként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md##supported-data-stores-and-formats).
