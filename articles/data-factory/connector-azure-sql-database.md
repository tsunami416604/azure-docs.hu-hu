---
title: Adatok másolása az vagy egy Azure SQL Database használatával a Data Factory |} A Microsoft Docs
description: Ismerje meg, hogyan másolhat adatokat az Azure SQL Database támogatott forrás adattárakból származó, vagy az SQL-adatbázisoktól a támogatott fogadó adattárakba Data Factory használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: jingwang
ms.openlocfilehash: 24fdfcb53e8f3cbf0e1bf4f7e567d9f768383ac1
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54884231"
---
# <a name="copy-data-to-or-from-azure-sql-database-by-using-azure-data-factory"></a>Adatok másolása, vagy az Azure SQL Database-ből az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Select the version of Data Factory service you use:"]
> * [1-es verzió](v1/data-factory-azure-sql-connector.md)
> * [Aktuális verzió](connector-azure-sql-database.md)

Ez a cikk ismerteti az Azure Data Factory másolási tevékenység használatával másolja az adatokat, vagy az Azure SQL Database. Épül a [másolási tevékenység áttekintése](copy-activity-overview.md) című cikket, amely megadja a másolási tevékenység általános áttekintést.

## <a name="supported-capabilities"></a>Támogatott képességek

Adatokat másolja vagy az Azure SQL Database bármely támogatott fogadó adattárba. És másolhat bármely támogatott forrásadattárból adatokat az Azure SQL Database. A másolási tevékenység által források vagy fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak és formátumok](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Pontosabban az Azure SQL Database-összekötő támogatja ezeket a funkciókat:

- Adatok másolása az Azure-erőforrások egy egyszerű vagy felügyelt szolgáltatásidentitások SQL-hitelesítés és Azure Active Directory (Azure AD) alkalmazástoken-hitelesítésének használata által.
- Forrásként adatok lekérdezése egy SQL-lekérdezést vagy tárolt eljárás használatával.
- Fogadóként adatok hozzáfűzése a táblát, vagy a másolás során az egyéni logikát tárolt eljárás meghívása.

Az Azure SQL Database [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017) jelenleg nem támogatja. 

> [!IMPORTANT]
> Az Azure Data Factory integrációs modul adatokat másol, konfiguráljon egy [Azure SQL server tűzfal](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) úgy, hogy az Azure-szolgáltatásokhoz férhet hozzá a kiszolgálón.
> Ha adatokat másolja egy saját üzemeltetésű integrációs modul használatával, konfigurálja az Azure SQL-kiszolgáló tűzfalán, hogy a megfelelő IP-címtartomány. A tartományba beletartozik az Azure SQL adatbázishoz való csatlakozáshoz használja a gép IP-Címmel.

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Az alábbi szakaszok nyújtanak, amelyek meghatározzák az adott Data Factory-entitások egy Azure SQL Database-összekötő-tulajdonságokkal kapcsolatos részletekért.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Ezek a Tulajdonságok támogatottak egy Azure SQL Database-beli társított szolgáltatást:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A **típus** tulajdonságot állítsa **AzureSqlDatabase**. | Igen |
| kapcsolati Sztringje | Adja meg a az Azure SQL Database-példányhoz való kapcsolódáshoz szükséges adatokat a **connectionString** tulajdonság. Jelölje meg a mező egy **SecureString** tárolja biztonságos helyen a Data Factory áttekintése, vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). | Igen |
| servicePrincipalId | Adja meg az alkalmazás ügyfél-azonosítót. | Igen, egy egyszerű szolgáltatást az Azure AD-hitelesítés használata esetén. |
| servicePrincipalKey | Adja meg az alkalmazáskulcsot. Jelölje meg a mező egy **SecureString** tárolja biztonságos helyen a Data Factory áttekintése, vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). | Igen, egy egyszerű szolgáltatást az Azure AD-hitelesítés használata esetén. |
| bérlő | Adja meg a bérlő információkat (tartomány neve vagy a bérlő azonosítója) alatt az alkalmazás található. Az Azure portal jobb felső sarkában az egér viszi, lekéréséhez. | Igen, egy egyszerű szolgáltatást az Azure AD-hitelesítés használata esetén. |
| connectVia | A [integrációs modul](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. Azure integrációs modul és a egy saját üzemeltetésű integrációs modult is használhatja, ha az adattár egy magánhálózaton található. Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. | Nem |

Különböző hitelesítési típus tekintse meg a következő szakaszok az Előfeltételek és a JSON-minták, illetve:

- [SQL-hitelesítés](#sql-authentication)
- [Az Azure AD alkalmazástoken-hitelesítésének: Egyszerű szolgáltatás](#service-principal-authentication)
- [Az Azure AD alkalmazástoken-hitelesítésének: Felügyelt identitások az Azure-erőforrásokhoz](#managed-identity)

>[!TIP]
>Ha nyomja le az "UserErrorFailedToConnectToSqlServer", hibakód: Hiba történt, és üzenet például a "a munkamenet korlátot, az adatbázis XXX elérte.", és adja hozzá `Pooling=false` a kapcsolati karakterláncot, és próbálkozzon újra.

### <a name="sql-authentication"></a>SQL-hitelesítés

#### <a name="linked-service-example-that-uses-sql-authentication"></a>SQL-hitelesítést használó társított szolgáltatás példa

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

A szolgáltatás egyszerűszolgáltatás-alapú Azure AD alkalmazástoken-hitelesítésének használatához kövesse az alábbi lépéseket:

1. **[Az Azure Active Directory-alkalmazás létrehozása](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)**  az Azure Portalról. Jegyezze fel az alkalmazás nevét és a következő értékeket, amelyek meghatározzák a társított szolgáltatást:

    - Alkalmazásazonosító
    - Alkalmazáskulcs
    - Bérlőazonosító

1. **[Az Azure Active Directory-rendszergazda kiépítése](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)**  az Azure SQL Serverhez, az Azure Portalon, ha ezt még nem tette meg. Azure AD-rendszergazdaként kell lennie az Azure AD-felhasználó vagy az Azure AD-csoport, de nem lehet egy egyszerű szolgáltatást. Ebben a lépésben végezhető el, hogy a következő lépéssel, használhatja az Azure AD identity egy tartalmazottadatbázis-felhasználó, a szolgáltatás egyszerű létrehozásához.

1. **[Hozzon létre tartalmazottadatbázis-felhasználók](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)**  egyszerű szolgáltatás számára. Az adatbázis vagy amelyek adatok másolása az Azure AD identitás, amelynek legalább az ssms-ben, más eszközök használatával szeretné bármely felhasználó ALTER engedéllyel. Futtassa a következő T-SQL: 
    
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

1. **Adja meg a szükséges engedélyekkel a szolgáltatásnév** szokásos módon SQL-felhasználók vagy mások számára. Futtassa a következő kódot:

    ```sql
    EXEC sp_addrolemember [role name], [your application name];
    ```

1. **Egy Azure SQL Database társított szolgáltatás konfigurálása** az Azure Data Factoryban.


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Társított szolgáltatás példa, amely használja az egyszerű szolgáltatásnév hitelesítése

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

### <a name="managed-identity"></a> Felügyelt identitások Azure-erőforrások hitelesítéshez

Adat-előállító társítható egy [-identitás az Azure-erőforrások](data-factory-service-identity.md) , amely az adott adat-előállító jelöli. A felügyeltszolgáltatás-identitás az Azure SQL Database-hitelesítéshez használható. A kijelölt gyári férhet hozzá, és másolja ezt az identitást az adatok vagy az adatbázishoz.

MSI-alapú Azure AD alkalmazástoken-hitelesítésének használatához kövesse az alábbi lépéseket:

1. **Hozzon létre egy csoportot az Azure ad-ben.** Ellenőrizze az MSI-előállító a csoport tagja.
    
    1. Keresse meg a data factory felügyeltszolgáltatás-identitás az Azure Portalról. Nyissa meg az adat-előállító **tulajdonságok**. Másolja az IDENTITÁS azonosítót.
    
    1. Telepítse a [az Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2) modul. Jelentkezzen be a `Connect-AzureAD` parancsot. Hozzon létre egy csoportot, és adja hozzá az adat-előállító MSI, amelynek a következő parancsok futtatásával.
    ```powershell
    $Group = New-AzureADGroup -DisplayName "<your group name>" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId "<your data factory service identity ID>"
    ```
    
1. **[Az Azure Active Directory-rendszergazda kiépítése](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)**  az Azure SQL Serverhez, az Azure Portalon, ha ezt még nem tette meg. Az Azure AD-rendszergazda lehet egy Azure AD-felhasználó vagy az Azure AD-csoporthoz. Ha a rendszergazda szerepkörrel, engedélyezze a csoportnak MSI-vel, hagyja ki a 3. és 4. A rendszergazda az adatbázis teljes hozzáféréssel fog rendelkezni.

1. **[Hozzon létre tartalmazottadatbázis-felhasználók](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)**  az Azure AD-csoport. Az adatbázis vagy amelyek adatok másolása az Azure AD identitás, amelynek legalább az ssms-ben, más eszközök használatával szeretné bármely felhasználó ALTER engedéllyel. Futtassa a következő T-SQL: 
    
    ```sql
    CREATE USER [your AAD group name] FROM EXTERNAL PROVIDER;
    ```

1. **Adja meg a szükséges engedélyekkel az Azure AD-csoport** szokásos módon SQL-felhasználók és mások. Például futtassa a következő kódot:

    ```sql
    EXEC sp_addrolemember [role name], [your AAD group name];
    ```

1. **Egy Azure SQL Database társított szolgáltatás konfigurálása** az Azure Data Factoryban.

#### <a name="linked-service-example-that-uses-msi-authentication"></a>MSI-hitelesítést használó társított szolgáltatás példa

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

Szakaszok és adatkészletek definiálását tulajdonságainak teljes listáját lásd: a [adatkészletek](https://docs.microsoft.com/azure/data-factory/concepts-datasets-linked-services) cikk. Ez a szakasz az Azure SQL Database-adatkészletet által támogatott tulajdonságok listáját tartalmazza.

Másolja az adatokat, vagy az Azure SQL Database, állítsa be a **típus** tulajdonság, az adatkészlet **AzureSqlTable**. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A **típus** értékre kell állítani a tulajdonságot az adatkészlet **AzureSqlTable**. | Igen |
| tableName | A tábla vagy nézet az Azure SQL Database-példány, amelyre a társított szolgáltatás hivatkozik a neve. | Nincs forrás, a fogadó Igen |

#### <a name="dataset-properties-example"></a>Adatkészlet tulajdonságai példa

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

Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz a forrás Azure SQL Database és a fogadó által támogatott tulajdonságok listáját tartalmazza.

### <a name="azure-sql-database-as-the-source"></a>Az Azure SQL Database-t a forrás

Adatok másolása az Azure SQL Database, állítsa be a **típus** tulajdonság, a másolási tevékenység forrás **SqlSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A **típus** értékre kell állítani a másolási tevékenység forrás tulajdonság **SqlSource**. | Igen |
| sqlReaderQuery | Az egyéni SQL-lekérdezés segítségével olvassa el az adatokat. Példa: `select * from MyTable`. | Nem |
| sqlReaderStoredProcedureName | A tárolt eljárást, amely adatokat olvas be a forrás-tábla neve. Az utolsó SQL-utasítást a tárolt eljárás a SELECT utasítással kell lennie. | Nem |
| storedProcedureParameters | A tárolt eljárás paraméterei.<br/>Megengedett értékek: neve vagy értéke párokat. Nevek és a kis-és a paraméterek meg kell egyeznie a neveket és a kis-és nagybetűhasználatot, a tárolt eljárás paraméterértékeinek. | Nem |

### <a name="points-to-note"></a>Tudnivalók

- Ha a **sqlReaderQuery** van megadva a **SqlSource**, a másolási tevékenység a lekérdezés fut az Azure SQL Database forrás, az adatok beolvasásához. Vagy megadhat egy tárolt eljárást. Adja meg **sqlReaderStoredProcedureName** és **storedProcedureParameters** Ha paramétereket fogadja tárolt eljárást.
- Ha nem adja meg vagy **sqlReaderQuery** vagy **sqlReaderStoredProcedureName**, az oszlopok definiálva a **struktúra** az adatkészlet JSON szakaszában is használható a lekérdezés hozhatnak létre. `select column1, column2 from mytable` futtatja az Azure SQL-adatbázison. Ha az adatkészlet definíciója nem rendelkezik a **struktúra**, az összes oszlop ki van jelölve, a táblából.

#### <a name="sql-query-example"></a>SQL-lekérdezési példa

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

### <a name="azure-sql-database-as-the-sink"></a>Az Azure SQL Database-t a fogadó

Adatok másolása az Azure SQL Database, állítsa be a **típus** tulajdonságot a másolási tevékenység fogadó való **SqlSink**. A következő tulajdonságok támogatottak a másolási tevékenység **fogadó** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A **típus** értékre kell állítani a másolási tevékenység fogadó tulajdonságát **SqlSink**. | Igen |
| WriteBatchSize | Adatok beszúrása SQL-táblát, amikor a puffer mérete eléri a **writeBatchSize**.<br/> Az engedélyezett érték **egész** (sorok száma). | Nem. Az alapértelmezett érték a 10000. |
| writeBatchTimeout | A várakozási idő a köteg beszúrási művelet befejezését, mielőtt azt az időkorlátot.<br/> Az engedélyezett érték **timespan**. Példa: "00: 30:00" (30 perc). | Nem |
| preCopyScript | Adjon meg egy SQL-lekérdezést a másolási tevékenység futtatása előtt írja az adatokat az Azure SQL Database-be. Csak indítva egyszer futtatni példányonkénti. Ez a tulajdonság használatával az előre betöltött adatokat. | Nem |
| sqlWriterStoredProcedureName | A tárolt eljárást, amely meghatározza, hogyan alkalmazhatja a forrásadatok egy cél táblába neve. Például akkor upserts, vagy átalakíthatja a saját üzleti logikája használatával. <br/><br/>A tárolt eljárás **kötegenként meghívása**. Csak egyszer futnak le, és nincs köze forrásadatokkal rendelkező műveletek, használja a `preCopyScript` tulajdonság. Példa műveleti delete és csonkolja. | Nem |
| storedProcedureParameters |A tárolt eljárás paraméterei.<br/>Megengedett értékek: név-érték párokat. Nevek és a kis-és a paraméterek meg kell egyeznie a neveket és a kis-és nagybetűhasználatot, a tárolt eljárás paraméterértékeinek. | Nem |
| sqlWriterTableType | Adjon meg egy tábla típusú név a tárolt eljárás használható. A másolási tevékenység áthelyezett elérhetővé teszi a tábla típusú egy ideiglenes táblába. Tárolt eljárás kód majd egyesítheti az adatok másolását a adatokkal. | Nem |

> [!TIP]
> Adatmásolás az Azure SQL Database, amikor a másolási tevékenység adatokat fűz hozzá a fogadó tábla alapértelmezés szerint. Ehhez az upsert vagy további üzleti logikát, használja a tárolt eljárás **SqlSink**. További részletek a [fogadó SQL tárolt eljárás meghívása](#invoking-stored-procedure-for-sql-sink).

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

#### <a name="invoke-a-stored-procedure-during-copy-for-upsert-example"></a>Tárolt eljárás meghívása során upsert például másolása

További részletek a [fogadó SQL tárolt eljárás meghívása](#invoking-stored-procedure-for-sql-sink).

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

Ez a szakasz bemutatja, hogyan másolhat adatokat egy forrás táblából egy identitásoszlop nélkül az identity oszlopot tartalmazó táblát.

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

#### <a name="source-dataset-json-definition"></a>Forrás adatkészlet JSON-definíció

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

#### <a name="destination-dataset-json-definition"></a>Cél adatkészlet JSON-definíció

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
> A forrás- és table eltérő sémával rendelkezik. 

A cél egy további oszlopot egy identitással rendelkezik. Ebben az esetben meg kell adnia a **struktúra** tulajdonság a célként megadott adatkészlet definíciójában, amely nem tartalmazza az identitásoszlop.

## <a name="invoking-stored-procedure-for-sql-sink"></a> Az SQL-fogadó tárolt eljárás meghívása

Azure SQL Database-be másol adatokat, amikor is konfigurálásához és meghívása felhasználó által megadott tárolt eljárást a további paraméterek.

Tárolt eljárás segítségével használhatja, ha a beépített másolási mechanizmusokkal nem szolgálnak ki erre a célra. Általában használhatók, amikor egy upsert, insert és update, vagy további feldolgozás előtt a végső beszúrási a forrásadatok a céltáblázatba történő kell elvégezni. Extra feldolgozási példákat egyesítési oszlop, keresse meg a további értéket, és annak több tábla.

A következő minta bemutatja, hogyan egy tárolt eljárást az upsert ehhez az Azure SQL Database egyik táblájába. Tegyük fel, amelyek bemeneti és a fogadó **Marketing** tábla minden egyes háromoszloposak: **ProfileID**, **állapot**, és **kategória**. Hajtsa végre az upsert alapján a **ProfileID** oszlopot, és csak egy adott kategória alkalmazhatja azt.

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

Adja meg a **SqlSink** a másolási tevékenység szakaszban:

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

Az adatbázis határoz meg a tárolt eljárás, amelynek a neve megegyezik a **SqlWriterStoredProcedureName**. Kezeli a bemeneti adatokat az adott forrásból származnak, és azt a kimeneti tábla egyesít. A tárolt eljárást a táblázat típusú paraméter neve legyen ugyanaz, mint a **tableName** adatkészletben történő definiálása okozza.

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

Az adatbázis határoz meg a táblatípus, amelynek a neve megegyezik a **sqlWriterTableType**. A tábla típusú sémát a sémát, a bemeneti adatok által visszaadott megegyezőnek kell lennie.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL,
    [Category] [varchar](256) NOT NULL,
)
```

A tárolt eljárás szolgáltatás kihasználja [Table-Valued paraméterek](https://msdn.microsoft.com/library/bb675163.aspx).

>[!NOTE]
>Ha pénzt és pénz adattípus meghívása tárolt eljárás által írt, értékek lehet kerekíteni. Adja meg a megfelelő adattípusokat TVP a decimális helyett pénzt és pénz csökkentése érdekében. 

## <a name="data-type-mapping-for-azure-sql-database"></a>Az Azure SQL Database-adattípus-leképezés

Másolt adatok vagy az Azure SQL Database, a következő hozzárendeléseket használják az Azure SQL Database adattípusok Azure Data Factory-közbenső adattípusok. Lásd: [séma és adatok írja be a hozzárendelések](copy-activity-schema-and-type-mapping.md) megtudhatja, hogyan másolási tevékenység leképezi a forrás séma és adatok típusa a fogadó.

| Az Azure SQL Database-adattípus | Data Factory közbenső adattípus |
|:--- |:--- |
| bigint |Int64 |
| Bináris |Byte] |
| bit |Logikai |
| CHAR |Karakterlánc, Char] |
| dátum |DateTime |
| Dátum és idő |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |DateTimeOffset |
| tizedes tört |tizedes tört |
| A FILESTREAM attribútum (varbinary(max)) |Byte] |
| Lebegőpontos |Dupla |
| image |Byte] |
| int |Int32 |
| költséget takaríthat meg |tizedes tört |
| nchar |Karakterlánc, Char] |
| ntext |Karakterlánc, Char] |
| numerikus |tizedes tört |
| nvarchar |Karakterlánc, Char] |
| valódi |Önálló |
| ROWVERSION |Byte] |
| smalldatetime |DateTime |
| smallint |Int16 |
| pénz |tizedes tört |
| sql_variant |Objektum |
| szöveg |Karakterlánc, Char] |
| time |Időtartam |
| időbélyeg |Byte] |
| tinyint |Bájt |
| UniqueIdentifier |GUID |
| varbinary |Byte] |
| varchar |Karakterlánc, Char] |
| xml |Xml |

>[!NOTE]
> A típusok képezi le közbenső tizedes tört szám típus jelenleg ADF támogatja a pontosság 28 legfeljebb. Ha adatok 28-nál nagyobb pontossággal rendelkezik, érdemes lehet alakítandó karakterláncot az SQL-lekérdezésben.

## <a name="next-steps"></a>További lépések
Az Azure Data Factory másolási tevékenység által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak és formátumok](copy-activity-overview.md##supported-data-stores-and-formats).
