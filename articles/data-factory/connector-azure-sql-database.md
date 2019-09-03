---
title: Adatok másolása Azure SQL Databaseba vagy onnan a Data Factory használatával | Microsoft Docs
description: Megtudhatja, hogyan másolhat adatok a támogatott forrásból származó adattárakból Azure SQL Database vagy SQL Databaseról a Data Factory használatával támogatott fogadó adattárakba.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: jingwang
ms.openlocfilehash: a8ab2039cde11876d853b411ca09a51e96e2ca0a
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/03/2019
ms.locfileid: "70233045"
---
# <a name="copy-data-to-or-from-azure-sql-database-by-using-azure-data-factory"></a>Adatok másolása Azure SQL Databaseba vagy onnan a Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki a használt Azure Data Factory verzióját:"]
> * [1-es verzió](v1/data-factory-azure-sql-connector.md)
> * [Aktuális verzió](connector-azure-sql-database.md)

Ez a cikk az adatok Azure SQL Databaseba való másolásának módját ismerteti. Azure Data Factory szolgáltatásról, olvassa el a [bevezető cikk](introduction.md).

## <a name="supported-capabilities"></a>Támogatott képességek

Ez az Azure SQL Database-összekötő a következő tevékenységek esetén támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrix-](copy-activity-overview.md) táblázattal
- [Adatfolyam hozzárendelése](concepts-data-flow-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)

Pontosabban, ez a Azure SQL Database-összekötő támogatja ezeket a funkciókat:

- Az adatok másolása SQL-hitelesítéssel és Azure Active Directory (Azure AD) alkalmazás-jogkivonat-hitelesítéssel az Azure-erőforrások egyszerű szolgáltatásával vagy felügyelt identitásával.
- Forrásként egy SQL-lekérdezés vagy tárolt eljárás használatával beolvashatja az adatokból.
- Fogadóként, az adathalmazhoz való hozzáfűzéssel vagy egy tárolt eljárás meghívásával egyéni logikával a másolás során.

>[!NOTE]
>Ez [](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-current) az összekötő jelenleg nem támogatja az Azure SQL Database Always Encryptedt. A megoldáshoz használhat egy [általános ODBC](connector-odbc.md) -összekötőt és egy SQL Server ODBC-illesztőt a saját üzemeltetésű integrációs modulon keresztül. Kövesse [ezt az útmutatót](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-current) az ODBC-illesztőprogram letöltésével és a kapcsolatok karakterlánc-beállításaival.

> [!IMPORTANT]
> Ha az Azure Data Factory Integration Runtime használatával másol Adatmásolást, konfiguráljon egy [azure SQL Server tűzfalat](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) úgy, hogy az Azure-szolgáltatások hozzáférhessenek a kiszolgálóhoz.
> Ha saját üzemeltetésű integrációs modul használatával másol Adatmásolást, konfigurálja az Azure SQL Server tűzfalat a megfelelő IP-címtartomány engedélyezéséhez. Ez a tartomány magában foglalja az Azure SQL Databasehoz való kapcsolódáshoz használt számítógép IP-címét.

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük azokat a tulajdonságokat, amelyek Azure Data Factory Azure SQL Database-összekötőhöz tartozó entitások definiálásához használatosak.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Ezek a tulajdonságok Azure SQL Database társított szolgáltatás esetén támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A **Type** tulajdonságot **AzureSqlDatabase**értékre kell beállítani. | Igen |
| connectionString | A **ConnectionString** tulajdonsághoz Azure SQL Database-példányhoz való kapcsolódáshoz szükséges adatok megadása. <br/>A mező megjelölése **SecureString** -ként, hogy biztonságosan tárolja Azure Data Factoryban. A jelszó vagy a szolgáltatás egyszerű kulcsa is elhelyezhető Azure Key Vaultban. Ha SQL-hitelesítéssel rendelkezik, húzza `password` ki a konfigurációt a kapcsolatok sztringből. További információ: a táblázatot követő JSON-példa és a [hitelesítő adatok tárolása Azure Key Vaultban](store-credentials-in-key-vault.md). | Igen |
| servicePrincipalId | Adja meg az alkalmazás ügyfél-azonosítót. | Igen, ha Azure AD-hitelesítést használ egy egyszerű szolgáltatással |
| servicePrincipalKey | Adja meg az alkalmazáskulcsot. A mező megjelölése **SecureString** -ként a biztonságos tárolás Azure Data Factory vagy [Azure Key Vault tárolt titkos](store-credentials-in-key-vault.md)kód hivatkozása alapján. | Igen, ha Azure AD-hitelesítést használ egy egyszerű szolgáltatással |
| tenant | Adja meg a bérlői adatokat, például a tartománynevet vagy a bérlő AZONOSÍTÓját, amely alatt az alkalmazás található. Lekéri a Azure Portal jobb felső sarkában lévő egér fölé. | Igen, ha Azure AD-hitelesítést használ egy egyszerű szolgáltatással |
| connectVia | Ez [](concepts-integration-runtime.md) az integrációs modul az adattárhoz való kapcsolódásra szolgál. Az Azure Integration Runtime vagy egy saját üzemeltetésű integrációs modult is használhat, ha az adattár egy magánhálózaton található. Ha nincs megadva, a rendszer az alapértelmezett Azure Integration Runtime-t használja. | Nem |

Különböző hitelesítési típus tekintse meg a következő szakaszok az Előfeltételek és a JSON-minták, illetve:

- [SQL-hitelesítés](#sql-authentication)
- [Azure AD-alkalmazás-jogkivonat hitelesítése: Egyszerű szolgáltatásnév](#service-principal-authentication)
- [Azure AD-alkalmazás-jogkivonat hitelesítése: Felügyelt identitások az Azure-erőforrásokhoz](#managed-identity)

>[!TIP]
>Ha hibát észlelt a "UserErrorFailedToConnectToSqlServer" hibakódmal, és egy olyan üzenet, mint például "az adatbázis munkamenet-korlátja XXX és elérte," adja hozzá `Pooling=false` a kapcsolati karakterláncot, és próbálkozzon újra.

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

**Jelszó Azure Key Vault** 

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

A szolgáltatás egyszerű Azure AD-alapú hitelesítésének használatához kövesse az alábbi lépéseket:

1. [Hozzon létre egy Azure Active Directory alkalmazást](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) a Azure Portal. Jegyezze fel az alkalmazás nevét és a következő értékeket, amelyek meghatározzák a társított szolgáltatást:

    - Alkalmazásazonosító
    - Alkalmazáskulcs
    - Bérlőazonosító

2. Ha még nem tette meg, hozzon [létre egy Azure Active Directory rendszergazdát](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server) az Azure-SQL Server Azure Portal. Az Azure AD-rendszergazdának Azure AD-felhasználónak vagy Azure AD-csoportnak kell lennie, de nem lehet egyszerű szolgáltatásnév. Ez a lépés úgy történik, hogy a következő lépésben egy Azure AD-identitással hozzon létre egy tárolt adatbázis-felhasználót az egyszerű szolgáltatásnév számára.

3. [Hozzon létre tárolt adatbázis-felhasználókat](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) az egyszerű szolgáltatásnév számára. Kapcsolódjon ahhoz az adatbázishoz, amelyről vagy rendszerre szeretne másolni egy olyan eszközt, amely a SQL Server Management Studiohoz hasonló eszközöket használ, és egy Azure AD-identitással rendelkezik, amely legalább bármilyen felhasználói engedély megváltoztatásával rendelkezik. Futtassa a következő T-SQL: 
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. Adja meg az egyszerű szolgáltatáshoz szükséges engedélyeket az SQL-felhasználók vagy mások számára általában. Futtassa a következő kódot. További lehetőségekért tekintse meg [ezt a dokumentumot](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017).

    ```sql
    EXEC sp_addrolemember [role name], [your application name];
    ```

5. Azure SQL Database társított szolgáltatás konfigurálása Azure Data Factoryban.


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

Az adatok előállítója felügyelt identitással társítható az [Azure-erőforrások számára](data-factory-service-identity.md) , amelyek az adott adatelőállítót jelölik. Ezt a felügyelt identitást Azure SQL Database hitelesítéshez használhatja. A kijelölt gyár ezen identitás használatával férhet hozzá az adatbázishoz, és másolhatja azokat az adatbázisból.

A felügyelt identitásos hitelesítés használatához kövesse az alábbi lépéseket.

1. Ha még nem tette meg, hozzon [létre egy Azure Active Directory rendszergazdát](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server) az Azure-SQL Server Azure Portal. Az Azure AD-rendszergazda lehet egy Azure AD-felhasználó vagy egy Azure AD-csoport. Ha a csoportot felügyelt identitással adja meg rendszergazdai szerepkörrel, ugorja át a 3. és a 4. lépést. A rendszergazda teljes hozzáféréssel rendelkezik az adatbázishoz.

2. [Hozzon létre tárolt adatbázis-felhasználókat](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) a Azure Data Factory felügyelt identitáshoz. Kapcsolódjon ahhoz az adatbázishoz, amelyről vagy rendszerre szeretne másolni egy olyan eszközt, amely a SQL Server Management Studiohoz hasonló eszközöket használ, és egy Azure AD-identitással rendelkezik, amely legalább bármilyen felhasználói engedély megváltoztatásával rendelkezik. Futtassa a következő T-SQL: 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. Adja meg a Data Factory felügyelt identitáshoz szükséges engedélyeket, ahogyan az SQL-felhasználók és mások számára általában nem. Futtassa a következő kódot. További lehetőségekért tekintse meg [ezt a dokumentumot](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017).

    ```sql
    EXEC sp_addrolemember [role name], [your Data Factory name];
    ```

4. Azure SQL Database társított szolgáltatás konfigurálása Azure Data Factoryban.

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

Az adatkészletek definiálásához elérhető csoportok és tulajdonságok teljes listáját lásd: adatkészletek. [](https://docs.microsoft.com/azure/data-factory/concepts-datasets-linked-services) Ez a szakasz a Azure SQL Database adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Az adatok Azure SQL Databaseba való másolásához a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | Az adatkészlet **Type** tulajdonságát **tulajdonsága azuresqltable**értékre kell állítani. | Igen |
| tableName | Annak a Azure SQL Database-példánynak a neve, amelyre a társított szolgáltatás hivatkozik. | Nincs forrás, a fogadó Igen |

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

A tevékenységek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját lásd: [folyamatok](concepts-pipelines-activities.md). Ez a szakasz a Azure SQL Database forrás és a fogadó által támogatott tulajdonságok listáját tartalmazza.

### <a name="azure-sql-database-as-the-source"></a>Azure SQL Database forrásként

Az adatok Azure SQL Databaseból történő másolásához a másolási tevékenység **forrása** szakaszban a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának **Type** tulajdonságát **AzureSqlSource**értékre kell állítani. A "SqlSource" típus továbbra is támogatott a visszamenőleges kompatibilitás érdekében. | Igen |
| sqlReaderQuery | Ez a tulajdonság az egyéni SQL-lekérdezést használja az adatolvasáshoz. Például: `select * from MyTable`. | Nem |
| sqlReaderStoredProcedureName | A tárolt eljárást, amely adatokat olvas be a forrás-tábla neve. Az utolsó SQL-utasítást a tárolt eljárás a SELECT utasítással kell lennie. | Nem |
| storedProcedureParameters | A tárolt eljárás paraméterei.<br/>Megengedett értékek: neve vagy értéke párokat. A paraméterek neveinek és burkolatának meg kell egyeznie a tárolt eljárás paramétereinek nevével és házával. | Nem |

**Megjegyzés:**

- Ha a **AzureSqlSource** **sqlReaderQuery** van megadva, a másolási tevékenység lefuttatja ezt a lekérdezést a Azure SQL Database forráson az adatkéréshez. Megadhat egy tárolt eljárást is a **sqlReaderStoredProcedureName** és a **storedProcedureParameters** megadásával, ha a tárolt eljárás paraméterekkel rendelkezik.
- Ha nem ad meg **sqlReaderQuery** vagy **sqlReaderStoredProcedureName**, az adatkészlet JSON-fájljának "struktúra" szakaszában meghatározott oszlopok a lekérdezések létrehozásához használatosak. A lekérdezés `select column1, column2 from mytable` Azure SQL Databaseon fut. Ha az adatkészlet definíciója nem rendelkezik "Structure" értékkel, az összes oszlop ki van választva a táblából.

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

### <a name="azure-sql-database-as-the-sink"></a>Azure SQL Database fogadóként

> [!TIP]
> További információ a támogatott írási viselkedésekről, konfigurációkról és ajánlott eljárásokról az [adatok Azure SQL Databaseba](#best-practice-for-loading-data-into-azure-sql-database)való betöltéséhez ajánlott eljárások közül.

Az adatAzure SQL Databaseba való másoláshoz a másolási tevékenység fogadója szakasz a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység fogadójának **Type** tulajdonságát **AzureSqlSink**értékre kell állítani. A "SqlSink" típus továbbra is támogatott a visszamenőleges kompatibilitás érdekében. | Igen |
| writeBatchSize | A kötegekben az SQL-táblába beszúrandósorok száma.<br/> Az engedélyezett érték **egész** (sorok száma). Alapértelmezés szerint a Azure Data Factory dinamikusan meghatározza a megfelelő batch-méretet a sor mérete alapján. | Nem |
| writeBatchTimeout | Az a várakozási idő, ameddig a kötegelt beszúrási művelet befejezi az időtúllépést.<br/> Az engedélyezett érték **timespan**. Ilyen például a "00:30:00" (30 perc). | Nem |
| preCopyScript | Adja meg a másolási tevékenység futtatásához szükséges SQL-lekérdezést az Azure SQL Databaseba való írás előtt. A rendszer csak egyszer hívja meg a példányt. Ez a tulajdonság használatával az előre betöltött adatokat. | Nem |
| sqlWriterStoredProcedureName | Annak a tárolt eljárásnak a neve, amely meghatározza, hogy a forrásadatok hogyan alkalmazhatók a célként megadott táblába. <br/>Ezt a tárolt eljárást *Batch*-ként kell meghívni. Olyan műveletekhez, amelyek csak egyszer futnak, és nem kell megtenniük a forrásadatokat, például törlés vagy csonkítás, használja `preCopyScript` a tulajdonságot. | Nem |
| storedProcedureTableTypeParameterName |A tárolt eljárásban megadott tábla típusú paraméter neve.  |Nem |
| sqlWriterTableType |A tárolt eljárásban használandó táblanév neve. A másolási tevékenység lehetővé teszi az áthelyezett adatáthelyezést egy ideiglenes táblában, amely ebben a táblázatban szerepel. A tárolt eljárási kód ezután egyesítheti a folyamatban lévő adattal másolt adatfájlokat. |Nem |
| storedProcedureParameters |A tárolt eljárás paraméterei.<br/>Az engedélyezett értékek név és érték párok. Nevek és a kis-és a paraméterek meg kell egyeznie a neveket és a kis-és nagybetűhasználatot, a tárolt eljárás paraméterértékeinek. | Nem |
| disableMetricsCollection | A Data Factory olyan mérőszámokat gyűjt, mint például a Azure SQL Database DTU a másolási teljesítmény optimalizálása és a javaslatok tekintetében. Ha ezt a viselkedést érinti, a kikapcsolásához válassza `true` a következőt:. | Nem (alapértelmezett `false`érték) |

**1. példa: Adathozzáfűzés**

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
                "writeBatchSize": 100000
            }
        }
    }
]
```

**2. példa: Tárolt eljárás meghívása a másolás során**

További információ az SQL-fogadó [tárolt eljárásának](#invoke-a-stored-procedure-from-a-sql-sink)meghívásáról.

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

## <a name="best-practice-for-loading-data-into-azure-sql-database"></a>Ajánlott eljárás az adatAzure SQL Databaseba való betöltéshez

Amikor az Adatmásolás Azure SQL Databaseba történik, különböző írási viselkedésre lehet szükség:

- [Hozzáfűzés](#append-data): A forrásadatok csak új rekordokkal rendelkeznek.
- [Upsert](#upsert-data): A forrásadatok a lapkákkal és a frissítésekkel is rendelkeznek.
- [Felülírás](#overwrite-the-entire-table): Minden alkalommal újra szeretném tölteni a teljes dimenzió táblát.
- [Írás Egyéni logikával](#write-data-with-custom-logic): További feldolgozásra van szükség a végső Beszúrás előtt a céltáblaba.

Tekintse át a megfelelő szakaszt a Azure Data Factory és az ajánlott eljárásokban való konfigurálásáról.

### <a name="append-data"></a>Adathozzáfűzés

Az adathozzáfűzés a Azure SQL Database fogadó összekötő alapértelmezett viselkedése. A Azure Data Factory egy tömeges beszúrást végez, hogy hatékonyan írjon a táblába. A forrást és a fogadót ennek megfelelően állíthatja be a másolási tevékenységbe.

### <a name="upsert-data"></a>Adatok beszúrása és frissítése (upsert)

**1. lehetőség:** Ha nagy mennyiségű adattal szeretne másolni, a következő megközelítéssel hajthat végre egy upsert: 

- Először használjon egy [adatbázis-hatókörű ideiglenes táblázatot](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current#database-scoped-global-temporary-tables-azure-sql-database) az összes rekord tömeges betöltéséhez a másolási tevékenység használatával. Mivel a rendszer nem naplózza az adatbázis-hatókörű ideiglenes táblákhoz tartozó műveleteket, másodpercenként több millió rekordot tölthet be.
- Egy tárolt eljárási tevékenység futtatása a Azure Data Factoryban [egyesítési](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) vagy INSERT/Update utasítás alkalmazásához. A temp táblát használja forrásként az összes frissítés vagy Beszúrás egyetlen tranzakcióként való végrehajtásához. Így csökken a kerekítési és a naplózási műveletek száma. A tárolt eljárási tevékenység végén a temp tábla lerövidíthető, hogy készen álljon a következő upsert ciklusra.

Azure Data Factory például létrehozhat egy **másolási tevékenységgel** rendelkező folyamatot egy **tárolt eljárási tevékenységgel**. A korábbi adatokat a forrás tárolójából egy Azure SQL Database ideiglenes táblába másolják, például: **# #UpsertTempTable**, mint a tábla neve az adatkészletben. Ezután az utóbbi egy tárolt eljárást hív meg, hogy egyesítse a forrás adatait a temp táblából a célként megadott táblába, és törölje a temp táblát.

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

**2. lehetőség:** Azt is megteheti, hogy [egy tárolt eljárást hív meg a másolási tevékenységen belül](#invoke-a-stored-procedure-from-a-sql-sink). Ez a módszer a forrás tábla minden egyes sorát futtatja ahelyett, hogy a másolási tevékenységben a tömeges beszúrást használja alapértelmezett megközelítésként, ami nem megfelelő a nagyméretű upsert.

### <a name="overwrite-the-entire-table"></a>A teljes táblázat felülírása

A másolási tevékenység fogadójában a **preCopyScript** tulajdonságot is konfigurálhatja. Ebben az esetben minden futó másolási tevékenységnél először Azure Data Factory futtatja a parancsfájlt. Ezután futtatja a másolatot az adatbeszúráshoz. Ha például felül szeretné írni a teljes táblázatot a legfrissebb adatokkal, adjon meg egy parancsfájlt, hogy először törölje az összes rekordot, mielőtt tömegesen betölti az új adatokat a forrásból.

### <a name="write-data-with-custom-logic"></a>Az adatírás egyéni logikával

Az egyéni logikával történő adatírás lépései hasonlóak az [Upsert](#upsert-data) -adatszakaszban leírt lépésekhez. Ha további feldolgozást kell alkalmaznia a forrásadatok végső beszúrása előtt a céltáblaba, nagy méret esetén a következő két dolog közül választhat:

- Töltse be az adatbázis-hatókörű ideiglenes táblát, majd hívja meg a tárolt eljárást. 
- Tárolt eljárás meghívása a másolás során.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a>Tárolt eljárás meghívása egy SQL-gyűjtőből

Amikor az adatok Azure SQL Databaseba másolhatók, a felhasználó által megadott tárolt eljárást további paraméterekkel is konfigurálhatja és meghívhatja. A tárolt eljárás funkció kihasználja a [tábla értékű paraméterek](https://msdn.microsoft.com/library/bb675163.aspx)előnyeit.

> [!TIP]
> A tárolt eljárás meghívása az adatsort soronként dolgozza fel egy tömeges művelet helyett, amelyet nem ajánlott nagyméretű másolásra használni. További információ az [adatok Azure SQL Databaseba való betöltésével kapcsolatos ajánlott eljárásokról](#best-practice-for-loading-data-into-azure-sql-database).

Tárolt eljárás használható, ha a beépített másolási mechanizmusok nem szolgálják ki a célt. Ilyen eset például, ha további feldolgozást szeretne alkalmazni a forrásadatok végső beszúrása előtt a céltáblaba. Néhány további feldolgozási példa: Ha egyesíteni szeretné az oszlopokat, további értékeket kereshet, és több táblába szúrhat be.

Az alábbi minta azt mutatja be, hogyan használható egy tárolt eljárás egy upsert egy táblázatba Azure SQL Database. Tegyük fel, hogy a bemeneti adatok és a fogadó **marketing** tábla mindhárom oszlopból áll: **ProfileID**, **állapot**és **Kategória**. A upsert a **ProfileID** oszlop alapján végezze el, és csak a "producta" nevű adott kategóriára alkalmazza.

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

3. A Azure Data Factory a másolási tevékenységben adja meg az **SQL** -fogadó szakaszt a következőképpen:

    ```json
    "sink": {
        "type": "AzureSqlSink",
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

## <a name="mapping-data-flow-properties"></a>Adatfolyam-tulajdonságok leképezése

A forrás- [átalakítás](data-flow-source.md) és a fogadó [átalakítás](data-flow-sink.md) részleteinek megismerése a leképezési folyamatokban.

## <a name="data-type-mapping-for-azure-sql-database"></a>Azure SQL Database adattípusának leképezése

Az adatoknak a vagy a Azure SQL Databaseba való másolásakor a rendszer a következő leképezéseket használja Azure SQL Database adattípusokból Azure Data Factory köztes adattípusokra. Ha szeretné megtudni, hogyan képezi le a másolási tevékenység a forrás sémát és az adattípust a fogadóra, tekintse meg a [séma-és adattípus](copy-activity-schema-and-type-mapping.md)-leképezéseket

| Azure SQL Database adattípus | Azure Data Factory időközi adattípus |
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
> A decimális ideiglenes típusra leképezett adattípusok esetében jelenleg Azure Data Factory támogatja a pontosságot akár 28-ra. Ha 28-nál nagyobb pontosságú adatmennyiséget tartalmaz, érdemes lehet karakterlánccá konvertálni az SQL-lekérdezésben.

## <a name="next-steps"></a>További lépések
A Azure Data Factoryban a másolási tevékenység által forrásként és nyelőként támogatott adattárak listáját a [támogatott adattárak és-formátumok](copy-activity-overview.md##supported-data-stores-and-formats)című részben tekintheti meg.
