---
title: Adatok másolása vagy az Azure SQL Database a Data Factory használatával |} Microsoft Docs
description: Ismerje meg az adatok másolása támogatott forráshierarchiából adatokat tárolja az Azure SQL Database vagy az SQL-adatbázis támogatott fogadó adattárolókhoz Data Factory használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/05/2018
ms.author: jingwang
ms.openlocfilehash: 5287a1d1f09a7057590b455c14aa7f70128ad7fa
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37053641"
---
# <a name="copy-data-to-or-from-azure-sql-database-by-using-azure-data-factory"></a>Másolja a adatok vagy az Azure SQL Database az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Select the version of Data Factory service you use:"]
> * [1-es verziójával](v1/data-factory-azure-sql-connector.md)
> * [Aktuális verzió](connector-azure-sql-database.md)

Ez a cikk azt ismerteti, hogyan másolási tevékenység használható az Azure Data Factory másolja az adatokat, vagy az Azure SQL Database. Buildekről nyújtanak a [másolási tevékenység áttekintése](copy-activity-overview.md) cikk, amelynek során a másolási tevékenység általános áttekintést.

## <a name="supported-capabilities"></a>Támogatott képességei

Másolhat adatokat a vagy az Azure SQL Database bármely támogatott fogadó adattár. És átmásolhatja adatok bármely támogatott forrás adattár az Azure SQL Database. A másolási tevékenység által támogatott adatforrások vagy mosdók adattárolókhoz listájáért lásd: a [adatokról és formátumok támogatott](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Az Azure SQL Database összekötő kifejezetten, ezeket a funkciókat támogatja:

- Másolja az adatokat a szolgáltatás egyszerű vagy a felügyelt szolgáltatás identitásának (MSI) SQL-hitelesítést és az Azure Active Directory (Azure AD) alkalmazás tokent használó hitelesítés használatával.
- Forrásként egy SQL-lekérdezést vagy tárolt eljárás használatával adatainak beolvasása.
- A fogadó adatok hozzáfűzése a célként megadott táblája vagy egyéni logikával tárolt eljárás meghívása a másolás során.

> [!IMPORTANT]
> Ha az adatok másolása Azure Data Factory integrációs futásidejű használatával, konfigurálja egy [Azure SQL-kiszolgáló tűzfal](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) érdekében, hogy a Azure-szolgáltatásokat a kiszolgáló.
> Ha egy önálló üzemeltetett integrációs futásidejű használatával adatok másolása az Azure SQL server tűzfal engedélyezze a megfelelő IP-címtartomány megadása A tartományba beletartozik a gép IP-cím segítségével csatlakozzon az Azure SQL Database.

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok részletesen bemutatják adat-előállító tartozó entitások meghatározásához az Azure SQL Database-összekötők használt tulajdonságokat.

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok

Ezeket a tulajdonságokat az Azure SQL Database kapcsolódó szolgáltatás támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A **típus** tulajdonság értékre kell állítani **AzureSqlDatabase**. | Igen |
| connectionString | Adja meg az Azure SQL Database-példány való kapcsolódáshoz szükséges adatokat a **connectionString** tulajdonság. Jelölje meg ebben a mezőben, mint egy **SecureString** tárolja biztonságos helyen, a Data factoryban vagy [hivatkozik az Azure Key Vault tárolt titkos kulcs](store-credentials-in-key-vault.md). | Igen |
| servicePrincipalId | Adja meg az alkalmazás ügyfél-azonosítót. | Igen, az Azure AD-alapú hitelesítés használatakor egy szolgáltatásnevet. |
| servicePrincipalKey | Adja meg az alkalmazás kulcsot. Jelölje meg ebben a mezőben, mint egy **SecureString** tárolja biztonságos helyen, a Data factoryban vagy [hivatkozik az Azure Key Vault tárolt titkos kulcs](store-credentials-in-key-vault.md). | Igen, az Azure AD-alapú hitelesítés használatakor egy szolgáltatásnevet. |
| bérlő | Adja meg a bérlői adatokat (tartomány nevét vagy a bérlő azonosító) alatt az alkalmazás található. Az Azure-portál jobb felső sarkában az egér rámutató lekérni azt. | Igen, az Azure AD-alapú hitelesítés használatakor egy szolgáltatásnevet. |
| connectVia | A [integrációs futásidejű](concepts-integration-runtime.md) csatlakozni az adattárolóhoz használandó. Azure integrációs futásidejű vagy egy önálló üzemeltetett integrációs futásidejű akkor használhatja, ha egy magánhálózaton található az adattárban. Ha nincs megadva, akkor használja az alapértelmezett Azure integrációs futásidejű. | Nem |

Különböző hitelesítési típusok tekintse meg az alábbi szakaszok az Előfeltételek és a JSON-mintákat, illetve:

- [SQL-hitelesítés](#sql-authentication)
- [Az Azure AD alkalmazás jogkivonat-alapú hitelesítés: egyszerű szolgáltatásnév](#service-principal-authentication)
- [Az Azure AD alkalmazás jogkivonat-alapú hitelesítés: felügyelt identitás](#managed-service-identity-authentication)

### <a name="sql-authentication"></a>SQL-hitelesítés

#### <a name="linked-service-example-that-uses-sql-authentication"></a>SQL-hitelesítést használó társított szolgáltatás – példa

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
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

### <a name="service-principal-authentication"></a>Egyszerű szolgáltatásnév hitelesítése

A szolgáltatás az Azure AD-alapú alkalmazás tokent használó hitelesítés használatához kövesse az alábbi lépéseket:

1. **[Hozzon létre egy Azure Active Directory-alkalmazás](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application)**  Azure-portálról. Jegyezze fel az alkalmazás nevét és a következő értékek, amelyek meghatározzák a társított szolgáltatás:

    - Alkalmazásazonosító
    - Alkalmazás kulcs
    - Bérlőazonosító

2. **[Egy Azure Active Directory-rendszergazda kiépítése](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)**  az Azure SQL-kiszolgáló, az Azure portálon, ha még nem tette. Az Azure AD-rendszergazdának kell lennie, az Azure AD-felhasználó vagy az Azure AD-csoport, de nem lehet egy egyszerű szolgáltatást. Ebben a lépésben végezhető el, hogy a következő lépésben használhatja az Azure AD identity tartalmazott adatbázis-felhasználó a szolgáltatás egyszerű létrehozásához.

3. **[Hozzon létre a tartalmazott adatbázis-felhasználók](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)**  a szolgáltatás rendszerbiztonsági tag. Csatlakoztassa az adatbázist, vagy amelyek át kívánja másolni az adatok eszközökkel SSMS, például egy Azure AD-identitás, amely rendelkezik legalább bármely felhasználó ALTER engedéllyel. Futtassa a következő T-SQL: 
    
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. **Adja meg a szükséges engedélyekkel a szolgáltatás egyszerű** szokásos módon SQL felhasználók vagy mások számára. Futtassa a következő kódot:

    ```sql
    EXEC sp_addrolemember [role name], [your application name];
    ```

5. **Egy Azure SQL Database társított szolgáltatás beállítása** az Azure Data Factory.


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Szolgáltatás egyszerű hitelesítést használó társított szolgáltatás – példa

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
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

### <a name="managed-service-identity-authentication"></a>Felügyelt Szolgáltatásidentitás hitelesítés

Egy adat-előállító is társítható egy [Szolgáltatásidentitás felügyelt](data-factory-service-identity.md) , amely jelzi, hogy az adott adat-előállítóban. A szolgáltatásidentitás használhatja az Azure SQL Database-hitelesítéshez. A kijelölt gyári férhetnek hozzá, és másolja a identitásával adatok vagy az adatbázishoz.

Az Azure AD MSI-alapú alkalmazás tokent használó hitelesítés használatához kövesse az alábbi lépéseket:

1. **Hozzon létre egy csoportot az Azure ad-ben.** A gyári MSI legyen a csoport tagja.

    a. Keresés a data factory szolgáltatásidentitás Azure-portálról. Lépjen a data factory **tulajdonságok**. Másolja a szolgáltatás IDENTITÁSÁNAK azonosítóját.

    b. Telepítse a [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2) modul. Jelentkezzen be a `Connect-AzureAD` parancsot. Futtassa a következő parancsok futtatásával hozzon létre egy csoportot, és adja hozzá a data factory MSI tagja.
    ```powershell
    $Group = New-AzureADGroup -DisplayName "<your group name>" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId "<your data factory service identity ID>"
    ```

2. **[Egy Azure Active Directory-rendszergazda kiépítése](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)**  az Azure SQL-kiszolgáló, az Azure portálon, ha még nem tette. Az Azure AD-rendszergazda az Azure AD-felhasználó vagy az Azure AD-csoport lehet. Ha a csoportban található MSI engedélyez egy rendszergazdai szerepkört, hagyja ki a 3. és 4. A rendszergazda az adatbázis teljes hozzáféréssel fog rendelkezni.

3. **[Hozzon létre a tartalmazott adatbázis-felhasználók](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)**  az Azure AD-csoport. Csatlakoztassa az adatbázist, vagy amelyek át kívánja másolni az adatok eszközökkel SSMS, például egy Azure AD-identitás, amely rendelkezik legalább bármely felhasználó ALTER engedéllyel. Futtassa a következő T-SQL: 
    
    ```sql
    CREATE USER [your AAD group name] FROM EXTERNAL PROVIDER;
    ```

4. **Adja meg a szükséges engedélyekkel az Azure AD-csoport** szokásos módon az SQL-felhasználók és a többi. Például futtassa a következő kódot:

    ```sql
    EXEC sp_addrolemember [role name], [your AAD group name];
    ```

5. **Egy Azure SQL Database társított szolgáltatás beállítása** az Azure Data Factory.

#### <a name="linked-service-example-that-uses-msi-authentication"></a>MSI-hitelesítést használó társított szolgáltatás – példa

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
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

Szakaszok és meghatározása adatkészletek esetében elérhető tulajdonságok teljes listáját lásd: a [adatkészletek](https://docs.microsoft.com/en-us/azure/data-factory/concepts-datasets-linked-services) cikk. Ez a témakör az Azure SQL Database adatkészlet által támogatott tulajdonságokról.

Másolja az adatokat, vagy az Azure SQL Database, állítsa be a **típus** a DataSet tulajdonság **AzureSqlTable**. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A **típus** értékre kell állítani a DataSet tulajdonság **AzureSqlTable**. | Igen |
| tableName | A tábla vagy nézet, amelyre a társított szolgáltatás hivatkozik az Azure SQL Database-példány neve. | Igen |

#### <a name="dataset-properties-example"></a>Adatkészlet tulajdonságai – példa

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
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és a rendelkezésre álló tevékenységek meghatározó tulajdonságok teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a témakör az Azure SQL Database-forrás és a fogadó által támogatott tulajdonságokról.

### <a name="azure-sql-database-as-the-source"></a>Az Azure SQL-adatbázis, mint a forrás

Adatok másolása az Azure SQL Database, állítsa be a **típus** tulajdonság a másolási tevékenység forrásból történő **SqlSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakasz:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A **típus** értékre kell állítani a másolási tevékenység forrás tulajdonság **SqlSource**. | Igen |
| sqlReaderQuery | Az egyéni SQL-lekérdezés segítségével adatokat olvasni. Példa: `select * from MyTable`. | Nem |
| sqlReaderStoredProcedureName | A tárolt eljárás, amely adatokat olvas a forrástábla neve. Az utolsó SQL-utasítás a következő tárolt eljárást a SELECT utasítással kell lennie. | Nem |
| storedProcedureParameters | A tárolt eljárás paramétereit.<br/>Megengedett értékek:-név és érték párokat. Nevét és a kis-és a paraméterek meg kell egyeznie a nevek és a kis-és nagybetűhasználat a tárolt eljárás paramétereit. | Nem |

### <a name="points-to-note"></a>Vegye figyelembe mutat

- Ha a **sqlReaderQuery** van megadva a **SqlSource**, másolási tevékenység fut ez a lekérdezés az adatok lekérdezése az Azure SQL Database forrása. Vagy megadhatja a tárolt eljárást. Adja meg **sqlReaderStoredProcedureName** és **storedProcedureParameters** Ha a tárolt eljárás paraméterek fogadja el.
- Ha nem adja meg vagy **sqlReaderQuery** vagy **sqlReaderStoredProcedureName**, az oszlopok definiálva a **struktúra** célra szolgálnak az adatkészlet JSON szakasza a lekérdezés létrehozásához. `select column1, column2 from mytable` az Azure SQL Database fut. Ha az adatkészlet-definícióban nem rendelkezik a **struktúra**, kiválasztott összes oszlop a táblából.
- Amikor **sqlReaderStoredProcedureName**, továbbra is meg kell adnia egy helyőrző **tableName** az adatkészlet JSON tulajdonság.

#### <a name="sql-query-example"></a>SQL-lekérdezés példa

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

#### <a name="stored-procedure-example"></a>Tárolt eljárás példa

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

### <a name="stored-procedure-definition"></a>Tárolt eljárás meghatározása

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

### <a name="azure-sql-database-as-the-sink"></a>Az Azure SQL Database, a fogadó

Adatok másolása az Azure SQL Database, állítsa be a **típus** tulajdonság a másolási tevékenység során a gyűjtése a **SqlSink**. A következő tulajdonságok támogatottak a másolási tevékenység **fogadó** szakasz:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A **típus** értékre kell állítani a másolási tevékenység fogadó tulajdonságának **SqlSink**. | Igen |
| writeBatchSize | Adatok beszúrása az SQL táblázatba, amikor a puffer mérete eléri a következőt **writeBatchSize**.<br/> Az engedélyezett érték **egész** (sorok száma). | Nem. Az alapértelmezett beállítás 10000. |
| writeBatchTimeout | A várakozási idő a köteg beszúrási művelet befejezését, mielőtt azt az időkorlátot.<br/> Az engedélyezett érték **timespan**. Példa: "00: 30:00" (30 perc). | Nem |
| preCopyScript | Adjon meg egy SQL-lekérdezés futtatása előtt az Azure SQL-adatbázisba írását másolási tevékenységhez. Csak meghívták egyszer futtatni példányonként. Ez a tulajdonság használatával az előre betöltött adatok törlése. | Nem |
| sqlWriterStoredProcedureName | A tárolt eljárás, amely meghatározza a forrásadatok alkalmazásáról a céloldali tábla neve. Példa: upserts vagy átalakítási által a saját üzleti logika használatával. <br/><br/>A tárolt eljárás **kötegenként meghívott**. Csak egyszer futnak le és nincs köze az adatforrás adatokkal műveleteket, használja a `preCopyScript` tulajdonság. Példa műveleteket delete és csonkolja. | Nem |
| storedProcedureParameters |A tárolt eljárás paramétereit.<br/>Megengedett értékek: név-érték párokat. Nevét és a kis-és a paraméterek meg kell egyeznie a nevek és a kis-és nagybetűhasználat a tárolt eljárás paramétereit. | Nem |
| sqlWriterTableType | Adjon meg egy tábla típus a következő tárolt eljárás használható. Másolási tevékenység elérhetővé teszi az adatok áthelyezése egy ideiglenes táblát, amely a táblatípus. Tárolt eljárás kódot is majd egyesítheti az adatokat, a meglévő adatok másolásának. | Nem |

> [!TIP]
> Az Azure SQL Database-adatok másolásakor másolási tevékenység hozzáfűzi adatokat a fogadó tábla alapértelmezés szerint. Ehhez az upsert vagy további üzleti logikát, használja a tárolt eljárás **SqlSink**. Többet is megtudhat a [meghívása tárolt eljárás végrehajtásával SQL gyűjtése](#invoking-stored-procedure-for-sql-sink).

#### <a name="append-data-example"></a>Példa adatok hozzáfűzése

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
                "type": "SqlSink",
                "writeBatchSize": 100000
            }
        }
    }
]
```

#### <a name="invoke-a-stored-procedure-during-copy-for-upsert-example"></a>A tárolt eljárás meghívása upsert például másolása során

Többet is megtudhat a [meghívása tárolt eljárás végrehajtásával SQL gyűjtése](#invoking-stored-procedure-for-sql-sink).

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
                "type": "SqlSink",
                "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
                "sqlWriterTableType": "CopyTestTableType",
                "storedProcedureParameters": {
                    "identifier": { "value": "1", "type": "Int" },
                    "stringData": { "value": "str1" }
                }
            }
        }
    }
]
```

## <a name="identity-columns-in-the-target-database"></a>A céladatbázis azonosító oszlop

Ez a szakasz bemutatja, hogyan adatokat másolni a forrástábla nélkül azonosító oszlop azonosító oszlopot tartalmazó táblát.

#### <a name="source-table"></a>Forrástábla

```sql
create table dbo.SourceTbl
(
       name varchar(100),
       age int
)
```

#### <a name="destination-table"></a>Céltábla

```sql
create table dbo.TargetTbl
(
       identifier int identity(1,1),
       name varchar(100),
       age int
)
```

> [!NOTE]
> A céltábla tartalmaz azonosító oszlopot.

#### <a name="source-dataset-json-definition"></a>Forrás adatkészlet JSON-definícióból

```json
{
    "name": "SampleSource",
    "properties": {
        "type": " AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "TestIdentitySQL",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTbl"
        }
    }
}
```

#### <a name="destination-dataset-json-definition"></a>Cél adatkészlet JSON-definícióból

```json
{
    "name": "SampleTarget",
    "properties": {
        "structure": [
            { "name": "name" },
            { "name": "age" }
        ],
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "TestIdentitySQL",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "TargetTbl"
        }
    }
}
```

> [!NOTE]
> A forrás és cél tábla sémája eltérő. 

A cél egy további oszlopot identitással rendelkezik. Ebben az esetben meg kell adnia a **struktúra** tulajdonság az a tároló adatkészlet-definícióban, amely nem tartalmazza az identitásoszlop.

## <a name="invoking-stored-procedure-for-sql-sink"></a> A fogadó SQL tárolt eljárás meghívása

Adatok másolása az Azure SQL Database-be, amikor is konfigurálhatja és hívhatnak meg a felhasználó által megadott tárolt eljárást a további paramétereket.

A tárolt eljárás segítségével használhatja, ha a beépített másolási mechanizmust a célt nem szolgál. Azok még jellemzően akkor egy upsert, az insert és a frissítés, vagy a felesleges feldolgozási a forrásadatok végső szúrás a célként megadott táblája előtt kell elvégezni. Néhány további feldolgozás többek között az egyesítési oszlopok, keresse meg a további értékeket, és több tábla szúrás.

A következő példa bemutatja, hogyan egy upsert tegye az Azure SQL-adatbázis egy táblába a tárolt eljárás használatával. Tegyük fel, amelyek bemeneti adatok és a fogadó **Marketing** minden táblának rendelkeznie három oszlopot: **ProfileID**, **állapot**, és **kategória**. Hajtsa végre a upsert alapján a **ProfileID** oszlopban, és csak az egy adott konkrét kategóriával.

#### <a name="output-dataset"></a>Kimeneti adatkészlet

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
        "typeProperties": {
            "tableName": "Marketing"
        }
    }
}
```

Adja meg a **SqlSink** szakasz a másolási tevékenység:

```json
"sink": {
    "type": "SqlSink",
    "SqlWriterTableType": "MarketingType",
    "SqlWriterStoredProcedureName": "spOverwriteMarketing",
    "storedProcedureParameters": {
        "category": {
            "value": "ProductA"
        }
    }
}
```

Az adatbázis meghatározása a neve megegyezik a tárolt eljárásnak a **SqlWriterStoredProcedureName**. Kezeli a bemeneti adatokat az adott forrásból származnak, és a kimeneti táblához egyesít. A paraméternév megadásához, a tárolt eljárás egyeznie kell a **tableName** adatkészlet definiálva.

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
      VALUES (source.ProfileID, source.State, source.Category)
END
```

Az adatbázisban, adja meg a tábla Típus – amelynek a neve megegyezik a **sqlWriterTableType**. A tábla típusú sémát a bemeneti adatok által visszaadott sémának megegyezőnek kell lennie.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL,
    [Category] [varchar](256) NOT NULL,
)
```

A tárolt eljárás szolgáltatás kihasználja [Table-Valued paraméterek](https://msdn.microsoft.com/library/bb675163.aspx).

## <a name="data-type-mapping-for-azure-sql-database"></a>Adattípus-hozzárendelése az Azure SQL Database

Vagy az Azure SQL Database a másolt adatok, a következő leképezéseit segítségével az Azure SQL Database adattípusok Azure Data Factory ideiglenes adattípusok. Lásd: [séma- és írja be a leképezéseket](copy-activity-schema-and-type-mapping.md) megtudhatja, hogyan másolási tevékenység van leképezve a séma- és adatok típusa a fogadó.

| Az Azure SQL adatbázis-adattípust | Data Factory ideiglenes adattípus |
|:--- |:--- |
| bigint |Int64 |
| Bináris |Byte] |
| bit |Logikai |
| karakter |Karakterlánc, Char] |
| dátum |DateTime |
| Dátum és idő |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |DateTimeOffset |
| Decimális |Decimális |
| A FILESTREAM attribútum (varbinary(max)) |Byte] |
| Lebegőpontos |Dupla |
| image |Byte] |
| int |Int32 |
| pénz |Decimális |
| nchar |Karakterlánc, Char] |
| ntext |Karakterlánc, Char] |
| numerikus |Decimális |
| nvarchar |Karakterlánc, Char] |
| valós |Önálló |
| ROWVERSION |Byte] |
| smalldatetime |DateTime |
| smallint |Int16 |
| kis pénz típusú értéknél |Decimális |
| sql_variant |Objektum * |
| szöveg |Karakterlánc, Char] |
| time |A TimeSpan |
| időbélyeg |Byte] |
| tinyint |Bájt |
| egyedi azonosító |GUID |
| varbinary |Byte] |
| varchar |Karakterlánc, Char] |
| xml |Xml |

## <a name="next-steps"></a>További lépések
Források és mosdók másolási tevékenység során az Azure Data Factory által támogatott adattárolókhoz listájáért lásd: [adatokról és formátumok támogatott](copy-activity-overview.md##supported-data-stores-and-formats).
