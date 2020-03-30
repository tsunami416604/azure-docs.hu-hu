---
title: Adatok másolása az SQL Server kiszolgálóra és az SQL Server kiszolgálóról
description: Megtudhatja, hogyan helyezhetát át az adatokat a helyszíni VAGY egy Azure-beli virtuális gépen lévő SQL Server-adatbázisba és onnan az Azure Data Factory használatával.
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
ms.date: 03/12/2020
ms.openlocfilehash: e7f91d60a0e181690859da64e491c4c0fa1e1a3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80238720"
---
# <a name="copy-data-to-and-from-sql-server-by-using-azure-data-factory"></a>Adatok másolása az SQL Server be- és kifutóról az Azure Data Factory használatával

> [!div class="op_single_selector" title1="Válassza ki az Azure Data Factory által használt verziót:"]
> * [1-es verzió](v1/data-factory-sqlserver-connector.md)
> * [Aktuális verzió](connector-sql-server.md)

Ez a cikk bemutatja, hogyan használhatja a másolási tevékenységet az Azure Data Factory adatok másolásához és sql server-adatbázisba. A [másolási tevékenység áttekintése](copy-activity-overview.md) cikkre épül, amely a másolási tevékenység általános áttekintését mutatja be.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez az SQL Server-összekötő a következő tevékenységek esetén támogatott:

- [Tevékenység másolása](copy-activity-overview.md) [támogatott forrás/fogadó mátrixcal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)

Az SQL Server adatbázisból adatokat másolhat bármely támogatott fogadóadattárba. Vagy bármely támogatott forrásadat-tárolóból adatokat másolhat sql server adatbázisba. A másolási tevékenység által forrásként vagy fogadóként támogatott adattárak listáját a [Támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblában láthatja.

Ez az SQL Server-összekötő különösen a következőket támogatja:

- SQL Server 2016-os, 2014-es, 2012-es, 2008-as R2,2008 és 2005-ös verziói.
- Adatok másolása SQL vagy Windows-hitelesítéssel.
- Forrásként adatok beolvasása SQL-lekérdezéssel vagy tárolt eljárással.
- Fogadóként adatok fűzése a céltáblához, vagy egy tárolt eljárás meghívása egyéni logikával a másolás során.

[Az SQL Server Express LocalDB](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-express-localdb?view=sql-server-2017) nem támogatott.

>[!NOTE]
>Az SQL Server [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017) szolgáltatását ez az összekötő most nem támogatja. A munka megkerüléséhez használhat [egy általános ODBC-összekötőt](connector-odbc.md) és egy SQL Server ODBC illesztőprogramot. Kövesse [ezt az útmutatót](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=sql-server-2017) az ODBC illesztőprogram letöltési és csatlakozási karakterlánc-konfigurációival.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok az SQL Server adatbázis-összekötőre jellemző Data Factory-entitások definiálásához használt tulajdonságok részleteit ismertetik.

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai

Az SQL Server csatolt szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A típustulajdonságot **SqlServer**-re kell állítani. | Igen |
| connectionString (kapcsolati karakterlánc) |Adja **connectionString** meg az SQL Server adatbázishoz SQL-hitelesítés vagy Windows-hitelesítés használatával történő csatlakozáshoz szükséges connectionString-adatokat. Lásd a következő mintákat.<br/>Az Azure Key Vaultban is elhelyezhet egy jelszót. Ha SQL-hitelesítésről van `password` szó, húzza ki a konfigurációt a kapcsolati karakterláncból. További információ: A JSON-példa a táblázat ot és az Áruház hitelesítő adatait az [Azure Key Vaultban.](store-credentials-in-key-vault.md) |Igen |
| userName (Felhasználónév) |Ha Windows-hitelesítést használ, adjon meg egy felhasználónevet. Ilyen például a **\\tartománynév felhasználóneve**. |Nem |
| jelszó |Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. Jelölje meg ezt a mezőt **SecureString** ként, hogy biztonságosan tárolhatja az Azure Data Factoryban. Vagy [hivatkozhat az Azure Key Vaultban tárolt titkos fájlokra.](store-credentials-in-key-vault.md) |Nem |
| connectVia | Ez [az integrációs futásidejű](concepts-integration-runtime.md) az adattárhoz való csatlakozáshoz használható. További információ az [Előfeltételek](#prerequisites) szakaszból. Ha nincs megadva, az alapértelmezett Azure-integrációs futásidejű használatos. |Nem |

>[!TIP]
>Ha a "UserErrorFailedToConnectToSqlServer" hibakóddal és a "Az adatbázis munkamenet-korlátjának XXX és `Pooling=false` elérte" hibaüzenetjelenik meg, adja hozzá a kapcsolati karakterláncot, és próbálkozzon újra.

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

**2. példa: SQL-hitelesítés használata jelszóval az Azure Key Vaultban**

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

Az adatkészletek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját az [adatkészletekről](concepts-datasets-linked-services.md) szóló cikkben olvashatja. Ez a szakasz az SQL Server adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Adatok SQL Server adatbázisból és SQL Server adatbázisba történő másolásához a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | Az adatkészlet típustulajdonságát **SqlServerTable**tulajdonságra kell állítani. | Igen |
| Séma | A séma neve. |Nem a forráshoz, Igen a mosogatóhoz  |
| tábla | A tábla/nézet neve. |Nem a forráshoz, Igen a mosogatóhoz  |
| tableName | A sémával rendelkező tábla/nézet neve. Ez a tulajdonság a visszamenőleges kompatibilitás érdekében támogatott. Az új számítási `schema` `table`feladatokhoz használja és használja. | Nem a forráshoz, Igen a mosogatóhoz |

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

A tevékenységek definiálására használható szakaszok és tulajdonságok teljes listáját a [Folyamatok](concepts-pipelines-activities.md) című cikkben olvashatja. Ez a szakasz az SQL Server forrás és -fogadó által támogatott tulajdonságok listáját tartalmazza.

### <a name="sql-server-as-a-source"></a>SQL Server mint forrás

Ha adatokat szeretne másolni az SQL Server kiszolgálóról, állítsa a forrástípust a másolási tevékenységben **az SqlSource programba.** A másolási tevékenység forrásszakaszában a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenységforrás típustulajdonságát **sqlsource**-ra kell állítani. | Igen |
| sqlReaderQuery |Az adatok olvasásához használja az egyéni SQL-lekérdezést. Például: `select * from MyTable`. |Nem |
| sqlReaderStoredProcedureName |Ez a tulajdonság annak a tárolt eljárásnak a neve, amely a forrástáblából olvassa be az adatokat. Az utolsó SQL utasításnak SELECT utasításnak kell lennie a tárolt eljárásban. |Nem |
| storedProcedureParameters |Ezek a paraméterek a tárolt eljáráshoz tartoznak.<br/>Az engedélyezett értékek név- vagy értékpárok. A paraméterek nevének és burkolatának meg kell egyeznie a tárolt eljárásparamétereinek nevével és burkolatával. |Nem |
| isolationLevel | Megadja az SQL-forrás tranzakciózárolási viselkedését. Az engedélyezett értékek a következők: **ReadCommitted** (alapértelmezett), **ReadUncommitted**, **RepeatableRead**, **Serializable**, **Snapshot**. További részletekért olvassa el ezt a [dokit.](https://docs.microsoft.com/dotnet/api/system.data.isolationlevel) | Nem |

**Megjegyzésre mutat:**

- Ha **az sqlReaderQuery** meg van adva az **SqlSource**programhoz, a másolási tevékenység ezt a lekérdezést futtatja az SQL Server forrásával az adatok lekérni. A tárolt eljárást az **sqlReaderStoredProcedureName** és a **storedProcedureParameters** megadásával is megadhatja, ha a tárolt eljárás paramétereket vesz igénybe.
- Ha nem ad meg **sqlReaderQuery** vagy **sqlReaderStoredProcedureName értéket,** a JSON adatkészlet "struktúra" szakaszában definiált oszlopokat a rendszer lekérdezés létrehozásához használja. A `select column1, column2 from mytable` lekérdezés az SQL Server kiszolgálón fut. Ha az adatkészlet-definíciónem rendelkezik "struktúra" beállítással, a program a táblából az összes oszlopot kijelöli.

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

### <a name="sql-server-as-a-sink"></a>SQL Server mint fogadó

> [!TIP]
> További információ a támogatott írási viselkedésekről, konfigurációkról és gyakorlati tanácsokról az [SQL Server kiszolgálóra történő adatbetöltéssel kapcsolatos gyakorlati tanácsokból.](#best-practice-for-loading-data-into-sql-server)

Ha adatokat szeretne másolni az SQL Server kiszolgálóra, állítsa a fogadó típusát a másolási tevékenységben **az SqlSink**.to A másolási tevékenység fogadója a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenységfogadó típustulajdonságának sqlsink -re kell **állítania.** | Igen |
| writeBatchSize |Az SQL-táblába *kötegenként*beszúrandó sorok száma .<br/>Az engedélyezett értékek egész számok a sorok számához. Alapértelmezés szerint az Azure Data Factory dinamikusan határozza meg a megfelelő kötegméretet a sorméretealapján. |Nem |
| writeBatchTimeout |Ez a tulajdonság megadja a kötegbehelyezési művelet befejezéséhez szükséges várakozási időt, mielőtt az időtúljárna.<br/>Az engedélyezett értékek az időtartományra vannak. Ilyen például a "00:30:00" 30 percig. Ha nincs megadva érték, az időtúlérték alapértelmezés szerint "02:00:00" lesz. |Nem |
| preCopyScript |Ez a tulajdonság egy SQL-lekérdezést ad meg a másolási tevékenység futtatásához, mielőtt adatokat írna az SQL Server kiszolgálóba. A példányfuttatáskor csak egyszer van meghívva. Ezzel a tulajdonsággal megtisztíthatja az előre betöltött adatokat. |Nem |
| sqlWriterDProcedureName | A tárolt eljárás neve, amely meghatározza a forrásadatok céltáblára történő alkalmazását. <br/>Ezt a tárolt eljárást *kötegenként kell meghívni.* A csak egyszer futó és a forrásadatokhoz semmi közük nem rendelkező műveletekhez, például a törléshez vagy a csonkoláshoz használja a `preCopyScript` tulajdonságot. | Nem |
| storedProcedureTableTypeParameterName |A tárolt eljárásban megadott táblatípus paraméterneve.  |Nem |
| sqlWriterTableType típus |A tárolt eljárásban használandó táblatípus neve. A másolási tevékenység elérhetővé teszi az áthelyezett adatokat egy ideiglenes táblában ezzel a táblatípussal. A tárolt eljáráskód ezután egyesítheti a másolt adatokat a meglévő adatokkal. |Nem |
| storedProcedureParameters |A tárolt eljárás paraméterei.<br/>Az engedélyezett értékek a név- és értékpárok. A paraméterek nevének és burkolatának meg kell egyeznie a tárolt eljárásparamétereinek nevével és burkolatával. | Nem |
| tableOption | Itt adható meg, hogy a program automatikusan hozza-e létre a fogadótáblát, ha nem létezik a forrásséma alapján. Az automatikus táblalétrehozása nem támogatott, ha a fogadó a tárolt eljárást adja meg, vagy a programozott másolási tevékenységben van konfigurálva. Az engedélyezett `none` értékek a `autoCreate`következők: (alapértelmezett), . |Nem |

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
                "writeBatchSize": 100000,
                "tableOption": "autoCreate"
            }
        }
    }
]
```

**2. példa: Tárolt eljárás meghívása másolás közben**

További részletek A [tárolt eljárás meghívása SQL-fogadóból](#invoke-a-stored-procedure-from-a-sql-sink).

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

## <a name="best-practice-for-loading-data-into-sql-server"></a>Ajánlott eljárás az adatok SQL Server kiszolgálóra való betöltéséhez

Amikor adatokat másol az SQL Server kiszolgálóra, eltérő írási viselkedésre lehet szükség:

- [Hozzáfűzés:](#append-data)A forrásadatok csak új rekordokat tartalmazó adatokat tartalmazó adatokat.
- [Upsert](#upsert-data): A forrásadatok beszúrásokat és frissítéseket is tartalmaznak.
- [Felülírás:](#overwrite-the-entire-table)Minden alkalommal újra szeretném tölteni a teljes dimenziótáblát.
- [Írjon egyéni logikával:](#write-data-with-custom-logic)További feldolgozásra van szükségem, mielőtt a végső beillesztés a céltáblába kerül.

Tekintse meg a megfelelő szakaszokat, hogyan konfigurálhatja az Azure Data Factory és az ajánlott eljárásokat.

### <a name="append-data"></a>Adatok hozzáfűzése

Az adatok hozzáfűzése az SQL Server-fogadóösszekötő alapértelmezett viselkedése. Az Azure Data Factory egy tömeges beszúrással hatékonyan ír a táblába. Beállíthatja a forrást, és ennek megfelelően elsüllyesztheti a másolási tevékenységet.

### <a name="upsert-data"></a>Adatok beszúrása és frissítése (upsert)

**1. lehetőség:** Ha nagy mennyiségű adatot szeretne másolni, a következő módszerrel végezze el a upsert et: 

- Először egy [ideiglenes tábla](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables) segítségével tömegesen töltse be az összes rekordot a másolási tevékenység használatával. Mivel az ideiglenes táblákon végzett műveletek nem naplózva vannak, több millió rekordot tölthet be másodpercek alatt.
- Futtasson egy tárolt eljárástevékenységet az Azure Data Factoryban [a MERGE](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) vagy INSERT/UPDATE utasítás alkalmazásához. Az ideiglenes tábla forrásként való végrehajtása az összes frissítést vagy beszúrást egyetlen tranzakcióként hajthatja végre. Ily módon csökken az oda- és naplóműveletek száma. A tárolt eljárási tevékenység végén az ideiglenes tábla csonkolható, hogy készen álljon a következő upsert ciklusra.

Például az Azure Data Factoryban létrehozhat egy folyamatot egy **tárolt eljárás tevékenységgel**láncolt **másolási tevékenységgel.** Az előbbi a forrástárolóból származó adatokat egy adatbázis ideiglenes táblájába másolja, például **##UpsertTempTable**, az adatkészlet táblaneveként. Ezután az utóbbi meghívja a tárolt eljárást az ideiglenes táblából származó forrásadatok nak a céltáblába történő egyesítéséhez és az ideiglenes tábla karbantartásához.

![Beszúrás és frissítés](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

Az adatbázisban definiáljon egy tárolt eljárást A MERGE logikával, például a következő példát, amely az előző tárolt eljárási tevékenységre mutat. Tegyük fel, hogy a cél a Következő **State**három oszlopot tartalmazó **Marketing** **Category** **tábla.** Végezze el a upsert alapján **ProfileID** oszlop.

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

**2. lehetőség:** Azt is választhatja, hogy [a másolási tevékenységen belül meghívja a tárolt eljárást.](#invoke-a-stored-procedure-from-a-sql-sink) Ez a megközelítés a forrástábla minden egyes sorát futtatja ahelyett, hogy tömeges beszúrást használna alapértelmezett megközelítésként a másolási tevékenységben, amely nem megfelelő a nagy méretű upsert számára.

### <a name="overwrite-the-entire-table"></a>A teljes tábla felülírása

A **preCopyScript** tulajdonságot egy másolási tevékenységgyűjtőben konfigurálhatja. Ebben az esetben minden egyes futó másolási tevékenység hez az Azure Data Factory először futtatja a parancsfájlt. Ezután futtatja a másolatot az adatok beszúrásához. Ha például felül szeretné írni a teljes táblát a legújabb adatokkal, adjon meg egy parancsfájlt, amely először törli az összes rekordot, mielőtt tömegesen betöltené az új adatokat a forrásból.

### <a name="write-data-with-custom-logic"></a>Adatok írása egyéni logikával

Az adatok írásának lépései egyéni logikával [hasonlóak a Upsert-adatok](#upsert-data) szakaszban leírtakhoz. Ha a forrásadatok végső beillesztése előtt további feldolgozást kell alkalmaznia a céltáblába, a nagy léptékű, a következő két dolgot teheti meg: 

- Töltsön be egy ideiglenes táblába, majd hívja meg a tárolt eljárást. 
- Tárolt eljárás meghívása a másolás során.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a><a name="invoke-a-stored-procedure-from-a-sql-sink"></a>Tárolt eljárás meghívása SQL-fogadóból

Amikor adatokat másol egy SQL Server adatbázisba, a felhasználó által megadott tárolt eljárást további paraméterekkel is konfigurálhatja és meghívhatja. A tárolt eljárás szolgáltatás kihasználja a [tábla értékű paramétereket.](https://msdn.microsoft.com/library/bb675163.aspx)

> [!TIP]
> A tárolt eljárás meghívása az adatsort sorról sorra dolgozza fel, nem pedig tömeges művelet használatával, amelyet nem ajánlunk nagyméretű másoláshoz. További információ az [sql serverbe történő adatbetöltéssel kapcsolatos gyakorlati tanácsokról.](#best-practice-for-loading-data-into-sql-server)

A tárolt eljárás akkor használható, ha a beépített másolási mechanizmusok nem szolgálják a célt. Ilyen például az, ha a forrásadatok végső beillesztése előtt további feldolgozást szeretne alkalmazni a céltáblába. Néhány további feldolgozási példa, ha egyesíteni szeretné az oszlopokat, további értékeket szeretne keresni, és több táblába szeretne adatokat beilleszteni.

Az alábbi minta bemutatja, hogyan lehet egy tárolt eljárás segítségével egy upsert egy tábla az SQL Server adatbázisban. Tegyük fel, hogy a bemeneti adatok és a **marketingfogadó** tábla három oszlopból áll: **ProfileID**, **State**és **Category**. Végezze el a upsert alapján **ProfileID** oszlop, és csak alkalmazza azt egy adott kategóriában az úgynevezett "ProductA".

1. Az adatbázisban adja meg az **sqlWriterTableType**nevű táblatípust. A táblatípus sémája megegyezik a bemeneti adatok által visszaadott sémával.

    ```sql
    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL,
        [Category] [varchar](256) NOT NULL
    )
    ```

2. Az adatbázisban definiálja a tárolt eljárást az **sqlWriterStoredProcedureName**nevű fájlnevével. Kezeli a megadott forrásból származó bemeneti adatokat, és egyesíti a kimeneti táblába. A tárolt eljárásban a táblatípus paraméterneve megegyezik az adatkészletben definiált **tableName** paraméternévvel.

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

3. Az Azure Data Factoryban határozza meg az **SQL-fogadó** szakaszt a másolási tevékenységben az alábbiak szerint:

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

## <a name="data-type-mapping-for-sql-server"></a>Adattípus-hozzárendelés az SQL Server hez

Amikor adatokat másol az SQL Serverről és az SQL Serverbe, a következő leképezéseket használja a rendszer az SQL Server adattípusaiból az Azure Data Factory köztes adattípusaiba. Ha meg szeretné tudni, hogy a másolási tevékenység hogyan rendeli le a forrássémát és az adattípust a fogadóhoz, olvassa el a [Séma- és adattípus-hozzárendelések című témakört.](copy-activity-schema-and-type-mapping.md)

| SQL Server adattípus | Az Azure Data Factory köztes adattípusa |
|:--- |:--- |
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
| sql_variant |Objektum |
| szöveg |Karakterlánc, Karakter |
| time |időtartam |
| időbélyeg |Bájt[] |
| tinyint |Int16 |
| uniqueidentifier |Guid |
| varbinary között |Bájt[] |
| varchar |Karakterlánc, Karakter |
| xml |Xml |

>[!NOTE]
> A decimális köztes típusra leképezett adattípusok esetében jelenleg az Azure Data Factory legfeljebb 28 pontosságot támogat. Ha 28-nál nagyobb pontosságú adatokkal rendelkezik, fontolja meg az SQL-lekérdezéskarakterláncgá való konvertálását.

## <a name="lookup-activity-properties"></a>A keresgaszíntevékenység tulajdonságai

A tulajdonságokrészleteinek megismeréséhez ellenőrizze a [Kereskövetési tevékenységet.](control-flow-lookup-activity.md)

## <a name="getmetadata-activity-properties"></a>GetMetadata tevékenység tulajdonságai

A tulajdonságokrészleteinek megismeréséhez jelölje be a [GetMetadata tevékenység](control-flow-get-metadata-activity.md) 

## <a name="troubleshoot-connection-issues"></a>Kapcsolati problémák hibaelhárítása

1. Állítsa be az SQL Server-példányt a távoli kapcsolatok fogadására. Indítsa el **az SQL Server Management Studio**alkalmazást, kattintson a jobb gombbal a **kiszolgálóra**, és válassza a **Tulajdonságok parancsot.** Válassza a **Kapcsolatok** elemet a listából, majd jelölje be a **Távoli kapcsolatok engedélyezése ehhez a kiszolgálóhoz** jelölőnégyzetet.

    ![Távoli kapcsolatok engedélyezése](media/copy-data-to-from-sql-server/AllowRemoteConnections.png)

    A részletes lépéseket [a Távelérés-kiszolgáló konfigurációs beállításának konfigurálása című témakörben tismerteti.](https://msdn.microsoft.com/library/ms191464.aspx)

2. Indítsa el **az SQL Server Configuration Manager programot.** Bontsa ki a kívánt **példánysql Server hálózati konfigurációját,** és válassza **az MSSQLSERVER protokolljai**lehetőséget. A protokollok a jobb oldali ablaktáblában jelennek meg. TCP/IP engedélyezése a jobb gombbal a **TCP/IP elemre** kattintva, majd az **Engedélyezés parancsra**kattintva.

    ![TCP/IP engedélyezése](./media/copy-data-to-from-sql-server/EnableTCPProptocol.png)

    A TCP/IP protokoll engedélyezésének további információiés alternatív módjai a [kiszolgálóhálózati protokoll engedélyezése vagy letiltása](https://msdn.microsoft.com/library/ms191294.aspx)című témakörben talál.

3. Ugyanebben az ablakban kattintson duplán a **TCP/IP** elemre a **TCP/IP tulajdonságai** ablak elindításához.
4. Váltson az **IP-címek** lapra. Görgessen le az **IPAll** szakasz megtekintéséhez. Írja le a **TCP-portot**. Az alapértelmezett érték **1433**.
5. Hozzon létre egy **szabályt a számítógépen lévő Windows tűzfalhoz,** amely engedélyezi a bejövő forgalmat ezen a porton keresztül. 
6. **Kapcsolat ellenőrzése**: Ha teljesen minősített névvel szeretne csatlakozni az SQL Server kiszolgálóhoz, használja az SQL Server Management Studio alkalmazást egy másik gépről. Például: `"<machine>.<domain>.corp.<company>.com,1433"`.

## <a name="next-steps"></a>További lépések
A forrásként támogatott és fogadóként az Azure Data Factory másolási tevékenysége által támogatott adattárak listáját a [Támogatott adattárak című témakörben tetszhet.](copy-activity-overview.md#supported-data-stores-and-formats)
