---
title: Adatok másolása Azure SQL Database felügyelt példányról a Azure Data Factory használatával | Microsoft Docs
description: Megtudhatja, hogyan helyezheti át az adatait Azure SQL Database felügyelt példányra Azure Data Factory használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: jingwang
ms.openlocfilehash: 0cc7313531e92aa0f57b09a9252902848297bdbf
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2019
ms.locfileid: "69996664"
---
# <a name="copy-data-to-and-from-azure-sql-database-managed-instance-by-using-azure-data-factory"></a>Adatok másolása Azure SQL Database felügyelt példányról a Azure Data Factory használatával

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok Azure SQL Database felügyelt példányba történő másolásához. A másolási [tevékenység](copy-activity-overview.md) áttekintő cikkében található, amely a másolási tevékenység általános áttekintését jeleníti meg.

## <a name="supported-capabilities"></a>Támogatott képességek

Az adatok Azure SQL Database felügyelt példányról bármely támogatott fogadó adattárba másolhatók. A felügyelt példányba bármilyen támogatott forrás adattárból is másolhat adatok. A másolási tevékenység által forrásként és fogadóként támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblázatban tekintheti meg.

Pontosabban, ez a Azure SQL Database felügyelt példány-összekötő támogatja a következőket:

- Az adatok másolása SQL-hitelesítéssel és Azure Active Directory (Azure AD) alkalmazás-jogkivonat-hitelesítéssel az Azure-erőforrások egyszerű szolgáltatásával vagy felügyelt identitásával.
- Forrásként egy SQL-lekérdezés vagy tárolt eljárás használatával beolvashatja az adatokból.
- Fogadóként, az adatsorok hozzáfűzésével vagy egy tárolt eljárás meghívásával egyéni logikával a másolás során.

>[!NOTE]
>Ez az összekötő [](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-mi-current) nem támogatja Azure SQL Database felügyelt példány Always Encryptedét. A megoldáshoz használhat egy [általános ODBC](connector-odbc.md) -összekötőt és egy SQL Server ODBC-illesztőt a saját üzemeltetésű integrációs modulon keresztül. Kövesse [ezt az útmutatót](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-mi-current) az ODBC-illesztőprogram letöltésével és a kapcsolatok karakterlánc-beállításaival.

>[!NOTE]
>Ez az összekötő jelenleg nem támogatja az egyszerű szolgáltatásnév és a felügyelt identitások hitelesítését. A megoldáshoz válasszon egy Azure SQL Database-összekötőt, és adja meg manuálisan a felügyelt példány kiszolgálóját.

## <a name="prerequisites"></a>Előfeltételek

A Azure SQL Database felügyelt példány [nyilvános végpontjának](../sql-database/sql-database-managed-instance-public-endpoint-securely.md)eléréséhez használhat egy Azure Data Factory felügyelt Azure Integration Runtime programot. Ügyeljen arra, hogy engedélyezze a nyilvános végpontot, és engedélyezze a nyilvános végponti forgalmat a hálózati biztonsági csoporton, hogy Azure Data Factory csatlakozni tudjanak az adatbázishoz. További információkért tekintse meg [ezt az útmutatót](../sql-database/sql-database-managed-instance-public-endpoint-configure.md).

A Azure SQL Database felügyelt példány privát végpontjának eléréséhez állítson be egy [saját](create-self-hosted-integration-runtime.md) üzemeltetésű integrációs modult, amely hozzáfér az adatbázishoz. Ha a saját üzemeltetésű integrációs modult ugyanabban a virtuális hálózatban hozza létre, mint a felügyelt példányát, győződjön meg arról, hogy az integrációs modul számítógépe a felügyelt példánytól eltérő alhálózaton van. Ha a saját üzemeltetésű integrációs modult a felügyelt példánytól eltérő virtuális hálózaton helyezi üzembe, a virtuális hálózati kapcsolatok vagy virtuális hálózatok használatával virtuális hálózati kapcsolatot is használhat. További információ: [az alkalmazás Összekötése Azure SQL Database felügyelt példányhoz](../sql-database/sql-database-managed-instance-connect-app.md).

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük azokat a tulajdonságokat, amelyek a Azure SQL Database felügyelt példány-összekötőhöz tartozó Azure Data Factory-entitások definiálásához használatosak.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A Azure SQL Database felügyelt példány társított szolgáltatása a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A Type tulajdonságot **AzureSqlMI**értékre kell beállítani. | Igen |
| connectionString |Ez a tulajdonság határozza meg az SQL-hitelesítés használatával a felügyelt példányhoz való kapcsolódáshoz szükséges **ConnectionString** -információkat. További információkért tekintse meg az alábbi példákat. <br/>Az alapértelmezett port a 1433-as számú. Ha Azure SQL Database felügyelt példányt használ nyilvános végponttal, explicit módon megadhatja a 3342-as portot.<br>A mező megjelölése **SecureString** -ként, hogy biztonságosan tárolja Azure Data Factoryban. A jelszó Azure Key Vaultban is elhelyezhető. Ha SQL-hitelesítéssel rendelkezik, húzza `password` ki a konfigurációt a kapcsolatok sztringből. További információ: a táblázatot követő JSON-példa és a [hitelesítő adatok tárolása Azure Key Vaultban](store-credentials-in-key-vault.md). |Igen |
| servicePrincipalId | Adja meg az alkalmazás ügyfél-azonosítót. | Igen, ha Azure AD-hitelesítést használ egy egyszerű szolgáltatással |
| servicePrincipalKey | Adja meg az alkalmazáskulcsot. A mező megjelölése **SecureString** -ként a biztonságos tárolás Azure Data Factory vagy [Azure Key Vault tárolt titkos](store-credentials-in-key-vault.md)kód hivatkozása alapján. | Igen, ha Azure AD-hitelesítést használ egy egyszerű szolgáltatással |
| tenant | Adja meg a bérlői adatokat, például a tartománynevet vagy a bérlő AZONOSÍTÓját, amely alatt az alkalmazás található. Lekéri a Azure Portal jobb felső sarkában lévő egér fölé. | Igen, ha Azure AD-hitelesítést használ egy egyszerű szolgáltatással |
| connectVia | Ez [](concepts-integration-runtime.md) az integrációs modul az adattárhoz való kapcsolódásra szolgál. Használhatja a saját üzemeltetésű integrációs modult vagy egy Azure Integration Runtime-t, ha a felügyelt példány nyilvános végponttal rendelkezik, és lehetővé teszi Azure Data Factory számára a hozzáférését. Ha nincs megadva, a rendszer az alapértelmezett Azure Integration Runtime-t használja. |Igen |

Különböző hitelesítési típus tekintse meg a következő szakaszok az Előfeltételek és a JSON-minták, illetve:

- [SQL-hitelesítés](#sql-authentication)
- [Azure AD-alkalmazás-jogkivonat hitelesítése: Egyszerű szolgáltatásnév](#service-principal-authentication)
- [Azure AD-alkalmazás-jogkivonat hitelesítése: Felügyelt identitások az Azure-erőforrásokhoz](#managed-identity)

### <a name="sql-authentication"></a>SQL-hitelesítés

**1. példa: SQL-hitelesítés használata**

```json
{
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<hostname,port>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
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
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<hostname,port>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;"
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

### <a name="service-principal-authentication"></a>Egyszerű szolgáltatásnév hitelesítése

A szolgáltatás egyszerű Azure AD-alapú hitelesítésének használatához kövesse az alábbi lépéseket:

1. A [felügyelt példányok Azure Active Directory rendszergazdájának](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance)üzembe helyezéséhez kövesse az alábbi lépéseket.

2. [Hozzon létre egy Azure Active Directory alkalmazást](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) a Azure Portal. Jegyezze fel az alkalmazás nevét és a következő értékeket, amelyek meghatározzák a társított szolgáltatást:

    - Alkalmazásazonosító
    - Alkalmazáskulcs
    - Bérlőazonosító

3. [Hozzon létre bejelentkezési adatokat](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) a Azure Data Factory felügyelt identitáshoz. SQL Server Management Studio (SSMS) alkalmazásban kapcsolódjon a felügyelt példányhoz egy rendszergazdaként szolgáló SQL Server-fiókkal. A **Master** adatbázisban futtassa a következő T-SQL-T:

    ```sql
    CREATE LOGIN [your application name] FROM EXTERNAL PROVIDER
    ```

4. [Hozzon létre tárolt adatbázis-felhasználókat](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) a Azure Data Factory felügyelt identitáshoz. Kapcsolódjon az adatbázishoz a vagy a rendszerből, amelyről másolni kívánja az adatait, futtassa a következő T-SQL-T: 
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER
    ```

5. Adja meg a Data Factory felügyelt identitáshoz szükséges engedélyeket, ahogyan az SQL-felhasználók és mások számára általában nem. Futtassa a következő kódot. További lehetőségekért tekintse meg [ezt a dokumentumot](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql?view=azuresqldb-mi-current).

    ```sql
    ALTER ROLE [role name e.g. db_owner] ADD MEMBER [your application name]
    ```

6. Azure SQL Database felügyelt példány társított szolgáltatásának konfigurálása Azure Data Factoryban.

**Példa: egyszerű szolgáltatásnév hitelesítésének használata**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<hostname,port>;Initial Catalog=<databasename>;"
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

Az adatok előállítója felügyelt identitással társítható az [Azure-erőforrások számára](data-factory-service-identity.md) , amelyek az adott adatelőállítót jelölik. Ezt a felügyelt identitást Azure SQL Database felügyelt példányok hitelesítéséhez használhatja. A kijelölt gyár ezen identitás használatával férhet hozzá az adatbázishoz, és másolhatja azokat az adatbázisból.

A felügyelt identitásos hitelesítés használatához kövesse az alábbi lépéseket.

1. A [felügyelt példányok Azure Active Directory rendszergazdájának](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance)üzembe helyezéséhez kövesse az alábbi lépéseket.

2. [Hozzon létre bejelentkezési adatokat](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) a Azure Data Factory felügyelt identitáshoz. SQL Server Management Studio (SSMS) alkalmazásban kapcsolódjon a felügyelt példányhoz egy rendszergazdaként szolgáló SQL Server-fiókkal. A **Master** adatbázisban futtassa a következő T-SQL-T:

    ```sql
    CREATE LOGIN [your Data Factory name] FROM EXTERNAL PROVIDER
    ```

3. [Hozzon létre tárolt adatbázis-felhasználókat](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) a Azure Data Factory felügyelt identitáshoz. Kapcsolódjon az adatbázishoz a vagy a rendszerből, amelyről másolni kívánja az adatait, futtassa a következő T-SQL-T: 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER
    ```

4. Adja meg a Data Factory felügyelt identitáshoz szükséges engedélyeket, ahogyan az SQL-felhasználók és mások számára általában nem. Futtassa a következő kódot. További lehetőségekért tekintse meg [ezt a dokumentumot](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql?view=azuresqldb-mi-current).

    ```sql
    ALTER ROLE [role name e.g. db_owner] ADD MEMBER [your Data Factory name]
    ```

5. Azure SQL Database felügyelt példány társított szolgáltatásának konfigurálása Azure Data Factoryban.

**Példa: felügyelt identitásos hitelesítés használata**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<hostname,port>;Initial Catalog=<databasename>;"
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

Az adatkészletek definiálásához használható csoportok és tulajdonságok teljes listáját az adatkészletek című cikkben találja. Ez a szakasz a Azure SQL Database felügyelt példány adatkészlete által támogatott tulajdonságok listáját tartalmazza.

Az adatok Azure SQL Database felügyelt példányba való másolásához a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | Az adatkészlet Type tulajdonságát **AzureSqlMITable**értékre kell állítani. | Igen |
| tableName |Ez a tulajdonság annak a táblának vagy nézetnek a neve, amelyre a társított szolgáltatás hivatkozik. | Nincs forrás, a fogadó Igen |

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
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálásához használható csoportok és tulajdonságok teljes listáját a [folyamatok](concepts-pipelines-activities.md) című cikkben találja. Ez a szakasz a Azure SQL Database felügyelt példány forrása és fogadója által támogatott tulajdonságok listáját tartalmazza.

### <a name="azure-sql-database-managed-instance-as-a-source"></a>Felügyelt példány Azure SQL Database forrásaként

Az adatok Azure SQL Database felügyelt példányból történő másolásához a másolási tevékenység forrása szakaszban a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának Type tulajdonságát **SqlMISource**értékre kell állítani. | Igen |
| sqlReaderQuery |Ez a tulajdonság az egyéni SQL-lekérdezést használja az adatolvasáshoz. Például: `select * from MyTable`. |Nem |
| sqlReaderStoredProcedureName |Ez a tulajdonság annak a tárolt eljárásnak a neve, amely beolvassa az adatokat a forrás táblából. Az utolsó SQL-utasítást a tárolt eljárás a SELECT utasítással kell lennie. |Nem |
| storedProcedureParameters |Ezek a paraméterek a tárolt eljáráshoz tartoznak.<br/>Megengedett értékek: neve vagy értéke párokat. A paraméterek nevének és burkolatának meg kell egyeznie a tárolt eljárás paramétereinek nevével és házával. |Nem |

**Vegye figyelembe a következő szempontokat:**

- Ha a **SqlMISource** **sqlReaderQuery** van megadva, a másolási tevékenység futtatja ezt a lekérdezést a felügyelt példány forrásán az adatgyűjtéshez. Megadhat egy tárolt eljárást is a **sqlReaderStoredProcedureName** és a **storedProcedureParameters** megadásával, ha a tárolt eljárás paraméterekkel rendelkezik.
- Ha nem ad meg **sqlReaderQuery** vagy **sqlReaderStoredProcedureName** tulajdonságot, a rendszer a JSON-adatkészlet "struktúra" szakaszában meghatározott oszlopokat használja a lekérdezések létrehozásához. A lekérdezés `select column1, column2 from mytable` a felügyelt példányon fut. Ha az adatkészlet definíciója nem rendelkezik "Structure" értékkel, az összes oszlop ki van választva a táblából.

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

### <a name="azure-sql-database-managed-instance-as-a-sink"></a>Felügyelt példány Azure SQL Database fogadóként

> [!TIP]
> További információ a támogatott írási viselkedésekről, konfigurációkról és ajánlott eljárásokról az [adatok Azure SQL Database felügyelt példányba](#best-practice-for-loading-data-into-azure-sql-database-managed-instance)való betöltéséhez ajánlott eljárások közül.

Az Adatmásolás Azure SQL Database felügyelt példányra történő másolásához a másolási tevékenység fogadója szakasz a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység fogadójának Type tulajdonságát **SqlMISink**értékre kell állítani. | Igen |
| writeBatchSize |A kötegekben az SQL-táblába beszúrandósorok száma.<br/>Az engedélyezett értékek a sorok számának egésze. Alapértelmezés szerint a Azure Data Factory dinamikusan meghatározza a megfelelő batch-méretet a sor mérete alapján.  |Nem |
| writeBatchTimeout |Ez a tulajdonság határozza meg, hogy a kötegelt beszúrási művelet várakozási ideje befejeződjön, mielőtt időtúllépés történt.<br/>Az engedélyezett értékek a TimeSpan vonatkoznak. Ilyen például a "00:30:00", amely 30 percet vesz igénybe. |Nem |
| preCopyScript |Ez a tulajdonság határozza meg a másolási tevékenység futtatásához szükséges SQL-lekérdezést, mielőtt az adatbevitel a felügyelt példányba történik. A rendszer csak egyszer hívja meg a példányt. Ezt a tulajdonságot használhatja az előre betöltött adatértékek törléséhez. |Nem |
| sqlWriterStoredProcedureName | Annak a tárolt eljárásnak a neve, amely meghatározza, hogy a forrásadatok hogyan alkalmazhatók a célként megadott táblába. <br/>Ezt a tárolt eljárást *Batch*-ként kell meghívni. Olyan műveletekhez, amelyek csak egyszer futnak, és nem kell megtenniük a forrásadatokat, például törlés vagy csonkítás, használja `preCopyScript` a tulajdonságot. | Nem |
| storedProcedureTableTypeParameterName |A tárolt eljárásban megadott tábla típusú paraméter neve.  |Nem |
| sqlWriterTableType |A tárolt eljárásban használandó táblanév neve. A másolási tevékenység lehetővé teszi az áthelyezett adatáthelyezést egy ideiglenes táblában, amely ebben a táblázatban szerepel. A tárolt eljárási kód ezután egyesítheti a folyamatban lévő adattal másolt adatfájlokat. |Nem |
| storedProcedureParameters |A tárolt eljárás paraméterei.<br/>Az engedélyezett értékek név és érték párok. Nevek és a kis-és a paraméterek meg kell egyeznie a neveket és a kis-és nagybetűhasználatot, a tárolt eljárás paraméterértékeinek. | Nem |

**1. példa: Adathozzáfűzés**

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
                "writeBatchSize": 100000
            }
        }
    }
]
```

**2. példa: Tárolt eljárás meghívása a másolás során**

További információ: [tárolt eljárás meghívása egy SQL mi](#invoke-a-stored-procedure-from-a-sql-sink)fogadóból.

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

## <a name="best-practice-for-loading-data-into-azure-sql-database-managed-instance"></a>Ajánlott eljárás az adatAzure SQL Database felügyelt példányba való betöltéséhez

Az Adatmásolás Azure SQL Database felügyelt példányba történő másolásakor különböző írási viselkedésre lehet szükség:

- [Hozzáfűzés](#append-data): A forrásadatok csak új rekordokkal rendelkeznek.
- [Upsert](#upsert-data): A forrásadatok a lapkákkal és a frissítésekkel is rendelkeznek.
- [Felülírás](#overwrite-the-entire-table): Minden alkalommal újra szeretném tölteni a teljes dimenzió táblát.
- [Írás Egyéni logikával](#write-data-with-custom-logic): További feldolgozásra van szükség a végső Beszúrás előtt a céltáblaba. 

Tekintse meg a megfelelő részeit, hogy miként konfigurálható a Azure Data Factory és az ajánlott eljárásokban.

### <a name="append-data"></a>Adathozzáfűzés

Az adathozzáfűzés a Azure SQL Database felügyelt példányok fogadó összekötője alapértelmezett viselkedése. A Azure Data Factory egy tömeges beszúrást végez, hogy hatékonyan írjon a táblába. A forrást és a fogadót ennek megfelelően állíthatja be a másolási tevékenységbe.

### <a name="upsert-data"></a>Adatok beszúrása és frissítése (upsert)

**1. lehetőség:** Ha nagy mennyiségű adattal szeretne másolni, a következő megközelítéssel hajthat végre egy upsert: 

- Először használjon egy [ideiglenes táblázatot](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables) az összes rekord tömeges betöltéséhez a másolási tevékenység használatával. Mivel a rendszer nem naplózza az ideiglenes táblák műveleteit, másodpercek alatt több millió rekordot is betölt.
- Egy tárolt eljárási tevékenység futtatása a Azure Data Factoryban [egyesítési](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) vagy INSERT/Update utasítás alkalmazásához. A temp táblát használja forrásként az összes frissítés vagy Beszúrás egyetlen tranzakcióként való végrehajtásához. Így csökken a kerekítési és a naplózási műveletek száma. A tárolt eljárási tevékenység végén a temp tábla lerövidíthető, hogy készen álljon a következő upsert ciklusra.

Azure Data Factory például létrehozhat egy **másolási tevékenységgel** rendelkező folyamatot egy **tárolt eljárási tevékenységgel**. A korábbi adatokat a forrás tárolójából egy ideiglenes táblába másolták, például: **# #UpsertTempTable**, mint a tábla neve az adatkészletben. Ezután az utóbbi egy tárolt eljárást hív meg, hogy egyesítse a forrás adatait a temp táblából a célként megadott táblába, és törölje a temp táblát.

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

**2. lehetőség:** Azt is megteheti, hogy egy [másolási tevékenységen belül meghívja a tárolt eljárást](#invoke-a-stored-procedure-from-a-sql-sink). Ez a módszer a forrás tábla minden egyes sorát futtatja ahelyett, hogy a másolási tevékenységben a tömeges beszúrást használja alapértelmezett megközelítésként, ami nem megfelelő a nagyméretű upsert.

### <a name="overwrite-the-entire-table"></a>A teljes táblázat felülírása

A **preCopyScript** tulajdonságot egy másolási tevékenység fogadójában is konfigurálhatja. Ebben az esetben minden futó másolási tevékenységnél először Azure Data Factory futtatja a parancsfájlt. Ezután futtatja a másolatot az adatbeszúráshoz. Ha például felül szeretné írni a teljes táblázatot a legfrissebb adatokkal, adjon meg egy parancsfájlt, hogy először törölje az összes rekordot, mielőtt tömegesen betölti az új adatokat a forrásból.

### <a name="write-data-with-custom-logic"></a>Az adatírás egyéni logikával

Az egyéni logikával történő adatírás lépései hasonlóak az [Upsert](#upsert-data) -adatszakaszban leírt lépésekhez. Ha további feldolgozást kell alkalmaznia a forrásadatok végső beszúrása előtt a céltáblaba, nagy méret esetén a következő két dolog közül választhat: 

- Töltsön be egy ideiglenes táblába, majd hívja meg a tárolt eljárást.
- Tárolt eljárás meghívása a másolás során.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a>Tárolt eljárás meghívása egy SQL-gyűjtőből

Az adatok Azure SQL Database felügyelt példányba való másolása esetén a felhasználó által megadott tárolt eljárás további paraméterekkel is konfigurálható és meghívható. A tárolt eljárás funkció kihasználja a [tábla értékű paraméterek](https://msdn.microsoft.com/library/bb675163.aspx)előnyeit.

> [!TIP]
> A tárolt eljárás meghívása az adatsort soronként dolgozza fel egy tömeges művelet helyett, amelyet nem ajánlott nagyméretű másolásra használni. További tudnivalók az [adatok Azure SQL Database felügyelt példányba](#best-practice-for-loading-data-into-azure-sql-database-managed-instance)való betöltésének ajánlott eljárásairól.

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

3. A Azure Data Factory a másolási tevékenységben adja meg az **SQL mi mosogató** szakaszt a következőképpen:

    ```json
    "sink": {
        "type": "SqlMISink",
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

## <a name="data-type-mapping-for-azure-sql-database-managed-instance"></a>Azure SQL Database felügyelt példány adattípusának leképezése

Az adatok Azure SQL Database felügyelt példányra való másolásakor a rendszer a következő leképezéseket használja Azure SQL Database felügyelt példányok adattípusairól Azure Data Factory köztes adattípusokra. Annak megismeréséhez, hogy a másolási tevékenység hogyan legyen leképezve a forrás sémából és az adattípusból a fogadóba, lásd: [séma és adattípus](copy-activity-schema-and-type-mapping.md)-leképezések.

| Azure SQL Database felügyelt példány adattípusa | Azure Data Factory időközi adattípus |
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

## <a name="next-steps"></a>További lépések
A Azure Data Factory a másolási tevékenység által forrásként és nyelőként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md##supported-data-stores-and-formats).
