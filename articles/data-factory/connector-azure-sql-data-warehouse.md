---
title: Adatok másolása, és az Azure SQL Data Warehouse-ból az Azure Data Factory használatával |} A Microsoft Docs
description: Ismerje meg, hogyan másolhat adatokat az Azure SQL Data Warehouse támogatott forrás áruházakból származó, vagy az SQL Data Warehouse támogatott fogadó-áruházak Data Factory használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: jingwang
ms.openlocfilehash: 29f5b9b704bcf4648e9c24516d8eff5429a0ce1d
ms.sourcegitcommit: a819209a7c293078ff5377dee266fa76fd20902c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/16/2019
ms.locfileid: "71009954"
---
# <a name="copy-data-to-or-from-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Adatok másolása, vagy az Azure SQL Data Warehouse-ból az Azure Data Factory használatával 
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [Version1](v1/data-factory-azure-sql-data-warehouse-connector.md)
> * [Aktuális verzió](connector-azure-sql-data-warehouse.md)

Ez a cikk az adatok Azure SQL Data Warehouseba való másolásának módját ismerteti. Azure Data Factory szolgáltatásról, olvassa el a [bevezető cikk](introduction.md).

## <a name="supported-capabilities"></a>Támogatott képességek

Ez az Azure SQL Data Warehouse-összekötő a következő tevékenységek esetén támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrix-](copy-activity-overview.md) táblázattal
- [Adatfolyam hozzárendelése](concepts-data-flow-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)

Pontosabban az Azure SQL Data Warehouse-összekötő támogatja ezeket a funkciókat:

- Adatok másolása az Azure-erőforrások egy egyszerű vagy felügyelt szolgáltatásidentitások SQL-hitelesítés és Azure Active Directory (Azure AD) alkalmazástoken-hitelesítésének használata által.
- Forrásként adatok lekérdezése egy SQL-lekérdezést vagy tárolt eljárás használatával.
- Fogadóként az adatok betöltése a PolyBase vagy a tömeges beszúrás. A PolyBase a jobb másolási teljesítmény érdekében javasoljuk.

> [!IMPORTANT]
> Az Azure Data Factory integrációs modul adatokat másol, konfiguráljon egy [Azure SQL server tűzfal](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) úgy, hogy az Azure-szolgáltatásokhoz férhet hozzá a kiszolgálón.
> Ha adatokat másolja egy saját üzemeltetésű integrációs modul használatával, konfigurálja az Azure SQL-kiszolgáló tűzfalán, hogy a megfelelő IP-címtartomány. A tartományba beletartozik az Azure SQL adatbázishoz való csatlakozáshoz használja a gép IP-Címmel.

## <a name="get-started"></a>Bevezetés

> [!TIP]
> A legjobb teljesítmény érdekében, az adatok betöltése az Azure SQL Data Warehouse a polybase szolgáltatást akkor használja. A [bA a PolyBase használatával az adatok betöltése az Azure SQL Data Warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse) szakasz részletesen. A használati esetek, olvassa [1 TB adat betöltése az Azure SQL Data Warehouse-bA az Azure Data Factoryvel 15 perc alatt](load-azure-sql-data-warehouse.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Az alábbi szakaszok nyújtanak, amelyek meghatározzák az adott Data Factory-entitások egy Azure SQL Data Warehouse-összekötő-tulajdonságokkal kapcsolatos részletekért.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Egy Azure SQL Data Warehouse társított szolgáltatást a következő tulajdonságok támogatottak:

| Tulajdonság            | Leírás                                                  | Szükséges                                                     |
| :------------------ | :----------------------------------------------------------- | :----------------------------------------------------------- |
| type                | A type tulajdonságot állítsa **AzureSqlDW**.             | Igen                                                          |
| connectionString    | Adja meg a az Azure SQL Data Warehouse-példányhoz való csatlakozáshoz szükséges információkat a **connectionString** tulajdonság. <br/>A mező megjelölése SecureString, hogy biztonságosan tárolja Data Factoryban. A jelszó/egyszerű szolgáltatásnév kulcsát a Azure Key Vaultban is elhelyezheti, és ha az SQL-hitelesítése lekéri a `password` konfigurációt a kapcsolatok karakterláncán kívül. További részletekért tekintse meg a táblázat alatti JSON-példát, és [tárolja a hitelesítő adatokat Azure Key Vault](store-credentials-in-key-vault.md) cikkben. | Igen                                                          |
| servicePrincipalId  | Adja meg az alkalmazás ügyfél-azonosítót.                         | Igen, egy egyszerű szolgáltatást az Azure AD-hitelesítés használata esetén. |
| servicePrincipalKey | Adja meg az alkalmazáskulcsot. Ez a mező megjelölése tárolja biztonságos helyen a Data Factory, a SecureString vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). | Igen, egy egyszerű szolgáltatást az Azure AD-hitelesítés használata esetén. |
| tenant              | Adja meg a bérlő információkat (tartomány neve vagy a bérlő azonosítója) alatt az alkalmazás található. Kérheti a viszi az egérmutatót az Azure portal jobb felső sarkában. | Igen, egy egyszerű szolgáltatást az Azure AD-hitelesítés használata esetén. |
| connectVia          | A [integrációs modul](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. Használhatja az Azure integrációs modul vagy a saját üzemeltetésű integrációs modul (ha az adattár egy magánhálózaton található). Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. | Nem                                                           |

Különböző hitelesítési típus tekintse meg a következő szakaszok az Előfeltételek és a JSON-minták, illetve:

- [SQL-hitelesítés](#sql-authentication)
- Azure AD-alkalmazás-jogkivonat hitelesítése: [Egyszerű szolgáltatás](#service-principal-authentication)
- Azure AD-alkalmazás-jogkivonat hitelesítése: [Azure-erőforrások felügyelt identitásai](#managed-identity)

>[!TIP]
>Ha nyomja le az "UserErrorFailedToConnectToSqlServer", hibakód: Hiba történt, és üzenet például a "a munkamenet korlátot, az adatbázis XXX elérte.", és adja hozzá `Pooling=false` a kapcsolati karakterláncot, és próbálkozzon újra.

### <a name="sql-authentication"></a>SQL-hitelesítés

#### <a name="linked-service-example-that-uses-sql-authentication"></a>SQL-hitelesítést használó társított szolgáltatás példa

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
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
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            },
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

1. **[Az Azure Active Directory-alkalmazás létrehozása](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)**  az Azure Portalról. Jegyezze fel az alkalmazás nevét és a következő értékeket, amelyek meghatározzák a társított szolgáltatást:

    - Alkalmazásazonosító
    - Alkalmazáskulcs
    - Bérlőazonosító

2. **[Az Azure Active Directory-rendszergazda kiépítése](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)**  az Azure SQL Serverhez, az Azure Portalon, ha ezt még nem tette meg. Az Azure AD-rendszergazda lehet egy Azure AD-felhasználó vagy az Azure AD-csoporthoz. Ha a csoportot felügyelt identitással adja meg rendszergazdai szerepkörrel, ugorja át a 3. és a 4. lépést. A rendszergazda az adatbázis teljes hozzáféréssel fog rendelkezni.

3. **[Hozzon létre tartalmazottadatbázis-felhasználók](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)**  egyszerű szolgáltatás számára. A data warehouse-bA a, vagy amely adatok másolása az Azure AD identitás, amelynek legalább az ssms-ben, más eszközök használatával szeretné bármely felhasználó ALTER engedéllyel. Futtassa a következő T-SQL:
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. **Adja meg a szükséges engedélyekkel a szolgáltatásnév** szokásos módon SQL-felhasználók vagy mások számára. Futtassa a következő kódot, vagy tekintse meg a [további lehetőségeket.](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017) Ha az adatok betöltéséhez szeretné használni a Base-t, olvassa el a [szükséges adatbázis-engedélyt](#required-database-permission).

    ```sql
    EXEC sp_addrolemember db_owner, [your application name];
    ```

5. **Egy Azure SQL Data Warehouse társított szolgáltatás konfigurálása** az Azure Data Factoryban.


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Társított szolgáltatás példa, amely használja az egyszerű szolgáltatásnév hitelesítése

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
            },
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

### <a name="managed-identity"></a> Felügyelt identitások Azure-erőforrások hitelesítéshez

Adat-előállító társítható egy [-identitás az Azure-erőforrások](data-factory-service-identity.md) , amely az adott előállító jelöli. Ezt a felügyelt identitást Azure SQL Data Warehouse hitelesítéshez használhatja. Férhet hozzá a kijelölt gyári, és a, vagy az adatok másolása az adatraktár-Ez az identitás használatával.

A felügyelt identitásos hitelesítés használatához kövesse az alábbi lépéseket:

1. **[Az Azure Active Directory-rendszergazda kiépítése](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)**  az Azure SQL Serverhez, az Azure Portalon, ha ezt még nem tette meg. Az Azure AD-rendszergazda lehet egy Azure AD-felhasználó vagy az Azure AD-csoporthoz. Ha a csoportot felügyelt identitással adja meg rendszergazdai szerepkörrel, ugorja át a 3. és a 4. lépést. A rendszergazda az adatbázis teljes hozzáféréssel fog rendelkezni.

2. **[Hozzon létre tárolt adatbázis-felhasználókat](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)** a Data Factory felügyelt identitáshoz. A data warehouse-bA a, vagy amely adatok másolása az Azure AD identitás, amelynek legalább az ssms-ben, más eszközök használatával szeretné bármely felhasználó ALTER engedéllyel. Futtassa a következő T-SQL. 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. **Adja meg a Data Factory felügyelt identitáshoz szükséges engedélyeket** , ahogyan az SQL-felhasználók és mások számára általában nem. Futtassa a következő kódot, vagy tekintse meg a [további lehetőségeket.](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017) Ha az adatok betöltéséhez szeretné használni a Base-t, olvassa el a [szükséges adatbázis-engedélyt](#required-database-permission).

    ```sql
    EXEC sp_addrolemember db_owner, [your Data Factory name];
    ```

5. **Egy Azure SQL Data Warehouse társított szolgáltatás konfigurálása** az Azure Data Factoryban.

**Példa:**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
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

Szakaszok és adatkészletek definiálását tulajdonságainak teljes listáját lásd: a [adatkészletek](concepts-datasets-linked-services.md) cikk. Ez a szakasz az Azure SQL Data Warehouse adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Az adatok Azure SQL Data Warehouseba való másolásához a következő tulajdonságok támogatottak:

| Tulajdonság  | Leírás                                                  | Szükséges                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| type      | A **típus** értékre kell állítani a tulajdonságot az adatkészlet **AzureSqlDWTable**. | Igen                         |
| schema | A séma neve. |Nincs forrás, a fogadó Igen  |
| table | A tábla vagy nézet neve. |Nincs forrás, a fogadó Igen  |
| tableName | A tábla/nézet neve a sémával. Ez a tulajdonság visszamenőleges kompatibilitás esetén támogatott. Az új számítási feladatokhoz `table`használja `schema` a és a elemet. | Nincs forrás, a fogadó Igen |

#### <a name="dataset-properties-example"></a>Adatkészlet tulajdonságai példa

```json
{
    "name": "AzureSQLDWDataset",
    "properties":
    {
        "type": "AzureSqlDWTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Data Warehouse linked service name>",
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

Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz a forrás Azure SQL Data Warehouse és a fogadó által támogatott tulajdonságok listáját tartalmazza.

### <a name="azure-sql-data-warehouse-as-the-source"></a>Az Azure SQL Data Warehouse forrása

Adatok másolása az Azure SQL Data Warehouse, állítsa be a **típus** tulajdonság, a másolási tevékenység forrás **SqlDWSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakaszban:

| Tulajdonság                     | Leírás                                                  | Szükséges |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| type                         | A **típus** értékre kell állítani a másolási tevékenység forrás tulajdonság **SqlDWSource**. | Igen      |
| sqlReaderQuery               | Az egyéni SQL-lekérdezés segítségével olvassa el az adatokat. Példa: `select * from MyTable`. | Nem       |
| sqlReaderStoredProcedureName | A tárolt eljárást, amely adatokat olvas be a forrás-tábla neve. Az utolsó SQL-utasítást a tárolt eljárás a SELECT utasítással kell lennie. | Nem       |
| storedProcedureParameters    | A tárolt eljárás paraméterei.<br/>Megengedett értékek: neve vagy értéke párokat. Nevek és a kis-és a paraméterek meg kell egyeznie a neveket és a kis-és nagybetűhasználatot, a tárolt eljárás paraméterértékeinek. | Nem       |

### <a name="points-to-note"></a>Tudnivalók

- Ha a **sqlReaderQuery** van megadva a **SqlSource**, a másolási tevékenység a lekérdezés fut az Azure SQL Data Warehouse forrás, az adatok beolvasásához. Vagy megadhat egy tárolt eljárást. Adja meg a **sqlReaderStoredProcedureName** és **storedProcedureParameters** Ha paramétereket fogadja tárolt eljárást.
- Ha nem adja meg vagy **sqlReaderQuery** vagy **sqlReaderStoredProcedureName**, az oszlopok definiálva a **struktúra** az adatkészlet JSON szakaszában is használható a lekérdezés hozhatnak létre. `select column1, column2 from mytable` Azure SQL Data Warehouse fut. Ha az adatkészlet definíciója nem rendelkezik a **struktúra**, az összes oszlop ki van jelölve, a táblából.

#### <a name="sql-query-example"></a>SQL-lekérdezési példa

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

#### <a name="stored-procedure-example"></a>Tárolt eljárás példa

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

### <a name="stored-procedure-definition"></a>Tárolt eljárás definíciója

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

### <a name="azure-sql-data-warehouse-as-sink"></a> Az Azure SQL Data Warehouse pedig a fogadó

Adatok másolása az Azure SQL Data Warehouse, állítsa a fogadó típusa másolási tevékenység **SqlDWSink**. A következő tulajdonságok támogatottak a másolási tevékenység **fogadó** szakaszban:

| Tulajdonság          | Leírás                                                  | Szükséges                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| type              | A **típus** értékre kell állítani a másolási tevékenység fogadó tulajdonságát **SqlDWSink**. | Igen                                           |
| allowPolyBase     | Azt jelzi, hogy a PolyBase, ha lehetséges, használja a BULKINSERT mechanizmus helyett. <br/><br/> Azt javasoljuk, hogy Ön adatok betöltése az SQL Data Warehouse-bA a PolyBase használatával. Tekintse meg a [bA a PolyBase használatával az adatok betöltése az Azure SQL Data Warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse) korlátozások és a részletek a következő szakaszban.<br/><br/>Engedélyezett értékek a következők **igaz** és **hamis** (alapértelmezett). | Nem                                            |
| polyBaseSettings  | Egy csoport tulajdonságok is lehet megadni, ha a **allowPolybase** tulajdonsága **igaz**. | Nem                                            |
| rejectValue       | Megadja a szám vagy százalékos aránya, amelyek is vissza kell utasítani, mielőtt a lekérdezés nem sikerült sorokat.<br/><br/>További információ a PolyBase visszautasítási lehetőségeit argumentumok szakaszában [CREATE EXTERNAL TABLE (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx). <br/><br/>Megengedett értékek: 0 (alapértelmezett), 1, 2, stb. | Nem                                            |
| rejectType        | Megadja, hogy a **rejectValue** akkor Szövegkonstansérték vagy százalékos.<br/><br/>Engedélyezett értékek a következők **érték** (alapértelmezett), és **százalékos**. | Nem                                            |
| rejectSampleValue | Mielőtt PolyBase újraszámítja a visszautasított sorok aránya beolvasandó sorok számát határozza meg.<br/><br/>Megengedett értékek: 1, 2, stb. | Igen, ha a **rejectType** van **százalékos**. |
| useTypeDefault    | Itt adhatja meg, hogyan szeretné kezelni a PolyBase kér le adatokat a szövegfájl elválasztójellel tagolt szöveges fájlok a hiányzó értékeket.<br/><br/>További tudnivalók a ezt a tulajdonságot a következő argumentumok szakaszában [CREATE EXTERNAL FILE FORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx).<br/><br/>Engedélyezett értékek a következők **igaz** és **hamis** (alapértelmezett).<br><br> | Nem                                            |
| writeBatchSize    | A **kötegekben**az SQL-táblába beillesztett sorok száma. Érvényes, csak ha a PolyBase nem használja.<br/><br/>Az engedélyezett érték **egész** (sorok száma). Alapértelmezés szerint a Data Factory dinamikusan meghatározza a megfelelő batch-méretet a sor mérete alapján. | Nem                                            |
| writeBatchTimeout | Várjon, amíg a kötegelt insert művelet befejezését, mielőtt azt az időkorlátot. Érvényes, csak ha a PolyBase nem használja.<br/><br/>Az engedélyezett érték **timespan**. Példa: "00:30:00" (30 perc). | Nem                                            |
| preCopyScript     | Adja meg a másolási tevékenység futtatása előtt az adatok Azure SQL Data Warehouse-bA írt minden egyes futtatásához egy SQL-lekérdezést. Ez a tulajdonság használatával az előre betöltött adatokat. | Nem                                            |
| tableOption | Meghatározza, hogy a rendszer automatikusan létrehozza-e a fogadó táblát, ha az nem létezik a forrásoldali séma alapján. Az automatikus tábla létrehozása nem támogatott, ha a szakaszos másolás a másolási tevékenységben van konfigurálva. Az engedélyezett értékek a `none` következők: (alapértelmezett `autoCreate`),. |Nem |
| disableMetricsCollection | A Data Factory olyan mérőszámokat gyűjt, mint például a SQL Data Warehouse DWU a másolási teljesítmény optimalizálása és a javaslatok tekintetében. Ha ezt a viselkedést érinti, a kikapcsolásához válassza `true` a következőt:. | Nem (alapértelmezett `false`érték) |

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

További információ a PolyBase használatával hatékonyan betöltése az SQL Data Warehouse a következő szakaszban.

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>Adatok betöltése az Azure SQL Data Warehouse a PolyBase használatával

Használatával [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) betöltés nagy mennyiségű adat Azure SQL Data Warehouse-ba, a nagy átviteli sebességű hatékony módja. Átviteli sebesség nagyobb nyereség láthatja az alapértelmezett BULKINSERT mechanizmus helyett a PolyBase használatával. A használati esetek, olvassa [1 TB adat betöltése az Azure SQL Data Warehouse-bA](v1/data-factory-load-sql-data-warehouse.md).

* Ha a forrásadatok az **Azure blobban vannak, Azure Data Lake Storage Gen1 vagy Azure Data Lake Storage Gen2**, és a **formátum a Base-kompatibilis**, a másolási tevékenység használatával közvetlenül is meghívhatja a albaseot, hogy Azure SQL Data Warehouse lekérje az adatok forrásból való lekérését. További információkért lásd:  **[közvetlen másolása a PolyBase használatával](#direct-copy-by-using-polybase)** .
* A forrásadattár és formátum a PolyBase által eredetileg nem támogatott, ha a **[szakaszos Másolás a PolyBase használatával](#staged-copy-by-using-polybase)** inkább funkciót. A szakaszos másolás funkciót is, nagyobb átviteli sebességet biztosít. A PolyBase-kompatibilis formátumra alakítja az adatokat automatikusan átalakítja. És az Azure Blob storage-ban tárolja az adatokat. Majd betölti az adatokat az SQL Data Warehouse-bA.

>[!TIP]
>További információ a [Base használatának ajánlott eljárásairól](#best-practices-for-using-polybase).

### <a name="direct-copy-by-using-polybase"></a>Közvetlen másolás a PolyBase használatával

SQL Data Warehouse a közvetlenül támogatja az Azure blobot, Azure Data Lake Storage Gen1 és Azure Data Lake Storage Gen2. Ha a forrásadatok megfelelnek az ebben a szakaszban ismertetett feltételeknek, használja a következőt a forrás adattárból közvetlenül a Azure SQL Data Warehouseba való másoláshoz. Ellenkező esetben használjon [szakaszos Másolás a PolyBase használatával](#staged-copy-by-using-polybase).

> [!TIP]
> Ha az adatok hatékony másolását SQL Data Warehousera szeretné elvégezni, további információt a [Azure Data Factory még könnyebbé és kényelmesebben is megtudhatja, hogy a Data Lake Store SQL Data Warehouse használatával hogyan lehet az adatokból információkat feltárni](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/).

A követelmények nem teljesülnek, ha az Azure Data Factory ellenőrzi a beállításokat, és automatikusan visszavált az adatok áthelyezése a BULKINSERT mechanizmusa.

1. A **forráshoz társított szolgáltatás** a következő típusokkal és hitelesítési módszerekkel rendelkezik:

    | Támogatott forrásoldali adattár típusa                             | Támogatott forrás-hitelesítési típus                        |
    | :----------------------------------------------------------- | :---------------------------------------------------------- |
    | [Azure Blob](connector-azure-blob-storage.md)                | Fiók kulcsának hitelesítése, felügyelt identitások hitelesítése |
    | [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | Egyszerű szolgáltatásnév hitelesítése                            |
    | [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | Fiók kulcsának hitelesítése, felügyelt identitások hitelesítése |

    >[!IMPORTANT]
    >Ha az Azure Storage VNet szolgáltatás-végponttal van konfigurálva, akkor felügyelt identitás-hitelesítést kell használnia – a [VNet szolgáltatás-végpontok Azure Storage-ban való használatának következményeire](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage)utal. Ismerje meg az [Azure Blob által felügyelt identitás-hitelesítés](connector-azure-blob-storage.md#managed-identity) és a [Azure Data Lake Storage Gen2 által felügyelt identitás-hitelesítési](connector-azure-data-lake-storage.md#managed-identity) szakasz Data Factory szükséges konfigurációit.

2. A **forrásadatok formátuma** a következő konfigurációkkal rendelkező **parketta**, **ork**vagy **tagolt szöveg**:

   1. A mappa elérési útja nem tartalmaz helyettesítő szűrőt.
   2. A fájl neve üres, vagy egyetlen fájlra mutat. Ha a helyettesítő fájl nevét adja meg a másolási tevékenységben, az `*` csak `*.*`a vagy a lehet.
   3. `rowDelimiter`**alapértelmezett**, **\n**, **\r\n**vagy **\r**.
   4. `nullValue`Alapértelmezés szerint marad, vagy **üres karakterláncra** ("") van állítva, `treatEmptyAsNull` és az alapértelmezett érték, vagy igaz értékre van állítva.
   5. `encodingName`Alapértelmezés szerint marad, vagy az **UTF-8**értékre van állítva.
   6. `quoteChar`, `escapeChar` és`skipLineCount` nincs megadva. A albase-támogatás kihagyása `firstRowAsHeader` a fejlécsorból, amely az ADF-ben konfigurálható.
   7. `compression` lehet **tömörítés nélküli**, **GZip**, vagy **Deflate**.

3. Ha a forrás mappa, `recursive` a másolási tevékenységben igaz értékre kell állítani.

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
                    "type": "AzureBlobStorageReadSetting",
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

Ha a forrásadatok nem felel meg az előző szakaszban, lehetővé teszik az adatok másolása egy átmeneti előkészítési Azure Blob storage példány keresztül. Az Azure Premium Storage nem lehet. Ebben az esetben az Azure Data Factory automatikusan futtatja átalakítások az adatok PolyBase adatok formátuma követelményeinek kielégítése érdekében. Ezután használja a PolyBase adatok betöltése az SQL Data Warehouse-bA. Végül, megtisztítja az ideiglenes adatokat a blob storage-ból. Lásd: [szakaszos Másolás](copy-activity-performance.md#staged-copy) részletes másolhat adatokat egy előkészítési Azure Blob storage-példány használatával.

A szolgáltatás használatához hozzon létre egy [azure blob Storage társított szolgáltatást](connector-azure-blob-storage.md#linked-service-properties) , amely az Azure Storage-fiókra hivatkozik az ideiglenes blob Storage-tárolóval. Ezután adja meg `enableStaging` a `stagingSettings` és a tulajdonságokat a másolási tevékenységhez, ahogy az a következő kódban is látható.

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

## <a name="best-practices-for-using-polybase"></a>Ajánlott eljárások a polybase használatához

Az alábbi szakaszok azokat az említett mellett ajánlott eljárásokat [ajánlott eljárások az Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-best-practices.md).

### <a name="required-database-permission"></a>Adatbázishoz szükséges engedélyt

A PolyBase használatához rendelkeznie kell a felhasználót, hogy adatokat tölt be az SQL Data Warehouse ["CONTROL" engedély](https://msdn.microsoft.com/library/ms191291.aspx) a céladatbázison. Ennek érdekében javítottunk egyik módja az, hogy adja hozzá a felhasználót, amelynek a **db_owner** szerepkör. Ismerje meg, hogyan valósítható meg a [SQL Data Warehouse áttekintése](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

### <a name="row-size-and-data-type-limits"></a>Sor mérete és az adatok írja be a korlátok

A PolyBase-betöltések pedig csak 1 MB-nál kisebb méretű sorokat. Nem használható a VARCHR (MAX), a NVARCHAR (MAX) vagy a VARBINARY (MAX) való betöltéshez. További információkért lásd: [SQL Data Warehouse szolgáltatás kapacitáskorlátait](../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Ha a forrásadatok 1 MB-nál nagyobb sorral rendelkezik, érdemes a forrástáblákhoz függőlegesen felosztása több kis azokat. Győződjön meg arról, hogy az egyes sorok legnagyobb mérete nem haladja meg a határértéket. A kisebb méretű táblák ezután betöltése a PolyBase használatával, és az Azure SQL Data Warehouse egyesíti.

Azt is megteheti, hogy az ilyen széles oszlopokkal rendelkező adatok esetében nem hozhatók be az adatok az ADF használatával, ha kikapcsolja az "alapszintű" beállítást.

### <a name="sql-data-warehouse-resource-class"></a>Az SQL Data Warehouse erőforrásosztály

A felhasználót, hogy adatokat tölt be az SQL Data Warehouse polybase nagyobb erőforrásosztályt hozzárendelni a legjobb teljesítmény elérése érdekében.

### <a name="polybase-troubleshooting"></a>Alapszintű hibaelhárítás

**Betöltés decimális oszlopba**

Ha a forrásadatok szöveges formátumban vagy más nem a-alapú kompatibilis tárolókban találhatók (a szakaszos másolással és a paritással), és üres értéket tartalmaz SQL Data Warehouse decimális oszlopba való betöltéshez, a következő hibaüzenet jelenhet meg:

```
ErrorCode=FailedDbOperation, ......HadoopSqlException: Error converting data type VARCHAR to DECIMAL.....Detailed Message=Empty string can't be converted to DECIMAL.....
```

A megoldás az "**alapértelmezett típus használata**" beállítás kijelölésének feloldása (hamis) a másolási tevékenység fogadója – > a bázisterület alapbeállításai. A["](https://docs.microsoft.com/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest#arguments
)USE_TYPE_DEFAULT" egy alapszintű natív konfiguráció, amely meghatározza, hogy a rendszer hogyan kezelje a hiányzó értékeket a tagolt szövegfájlokban, amikor a viszonyítási adatok beolvasása a szövegfájlból történik. 

**`tableName`Azure SQL Data Warehouse**

Az alábbi táblázat példákat mutat be, hogyan adhat meg a **tableName** JSON adatkészlet tulajdonság. Séma és a táblázat neve különböző kombinációit jeleníti meg.

| Adatbázis-séma | Tábla neve | **tableName** JSON-tulajdonság               |
| --------- | ---------- | ----------------------------------------- |
| dbo       | MyTable    | MyTable vagy dbo.MyTable vagy [dbo].[MyTable] |
| dbo1      | MyTable    | dbo1.MyTable vagy [dbo1].[MyTable]          |
| dbo       | My.Table   | [My.Table] vagy [dbo].[My.Table]            |
| dbo1      | My.Table   | [dbo1].[My.Table]                         |

A következő hibát látja, ha a probléma lehet, hogy a megadott érték a **tableName** tulajdonság. Lásd az előző táblázatban adja meg az értékeket a megfelelő módszer az **tableName** JSON-tulajdonságot.

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

**Alapértelmezett értékeket tartalmazó oszlopok**

A PolyBase szolgáltatás, a Data Factory jelenleg csak az azonos számú oszlopot, mint a céltábla fogad el. Ilyen például, ahol az egyiket van definiálva az alapértelmezett érték négy oszlopot tartalmazó táblát. A bemeneti adatok továbbra is rendelkeznie kell négy oszlopot. Három oszlop a bemeneti adatkészlet az alábbihoz hasonló hibaüzenetet eredményez:

```
All columns of the table must be specified in the INSERT BULK statement.
```

A NULL érték az alapértelmezett érték formája. Ha az oszlop nullázható, a bemeneti adatokat az a blob az adott oszlop lehet üres. De hiányzik a bemeneti adatkészlet a nem lehet. PolyBase az Azure SQL Data Warehouse a hiányzó értékeket szúr be NULL.

## <a name="mapping-data-flow-properties"></a>Adatfolyam-tulajdonságok leképezése

A forrás- [átalakítás](data-flow-source.md) és a fogadó [átalakítás](data-flow-sink.md) részleteinek megismerése a leképezési folyamatokban.

## <a name="data-type-mapping-for-azure-sql-data-warehouse"></a>Az Azure SQL Data Warehouse adattípus-leképezés

Másolt adatok vagy az Azure SQL Data Warehouse, a következő hozzárendeléseket használják az Azure SQL Data Warehouse adattípusok Azure Data Factory-közbenső adattípusok. Lásd: [séma és adatok írja be a hozzárendelések](copy-activity-schema-and-type-mapping.md) megtudhatja, hogyan másolási tevékenység leképezi a forrás séma és adatok típusa a fogadó.

>[!TIP]
>Tekintse meg az SQL DW által támogatott adattípusok és a nem támogatott megoldások [Azure SQL Data Warehouse cikkében található táblázat adattípusait](../sql-data-warehouse/sql-data-warehouse-tables-data-types.md) .

| Az Azure SQL Data Warehouse-adattípus    | Data Factory közbenső adattípus |
| :------------------------------------ | :----------------------------- |
| bigint                                | Int64                          |
| binary                                | Byte[]                         |
| bit                                   | Boolean                        |
| char                                  | String, Char[]                 |
| date                                  | DateTime                       |
| Datetime                              | DateTime                       |
| datetime2                             | DateTime                       |
| Datetimeoffset                        | DateTimeOffset                 |
| Decimal                               | Decimal                        |
| FILESTREAM attribute (varbinary(max)) | Byte[]                         |
| Float                                 | Double                         |
| image                                 | Byte[]                         |
| int                                   | Int32                          |
| money                                 | Decimal                        |
| nchar                                 | String, Char[]                 |
| numeric                               | Decimal                        |
| nvarchar                              | String, Char[]                 |
| real                                  | Single                         |
| rowversion                            | Byte[]                         |
| smalldatetime                         | DateTime                       |
| smallint                              | Int16                          |
| smallmoney                            | Decimal                        |
| time                                  | TimeSpan                       |
| tinyint                               | Byte                           |
| uniqueidentifier                      | Guid                           |
| varbinary                             | Byte[]                         |
| varchar                               | String, Char[]                 |

## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [keresési tevékenységet](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>GetMetadata tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [GetMetaData tevékenységet](control-flow-get-metadata-activity.md) 

## <a name="next-steps"></a>További lépések
Az Azure Data Factory másolási tevékenység által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak és formátumok](copy-activity-overview.md##supported-data-stores-and-formats).
