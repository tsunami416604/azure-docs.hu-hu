---
title: Adatok másolása az Azure SQL Database felügyelt példányába és onnan
description: Megtudhatja, hogyan helyezhet át adatokat az Azure SQL Database felügyelt példánya és az Azure Data Factory használatával.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.custom: seo-lt-2019
ms.date: 03/12/2020
ms.openlocfilehash: 11f4005e802e2a584b21903bfead2c6b9701f065
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80238757"
---
# <a name="copy-data-to-and-from-azure-sql-database-managed-instance-by-using-azure-data-factory"></a>Adatok másolása az Azure SQL Database felügyelt példányába az Azure Data Factory használatával

Ez a cikk bemutatja, hogyan használhatja a másolási tevékenységet az Azure Data Factory adatok másolásához és az Azure SQL Database felügyelt példány. A [Tevékenység másolása áttekintése](copy-activity-overview.md) cikkre épül, amely a másolási tevékenység általános áttekintését mutatja be.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez az Azure SQL Database felügyelt példány összekötő a következő tevékenységek esetén támogatott:

- [Tevékenység másolása](copy-activity-overview.md) [támogatott forrás/fogadó mátrixcal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)

Az Azure SQL Database felügyelt példányból adatokat másolhat bármely támogatott fogadó adattárba. Bármely támogatott forrásadattárból adatokat másolhat a felügyelt példányba. A másolási tevékenység által forrásként és fogadóként támogatott adattárak listáját a [Támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblában láthatja.

Ez az Azure SQL Database felügyelt példány-összekötő kifejezetten a következőket támogatja:

- Adatok másolása SQL-hitelesítés és az Azure Active Directory (Azure AD) alkalmazástoken-hitelesítés használatával egyszerű szolgáltatásvagy felügyelt identitások Azure-erőforrások használatával.
- Forrásként adatok beolvasása SQL-lekérdezéssel vagy tárolt eljárással.
- Fogadóként adatok fűzése a céltáblához, vagy egy tárolt eljárás meghívása egyéni logikával a másolás során.

>[!NOTE]
>Az Azure SQL Database felügyelt példány [a mindig titkosított](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-mi-current) jelenleg nem támogatja. A munka megkerüléséhez használhat egy [általános ODBC-összekötőt](connector-odbc.md) és egy SQL Server ODBC-illesztőprogramot egy saját üzemeltetésű integrációs futásidő segítségével. Kövesse [ezt az útmutatót](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-mi-current) az ODBC illesztőprogram letöltési és csatlakozási karakterlánc-konfigurációival.

## <a name="prerequisites"></a>Előfeltételek

Az Azure SQL Database felügyelt példány [nyilvános végponteléréséhez](../sql-database/sql-database-managed-instance-public-endpoint-securely.md)használhatja az Azure Data Factory felügyelt Azure-integrációs futásidejű. Győződjön meg arról, hogy engedélyezi a nyilvános végpontot, és nyilvános végpontforgalmat is engedélyez a hálózati biztonsági csoportban, hogy az Azure Data Factory csatlakozni tud az adatbázishoz. További információt [ebben az útmutatóban](../sql-database/sql-database-managed-instance-public-endpoint-configure.md)talál.

Az Azure SQL Database felügyelt példány privát végpont eléréséhez hozzon létre egy [saját üzemeltetésű integrációs futásidejű,](create-self-hosted-integration-runtime.md) amely hozzáférhet az adatbázishoz. Ha a saját üzemeltetésű integrációs futásidejű ugyanabban a virtuális hálózatban, mint a felügyelt példány, győződjön meg arról, hogy az integrációs futásidejű gép egy másik alhálózatban, mint a felügyelt példány. Ha a saját üzemeltetésű integrációs futásidejű egy másik virtuális hálózatban, mint a felügyelt példány, használhatja akár egy virtuális hálózati társviszony-létesítés, vagy egy virtuális hálózat virtuális hálózati kapcsolat. További információt az [Alkalmazás csatlakoztatása az Azure SQL Database felügyelt példányhoz című témakörben talál.](../sql-database/sql-database-managed-instance-connect-app.md)

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok az Azure Data Factory-entitások az Azure SQL Database felügyelt példány összekötőre jellemző definiálásához használt tulajdonságok részleteit ismertetik.

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai

Az Azure SQL Database felügyelt példányhoz csatolt szolgáltatása a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A típustulajdonságot **AzureSqlMI-re**kell állítani. | Igen |
| connectionString (kapcsolati karakterlánc) |Ez a tulajdonság a felügyelt példányhoz SQL-hitelesítéssel történő csatlakozáshoz szükséges **connectionString-adatokat** adja meg. További információt az alábbi példákban talál. <br/>Az alapértelmezett port a 1433-as számú. Ha az Azure SQL Database felügyelt példánynyilvános végponttal, explicit módon adja meg a 3342-es portot.<br> Az Azure Key Vaultban is elhelyezhet egy jelszót. Ha SQL-hitelesítésről van `password` szó, húzza ki a konfigurációt a kapcsolati karakterláncból. További információ: A JSON-példa a táblázat ot és az Áruház hitelesítő adatait az [Azure Key Vaultban.](store-credentials-in-key-vault.md) |Igen |
| servicePrincipalId | Adja meg az alkalmazás ügyfélazonosítóját. | Igen, ha egyszerű szolgáltatással használja az Azure AD-hitelesítést |
| servicePrincipalKey | Adja meg az alkalmazás kulcsát. Jelölje meg ezt a mezőt **SecureString** ként, hogy biztonságosan tárolhatja az Azure Data Factoryban, vagy [hivatkozzon az Azure Key Vaultban tárolt titkos fájlokra.](store-credentials-in-key-vault.md) | Igen, ha egyszerű szolgáltatással használja az Azure AD-hitelesítést |
| Bérlő | Adja meg a bérlői adatokat, például a tartománynevet vagy a bérlőazonosítót, amely alatt az alkalmazás található. Az egér egér relai mozgatórészének az Azure Portal jobb felső sarkában való lebegtetésével. | Igen, ha egyszerű szolgáltatással használja az Azure AD-hitelesítést |
| connectVia | Ez [az integrációs futásidejű](concepts-integration-runtime.md) az adattárhoz való csatlakozáshoz használható. Használhatja a saját üzemeltetésű integrációs runtime vagy egy Azure-integrációs runtime, ha a felügyelt példány rendelkezik egy nyilvános végpont, és lehetővé teszi az Azure Data Factory eléréséhez. Ha nincs megadva, az alapértelmezett Azure-integrációs futásidejű használatos. |Igen |

A különböző hitelesítési típusok esetében tekintse meg az előfeltételekről és jsonmintákról szóló alábbi szakaszokat:

- [SQL-hitelesítés](#sql-authentication)
- [Azure AD alkalmazástoken-hitelesítés: egyszerű szolgáltatás](#service-principal-authentication)
- [Azure AD-alkalmazástoken-hitelesítés: Felügyelt identitások az Azure-erőforrásokhoz](#managed-identity)

### <a name="sql-authentication"></a>SQL-hitelesítés

**1. példa: SQL-hitelesítés használata**

```json
{
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": "Data Source=<hostname,port>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
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
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": "Data Source=<hostname,port>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;",
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

1. Kövesse az [Azure Active Directory-rendszergazda kiépítése a felügyelt példányhoz című lépéslépéseit.](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance)

2. [Hozzon létre egy Azure Active Directory-alkalmazást](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) az Azure Portalon. Jegyezze fel az alkalmazás nevét és a csatolt szolgáltatást meghatározó következő értékeket:

    - Alkalmazásazonosító
    - Alkalmazáskulcs
    - Bérlőazonosító

3. [Hozzon létre bejelentkezéseket](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) az Azure Data Factory felügyelt identitásához. Az SQL Server Management Studio (SSMS) rendszerben csatlakozzon a felügyelt példányhoz egy sql server fiókkal, amely **rendszergazdai rendszergazda.** A **főadatbázisban** futtassa a következő T-SQL-t:

    ```sql
    CREATE LOGIN [your application name] FROM EXTERNAL PROVIDER
    ```

4. [Hozzon létre egy tartalmazott adatbázis-felhasználók](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) at az Azure Data Factory felügyelt identitás. Csatlakozzon ahhoz az adatbázishoz, amelyből adatokat szeretne másolni, futtassa a következő T-SQL-t: 
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER
    ```

5. Adja meg a Data Factory felügyelt identitás szükséges engedélyeket, ahogy általában az SQL-felhasználók és mások. Futtassa a következő kódot. További lehetőségeket [ebben a dokumentumban](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql?view=azuresqldb-mi-current)tartalmaz.

    ```sql
    ALTER ROLE [role name e.g. db_owner] ADD MEMBER [your application name]
    ```

6. Konfiguráljon egy Azure SQL Database Database felügyelt példányhoz csatolt szolgáltatást az Azure Data Factoryban.

**Példa: egyszerű szolgáltatáshitelesítés használata**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": "Data Source=<hostname,port>;Initial Catalog=<databasename>;",
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

Az adat-előállító az [Azure-erőforrások felügyelt identitásához](data-factory-service-identity.md) társítható, amely az adott adat-előállítót képviseli. Ezt a felügyelt identitást használhatja az Azure SQL Database felügyelt példányhitelesítéséhez. A kijelölt gyár i. identitás használatával hozzáférhet az adatbázisból vagy az adatbázisba.

Felügyelt identitás-hitelesítés használatához kövesse az alábbi lépéseket.

1. Kövesse az [Azure Active Directory-rendszergazda kiépítése a felügyelt példányhoz című lépéslépéseit.](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance)

2. [Hozzon létre bejelentkezéseket](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) az Azure Data Factory felügyelt identitásához. Az SQL Server Management Studio (SSMS) rendszerben csatlakozzon a felügyelt példányhoz egy sql server fiókkal, amely **rendszergazdai rendszergazda.** A **főadatbázisban** futtassa a következő T-SQL-t:

    ```sql
    CREATE LOGIN [your Data Factory name] FROM EXTERNAL PROVIDER
    ```

3. [Hozzon létre egy tartalmazott adatbázis-felhasználók](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) at az Azure Data Factory felügyelt identitás. Csatlakozzon ahhoz az adatbázishoz, amelyből adatokat szeretne másolni, futtassa a következő T-SQL-t: 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER
    ```

4. Adja meg a Data Factory felügyelt identitás szükséges engedélyeket, ahogy általában az SQL-felhasználók és mások. Futtassa a következő kódot. További lehetőségeket [ebben a dokumentumban](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql?view=azuresqldb-mi-current)tartalmaz.

    ```sql
    ALTER ROLE [role name e.g. db_owner] ADD MEMBER [your Data Factory name]
    ```

5. Konfiguráljon egy Azure SQL Database Database felügyelt példányhoz csatolt szolgáltatást az Azure Data Factoryban.

**Példa: felügyelt identitáshitelesítést használ**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": "Data Source=<hostname,port>;Initial Catalog=<databasename>;"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálására használható szakaszok és tulajdonságok teljes listáját az adatkészletekről szóló cikkben olvashatja. Ez a szakasz az Azure SQL Database felügyelt példány adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Az Azure SQL Database felügyelt példányának adatok másolásához és onnan történő másolásához a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | Az adatkészlet típustulajdonságát **Az AzureSqlMITable**beállításra kell állítani. | Igen |
| Séma | A séma neve. |Nem a forráshoz, Igen a mosogatóhoz  |
| tábla | A tábla/nézet neve. |Nem a forráshoz, Igen a mosogatóhoz  |
| tableName | A sémával rendelkező tábla/nézet neve. Ez a tulajdonság a visszamenőleges kompatibilitás érdekében támogatott. Az új számítási `schema` `table`feladatokhoz használja és használja. | Nem a forráshoz, Igen a mosogatóhoz |

**Példa**

```json
{
    "name": "AzureSqlMIDataset",
    "properties":
    {
        "type": "AzureSqlMITable",
        "linkedServiceName": {
            "referenceName": "<Managed Instance linked service name>",
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

A tevékenységek definiálására használható szakaszok és tulajdonságok teljes listáját a [Folyamatok](concepts-pipelines-activities.md) című cikkben olvashatja. Ez a szakasz az Azure SQL Database felügyelt példány forrása és fogadója által támogatott tulajdonságok listáját tartalmazza.

### <a name="azure-sql-database-managed-instance-as-a-source"></a>Azure SQL Database felügyelt példány forrásként

Az Azure SQL Database felügyelt példányából származó adatok másolásához a másolási tevékenység forrásszakaszában a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenységforrás típustulajdonságát **sqlMISource**-re kell állítani. | Igen |
| sqlReaderQuery |Ez a tulajdonság az egyéni SQL-lekérdezést használja az adatok olvasásához. Például: `select * from MyTable`. |Nem |
| sqlReaderStoredProcedureName |Ez a tulajdonság annak a tárolt eljárásnak a neve, amely a forrástáblából olvassa be az adatokat. Az utolsó SQL utasításnak SELECT utasításnak kell lennie a tárolt eljárásban. |Nem |
| storedProcedureParameters |Ezek a paraméterek a tárolt eljáráshoz tartoznak.<br/>Az engedélyezett értékek név- vagy értékpárok. A paraméterek nevének és burkolatának meg kell egyeznie a tárolt eljárásparamétereinek nevével és burkolatával. |Nem |
| isolationLevel | Megadja az SQL-forrás tranzakciózárolási viselkedését. Az engedélyezett értékek a következők: **ReadCommitted** (alapértelmezett), **ReadUncommitted**, **RepeatableRead**, **Serializable**, **Snapshot**. További részletekért olvassa el ezt a [dokit.](https://docs.microsoft.com/dotnet/api/system.data.isolationlevel) | Nem |

**Vegye figyelembe a következő szempontokat:**

- Ha **az sqlReaderQuery** meg van adva az **SqlMISource-hoz,** a másolási tevékenység futtatja ezt a lekérdezést a felügyelt példány forrásával az adatok lekérni. A tárolt eljárást az **sqlReaderStoredProcedureName** és a **storedProcedureParameters** megadásával is megadhatja, ha a tárolt eljárás paramétereket vesz igénybe.
- Ha nem adja meg sem az **sqlReaderQuery,** sem az **sqlReaderStoredProcedureName** tulajdonságot, a JSON adatkészlet "struktúra" szakaszában definiált oszlopokat használja a rendszer a lekérdezés létrehozásához. A `select column1, column2 from mytable` lekérdezés a felügyelt példányon fut. Ha az adatkészlet-definíciónem rendelkezik "struktúra" beállítással, a program a táblából az összes oszlopot kijelöli.

**Példa: SQL-lekérdezés használata**

```json
"activities":[
    {
        "name": "CopyFromAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Managed Instance input dataset name>",
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
                "type": "SqlMISource",
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
        "name": "CopyFromAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Managed Instance input dataset name>",
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
                "type": "SqlMISource",
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

### <a name="azure-sql-database-managed-instance-as-a-sink"></a>Azure SQL Database felügyelt példány fogadóként

> [!TIP]
> Tudjon meg többet a támogatott írási viselkedés, konfigurációk és ajánlott eljárások [az ajánlott eljárás az adatok betöltése az Azure SQL Database felügyelt példány.](#best-practice-for-loading-data-into-azure-sql-database-managed-instance)

Az Azure SQL Database felügyelt példányába való adatmásoláshoz a következő tulajdonságokat támogatja a másolási tevékenység fogadója szakasz:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenységfogadó típustulajdonságát **SqlMISink (SqlMISink**) tulajdonságra kell állítani. | Igen |
| writeBatchSize |Az SQL-táblába *kötegenként*beszúrandó sorok száma .<br/>Az engedélyezett értékek egész számok a sorok számához. Alapértelmezés szerint az Azure Data Factory dinamikusan határozza meg a megfelelő kötegméretet a sorméretealapján.  |Nem |
| writeBatchTimeout |Ez a tulajdonság megadja a kötegbehelyezési művelet befejezéséhez szükséges várakozási időt, mielőtt az időtúljárna.<br/>Az engedélyezett értékek az időtartományra vannak. Erre példa a "00:30:00", amely 30 perc. |Nem |
| preCopyScript |Ez a tulajdonság egy SQL-lekérdezést ad meg a másolási tevékenység futtatásához, mielőtt adatokat írna a felügyelt példányba. A példányfuttatáskor csak egyszer van meghívva. Ezzel a tulajdonsággal megtisztíthatja az előre betöltött adatokat. |Nem |
| sqlWriterDProcedureName | A tárolt eljárás neve, amely meghatározza a forrásadatok céltáblára történő alkalmazását. <br/>Ezt a tárolt eljárást *kötegenként kell meghívni.* A csak egyszer futó és a forrásadatokhoz semmi közük nem rendelkező műveletekhez, például a törléshez vagy a csonkoláshoz használja a `preCopyScript` tulajdonságot. | Nem |
| storedProcedureTableTypeParameterName |A tárolt eljárásban megadott táblatípus paraméterneve.  |Nem |
| sqlWriterTableType típus |A tárolt eljárásban használandó táblatípus neve. A másolási tevékenység elérhetővé teszi az áthelyezett adatokat egy ideiglenes táblában ezzel a táblatípussal. A tárolt eljáráskód ezután egyesítheti a másolt adatokat a meglévő adatokkal. |Nem |
| storedProcedureParameters |A tárolt eljárás paraméterei.<br/>Az engedélyezett értékek a név- és értékpárok. A paraméterek nevének és burkolatának meg kell egyeznie a tárolt eljárásparamétereinek nevével és burkolatával. | Nem |
| tableOption | Itt adható meg, hogy a program automatikusan hozza-e létre a fogadótáblát, ha nem létezik a forrásséma alapján. Az automatikus táblalétrehozása nem támogatott, ha a fogadó a tárolt eljárást adja meg, vagy a programozott másolási tevékenységben van konfigurálva. Az engedélyezett `none` értékek a `autoCreate`következők: (alapértelmezett), . |Nem |

**1. példa: Adatok hozzáfűzése**

```json
"activities":[
    {
        "name": "CopyToAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Managed Instance output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlMISink",
                "writeBatchSize": 100000,
                "tableOption": "autoCreate"
            }
        }
    }
]
```

**2. példa: Tárolt eljárás meghívása másolás közben**

További részletek A [tárolt eljárás meghívása sql mi fogadóból](#invoke-a-stored-procedure-from-a-sql-sink).

```json
"activities":[
    {
        "name": "CopyToAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Managed Instance output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlMISink",
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

## <a name="best-practice-for-loading-data-into-azure-sql-database-managed-instance"></a>Ajánlott eljárás az azure-beli SQL-adatbázis felügyelt példányába történő adatbetöltéshez

Amikor adatokat másol az Azure SQL Database felügyelt példányába, előfordulhat, hogy más írási viselkedésre van szükség:

- [Hozzáfűzés:](#append-data)A forrásadatok csak új rekordokat tartalmazó adatokat tartalmazó adatokat.
- [Upsert](#upsert-data): A forrásadatok beszúrásokat és frissítéseket is tartalmaznak.
- [Felülírás:](#overwrite-the-entire-table)Minden alkalommal újra szeretném tölteni a teljes dimenziótáblát.
- [Írjon egyéni logikával:](#write-data-with-custom-logic)További feldolgozásra van szükségem, mielőtt a végső beillesztés a céltáblába kerül. 

Tekintse meg a megfelelő szakaszokat, hogyan konfigurálhatja az Azure Data Factory és az ajánlott eljárásokat.

### <a name="append-data"></a>Adatok hozzáfűzése

Az adatok hozzáfűzése az Azure SQL Database felügyelt példány a fogadóösszekötő alapértelmezett viselkedése. Az Azure Data Factory egy tömeges beszúrással hatékonyan ír a táblába. Beállíthatja a forrást, és ennek megfelelően elsüllyesztheti a másolási tevékenységet.

### <a name="upsert-data"></a>Adatok beszúrása és frissítése (upsert)

**1. lehetőség:** Ha nagy mennyiségű adatot szeretne másolni, a következő módszerrel végezze el a upsert et: 

- Először egy [ideiglenes tábla](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables) segítségével tömegesen töltse be az összes rekordot a másolási tevékenység használatával. Mivel az ideiglenes táblákon végzett műveletek nem naplózva vannak, több millió rekordot tölthet be másodpercek alatt.
- Futtasson egy tárolt eljárástevékenységet az Azure Data Factoryban [a MERGE](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) vagy INSERT/UPDATE utasítás alkalmazásához. Az ideiglenes tábla forrásként való végrehajtása az összes frissítést vagy beszúrást egyetlen tranzakcióként hajthatja végre. Ily módon csökken az oda- és naplóműveletek száma. A tárolt eljárási tevékenység végén az ideiglenes tábla csonkolható, hogy készen álljon a következő upsert ciklusra.

Például az Azure Data Factoryban létrehozhat egy folyamatot egy **tárolt eljárás tevékenységgel**láncolt **másolási tevékenységgel.** Az előbbi a forrástárolóból származó adatokat egy ideiglenes táblába másolja, például **##UpsertTempTable**, az adatkészlet táblaneveként. Ezután az utóbbi meghívja a tárolt eljárást az ideiglenes táblából származó forrásadatok nak a céltáblába történő egyesítéséhez és az ideiglenes tábla karbantartásához.

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

**2. lehetőség:** Azt is választhatja, hogy [egy tárolt eljárást hív meg egy másolási tevékenységen belül.](#invoke-a-stored-procedure-from-a-sql-sink) Ez a megközelítés a forrástábla minden egyes sorát futtatja ahelyett, hogy tömeges beszúrást használna alapértelmezett megközelítésként a másolási tevékenységben, amely nem megfelelő a nagy méretű upsert számára.

### <a name="overwrite-the-entire-table"></a>A teljes tábla felülírása

A **preCopyScript** tulajdonságot egy másolási tevékenységgyűjtőben konfigurálhatja. Ebben az esetben minden egyes futó másolási tevékenység hez az Azure Data Factory először futtatja a parancsfájlt. Ezután futtatja a másolatot az adatok beszúrásához. Ha például felül szeretné írni a teljes táblát a legújabb adatokkal, adjon meg egy parancsfájlt, amely először törli az összes rekordot, mielőtt tömegesen betöltené az új adatokat a forrásból.

### <a name="write-data-with-custom-logic"></a>Adatok írása egyéni logikával

Az adatok írásának lépései egyéni logikával [hasonlóak a Upsert-adatok](#upsert-data) szakaszban leírtakhoz. Ha a forrásadatok végső beillesztése előtt további feldolgozást kell alkalmaznia a céltáblába, a nagy léptékű, a következő két dolgot teheti meg: 

- Töltsön be egy ideiglenes táblába, majd hívja meg a tárolt eljárást.
- Tárolt eljárás meghívása a másolás során.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a><a name="invoke-a-stored-procedure-from-a-sql-sink"></a>Tárolt eljárás meghívása SQL-fogadóból

Amikor adatokat másol az Azure SQL Database felügyelt példányba, konfigurálhatja és meghívhat egy felhasználó által megadott tárolt eljárást további paraméterekkel. A tárolt eljárás szolgáltatás kihasználja a [tábla értékű paramétereket.](https://msdn.microsoft.com/library/bb675163.aspx)

> [!TIP]
> A tárolt eljárás meghívása az adatsort sorról sorra dolgozza fel, nem pedig tömeges művelet használatával, amelyet nem ajánlunk nagyméretű másoláshoz. További információ az [Azure SQL Database felügyelt példányba történő adatbetöltéssel kapcsolatos gyakorlati tanácsokról.](#best-practice-for-loading-data-into-azure-sql-database-managed-instance)

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

3. Az Azure Data Factoryban határozza meg az **SQL MI-fogadó** szakaszt a másolási tevékenységben az alábbiak szerint:

    ```json
    "sink": {
        "type": "SqlMISink",
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

## <a name="data-type-mapping-for-azure-sql-database-managed-instance"></a>Adattípus-leképezés az Azure SQL Database felügyelt példányához

Amikor az adatokat az Azure SQL Database felügyelt példányába másolja, a következő leképezéseket használja az Azure SQL Database felügyelt példányadattípusai az Azure Data Factory köztes adattípusai. Ha meg szeretné tudni, hogy a másolási tevékenység hogyan lesz leképezve a forrássémáról és az adattípusról a fogadóra, olvassa el a [Séma- és adattípus-hozzárendelések című témakört.](copy-activity-schema-and-type-mapping.md)

| Azure SQL Database felügyelt példány adattípusa | Az Azure Data Factory köztes adattípusa |
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

## <a name="next-steps"></a>További lépések
A forrásként támogatott és fogadóként az Azure Data Factory másolási tevékenysége által támogatott adattárak listáját a [Támogatott adattárak című témakörben tetszhet.](copy-activity-overview.md#supported-data-stores-and-formats)
