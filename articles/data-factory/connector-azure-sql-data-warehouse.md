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
ms.date: 12/12/2019
ms.openlocfilehash: f009b438cb0dc227289d65604d89c11fd382b675
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78356284"
---
# <a name="copy-and-transform-data-in-azure-synapse-analytics-formerly-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Adatmásolás és átalakítás az Azure szinapszis Analyticsben (korábban Azure SQL Data Warehouse) a használatával Azure Data Factory 

> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [Version1](v1/data-factory-azure-sql-data-warehouse-connector.md)
> * [Aktuális verzió](connector-azure-sql-data-warehouse.md)

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok másolásához az Azure szinapszis Analytics szolgáltatásba, és hogyan lehet adatfolyamot használni az adatok Azure Data Lake Storage Gen2 való átalakításához. A Azure Data Factoryről a [bevezető cikkben](introduction.md)olvashat bővebben.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez az Azure szinapszis Analytics-összekötő a következő tevékenységek esetében támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrix-](copy-activity-overview.md) táblázattal
- [Adatfolyam hozzárendelése](concepts-data-flow-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)

Másolási tevékenység esetén ez az Azure szinapszis Analytics-összekötő a következő funkciókat támogatja:

- Adatok másolása az Azure-erőforrások egy egyszerű vagy felügyelt szolgáltatásidentitások SQL-hitelesítés és Azure Active Directory (Azure AD) alkalmazástoken-hitelesítésének használata által.
- Forrásként adatok lekérdezése egy SQL-lekérdezést vagy tárolt eljárás használatával.
- Fogadóként az adatok betöltését a [Base](#use-polybase-to-load-data-into-azure-sql-data-warehouse) vagy a [copy utasítás](#use-copy-statement) (előzetes verzió) vagy a tömeges Beszúrás használatával. A jobb másolási teljesítmény érdekében javasolt a Base vagy a COPY utasítás (előzetes verzió).

> [!IMPORTANT]
> Ha Azure Data Factory Integration Runtime használatával másol Adatmásolást, konfigurálja az [Azure SQL Server-tűzfalat](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) úgy, hogy az Azure-szolgáltatások hozzáférhessenek a kiszolgálóhoz.
> Ha adatokat másolja egy saját üzemeltetésű integrációs modul használatával, konfigurálja az Azure SQL-kiszolgáló tűzfalán, hogy a megfelelő IP-címtartomány. Ez a tartomány tartalmazza a számítógép IP-címét, amely az Azure szinapszis Analyticshez való kapcsolódáshoz használatos.

## <a name="get-started"></a>Első lépések

> [!TIP]
> A legjobb teljesítmény eléréséhez használja a Base-t az adatok Azure szinapszis Analyticsbe való betöltéséhez. Az [adatok Azure szinapszis analyticsbe való betöltéséhez használja](#use-polybase-to-load-data-into-azure-sql-data-warehouse) a következőt:. A használati eseteket bemutató bemutatóért lásd: [1 TB betöltése az Azure szinapszis Analytics szolgáltatásba 15 perc alatt, Azure Data Factory](load-azure-sql-data-warehouse.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük azokat a tulajdonságokat, amelyek az Azure szinapszis Analytics-összekötőhöz tartozó Data Factory entitásokat határozzák meg.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Az Azure szinapszis Analytics társított szolgáltatása a következő tulajdonságokat támogatja:

| Tulajdonság            | Leírás                                                  | Kötelező                                                     |
| :------------------ | :----------------------------------------------------------- | :----------------------------------------------------------- |
| type                | A Type tulajdonságot **AzureSqlDW**értékre kell beállítani.             | Igen                                                          |
| connectionString    | A **ConnectionString** tulajdonsághoz tartozó Azure szinapszis Analytics-példányhoz való kapcsolódáshoz szükséges információk megadása. <br/>A mező megjelölése SecureString, hogy biztonságosan tárolja Data Factoryban. A jelszó/egyszerű szolgáltatásnév kulcsát a Azure Key Vaultban is elhelyezheti, és ha az SQL-hitelesítése lekéri a `password` konfigurációt a kapcsolatok karakterláncán kívülre. További részletekért tekintse meg a táblázat alatti JSON-példát, és [tárolja a hitelesítő adatokat Azure Key Vault](store-credentials-in-key-vault.md) cikkben. | Igen                                                          |
| servicePrincipalId  | Adja meg az alkalmazás ügyfél-azonosítót.                         | Igen, egy egyszerű szolgáltatást az Azure AD-hitelesítés használata esetén. |
| servicePrincipalKey | Adja meg az alkalmazáskulcsot. Megjelöli ezt a mezőt SecureString, hogy biztonságosan tárolja Data Factoryban, vagy [hivatkozjon a Azure Key Vault tárolt titkos kulcsra](store-credentials-in-key-vault.md). | Igen, egy egyszerű szolgáltatást az Azure AD-hitelesítés használata esetén. |
| tenant              | Adja meg a bérlő információkat (tartomány neve vagy a bérlő azonosítója) alatt az alkalmazás található. Kérheti a viszi az egérmutatót az Azure portal jobb felső sarkában. | Igen, egy egyszerű szolgáltatást az Azure AD-hitelesítés használata esetén. |
| connectVia          | Az adattárhoz való csatlakozáshoz használt [integrációs](concepts-integration-runtime.md) modul. Használhatja az Azure integrációs modul vagy a saját üzemeltetésű integrációs modul (ha az adattár egy magánhálózaton található). Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. | Nem                                                           |

Különböző hitelesítési típus tekintse meg a következő szakaszok az Előfeltételek és a JSON-minták, illetve:

- [SQL-hitelesítés](#sql-authentication)
- Azure AD-alkalmazás-jogkivonat hitelesítése: [szolgáltatásnév](#service-principal-authentication)
- Azure AD-alkalmazás-jogkivonat hitelesítése: [felügyelt identitások az Azure-erőforrásokhoz](#managed-identity)

>[!TIP]
>Ha a hibakódot "UserErrorFailedToConnectToSqlServer"-ként találta, és a következőhöz hasonló üzenet jelenik meg: "az adatbázis munkamenet-korlátja XXX, és elérte a műveletet.", adja hozzá `Pooling=false` a kapcsolati karakterlánchoz, és próbálkozzon újra.

### <a name="sql-authentication"></a>SQL-hitelesítés

#### <a name="linked-service-example-that-uses-sql-authentication"></a>SQL-hitelesítést használó társított szolgáltatás példa

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

Szolgáltatás egyszerűszolgáltatás-alapú Azure AD alkalmazástoken-hitelesítésének használatához kövesse az alábbi lépéseket:

1. **[Hozzon létre egy Azure Active Directory alkalmazást](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)** a Azure Portal. Jegyezze fel az alkalmazás nevét és a következő értékeket, amelyek meghatározzák a társított szolgáltatást:

    - Alkalmazásazonosító
    - Alkalmazáskulcs
    - Bérlőazonosító

2. Ha még nem tette meg, hozzon **[létre egy Azure Active Directory rendszergazdát](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** az Azure SQL Server-kiszolgáló Azure Portal. Az Azure AD-rendszergazda lehet egy Azure AD-felhasználó vagy az Azure AD-csoporthoz. Ha a csoportot felügyelt identitással adja meg rendszergazdai szerepkörrel, ugorja át a 3. és a 4. lépést. A rendszergazda az adatbázis teljes hozzáféréssel fog rendelkezni.

3. **[Hozzon létre tárolt adatbázis-felhasználókat](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)** az egyszerű szolgáltatásnév számára. A data warehouse-bA a, vagy amely adatok másolása az Azure AD identitás, amelynek legalább az ssms-ben, más eszközök használatával szeretné bármely felhasználó ALTER engedéllyel. Futtassa a következő T-SQL:
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. **Adja meg az egyszerű szolgáltatáshoz szükséges engedélyeket az** SQL-felhasználók vagy mások számára általában. Futtassa a következő kódot, vagy tekintse meg a [további lehetőségeket.](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017) Ha az adatok betöltéséhez szeretné használni a Base-t, olvassa el a [szükséges adatbázis-engedélyt](#required-database-permission).

    ```sql
    EXEC sp_addrolemember db_owner, [your application name];
    ```

5. Az **Azure szinapszis Analytics társított szolgáltatásának konfigurálása** Azure Data Factoryban.


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Társított szolgáltatás példa, amely használja az egyszerű szolgáltatásnév hitelesítése

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

### <a name="managed-identity"></a>Felügyelt identitások az Azure-erőforrások hitelesítéséhez

Az adatok előállítója az adott gyárat képviselő [Azure-erőforrások felügyelt identitásával](data-factory-service-identity.md) társítható. Ezt a felügyelt identitást használhatja az Azure szinapszis Analytics-hitelesítéshez. Férhet hozzá a kijelölt gyári, és a, vagy az adatok másolása az adatraktár-Ez az identitás használatával.

A felügyelt identitásos hitelesítés használatához kövesse az alábbi lépéseket:

1. Ha még nem tette meg, hozzon **[létre egy Azure Active Directory rendszergazdát](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** az Azure SQL Server-kiszolgáló Azure Portal. Az Azure AD-rendszergazda lehet egy Azure AD-felhasználó vagy az Azure AD-csoporthoz. Ha a csoportot felügyelt identitással adja meg rendszergazdai szerepkörrel, ugorja át a 3. és a 4. lépést. A rendszergazda az adatbázis teljes hozzáféréssel fog rendelkezni.

2. **[Hozzon létre tárolt adatbázis-felhasználókat](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)** a Data Factory felügyelt identitáshoz. A data warehouse-bA a, vagy amely adatok másolása az Azure AD identitás, amelynek legalább az ssms-ben, más eszközök használatával szeretné bármely felhasználó ALTER engedéllyel. Futtassa a következő T-SQL. 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. **Adja meg a Data Factory felügyelt identitáshoz szükséges engedélyeket** , ahogyan az SQL-felhasználók és mások számára általában nem. Futtassa a következő kódot, vagy tekintse meg a [további lehetőségeket.](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017) Ha az adatok betöltéséhez szeretné használni a Base-t, olvassa el a [szükséges adatbázis-engedélyt](#required-database-permission).

    ```sql
    EXEC sp_addrolemember db_owner, [your Data Factory name];
    ```

5. Az **Azure szinapszis Analytics társított szolgáltatásának konfigurálása** Azure Data Factoryban.

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
| type      | Az adatkészlet **Type** tulajdonságát **AzureSqlDWTable**értékre kell állítani. | Igen                         |
| schema | A séma neve. |Nincs forrás, a fogadó Igen  |
| table | A tábla vagy nézet neve. |Nincs forrás, a fogadó Igen  |
| tableName | A tábla/nézet neve a sémával. Ez a tulajdonság visszamenőleges kompatibilitás esetén támogatott. Az új számítási feladatokhoz használja a `schema` és a `table`. | Nincs forrás, a fogadó Igen |

#### <a name="dataset-properties-example"></a>Adatkészlet tulajdonságai példa

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

Az adatok Azure szinapszis Analyticsből való másolásához állítsa a **Type (típus** ) tulajdonságot a másolási tevékenység forrása **SqlDWSource**értékre. A másolási tevékenység **forrása** szakasz a következő tulajdonságokat támogatja:

| Tulajdonság                     | Leírás                                                  | Kötelező |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| type                         | A másolási tevékenység forrásának **Type** tulajdonságát **SqlDWSource**értékre kell állítani. | Igen      |
| sqlReaderQuery               | Az egyéni SQL-lekérdezés segítségével olvassa el az adatokat. Példa: `select * from MyTable`. | Nem       |
| sqlReaderStoredProcedureName | A tárolt eljárást, amely adatokat olvas be a forrás-tábla neve. Az utolsó SQL-utasítást a tárolt eljárás a SELECT utasítással kell lennie. | Nem       |
| storedProcedureParameters    | A tárolt eljárás paraméterei.<br/>Megengedett értékek: neve vagy értéke párokat. Nevek és a kis-és a paraméterek meg kell egyeznie a neveket és a kis-és nagybetűhasználatot, a tárolt eljárás paraméterértékeinek. | Nem       |

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

### <a name="azure-sql-data-warehouse-as-sink"></a>Azure szinapszis-elemzés fogadóként

Azure Data Factory háromféle módon tölthető be az adatSQL Data Warehouseba.

![SQL DW-fogadó másolási beállításai](./media/connector-azure-sql-data-warehouse/sql-dw-sink-copy-options.png)

- [A Base használata](#use-polybase-to-load-data-into-azure-sql-data-warehouse) 
- [A COPY utasítás használata (előzetes verzió)](#use-copy-statement)
- Tömeges Beszúrás használata

Az adatok betöltésének leggyorsabb és leginkább méretezhető módja a [(vagy a](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) [copy](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) ) (előzetes verzió).

Az adatAzure SQL Data Warehouseba való másoláshoz állítsa a fogadó típust a másolási tevékenység **SqlDWSink**. A másolási tevékenység fogadója szakasz a következő tulajdonságokat támogatja:

| Tulajdonság          | Leírás                                                  | Kötelező                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| type              | A másolási tevékenység fogadójának **Type** tulajdonságát **SqlDWSink**értékre kell állítani. | Igen                                           |
| allowPolyBase     | Azt jelzi, hogy az adatok a SQL Data Warehouseba való betöltéséhez használható-e a albase. `allowCopyCommand` és `allowPolyBase` nem lehet egyszerre igaz. <br/><br/>A korlátozások és részletek a következő témakörben olvashatók: az adatok Azure SQL Data Warehouse szakaszba való [betöltésének használata](#use-polybase-to-load-data-into-azure-sql-data-warehouse) .<br/><br/>Az engedélyezett értékek: **true** és **false** (alapértelmezett). | Nem.<br/>Alkalmazhatja a Base használatakor.     |
| polyBaseSettings  | A tulajdonságok olyan csoportja, amely akkor adható meg, ha a `allowPolybase` tulajdonság értéke **true (igaz**). | Nem.<br/>Alkalmazhatja a Base használatakor. |
| allowCopyCommand | Azt jelzi, hogy a [copy utasítást](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (előzetes verzió) kell-e használni az adatok SQL Data Warehouseba való betöltéséhez. `allowCopyCommand` és `allowPolyBase` nem lehet egyszerre igaz. <br/><br/>Megkötések és részletek a [másolási utasítás használata az adatok Azure SQL Data Warehouse szakaszba való betöltéséhez](#use-copy-statement) című témakörben talál.<br/><br/>Az engedélyezett értékek: **true** és **false** (alapértelmezett). | Nem.<br>A MÁSOLÁSkor alkalmazandó. |
| copyCommandSettings | A tulajdonságok olyan csoportja, amely akkor adható meg, ha `allowCopyCommand` tulajdonság értéke TRUE (igaz). | Nem.<br/>A MÁSOLÁSkor alkalmazandó. |
| writeBatchSize    | A **kötegekben**az SQL-táblába beillesztett sorok száma.<br/><br/>Az engedélyezett érték **egész szám** (sorok száma). Alapértelmezés szerint a Data Factory dinamikusan meghatározza a megfelelő batch-méretet a sor mérete alapján. | Nem.<br/>Tömeges Beszúrás használatakor alkalmazandó.     |
| writeBatchTimeout | Várjon, amíg a kötegelt insert művelet befejezését, mielőtt azt az időkorlátot.<br/><br/>Az engedélyezett érték a **TimeSpan**. Példa: "00: 30:00" (30 perc). | Nem.<br/>Tömeges Beszúrás használatakor alkalmazandó.        |
| preCopyScript     | Adja meg a másolási tevékenység futtatása előtt az adatok Azure SQL Data Warehouse-bA írt minden egyes futtatásához egy SQL-lekérdezést. Ez a tulajdonság használatával az előre betöltött adatokat. | Nem                                            |
| tableOption | Meghatározza, hogy a rendszer automatikusan létrehozza-e a fogadó táblát, ha az nem létezik a forrásoldali séma alapján. Az automatikus tábla létrehozása nem támogatott, ha a szakaszos másolás a másolási tevékenységben van konfigurálva. Az engedélyezett értékek a következők: `none` (alapértelmezett), `autoCreate`. |Nem |
| disableMetricsCollection | A Data Factory olyan mérőszámokat gyűjt, mint például a SQL Data Warehouse DWU a másolási teljesítmény optimalizálása és a javaslatok tekintetében. Ha ezt a viselkedést érinti, a kikapcsolásához válassza a `true` lehetőséget. | Nem (az alapértelmezett érték `false`) |

#### <a name="sql-data-warehouse-sink-example"></a>Az SQL Data Warehouse fogadó példa

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

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>Adatok betöltése az Azure SQL Data Warehouse a PolyBase használatával

A [Base](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) használatával nagy mennyiségű adat tölthető be az Azure szinapszis analyticsbe nagy átviteli sebességgel. Átviteli sebesség nagyobb nyereség láthatja az alapértelmezett BULKINSERT mechanizmus helyett a PolyBase használatával. A használati eseteket bemutató bemutatóért lásd: [1 TB betöltése az Azure szinapszis analyticsbe](v1/data-factory-load-sql-data-warehouse.md).

* Ha a forrásadatok az **Azure blobban vannak, Azure Data Lake Storage Gen1 vagy Azure Data Lake Storage Gen2**, és a **formátum a Base-kompatibilis**, a másolási tevékenység használatával közvetlenül is meghívhatja a albaseot, hogy Azure SQL Data Warehouse lekérje az adatok forrásból való lekérését. Részletekért lásd: a **[közvetlen másolás a Base használatával](#direct-copy-by-using-polybase)** .
* Ha a forrás adattárat és a formátumot eredetileg nem a Base támogatja, használja a **[szakaszos másolást a kiindulási funkció használatával](#staged-copy-by-using-polybase)** . A szakaszos másolás funkciót is, nagyobb átviteli sebességet biztosít. A szolgáltatás automatikusan konvertálja az adatokat a Base-kompatibilis formátumba, az Azure Blob Storage-ban tárolja az adatok tárolását. Ezután meghívja a SQL Data Warehouseba az adatok betöltését.

>[!TIP]
>További információ a [Base használatának ajánlott eljárásairól](#best-practices-for-using-polybase).

A másolási tevékenység `polyBaseSettings` a következő alapbeállításokat támogatja:

| Tulajdonság          | Leírás                                                  | Kötelező                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| rejectValue       | Megadja a szám vagy százalékos aránya, amelyek is vissza kell utasítani, mielőtt a lekérdezés nem sikerült sorokat.<br/><br/>További információk a [create External Table (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx)argumentumok szakaszban található "a Base 's elutasítás" beállításairól. <br/><br/>Megengedett értékek: 0 (alapértelmezett), 1, 2, stb. | Nem                                            |
| rejectType        | Megadja, hogy a **rejectValue** kapcsoló literális érték vagy százalék-e.<br/><br/>Az engedélyezett értékek **érték** (alapértelmezett) és **százalék**. | Nem                                            |
| rejectSampleValue | Mielőtt PolyBase újraszámítja a visszautasított sorok aránya beolvasandó sorok számát határozza meg.<br/><br/>Megengedett értékek: 1, 2, stb. | Igen, ha a **rejectType** **százaléka százalék**. |
| useTypeDefault    | Itt adhatja meg, hogyan szeretné kezelni a PolyBase kér le adatokat a szövegfájl elválasztójellel tagolt szöveges fájlok a hiányzó értékeket.<br/><br/>Erről a tulajdonságról a [külső fájlformátum létrehozása (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx)argumentumai című szakaszban olvashat bővebben.<br/><br/>Az engedélyezett értékek: **true** és **false** (alapértelmezett).<br><br> | Nem                                            |

### <a name="direct-copy-by-using-polybase"></a>Közvetlen másolás a PolyBase használatával

SQL Data Warehouse a közvetlenül támogatja az Azure blobot, Azure Data Lake Storage Gen1 és Azure Data Lake Storage Gen2. Ha a forrásadatok megfelelnek az ebben a szakaszban ismertetett feltételeknek, használja a következőt a forrás adattárból az Azure szinapszis Analyticsbe való közvetlen másoláshoz. Ellenkező esetben használjon [a szakaszos másolást a Base használatával](#staged-copy-by-using-polybase).

> [!TIP]
> Ha az adatok hatékony másolását SQL Data Warehousera szeretné elvégezni, további információt a [Azure Data Factory még könnyebbé és kényelmesebben is megtudhatja, hogy a Data Lake Store SQL Data Warehouse használatával hogyan lehet az adatokból információkat feltárni](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/).

A követelmények nem teljesülnek, ha az Azure Data Factory ellenőrzi a beállításokat, és automatikusan visszavált az adatok áthelyezése a BULKINSERT mechanizmusa.

1. A **forráshoz társított szolgáltatás** a következő típusokkal és hitelesítési módszerekkel rendelkezik:

    | Támogatott forrásoldali adattár típusa                             | Támogatott forrás-hitelesítési típus                        |
    | :----------------------------------------------------------- | :---------------------------------------------------------- |
    | [Azure-Blob](connector-azure-blob-storage.md)                | Fiók kulcsának hitelesítése, felügyelt identitások hitelesítése |
    | [1. generációs Azure Data Lake Storage](connector-azure-data-lake-store.md) | Egyszerű szolgáltatásnév hitelesítése                            |
    | [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | Fiók kulcsának hitelesítése, felügyelt identitások hitelesítése |

    >[!IMPORTANT]
    >Ha az Azure Storage VNet szolgáltatás-végponttal van konfigurálva, akkor felügyelt identitás-hitelesítést kell használnia – a [VNet szolgáltatás-végpontok Azure Storage-ban való használatának következményeire](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage)utal. Ismerje meg az [Azure Blob által felügyelt identitás-hitelesítés](connector-azure-blob-storage.md#managed-identity) és a [Azure Data Lake Storage Gen2 által felügyelt identitás-hitelesítési](connector-azure-data-lake-storage.md#managed-identity) szakasz Data Factory szükséges konfigurációit.

2. A **forrásadatok formátuma** a következő konfigurációkkal rendelkező **parketta**, **ork**vagy **tagolt szöveg**:

   1. A mappa elérési útja nem tartalmaz helyettesítő szűrőt.
   2. A fájl neve üres, vagy egyetlen fájlra mutat. Ha helyettesítő fájl nevét adja meg a másolási tevékenységben, akkor csak `*` vagy `*.*`lehet.
   3. a `rowDelimiter` **alapértelmezett**, **\n**, **\r\n**vagy **\r**.
   4. `nullValue` az alapértelmezett érték, vagy **üres sztring** ("") értékre van állítva, és `treatEmptyAsNull` az alapértelmezett érték, vagy igaz értékre van állítva.
   5. `encodingName` az alapértelmezett érték, vagy az **UTF-8**értékre van állítva.
   6. `quoteChar`, `escapeChar`és `skipLineCount` nincs megadva. A albase-támogatás kihagyása a fejlécsorból, amely `firstRowAsHeader`ként konfigurálható ADF-ben.
   7. `compression` nem lehet **tömörítés**, **gzip**vagy **deflate**.

3. Ha a forrás egy mappa, a másolási tevékenység `recursive` a True értékre kell állítani.

4. nincs megadva `wildcardFolderPath`, `wildcardFilename`, `modifiedDateTimeStart`és `modifiedDateTimeEnd`.

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

### <a name="staged-copy-by-using-polybase"></a>A PolyBase használatával szakaszos másolás

Ha a forrásadatok nem natív módon kompatibilisek a-alapú alkalmazásokkal, engedélyezze az adatok másolását egy átmeneti Azure Blob Storage-példányon keresztül (nem lehet Azure Premium Storage). Ebben az esetben a Azure Data Factory automatikusan átalakítja az adatokra, hogy megfeleljenek a Base adatformátumra vonatkozó követelményeinek. Ezután meghívja a SQL Data Warehouseba az adatok betöltését. Végül, megtisztítja az ideiglenes adatokat a blob storage-ból. Az adatok átmeneti Azure Blob Storage-példányon keresztül történő másolásával kapcsolatos részletekért tekintse meg a [Lépcsőzetes másolás című szakaszt](copy-activity-performance.md#staged-copy) .

A szolgáltatás használatához hozzon létre egy [azure blob Storage társított szolgáltatást](connector-azure-blob-storage.md#linked-service-properties) , amely az Azure Storage-fiókra hivatkozik az ideiglenes blob Storage-tárolóval. Ezután adja meg a másolási tevékenység `enableStaging` és `stagingSettings` tulajdonságait, ahogy az a következő kódban látható.

>[!IMPORTANT]
>Ha az előkészítési Azure Storage VNet szolgáltatás-végponttal van konfigurálva, akkor felügyelt identitás-hitelesítést kell használnia – a [VNet szolgáltatás-végpontok Azure Storage-ban való használatának következményeire](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage)utal. Az [Azure Blob által felügyelt identitás-hitelesítés](connector-azure-blob-storage.md#managed-identity)Data Factory a szükséges konfigurációk megismerése.

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

### <a name="best-practices-for-using-polybase"></a>Ajánlott eljárások a polybase használatához

A következő szakaszokban az ajánlott eljárások az [Azure szinapszis Analytics ajánlott eljárásaiban](../sql-data-warehouse/sql-data-warehouse-best-practices.md)is szerepelnek.

#### <a name="required-database-permission"></a>Adatbázishoz szükséges engedélyt

A (z) rendszerbe való betöltést követően a felhasználónak ["vezérlés" engedéllyel](https://msdn.microsoft.com/library/ms191291.aspx) kell rendelkeznie a céladatbázis SQL Data Warehouse. Ennek egyik módja, ha a felhasználót a **db_owner** szerepkör tagjaként adja hozzá. Ebből a cikkből megtudhatja, hogyan teheti meg ezt a [SQL Data Warehouse áttekintésében](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

#### <a name="row-size-and-data-type-limits"></a>Sor mérete és az adatok írja be a korlátok

A PolyBase-betöltések pedig csak 1 MB-nál kisebb méretű sorokat. Nem használható a VARCHR (MAX), a NVARCHAR (MAX) vagy a VARBINARY (MAX) való betöltéshez. További információ: [SQL Data Warehouse szolgáltatási kapacitás korlátai](../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Ha a forrásadatok 1 MB-nál nagyobb sorral rendelkezik, érdemes a forrástáblákhoz függőlegesen felosztása több kis azokat. Győződjön meg arról, hogy az egyes sorok legnagyobb mérete nem haladja meg a határértéket. A kisebb táblák ezután a Base használatával tölthetők be, és összekapcsolhatók az Azure szinapszis Analyticsben.

Azt is megteheti, hogy az ilyen széles oszlopokkal rendelkező adatok esetében nem hozhatók be az adatok az ADF használatával, ha kikapcsolja az "alapszintű" beállítást.

#### <a name="sql-data-warehouse-resource-class"></a>Az SQL Data Warehouse erőforrásosztály

A felhasználót, hogy adatokat tölt be az SQL Data Warehouse polybase nagyobb erőforrásosztályt hozzárendelni a legjobb teljesítmény elérése érdekében.

#### <a name="polybase-troubleshooting"></a>Alapszintű hibaelhárítás

**Betöltés decimális oszlopba**

Ha a forrásadatok szöveges formátumban vagy más nem a-alapú kompatibilis tárolókban találhatók (a szakaszos másolással és a paritással), és üres értéket tartalmaz SQL Data Warehouse decimális oszlopba való betöltéshez, a következő hibaüzenet jelenhet meg:

```
ErrorCode=FailedDbOperation, ......HadoopSqlException: Error converting data type VARCHAR to DECIMAL.....Detailed Message=Empty string can't be converted to DECIMAL.....
```

A megoldás az "**alapértelmezett típus használata**" beállítás kijelölésének feloldása (hamis) a másolási tevékenység fogadója – > a bázisterület alapbeállításai. A "[USE_TYPE_DEFAULT](https://docs.microsoft.com/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest#arguments
)" egy alapszintű natív konfiguráció, amely meghatározza, hogy a rendszer hogyan kezelje a hiányzó értékeket a tagolt szövegfájlokban, amikor a viszonyítási adatok beolvasása a szövegfájlból történik. 

**`tableName` az Azure szinapszis Analyticsben**

A következő táblázat példákat mutat be arra, hogyan határozhatja meg a **Táblanév** tulajdonságot a JSON-adatkészletben. Séma és a táblázat neve különböző kombinációit jeleníti meg.

| Adatbázis-séma | Tábla neve | **Táblanév** JSON-tulajdonság               |
| --------- | ---------- | ----------------------------------------- |
| dbo       | MyTable    | MyTable vagy dbo.MyTable vagy [dbo].[MyTable] |
| dbo1      | MyTable    | dbo1.MyTable vagy [dbo1].[MyTable]          |
| dbo       | My.Table   | [My.Table] vagy [dbo].[My.Table]            |
| dbo1      | My.Table   | [dbo1].[My.Table]                         |

Ha a következő hiba jelenik meg, a probléma lehet a **Táblanév** tulajdonsághoz megadott érték. A **Táblanév** JSON-tulajdonság értékeinek megadásához tekintse meg a fenti táblázatot a megfelelő módon.

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

**Alapértelmezett értékeket tartalmazó oszlopok**

A PolyBase szolgáltatás, a Data Factory jelenleg csak az azonos számú oszlopot, mint a céltábla fogad el. Ilyen például, ahol az egyiket van definiálva az alapértelmezett érték négy oszlopot tartalmazó táblát. A bemeneti adatok továbbra is rendelkeznie kell négy oszlopot. Három oszlop a bemeneti adatkészlet az alábbihoz hasonló hibaüzenetet eredményez:

```
All columns of the table must be specified in the INSERT BULK statement.
```

A NULL érték az alapértelmezett érték formája. Ha az oszlop nullázható, a bemeneti adatokat az a blob az adott oszlop lehet üres. De hiányzik a bemeneti adatkészlet a nem lehet. Az Azure szinapszis Analyticsben hiányzó értékekhez tartozó unbase lapkák NULL értékűek.

## <a name="use-copy-statement"></a>Adatok betöltése a MÁSOLÁSi utasítás használatával Azure SQL Data Warehouseba (előzetes verzió)

A SQL Data Warehouse [copy utasítás](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (előzetes verzió) közvetlenül támogatja az **Azure blobból és Azure Data Lake Storage Gen2ból**származó adatok betöltését. Ha a forrásadatok megfelelnek az ebben a szakaszban leírt feltételeknek, dönthet úgy, hogy az ADF MÁSOLÁSi utasítását használja az adatok Azure SQL Data Warehouseba való betöltéséhez. Azure Data Factory ellenőrzi a beállításokat, és sikertelenül futtatja a másolási tevékenységet, ha a feltételek nem teljesülnek.

>[!NOTE]
>Jelenleg Data Factory csak a COPY utasítással kompatibilis, alább említett forrásokból származó másolást támogatja.

A COPY utasítás használata a következő konfigurációt támogatja:

1. A **forrásként társított szolgáltatás és formátum** a következő típusokkal és hitelesítési módszerekkel rendelkezik:

    | Támogatott forrásoldali adattár típusa                             | Támogatott formátum           | Támogatott forrás-hitelesítési típus                         |
    | :----------------------------------------------------------- | -------------------------- | :----------------------------------------------------------- |
    | [Azure-Blob](connector-azure-blob-storage.md)                | [Tagolt szöveg](format-delimited-text.md)             | Fiók kulcsának hitelesítése, közös hozzáférésű aláírás-hitelesítés, egyszerű szolgáltatásnév hitelesítés, felügyelt identitások hitelesítése |
    | &nbsp;                                                       | [Parquet](format-parquet.md)                    | Fiók kulcsának hitelesítése, közös hozzáférésű aláírások hitelesítése |
    | &nbsp;                                                       | [ORC](format-orc.md)                        | Fiók kulcsának hitelesítése, közös hozzáférésű aláírások hitelesítése |
    | [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | [Tagolt szöveg](format-delimited-text.md)<br/>[Parquet](format-parquet.md)<br/>[ORC](format-orc.md) | Fiók kulcsának hitelesítése, egyszerű szolgáltatásnév hitelesítése, felügyelt identitások hitelesítése |

    >[!IMPORTANT]
    >Ha az Azure Storage VNet szolgáltatás-végponttal van konfigurálva, akkor felügyelt identitás-hitelesítést kell használnia – a [VNet szolgáltatás-végpontok Azure Storage-ban való használatának következményeire](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage)utal. Ismerje meg az [Azure Blob által felügyelt identitás-hitelesítés](connector-azure-blob-storage.md#managed-identity) és a [Azure Data Lake Storage Gen2 által felügyelt identitás-hitelesítési](connector-azure-data-lake-storage.md#managed-identity) szakasz Data Factory szükséges konfigurációit.

2. A formátum beállításai a következők:

   1. A **parketta**esetében: `compression` **nem lehet tömörítés**, **Snappy**vagy **gzip**.
   2. Az **ork**esetében: `compression` **nem lehet tömörítés**, **zlib**vagy **Snappy**.
   3. **Tagolt szöveg**esetén:
      1. `rowDelimiter` explicit módon van beállítva **egyetlen karakterként** vagy " **\r\n**", az alapértelmezett érték nem támogatott.
      2. `nullValue` az alapértelmezett érték, vagy **üres karakterláncra** ("") van állítva.
      3. `encodingName` az alapértelmezett érték, vagy az **UTF-8 vagy az UTF-16**értékre van állítva.
      4. a `escapeChar`nak azonosnak kell lennie `quoteChar`, és nem üres.
      5. `skipLineCount` az alapértelmezett érték, vagy 0 értékre van állítva.
      6. `compression` nem lehet **tömörítés** vagy **gzip**.

3. Ha a forrás egy mappa, a másolási tevékenység `recursive` a True értékre kell állítani.

4. nincs megadva `wildcardFolderPath`, `wildcardFilename`, `modifiedDateTimeStart`és `modifiedDateTimeEnd`.

A másolási tevékenység `allowCopyCommand` alatt a következő MÁSOLÁSi utasítás beállításai támogatottak:

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


## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [keresési tevékenységet](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>GetMetadata tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [GetMetaData tevékenységet](control-flow-get-metadata-activity.md) 

## <a name="data-type-mapping-for-azure-sql-data-warehouse"></a>Az Azure SQL Data Warehouse adattípus-leképezés

## <a name="mapping-data-flow-properties"></a>Adatfolyam-tulajdonságok leképezése

Az adatok leképezési folyamatba való átalakításakor az Azure szinapszis Analyticsből származó táblákat olvashat és írhat. További információ: a forrás- [átalakítás](data-flow-source.md) és a fogadó [transzformáció](data-flow-sink.md) a leképezési adatfolyamatokban.

### <a name="source-transformation"></a>Forrás-átalakítás

Az Azure szinapszis Analytics szolgáltatáshoz tartozó beállítások a forrás-átalakítás **forrás beállításai** lapján érhetők el. 

**Bemenet:** Válassza ki, hogy a forrást egy táblán (```Select * from <table-name>```), vagy egy egyéni SQL-lekérdezést szeretne megadni.

**Lekérdezés**: Ha a beviteli mezőben a lekérdezés lehetőséget választotta, adjon meg egy SQL-lekérdezést a forráshoz. Ez a beállítás felülbírálja az adatkészletben kiválasztott összes táblát. Az **Order by** záradékok itt nem támogatottak, de a teljes select from utasítással is megadható. A felhasználó által definiált Table functions is használható. a **select * from udfGetData ()** egy olyan UDF az SQL-ben, amely egy táblázatot ad vissza. Ez a lekérdezés létrehoz egy forrástábla, amelyet az adatfolyamatában használhat. A lekérdezések használata nagyszerű lehetőséget nyújt a sorok tesztelésre vagy keresésekre való csökkentésére is. 

* SQL-példa: ```Select * from MyTable where customerId > 1000 and customerId < 2000```

**Köteg mérete**: adjon meg egy batch-méretet, amely nagy mennyiségű adatokat olvas be.

**Elkülönítési szint**: a leképezési adatfolyamban található SQL-források alapértelmezett értéke nem véglegesítve. A következő értékek egyikére módosíthatja az elkülönítési szintet:
* Olvasás véglegesítve
* Nem véglegesített olvasás
* Ismételhető olvasás
* Szerializálható
* Nincs (elkülönítési szint figyelmen kívül hagyása)

![Elkülönítési szint](media/data-flow/isolationlevel.png "Elkülönítési szint")

### <a name="sink-transformation"></a>Fogadó transzformáció

Az Azure szinapszis Analytics szolgáltatáshoz tartozó beállítások a fogadó transzformáció **Beállítások** lapján érhetők el.

**Frissítési módszer:** Meghatározza, hogy mely műveletek engedélyezettek az adatbázis célhelyén. Az alapértelmezett érték a beszúrások engedélyezése. Sorok frissítéséhez, upsert vagy törléséhez módosítani kell az Alter-Row transzformációt a műveletek sorainak címkézéséhez. A frissítések, upsert és törlések esetében meg kell adni a kulcs oszlopát vagy oszlopait annak meghatározásához, hogy melyik sort kell megváltoztatni.

**Tábla művelete:** Meghatározza, hogy a rendszer az összes sort újra létrehozza vagy eltávolítja a célhelyről az írás előtt.
* Nincs: a rendszer nem hajt végre műveletet a táblán.
* Újból létrehozva: a tábla eldobása és újbóli létrehozása megtörténik. Új tábla dinamikus létrehozásakor szükséges.
* Csonkítás: a céltábla összes sora el lesz távolítva.

**Előkészítés engedélyezése:** Meghatározza, hogy az Azure szinapszis Analytics szolgáltatásba való íráskor kell-e használni a [viszonyítási alapot](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide?view=sql-server-ver15)

**Köteg mérete**: azt határozza meg, hogy hány sort kell megírni az egyes gyűjtők. A nagyobb méretű kötegek növelik a tömörítési és a memória-optimalizálást, de a gyorsítótárban tárolt adatmennyiség miatt kifogytak a memória

**SQL-parancsfájlok előtti és utáni**műveletek: Itt adhatja meg azokat a TÖBBsoros SQL-parancsfájlokat, amelyek a (z) előtti (előfeldolgozási) és a (feldolgozás utáni) adatainak a fogadó adatbázisba való beírásakor lesznek végrehajtva

![SQL-feldolgozási parancsfájlok előzetes és utáni feldolgozása](media/data-flow/prepost1.png "SQL-feldolgozási parancsfájlok")

## <a name="data-type-mapping-for-azure-synapse-analytics"></a>Adattípusok leképezése az Azure szinapszis Analytics szolgáltatáshoz

Amikor a vagy az Azure szinapszis Analytics szolgáltatásba másol adatait, a következő leképezéseket használja az Azure szinapszis Analytics adattípusokból Azure Data Factory átmeneti adattípusokra. Lásd: [séma-és adattípus-leképezések](copy-activity-schema-and-type-mapping.md) , amelyből megtudhatja, hogy a másolási tevékenység hogyan képezi le a forrás sémáját és az adattípust

>[!TIP]
>Az SQL DW által támogatott adattípusok és a nem támogatott megoldások esetében tekintse meg a [táblázat adattípusait az Azure szinapszis Analytics-](../sql-data-warehouse/sql-data-warehouse-tables-data-types.md) cikkben.

| Az Azure szinapszis Analytics adattípusa    | Data Factory közbenső adattípus |
| :------------------------------------ | :----------------------------- |
| bigint                                | Int64                          |
| binary                                | Byte[]                         |
| bit                                   | Logikai                        |
| char                                  | String, Char[]                 |
| dátum                                  | DateTime                       |
| Dátum és idő                              | DateTime                       |
| datetime2                             | DateTime                       |
| Datetimeoffset                        | DateTimeOffset                 |
| tizedes tört                               | tizedes tört                        |
| FILESTREAM attribute (varbinary(max)) | Byte[]                         |
| Float                                 | Dupla                         |
| image                                 | Byte[]                         |
| int                                   | Int32                          |
| money                                 | tizedes tört                        |
| nchar                                 | String, Char[]                 |
| numeric                               | tizedes tört                        |
| nvarchar                              | String, Char[]                 |
| real                                  | Single                         |
| rowversion                            | Byte[]                         |
| smalldatetime                         | DateTime                       |
| smallint                              | Int16                          |
| smallmoney                            | tizedes tört                        |
| time                                  | Időtartam                       |
| tinyint                               | Bájt                           |
| uniqueidentifier                      | Guid                           |
| varbinary                             | Byte[]                         |
| varchar                               | String, Char[]                 |

## <a name="next-steps"></a>Következő lépések
A forrásként és fogadóként támogatott adattárak listáját a Azure Data Factory másolási tevékenysége című részben tekintheti meg a [támogatott adattárak és-formátumok](copy-activity-overview.md#supported-data-stores-and-formats)című témakörben.
