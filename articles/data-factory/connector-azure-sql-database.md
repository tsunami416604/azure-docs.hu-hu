---
title: Adatok másolása és átalakítása az Azure SQL Database-ben
description: Megtudhatja, hogyan másolhat adatokat az Azure SQL Database-be és onnan, és hogyan alakíthat át adatokat az Azure SQL Database-ben az Azure Data Factory használatával.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/12/2020
ms.openlocfilehash: db55e685fb50c89eb850e1b9ee9dcf13d20fb614
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417543"
---
# <a name="copy-and-transform-data-in-azure-sql-database-by-using-azure-data-factory"></a>Adatok másolása és átalakítása az Azure SQL Database-ben az Azure Data Factory használatával

> [!div class="op_single_selector" title1="Válassza ki az Azure Data Factory által használt verziót:"]
> * [1-es verzió](v1/data-factory-azure-sql-connector.md)
> * [Aktuális verzió](connector-azure-sql-database.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk bemutatja, hogyan használhatja a másolási tevékenység et az Azure Data Factory adatok másolására és az Azure SQL-adatbázisba, és az Adatfolyam használatával alakítja át az adatokat az Azure SQL Database-ben. Az Azure Data Factory ról a [bevezető cikkben](introduction.md)olvashat.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez az Azure SQL Database-összekötő a következő tevékenységek esetén támogatott:

- [Tevékenység másolása](copy-activity-overview.md) [támogatott forrás/fogadó mátrix](copy-activity-overview.md) táblával
- [Adatfolyam leképezése](concepts-data-flow-overview.md)
- [Keress tevékenységet](control-flow-lookup-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)

Másolási tevékenység esetén ez az Azure SQL Database-összekötő a következő függvényeket támogatja:

- Adatok másolása SQL-hitelesítés és az Azure Active Directory (Azure AD) alkalmazástoken-hitelesítés használatával egyszerű szolgáltatásvagy felügyelt identitások Azure-erőforrások használatával.
- Forrásként adatok beolvasása SQL-lekérdezéssel vagy tárolt eljárással.
- Fogadóként adatok fűzése a céltáblához, vagy egy tárolt eljárás meghívása egyéni logikával a másolás során.

>[!NOTE]
>Az Azure SQL Database [mindig titkosított](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-current) nem támogatja ez az összekötő most. A munka megkerüléséhez használhat egy [általános ODBC-összekötőt](connector-odbc.md) és egy SQL Server ODBC-illesztőprogramot egy saját üzemeltetésű integrációs futásidő segítségével. Kövesse [ezt az útmutatót](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-current) az ODBC illesztőprogram letöltési és csatlakozási karakterlánc-konfigurációival.

> [!IMPORTANT]
> Ha az Azure Data Factory integrációs futásideje használatával másolja az adatokat, konfiguráljon egy [Azure SQL Server tűzfalat,](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) hogy az Azure-szolgáltatások hozzáférhessenek a kiszolgálóhoz.
> Ha az adatokmásolása saját üzemeltetésű integrációs futásidejű használatával történik, konfigurálja az Azure SQL Server tűzfalát a megfelelő IP-tartomány engedélyezéséhez. Ez a tartomány tartalmazza a gép IP-címét, amely az Azure SQL Database-hez való csatlakozáshoz használható.

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok az Azure Data Factory-entitások azure-sql-adatbázis-összekötőre jellemző entitásainak definiálásához használt tulajdonságok részleteit ismertetik.

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai

Ezek a tulajdonságok egy Azure SQL Database-hez csatolt szolgáltatás esetén támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A **típustulajdonságot** **az AzureSqlDatabase tulajdonságra**kell állítani. | Igen |
| connectionString (kapcsolati karakterlánc) | Adja meg az Azure SQL Database-példányhoz való csatlakozáshoz szükséges információkat a **connectionString** tulajdonsághoz. <br/>Az Azure Key Vaultban jelszót vagy egyszerű szolgáltatáskulcsot is elhelyezhet. Ha SQL-hitelesítésről van `password` szó, húzza ki a konfigurációt a kapcsolati karakterláncból. További információ: A JSON-példa a táblázat ot és az Áruház hitelesítő adatait az [Azure Key Vaultban.](store-credentials-in-key-vault.md) | Igen |
| servicePrincipalId | Adja meg az alkalmazás ügyfélazonosítóját. | Igen, ha egyszerű szolgáltatással használja az Azure AD-hitelesítést |
| servicePrincipalKey | Adja meg az alkalmazás kulcsát. Jelölje meg ezt a mezőt **SecureString** ként, hogy biztonságosan tárolhatja az Azure Data Factoryban, vagy [hivatkozzon az Azure Key Vaultban tárolt titkos fájlokra.](store-credentials-in-key-vault.md) | Igen, ha egyszerű szolgáltatással használja az Azure AD-hitelesítést |
| Bérlő | Adja meg a bérlői adatokat, például a tartománynevet vagy a bérlőazonosítót, amely alatt az alkalmazás található. Az egér egér relai mozgatórészének az Azure Portal jobb felső sarkában való lebegtetésével. | Igen, ha egyszerű szolgáltatással használja az Azure AD-hitelesítést |
| connectVia | Ez [az integrációs futásidejű](concepts-integration-runtime.md) az adattárhoz való csatlakozáshoz használható. Használhatja az Azure-integrációs runtime vagy egy saját üzemeltetésű integrációs runtime, ha az adattár található egy magánhálózat. Ha nincs megadva, az alapértelmezett Azure-integrációs futásidejű használatos. | Nem |

A különböző hitelesítési típusok esetében tekintse meg az előfeltételekről és jsonmintákról szóló alábbi szakaszokat:

- [SQL-hitelesítés](#sql-authentication)
- [Azure AD alkalmazástoken-hitelesítés: egyszerű szolgáltatás](#service-principal-authentication)
- [Azure AD-alkalmazástoken-hitelesítés: Felügyelt identitások az Azure-erőforrásokhoz](#managed-identity)

>[!TIP]
>Ha a "UserErrorFailedToConnectToSqlServer" hibakóddal és a "Az adatbázis munkamenet-korlátjának XXX és `Pooling=false` elérte" hibaüzenetjelenik meg, adja hozzá a kapcsolati karakterláncot, és próbálkozzon újra.

### <a name="sql-authentication"></a>SQL-hitelesítés

#### <a name="linked-service-example-that-uses-sql-authentication"></a>SQL-hitelesítést használó csatolt szolgáltatáspélda

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
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

**Jelszó az Azure Key Vaultban** 

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30",
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

### <a name="service-principal-authentication"></a>Egyszerű szolgáltatásnév hitelesítése

Egyszerű szolgáltatásalapú Azure AD-alkalmazásjogkivonat-hitelesítés használatához hajtsa végre az alábbi lépéseket:

1. [Hozzon létre egy Azure Active Directory-alkalmazást](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) az Azure Portalon. Jegyezze fel az alkalmazás nevét és a csatolt szolgáltatást meghatározó következő értékeket:

    - Alkalmazásazonosító
    - Alkalmazáskulcs
    - Bérlőazonosító

2. [Azure Active Directory-rendszergazda kiépítése](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server) az Azure SQL Server az Azure Portalon, ha még nem tette meg. Az Azure AD-rendszergazda kell egy Azure AD-felhasználó vagy az Azure AD-csoport, de nem lehet egyszerű szolgáltatás. Ez a lépés úgy történik, hogy a következő lépésben egy Azure AD-identitás használatával hozzon létre egy tartalmazott adatbázis-felhasználó a szolgáltatásnév.

3. [A szolgáltatásnévhez hozzon létre egyben foglalt adatbázis-felhasználókat.](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) Csatlakozzon az adatbázishoz, vagy amelyhez adatokat szeretne másolni az sql Server Management Studio eszközeivel, egy Azure AD-identitással, amely legalább ALTER BÁRMELY FELHASZNÁLÓ engedéllyel rendelkezik. Futtassa a következő T-SQL-t: 
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. Adja meg a szolgáltatás névvel szükséges engedélyeket, ahogy általában az SQL-felhasználók vagy mások. Futtassa a következő kódot. További lehetőségeket [ebben a dokumentumban](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017)tartalmaz.

    ```sql
    ALTER ROLE [role name] ADD MEMBER [your application name];
    ```

5. Konfiguráljon egy Azure SQL Database-kapcsolt szolgáltatást az Azure Data Factoryban.


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Csatolt szolgáltatás példa, amely egyszerű szolgáltatáshitelesítést használ

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
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

Az adat-előállító az [Azure-erőforrások felügyelt identitásához](data-factory-service-identity.md) társítható, amely az adott adat-előállítót képviseli. Ezt a felügyelt identitást használhatja az Azure SQL Database-hitelesítéshez. A kijelölt gyár i. identitás használatával hozzáférhet az adatbázisból vagy az adatbázisba.

Felügyelt identitás-hitelesítés használatához kövesse az alábbi lépéseket.

1. [Azure Active Directory-rendszergazda kiépítése](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server) az Azure SQL Server az Azure Portalon, ha még nem tette meg. Az Azure AD-rendszergazda lehet Egy Azure AD-felhasználó vagy egy Azure AD-csoport. Ha a felügyelt identitással rendelkező csoportot rendszergazdai szerepkörrel adja meg, hagyja ki a 3. A rendszergazda teljes hozzáféréssel rendelkezik az adatbázishoz.

2. [Hozzon létre egy tartalmazott adatbázis-felhasználók](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) at az Azure Data Factory felügyelt identitás. Csatlakozzon az adatbázishoz, vagy amelyhez adatokat szeretne másolni az sql Server Management Studio eszközeivel, egy Azure AD-identitással, amely legalább ALTER BÁRMELY FELHASZNÁLÓ engedéllyel rendelkezik. Futtassa a következő T-SQL-t: 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. Adja meg a Data Factory felügyelt identitás szükséges engedélyeket, ahogy általában az SQL-felhasználók és mások. Futtassa a következő kódot. További lehetőségeket [ebben a dokumentumban](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017)tartalmaz.

    ```sql
    ALTER ROLE [role name] ADD MEMBER [your Data Factory name];
    ```

4. Konfiguráljon egy Azure SQL Database-kapcsolt szolgáltatást az Azure Data Factoryban.

**Példa**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
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

Az adatkészletek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját az Adatkészletek című [témakörben található.](https://docs.microsoft.com/azure/data-factory/concepts-datasets-linked-services) 

Az Azure SQL Database adatkészleta a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | Az adatkészlet **típustulajdonságát** **Az AzureSqlTable**programra kell állítani. | Igen |
| Séma | A séma neve. |Nem a forráshoz, Igen a mosogatóhoz  |
| tábla | A tábla/nézet neve. |Nem a forráshoz, Igen a mosogatóhoz  |
| tableName | A sémával rendelkező tábla/nézet neve. Ez a tulajdonság a visszamenőleges kompatibilitás érdekében támogatott. Az új számítási `schema` `table`feladatokhoz használja és használja. | Nem a forráshoz, Igen a mosogatóhoz |

#### <a name="dataset-properties-example"></a>Példa adatkészlet tulajdonságaira

```json
{
    "name": "AzureSQLDbDataset",
    "properties":
    {
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Database linked service name>",
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

A tevékenységek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját a [Folyamatok című témakörben található.](concepts-pipelines-activities.md) Ez a szakasz az Azure SQL Database-forrás és -fogadó által támogatott tulajdonságok listáját tartalmazza.

### <a name="azure-sql-database-as-the-source"></a>Az Azure SQL Database mint forrás

Az Azure SQL Database-ből származó adatok másolásához a másolási tevékenység **forrásszakaszában** a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenységforrás **típustulajdonságát** **az AzureSqlSource**mezőre kell állítani. Az "SqlSource" típus továbbra is támogatott a visszamenőleges kompatibilitás érdekében. | Igen |
| sqlReaderQuery | Ez a tulajdonság az egyéni SQL-lekérdezést használja az adatok olvasásához. Például: `select * from MyTable`. | Nem |
| sqlReaderStoredProcedureName | Annak a tárolt eljárásnak a neve, amely adatokat olvas be a forrástáblából. Az utolsó SQL utasításnak SELECT utasításnak kell lennie a tárolt eljárásban. | Nem |
| storedProcedureParameters | A tárolt eljárás paraméterei.<br/>Az engedélyezett értékek név- vagy értékpárok. A paraméterek nevének és burkolatának meg kell egyeznie a tárolt eljárásparamétereinek nevével és burkolatával. | Nem |
| isolationLevel | Megadja az SQL-forrás tranzakciózárolási viselkedését. Az engedélyezett értékek a következők: **ReadCommitted** (alapértelmezett), **ReadUncommitted**, **RepeatableRead**, **Serializable**, **Snapshot**. További részletekért olvassa el ezt a [dokit.](https://docs.microsoft.com/dotnet/api/system.data.isolationlevel) | Nem |

**Megjegyzésre mutat:**

- Ha **az sqlReaderQuery** meg van adva az **AzureSqlSource-hoz,** a másolási tevékenység futtatja ezt a lekérdezést az Azure SQL Database forrásában az adatok lekérni. A tárolt eljárást az **sqlReaderStoredProcedureName** és a **storedProcedureParameters** megadásával is megadhatja, ha a tárolt eljárás paramétereket vesz igénybe.
- Ha nem ad meg **sqlReaderQuery** vagy **sqlReaderStoredProcedureName értéket,** a JSON adatkészlet "struktúra" szakaszában definiált oszlopokat a rendszer lekérdezés létrehozásához használja. A `select column1, column2 from mytable` lekérdezés az Azure SQL Database-en fut. Ha az adatkészlet-definíciónem rendelkezik "struktúra" beállítással, a program a táblából az összes oszlopot kijelöli.

#### <a name="sql-query-example"></a>Példa SQL-lekérdezésre

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
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
                "type": "AzureSqlSource",
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
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
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
                "type": "AzureSqlSource",
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

### <a name="azure-sql-database-as-the-sink"></a>Az Azure SQL Database a fogadó

> [!TIP]
> Tudjon meg többet a támogatott írási viselkedés, konfigurációk és ajánlott eljárások [az ajánlott adatok betöltése az Azure SQL Database.learn](#best-practice-for-loading-data-into-azure-sql-database)more about the supported write behaviors, configurations, and best practices from Best practice for loading data into Azure SQL Database.

Az Azure SQL Database-be való másoláshoz a következő tulajdonságokat támogatja a másolási tevékenység **fogadó** ja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenység fogadójának **típustulajdonságát** **az AzureSqlSink**beállításra kell állítani. Az "SqlSink" típus továbbra is támogatott a visszamenőleges kompatibilitás érdekében. | Igen |
| writeBatchSize | Az SQL-táblába *kötegenként*beszúrandó sorok száma .<br/> Az engedélyezett érték **egész szám** (sorok száma). Alapértelmezés szerint az Azure Data Factory dinamikusan határozza meg a megfelelő kötegméretet a sorméretealapján. | Nem |
| writeBatchTimeout | A kötegbehelyezési művelet befejezéséhez, mielőtt időtúljárna.<br/> Az engedélyezett érték az **időtartomány**. Erre példa a "00:30:00" (30 perc). | Nem |
| preCopyScript | Adja meg az SQL-lekérdezést a másolási tevékenység futtatásához, mielőtt adatokat írna az Azure SQL Database-be. A példányfuttatáskor csak egyszer van meghívva. Ezzel a tulajdonsággal megtisztítja az előre betöltött adatokat. | Nem |
| sqlWriterDProcedureName | A tárolt eljárás neve, amely meghatározza a forrásadatok céltáblára történő alkalmazását. <br/>Ezt a tárolt eljárást *kötegenként kell meghívni.* A csak egyszer futó és a forrásadatokhoz semmi közük nem rendelkező műveletekhez, például a törléshez vagy a csonkoláshoz használja a `preCopyScript` tulajdonságot. | Nem |
| storedProcedureTableTypeParameterName |A tárolt eljárásban megadott táblatípus paraméterneve.  |Nem |
| sqlWriterTableType típus |A tárolt eljárásban használandó táblatípus neve. A másolási tevékenység elérhetővé teszi az áthelyezett adatokat egy ideiglenes táblában ezzel a táblatípussal. A tárolt eljáráskód ezután egyesítheti a másolt adatokat a meglévő adatokkal. |Nem |
| storedProcedureParameters |A tárolt eljárás paraméterei.<br/>Az engedélyezett értékek a név- és értékpárok. A paraméterek nevének és burkolatának meg kell egyeznie a tárolt eljárásparamétereinek nevével és burkolatával. | Nem |
| tableOption | Itt adható meg, hogy a program automatikusan hozza-e létre a fogadótáblát, ha nem létezik a forrásséma alapján. Az automatikus táblalétrehozása nem támogatott, ha a fogadó a tárolt eljárást adja meg, vagy a programozott másolási tevékenységben van konfigurálva. Az engedélyezett `none` értékek a `autoCreate`következők: (alapértelmezett), . |Nem |
| disableMetricsCollection | A Data Factory olyan metrikákat gyűjt, mint például az Azure SQL Database DDO-k a másolási teljesítmény optimalizálásához és a javaslatokhoz. Ha ez a viselkedés aggasztja, adja meg `true` a kikapcsolására. | Nem (az `false`alapértelmezett ) |

**1. példa: Adatok hozzáfűzése**

```json
"activities":[
    {
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureSqlSink",
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
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureSqlSink",
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

## <a name="best-practice-for-loading-data-into-azure-sql-database"></a>Ajánlott eljárás az adatok Azure SQL Database-be való betöltéséhez

Amikor adatokat másol az Azure SQL Database-be, előfordulhat, hogy más írási viselkedésre van szükség:

- [Hozzáfűzés:](#append-data)A forrásadatok csak új rekordokat tartalmazó adatokat tartalmazó adatokat.
- [Upsert](#upsert-data): A forrásadatok beszúrásokat és frissítéseket is tartalmaznak.
- [Felülírás:](#overwrite-the-entire-table)Minden alkalommal újra szeretnék tölteni egy teljes dimenziótáblát.
- [Írjon egyéni logikával:](#write-data-with-custom-logic)További feldolgozásra van szükségem, mielőtt a végső beillesztés a céltáblába kerül.

Tekintse meg a megfelelő szakaszokat az Azure Data Factory konfigurálásáról és az ajánlott eljárásokról.

### <a name="append-data"></a>Adatok hozzáfűzése

Az adatok hozzáfűzése az Azure SQL Database-fogadó összekötő alapértelmezett viselkedése. Az Azure Data Factory egy tömeges beszúrással hatékonyan ír a táblába. Beállíthatja a forrást, és ennek megfelelően elsüllyesztheti a másolási tevékenységet.

### <a name="upsert-data"></a>Adatok beszúrása és frissítése (upsert)

**1. lehetőség:** Ha nagy mennyiségű adatot szeretne másolni, a következő módszerrel végezze el a upsert et: 

- Először használjon [egy adatbázis hatókörrel tartozó ideiglenes táblát](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current#database-scoped-global-temporary-tables-azure-sql-database) az összes rekord tömeges betöltéséhez a másolási tevékenység használatával. Mivel az adatbázis hatókörrel végzett ideiglenes tábláihoz tartozó műveletek nem naplózhatók, több millió rekordot tölthet be másodpercek alatt.
- Futtasson egy tárolt eljárástevékenységet az Azure Data Factoryban [a MERGE](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) vagy INSERT/UPDATE utasítás alkalmazásához. Az ideiglenes tábla forrásként való végrehajtása az összes frissítést vagy beszúrást egyetlen tranzakcióként hajthatja végre. Ily módon csökken az oda- és naplóműveletek száma. A tárolt eljárási tevékenység végén az ideiglenes tábla csonkolható, hogy készen álljon a következő upsert ciklusra.

Például az Azure Data Factoryban létrehozhat egy folyamatot egy **tárolt eljárás tevékenységgel**láncolt **másolási tevékenységgel.** Az előbbi adatokat a forrástárolóból egy Azure SQL Database ideiglenes táblába másolja, például **a ##UpsertTempTable**, mint az adatkészlet táblanevét. Ezután az utóbbi meghívja a tárolt eljárást az ideiglenes táblából származó forrásadatok nak a céltáblába történő egyesítéséhez és az ideiglenes tábla karbantartásához.

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

**2. lehetőség:** Azt is választhatja, hogy [a másolási tevékenységen belül meghívja a tárolt eljárást.](#invoke-a-stored-procedure-from-a-sql-sink) Ez a megközelítés a forrástáblában `writeBatchSize` minden köteget futtat (a tulajdonság szerint) ahelyett, hogy a másolási tevékenység alapértelmezett beszúrásaként használna.

### <a name="overwrite-the-entire-table"></a>A teljes tábla felülírása

A **preCopyScript** tulajdonság konfigurálható a másolási tevékenység fogadójában. Ebben az esetben minden egyes futó másolási tevékenység hez az Azure Data Factory először futtatja a parancsfájlt. Ezután futtatja a másolatot az adatok beszúrásához. Ha például felül szeretné írni a teljes táblát a legújabb adatokkal, adjon meg egy parancsfájlt, amely először törli az összes rekordot, mielőtt tömegesen betöltené az új adatokat a forrásból.

### <a name="write-data-with-custom-logic"></a>Adatok írása egyéni logikával

Az adatok írásának lépései egyéni logikával [hasonlóak a Upsert-adatok](#upsert-data) szakaszban leírtakhoz. Ha a forrásadatok végső beillesztése előtt további feldolgozást kell alkalmaznia a céltáblába, a nagy léptékű, a következő két dolgot teheti meg:

- Töltse be az adatbázis hatókörét tartalmazó ideiglenes táblába, majd hívja meg a tárolt eljárást. 
- Tárolt eljárás meghívása a másolás során.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a><a name="invoke-a-stored-procedure-from-a-sql-sink"></a>Tárolt eljárás meghívása SQL-fogadóból

Amikor adatokat másol az Azure SQL Database-be, konfigurálhatja és meghívhat egy felhasználó által megadott tárolt eljárást, amely további paramétereket biztosít a forrástábla minden egyes kötegében. A tárolt eljárás szolgáltatás kihasználja a [tábla értékű paramétereket.](https://msdn.microsoft.com/library/bb675163.aspx)

A tárolt eljárás akkor használható, ha a beépített másolási mechanizmusok nem szolgálják a célt. Ilyen például az, ha a forrásadatok végső beillesztése előtt további feldolgozást szeretne alkalmazni a céltáblába. Néhány további feldolgozási példa, ha egyesíteni szeretné az oszlopokat, további értékeket szeretne keresni, és több táblába szeretne beszúrni.

Az alábbi minta bemutatja, hogyan használhatja a tárolt eljárást egy upsert egy táblába az Azure SQL Database-ben. Tegyük fel, hogy a bemeneti adatok és a **marketingfogadó** tábla három oszlopból áll: **ProfileID**, **State**és **Category**. Végezze el a upsert alapján **ProfileID** oszlop, és csak alkalmazza azt egy adott kategóriában az úgynevezett "ProductA".

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
        "type": "AzureSqlSink",
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

## <a name="mapping-data-flow-properties"></a>Adatfolyam tulajdonságainak leképezése

Amikor átalakítja az adatokat az adatleképezési adatfolyamban, az Azure SQL Database tábláit olvashatja és írhatja. További információ: a [forrásátalakítása](data-flow-source.md) és a [fogadó átalakítása](data-flow-sink.md) az adatfolyamatok leképezésében.

### <a name="source-transformation"></a>Forrás átalakítása

Az Azure SQL-adatbázisra vonatkozó beállítások a forrásátalakítás **Forrásbeállítások** lapján érhetők el. 

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

Az Azure SQL Database-re vonatkozó beállítások a fogadó **átalakításának Beállítások** lapján érhetők el.

**Frissítési módszer:** Azt határozza meg, hogy milyen műveletek engedélyezettek az adatbázis célállomásán. Az alapértelmezett beállítás az, hogy csak a beszúrásokat engedélyezi. A sorok frissítéséhez, upsert-jéhez vagy törléséhez egy alter-row átalakításra van szükség a műveletek sorainak címkézéséhez. A frissítések, upserts és törlések, egy kulcs oszlop vagy oszlopok kell beállítani, hogy melyik sort kell módosítani.

![Kulcsoszlopok](media/data-flow/keycolumn.png "Kulcsoszlopok")

Az itt kulcsként választott oszlopnevet az ADF a későbbi frissítés, upsert, delete részeként fogja használni. Ezért ki kell választania egy oszlopot, amely létezik a Fogadó leképezésben. Ha nem szeretné beírni az értéket ebbe a kulcsoszlopba, kattintson a "Kulcsoszlopok írásának kihagyása" gombra.

**Táblázat művelet:** Azt határozza meg, hogy az írás előtt az összes sort újra létrehozza vagy eltávolítja-e a céltáblából.
* Nincs: Az asztalhoz nem történik művelet.
* Újralétrehozás: A tábla elesik, és újra létrejön. Új tábla dinamikus létrehozása esetén szükséges.
* Csonkolás: A céltábla minden sora eltávolításra kerül.

**Kötegméret**: Azt szabályozza, hogy hány sor kerül írásra az egyes gyűjtőkben. A nagyobb kötegméretek javítják a tömörítést és a memóriaoptimalizálást, de az adatok gyorsítótárazásakor kiszorítják a memóriából kitett kivételeket.

**SQL-parancsfájlok előtti és postai száma:** Adja meg azokat a többsoros SQL-parancsfájlokat, amelyek az (előfeldolgozás) és az (utófeldolgozás) előtt és után kerülnek a Sink-adatbázisba

![SQL-feldolgozás előtti és utáni parancsfájlok](media/data-flow/prepost1.png "SQL-feldolgozási parancsfájlok")

## <a name="data-type-mapping-for-azure-sql-database"></a>Adattípus-leképezés az Azure SQL Database-hez

Amikor az adatokat az Azure SQL Database-ből vagy az Azure SQL Database-be másolja, a következő leképezéseket használja a rendszer az Azure SQL Database adattípusaiból az Azure Data Factory köztes adattípusaihoz. Ha meg szeretné tudni, hogy a másolási tevékenység hogyan rendeli le a forrássémát és az adattípust a fogadóhoz, olvassa el a [Séma- és adattípus-hozzárendelések című témakört.](copy-activity-schema-and-type-mapping.md)

| Azure SQL Database adattípus | Az Azure Data Factory köztes adattípusa |
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
| tinyint |Bájt |
| uniqueidentifier |Guid |
| varbinary között |Bájt[] |
| varchar |Karakterlánc, Karakter |
| xml |Xml |

>[!NOTE]
> A decimális köztes típusra leképezett adattípusok esetében jelenleg az Azure Data Factory legfeljebb 28 pontosságot támogat. Ha 28-nál nagyobb pontosságú adatokkal rendelkezik, fontolja meg az SQL-lekérdezésben karakterláncsá való konvertálását.

## <a name="lookup-activity-properties"></a>A keresgaszíntevékenység tulajdonságai

A tulajdonságokrészleteinek megismeréséhez ellenőrizze a [Kereskövetési tevékenységet.](control-flow-lookup-activity.md)

## <a name="getmetadata-activity-properties"></a>GetMetadata tevékenység tulajdonságai

A tulajdonságokrészleteinek megismeréséhez jelölje be a [GetMetadata tevékenység](control-flow-get-metadata-activity.md) 

## <a name="next-steps"></a>További lépések
Az Azure Data Factory másolási tevékenysége által forrásként és fogadóként támogatott adattárak listáját a [Támogatott adattárak és -formátumok című témakörben tetszhet.](copy-activity-overview.md#supported-data-stores-and-formats)
