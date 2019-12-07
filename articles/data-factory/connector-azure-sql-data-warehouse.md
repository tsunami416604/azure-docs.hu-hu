---
title: Adatmásolás és átalakítás Azure SQL Data Warehouse
description: Megtudhatja, hogyan másolhat adatok Azure SQL Data Warehouseba és onnan, és hogyan alakíthatja át a Azure SQL Data Warehouse adatait Data Factory használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: jingwang
ms.openlocfilehash: 1bd6d4d594bc7988d2dceaae533202f2a41379f1
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74891038"
---
# <a name="copy-and-transform-data-in-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Azure SQL Data Warehouse adatmásolása és átalakítása a Azure Data Factory használatával 
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [Version1](v1/data-factory-azure-sql-data-warehouse-connector.md)
> * [Aktuális verzió](connector-azure-sql-data-warehouse.md)

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok másolásához és a Azure SQL Data Warehouseához, valamint az adatfolyamatok használata az adatok Azure Data Lake Storage Gen2ban való átalakításához. A Azure Data Factoryről a [bevezető cikkben](introduction.md)olvashat bővebben.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez az Azure SQL Data Warehouse-összekötő a következő tevékenységek esetén támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrix-](copy-activity-overview.md) táblázattal
- [Adatfolyam hozzárendelése](concepts-data-flow-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)

Másolási tevékenység esetén ez az Azure SQL Data Warehouse-összekötő támogatja ezeket a funkciókat:

- Adatmásolás SQL-hitelesítéssel és Azure Active Directory (Azure AD) alkalmazás-jogkivonat hitelesítéssel az Azure-erőforrásokhoz tartozó egyszerű szolgáltatásnév vagy felügyelt identitás használatával.
- Forrásként egy SQL-lekérdezés vagy tárolt eljárás használatával kérhet le egy adatforrást.
- Fogadóként a betöltési adatok a következőkkel tölthetők be: albase vagy tömeges Beszúrás. Javasoljuk, hogy a jobb másolási teljesítmény érdekében a következőt ajánljuk:.

> [!IMPORTANT]
> Ha Azure Data Factory Integration Runtime használatával másol Adatmásolást, konfigurálja az [Azure SQL Server-tűzfalat](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) úgy, hogy az Azure-szolgáltatások hozzáférhessenek a kiszolgálóhoz.
> Ha saját üzemeltetésű integrációs modul használatával másol Adatmásolást, konfigurálja az Azure SQL Server-tűzfalat a megfelelő IP-címtartomány engedélyezéséhez. Ez a tartomány tartalmazza a számítógép IP-címét, amely a Azure SQL Databasehoz való kapcsolódáshoz használatos.

## <a name="get-started"></a>Az első lépések

> [!TIP]
> A legjobb teljesítmény érdekében az adatok Azure SQL Data Warehouseba való betöltéséhez használja a következőt: Base. Az [adatok Azure SQL Data Warehouse szakaszba való betöltéséhez használja](#use-polybase-to-load-data-into-azure-sql-data-warehouse) a következőt:. A használati eseteket bemutató bemutatóért lásd: [1 TB Betöltése Azure SQL Data Warehouse 15 perc alatt Azure Data Factory](load-azure-sql-data-warehouse.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük azokat a tulajdonságokat, amelyek Data Factory Azure SQL Data Warehouse-összekötőhöz tartozó entitásokat határoznak meg.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Azure SQL Data Warehouse társított szolgáltatás esetében a következő tulajdonságok támogatottak:

| Tulajdonság            | Leírás                                                  | Szükséges                                                     |
| :------------------ | :----------------------------------------------------------- | :----------------------------------------------------------- |
| type                | A Type tulajdonságot **AzureSqlDW**értékre kell beállítani.             | Igen                                                          |
| connectionString    | A **ConnectionString** tulajdonsághoz tartozó Azure SQL Data Warehouse-példányhoz való kapcsolódáshoz szükséges információk megadása. <br/>A mező megjelölése SecureString, hogy biztonságosan tárolja Data Factoryban. A jelszó/egyszerű szolgáltatásnév kulcsát a Azure Key Vaultban is elhelyezheti, és ha az SQL-hitelesítése lekéri a `password` konfigurációt a kapcsolatok karakterláncán kívülre. További részletekért tekintse meg a táblázat alatti JSON-példát, és [tárolja a hitelesítő adatokat Azure Key Vault](store-credentials-in-key-vault.md) cikkben. | Igen                                                          |
| servicePrincipalId  | Határozza meg az alkalmazás ügyfél-AZONOSÍTÓját.                         | Igen, ha Azure AD-hitelesítést használ egy egyszerű szolgáltatással. |
| servicePrincipalKey | Az alkalmazás kulcsának meghatározása. Megjelöli ezt a mezőt SecureString, hogy biztonságosan tárolja Data Factoryban, vagy [hivatkozjon a Azure Key Vault tárolt titkos kulcsra](store-credentials-in-key-vault.md). | Igen, ha Azure AD-hitelesítést használ egy egyszerű szolgáltatással. |
| Bérlő              | Adja meg a bérlői adatokat (tartománynevet vagy bérlői azonosítót), amely alatt az alkalmazás található. Lekérheti a Azure Portal jobb felső sarkában lévő egér fölé. | Igen, ha Azure AD-hitelesítést használ egy egyszerű szolgáltatással. |
| Connectvia tulajdonsággal          | Az adattárhoz való csatlakozáshoz használt [integrációs](concepts-integration-runtime.md) modul. Használhat Azure Integration Runtime vagy saját üzemeltetésű integrációs modult (ha az adattár egy magánhálózaton található). Ha nincs megadva, az alapértelmezett Azure Integration Runtime használja. | Nem                                                           |

Különböző hitelesítési típusok esetén tekintse át az előfeltételek és JSON-minták következő, az előfeltételeket és a JSON-mintákat ismertető szakaszt:

- [SQL-hitelesítés](#sql-authentication)
- Azure AD-alkalmazás-jogkivonat hitelesítése: [szolgáltatásnév](#service-principal-authentication)
- Azure AD-alkalmazás-jogkivonat hitelesítése: [felügyelt identitások az Azure-erőforrásokhoz](#managed-identity)

>[!TIP]
>Ha a hibakódot "UserErrorFailedToConnectToSqlServer"-ként találta, és a következőhöz hasonló üzenet jelenik meg: "az adatbázis munkamenet-korlátja XXX, és elérte a műveletet.", adja hozzá `Pooling=false` a kapcsolati karakterlánchoz, és próbálkozzon újra.

### <a name="sql-authentication"></a>SQL-hitelesítés

#### <a name="linked-service-example-that-uses-sql-authentication"></a>SQL-hitelesítést használó társított szolgáltatási példa

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

A szolgáltatás egyszerű Azure AD-alapú alkalmazás-jogkivonat-hitelesítésének használatához kövesse az alábbi lépéseket:

1. **[Hozzon létre egy Azure Active Directory alkalmazást](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)** a Azure Portal. Jegyezze fel az alkalmazás nevét és a társított szolgáltatást meghatározó következő értékeket:

    - Alkalmazásazonosító
    - Alkalmazás kulcsa
    - Bérlőazonosító

2. Ha még nem tette meg, hozzon **[létre egy Azure Active Directory rendszergazdát](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** az Azure SQL Server-kiszolgáló Azure Portal. Az Azure AD-rendszergazda lehet egy Azure AD-felhasználó vagy egy Azure AD-csoport. Ha a csoportot felügyelt identitással adja meg rendszergazdai szerepkörrel, ugorja át a 3. és a 4. lépést. A rendszergazdának teljes hozzáférése lesz az adatbázishoz.

3. **[Hozzon létre tárolt adatbázis-felhasználókat](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)** az egyszerű szolgáltatásnév számára. Kapcsolódjon az adattárházhoz a vagy a rendszerből, amelyről olyan eszközökkel szeretné másolni az adatait, mint a SSMS, egy olyan Azure AD-identitással, amely legalább bármilyen felhasználói engedély megváltoztatásával rendelkezik. Futtassa a következő T-SQL-T:
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. **Adja meg az egyszerű szolgáltatáshoz szükséges engedélyeket az** SQL-felhasználók vagy mások számára általában. Futtassa a következő kódot, vagy tekintse meg a [további lehetőségeket.](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017) Ha az adatok betöltéséhez szeretné használni a Base-t, olvassa el a [szükséges adatbázis-engedélyt](#required-database-permission).

    ```sql
    EXEC sp_addrolemember db_owner, [your application name];
    ```

5. **Azure SQL Data Warehouse társított szolgáltatás konfigurálása** Azure Data Factoryban.


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>A szolgáltatás egyszerű hitelesítését használó társított szolgáltatási példa

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

### <a name="managed-identity"></a>Felügyelt identitások az Azure-erőforrások hitelesítéséhez

Az adatok előállítója az adott gyárat képviselő [Azure-erőforrások felügyelt identitásával](data-factory-service-identity.md) társítható. Ezt a felügyelt identitást Azure SQL Data Warehouse hitelesítéshez használhatja. A kijelölt gyár ezen identitás használatával elérheti és átmásolhatja az adattárházból származó és onnan származó adatok adatait.

A felügyelt identitásos hitelesítés használatához kövesse az alábbi lépéseket:

1. Ha még nem tette meg, hozzon **[létre egy Azure Active Directory rendszergazdát](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** az Azure SQL Server-kiszolgáló Azure Portal. Az Azure AD-rendszergazda lehet egy Azure AD-felhasználó vagy egy Azure AD-csoport. Ha a csoportot felügyelt identitással adja meg rendszergazdai szerepkörrel, ugorja át a 3. és a 4. lépést. A rendszergazdának teljes hozzáférése lesz az adatbázishoz.

2. **[Hozzon létre tárolt adatbázis-felhasználókat](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)** a Data Factory felügyelt identitáshoz. Kapcsolódjon az adattárházhoz a vagy a rendszerből, amelyről olyan eszközökkel szeretné másolni az adatait, mint a SSMS, egy olyan Azure AD-identitással, amely legalább bármilyen felhasználói engedély megváltoztatásával rendelkezik. Futtassa az alábbi T-SQL-T. 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. **Adja meg a Data Factory felügyelt identitáshoz szükséges engedélyeket** , ahogyan az SQL-felhasználók és mások számára általában nem. Futtassa a következő kódot, vagy tekintse meg a [további lehetőségeket.](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017) Ha az adatok betöltéséhez szeretné használni a Base-t, olvassa el a [szükséges adatbázis-engedélyt](#required-database-permission).

    ```sql
    EXEC sp_addrolemember db_owner, [your Data Factory name];
    ```

5. **Azure SQL Data Warehouse társított szolgáltatás konfigurálása** Azure Data Factoryban.

**Példa**

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

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját az [adatkészletek](concepts-datasets-linked-services.md) című cikkben találja. 

Azure SQL Data Warehouse adatkészlet esetében a következő tulajdonságok támogatottak:

| Tulajdonság  | Leírás                                                  | Szükséges                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| type      | Az adatkészlet **Type** tulajdonságát **AzureSqlDWTable**értékre kell állítani. | Igen                         |
| séma | A séma neve. |Nem, forrás, igen, fogadó  |
| table | A tábla vagy nézet neve. |Nem, forrás, igen, fogadó  |
| tableName | A tábla/nézet neve a sémával. Ez a tulajdonság visszamenőleges kompatibilitás esetén támogatott. Az új számítási feladatokhoz használja a `schema` és a `table`. | Nem, forrás, igen, fogadó |

#### <a name="dataset-properties-example"></a>Adatkészlet tulajdonságai – példa

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

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját a [folyamatok](concepts-pipelines-activities.md) című cikkben találja. Ez a szakasz a Azure SQL Data Warehouse forrás és a fogadó által támogatott tulajdonságok listáját tartalmazza.

### <a name="azure-sql-data-warehouse-as-the-source"></a>Azure SQL Data Warehouse forrásként

Az adatok Azure SQL Data Warehouseból való másolásához a másolási tevékenység forrásában állítsa a **Type (típus** ) tulajdonságot **SqlDWSource**értékre. A másolási tevékenység **forrása** szakasz a következő tulajdonságokat támogatja:

| Tulajdonság                     | Leírás                                                  | Szükséges |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| type                         | A másolási tevékenység forrásának **Type** tulajdonságát **SqlDWSource**értékre kell állítani. | Igen      |
| sqlReaderQuery               | Az egyéni SQL-lekérdezés használatával olvassa be az adatolvasást. Példa: `select * from MyTable`. | Nem       |
| sqlReaderStoredProcedureName | Annak a tárolt eljárásnak a neve, amely beolvassa az adatokat a forrás táblából. Az utolsó SQL-utasításnak SELECT utasításnak kell lennie a tárolt eljárásban. | Nem       |
| storedProcedureParameters    | A tárolt eljárás paraméterei.<br/>Az engedélyezett értékek név vagy érték párok. A paraméterek nevének és burkolatának meg kell egyeznie a tárolt eljárás paramétereinek nevével és házával. | Nem       |

### <a name="points-to-note"></a>Megjegyzésre mutató pontok

- Ha a **sqlReaderQuery** meg van adva a **SqlSource**, a másolási tevékenység lefuttatja ezt a lekérdezést a Azure SQL Data Warehouse forráson az adatkéréshez. Vagy megadhat egy tárolt eljárást is. A **sqlReaderStoredProcedureName** és a **storedProcedureParameters** megadása, ha a tárolt eljárás paraméterekkel rendelkezik.
- Ha nem ad meg **sqlReaderQuery** vagy **sqlReaderStoredProcedureName**-t, a rendszer egy lekérdezés létrehozásához az adatkészlet JSON-fájljának **struktúra** szakaszában meghatározott oszlopokat használja. a `select column1, column2 from mytable` Azure SQL Data Warehouseon fut. Ha az adatkészlet definíciója nem rendelkezik a **struktúrával**, az összes oszlop ki lesz választva a táblából.

#### <a name="sql-query-example"></a>Példa SQL-lekérdezésre

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

#### <a name="stored-procedure-example"></a>Példa tárolt eljárásra

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

### <a name="azure-sql-data-warehouse-as-sink"></a>Azure SQL Data Warehouse fogadóként

Az adatAzure SQL Data Warehouseba való másoláshoz állítsa a fogadó típust a másolási tevékenység **SqlDWSink**. A másolási tevékenység fogadója szakasz a következő tulajdonságokat támogatja:

| Tulajdonság          | Leírás                                                  | Szükséges                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| type              | A másolási tevékenység fogadójának **Type** tulajdonságát **SqlDWSink**értékre kell állítani. | Igen                                           |
| allowPolyBase     | Azt jelzi, hogy a BULKINSERT mechanizmus helyett a következőt kell-e használni, ha van ilyen. <br/><br/> Azt javasoljuk, hogy az adatok a SQL Data Warehouseba való betöltését a Base használatával. A korlátozások és részletek érdekében tekintse meg a következő témakört: az adatok Azure SQL Data Warehouse szakaszba való [betöltésének használata](#use-polybase-to-load-data-into-azure-sql-data-warehouse) .<br/><br/>Az engedélyezett értékek: **true** és **false** (alapértelmezett). | Nem                                            |
| polyBaseSettings  | A tulajdonságok olyan csoportja, amely akkor adható meg, ha a **allowPolybase** tulajdonság értéke **true (igaz**). | Nem                                            |
| rejectValue       | A lekérdezés sikertelensége előtt visszautasítható sorok számát vagy százalékos arányát adja meg.<br/><br/>További információk a [create External Table (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx)argumentumok szakaszban található "a Base 's elutasítás" beállításairól. <br/><br/>Az engedélyezett értékek: 0 (alapértelmezett), 1, 2 stb. | Nem                                            |
| rejectType        | Megadja, hogy a **rejectValue** kapcsoló literális érték vagy százalék-e.<br/><br/>Az engedélyezett értékek **érték** (alapértelmezett) és **százalék**. | Nem                                            |
| rejectSampleValue | Meghatározza a lekérdezni kívánt sorok számát, mielőtt a rendszer újraszámítja az elutasított sorok százalékos arányát.<br/><br/>Az engedélyezett értékek: 1, 2 stb. | Igen, ha a **rejectType** **százaléka százalék**. |
| useTypeDefault    | Meghatározza, hogy a rendszer hogyan kezelje a hiányzó értékeket a tagolt szövegfájlokban, ha a viszonyítási adatok beolvasása a szövegfájlból történik.<br/><br/>Erről a tulajdonságról a [külső fájlformátum létrehozása (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx)argumentumai című szakaszban olvashat bővebben.<br/><br/>Az engedélyezett értékek: **true** és **false** (alapértelmezett).<br><br> | Nem                                            |
| writeBatchSize    | A **kötegekben**az SQL-táblába beillesztett sorok száma. Csak akkor érvényes, ha a rendszer nem használja a főadatbázist.<br/><br/>Az engedélyezett érték **egész szám** (sorok száma). Alapértelmezés szerint a Data Factory dinamikusan meghatározza a megfelelő batch-méretet a sor mérete alapján. | Nem                                            |
| writeBatchTimeout | Várakozási idő a kötegelt beszúrási művelet befejezéséhez az időtúllépés előtt. Csak akkor érvényes, ha a rendszer nem használja a főadatbázist.<br/><br/>Az engedélyezett érték a **TimeSpan**. Például: "00:30:00" (30 perc). | Nem                                            |
| preCopyScript     | Adja meg a másolási tevékenység futtatásához szükséges SQL-lekérdezést, mielőtt az egyes futtatások Azure SQL Data Warehousebe írna. Ezzel a tulajdonsággal törölheti az előre feltöltött adatkészleteket. | Nem                                            |
| tableOption | Meghatározza, hogy a rendszer automatikusan létrehozza-e a fogadó táblát, ha az nem létezik a forrásoldali séma alapján. Az automatikus tábla létrehozása nem támogatott, ha a szakaszos másolás a másolási tevékenységben van konfigurálva. Az engedélyezett értékek a következők: `none` (alapértelmezett), `autoCreate`. |Nem |
| disableMetricsCollection | A Data Factory olyan mérőszámokat gyűjt, mint például a SQL Data Warehouse DWU a másolási teljesítmény optimalizálása és a javaslatok tekintetében. Ha ezt a viselkedést érinti, a kikapcsolásához válassza a `true` lehetőséget. | Nem (az alapértelmezett érték `false`) |

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

További információ a SQL Data Warehouse a következő szakaszban való hatékony betöltéséhez használható a Base használatával.

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>Adatok betöltése a Azure SQL Data Warehouseba a Base használatával

A [Base](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) használatával nagy mennyiségű adat tölthető be Azure SQL Data Warehouse magas átviteli sebességgel. Az adatátviteli sebesség nagy mennyiségű nyereségét az alapértelmezett BULKINSERT mechanizmus helyett a albase használatával fogja látni. A használati esetekkel kapcsolatos bemutatóért lásd: [1 TB Betöltése Azure SQL Data Warehouseba](v1/data-factory-load-sql-data-warehouse.md).

* Ha a forrásadatok az **Azure blobban vannak, Azure Data Lake Storage Gen1 vagy Azure Data Lake Storage Gen2**, és a **formátum a Base-kompatibilis**, a másolási tevékenység használatával közvetlenül is meghívhatja a albaseot, hogy Azure SQL Data Warehouse lekérje az adatok forrásból való lekérését. Részletekért lásd: a **[közvetlen másolás a Base használatával](#direct-copy-by-using-polybase)** .
* Ha a forrás adattárat és a formátumot eredetileg nem a Base támogatja, használja a **[szakaszos másolást a kiindulási funkció használatával](#staged-copy-by-using-polybase)** . Az előkészített másolási funkció jobb átviteli sebességet is biztosít. A rendszer automatikusan konvertálja az adatok alap-kompatibilis formátumba. És tárolja az Azure Blob Storage-ban tárolt adattárakat. Ezután betölti az adatSQL Data Warehouseba.

>[!TIP]
>További információ a [Base használatának ajánlott eljárásairól](#best-practices-for-using-polybase).

### <a name="direct-copy-by-using-polybase"></a>Közvetlen másolás a Base használatával

SQL Data Warehouse a közvetlenül támogatja az Azure blobot, Azure Data Lake Storage Gen1 és Azure Data Lake Storage Gen2. Ha a forrásadatok megfelelnek az ebben a szakaszban ismertetett feltételeknek, használja a következőt a forrás adattárból közvetlenül a Azure SQL Data Warehouseba való másoláshoz. Ellenkező esetben használjon [a szakaszos másolást a Base használatával](#staged-copy-by-using-polybase).

> [!TIP]
> Ha az adatok hatékony másolását SQL Data Warehousera szeretné elvégezni, további információt a [Azure Data Factory még könnyebbé és kényelmesebben is megtudhatja, hogy a Data Lake Store SQL Data Warehouse használatával hogyan lehet az adatokból információkat feltárni](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/).

Ha a követelmények nem teljesülnek, Azure Data Factory ellenőrzi a beállításokat, és automatikusan visszakerül a BULKINSERT mechanizmusra az adatáthelyezéshez.

1. A **forráshoz társított szolgáltatás** a következő típusokkal és hitelesítési módszerekkel rendelkezik:

    | Támogatott forrásoldali adattár típusa                             | Támogatott forrás-hitelesítési típus                        |
    | :----------------------------------------------------------- | :---------------------------------------------------------- |
    | [Azure Blob](connector-azure-blob-storage.md)                | Fiók kulcsának hitelesítése, felügyelt identitások hitelesítése |
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

### <a name="staged-copy-by-using-polybase"></a>Előkészített másolás a Base használatával

Ha a forrásadatok nem felelnek meg az előző szakasz feltételeinek, engedélyezze az adatok másolását egy átmeneti Azure Blob Storage-példányon keresztül. Nem lehet Azure-Premium Storage. Ebben az esetben a Azure Data Factory automatikusan futtatja az adatok átalakításait, hogy megfeleljen a Base adatformátumra vonatkozó követelményeinek. Ezt követően a program a SQL Data Warehouseba tölti be az adatok betöltéséhez használt alalapot. Végezetül törli az ideiglenes adatait a blob Storage-ból. Az adatok átmeneti Azure Blob Storage-példányon keresztül történő másolásával kapcsolatos részletekért tekintse meg a [Lépcsőzetes másolás című szakaszt](copy-activity-performance.md#staged-copy) .

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

## <a name="best-practices-for-using-polybase"></a>Ajánlott eljárások a Base használatához

A következő szakaszokban a [Azure SQL Data Warehouse ajánlott eljárásain](../sql-data-warehouse/sql-data-warehouse-best-practices.md)kívül ajánlott eljárásokat ismertetjük.

### <a name="required-database-permission"></a>Szükséges adatbázis-engedély

A (z) rendszerbe való betöltést követően a felhasználónak ["vezérlés" engedéllyel](https://msdn.microsoft.com/library/ms191291.aspx) kell rendelkeznie a céladatbázis SQL Data Warehouse. Ennek egyik módja, ha a felhasználót a **db_owner** szerepkör tagjaként adja hozzá. Ebből a cikkből megtudhatja, hogyan teheti meg ezt a [SQL Data Warehouse áttekintésében](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

### <a name="row-size-and-data-type-limits"></a>Sorok mérete és adattípusi korlátai

A kiinduló terhelések az 1 MB-nál kisebb sorokra korlátozódnak. Nem használható a VARCHR (MAX), a NVARCHAR (MAX) vagy a VARBINARY (MAX) való betöltéshez. További információ: [SQL Data Warehouse szolgáltatási kapacitás korlátai](../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Ha a forrásadatok 1 MB-nál nagyobb méretű sorokkal rendelkeznek, érdemes lehet függőlegesen felosztani a forrás táblákat több kis méretre. Győződjön meg arról, hogy az egyes sorok legnagyobb mérete nem haladja meg a korlátot. A kisebb táblák ezután a Base használatával tölthetők be, és a Azure SQL Data Warehouse együtt egyesítve lesznek.

Azt is megteheti, hogy az ilyen széles oszlopokkal rendelkező adatok esetében nem hozhatók be az adatok az ADF használatával, ha kikapcsolja az "alapszintű" beállítást.

### <a name="sql-data-warehouse-resource-class"></a>SQL Data Warehouse Resource osztály

A lehető legjobb teljesítmény elérése érdekében rendeljen hozzá egy nagyobb erőforrás-osztályt ahhoz a felhasználóhoz, amely az adatok a SQL Data Warehouseba való betöltését teszi lehetővé.

### <a name="polybase-troubleshooting"></a>Alapszintű hibaelhárítás

**Betöltés decimális oszlopba**

Ha a forrásadatok szöveges formátumban vagy más nem a-alapú kompatibilis tárolókban találhatók (a szakaszos másolással és a paritással), és üres értéket tartalmaz SQL Data Warehouse decimális oszlopba való betöltéshez, a következő hibaüzenet jelenhet meg:

```
ErrorCode=FailedDbOperation, ......HadoopSqlException: Error converting data type VARCHAR to DECIMAL.....Detailed Message=Empty string can't be converted to DECIMAL.....
```

A megoldás az "**alapértelmezett típus használata**" beállítás kijelölésének feloldása (hamis) a másolási tevékenység fogadója – > a bázisterület alapbeállításai. A "[USE_TYPE_DEFAULT](https://docs.microsoft.com/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest#arguments
)" egy alapszintű natív konfiguráció, amely meghatározza, hogy a rendszer hogyan kezelje a hiányzó értékeket a tagolt szövegfájlokban, amikor a viszonyítási adatok beolvasása a szövegfájlból történik. 

**`tableName` a Azure SQL Data Warehouse**

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

A NULL érték az alapértelmezett érték egy speciális formája. Ha az oszlop üres, akkor előfordulhat, hogy az adott oszlop blobjában lévő bemeneti adatok üresek. De nem lehet hiányzik a bemeneti adatkészletből. A Azure SQL Data Warehouseban található hiányzó értékekhez tartozó albase szúr be NULL értéket.

## <a name="mapping-data-flow-properties"></a>Adatfolyam-tulajdonságok leképezése

A forrás- [átalakítás](data-flow-source.md) és a fogadó [átalakítás](data-flow-sink.md) részleteinek megismerése a leképezési folyamatokban.

## <a name="data-type-mapping-for-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse adattípusának leképezése

Amikor a vagy a rendszerből másol adatokból Azure SQL Data Warehouse, a rendszer az alábbi leképezéseket használja Azure SQL Data Warehouse adattípusokból Azure Data Factory köztes adattípusokra. Lásd: [séma-és adattípus-leképezések](copy-activity-schema-and-type-mapping.md) , amelyből megtudhatja, hogy a másolási tevékenység hogyan képezi le a forrás sémáját és az adattípust

>[!TIP]
>Tekintse meg az SQL DW által támogatott adattípusok és a nem támogatott megoldások [Azure SQL Data Warehouse cikkében található táblázat adattípusait](../sql-data-warehouse/sql-data-warehouse-tables-data-types.md) .

| Azure SQL Data Warehouse adattípus    | Data Factory időközi adattípus |
| :------------------------------------ | :----------------------------- |
| bigint                                | Int64                          |
| binary                                | Bájt []                         |
| bit                                   | Logikai                        |
| char                                  | Karakterlánc, char []                 |
| dátum                                  | Dátum és idő                       |
| Dátum/idő                              | Dátum és idő                       |
| datetime2                             | Dátum és idő                       |
| DateTimeOffset                        | DateTimeOffset                 |
| Decimális                               | Decimális                        |
| FILESTREAM attribútum (varbinary (max)) | Bájt []                         |
| Lebegőpontos szám                                 | Double                         |
| image                                 | Bájt []                         |
| int                                   | Int32                          |
| pénzt                                 | Decimális                        |
| NCHAR                                 | Karakterlánc, char []                 |
| numerikus                               | Decimális                        |
| nvarchar                              | Karakterlánc, char []                 |
| real                                  | Önálló                         |
| ROWVERSION                            | Bájt []                         |
| idő adattípusúra                         | Dátum és idő                       |
| smallint                              | Int16                          |
| túlcsordulási                            | Decimális                        |
| time                                  | időtartam                       |
| tinyint                               | Bájt                           |
| uniqueidentifier                      | GUID                           |
| varbinary                             | Bájt []                         |
| varchar                               | Karakterlánc, char []                 |

## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [keresési tevékenységet](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>GetMetadata tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [GetMetaData tevékenységet](control-flow-get-metadata-activity.md) 

## <a name="next-steps"></a>Következő lépések
A forrásként és fogadóként támogatott adattárak listáját a Azure Data Factory másolási tevékenysége című részben tekintheti meg a [támogatott adattárak és-formátumok](copy-activity-overview.md##supported-data-stores-and-formats)című témakörben.
