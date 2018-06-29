---
title: Adatok másolása és az Azure SQL Data Warehouse Azure Data Factory használatával |} Microsoft Docs
description: Ismerje meg az adatok másolása az Azure SQL Data Warehouse támogatott forrás áruházakból vagy az SQL Data Warehouse támogatott fogadó áruházak Data Factory használatával.
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
ms.date: 05/28/2018
ms.author: jingwang
ms.openlocfilehash: 42ffdbf117b3f522e27e6e46628231ddb8221018
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37051627"
---
#  <a name="copy-data-to-or-from-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Másolja a adatok vagy az Azure SQL Data Warehouse Azure Data Factory használatával 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you're using:"]
> * [Version1 ](v1/data-factory-azure-sql-data-warehouse-connector.md)
> * [Aktuális verzió](connector-azure-sql-data-warehouse.md)

Ez a cikk ismerteti, hogyan másolási tevékenység az Azure Data Factoryben az adatok másolása, vagy az Azure SQL Data Warehouse. Buildekről nyújtanak a [másolási tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

## <a name="supported-capabilities"></a>Támogatott képességei

Azure SQL Data Warehouse adatok bármely támogatott fogadó adattárolóhoz másolhatja. És átmásolhatja adatok bármely támogatott forrás adattár az Azure SQL Data Warehouse. A másolási tevékenység által támogatott adatforrások vagy mosdók adattárolókhoz listájáért lásd: a [adatokról és formátumok támogatott](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Az Azure SQL Data Warehouse-összekötő kifejezetten, ezeket a funkciókat támogatja:

- Másolja az adatokat a szolgáltatás egyszerű vagy a felügyelt szolgáltatás identitásának (MSI) SQL-hitelesítést és az Azure Active Directory (Azure AD) alkalmazás tokent használó hitelesítés használatával.
- Forrásként egy SQL-lekérdezést vagy tárolt eljárás használatával adatainak beolvasása.
- A fogadó, mint adatok betöltése a PolyBase vagy a tömeges beszúrás. A PolyBase másolási teljesítmény növelése érdekében ajánlott.

> [!IMPORTANT]
> Ügyeljen arra, hogy a PolyBase csak SQL-hitelesítést, de nem az Azure AD-alapú hitelesítés támogatja-e.

> [!IMPORTANT]
> Ha az adatok másolása Azure Data Factory integrációs futásidejű használatával, konfigurálja egy [Azure SQL-kiszolgáló tűzfal](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) , hogy az Azure-szolgáltatásokhoz férhetnek hozzá a kiszolgálóhoz.
> Ha egy önálló üzemeltetett integrációs futásidejű használatával adatok másolása az Azure SQL server tűzfal engedélyezze a megfelelő IP-címtartomány megadása A tartományba beletartozik a gép IP-cím segítségével csatlakozzon az Azure SQL Database.

## <a name="get-started"></a>Bevezetés

> [!TIP]
> A legjobb teljesítmény érdekében az adatok betöltése az Azure SQL Data Warehouse polybase szolgáltatást akkor használja. A [használja a PolyBase az adatok betöltése az Azure SQL Data Warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse) szakasz részleteket tartalmaz. A használati esetek bemutatóért lásd: [1 TB-os betöltése az Azure SQL Data Warehouse a 15 perc Azure Data Factory](load-azure-sql-data-warehouse.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok részletesen bemutatják az Azure SQL Data Warehouse-összekötő a Data Factory tartozó entitások meghatározó tulajdonságok biztosítása.

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok

A következő tulajdonságok támogatottak az Azure SQL Data Warehouse kapcsolódó szolgáltatás:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot meg kell **AzureSqlDW**. | Igen |
| connectionString | Adja meg az Azure SQL Data Warehouse-példány való kapcsolódáshoz szükséges adatokat a **connectionString** tulajdonság. Jelölje meg ebben a mezőben, mint egy **SecureString** tárolja biztonságos helyen, a Data factoryban vagy [hivatkozik az Azure Key Vault tárolt titkos kulcs](store-credentials-in-key-vault.md). | Igen |
| servicePrincipalId | Adja meg az alkalmazás ügyfél-azonosítót. | Igen, az Azure AD-alapú hitelesítés használatakor egy szolgáltatásnevet. |
| servicePrincipalKey | Adja meg az alkalmazás kulcsot. Ez a mező megjelölése a SecureString tárolja biztonságos helyen, a Data factoryban vagy [hivatkozik az Azure Key Vault tárolt titkos kulcs](store-credentials-in-key-vault.md). | Igen, az Azure AD-alapú hitelesítés használatakor egy szolgáltatásnevet. |
| bérlő | Adja meg a bérlői adatokat (tartomány nevét vagy a bérlő azonosító) alatt az alkalmazás található. Azt az Azure-portál jobb felső sarkában az egér rámutató által kérheti le. | Igen, az Azure AD-alapú hitelesítés használatakor egy szolgáltatásnevet. |
| connectVia | A [integrációs futásidejű](concepts-integration-runtime.md) csatlakozni az adattárolóhoz használandó. Használhat Azure integrációs futásidejű vagy egy önálló üzemeltetett integrációs futásidejű (amennyiben az adattároló egy magánhálózaton található). Ha nincs megadva, akkor használja az alapértelmezett Azure integrációs futásidejű. | Nem |

Különböző hitelesítési típusok tekintse meg az alábbi szakaszok az Előfeltételek és a JSON-mintákat, illetve:

- [SQL-hitelesítés](#sql-authentication)
- Az Azure AD alkalmazás jogkivonat-alapú hitelesítés: [egyszerű szolgáltatásnév](#service-principal-authentication)
- Az Azure AD alkalmazás jogkivonat-alapú hitelesítés: [felügyelt identitás](#managed-service-identity-authentication)

### <a name="sql-authentication"></a>SQL-hitelesítés

#### <a name="linked-service-example-that-uses-sql-authentication"></a>SQL-hitelesítést használó társított szolgáltatás – példa

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

### <a name="service-principal-authentication"></a>Egyszerű szolgáltatásnév hitelesítése

A szolgáltatás az Azure AD-alapú alkalmazás tokent használó hitelesítés használatához kövesse az alábbi lépéseket:

1. **[Hozzon létre egy Azure Active Directory-alkalmazás](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application)**  Azure-portálról. Jegyezze fel az alkalmazás nevét és a következő értékek, amelyek meghatározzák a társított szolgáltatás:

    - Alkalmazásazonosító
    - Alkalmazás kulcs
    - Bérlőazonosító

2. **[Egy Azure Active Directory-rendszergazda kiépítése](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)**  az Azure SQL-kiszolgáló, az Azure portálon, ha még nem tette. Az Azure AD-rendszergazda az Azure AD-felhasználó vagy az Azure AD-csoport lehet. Ha a csoportban található MSI engedélyez egy rendszergazdai szerepkört, hagyja ki a 3. és 4. A rendszergazda az adatbázis teljes hozzáféréssel fog rendelkezni.

3. **[Hozzon létre a tartalmazott adatbázis-felhasználók](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)**  a szolgáltatás rendszerbiztonsági tag. Csatlakozás a adatraktárát, vagy amelyek át kívánja másolni az adatok eszközökkel SSMS, például egy Azure AD-identitás, amely rendelkezik legalább bármely felhasználó ALTER engedéllyel. Futtassa a következő T-SQL:
    
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. **Adja meg a szükséges engedélyekkel a szolgáltatás egyszerű** szokásos módon SQL felhasználók vagy mások számára. Futtassa a következő kódot:

    ```sql
    EXEC sp_addrolemember [role name], [your application name];
    ```

5. **Egy Azure SQL Data Warehouse társított szolgáltatás beállítása** az Azure Data Factory.


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Szolgáltatás egyszerű hitelesítést használó társított szolgáltatás – példa

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

### <a name="managed-service-identity-authentication"></a>Felügyelt Szolgáltatásidentitás hitelesítés

Egy adat-előállító is társítható egy [Szolgáltatásidentitás felügyelt](data-factory-service-identity.md) , amely az adott gyári jelöli. A szolgáltatásidentitás használhatja az Azure SQL Data Warehouse-hitelesítéshez. Férhetnek hozzá a kijelölt gyári, és a vagy az adatok másolása az adatraktár-e identitásával.

> [!IMPORTANT]
> Vegye figyelembe, hogy a PolyBase jelenleg nem támogatott az MSI-hitelesítéshez.

Az Azure AD MSI-alapú alkalmazás tokent használó hitelesítés használatához kövesse az alábbi lépéseket:

1. **Hozzon létre egy csoportot az Azure ad-ben.** A gyári MSI legyen a csoport tagja.

    a. Keresés a data factory szolgáltatásidentitás Azure-portálról. Lépjen a data factory **tulajdonságok**. Másolja a szolgáltatás IDENTITÁSÁNAK azonosítóját.

    b. Telepítse a [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2) modul. Jelentkezzen be a `Connect-AzureAD` parancsot. Futtassa a következő parancsok futtatásával hozzon létre egy csoportot, és adja hozzá a data factory MSI tagja.
    ```powershell
    $Group = New-AzureADGroup -DisplayName "<your group name>" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId "<your data factory service identity ID>"
    ```

2. **[Egy Azure Active Directory-rendszergazda kiépítése](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)**  az Azure SQL-kiszolgáló, az Azure portálon, ha még nem tette.

3. **[Hozzon létre a tartalmazott adatbázis-felhasználók](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)**  az Azure AD-csoport. Csatlakozás a adatraktárát, vagy amelyek át kívánja másolni az adatok eszközökkel SSMS, például egy Azure AD-identitás, amely rendelkezik legalább bármely felhasználó ALTER engedéllyel. Futtassa a következő T-SQL. 
    
    ```sql
    CREATE USER [your Azure AD group name] FROM EXTERNAL PROVIDER;
    ```

4. **Adja meg a szükséges engedélyekkel az Azure AD-csoport** szokásos módon az SQL-felhasználók és a többi. Például futtassa a következő kódot.

    ```sql
    EXEC sp_addrolemember [role name], [your Azure AD group name];
    ```

5. **Egy Azure SQL Data Warehouse társított szolgáltatás beállítása** az Azure Data Factory.

#### <a name="linked-service-example-that-uses-msi-authentication"></a>MSI-hitelesítést használó társított szolgáltatás – példa

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

Szakaszok és meghatározása adatkészletek esetében elérhető tulajdonságok teljes listáját lásd: a [adatkészletek](https://docs.microsoft.com/en-us/azure/data-factory/concepts-datasets-linked-services) cikk. Ez a témakör az Azure SQL Data Warehouse adatkészlet által támogatott tulajdonságokról.

Másolja az adatokat, vagy az Azure SQL Data Warehouse, állítsa be a **típus** a DataSet tulajdonság **AzureSqlDWTable**. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A **típus** értékre kell állítani a DataSet tulajdonság **AzureSqlDWTable**. | Igen |
| tableName | A tábla vagy nézet, amelyre a társított szolgáltatás hivatkozik Azure SQL Data Warehouse-példány neve. | Igen |

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
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és a rendelkezésre álló tevékenységek meghatározó tulajdonságok teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a témakör az Azure SQL Data Warehouse-forrás és a fogadó által támogatott tulajdonságokról.

### <a name="azure-sql-data-warehouse-as-the-source"></a>Az SQL Data Warehouse forrásaként

Adatok másolása az Azure SQL Data Warehouse, állítsa be a **típus** tulajdonság a másolási tevékenység forrásból történő **SqlDWSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakasz:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A **típus** értékre kell állítani a másolási tevékenység forrás tulajdonság **SqlDWSource**. | Igen |
| sqlReaderQuery | Az egyéni SQL-lekérdezés segítségével adatokat olvasni. Példa: `select * from MyTable`. | Nem |
| sqlReaderStoredProcedureName | A tárolt eljárás, amely adatokat olvas a forrástábla neve. Az utolsó SQL-utasítás a következő tárolt eljárást a SELECT utasítással kell lennie. | Nem |
| storedProcedureParameters | A tárolt eljárás paramétereit.<br/>Megengedett értékek:-név és érték párokat. Nevét és a kis-és a paraméterek meg kell egyeznie a nevek és a kis-és nagybetűhasználat a tárolt eljárás paramétereit. | Nem |

### <a name="points-to-note"></a>Vegye figyelembe mutat

- Ha a **sqlReaderQuery** van megadva a **SqlSource**, a másolási tevékenység fut ez a lekérdezés az adatok lekérdezése az Azure SQL Data Warehouse forrás. Vagy megadhatja a tárolt eljárást. Adja meg a **sqlReaderStoredProcedureName** és **storedProcedureParameters** Ha a tárolt eljárás paraméterek fogadja el.
- Ha nem adja meg vagy **sqlReaderQuery** vagy **sqlReaderStoredProcedureName**, az oszlopok definiálva a **struktúra** célra szolgálnak az adatkészlet JSON szakasza a lekérdezés létrehozásához. `select column1, column2 from mytable` Azure SQL Data Warehouse fut. Ha az adatkészlet-definícióban nem rendelkezik a **struktúra**, kiválasztott összes oszlop a táblából.
- Amikor **sqlReaderStoredProcedureName**, továbbra is meg kell adnia egy helyőrző **tableName** az adatkészlet JSON tulajdonság.

#### <a name="sql-query-example"></a>SQL-lekérdezés példa

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

### <a name="azure-sql-data-warehouse-as-sink"></a> Az SQL Data Warehouse mint fogadó

Adatok másolása az Azure SQL Data Warehouse, állítsa a fogadó típusa másolási tevékenység **SqlDWSink**. A következő tulajdonságok támogatottak a másolási tevékenység **fogadó** szakasz:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A **típus** értékre kell állítani a másolási tevékenység fogadó tulajdonságának **SqlDWSink**. | Igen |
| allowPolyBase | Azt jelzi, hogy ha alkalmazható, a PolyBase használata helyett a BULKINSERT mechanizmus. <br/><br/> Azt javasoljuk, hogy Ön adatok betöltése az SQL Data Warehouse PolyBase használatával. Lásd: a [használja a PolyBase az adatok betöltése az Azure SQL Data Warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse) szakaszban a korlátozások és részleteit.<br/><br/>Két érték engedélyezett **igaz** és **hamis** (alapértelmezett).  | Nem |
| polyBaseSettings | Egy csoport, amely tulajdonságok megadott, amikor a **allowPolybase** tulajdonsága **igaz**. | Nem |
| rejectValue | Megadja a szám vagy is el kell utasítani, mielőtt a lekérdezés nem sikerült sorokat százalékát.<br/><br/>Ismerje meg a PolyBase utasítsa el az lehetőségeket argumentumok szakaszában [külső tábla létrehozása (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx). <br/><br/>Megengedett értékek: 0 (alapértelmezés), 1, 2, stb. |Nem |
| rejectType | Megadja, hogy a **rejectValue** lehetőség konstans értéket vagy százalékában.<br/><br/>Két érték engedélyezett **érték** (alapértelmezett) és **százalékos**. | Nem |
| rejectSampleValue | Mielőtt PolyBase újraszámítja a visszautasított sorok százalékát beolvasandó sorok számát határozza meg.<br/><br/>Megengedett értékek: 1, 2, stb. | Igen, ha a **rejectType** van **százalékos**. |
| useTypeDefault | Megadja, hogyan legyen kezelve tagolt szövegfájlok a hiányzó értékeket, amikor a PolyBase kér le adatokat a szövegfájlból.<br/><br/>Ezt a tulajdonságot, az argumentumok ismertető részben olvashat [létrehozása külső FÁJLFORMÁTUM (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx).<br/><br/>Két érték engedélyezett **igaz** és **hamis** (alapértelmezett). | Nem |
| writeBatchSize | Adatok beszúrása az SQL táblázatba, amikor a puffer mérete eléri a következőt **writeBatchSize**. Érvényes, csak ha PolyBase nem használja.<br/><br/>Az engedélyezett érték **egész** (sorok száma). | Nem. Az alapértelmezett beállítás 10000. |
| writeBatchTimeout | Várakozási idő a kötegelt beszúrási művelet befejezését, mielőtt azt az időkorlátot. Érvényes, csak ha PolyBase nem használja.<br/><br/>Az engedélyezett érték **timespan**. Példa: "00: 30:00" (30 perc). | Nem |
| preCopyScript | Adjon meg egy SQL-lekérdezés futtatása előtt az adatok írása az Azure SQL Data Warehouse minden egyes futtatásához a másolási tevékenység. Ez a tulajdonság használatával az előre betöltött adatok törlése. | Nem | (#repeatability során-másolási). | A lekérdezési utasítást. | Nem |

#### <a name="sql-data-warehouse-sink-example"></a>Az SQL Data Warehouse fogadó – példa

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

A PolyBase használatával hatékonyan betöltése az SQL Data Warehouse a következő szakaszban olvashat.

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>Adatok betöltése az Azure SQL Data Warehouse PolyBase segítségével

Használatával [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) hatékonyan nagy mennyiségű adatok betöltése az Azure SQL Data Warehouse nagy átviteli sebességgel. Látni fogja a átviteli sebességének nagy nyereség helyett az alapértelmezett BULKINSERT mechanizmus a PolyBase használatával. Lásd: [teljesítményfigyelési](copy-activity-performance.md#performance-reference) részletes összehasonlítását. A használati esetek bemutatóért lásd: [1 TB-os betöltése az Azure SQL Data Warehouse](https://docs.microsoft.com/en-us/azure/data-factory/v1/data-factory-load-sql-data-warehouse).

* Ha a forrásadatok az Azure Blob-tároló vagy az Azure Data Lake Store, és a formátuma nem kompatibilis a PolyBase, a Másolás közvetlen az Azure SQL Data Warehouse PolyBase használatával. További információkért lásd:  **[másolás közvetlen a PolyBase használatával](#direct-copy-by-using-polybase)**.
* Ha a forrás-tárolót és formátum eredetileg nem támogatott a PolyBase által, a **[előkészített másolása a PolyBase használatával](#staged-copy-by-using-polybase)** inkább a beállítást. Az előkészített másolási szolgáltatás is biztosít nagyobb átviteli sebesség. Automatikusan átalakítja az adatokat a PolyBase-kompatibilis formátumban. És az Azure Blob Storage tárolóban tárolja az adatokat. Majd betölti az adatokat az SQL Data Warehouse.

> [!IMPORTANT]
> Vegye figyelembe, hogy a PolyBase jelenleg nem támogatott az Azure AD-alkalmazás MSI-alapú tokent használó hitelesítés.

### <a name="direct-copy-by-using-polybase"></a>A PolyBase használatával közvetlen másolása

SQL Data Warehouse PolyBase közvetlenül a Azure-Blob és az Azure Data Lake Store támogatja. Szolgáltatás egyszerű használja forrásként, és adott fájl formátuma követelményekkel rendelkezik. Ha a forrásadatok megfelel a jelen szakaszban bemutatott, közvetlen másolása a forrás-tárolót az Azure SQL Data Warehouse PolyBase segítségével. Ellenkező esetben használja [másolása a PolyBase használatával előkészített](#staged-copy-by-using-polybase).

> [!TIP]
> Adatok másolása hatékonyan Data Lake Store az SQL Data Warehouse, további információhoz [Azure Data Factory megkönnyíti még és kényelmes elvégzésével nyújt betekintést az adatokat, az SQL Data Warehouse szolgáltatással Data Lake Store használatakor](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/).

A feltételeknek nem felel meg, ha az Azure Data Factory ellenőrzi a beállításait, és automatikusan visszaáll az adatátvitelt jelölik a BULKINSERT mechanizmus.

1. A **forrás társított szolgáltatás** típus **AzureStorage** vagy **AzureDataLakeStore** szolgáltatás egyszerű hitelesítéssel.
2. A **bemeneti adatkészlet** típus **AzureBlob** vagy **AzureDataLakeStoreFile**. A formátum típusa alapján `type` tulajdonságai **OrcFormat**, **ParquetFormat**, vagy **szöveges**, az a következő beállításokat:

   1. `rowDelimiter` kell **\n**.
   2. `nullValue` értéke **üres karakterlánc** (""), vagy `treatEmptyAsNull` értéke **igaz**.
   3. `encodingName` értéke **utf-8**, az alapértelmezett érték.
   4. `escapeChar`, `quoteChar`, `firstRowAsHeader`, és `skipLineCount` nincsenek megadva.
   5. `compression` lehet **tömörítés**, **GZip**, vagy **Deflate**.

    ```json
    "typeProperties": {
       "folderPath": "<blobpath>",
       "format": {
           "type": "TextFormat",
           "columnDelimiter": "<any delimiter>",
           "rowDelimiter": "\n",
           "nullValue": "",
           "encodingName": "utf-8"
       },
       "compression": {
           "type": "GZip",
           "level": "Optimal"
       }
    },
    ```

3. Nincs nincs `skipHeaderLineCount` beállítás alatt **BlobSource** vagy **AzureDataLakeStore** az adatcsatorna másolási tevékenységhez.
4. Nincs nincs `sliceIdentifierColumnName` beállítás alatt **SqlDWSink** az adatcsatorna másolási tevékenységhez. A PolyBase biztosítja, hogy az összes adat frissül, vagy nem frissül, az egyszeri futtatás. Eléréséhez **ismételhetőség**, használjon `sqlWriterCleanupScript`.

```json
"activities":[
    {
        "name": "CopyFromAzureBlobToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "BlobDataset",
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
                "type": "BlobSource",
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            }
        }
    }
]
```

### <a name="staged-copy-by-using-polybase"></a>A PolyBase használatával előkészített másolása

Amikor a forrásadatok nem felel meg az előző szakaszban, lehetővé teszik az adatok másolása egy ideiglenes átmeneti Azure Blob storage példány keresztül. Prémium szintű Azure Storage nem lehet. Ebben az esetben Azure Data Factory automatikusan futó átalakítások adatok formátum követelményeinek teljesítése érdekében a PolyBase az adatokat. A PolyBase majd az adatok betöltése az SQL Data Warehouse használ. Végül azt a szükségtelenné vált az a blob storage ideiglenes adatait. Lásd: [másolási előkészített](copy-activity-performance.md#staged-copy) egy átmeneti Azure Blob storage-példányt keresztül az adatok másolásának vonatkozó további információért.

Ez a funkció használatához hozzon létre egy [Azure Storage társított szolgáltatásnak](connector-azure-blob-storage.md#linked-service-properties) , amely hivatkozik a közbenső blob tároló Azure storage-fiók. Adja meg a `enableStaging` és `stagingSettings` tulajdonságai között a másolási tevékenység során az alábbi kódban látható módon:

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

## <a name="best-practices-for-using-polybase"></a>Gyakorlati tanácsok a PolyBase használatával

A következő szakaszokban mellett a gyakorlatok [ajánlott eljárások az Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-best-practices.md).

### <a name="required-database-permission"></a>Adatbázis szükséges engedéllyel

A PolyBase használatához rendelkeznie kell a felhasználót, hogy az adatok betöltése az SQL Data Warehouse ["Vezérlő" engedély](https://msdn.microsoft.com/library/ms191291.aspx) a célként megadott adatbázison. A felhasználó hozzáadása tagjaként egyik módja, amely a **db_owner** szerepkör. Útmutató: ehhez a [SQL Data Warehouse áttekintés](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

### <a name="row-size-and-data-type-limits"></a>Írja be korlátok sorméret és adatok

A PolyBase terhelések 1 MB-nál kisebb sorok korlátozódnak. Ezek VARCHR(MAX), NVARCHAR(MAX) vagy VARBINARY(MAX) nem lehet betölteni. További információkért lásd: [SQL Data Warehouse szolgáltatás kapacitáskorlátait](../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Amikor a forrásadatok 1 MB-nál nagyobb sora van, érdemes a forrástáblákból függőleges felosztása több kis néhányat a meglévők közül. Győződjön meg arról, hogy minden egyes sorára legnagyobb mérete nem haladhatja meg a határértéket. A kisebb táblák majd betöltése a PolyBase használatával, és egyesíti az Azure SQL Data Warehouse.

### <a name="sql-data-warehouse-resource-class"></a>Az SQL Data Warehouse erőforrás osztály

A legjobb lehetséges átviteli sebesség eléréséhez rendelje hozzá a felhasználót, hogy az adatok betöltése az SQL Data Warehouse polybase nagyobb erőforrásosztály.

### <a name="tablename-in-azure-sql-data-warehouse"></a>**tableName** az Azure SQL Data Warehouse

Az alábbi táblázat példákat mutat be, hogyan adhatja meg a **tableName** JSON adatkészlet tulajdonság. Azt mutatja, hogy több kombinációi séma és a táblázat neve.

| Megadott adatbázissémát | Tábla neve | **tableName** JSON tulajdonság |
| --- | --- | --- |
| dbo | Táblanév | MyTable vagy dbo. MyTable vagy [dbo]. [MyTable] |
| dbo1 | Táblanév | dbo1. MyTable vagy [dbo1]. [MyTable] |
| dbo | My.Table | [My.Table] vagy [dbo]. [My.Table] |
| dbo1 | My.Table | [dbo1].[My.Table] |

Ha a következő hibát látja, a problémát valószínűleg a megadott érték a **tableName** tulajdonság. Lásd az előző táblázatban a megfelelő módszer értékeinek megadása a **tableName** JSON tulajdonság.

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

### <a name="columns-with-default-values"></a>Az alapértelmezett értékekkel oszlopok

A PolyBase szolgáltatás adat-előállítóban jelenleg csak az azonos számú oszlopot, mint a céltábla fogad el. Példa: ha egyikük van definiálva az alapértelmezett érték négy oszlopokkal rendelkező táblát. A bemeneti adatok továbbra is kell tartoznia a négy oszlopot. Három oszlop a bemeneti adatkészletet az alábbihoz hasonló hibát eredményez:

```
All columns of the table must be specified in the INSERT BULK statement.
```

A NULL érték egy speciális formája, amelyet az alapértelmezett érték. Ha az oszlop nullázható, az adott oszlop található a bemeneti adatok lehet üres. Azonban nem lehet a bemeneti adatkészletből hiányzik. PolyBase az Azure SQL Data Warehouse a hiányzó értékeket szúr be NULL.

## <a name="data-type-mapping-for-azure-sql-data-warehouse"></a>Adattípus-hozzárendelése az Azure SQL Data Warehouse

Ha a vagy az Azure SQL Data Warehouse-adatok másolása a következő megfeleltetéseket használ az Azure SQL Data Warehouse adattípusok az Azure Data Factory ideiglenes adattípusok. Lásd: [séma- és írja be a leképezéseket](copy-activity-schema-and-type-mapping.md) megtudhatja, hogyan másolási tevékenység van leképezve a séma- és adatok típusa a fogadó.

| Az Azure SQL Data Warehouse-adattípus | Data Factory ideiglenes adattípus |
|:--- |:--- |
| bigint | Int64 |
| Bináris | Byte] |
| bit | Logikai |
| karakter | Karakterlánc, Char] |
| dátum | DateTime |
| Dátum és idő | DateTime |
| datetime2 | DateTime |
| Datetimeoffset | DateTimeOffset |
| Decimális | Decimális |
| A FILESTREAM attribútum (varbinary(max)) | Byte] |
| Lebegőpontos | Dupla |
| image | Byte] |
| int | Int32 |
| pénz | Decimális |
| nchar | Karakterlánc, Char] |
| ntext | Karakterlánc, Char] |
| numerikus | Decimális |
| nvarchar | Karakterlánc, Char] |
| valós | Önálló |
| ROWVERSION | Byte] |
| smalldatetime | DateTime |
| smallint | Int16 |
| kis pénz típusú értéknél | Decimális |
| sql_variant | Objektum * |
| szöveg | Karakterlánc, Char] |
| time | A TimeSpan |
| időbélyeg | Byte] |
| tinyint | Bájt |
| egyedi azonosító | GUID |
| varbinary | Byte] |
| varchar | Karakterlánc, Char] |
| xml | Xml |

## <a name="next-steps"></a>További lépések
Források és mosdók másolási tevékenység során az Azure Data Factory által támogatott adattárolókhoz listájáért lásd: [adatokról és formátumok támogatott](copy-activity-overview.md##supported-data-stores-and-formats).
