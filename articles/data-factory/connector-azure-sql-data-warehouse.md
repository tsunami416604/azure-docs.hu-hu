---
title: Adatok másolása és átalakítása az Azure Synapse Analytics szolgáltatásban
description: Megtudhatja, hogyan másolhat adatokat az Azure Synapse Analytics szolgáltatásba és onnan, és hogyan alakíthatja át az adatokat az Azure Synapse Analytics szolgáltatásban a Data Factory használatával.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/25/2020
ms.openlocfilehash: 950bbc17af920f104f31af4d324f5546ff29217e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80257954"
---
# <a name="copy-and-transform-data-in-azure-synapse-analytics-formerly-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Adatok másolása és átalakítása az Azure Synapse Analytics (korábbi években Azure SQL Data Warehouse) szolgáltatásban az Azure Data Factory használatával 

> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
> * [1. verzió](v1/data-factory-azure-sql-data-warehouse-connector.md)
> * [Aktuális verzió](connector-azure-sql-data-warehouse.md)

Ez a cikk bemutatja, hogyan használhatja a másolási tevékenység et az Azure Data Factory adatok másolásához és az Azure Synapse Analytics, és az adatfolyam segítségével az azure Data Lake Storage Gen2 adatok átalakítása. Az Azure Data Factory ról a [bevezető cikkben](introduction.md)olvashat.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez az Azure Synapse Analytics-összekötő a következő tevékenységek esetén támogatott:

- [Tevékenység másolása](copy-activity-overview.md) [támogatott forrás/fogadó mátrix](copy-activity-overview.md) táblával
- [Adatfolyam leképezése](concepts-data-flow-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)

A másolási tevékenység, ez az Azure Synapse Analytics-összekötő támogatja ezeket a funkciókat:

- Adatok másolása SQL-hitelesítés és az Azure Active Directory (Azure AD) alkalmazástoken-hitelesítés használatával egyszerű szolgáltatásvagy felügyelt identitások Azure-erőforrások használatával.
- Forrásként sql-lekérdezéssel vagy tárolt eljárással olvassa be az adatokat.
- Fogadóként töltse be az adatokat [a PolyBase](#use-polybase-to-load-data-into-azure-sql-data-warehouse) vagy [copy utasítás](#use-copy-statement) (előzetes verzió) vagy a tömeges beszúrás használatával. A jobb másolási teljesítmény érdekében a PolyBase vagy copy utasítást (előzetes verzió) javasoljuk.

> [!IMPORTANT]
> Ha az adatokat az Azure Data Factory Integration Runtime használatával másolja, konfiguráljon egy [Azure SQL-kiszolgálótűzfalat,](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) hogy az Azure-szolgáltatások hozzáférhessenek a kiszolgálóhoz.
> Ha az adatok másolása saját üzemeltetésű integrációs futásidő használatával, konfigurálja az Azure SQL server tűzfal, hogy a megfelelő IP-tartományban. Ez a tartomány tartalmazza a gép IP-címét, amely az Azure Synapse Analytics-hez való csatlakozáshoz használatos.

## <a name="get-started"></a>Bevezetés

> [!TIP]
> A legjobb teljesítmény elérése érdekében a PolyBase segítségével töltse be az adatokat az Azure Synapse Analytics szolgáltatásba. A [PolyBase használata adatok betöltéséhez az Azure Synapse Analytics](#use-polybase-to-load-data-into-azure-sql-data-warehouse) szakasz részletesen tartalmazza. A használati eset forgatókönyve: [1 TB betöltése az Azure Synapse Analytics betöltése 15 perc alatt az Azure Data Factory segítségével című témakörben.](load-azure-sql-data-warehouse.md)

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok az Azure Synapse Analytics-összekötőkre jellemző Data Factory-entitásokat definiáló tulajdonságok részleteit ismertetik.

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai

Az alábbi tulajdonságok támogatottak egy Azure Synapse Analytics csatolt szolgáltatás:

| Tulajdonság            | Leírás                                                  | Kötelező                                                     |
| :------------------ | :----------------------------------------------------------- | :----------------------------------------------------------- |
| type                | A típustulajdonságot **AzureSqlDW-re**kell állítani.             | Igen                                                          |
| connectionString (kapcsolati karakterlánc)    | Adja meg az Azure Synapse Analytics-példányhoz **connectionString** való csatlakozáshoz szükséges információkat. <br/>Jelölje meg ezt a mezőt SecureStringként, hogy biztonságosan tárolhatja a Data Factory-ban. Jelszó/egyszerű szolgáltatáskulcs az Azure Key Vaultban is, és ha `password` SQL-hitelesítés, húzza ki a konfigurációt a kapcsolati karakterláncból. Tekintse meg a JSON-példát a táblázat alatt, és [az Áruház hitelesítő adatait az Azure Key Vault cikkében](store-credentials-in-key-vault.md) további részletekkel. | Igen                                                          |
| servicePrincipalId  | Adja meg az alkalmazás ügyfélazonosítóját.                         | Igen, ha az Azure AD-hitelesítést egy egyszerű szolgáltatással használja. |
| servicePrincipalKey | Adja meg az alkalmazás kulcsát. Jelölje meg ezt a mezőt SecureStringként a Data Factory biztonságos tárolásához, vagy [hivatkozzon az Azure Key Vaultban tárolt titkos fájlokra.](store-credentials-in-key-vault.md) | Igen, ha az Azure AD-hitelesítést egy egyszerű szolgáltatással használja. |
| Bérlő              | Adja meg a bérlői adatokat (tartománynév vagy bérlőazonosító), amely alatt az alkalmazás található. Az egér relai vonala az Azure Portal jobb felső sarkában lebeg. | Igen, ha az Azure AD-hitelesítést egy egyszerű szolgáltatással használja. |
| connectVia          | Az adattárhoz való csatlakozáshoz használandó [integrációs futásidő.](concepts-integration-runtime.md) Használhatja az Azure-integrációs runtime vagy egy saját üzemeltetésű integrációs runtime (ha az adattár található egy magánhálózat). Ha nincs megadva, az alapértelmezett Azure-integrációs runtime-ot használja. | Nem                                                           |

A különböző hitelesítési típusok esetében tekintse meg az előfeltételekről és jsonmintákról szóló alábbi szakaszokat:

- [SQL-hitelesítés](#sql-authentication)
- Azure AD alkalmazástoken-hitelesítés: [egyszerű szolgáltatás](#service-principal-authentication)
- Azure AD-alkalmazástoken-hitelesítés: [Felügyelt identitások az Azure-erőforrásokhoz](#managed-identity)

>[!TIP]
>Ha a "UserErrorFailedToConnectToSqlServer" hibaüzenettel és a "Az adatbázis munkamenet-korlátjának XXX és `Pooling=false` elérte" hibaüzenetet kapja, adja hozzá a kapcsolati karakterláncot, majd próbálkozzon újra.

### <a name="sql-authentication"></a>SQL-hitelesítés

#### <a name="linked-service-example-that-uses-sql-authentication"></a>SQL-hitelesítést használó csatolt szolgáltatáspélda

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Jelszó az Azure Key Vaultban:**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="service-principal-authentication"></a>Egyszerű szolgáltatásnév hitelesítése

Egyszerű szolgáltatásalapú Azure AD-alkalmazásjogkivonat-hitelesítés használatához hajtsa végre az alábbi lépéseket:

1. **[Hozzon létre egy Azure Active Directory-alkalmazást](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)** az Azure Portalon. Jegyezze fel az alkalmazás nevét és a csatolt szolgáltatást meghatározó következő értékeket:

    - Alkalmazásazonosító
    - Alkalmazáskulcs
    - Bérlőazonosító

2. **[Azure Active Directory-rendszergazda kiépítése](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** az Azure SQL-kiszolgálóhoz az Azure Portalon, ha még nem tette meg. Az Azure AD-rendszergazda lehet egy Azure AD-felhasználó vagy az Azure AD-csoport. Ha a felügyelt identitással rendelkező csoportot rendszergazdai szerepkörrel adja meg, hagyja ki a 3. A rendszergazda teljes hozzáféréssel rendelkezik az adatbázishoz.

3. **[A szolgáltatásnévhez hozzon létre egyben foglalt adatbázis-felhasználókat.](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)** Csatlakozzon az adattárházhoz, vagy amelyhez adatokat szeretne másolni az ssms-hez hasonló eszközök használatával, egy Azure AD-identitással, amely legalább ALTER BÁRMELY FELHASZNÁLÓ engedéllyel rendelkezik. Futtassa a következő T-SQL-t:
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. **Adja meg a szolgáltatás névvel szükséges engedélyeket,** ahogy általában az SQL-felhasználók vagy mások. Futtassa a következő kódot, vagy tekintse meg a további lehetőségeket [itt](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017). Ha a PolyBase segítségével szeretné betölteni az adatokat, ismerje meg a [szükséges adatbázis-engedélyt.](#required-database-permission)

    ```sql
    EXEC sp_addrolemember db_owner, [your application name];
    ```

5. **Konfiguráljon egy Azure Synapse Analytics-kapcsolt szolgáltatást** az Azure Data Factoryban.


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Csatolt szolgáltatás példa, amely egyszerű szolgáltatáshitelesítést használ

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a>Felügyelt identitások az Azure-erőforrások hitelesítéséhez

Az adat-előállító az [Azure-erőforrások felügyelt identitásához](data-factory-service-identity.md) társítható, amely az adott gyárat képviseli. Ezt a felügyelt identitást használhatja az Azure Synapse Analytics-hitelesítéshez. A kijelölt gyár i. adatokat érhet el és másolhat az adattárházból ezzel az identitással.

A felügyelt identitáshitelesítés használatához hajtsa végre az alábbi lépéseket:

1. **[Azure Active Directory-rendszergazda kiépítése](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** az Azure SQL-kiszolgálóhoz az Azure Portalon, ha még nem tette meg. Az Azure AD-rendszergazda lehet egy Azure AD-felhasználó vagy az Azure AD-csoport. Ha a felügyelt identitással rendelkező csoportot rendszergazdai szerepkörrel adja meg, hagyja ki a 3. A rendszergazda teljes hozzáféréssel rendelkezik az adatbázishoz.

2. A data factory felügyelt identitáshoz **[hozzon létre egytartalmazott adatbázis-felhasználókat.](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)** Csatlakozzon az adattárházhoz, vagy amelyhez adatokat szeretne másolni az ssms-hez hasonló eszközök használatával, egy Azure AD-identitással, amely legalább ALTER BÁRMELY FELHASZNÁLÓ engedéllyel rendelkezik. Futtassa a következő T-SQL.Run the following T-SQL. 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. **Adja meg a Data Factory felügyelt identitás szükséges engedélyeket,** ahogy általában az SQL-felhasználók és mások. Futtassa a következő kódot, vagy tekintse meg a további lehetőségeket [itt](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017). Ha a PolyBase segítségével szeretné betölteni az adatokat, ismerje meg a [szükséges adatbázis-engedélyt.](#required-database-permission)

    ```sql
    EXEC sp_addrolemember db_owner, [your Data Factory name];
    ```

5. **Konfiguráljon egy Azure Synapse Analytics-kapcsolt szolgáltatást** az Azure Data Factoryban.

**Példa:**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját az [Adatkészletek](concepts-datasets-linked-services.md) című cikkben olvashatja. 

Az Azure Synapse Analytics adatkészleta következő tulajdonságokat támogatja:

| Tulajdonság  | Leírás                                                  | Kötelező                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| type      | Az adatkészlet **típustulajdonságát** **Az AzureSqlDWTable**beállításra kell állítani. | Igen                         |
| Séma | A séma neve. |Nem a forráshoz, Igen a mosogatóhoz  |
| tábla | A tábla/nézet neve. |Nem a forráshoz, Igen a mosogatóhoz  |
| tableName | A sémával rendelkező tábla/nézet neve. Ez a tulajdonság a visszamenőleges kompatibilitás érdekében támogatott. Az új számítási `schema` `table`feladatokhoz használja és használja. | Nem a forráshoz, Igen a mosogatóhoz |

#### <a name="dataset-properties-example"></a>Példa adatkészlet tulajdonságaira

```json
{
    "name": "AzureSQLDWDataset",
    "properties":
    {
        "type": "AzureSqlDWTable",
        "linkedServiceName": {
            "referenceName": "<Azure Synapse Analytics linked service name>",
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

## <a name="copy-activity-properties"></a>Tevékenység tulajdonságainak másolása

A tevékenységek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját a [Folyamatok](concepts-pipelines-activities.md) című cikkben olvashat. Ez a szakasz az Azure Synapse Analytics-forrás és -fogadó által támogatott tulajdonságok listáját tartalmazza.

### <a name="azure-synapse-analytics-as-the-source"></a>Az Azure Synapse Analytics mint forrás

Az Azure Synapse Analytics adatainak másolásához állítsa be a **típustulajdonságot** a Másolási tevékenység forrásában az **SqlDWSource mezőre.** A következő tulajdonságokat támogatja a Másolási tevékenység **forrásszakasz:**

| Tulajdonság                     | Leírás                                                  | Kötelező |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| type                         | A Másolási tevékenység **forrástípus** tulajdonságát **SqlDWSource**-re kell állítani. | Igen      |
| sqlReaderQuery               | Az adatok olvasásához használja az egyéni SQL-lekérdezést. Példa: `select * from MyTable`. | Nem       |
| sqlReaderStoredProcedureName | Annak a tárolt eljárásnak a neve, amely adatokat olvas be a forrástáblából. Az utolsó SQL utasításnak SELECT utasításnak kell lennie a tárolt eljárásban. | Nem       |
| storedProcedureParameters    | A tárolt eljárás paraméterei.<br/>Az engedélyezett értékek név- vagy értékpárok. A paraméterek nevének és burkolatának meg kell egyeznie a tárolt eljárásparamétereinek nevével és burkolatával. | Nem       |
| isolationLevel | Megadja az SQL-forrás tranzakciózárolási viselkedését. Az engedélyezett értékek a következők: **ReadCommitted** (alapértelmezett), **ReadUncommitted**, **RepeatableRead**, **Serializable**, **Snapshot**. További részletekért olvassa el ezt a [dokit.](https://docs.microsoft.com/dotnet/api/system.data.isolationlevel) | Nem |

**Példa: SQL-lekérdezés használata**

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL DW input dataset name>",
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
                "type": "SqlDWSource",
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
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL DW input dataset name>",
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
                "type": "SqlDWSource",
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

**Tárolt mintaeljárás:**

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

### <a name="azure-synapse-analytics-as-sink"></a><a name="azure-sql-data-warehouse-as-sink"></a>Az Azure Synapse Analytics mosogatóként

Az Azure Data Factory három módon támogatja az adatok SQL Data Warehouse betöltését.

![SQL DW fogadó másolási beállításai](./media/connector-azure-sql-data-warehouse/sql-dw-sink-copy-options.png)

- [A PolyBase használata](#use-polybase-to-load-data-into-azure-sql-data-warehouse) 
- [COPY utasítás használata (előzetes verzió)](#use-copy-statement)
- Tömeges lapka használata

Az adatok betöltésének leggyorsabb és leginkább skálázható módja a [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) vagy a [COPY utasítás](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (előzetes verzió).

Ha adatokat szeretne másolni az Azure SQL Data Warehouse-ba, állítsa be a fogadó típusát a Másolási tevékenység ben SqlDWSink .To copy data to Azure SQL Data Warehouse, set the sink type in Copy Activity to **SqlDWSink**. A következő tulajdonságokat támogatja a Másolási tevékenység **fogadó** ja:

| Tulajdonság          | Leírás                                                  | Kötelező                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| type              | A Másolási tevékenység fogadójának **típustulajdonságát** **SqlDWSink (SqlDWSink**) tulajdonságra kell állítani. | Igen                                           |
| allowPolyBase     | Azt jelzi, hogy a PolyBase használatával kell-e adatokat betölteni az SQL Data Warehouse-ba. `allowCopyCommand`és `allowPolyBase` nem lehet mindkettő igaz. <br/><br/>A korlátozásokért és részletekért olvassa el [a PolyBase használata az Adatok azure SQL Data Warehouse szakaszba való betöltéséhez](#use-polybase-to-load-data-into-azure-sql-data-warehouse) című témakört.<br/><br/>Az engedélyezett **értékek: Igaz** és **Hamis** (alapértelmezett). | Nem.<br/>Alkalmazza a PolyBase használatakor.     |
| poliBaseSettings  | Tulajdonságok csoportja, amely akkor adható `allowPolybase` meg, ha a tulajdonság **értéke igaz.** | Nem.<br/>Alkalmazza a PolyBase használatakor. |
| allowCopyCommand parancs | Azt jelzi, hogy a [COPY utasítás](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (előzetes verzió) használatával kell-e adatokat betölteni az SQL Data Warehouse-ba. `allowCopyCommand`és `allowPolyBase` nem lehet mindkettő igaz. <br/><br/>A megkötések és részletek [értésekért lásd: COPY utasítás használata az Azure SQL Data Warehouse szakaszba](#use-copy-statement) való betöltéséhez.<br/><br/>Az engedélyezett **értékek: Igaz** és **Hamis** (alapértelmezett). | Nem.<br>A COPY használatakor alkalmazza a programot. |
| copyCommandSettings | Tulajdonságok olyan csoportja, amely akkor `allowCopyCommand` adható meg, ha a tulajdonság ÉRTÉKE TRUE. | Nem.<br/>A COPY használatakor alkalmazza a programot. |
| writeBatchSize    | Az SQL-táblába **kötegenként**beszúrandó sorok száma .<br/><br/>Az engedélyezett érték **egész szám** (sorok száma). Alapértelmezés szerint a Data Factory dinamikusan határozza meg a megfelelő kötegméretet a sorméret alapján. | Nem.<br/>Alkalmazza tömeges beszúrás esetén.     |
| writeBatchTimeout | Várjon időt a kötegbehelyezési művelet befejezésére, mielőtt az időtúljárna.<br/><br/>Az engedélyezett érték az **időtartomány**. Példa: "00:30:00" (30 perc). | Nem.<br/>Alkalmazza tömeges beszúrás esetén.        |
| preCopyScript     | Adja meg az SQL-lekérdezést a másolási tevékenység futtatásához, mielőtt adatokat írna az Azure SQL Data Warehouse-ba minden futtatáskor. Ezzel a tulajdonsággal megtisztítja az előre betöltött adatokat. | Nem                                            |
| tableOption | Itt adható meg, hogy a program automatikusan hozza-e létre a fogadótáblát, ha nem létezik a forrásséma alapján. Az automatikus táblalétrehozása nem támogatott, ha a programozta példánymásolási tevékenységben van konfigurálva. Az engedélyezett `none` értékek a `autoCreate`következők: (alapértelmezett), . |Nem |
| disableMetricsCollection | A Data Factory olyan metrikákat gyűjt, mint például az SQL Data Warehouse DWUs a másolási teljesítmény optimalizálásához és a javaslatokhoz. Ha ez a viselkedés aggasztja, adja meg `true` a kikapcsolására. | Nem (az `false`alapértelmezett ) |

#### <a name="sql-data-warehouse-sink-example"></a>Példa az SQL Data Warehouse fogadójára

```json
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true,
    "polyBaseSettings":
    {
        "rejectType": "percentage",
        "rejectValue": 10.0,
        "rejectSampleValue": 100,
        "useTypeDefault": true
    }
}
```

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>Adatok betöltése az Azure SQL Data Warehouse-ba a PolyBase használatával

A [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) használatával hatékony anamnézis nagy mennyiségű adatot tölthet be az Azure Synapse Analytics nagy átviteli sebességgel. Az átviteli érték nagy nyereséget fog látni az alapértelmezett BULKINSERT mechanizmus helyett a PolyBase használatával. A használati eset forgatókönyve: [1 TB betöltése az Azure Synapse Analytics szolgáltatásba.](v1/data-factory-load-sql-data-warehouse.md)

* Ha a forrásadatok **az Azure Blob, az Azure Data Lake Storage Gen1 vagy**az Azure Data Lake Storage Gen2 , és a formátum **PolyBase kompatibilis,** másolási tevékenység segítségével közvetlenül meghívja PolyBase, hogy az Azure SQL Data Warehouse lekéri az adatokat a forrásból. További információt a Közvetlen másolás a PolyBase használatával című **[témakörben talál.](#direct-copy-by-using-polybase)**
* Ha a forrásadat-tárolót és -formátumot a PolyBase eredetileg nem támogatja, használja a **[Szakaszos másolást a PolyBase](#staged-copy-by-using-polybase)** szolgáltatás használatával. A szakaszos másolási funkció is jobb átviteli. Automatikusan átalakítja az adatokat PolyBase-kompatibilis formátumba, tárolja az adatokat az Azure Blob storage-ban., majd felhívja a PolyBase adatok betöltése az SQL Data Warehouse.It automatically converts the data into PolyBase-kompatibilis formátumban, tárolja az adatokat az Azure Blob storage., majd felhívja polyBase adatok betöltése az SQL Data Warehouse.

>[!TIP]
>További információ a [PolyBase használatának gyakorlati tanácsairól.](#best-practices-for-using-polybase)

A `polyBaseSettings` másolási tevékenység a következő PolyBase-beállításokat támogatja:

| Tulajdonság          | Leírás                                                  | Kötelező                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| rejectValue       | Megadja a lekérdezés sikertelensedése előtt elutasítható sorok számát vagy százalékos arányát.<br/><br/>További információ a PolyBase elutasítási beállításairól a [CREATE EXTERNAL TABLE (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx)argumentumok szakaszában olvashat. <br/><br/>Az engedélyezett értékek: 0 (alapértelmezett), 1, 2 stb. | Nem                                            |
| rejectType        | Itt adható **meg,** hogy a rejectValue beállítás literális érték vagy százalék.<br/><br/>Az engedélyezett értékek **érték** (alapértelmezett) és **Százalék**. | Nem                                            |
| rejectSampleValue | Meghatározza, hogy a PolyBase hány sort kell beolvasnia, mielőtt a PolyBase újraszámítja az elutasított sorok százalékos arányát.<br/><br/>Megengedett értékek: 1, 2 stb. | Igen, ha a **rejectType** **százalék .** |
| useTypeDefault    | Itt adható meg, hogyan kezelje a tagolt szövegfájlokban hiányzó értékeket, amikor a PolyBase adatokat olvas le a szövegfájlból.<br/><br/>Erről a tulajdonságról a [CREATE EXTERNAL FILE FORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx)argumentumok szakaszában olvashat bővebben.<br/><br/>Az engedélyezett **értékek: Igaz** és **Hamis** (alapértelmezett).<br><br> | Nem                                            |

### <a name="direct-copy-by-using-polybase"></a>Közvetlen másolás a PolyBase használatával

Az SQL Data Warehouse PolyBase közvetlenül támogatja az Azure Blobot, az Azure Data Lake Storage Gen1-et és az Azure Data Lake Storage Gen2-t. Ha a forrásadatok megfelelnek az ebben a szakaszban leírt feltételeknek, a PolyBase segítségével másolja közvetlenül a forrás-adattárból az Azure Synapse Analytics. Ellenkező esetben használja [a Szakaszos másolást a PolyBase használatával.](#staged-copy-by-using-polybase)

> [!TIP]
> Az adatok SQL Data Warehouse-ba történő hatékony másolásához az Azure Data Factory további információival [még egyszerűbbé és kényelmesebbé teszi az adatokból származó információk feltárását a Data Lake Store SQL Data Warehouse használatával történő használata során.](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/)

Ha a követelmények nem teljesülnek, az Azure Data Factory ellenőrzi a beállításokat, és automatikusan visszaáll az adatáthelyezés BULKINSERT mechanizmusára.

1. A **forráskapcsolt szolgáltatás** a következő típusokkal és hitelesítési módszerekkel működik:

    | Támogatott forrásadattár típusa                             | Támogatott forráshitelesítéstípusa                        |
    | :----------------------------------------------------------- | :---------------------------------------------------------- |
    | [Azure-blob](connector-azure-blob-storage.md)                | Fiókkulcs-hitelesítés, felügyelt identitás-hitelesítés |
    | [1. generációs Azure Data Lake Storage](connector-azure-data-lake-store.md) | Egyszerű szolgáltatásnév hitelesítése                            |
    | [2. generációs Azure Data Lake Storage](connector-azure-data-lake-storage.md) | Fiókkulcs-hitelesítés, felügyelt identitás-hitelesítés |

    >[!IMPORTANT]
    >Ha az Azure Storage vnet szolgáltatásvégpontdal van konfigurálva, felügyelt identitás-hitelesítést kell használnia – lásd [a VNet-szolgáltatásvégpontok azure storage-lal való használatának hatása](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage)című területet. Ismerje meg a szükséges konfigurációkat a Data Factory az [Azure Blob - felügyelt identitás-hitelesítés](connector-azure-blob-storage.md#managed-identity) és az Azure Data Lake Storage [Gen2 - felügyelt identitás hitelesítési](connector-azure-data-lake-storage.md#managed-identity) szakaszban.

2. A **forrásadat-formátum** **Parketta,** **ORC**vagy **Tagolt szöveg,** a következő konfigurációkkal:

   1. A mappa elérési útja nem tartalmaz helyettesítő szűrőt.
   2. A fájlnév üres, vagy egyetlen fájlra mutat. Ha helyettesítő fájlnevet ad meg a másolási tevékenységben, akkor az csak a `*` vagy `*.*`.
   3. `rowDelimiter`az **alapértelmezett**, **\n**, **\r\n**vagy **\r**.
   4. `nullValue`marad alapértelmezettként, vagy **üres karakterláncra** `treatEmptyAsNull` ("" van beállítva), és alapértelmezettként marad, vagy true értékre van állítva.
   5. `encodingName`marad alapértelmezettként, vagy **utf-8 értékre**van állítva.
   6. `quoteChar`, `escapeChar`és `skipLineCount` nincs megadva. A PolyBase támogatja a fejlécsor `firstRowAsHeader` kihagyását, amely az ADF-hez megfelelően konfigurálható.
   7. `compression`lehet **nincs tömörítés,** **GZip**vagy **Deflate**.

3. Ha a forrás mappa, `recursive` a másolási tevékenységben igaz értékre kell állítani.

4. `wildcardFolderPath`, `wildcardFilename` `modifiedDateTimeStart`, `modifiedDateTimeEnd` `additionalColumns` és nincs megadva.

>[!NOTE]
>Ha a forrás mappa, vegye figyelembe, hogy a PolyBase fájlokat kér be a mappából és annak összes almappájából, és nem olvassa be az adatokat olyan fájlokból, amelyeknél a fájlnév aláhúzással (_) vagy pont (.) karakterrel kezdődik, ahogy az itt dokumentált [- HELY argumentum](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest#arguments-2).

```json
"activities":[
    {
        "name": "CopyFromAzureBlobToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "ParquetDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "ParquetSource",
                "storeSettings":{
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            }
        }
    }
]
```

### <a name="staged-copy-by-using-polybase"></a>Szakaszos másolás a PolyBase használatával

Ha a forrásadatok natív módon nem kompatibilisek a PolyBase szolgáltatással, engedélyezze az adatok másolását egy köztes átmeneti Azure Blob storage-példányon keresztül (nem lehet az Azure Premium Storage). Ebben az esetben az Azure Data Factory automatikusan konvertálja az adatokat, hogy megfeleljenek a PolyBase adatformátum-követelményeinek. Ezután meghívja a PolyBase adatokat az SQL Data Warehouse-ba. Végül törli az ideiglenes adatokat a blob storage-ból. Tekintse [meg a Szakaszos másolás](copy-activity-performance.md#staged-copy) az adatok egy átmeneti Azure Blob storage-példányon keresztül történő másolásának részleteit.

A funkció használatához hozzon létre egy [Azure Blob Storage-kapcsolt szolgáltatás,](connector-azure-blob-storage.md#linked-service-properties) amely hivatkozik az Azure storage-fiók az ideiglenes blob storage. Ezután `enableStaging` adja `stagingSettings` meg a másolási tevékenység és tulajdonságait az alábbi kódban látható módon.

>[!IMPORTANT]
>Ha az átmeneti Azure Storage vnet szolgáltatás-végpontdal van konfigurálva, felügyelt identitás-hitelesítést kell használnia – lásd [a VNet-szolgáltatásvégpontok azure-tárolóval való használatának hatása](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage)című területet. Ismerje meg a szükséges konfigurációkat a Data Factory az [Azure Blob - felügyelt identitás hitelesítés.](connector-azure-blob-storage.md#managed-identity)

```json
"activities":[
    {
        "name": "CopyFromSQLServerToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "SQLServerDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
]
```

### <a name="best-practices-for-using-polybase"></a>Gyakorlati tanácsok a PolyBase használatához

Az alábbi szakaszok az [Azure Synapse Analytics ajánlott eljárásokon](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-best-practices.md)kívül gyakorlati tanácsokat is kínálnak.

#### <a name="required-database-permission"></a>Szükséges adatbázis-engedély

A PolyBase használatához az SQL Data Warehouse-ba adatokat betöltő felhasználónak ["CONTROL" engedéllyel](https://msdn.microsoft.com/library/ms191291.aspx) kell rendelkeznie a céladatbázishoz. Ennek egyik módja a felhasználó hozzáadása a **db_owner** szerepkör tagjaként. Ebből megtudhatja, hogyan teheti meg ezt az [SQL Data Warehouse áttekintése című témakörben.](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization)

#### <a name="row-size-and-data-type-limits"></a>Sorméret- és adattípus-korlátok

A PolyBase terhelései 1 MB-nál kisebb sorokra korlátozódnak. Nem használható varchr(MAX), NVARCHAR(MAX) vagy VARBINARY(MAX) fájlba való betöltésre. További információ: [SQL Data Warehouse szolgáltatás kapacitáskorlátok](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Ha a forrásadatok sorai 1 MB-nál nagyobbak, érdemes lehet függőlegesen felosztani a forrástáblákat több kisebbre. Győződjön meg arról, hogy az egyes sorok legnagyobb mérete nem haladja meg a korlátot. A kisebb táblák ezután betölthetők a PolyBase használatával, és egyesíthetők az Azure Synapse Analytics szolgáltatásban.

Az ilyen széles oszlopokkal rendelkező adatok esetében a nem PolyBase segítségével az ADF használatával töltheti be az adatokat a "PolyBase engedélyezése" beállítás kikapcsolásával.

#### <a name="sql-data-warehouse-resource-class"></a>SQL Data Warehouse erőforrásosztály

A lehető legjobb átviteli kapacitás elérése érdekében rendeljen hozzá egy nagyobb erőforrásosztályt a felhasználóhoz, amely adatokat tölt be az SQL Data Warehouse-ba a PolyBase-en keresztül.

#### <a name="polybase-troubleshooting"></a>PolyBase hibaelhárítás

**Betöltés a Decimális oszlopba**

Ha a forrásadatok szöveges formátumúak vagy más, nem PolyBase-kompatibilis tárolók (szakaszos másolás sal és PolyBase használatával), és üres értéket tartalmaznak az SQL Data Warehouse decimális oszlopába betöltendő értékként, a következő hibát találhatja meg:

```
ErrorCode=FailedDbOperation, ......HadoopSqlException: Error converting data type VARCHAR to DECIMAL.....Detailed Message=Empty string can't be converted to DECIMAL.....
```

A megoldás az , hogy törölje a **"Type default**" opció (hamis) jelölőnégyzet jelölését a másolási tevékenység fogadójában -> PolyBase-beállításokat. Az "[USE_TYPE_DEFAULT](https://docs.microsoft.com/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest#arguments
)" egy PolyBase natív konfiguráció, amely meghatározza, hogyan kell kezelni a hiányzó értékeket a tagolt szövegfájlokban, amikor a PolyBase adatokat olvas le a szövegfájlból. 

**`tableName`az Azure Synapse Analytics szolgáltatásban**

Az alábbi táblázat példákat mutat be a **TableName** tulajdonság json adatkészletben való megadására. A séma és a táblanevek több kombinációját jeleníti meg.

| ADATBÁZIS-séma | Tábla neve | **táblaneve** JSON tulajdonság               |
| --------- | ---------- | ----------------------------------------- |
| Dbo között       | Saját táblázat    | MyTable vagy dbo. MyTable vagy [dbo]. [MyTable] |
| dbo1      | Saját táblázat    | dbo1. MyTable vagy [dbo1]. [MyTable]          |
| Dbo között       | Saját tábla   | [My.Table] vagy [dbo]. [Saját.Table]            |
| dbo1      | Saját tábla   | [dbo1]. [Saját.Table]                         |

Ha a következő hiba jelenik meg, a probléma a **tableName** tulajdonsághoz megadott érték lehet. A **tableName** JSON tulajdonság értékeinek megadásához az előző táblázatban található.

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

**Oszlopok alapértelmezett értékekkel**

Jelenleg a Data Factory PolyBase szolgáltatása csak ugyanannyi oszlopot fogad el, mint a céltáblában. Erre példa egy négy oszlopot tartalmazó táblázat, ahol az egyik alapértelmezett értékkel van definiálva. A bemeneti adatoknak még négy oszlopra van szüksége. A háromoszlopos bemeneti adatkészlet a következőhöz hasonló hibaüzenetet eredményez:

```
All columns of the table must be specified in the INSERT BULK statement.
```

A NULL érték az alapértelmezett érték speciális formája. Ha az oszlop nullázható, az adott oszlop blobjában lévő bemeneti adatok üresek lehetnek. De nem hiányozhat a bemeneti adatkészletből. A PolyBase null értéket szúr be az Azure Synapse Analytics hiányzó értékeihez.

## <a name="use-copy-statement-to-load-data-into-azure-sql-data-warehouse-preview"></a><a name="use-copy-statement"></a>Adatok betöltése az Azure SQL Data Warehouse-ba (előzetes verzió) COPY utasítás használatával

Az SQL Data Warehouse [COPY utasítás](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (előzetes verzió) közvetlenül támogatja az Azure Blobból és az Azure Data Lake Storage Gen2-ből származó adatok **betöltését.** Ha a forrásadatok megfelelnek az ebben a szakaszban leírt feltételeknek, választhat, hogy az ADF COPY utasításhasználatával adatokat tölt be az Azure SQL Data Warehouse-ba. Az Azure Data Factory ellenőrzi a beállításokat, és nem sikerül a másolási tevékenység futtatása, ha a feltételek nem teljesülnek.

>[!NOTE]
>Jelenleg A Data Factory csak az alább említett COPY utasításkompatibilis forrásokból származó másolatot támogatja.

A COPY utasítás használata a következő konfigurációt támogatja:

1. A **forráshoz csatolt szolgáltatás és formátum** a következő típusokkal és hitelesítési módszerekkel van elhasználva:

    | Támogatott forrásadattár típusa                             | Támogatott formátum           | Támogatott forráshitelesítéstípusa                         |
    | :----------------------------------------------------------- | -------------------------- | :----------------------------------------------------------- |
    | [Azure-blob](connector-azure-blob-storage.md)                | [Tagolt szöveg](format-delimited-text.md)             | Fiókkulcs-hitelesítés, megosztott hozzáférésű aláírás-hitelesítés, egyszerű szolgáltatáshitelesítés, felügyelt identitáshitelesítés |
    | &nbsp;                                                       | [Parketta](format-parquet.md)                    | Fiókkulcs-hitelesítés, megosztott hozzáférésű aláírás hitelesítése |
    | &nbsp;                                                       | [Ork](format-orc.md)                        | Fiókkulcs-hitelesítés, megosztott hozzáférésű aláírás hitelesítése |
    | [2. generációs Azure Data Lake Storage](connector-azure-data-lake-storage.md) | [Tagolt szöveg](format-delimited-text.md)<br/>[Parketta](format-parquet.md)<br/>[Ork](format-orc.md) | Fiókkulcs-hitelesítés, egyszerű szolgáltatáshitelesítés, felügyelt identitáshitelesítés |

    >[!IMPORTANT]
    >Ha az Azure Storage vnet szolgáltatásvégpontdal van konfigurálva, felügyelt identitás-hitelesítést kell használnia – lásd [a VNet-szolgáltatásvégpontok azure storage-lal való használatának hatása](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage)című területet. Ismerje meg a szükséges konfigurációkat a Data Factory az [Azure Blob - felügyelt identitás-hitelesítés](connector-azure-blob-storage.md#managed-identity) és az Azure Data Lake Storage [Gen2 - felügyelt identitás hitelesítési](connector-azure-data-lake-storage.md#managed-identity) szakaszban.

2. A formázási beállítások a következők:

   1. **Parketta** `compression` esetén: nem lehet **tömörítés,** **Snappy**vagy **GZip**.
   2. **Orc**esetén: `compression` nem lehet **tömörítés,** **zlib**vagy **Snappy**.
   3. **Tagolt szöveg**esetén:
      1. `rowDelimiter`explicit módon **egyetlen karakterként** vagy "**\r\n**" karakterként van beállítva, az alapértelmezett érték nem támogatott.
      2. `nullValue`marad alapértelmezettként, vagy **üres karakterláncra** van állítva ("").
      3. `encodingName`marad alapértelmezettként, vagy **utf-8 vagy utf-16**értékre van állítva.
      4. `escapeChar`meg kell `quoteChar`egyeznie a lehetőségnek, és nem üres.
      5. `skipLineCount`marad alapértelmezettként, vagy 0-ra van állítva.
      6. `compression`lehet **nem tömörítés** vagy **GZip**.

3. Ha a forrás mappa, `recursive` a másolási tevékenységben igaz értékre kell állítani.

4. `wildcardFolderPath`, `wildcardFilename` `modifiedDateTimeStart`, `modifiedDateTimeEnd` `additionalColumns` és nincs megadva.

A `allowCopyCommand` másolási tevékenység a következő COPY utasítás-beállításokat támogatja:

| Tulajdonság          | Leírás                                                  | Kötelező                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| alapértelmezett értékek | Az SQL DW minden egyes céloszlopának alapértelmezett értékeit adja meg.  A tulajdonság alapértelmezett értékei felülírják az adatraktárban beállított DEFAULT megkötést, és az identitásoszlopnak nem lehet alapértelmezett értéke. | Nem |
| további opciók | További beállítások, amelyek et át kell adni az SQL DW COPY utasításközvetlenül a "With" záradék [COPY utasítás .](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) Szükség szerint adja meg az értéket a COPY utasítás követelményeinek való megfeleléshez. | Nem |

```json
"activities":[
    {
        "name": "CopyFromAzureBlobToSQLDataWarehouseViaCOPY",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "ParquetDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "ParquetSource",
                "storeSettings":{
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "SqlDWSink",
                "allowCopyCommand": true, 
                "copyCommandSettings": {
                    "defaultValues": [ 
                        { 
                            "columnName": "col_string", 
                            "defaultValue": "DefaultStringValue" 
                        }
                    ],
                    "additionalOptions": { 
                        "MAXERRORS": "10000", 
                        "DATEFORMAT": "'ymd'" 
                    }
                }
            },
            "enableSkipIncompatibleRow": true
        }
    }
]
```


## <a name="lookup-activity-properties"></a>A keresgaszíntevékenység tulajdonságai

A tulajdonságokrészleteinek megismeréséhez ellenőrizze a [Kereskövetési tevékenységet.](control-flow-lookup-activity.md)

## <a name="getmetadata-activity-properties"></a>GetMetadata tevékenység tulajdonságai

A tulajdonságokrészleteinek megismeréséhez jelölje be a [GetMetadata tevékenység](control-flow-get-metadata-activity.md) 

## <a name="data-type-mapping-for-azure-sql-data-warehouse"></a>Adattípus-leképezés az Azure SQL Data Warehouse-hoz

## <a name="mapping-data-flow-properties"></a>Adatfolyam tulajdonságainak leképezése

Amikor átalakítja az adatokat az adatleképezési adatfolyamban, az Azure Synapse Analytics tábláit olvashatja és írhatja. További információ: a [forrásátalakítása](data-flow-source.md) és a [fogadó átalakítása](data-flow-sink.md) az adatfolyamatok leképezésében.

### <a name="source-transformation"></a>Forrás átalakítása

Az Azure Synapse Analytics-beállítások a forrásátalakítás **Forrásbeállítások** lapján érhetők el. 

**Bemenet:** Adja meg, hogy a forrást ```Select * from <table-name>```egy táblára (azaz ) irányítsa, vagy adjon meg egy egyéni SQL-lekérdezést.

**Lekérdezés**: Ha a beviteli mezőben a Lekérdezés lehetőséget választjuk, írjunk be egy SQL-lekérdezést a forráshoz. Ez a beállítás felülír minden olyan táblát, amelyet az adatkészletben választott. **A Rendelési** záradékok itt nem támogatottak, de beállíthat egy teljes SELECT FROM utasítást. A felhasználó által definiált táblafüggvényeket is használhatja. **select * udfGetData()** egy UDF SQL-ben, amely egy táblázatot ad vissza. Ez a lekérdezés olyan forrástáblát hoz létre, amelyet az adatfolyamban használhat. A lekérdezések használata is nagyszerű módja annak, hogy csökkentse a sorok tesztelése vagy a lookups. 

* SQL példa:```Select * from MyTable where customerId > 1000 and customerId < 2000```

**Kötegméret:** Adja meg a köteg méretét, hogy nagy adatokat tömbljön be olvasásokba.

**Elkülönítési szint**: Az SQL-források alapértelmezett leképezési adatfolyam olvasása nem véglegesített. Az elkülönítési szintet itt a következő értékek egyikére módosíthatja:
* Véglegesített olvasás
* Nem véglegesített olvasás
* Ismételhető olvasás
* Szerializálható
* Nincs (az elkülönítési szint figyelmen kívül hagyása)

![Elkülönítési szint](media/data-flow/isolationlevel.png "Elkülönítési szint")

### <a name="sink-transformation"></a>Mosogató átalakítása

Az Azure Synapse Analytics-beállítások a fogadó **átalakításának Beállítások** lapján érhetők el.

**Frissítési módszer:** Azt határozza meg, hogy milyen műveletek engedélyezettek az adatbázis célállomásán. Az alapértelmezett beállítás az, hogy csak a beszúrásokat engedélyezi. A sorok frissítéséhez, upsert-jéhez vagy törléséhez egy alter-row átalakításra van szükség a műveletek sorainak címkézéséhez. A frissítések, upserts és törlések, egy kulcs oszlop vagy oszlopok kell beállítani, hogy melyik sort kell módosítani.

**Táblázat művelet:** Azt határozza meg, hogy az írás előtt az összes sort újra létrehozza vagy eltávolítja-e a céltáblából.
* Nincs: Az asztalhoz nem történik művelet.
* Újralétrehozás: A tábla elesik, és újra létrejön. Új tábla dinamikus létrehozása esetén szükséges.
* Csonkolás: A céltábla minden sora eltávolításra kerül.

**Előkészítés engedélyezése:** Meghatározza, hogy használja-e a [PolyBase-t](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide?view=sql-server-ver15) az Azure Synapse Analytics szolgáltatásba való íráskor

**Kötegméret**: Azt szabályozza, hogy hány sor kerül írásra az egyes gyűjtőkben. A nagyobb kötegméretek javítják a tömörítést és a memóriaoptimalizálást, de az adatok gyorsítótárazásakor kiszorítják a memóriából kitett kivételeket.

**SQL-parancsfájlok előtti és postai száma:** Adja meg azokat a többsoros SQL-parancsfájlokat, amelyek az (előfeldolgozás) és az (utófeldolgozás) előtt és után kerülnek a Sink-adatbázisba

![SQL-feldolgozás előtti és utáni parancsfájlok](media/data-flow/prepost1.png "SQL-feldolgozási parancsfájlok")

## <a name="data-type-mapping-for-azure-synapse-analytics"></a>Adattípus-leképezés az Azure Synapse Analytics-hez

Amikor adatokat másol az Azure Synapse Analytics szolgáltatásból vagy az Azure Synapse Analytics szolgáltatásba, a következő leképezéseket használja a rendszer az Azure Synapse Analytics adattípusaiból az Azure Data Factory köztes adattípusaihoz. Tekintse meg [a séma- és adattípus-hozzárendeléseket,](copy-activity-schema-and-type-mapping.md) amelyekből megtudhatja, hogy a Másolási tevékenység hogyan képezi le a forrássémát és az adattípust a fogadóhoz.

>[!TIP]
>Tekintse meg [az Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-data-types.md) az ON SQL DW által támogatott adattípusokról és a nem támogatott agazatok megoldási cikkét.

| Az Azure Synapse Analytics adattípusa    | Adatgyár köztes adattípusa |
| :------------------------------------ | :----------------------------- |
| bigint                                | Int64                          |
| binary                                | Bájt[]                         |
| bit                                   | Logikai                        |
| Char                                  | Karakterlánc, Karakter                 |
| dátum                                  | DateTime                       |
| Datetime                              | DateTime                       |
| datetime2                             | DateTime                       |
| Dátumidő-eltolás                        | DateTimeOffset (Dátumidő-eltolás)                 |
| Decimal                               | Decimal                        |
| FILESTREAM attribútum (varbinary(max)) | Bájt[]                         |
| Float                                 | Double                         |
| image                                 | Bájt[]                         |
| int                                   | Int32                          |
| Pénzt                                 | Decimal                        |
| nchar                                 | Karakterlánc, Karakter                 |
| numerikus                               | Decimal                        |
| nvarchar                              | Karakterlánc, Karakter                 |
| real                                  | Egyirányú                         |
| rowversion (sorverzió)                            | Bájt[]                         |
| smalldatetime                         | DateTime                       |
| smallint                              | Int16                          |
| kis pénz                            | Decimal                        |
| time                                  | időtartam                       |
| tinyint                               | Bájt                           |
| uniqueidentifier                      | Guid                           |
| varbinary között                             | Bájt[]                         |
| varchar                               | Karakterlánc, Karakter                 |

## <a name="next-steps"></a>További lépések
Az Azure Data Factory másolási tevékenység által forrásként és fogadóként támogatott adattárak listáját a [támogatott adattárak és formátumok című témakörben tetszhet.](copy-activity-overview.md#supported-data-stores-and-formats)
