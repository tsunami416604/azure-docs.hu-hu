---
title: Az Azure szinapszis Analytics szolgáltatásban tárolt adatmásolási és-átalakítási funkciók
description: Ismerje meg, hogyan másolhat adatok az Azure szinapszis Analytics szolgáltatásba és onnan, és hogyan alakíthatja át az adatait az Azure szinapszis Analyticsben Data Factory használatával.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/25/2020
ms.openlocfilehash: 4890013fe584c49caa9e358c924911255a7f5d33
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/25/2020
ms.locfileid: "88815963"
---
# <a name="copy-and-transform-data-in-azure-synapse-analytics-formerly-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Adatmásolás és átalakítás az Azure szinapszis Analyticsben (korábban Azure SQL Data Warehouse) a használatával Azure Data Factory

> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
>
> - [Version1](v1/data-factory-azure-sql-data-warehouse-connector.md)
> - [Aktuális verzió](connector-azure-sql-data-warehouse.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok másolásához az Azure szinapszis Analytics szolgáltatásba, és hogyan lehet adatfolyamot használni az adatok Azure Data Lake Storage Gen2 való átalakításához. A Azure Data Factoryről a [bevezető cikkben](introduction.md)olvashat bővebben.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez az Azure szinapszis Analytics-összekötő a következő tevékenységek esetében támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrix-](copy-activity-overview.md) táblázattal
- [Adatfolyam hozzárendelése](concepts-data-flow-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)

Másolási tevékenység esetén ez az Azure szinapszis Analytics-összekötő a következő funkciókat támogatja:

- Adatmásolás SQL-hitelesítéssel és Azure Active Directory (Azure AD) alkalmazás-jogkivonat hitelesítéssel az Azure-erőforrásokhoz tartozó egyszerű szolgáltatásnév vagy felügyelt identitás használatával.
- Forrásként egy SQL-lekérdezés vagy tárolt eljárás használatával kérhet le egy adatforrást. A részleteket az Azure szinapszis Analytics-forrásból [származó párhuzamos](#parallel-copy-from-synapse-analytics) másolással is megtekintheti.
- Fogadóként az adatok betöltését a [Base](#use-polybase-to-load-data-into-azure-sql-data-warehouse) vagy a [copy utasítás](#use-copy-statement) (előzetes verzió) vagy a tömeges Beszúrás használatával. A jobb másolási teljesítmény érdekében javasolt a Base vagy a COPY utasítás (előzetes verzió). Az összekötő támogatja a céltábla automatikus létrehozását is, ha az nem létezik a forrásoldali séma alapján.

> [!IMPORTANT]
> Ha Azure Data Factory Integration Runtime használatával másol Adatmásolást, konfigurálja a [kiszolgálói szintű tűzfalszabály](../azure-sql/database/firewall-configure.md) használatát, hogy az Azure-szolgáltatások hozzáférhessenek a [logikai SQL-kiszolgálóhoz](../azure-sql/database/logical-servers.md).
> Ha saját üzemeltetésű integrációs modul használatával másol Adatmásolást, konfigurálja a tűzfalat a megfelelő IP-címtartomány engedélyezéséhez. Ez a tartomány tartalmazza a számítógép IP-címét, amely az Azure szinapszis Analyticshez való kapcsolódáshoz használatos.

## <a name="get-started"></a>Bevezetés

> [!TIP]
> A legjobb teljesítmény eléréséhez használja a Base-t az adatok Azure szinapszis Analyticsbe való betöltéséhez. Az [adatok Azure szinapszis analyticsbe való betöltéséhez használja](#use-polybase-to-load-data-into-azure-sql-data-warehouse) a következőt:. A használati eseteket bemutató bemutatóért lásd: [1 TB betöltése az Azure szinapszis Analytics szolgáltatásba 15 perc alatt, Azure Data Factory](load-azure-sql-data-warehouse.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük azokat a tulajdonságokat, amelyek az Azure szinapszis Analytics-összekötőhöz tartozó Data Factory entitásokat határozzák meg.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Az Azure szinapszis Analytics társított szolgáltatása a következő tulajdonságokat támogatja:

| Tulajdonság            | Leírás                                                  | Kötelező                                                     |
| :------------------ | :----------------------------------------------------------- | :----------------------------------------------------------- |
| típus                | A Type tulajdonságot **AzureSqlDW**értékre kell beállítani.             | Igen                                                          |
| connectionString    | A **ConnectionString** tulajdonsághoz tartozó Azure szinapszis Analytics-példányhoz való kapcsolódáshoz szükséges információk megadása. <br/>A mező megjelölése SecureString, hogy biztonságosan tárolja Data Factoryban. A jelszó/egyszerű szolgáltatásnév kulcsát a Azure Key Vaultban is elhelyezheti, és ha az SQL-hitelesítése lekéri a `password` konfigurációt a kapcsolatok karakterláncán kívül. További részletekért tekintse meg a táblázat alatti JSON-példát, és [tárolja a hitelesítő adatokat Azure Key Vault](store-credentials-in-key-vault.md) cikkben. | Igen                                                          |
| servicePrincipalId  | Határozza meg az alkalmazás ügyfél-AZONOSÍTÓját.                         | Igen, ha Azure AD-hitelesítést használ egy egyszerű szolgáltatással. |
| servicePrincipalKey | Az alkalmazás kulcsának meghatározása. Megjelöli ezt a mezőt SecureString, hogy biztonságosan tárolja Data Factoryban, vagy [hivatkozjon a Azure Key Vault tárolt titkos kulcsra](store-credentials-in-key-vault.md). | Igen, ha Azure AD-hitelesítést használ egy egyszerű szolgáltatással. |
| Bérlő              | Adja meg a bérlői adatokat (tartománynevet vagy bérlői azonosítót), amely alatt az alkalmazás található. Lekérheti a Azure Portal jobb felső sarkában lévő egér fölé. | Igen, ha Azure AD-hitelesítést használ egy egyszerű szolgáltatással. |
| azureCloudType | Az egyszerű szolgáltatás hitelesítéséhez adja meg az Azure AD-alkalmazás regisztrálásához használt Azure-beli felhőalapú környezet típusát. <br/> Az engedélyezett értékek a következők: **AzurePublic**, **AzureChina**, **AzureUsGovernment**és **AzureGermany**. Alapértelmezés szerint a rendszer az adatfeldolgozó felhőalapú környezetét használja. | Nem |
| Connectvia tulajdonsággal          | Az adattárhoz való csatlakozáshoz használt [integrációs](concepts-integration-runtime.md) modul. Használhat Azure Integration Runtime vagy saját üzemeltetésű integrációs modult (ha az adattár egy magánhálózaton található). Ha nincs megadva, az alapértelmezett Azure Integration Runtime használja. | Nem                                                           |

Különböző hitelesítési típusok esetén tekintse át az előfeltételek és JSON-minták következő, az előfeltételeket és a JSON-mintákat ismertető szakaszt:

- [SQL-hitelesítés](#sql-authentication)
- Azure AD-alkalmazás-jogkivonat hitelesítése: [szolgáltatásnév](#service-principal-authentication)
- Azure AD-alkalmazás-jogkivonat hitelesítése: [felügyelt identitások az Azure-erőforrásokhoz](#managed-identity)

>[!TIP]
>Ha a hibakódot "UserErrorFailedToConnectToSqlServer"-ként találta, és a következőhöz hasonló üzenet jelenik meg: "az adatbázis munkamenet-korlátja XXX, és elérte a műveletet.", adja hozzá `Pooling=false` a kapcsolati karakterláncot, és próbálkozzon újra.

### <a name="sql-authentication"></a>SQL-hitelesítés

#### <a name="linked-service-example-that-uses-sql-authentication"></a>SQL-hitelesítést használó társított szolgáltatási példa

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

**Jelszó Azure Key Vaultban:**

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

A szolgáltatás egyszerű Azure AD-alapú alkalmazás-jogkivonat-hitelesítésének használatához kövesse az alábbi lépéseket:

1. **[Hozzon létre egy Azure Active Directory alkalmazást](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)** a Azure Portal. Jegyezze fel az alkalmazás nevét és a társított szolgáltatást meghatározó következő értékeket:

   - Alkalmazásazonosító
   - Alkalmazás kulcsa
   - Bérlőazonosító

2. Ha még nem tette meg, hozzon **[létre egy Azure Active Directory rendszergazdát](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-database)** a kiszolgálójának Azure Portal. Az Azure AD-rendszergazda lehet egy Azure AD-felhasználó vagy egy Azure AD-csoport. Ha a csoportot felügyelt identitással adja meg rendszergazdai szerepkörrel, ugorja át a 3. és a 4. lépést. A rendszergazdának teljes hozzáférése lesz az adatbázishoz.

3. **[Hozzon létre tárolt adatbázis-felhasználókat](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)** az egyszerű szolgáltatásnév számára. Kapcsolódjon az adattárházhoz a vagy a rendszerből, amelyről olyan eszközökkel szeretné másolni az adatait, mint a SSMS, egy olyan Azure AD-identitással, amely legalább bármilyen felhasználói engedély megváltoztatásával rendelkezik. Futtassa a következő T-SQL-T:
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. **Adja meg az egyszerű szolgáltatáshoz szükséges engedélyeket az** SQL-felhasználók vagy mások számára általában. Futtassa a következő kódot, vagy tekintse meg a [további lehetőségeket.](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017) Ha az adatok betöltéséhez szeretné használni a Base-t, olvassa el a [szükséges adatbázis-engedélyt](#required-database-permission).

    ```sql
    EXEC sp_addrolemember db_owner, [your application name];
    ```

5. Az **Azure szinapszis Analytics társított szolgáltatásának konfigurálása** Azure Data Factoryban.

#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>A szolgáltatás egyszerű hitelesítését használó társított szolgáltatási példa

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

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a> Felügyelt identitások az Azure-erőforrások hitelesítéséhez

Az adatok előállítója az adott gyárat képviselő [Azure-erőforrások felügyelt identitásával](data-factory-service-identity.md) társítható. Ezt a felügyelt identitást használhatja az Azure szinapszis Analytics-hitelesítéshez. A kijelölt gyár ezen identitás használatával elérheti és átmásolhatja az adattárházból származó és onnan származó adatok adatait.

A felügyelt identitásos hitelesítés használatához kövesse az alábbi lépéseket:

1. Ha még nem tette meg, hozzon **[létre egy Azure Active Directory rendszergazdát](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-database)** a Azure Portal-kiszolgálóhoz. Az Azure AD-rendszergazda lehet egy Azure AD-felhasználó vagy egy Azure AD-csoport. Ha a csoportot felügyelt identitással adja meg rendszergazdai szerepkörrel, ugorja át a 3. és a 4. lépést. A rendszergazdának teljes hozzáférése lesz az adatbázishoz.

2. **[Hozzon létre tárolt adatbázis-felhasználókat](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)** a Data Factory felügyelt identitáshoz. Kapcsolódjon az adattárházhoz a vagy a rendszerből, amelyről olyan eszközökkel szeretné másolni az adatait, mint a SSMS, egy olyan Azure AD-identitással, amely legalább bármilyen felhasználói engedély megváltoztatásával rendelkezik. Futtassa az alábbi T-SQL-T.
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. **Adja meg a Data Factory felügyelt identitáshoz szükséges engedélyeket** , ahogyan az SQL-felhasználók és mások számára általában nem. Futtassa a következő kódot, vagy tekintse meg a [további lehetőségeket.](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017) Ha az adatok betöltéséhez szeretné használni a Base-t, olvassa el a [szükséges adatbázis-engedélyt](#required-database-permission).

    ```sql
    EXEC sp_addrolemember db_owner, [your Data Factory name];
    ```

4. Az **Azure szinapszis Analytics társított szolgáltatásának konfigurálása** Azure Data Factoryban.

**Példa**

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

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját az [adatkészletek](concepts-datasets-linked-services.md) című cikkben találja.

Az Azure szinapszis Analytics-adatkészlet a következő tulajdonságokat támogatja:

| Tulajdonság  | Leírás                                                  | Kötelező                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| típus      | Az adatkészlet **Type** tulajdonságát **AzureSqlDWTable**értékre kell állítani. | Igen                         |
| schema | A séma neve. |Nem, forrás, igen, fogadó  |
| table | A tábla vagy nézet neve. |Nem, forrás, igen, fogadó  |
| tableName | A tábla/nézet neve a sémával. Ez a tulajdonság visszamenőleges kompatibilitás esetén támogatott. Az új számítási feladatokhoz használja a és a elemet `schema` `table` . | Nem, forrás, igen, fogadó |

### <a name="dataset-properties-example"></a>Adatkészlet tulajdonságai – példa

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

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját a [folyamatok](concepts-pipelines-activities.md) című cikkben találja. Ez a szakasz az Azure szinapszis Analytics-forrás és a fogadó által támogatott tulajdonságok listáját tartalmazza.

### <a name="azure-synapse-analytics-as-the-source"></a>Az Azure szinapszis Analytics forrása

>[!TIP]
>Ha az adatok particionálásával hatékonyan szeretné betölteni az Azure szinapszis Analytics adatait, további információt a [szinapszis Analyticsből származó párhuzamos másolatból](#parallel-copy-from-synapse-analytics)tudhat meg.

Az adatok Azure szinapszis Analyticsből való másolásához állítsa a **Type (típus** ) tulajdonságot a másolási tevékenység forrása **SqlDWSource**értékre. A másolási tevékenység **forrása** szakasz a következő tulajdonságokat támogatja:

| Tulajdonság                     | Leírás                                                  | Kötelező |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| típus                         | A másolási tevékenység forrásának **Type** tulajdonságát **SqlDWSource**értékre kell állítani. | Igen      |
| sqlReaderQuery               | Az egyéni SQL-lekérdezés használatával olvassa be az adatolvasást. Példa: `select * from MyTable`. | Nem       |
| sqlReaderStoredProcedureName | Annak a tárolt eljárásnak a neve, amely beolvassa az adatokat a forrás táblából. Az utolsó SQL-utasításnak SELECT utasításnak kell lennie a tárolt eljárásban. | Nem       |
| storedProcedureParameters    | A tárolt eljárás paraméterei.<br/>Az engedélyezett értékek név vagy érték párok. A paraméterek nevének és burkolatának meg kell egyeznie a tárolt eljárás paramétereinek nevével és házával. | Nem       |
| isolationLevel | Meghatározza az SQL-forrás tranzakció-zárolási viselkedését. Az engedélyezett értékek a következők: **ReadCommitted**, **ReadUncommitted**, **RepeatableRead**, **szerializálható**, **Pillanatkép**. Ha nincs megadva, a rendszer az adatbázis alapértelmezett elkülönítési szintjét használja. További részletekért tekintse meg [ezt a dokumentációt](https://docs.microsoft.com/dotnet/api/system.data.isolationlevel) . | Nem |
| partitionOptions | Megadja az adatok Azure szinapszis Analyticsből való betöltéséhez használt adatparticionálási beállításokat. <br>Az engedélyezett értékek a következők: **none** (alapértelmezett), **PhysicalPartitionsOfTable**és **DynamicRange**.<br>Ha engedélyezve van egy partíciós beállítás (azaz nem `None` ), az Azure szinapszis-elemzésből származó adatok párhuzamos betöltésének foka a [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) másolási tevékenység beállításai vezérlik. | Nem |
| partitionSettings | Határozza meg az adatparticionálási beállítások csoportját. <br>Akkor alkalmazza, ha a partíció lehetőség nem `None` . | Nem |
| ***Alatt `partitionSettings` :*** | | |
| partitionColumnName | Adja meg a forrás oszlop nevét **Integer vagy Date/datetime típusú** értékben, amelyet a rendszer a párhuzamos másolási tartomány particionálásakor fog használni. Ha nincs megadva, a rendszer automatikusan észleli a tábla indexét vagy elsődleges kulcsát, és a partíció oszlopként használja.<br>Akkor alkalmazza, ha a partíciós beállítás van `DynamicRange` . Ha lekérdezést használ a forrásadatok beolvasásához,  `?AdfDynamicRangePartitionCondition ` a WHERE záradékban lévő hookot. Példaként tekintse meg az [SQL Database párhuzamos másolási](#parallel-copy-from-synapse-analytics) szakaszát. | Nem |
| partitionUpperBound | A particionálási tartomány felosztásának partíciós oszlopának maximális értéke. Ezzel az értékkel lehet eldönteni, hogy a partíció Stride-e, nem pedig a táblázat sorainak szűrésére. A program a tábla vagy a lekérdezés eredményének összes sorát particionálja és másolja. Ha nincs megadva, a másolási tevékenység automatikusan felismeri az értéket.  <br>Akkor alkalmazza, ha a partíciós beállítás van `DynamicRange` . Példaként tekintse meg az [SQL Database párhuzamos másolási](#parallel-copy-from-synapse-analytics) szakaszát. | Nem |
| partitionLowerBound | A particionálási tartomány felosztásához szükséges partíciós oszlop minimális értéke. Ezzel az értékkel lehet eldönteni, hogy a partíció Stride-e, nem pedig a táblázat sorainak szűrésére. A program a tábla vagy a lekérdezés eredményének összes sorát particionálja és másolja. Ha nincs megadva, a másolási tevékenység automatikusan felismeri az értéket.<br>Akkor alkalmazza, ha a partíciós beállítás van `DynamicRange` . Példaként tekintse meg az [SQL Database párhuzamos másolási](#parallel-copy-from-synapse-analytics) szakaszát. | Nem |

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

**Minta tárolt eljárás:**

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

### <a name="azure-synapse-analytics-as-sink"></a><a name="azure-sql-data-warehouse-as-sink"></a> Azure szinapszis-elemzés fogadóként

Azure Data Factory háromféle módon tölthető be az adatSQL Data Warehouseba.

![SQL DW-fogadó másolási beállításai](./media/connector-azure-sql-data-warehouse/sql-dw-sink-copy-options.png)

- [A Base használata](#use-polybase-to-load-data-into-azure-sql-data-warehouse)
- [A COPY utasítás használata (előzetes verzió)](#use-copy-statement)
- Tömeges Beszúrás használata

Az adatok betöltésének leggyorsabb és leginkább méretezhető módja a [(vagy a](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) [copy](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) ) (előzetes verzió).

Az adatAzure SQL Data Warehouseba való másoláshoz állítsa a fogadó típust a másolási tevékenység **SqlDWSink**. A másolási tevékenység fogadója szakasz a következő **sink** tulajdonságokat támogatja:

| Tulajdonság          | Leírás                                                  | Kötelező                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| típus              | A másolási tevékenység fogadójának **Type** tulajdonságát **SqlDWSink**értékre kell állítani. | Igen                                           |
| allowPolyBase     | Azt jelzi, hogy az adatok a SQL Data Warehouseba való betöltéséhez használható-e a albase. `allowCopyCommand` és `allowPolyBase` nem lehet egyszerre igaz. <br/><br/>A korlátozások és részletek a következő témakörben olvashatók: az adatok Azure SQL Data Warehouse szakaszba való [betöltésének használata](#use-polybase-to-load-data-into-azure-sql-data-warehouse) .<br/><br/>Az engedélyezett értékek: **true** és **false** (alapértelmezett). | Nem.<br/>Alkalmazhatja a Base használatakor.     |
| polyBaseSettings  | Tulajdonságok csoportja, amely akkor adható meg, ha a `allowPolybase` tulajdonság értéke TRUE ( **igaz**). | Nem.<br/>Alkalmazhatja a Base használatakor. |
| allowCopyCommand | Azt jelzi, hogy a [copy utasítást](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (előzetes verzió) kell-e használni az adatok SQL Data Warehouseba való betöltéséhez. `allowCopyCommand` és `allowPolyBase` nem lehet egyszerre igaz. <br/><br/>Megkötések és részletek a [másolási utasítás használata az adatok Azure SQL Data Warehouse szakaszba való betöltéséhez](#use-copy-statement) című témakörben talál.<br/><br/>Az engedélyezett értékek: **true** és **false** (alapértelmezett). | Nem.<br>A MÁSOLÁSkor alkalmazandó. |
| copyCommandSettings | Tulajdonságok csoportja, amely akkor adható meg, ha `allowCopyCommand` a tulajdonság értéke TRUE (igaz). | Nem.<br/>A MÁSOLÁSkor alkalmazandó. |
| writeBatchSize    | A **kötegekben**az SQL-táblába beillesztett sorok száma.<br/><br/>Az engedélyezett érték **egész szám** (sorok száma). Alapértelmezés szerint a Data Factory dinamikusan meghatározza a megfelelő batch-méretet a sor mérete alapján. | Nem.<br/>Tömeges Beszúrás használatakor alkalmazandó.     |
| writeBatchTimeout | Várakozási idő a kötegelt beszúrási művelet befejezéséhez az időtúllépés előtt.<br/><br/>Az engedélyezett érték a **TimeSpan**. Például: "00:30:00" (30 perc). | Nem.<br/>Tömeges Beszúrás használatakor alkalmazandó.        |
| preCopyScript     | Adja meg a másolási tevékenység futtatásához szükséges SQL-lekérdezést, mielőtt az egyes futtatások Azure SQL Data Warehousebe írna. Ezzel a tulajdonsággal törölheti az előre feltöltött adatkészleteket. | Nem                                            |
| tableOption | Meghatározza, hogy a [rendszer automatikusan létrehozza](copy-activity-overview.md#auto-create-sink-tables) -e a fogadó táblát, ha az nem létezik a forrásoldali séma alapján. Az engedélyezett értékek a következők: `none` (alapértelmezett), `autoCreate` . |Nem |
| disableMetricsCollection | A Data Factory olyan mérőszámokat gyűjt, mint például a SQL Data Warehouse DWU a másolási teljesítmény optimalizálása és a javaslatok tekintetében. Ha ezt a viselkedést érinti, a `true` kikapcsolásához válassza a következőt:. | Nem (alapértelmezett érték `false` ) |

#### <a name="sql-data-warehouse-sink-example"></a>SQL Data Warehouse fogadó példa

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

## <a name="parallel-copy-from-synapse-analytics"></a>Párhuzamos másolás a szinapszis Analyticsből

Az Azure szinapszis Analytics-összekötő a másolási tevékenységben beépített adatparticionálást biztosít az adatmásoláshoz párhuzamosan. Az adatparticionálási beállítások a másolási tevékenység **forrás** lapján találhatók.

![Képernyőfelvétel a partíciós beállításokról](./media/connector-sql-server/connector-sql-partition-options.png)

A particionált másolás engedélyezésekor a másolási tevékenység párhuzamos lekérdezéseket futtat az Azure szinapszis Analytics-forráson az adatpartíciók betöltéséhez. A párhuzamos mértéket a [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) másolási tevékenység beállításai vezérlik. Ha például négyre van állítva `parallelCopies` , Data Factory egyidejűleg létrehoz és futtat négy lekérdezést a megadott partíciós beállítás és beállítások alapján, és mindegyik lekérdezés az Azure szinapszis Analyticsből származó adatok egy részét kéri le.

Javasoljuk, hogy engedélyezze a párhuzamos másolást az adatok particionálásával, különösen akkor, ha nagy mennyiségű adatmennyiséget tölt be az Azure szinapszis Analyticsből. Az alábbiakban a különböző forgatókönyvekhez javasolt konfigurációk szerepelnek. Az adatok file-alapú adattárba való másolása esetén ajánlott több fájlként írni egy mappába (csak a mappa nevét adja meg), ebben az esetben a teljesítmény jobb, mint egyetlen fájl írásakor.

| Forgatókönyv                                                     | Javasolt beállítások                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Teljes terhelés nagyméretű táblából, fizikai partíciókkal.        | **Partíciós beállítás**: a tábla fizikai partíciói. <br><br/>A végrehajtás során a Data Factory automatikusan észleli a fizikai partíciókat, és az Adatmásolást partíciók szerint. |
| Teljes terhelés a nagyméretű táblából fizikai partíciók nélkül, egész számmal vagy datetime oszloppal az adatok particionálásához. | **Partíciós beállítások**: dinamikus tartományú partíció.<br>**Partíciós oszlop** (nem kötelező): az adatparticionáláshoz használt oszlop megadása. Ha nincs megadva, a rendszer az indexet vagy az elsődleges kulcs oszlopot használja.<br/>**Partíció felső határa** és a **partíció alsó határa** (nem kötelező): adja meg, hogy meg szeretné-e állapítani a partíciós lépéseket. Ez nem a tábla sorainak szűrésére szolgál, a tábla összes sorát particionálja és másolja a rendszer. Ha nincs megadva, a másolási tevékenység automatikusan felismeri az értékeket.<br><br>Ha például az "ID" partíciós oszlop értéke 1 és 100 között van, és az alsó határ 20-ra van állítva, a felső határ pedig 80, és a párhuzamos másolás 4 Data Factory, akkor az adatok 4 partíció-azonosítóval vannak lekérdezve a következő tartományban: <= 20, [21, 50], [51, 80] és >= 81. |
| Nagy mennyiségű adat betöltése egyéni lekérdezéssel fizikai partíciók nélkül, egész szám vagy dátum/datetime oszlop használatával az adatok particionálásához. | **Partíciós beállítások**: dinamikus tartományú partíció.<br>**Lekérdezés**: `SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>` .<br>**Partíciós oszlop**: az adatparticionáláshoz használt oszlop megadására szolgál.<br>**Partíció felső határa** és a **partíció alsó határa** (nem kötelező): adja meg, hogy meg szeretné-e állapítani a partíciós lépéseket. Ez nem a tábla sorainak szűrésére szolgál, a lekérdezés eredményének összes sorát particionálja és másolja a rendszer. Ha nincs megadva, a másolási tevékenység automatikusan felismeri az értéket.<br><br>A végrehajtás során Data Factory lecseréli `?AdfRangePartitionColumnName` az egyes partíciók tényleges oszlop-és értéktartomány-tartományára, és elküldi az Azure szinapszis Analyticsnek. <br>Ha például az "ID" partíciós oszlop értéke 1 és 100 között van, és az alsó határ 20-ra van állítva, a felső határ pedig 80, és a párhuzamos másolás 4 Data Factory, akkor az adatok 4 partíció-azonosítóval vannak lekérdezve a következő tartományban: <= 20, [21, 50], [51, 80] és >= 81. |

Ajánlott eljárások az adattároláshoz a Partition kapcsolóval:

1. Válassza a megkülönböztető oszlop partíciós oszlopként (például az elsődleges kulcs vagy az egyedi kulcs) lehetőséget az adattorzítás elkerüléséhez. 
2. Ha a tábla beépített partícióval rendelkezik, a jobb teljesítmény érdekében használja a "fizikai partíciók tábla" partíciós beállítást.
3. Ha Azure Integration Runtimet használ az adatmásoláshoz, több számítási erőforrás kihasználása érdekében megadhat nagyobb "[adatintegrációs egységeket (DIU)](copy-activity-performance-features.md#data-integration-units)" (>4). Tekintse át a megfelelő forgatókönyveket.
4. "A[másolási párhuzamosság foka](copy-activity-performance-features.md#parallel-copy)" szabályozza a partíciók számát, így a szám túl nagy ideig nem árt a teljesítménynek, javasoljuk, hogy ezt a számot (DIU vagy saját ÜZEMELTETÉSű IR-csomópontok száma) * (2 – 4) állítsa be.
5. Megjegyzés: az Azure szinapszis Analytics egy pillanat alatt legfeljebb 32 lekérdezést futtathat, a "másolási párhuzamosság foka" beállítás túl nagy mértékben okozhatja a szinapszisok szabályozásával kapcsolatos problémát.

**Példa: teljes terhelés a nagyméretű táblából fizikai partíciókkal**

```json
"source": {
    "type": "SqlDWSource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**Példa: lekérdezés dinamikus tartományú partícióval**

```json
"source": {
    "type": "SqlDWSource",
    "query": "SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column (optional) to decide the partition stride, not as data filter>",
        "partitionLowerBound": "<lower_value_of_partition_column (optional) to decide the partition stride, not as data filter>"
    }
}
```

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>Adatok betöltése a Azure SQL Data Warehouseba a Base használatával

A [Base](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) használatával nagy mennyiségű adat tölthető be az Azure szinapszis analyticsbe nagy átviteli sebességgel. Az adatátviteli sebesség nagy mennyiségű nyereségét az alapértelmezett BULKINSERT mechanizmus helyett a albase használatával fogja látni. A használati eseteket bemutató bemutatóért lásd: [1 TB betöltése az Azure szinapszis analyticsbe](v1/data-factory-load-sql-data-warehouse.md).

- Ha a forrásadatok az **Azure blobban vannak, Azure Data Lake Storage Gen1 vagy Azure Data Lake Storage Gen2**, és a **formátum a Base-kompatibilis**, a másolási tevékenység használatával közvetlenül is meghívhatja a albaseot, hogy Azure SQL Data Warehouse lekérje az adatok forrásból való lekérését. Részletekért lásd: a **[közvetlen másolás a Base használatával](#direct-copy-by-using-polybase)**.
- Ha a forrás adattárat és a formátumot eredetileg nem a Base támogatja, használja a **[szakaszos másolást a kiindulási funkció használatával](#staged-copy-by-using-polybase)** . Az előkészített másolási funkció jobb átviteli sebességet is biztosít. A szolgáltatás automatikusan konvertálja az adatok alap-kompatibilis formátumba, az adatok tárolása az Azure Blob Storage-ban, majd az adatok SQL Data Warehouseba való betöltését kéri.

> [!TIP]
> További információ a [Base használatának ajánlott eljárásairól](#best-practices-for-using-polybase).

A másolási tevékenység alatt a következő alapbeállítások támogatottak `polyBaseSettings` :

| Tulajdonság          | Leírás                                                  | Kötelező                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| rejectValue       | A lekérdezés sikertelensége előtt visszautasítható sorok számát vagy százalékos arányát adja meg.<br/><br/>További információk a [create External Table (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx)argumentumok szakaszban található "a Base 's elutasítás" beállításairól. <br/><br/>Az engedélyezett értékek: 0 (alapértelmezett), 1, 2 stb. | Nem                                            |
| rejectType        | Megadja, hogy a **rejectValue** kapcsoló literális érték vagy százalék-e.<br/><br/>Az engedélyezett értékek **érték** (alapértelmezett) és **százalék**. | Nem                                            |
| rejectSampleValue | Meghatározza a lekérdezni kívánt sorok számát, mielőtt a rendszer újraszámítja az elutasított sorok százalékos arányát.<br/><br/>Az engedélyezett értékek: 1, 2 stb. | Igen, ha a **rejectType** **százaléka százalék**. |
| useTypeDefault    | Meghatározza, hogy a rendszer hogyan kezelje a hiányzó értékeket a tagolt szövegfájlokban, ha a viszonyítási adatok beolvasása a szövegfájlból történik.<br/><br/>Erről a tulajdonságról a [külső fájlformátum létrehozása (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx)argumentumai című szakaszban olvashat bővebben.<br/><br/>Az engedélyezett értékek: **true** és **false** (alapértelmezett).<br><br> | Nem                                            |

### <a name="direct-copy-by-using-polybase"></a>Közvetlen másolás a Base használatával

SQL Data Warehouse a közvetlenül támogatja az Azure blobot, Azure Data Lake Storage Gen1 és Azure Data Lake Storage Gen2. Ha a forrásadatok megfelelnek az ebben a szakaszban ismertetett feltételeknek, használja a következőt a forrás adattárból az Azure szinapszis Analyticsbe való közvetlen másoláshoz. Ellenkező esetben használjon [a szakaszos másolást a Base használatával](#staged-copy-by-using-polybase).

> [!TIP]
> Ha az adatok hatékony másolását SQL Data Warehousera szeretné elvégezni, további információt a [Azure Data Factory még könnyebbé és kényelmesebben is megtudhatja, hogy a Data Lake Store SQL Data Warehouse használatával hogyan lehet az adatokból információkat feltárni](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/).

Ha a követelmények nem teljesülnek, Azure Data Factory ellenőrzi a beállításokat, és automatikusan visszakerül a BULKINSERT mechanizmusra az adatáthelyezéshez.

1. A **forráshoz társított szolgáltatás** a következő típusokkal és hitelesítési módszerekkel rendelkezik:

    | Támogatott forrásoldali adattár típusa                             | Támogatott forrás-hitelesítési típus                        |
    | :----------------------------------------------------------- | :---------------------------------------------------------- |
    | [Azure-blob](connector-azure-blob-storage.md)                | Fiók kulcsának hitelesítése, felügyelt identitások hitelesítése |
    | [1. generációs Azure Data Lake Storage](connector-azure-data-lake-store.md) | Egyszerű szolgáltatásnév hitelesítése                            |
    | [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | Fiók kulcsának hitelesítése, felügyelt identitások hitelesítése |

    >[!IMPORTANT]
    >Ha az Azure Storage VNet szolgáltatás-végponttal van konfigurálva, akkor felügyelt identitás-hitelesítést kell használnia – a [VNet szolgáltatás-végpontok Azure Storage-ban való használatának következményeire](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage)utal. Ismerje meg az [Azure Blob által felügyelt identitás-hitelesítés](connector-azure-blob-storage.md#managed-identity) és a [Azure Data Lake Storage Gen2 által felügyelt identitás-hitelesítési](connector-azure-data-lake-storage.md#managed-identity) szakasz Data Factory szükséges konfigurációit.

2. A **forrásadatok formátuma** a következő konfigurációkkal rendelkező **parketta**, **ork**vagy **tagolt szöveg**:

   1. A mappa elérési útja nem tartalmaz helyettesítő szűrőt.
   2. A fájl neve üres, vagy egyetlen fájlra mutat. Ha a helyettesítő fájl nevét adja meg a másolási tevékenységben, az csak a vagy a lehet `*` `*.*` .
   3. `rowDelimiter`**alapértelmezett**, **\n**, **\r\n**vagy **\r**.
   4. `nullValue` Alapértelmezés szerint marad, vagy **üres karakterláncra** ("") van állítva, és `treatEmptyAsNull` az alapértelmezett érték, vagy igaz értékre van állítva.
   5. `encodingName` Alapértelmezés szerint marad, vagy az **UTF-8**értékre van állítva.
   6. `quoteChar`, `escapeChar` és `skipLineCount` nincs megadva. A albase-támogatás kihagyása a fejlécsorból, amely `firstRowAsHeader` Az ADF-ben konfigurálható.
   7. `compression` nem lehet **tömörítés**, **gzip**vagy **deflate**.

3. Ha a forrás mappa, `recursive` a másolási tevékenységben igaz értékre kell állítani.

4. `wildcardFolderPath` ,,, `wildcardFilename` `modifiedDateTimeStart` `modifiedDateTimeEnd` és `additionalColumns` nincsenek megadva.

>[!NOTE]
>Ha a forrás mappa, vegye figyelembe, hogy a Base lekéri a fájlokat a mappából és annak összes almappájából, és nem kéri le az adatokból azokat a fájlokat, amelyekhez a fájlnév aláhúzással (_) vagy ponttal (.) kezdődik [.](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest#arguments-2)

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

### <a name="staged-copy-by-using-polybase"></a>Előkészített másolás a Base használatával

Ha a forrásadatok nem natív módon kompatibilisek a-alapú alkalmazásokkal, engedélyezze az adatok másolását egy átmeneti Azure Blob Storage-példányon keresztül (nem lehet Azure Premium Storage). Ebben az esetben a Azure Data Factory automatikusan átalakítja az adatokra, hogy megfeleljenek a Base adatformátumra vonatkozó követelményeinek. Ezután meghívja a SQL Data Warehouseba az adatok betöltését. Végezetül törli az ideiglenes adatait a blob Storage-ból. Az adatok átmeneti Azure Blob Storage-példányon keresztül történő másolásával kapcsolatos részletekért tekintse meg a [Lépcsőzetes másolás című szakaszt](copy-activity-performance-features.md#staged-copy) .

A szolgáltatás használatához hozzon létre egy [azure blob Storage társított szolgáltatást](connector-azure-blob-storage.md#linked-service-properties) , amely az Azure Storage-fiókra hivatkozik az ideiglenes blob Storage-tárolóval. Ezután adja meg a `enableStaging` és a `stagingSettings` tulajdonságokat a másolási tevékenységhez, ahogy az a következő kódban is látható.

>[!IMPORTANT]
>Ha az előkészítési Azure Storage VNet szolgáltatás-végponttal van konfigurálva, akkor felügyelt identitás-hitelesítést kell használnia – a [VNet szolgáltatás-végpontok Azure Storage-ban való használatának következményeire](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage)utal. Az [Azure Blob által felügyelt identitás-hitelesítés](connector-azure-blob-storage.md#managed-identity)Data Factory a szükséges konfigurációk megismerése.

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

### <a name="best-practices-for-using-polybase"></a>Ajánlott eljárások a Base használatához

A következő szakaszokban az ajánlott eljárások az [Azure szinapszis Analytics ajánlott eljárásaiban](../synapse-analytics/sql/best-practices-sql-pool.md)is szerepelnek.

#### <a name="required-database-permission"></a>Szükséges adatbázis-engedély

A (z) rendszerbe való betöltést követően a felhasználónak ["vezérlés" engedéllyel](https://msdn.microsoft.com/library/ms191291.aspx) kell rendelkeznie a céladatbázis SQL Data Warehouse. Ennek egyik módja, ha a felhasználót a **db_owner** szerepkör tagjaként adja hozzá. Ebből a cikkből megtudhatja, hogyan teheti meg ezt a [SQL Data Warehouse áttekintésében](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

#### <a name="row-size-and-data-type-limits"></a>Sorok mérete és adattípusi korlátai

A kiinduló terhelések az 1 MB-nál kisebb sorokra korlátozódnak. Nem használható a VARCHR (MAX), a NVARCHAR (MAX) vagy a VARBINARY (MAX) való betöltéshez. További információ: [SQL Data Warehouse szolgáltatási kapacitás korlátai](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Ha a forrásadatok 1 MB-nál nagyobb méretű sorokkal rendelkeznek, érdemes lehet függőlegesen felosztani a forrás táblákat több kis méretre. Győződjön meg arról, hogy az egyes sorok legnagyobb mérete nem haladja meg a korlátot. A kisebb táblák ezután a Base használatával tölthetők be, és összekapcsolhatók az Azure szinapszis Analyticsben.

Azt is megteheti, hogy az ilyen széles oszlopokkal rendelkező adatok esetében nem hozhatók be az adatok az ADF használatával, ha kikapcsolja az "alapszintű" beállítást.

#### <a name="sql-data-warehouse-resource-class"></a>SQL Data Warehouse Resource osztály

A lehető legjobb teljesítmény elérése érdekében rendeljen hozzá egy nagyobb erőforrás-osztályt ahhoz a felhasználóhoz, amely az adatok a SQL Data Warehouseba való betöltését teszi lehetővé.

#### <a name="polybase-troubleshooting"></a>Alapszintű hibaelhárítás

**Betöltés decimális oszlopba**

Ha a forrásadatok szöveges formátumban vagy más nem a-alapú kompatibilis tárolókban találhatók (a szakaszos másolással és a paritással), és üres értéket tartalmaz SQL Data Warehouse decimális oszlopba való betöltéshez, a következő hibaüzenet jelenhet meg:

```
ErrorCode=FailedDbOperation, ......HadoopSqlException: Error converting data type VARCHAR to DECIMAL.....Detailed Message=Empty string can't be converted to DECIMAL.....
```

A megoldás az "**alapértelmezett típus használata**" beállítás kijelölésének feloldása (hamis) a másolási tevékenység fogadója – > a bázisterület alapbeállításai. A "[USE_TYPE_DEFAULT](https://docs.microsoft.com/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest#arguments
)" egy alapszintű natív konfiguráció, amely meghatározza, hogy a rendszer hogyan kezelje a hiányzó értékeket a tagolt szövegfájlokban, amikor a viszonyítási adatok beolvasása a szövegfájlból történik.

**`tableName` Az Azure szinapszis Analyticsben**

A következő táblázat példákat mutat be arra, hogyan határozhatja meg a **Táblanév** tulajdonságot a JSON-adatkészletben. A séma és a táblanév számos kombinációját mutatja.

| ADATBÁZIS-séma | Tábla neve | **Táblanév** JSON-tulajdonság               |
| --------- | ---------- | ----------------------------------------- |
| dbo       | Sajáttábla    | Sajáttábla vagy dbo. Sajáttábla vagy [dbo]. Sajáttábla |
| dbo1      | Sajáttábla    | dbo1. Sajáttábla vagy [dbo1]. Sajáttábla          |
| dbo       | Saját. tábla   | [Saját. table] vagy [dbo]. [Saját tábla]            |
| dbo1      | Saját. tábla   | [dbo1]. [Saját tábla]                         |

Ha a következő hiba jelenik meg, a probléma lehet a **Táblanév** tulajdonsághoz megadott érték. A **Táblanév** JSON-tulajdonság értékeinek megadásához tekintse meg a fenti táblázatot a megfelelő módon.

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

**Alapértelmezett értékeket tartalmazó oszlopok**

Jelenleg a Data Factory a (z) alapszintű szolgáltatása csak a célként megadott tábla oszlopainak számát fogadja el. Ilyen például egy olyan tábla, amelyben négy oszlop található, ahol az egyiket alapértelmezett értékkel definiálják. A bemeneti adatoknak még négy oszlopra van szükségük. A három oszlopból álló bemeneti adatkészlet a következő üzenethez hasonló hibát eredményez:

```
All columns of the table must be specified in the INSERT BULK statement.
```

A NULL érték az alapértelmezett érték egy speciális formája. Ha az oszlop üres, akkor előfordulhat, hogy az adott oszlop blobjában lévő bemeneti adatok üresek. De nem lehet hiányzik a bemeneti adatkészletből. Az Azure szinapszis Analyticsben hiányzó értékekhez tartozó unbase lapkák NULL értékűek.

## <a name="use-copy-statement-to-load-data-into-azure-sql-data-warehouse-preview"></a><a name="use-copy-statement"></a> Adatok betöltése a MÁSOLÁSi utasítás használatával Azure SQL Data Warehouseba (előzetes verzió)

A SQL Data Warehouse [copy utasítás](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (előzetes verzió) közvetlenül támogatja az **Azure blobból és Azure Data Lake Storage Gen2ból**származó adatok betöltését. Ha a forrásadatok megfelelnek az ebben a szakaszban leírt feltételeknek, dönthet úgy, hogy az ADF MÁSOLÁSi utasítását használja az adatok Azure SQL Data Warehouseba való betöltéséhez. Azure Data Factory ellenőrzi a beállításokat, és sikertelenül futtatja a másolási tevékenységet, ha a feltételek nem teljesülnek.

>[!NOTE]
>Jelenleg Data Factory csak a COPY utasítással kompatibilis, alább említett forrásokból származó másolást támogatja.

A COPY utasítás használata a következő konfigurációt támogatja:

1. A **forrásként társított szolgáltatás és formátum** a következő típusokkal és hitelesítési módszerekkel rendelkezik:

    | Támogatott forrásoldali adattár típusa                             | Támogatott formátum           | Támogatott forrás-hitelesítési típus                         |
    | :----------------------------------------------------------- | -------------------------- | :----------------------------------------------------------- |
    | [Azure-blob](connector-azure-blob-storage.md)                | [Tagolt szöveg](format-delimited-text.md)             | Fiók kulcsának hitelesítése, közös hozzáférésű aláírás-hitelesítés, egyszerű szolgáltatásnév hitelesítés, felügyelt identitások hitelesítése |
    | &nbsp;                                                       | [Parquet](format-parquet.md)                    | Fiók kulcsának hitelesítése, közös hozzáférésű aláírások hitelesítése |
    | &nbsp;                                                       | [ORC](format-orc.md)                        | Fiók kulcsának hitelesítése, közös hozzáférésű aláírások hitelesítése |
    | [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | [Tagolt szöveg](format-delimited-text.md)<br/>[Parquet](format-parquet.md)<br/>[ORC](format-orc.md) | Fiók kulcsának hitelesítése, egyszerű szolgáltatásnév hitelesítése, felügyelt identitások hitelesítése |

    >[!IMPORTANT]
    >Ha az Azure Storage VNet szolgáltatás-végponttal van konfigurálva, akkor felügyelt identitás-hitelesítést kell használnia – a [VNet szolgáltatás-végpontok Azure Storage-ban való használatának következményeire](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage)utal. Ismerje meg az [Azure Blob által felügyelt identitás-hitelesítés](connector-azure-blob-storage.md#managed-identity) és a [Azure Data Lake Storage Gen2 által felügyelt identitás-hitelesítési](connector-azure-data-lake-storage.md#managed-identity) szakasz Data Factory szükséges konfigurációit.

2. A formátum beállításai a következők:

   1. A **parketta**esetében: `compression` **nem lehet tömörítés**, **Snappy**vagy **gzip**.
   2. Az **ork**esetében: `compression` **nem lehet tömörítés**, **```zlib```** vagy **Snappy**.
   3. **Tagolt szöveg**esetén:
      1. `rowDelimiter` explicit módon van beállítva **egyetlen karakterként** vagy "**\r\n**", az alapértelmezett érték nem támogatott.
      2. `nullValue` Alapértelmezés szerint marad, vagy **üres karakterláncra** ("") van állítva.
      3. `encodingName` Alapértelmezés szerint balra van állítva, vagy UTF **-8 vagy UTF-16**értékre van beállítva.
      4. `escapeChar` azonosnak kell lennie `quoteChar` , és nem üres.
      5. `skipLineCount` alapértelmezett vagy 0 értékre van állítva.
      6. `compression` nem lehet **tömörítés** vagy **gzip**.

3. Ha a forrás mappa, `recursive` a másolási tevékenységnek igaz értékűnek kell lennie, és a következőnek kell `wildcardFilename` lennie: `*` . 

4. `wildcardFolderPath` , `wildcardFilename` (a kivételével `*` ), `modifiedDateTimeStart` `modifiedDateTimeEnd` és `additionalColumns` nincs megadva.

A másolási tevékenység alatt a következő MÁSOLÁSi utasítás beállításai támogatottak `allowCopyCommand` :

| Tulajdonság          | Leírás                                                  | Kötelező                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| defaultValues | Meghatározza az SQL DW-beli egyes célértékek alapértelmezett értékeit.  A tulajdonság alapértelmezett értékei felülírják az adatraktárban beállított alapértelmezett korlátozást, és az Identity oszlop nem rendelkezhet alapértelmezett értékkel. | Nem |
| additionalOptions | További beállítások, amelyek az SQL DW COPY utasításhoz közvetlenül a [copy utasítás](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest)"with" záradékával lesznek átadva. A MÁSOLÁSi utasítás követelményeinek megfelelően adja meg az értéket, ha szükséges. | Nem |

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

## <a name="mapping-data-flow-properties"></a>Adatfolyam-tulajdonságok leképezése

Az adatok leképezési folyamatba való átalakításakor az Azure szinapszis Analyticsből származó táblákat olvashat és írhat. További információ: a forrás- [átalakítás](data-flow-source.md) és a fogadó [transzformáció](data-flow-sink.md) a leképezési adatfolyamatokban.

### <a name="source-transformation"></a>Forrás-átalakítás

Az Azure szinapszis Analytics szolgáltatáshoz tartozó beállítások a forrás-átalakítás **forrás beállításai** lapján érhetők el.

**Bemenet** Válassza ki, hogy a forrást egy táblán, ```Select * from <table-name>``` vagy egy egyéni SQL-lekérdezést adjon meg.

**Előkészítés engedélyezése** Javasoljuk, hogy ezt a beállítást a szinapszis DW-forrásokkal rendelkező éles számítási feladatokban használja. Ha egy folyamatból Synapase-forrásokkal rendelkező adatfolyam-tevékenységet hajt végre, az ADF egy átmeneti tárolási hely Storage-fiókját fogja kérni, és ezt fogja használni az előkészített adatok betöltéséhez. Ez a leggyorsabb mechanizmus a szinapszis DW-ből származó adatok betöltéséhez.

**Lekérdezés**: Ha a beviteli mezőben a lekérdezés lehetőséget választotta, adjon meg egy SQL-lekérdezést a forráshoz. Ez a beállítás felülbírálja az adatkészletben kiválasztott összes táblát. Az **Order by** záradékok itt nem támogatottak, de a teljes select from utasítással is megadható. A felhasználó által definiált Table functions is használható. a **select * from udfGetData ()** egy olyan UDF az SQL-ben, amely egy táblázatot ad vissza. Ez a lekérdezés létrehoz egy forrástábla, amelyet az adatfolyamatában használhat. A lekérdezések használata nagyszerű lehetőséget nyújt a sorok tesztelésre vagy keresésekre való csökkentésére is.

SQL-példa: ```Select * from MyTable where customerId > 1000 and customerId < 2000```

**Köteg mérete**: adjon meg egy batch-méretet, amely nagy mennyiségű adatokat olvas be. Az adatforgalomban az ADF ezt a beállítást fogja használni a Spark oszlopos gyorsítótárazás beállításához. Ez egy opciós mező, amely a Spark alapértelmezett értékeit fogja használni, ha az üresen marad.

**Elkülönítési szint**: a leképezési adatfolyamban található SQL-források alapértelmezett értéke nem véglegesítve. A következő értékek egyikére módosíthatja az elkülönítési szintet:

- Olvasás véglegesítve
- Nem véglegesített olvasás
- Ismételhető olvasás
- Szerializálható * – nincs (az elkülönítési szint figyelmen kívül hagyása)

![Elkülönítési szint](media/data-flow/isolationlevel.png "Elkülönítési szint")

### <a name="sink-transformation"></a>Fogadó transzformáció

Az Azure szinapszis Analytics szolgáltatáshoz tartozó beállítások a fogadó transzformáció **Beállítások** lapján érhetők el.

**Frissítési módszer:** Meghatározza, hogy mely műveletek engedélyezettek az adatbázis célhelyén. Az alapértelmezett érték a beszúrások engedélyezése. Sorok frissítéséhez, upsert vagy törléséhez módosítani kell az Alter-Row transzformációt a műveletek sorainak címkézéséhez. A frissítések, upsert és törlések esetében meg kell adni a kulcs oszlopát vagy oszlopait annak meghatározásához, hogy melyik sort kell megváltoztatni.

**Tábla művelete:** Meghatározza, hogy a rendszer az összes sort újra létrehozza vagy eltávolítja a célhelyről az írás előtt.

- Nincs: a rendszer nem hajt végre műveletet a táblán.
- Újból létrehozva: a tábla eldobása és újbóli létrehozása megtörténik. Új tábla dinamikus létrehozásakor szükséges.
- Csonkítás: a céltábla összes sora el lesz távolítva.

**Előkészítés engedélyezése:** Meghatározza, hogy az Azure szinapszis Analytics szolgáltatásba való íráskor kell-e használni a [viszonyítási alapot](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide?view=sql-server-ver15)

**Köteg mérete**: azt határozza meg, hogy hány sort kell megírni az egyes gyűjtők. A nagyobb méretű kötegek növelik a tömörítési és a memória-optimalizálást, de a gyorsítótárban tárolt adatmennyiség miatt kifogytak a memória

**SQL-parancsfájlok előtti és utáni**műveletek: Itt adhatja meg azokat a TÖBBsoros SQL-parancsfájlokat, amelyek a (z) előtti (előfeldolgozási) és a (feldolgozás utáni) adatainak a fogadó adatbázisba való beírásakor lesznek végrehajtva

![SQL-feldolgozási parancsfájlok előzetes és utáni feldolgozása](media/data-flow/prepost1.png "SQL-feldolgozási parancsfájlok")

## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [keresési tevékenységet](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>GetMetadata tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [GetMetaData tevékenységet](control-flow-get-metadata-activity.md)

## <a name="data-type-mapping-for-azure-synapse-analytics"></a>Adattípusok leképezése az Azure szinapszis Analytics szolgáltatáshoz

Amikor a vagy az Azure szinapszis Analytics szolgáltatásba másol adatait, a következő leképezéseket használja az Azure szinapszis Analytics adattípusokból Azure Data Factory átmeneti adattípusokra. Lásd: [séma-és adattípus-leképezések](copy-activity-schema-and-type-mapping.md) , amelyből megtudhatja, hogy a másolási tevékenység hogyan képezi le a forrás sémáját és az adattípust

>[!TIP]
>Az SQL DW által támogatott adattípusok és a nem támogatott megoldások esetében tekintse meg a [táblázat adattípusait az Azure szinapszis Analytics-](../synapse-analytics/sql/develop-tables-data-types.md) cikkben.

| Az Azure szinapszis Analytics adattípusa    | Data Factory időközi adattípus |
| :------------------------------------ | :----------------------------- |
| bigint                                | Int64                          |
| binary                                | Bájt []                         |
| bit                                   | Logikai érték                        |
| char                                  | Karakterlánc, char []                 |
| dátum                                  | DateTime                       |
| Datetime                              | DateTime                       |
| datetime2                             | DateTime                       |
| DateTimeOffset                        | DateTimeOffset                 |
| Tizedesjegy                               | Tizedesjegy                        |
| FILESTREAM attribútum (varbinary (max)) | Bájt []                         |
| Float                                 | Dupla                         |
| image                                 | Bájt []                         |
| int                                   | Int32                          |
| pénzt                                 | Tizedesjegy                        |
| NCHAR                                 | Karakterlánc, char []                 |
| numerikus                               | Tizedesjegy                        |
| nvarchar                              | Karakterlánc, char []                 |
| valós szám                                  | Egyirányú                         |
| ROWVERSION                            | Bájt []                         |
| idő adattípusúra                         | DateTime                       |
| smallint                              | Int16                          |
| túlcsordulási                            | Tizedesjegy                        |
| time                                  | időtartam                       |
| tinyint                               | Bájt                           |
| uniqueidentifier                      | Guid                           |
| varbinary                             | Bájt []                         |
| varchar                               | Karakterlánc, char []                 |

## <a name="next-steps"></a>További lépések

A forrásként és fogadóként támogatott adattárak listáját a Azure Data Factory másolási tevékenysége című részben tekintheti meg a [támogatott adattárak és-formátumok](copy-activity-overview.md#supported-data-stores-and-formats)című témakörben.
