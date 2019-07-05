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
ms.date: 06/14/2019
ms.author: jingwang
ms.openlocfilehash: 5dbd739070b1f66fe5ff04f319a3818269d0bdaa
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449609"
---
# <a name="copy-data-to-or-from-azure-sql-database-by-using-azure-data-factory"></a>Adatok másolása, vagy az Azure SQL Database-ből az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki az Azure Data Factory használt verzióját:"]
> * [1-es verzió](v1/data-factory-azure-sql-connector.md)
> * [Aktuális verzió](connector-azure-sql-database.md)

Ez a cikk ismerteti, hogyan másolhat adatokat, és az Azure SQL Database-ből. Azure Data Factory szolgáltatásról, olvassa el a [bevezető cikk](introduction.md).

## <a name="supported-capabilities"></a>Támogatott képességek

Az Azure SQL Database-összekötő a következő tevékenységek esetén támogatott:

- [Másolási tevékenység](copy-activity-overview.md) a [támogatott forrás/fogadó mátrix](copy-activity-overview.md) tábla
- [Az adatfolyam-leképezés](concepts-data-flow-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)

Pontosabban az Azure SQL Database-összekötő támogatja ezeket a funkciókat:

- Adatok másolása az Azure-erőforrások egy egyszerű vagy felügyelt szolgáltatásidentitások SQL-hitelesítés és Azure Active Directory (Azure AD) alkalmazástoken-hitelesítésének használata által.
- Forrásként adatok beolvasása egy SQL-lekérdezést vagy tárolt eljárás használatával.
- Fogadóként táblát vagy a másolás során az egyéni logikát tárolt eljárás meghívása adatok hozzáfűzése.

>[!NOTE]
>Az Azure SQL Database [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-current) nem támogatja ennek az összekötőnek most. Megkerülő megoldásként használható olyan [általános ODBC-összekötő](connector-odbc.md) és a egy SQL Server ODBC-illesztőt a saját üzemeltetésű integrációs modulon keresztül. Hajtsa végre a [Ez az útmutató](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-current) ODBC illesztőprogram letöltési és kapcsolati karakterlánc konfigurációkkal.

> [!IMPORTANT]
> Adatmásolás az Azure Data Factory integrációs modul használatával, konfiguráljon egy [Azure SQL Server tűzfal](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) úgy, hogy az Azure-szolgáltatásokhoz férhet hozzá a kiszolgálón.
> Ha másol adatokat egy saját üzemeltetésű integrációs modul használatával, konfigurálja az Azure SQL-kiszolgáló tűzfalán, hogy a megfelelő IP-címtartomány. A tartományba beletartozik az Azure SQL adatbázishoz való csatlakozáshoz használja a gép IP-Címmel.

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Az alábbi szakaszok nyújtanak, amelyek meghatározzák az adott Azure Data Factory-entitások egy Azure SQL Database-összekötő-tulajdonságokkal kapcsolatos részletekért.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Ezek a Tulajdonságok támogatottak egy Azure SQL Database-beli társított szolgáltatást:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A **típus** tulajdonságot állítsa **AzureSqlDatabase**. | Igen |
| connectionString | Adja meg a az Azure SQL Database-példányhoz való kapcsolódáshoz szükséges adatokat a **connectionString** tulajdonság. <br/>Jelölje meg a mező **SecureString** tárolja biztonságos helyen az Azure Data Factoryban. Jelszó vagy szolgáltatás szolgáltatásnév kulcsát az Azure Key Vaultban lehet tenni. Ha SQL-hitelesítés, kérje le a `password` konfigurációs ki a kapcsolati karakterláncot. További információkért tekintse meg a tábla a következő példa a JSON és [Store hitelesítő adatokat az Azure Key Vaultban](store-credentials-in-key-vault.md). | Igen |
| servicePrincipalId | Adja meg az alkalmazás ügyfél-azonosítót. | Igen, egy egyszerű szolgáltatást az Azure AD-hitelesítés használata esetén |
| servicePrincipalKey | Adja meg az alkalmazáskulcsot. Jelölje meg a mező **SecureString** tárolja biztonságos helyen az Azure Data Factory vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). | Igen, egy egyszerű szolgáltatást az Azure AD-hitelesítés használata esetén |
| tenant | Adja meg a bérlő információkat, például a tartománynév, vagy a bérlő Azonosítóját, amelyben az alkalmazás található. Az Azure portal jobb felső sarkában az egér viszi, lekéréséhez. | Igen, egy egyszerű szolgáltatást az Azure AD-hitelesítés használata esetén |
| connectVia | Ez [integrációs modul](concepts-integration-runtime.md) az adattárban való kapcsolódásra szolgál. Az Azure-beli integrációs modul és a egy saját üzemeltetésű integrációs modult is használhatja, ha az adattár egy magánhálózaton található. Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. | Nem |

Különböző hitelesítési típus tekintse meg a következő szakaszok az Előfeltételek és a JSON-minták, illetve:

- [SQL-hitelesítés](#sql-authentication)
- [Az Azure AD alkalmazástoken-hitelesítésének: Egyszerű szolgáltatás](#service-principal-authentication)
- [Az Azure AD alkalmazástoken-hitelesítésének: Felügyelt identitások az Azure-erőforrásokhoz](#managed-identity)

>[!TIP]
>Ha eléri a következő hibakóddal: "UserErrorFailedToConnectToSqlServer" hiba történt, valamint egy üzenetet, és mint "a munkamenet korlátot, az adatbázis XXX és elérte az", adja hozzá a `Pooling=false` a kapcsolati karakterláncot, és próbálkozzon újra.

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

**Az Azure Key Vault a jelszó** 

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
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

A szolgáltatás egyszerűszolgáltatás-alapú Azure AD alkalmazástoken-hitelesítésének használatához kövesse az alábbi lépéseket:

1. [Az Azure Active Directory-alkalmazás létrehozása](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) az Azure Portalról. Jegyezze fel az alkalmazás nevét és a következő értékeket, amelyek meghatározzák a társított szolgáltatást:

    - Alkalmazásazonosító
    - Alkalmazáskulcs
    - Bérlőazonosító

2. [Az Azure Active Directory-rendszergazda kiépítése](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server) az Azure SQL Serverhez az Azure Portalon, ha ezt még nem tette meg. Azure AD-rendszergazdaként kell lennie az Azure AD-felhasználó vagy az Azure AD-csoport, de nem lehet egy egyszerű szolgáltatást. Ebben a lépésben végezhető el, hogy a következő lépéssel, használhatja az Azure AD identity egy tartalmazottadatbázis-felhasználó, a szolgáltatás egyszerű létrehozásához.

3. [Hozzon létre tartalmazottadatbázis-felhasználók](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) egyszerű szolgáltatás számára. Az adatbázis vagy amely eszközökkel, mint például az SQL Server Management Studio segítségével, amely rendelkezik legalább egy Azure AD-identitásnak adatokat másolni kívánt bármely felhasználó ALTER engedéllyel. Futtassa a következő T-SQL: 
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. Adja meg a szolgáltatásnév a szokásos módon SQL-felhasználók vagy mások számára szükséges engedélyekkel. Futtassa a következő kódot. További beállításokért lásd: [ebben a dokumentumban](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017).

    ```sql
    EXEC sp_addrolemember [role name], [your application name];
    ```

5. Egy Azure SQL Database társított szolgáltatás konfigurálása az Azure Data Factoryban.


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

Adat-előállító társítható egy [-identitás az Azure-erőforrások](data-factory-service-identity.md) , amely az adott adat-előállító jelöli. A felügyelt identitást is használhatja az Azure SQL Database-hitelesítéshez. A kijelölt gyári férhet hozzá, és másolja ezt az identitást az adatok vagy az adatbázishoz.

Felügyelt identitás-hitelesítést használ, kövesse az alábbi lépéseket.

1. [Az Azure Active Directory-rendszergazda kiépítése](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server) az Azure SQL Serverhez az Azure Portalon, ha ezt még nem tette meg. Az Azure AD-rendszergazda lehet egy Azure AD-felhasználó vagy az Azure AD-csoportok. Ha a csoport felügyelt identitás rendszergazda szerepkörrel rendelkező biztosít, hagyja ki a 3. és 4. A rendszergazda rendelkezik teljes körű hozzáféréssel az adatbázishoz.

2. [Hozzon létre tartalmazottadatbázis-felhasználók](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) az Azure Data Factory által kezelt identitás. Az adatbázis vagy amely eszközökkel, mint például az SQL Server Management Studio segítségével, amely rendelkezik legalább egy Azure AD-identitásnak adatokat másolni kívánt bármely felhasználó ALTER engedéllyel. Futtassa a következő T-SQL: 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. Adja meg az adat-előállító felügyelt identitás szükséges engedélyekkel, megszokott SQL-felhasználók és mások. Futtassa a következő kódot. További beállításokért lásd: [ebben a dokumentumban](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017).

    ```sql
    EXEC sp_addrolemember [role name], [your Data Factory name];
    ```

4. Egy Azure SQL Database társított szolgáltatás konfigurálása az Azure Data Factoryban.

**Példa**

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

Szakaszok és adatkészletet határoz meg a rendelkezésre álló tulajdonságok teljes listáját lásd: [adatkészletek](https://docs.microsoft.com/azure/data-factory/concepts-datasets-linked-services). Ez a szakasz az Azure SQL Database-adatkészletet által támogatott tulajdonságok listáját tartalmazza.

Másolja az adatokat, vagy az Azure SQL Database, az a következő tulajdonságok támogatottak:

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
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: [folyamatok](concepts-pipelines-activities.md). Ez a szakasz a forrás Azure SQL Database és a fogadó által támogatott tulajdonságok listáját tartalmazza.

### <a name="azure-sql-database-as-the-source"></a>Az Azure SQL Database-t a forrás

Adatok másolása az Azure SQL Database, állítsa be a **típus** tulajdonság a másolási tevékenység forrásból való **SqlSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A **típus** értékre kell állítani a másolási tevékenység forrása tulajdonságát **SqlSource**. | Igen |
| sqlReaderQuery | Ez a tulajdonság az egyéni SQL-lekérdezést használ adatokat olvasni. Például: `select * from MyTable`. | Nem |
| sqlReaderStoredProcedureName | A tárolt eljárást, amely adatokat olvas be a forrás-tábla neve. Az utolsó SQL-utasítást a tárolt eljárás a SELECT utasítással kell lennie. | Nem |
| storedProcedureParameters | A tárolt eljárás paraméterei.<br/>Megengedett értékek: neve vagy értéke párokat. A neveket és a kis-és a paraméterek meg kell egyeznie a neveket és a kis-és nagybetűhasználatot, a tárolt eljárás paraméterértékeinek. | Nem |

**Tudnivalók:**

- Ha **sqlReaderQuery** megadott **SqlSource**, a másolási tevékenység a lekérdezés fut az Azure SQL Database forrás, az adatok beolvasásához. Emellett megadhatja a tárolt eljárás megadásával **sqlReaderStoredProcedureName** és **storedProcedureParameters** Ha paramétereket fogadja tárolt eljárást.
- Ha nem adja meg vagy **sqlReaderQuery** vagy **sqlReaderStoredProcedureName**, a az adatkészlet JSON "struktúra" szakaszban megadott oszlopok lekérdezés létrehozására szolgálnak. A lekérdezés `select column1, column2 from mytable` fut az Azure SQL-adatbázison. Ha az adatkészlet-definícióban nem tartalmaz "szerkezet", az összes oszlop ki van jelölve, a táblából.

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

> [!TIP]
> További információ a támogatott írási viselkedéseket, konfigurációk és ajánlott eljárásait [ajánlott eljárás az adatok betöltéséhez az Azure SQL Database-be](#best-practice-for-loading-data-into-azure-sql-database).

Adatok másolása az Azure SQL Database, állítsa be a **típus** tulajdonságot a másolási tevékenység fogadó való **SqlSink**. A következő tulajdonságok támogatottak a másolási tevékenység **fogadó** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A **típus** értékre kell állítani a másolási tevékenység fogadó tulajdonságát **SqlSink**. | Igen |
| writeBatchSize | Az SQL-táblába beszúrandó sorok számát *kötegenként*.<br/> Az engedélyezett érték **egész** (sorok száma). Alapértelmezés szerint az Azure Data Factory dinamikusan a sor mérete alapján megfelelő Köteg mérete határozza meg. | Nem |
| writeBatchTimeout | A várakozási idő a köteg beszúrási művelet befejezését, mielőtt azt az időkorlátot.<br/> Az engedélyezett érték **timespan**. Például "00: 30:00" (30 perc). | Nem |
| preCopyScript | Adjon meg egy SQL-lekérdezést a másolási tevékenység futtatása előtt írja az adatokat az Azure SQL Database-be. Indítva csak egyszer futtatni példányonkénti. Ez a tulajdonság használatával az előre betöltött adatokat. | Nem |
| sqlWriterStoredProcedureName | A tárolt eljárást, amely meghatározza, hogyan alkalmazhatja a forrásadatok egy cél táblába neve. <br/>A tárolt eljárás *kötegenként meghívása*. Olyan műveletek, amelyek csak egyszer futnak, és nincs köze tartalmazó adatforrásból, például törlés, és le, használja a `preCopyScript` tulajdonság. | Nem |
| storedProcedureParameters |A tárolt eljárás paraméterei.<br/>Megengedett értékek: név-érték párokat. Nevek és a kis-és a paraméterek meg kell egyeznie a neveket és a kis-és nagybetűhasználatot, a tárolt eljárás paraméterértékeinek. | Nem |
| sqlWriterTableType | Adjon meg egy tábla típusú név a tárolt eljárás használható. A másolási tevékenység áthelyezett elérhetővé teszi a tábla típusú egy ideiglenes táblába. Tárolt eljárás kód majd egyesítheti az éppen másolt adatokat a adatokkal. | Nem |

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
                "type": "SqlSink",
                "writeBatchSize": 100000
            }
        }
    }
]
```

**2. példa: Tárolt eljárás meghívása során másolása**

További részletek a [a egy SQL-fogadó tárolt eljárás meghívása](#invoke-a-stored-procedure-from-a-sql-sink).

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

## <a name="best-practice-for-loading-data-into-azure-sql-database"></a>Ajánlott eljárás az adatok betöltéséhez az Azure SQL Database-be

Az Azure SQL Database-be másol adatokat, amikor szüksége lehet a különböző írási viselkedés:

- [Hozzáfűzés](#append-data): A forrásadatok csak új rekordokat tartalmaz.
- [Upsert](#upsert-data): A forrásadatok beszúrások és frissítések is rendelkezik.
- [Írja felül](#overwrite-the-entire-table): Töltse be újra egy teljes táblát minden alkalommal, amikor szeretnék.
- [Az egyéni logikát írási](#write-data-with-custom-logic): További feldolgozás előtt a végső beszúrási a céltáblázatba történő van szükségem.

Tekintse meg a megfelelő részek konfigurálásáról az Azure Data Factory és az ajánlott eljárásokat.

### <a name="append-data"></a>Adatok hozzáfűzése

Adatok hozzáfűzése a fogadó Azure SQL Database összekötő alapértelmezett viselkedését. Az Azure Data Factory végzi a tömeges beszúrás hatékonyan írni a táblában. Konfigurálhatja a forrás és fogadó ennek megfelelően a másolási tevékenység.

### <a name="upsert-data"></a>Adatok beszúrása és frissítése (upsert)

**1. lehetőség:** Ha rendelkezik egy nagy mennyiségű adat másolása, egy upsert ehhez a következő módon használható: 

- Először egy [adatbázishoz kötődő ideiglenes tábla](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current#database-scoped-global-temporary-tables-azure-sql-database) a tömeges betöltés összes rögzíti a másolási tevékenység használatával. Nem naplózza az adatbázishoz kötődő ideiglenes táblákra mutató műveletet, mert rekordok millióit, betöltheti másodpercek alatt.
- A tárolt eljárási tevékenység futtatása az Azure Data Factoryban a alkalmazni egy [EGYESÍTÉSE](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) vagy a beszúrási/frissítés utasítást. A forrás összes frissít vagy szúr be egy tranzakcióként, használja az ideiglenes tábla. Ezzel a módszerrel adatváltások és naplózási műveletek száma csökken. A tárolt eljárási tevékenység végén az ideiglenes tábla levágása, készen áll a következő upsert ciklusig.

Tegyük fel, az Azure Data Factory, létrehozhat rendelkező folyamatot egy **másolási tevékenység** a kapcsolt egy **Stored Procedure-tevékenység**. A korábbi adatokat másol a forrásadattárba a egy Azure SQL Database ideiglenes táblába, például **##UpsertTempTable**, a table neveként az adatkészletben. Ezután az utóbbi egy tárolt eljárást hív meg az ideiglenes táblából a forrásadatok egyesítse a céltábla és az ideiglenes tábla karbantartása.

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

Adja meg az adatbázis egyesítési logikával, például a következő példának, amely az előző tárolt eljárás tevékenység a mutatott tárolt eljárás. Tegyük fel, hogy a cél a **Marketing** három oszlopokat tartalmazó táblába: **ProfileID**, **állapot**, és **kategória**. Hajtsa végre az upsert alapján a **ProfileID** oszlop.

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

**2. lehetőség:** Is választhat [belül a másolási tevékenység tárolt eljárás meghívása](#invoke-a-stored-procedure-from-a-sql-sink). Ez a módszer minden egyes sor futtatja a forrástábla, mint az alapértelmezett módszer a másolási tevékenység, amely nem megfelelő, nagy léptékű upsert a tömeges beszúrás helyett.

### <a name="overwrite-the-entire-table"></a>Írja felül a teljes tábla

Konfigurálhatja a **preCopyScript** a másolási tevékenység fogadó tulajdonságot. Ebben az esetben minden másolási tevékenység, amely futtatja, az Azure Data Factory futtatja először. Ezután futtatja a másolat illessze be az adatokat. A teljes tábla felülírja a legújabb adatokkal, adja meg például egy parancsfájlt, amely először törölje az összes rekordot tömeges, mielőtt az új adatok betöltése a forrásból.

### <a name="write-data-with-custom-logic"></a>Egyéni logikát az adatok írása

Az egyéni logikát az adatok írása a lépések hasonlóak ismertetett a [Upsert adatok](#upsert-data) szakaszban. Amikor a alkalmazni szeretne további feldolgozás előtt a céltáblázatba történő, nagy méretekben a forrásadatok a végső beszúrási teheti két dolog egyikét:

- Betölteni a hatókörrel rendelkező adatbázis ideiglenes táblát, és ezután meghívja a tárolt eljárást. 
- Tárolt eljárás meghívása másolása során.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a> A egy SQL-fogadó tárolt eljárás meghívása

Az Azure SQL Database-be másol adatokat, amikor is konfigurálhatja és felhasználó által megadott tárolt eljárást a további paraméterek meghívása.

> [!TIP]
> Tárolt eljárás meghívása dolgozza fel az adatokat soronként helyett tömeges műveletet, amely nagyméretű példány esetében nem ajánlott. További információkat talál a [ajánlott eljárás az adatok betöltéséhez az Azure SQL Database-be](#best-practice-for-loading-data-into-azure-sql-database).

Tárolt eljárás segítségével használhatja, ha a beépített másolási mechanizmusokkal nem szolgálnak ki erre a célra. Például akkor, ha további feldolgozás előtt a végső beszúrási a forrásadatok a céltáblázatba történő alkalmazásához. Extra feldolgozási példákat akkor, ha a kívánt oszlopok egyesítése, további értékek keresése és egynél több táblába beszúrandó.

A következő minta bemutatja, hogyan egy tárolt eljárást az upsert ehhez az Azure SQL Database egyik táblájába. Tegyük fel, amely a bemeneti adatokat és a fogadó **Marketing** tábla minden egyes háromoszloposak: **ProfileID**, **állapot**, és **kategória**. Hajtsa végre az upsert alapján a **ProfileID** oszlopot, és csak egy adott kategória alkalmazhatja azt.

**Kimeneti adatkészlet:** A "tableName" az ugyanazon tábla típusú paraméter neve a tárolt eljárás, ahogyan a következő tárolt eljárás parancsfájl:

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

Adja meg a **SQL-fogadó** a másolási tevékenység a következő szakaszban:

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

Az adatbázis határoz meg a tárolt eljárás neve megegyezik a **SqlWriterStoredProcedureName**. Kezeli a bemeneti adatokat az adott forrásból származnak, és azt a kimeneti tábla egyesít. A tábla típusú a tárolt eljárás paraméter neve megegyezik a **tableName** adatkészletben történő definiálása okozza.

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

Az adatbázis határoz meg a táblatípus, amelynek a neve megegyezik **sqlWriterTableType**. A táblatípus sémája megegyezik a sémanévvel, a bemeneti adatokat adott vissza.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL,
    [Category] [varchar](256) NOT NULL
)
```

A tárolt eljárás szolgáltatás kihasználja [tábla értékű paraméterek](https://msdn.microsoft.com/library/bb675163.aspx).

## <a name="mapping-data-flow-properties"></a>Data flow tulajdonságai

A részletek [átalakítási forrás](data-flow-source.md) és [átalakítási fogadó](data-flow-sink.md) hozzárendelni az adatfolyam.

## <a name="data-type-mapping-for-azure-sql-database"></a>Az Azure SQL Database-adattípus-leképezés

Adatok vagy az Azure SQL Database átmásolva, amikor a következő hozzárendeléseket használják az Azure SQL Database adattípusok Azure Data Factory-közbenső adattípusok. Hogyan a másolási tevékenység leképezi a forrás séma és adatok típusa a fogadó kapcsolatban lásd: [séma és adatok írja be a hozzárendelések](copy-activity-schema-and-type-mapping.md).

| Az Azure SQL Database-adattípus | Az Azure Data Factory köztes adatok típusa |
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
| tinyint |Byte |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |Xml |

>[!NOTE]
> Az adattípusok alapján, amelyek ideiglenes tizedes típusra jelenleg Azure Data Factory támogatja a legfeljebb 28 pontosság. Ha adatok 28-nál nagyobb pontossággal rendelkezik, fontolja meg egy karakterláncot az SQL-lekérdezésben konvertálása.

## <a name="next-steps"></a>További lépések
A másolási tevékenység az Azure Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak és formátumok](copy-activity-overview.md##supported-data-stores-and-formats).
