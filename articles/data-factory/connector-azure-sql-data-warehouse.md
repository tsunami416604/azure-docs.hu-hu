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
ms.devlang: na
ms.topic: conceptual
ms.date: 07/28/2018
ms.author: jingwang
ms.openlocfilehash: 7a9adc8e9b7bcf69cce6b8ecf00e44477c1b0da3
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39430739"
---
#  <a name="copy-data-to-or-from-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Adatok másolása, vagy az Azure SQL Data Warehouse-ból az Azure Data Factory használatával 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you're using:"]
> * [Verzió1 ](v1/data-factory-azure-sql-data-warehouse-connector.md)
> * [Aktuális verzió](connector-azure-sql-data-warehouse.md)

Ez a cikk azt ismerteti, hogyan Azure Data Factory másolási tevékenység használatával másolja az adatokat, vagy az Azure SQL Data Warehouse-ból. Épül a [másolási tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

## <a name="supported-capabilities"></a>Támogatott képességek

Az Azure SQL Data Warehouse-ból adatokat másolhatja bármely támogatott fogadó adattárba. És másolhat bármely támogatott forrásadattárból adatokat az Azure SQL Data Warehouse. A másolási tevékenység által források vagy fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak és formátumok](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Pontosabban az Azure SQL Data Warehouse-összekötő támogatja ezeket a funkciókat:

- Adatok másolása az SQL-hitelesítés és Azure Active Directory (Azure AD) alkalmazástoken-hitelesítésének használatával egy egyszerű szolgáltatást, vagy a Felügyeltszolgáltatás-identitás (MSI).
- Forrásként adatok lekérdezése egy SQL-lekérdezést vagy tárolt eljárás használatával.
- Fogadóként az adatok betöltése a PolyBase vagy a tömeges beszúrás. A PolyBase a jobb másolási teljesítmény érdekében javasoljuk.

> [!IMPORTANT]
> Vegye figyelembe, hogy a PolyBase támogatja, csak SQL-hitelesítést, de nem Azure AD-hitelesítés.

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

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot állítsa **AzureSqlDW**. | Igen |
| kapcsolati Sztringje | Adja meg a az Azure SQL Data Warehouse-példányhoz való csatlakozáshoz szükséges információkat a **connectionString** tulajdonság. Jelölje meg a mező egy **SecureString** tárolja biztonságos helyen a Data Factory áttekintése, vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). | Igen |
| servicePrincipalId | Adja meg az alkalmazás ügyfél-azonosítót. | Igen, egy egyszerű szolgáltatást az Azure AD-hitelesítés használata esetén. |
| servicePrincipalKey | Adja meg az alkalmazáskulcsot. Ez a mező megjelölése tárolja biztonságos helyen a Data Factory, a SecureString vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). | Igen, egy egyszerű szolgáltatást az Azure AD-hitelesítés használata esetén. |
| bérlő | Adja meg a bérlő információkat (tartomány neve vagy a bérlő azonosítója) alatt az alkalmazás található. Kérheti a viszi az egérmutatót az Azure portal jobb felső sarkában. | Igen, egy egyszerű szolgáltatást az Azure AD-hitelesítés használata esetén. |
| connectVia | A [integrációs modul](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. Használhatja az Azure integrációs modul vagy a saját üzemeltetésű integrációs modul (ha az adattár egy magánhálózaton található). Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. | Nem |

Különböző hitelesítési típus tekintse meg a következő szakaszok az Előfeltételek és a JSON-minták, illetve:

- [SQL-hitelesítés](#sql-authentication)
- Az Azure AD alkalmazástoken-hitelesítésének: [egyszerű szolgáltatás](#service-principal-authentication)
- Az Azure AD alkalmazástoken-hitelesítésének: [Felügyeltszolgáltatás-identitás](#managed-service-identity-authentication)

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

### <a name="service-principal-authentication"></a>Egyszerű szolgáltatásnév hitelesítése

Szolgáltatás egyszerűszolgáltatás-alapú Azure AD alkalmazástoken-hitelesítésének használatához kövesse az alábbi lépéseket:

1. **[Az Azure Active Directory-alkalmazás létrehozása](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application)**  az Azure Portalról. Jegyezze fel az alkalmazás nevét és a következő értékeket, amelyek meghatározzák a társított szolgáltatást:

    - Alkalmazásazonosító
    - Alkalmazáskulcs
    - Bérlőazonosító

1. **[Az Azure Active Directory-rendszergazda kiépítése](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)**  az Azure SQL Serverhez, az Azure Portalon, ha ezt még nem tette meg. Az Azure AD-rendszergazda lehet egy Azure AD-felhasználó vagy az Azure AD-csoporthoz. Ha a rendszergazda szerepkörrel, engedélyezze a csoportnak MSI-vel, hagyja ki a 3. és 4. A rendszergazda az adatbázis teljes hozzáféréssel fog rendelkezni.

1. **[Hozzon létre tartalmazottadatbázis-felhasználók](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)**  egyszerű szolgáltatás számára. A data warehouse-bA a, vagy amely adatok másolása az Azure AD identitás, amelynek legalább az ssms-ben, más eszközök használatával szeretné bármely felhasználó ALTER engedéllyel. Futtassa a következő T-SQL:
    
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

1. **Adja meg a szükséges engedélyekkel a szolgáltatásnév** szokásos módon SQL-felhasználók vagy mások számára. Futtassa a következő kódot:

    ```sql
    EXEC sp_addrolemember [role name], [your application name];
    ```

1. **Egy Azure SQL Data Warehouse társított szolgáltatás konfigurálása** az Azure Data Factoryban.


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

### <a name="managed-service-identity-authentication"></a>Felügyeltszolgáltatás-identitás felügyelt hitelesítésre

Adat-előállító társítható egy [Felügyeltszolgáltatás-identitás](data-factory-service-identity.md) , amely az adott előállító jelöli. A felügyeltszolgáltatás-identitás az Azure SQL Data Warehouse-hitelesítéshez használható. Férhet hozzá a kijelölt gyári, és a, vagy az adatok másolása az adatraktár-Ez az identitás használatával.

> [!IMPORTANT]
> Vegye figyelembe, hogy a PolyBase jelenleg nem támogatott az MSI-hitelesítéssel.

MSI-alapú Azure AD alkalmazástoken-hitelesítésének használatához kövesse az alábbi lépéseket:

1. **Hozzon létre egy csoportot az Azure ad-ben.** Ellenőrizze az MSI-előállító a csoport tagja.

    a. Keresse meg a data factory felügyeltszolgáltatás-identitás az Azure Portalról. Nyissa meg az adat-előállító **tulajdonságok**. Másolja az IDENTITÁS azonosítót.

    b. Telepítse a [az Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2) modul. Jelentkezzen be a `Connect-AzureAD` parancsot. Hozzon létre egy csoportot, és adja hozzá az adat-előállító MSI, amelynek a következő parancsok futtatásával.
    ```powershell
    $Group = New-AzureADGroup -DisplayName "<your group name>" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId "<your data factory service identity ID>"
    ```

1. **[Az Azure Active Directory-rendszergazda kiépítése](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)**  az Azure SQL Serverhez, az Azure Portalon, ha ezt még nem tette meg.

1. **[Hozzon létre tartalmazottadatbázis-felhasználók](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)**  az Azure AD-csoport. A data warehouse-bA a, vagy amely adatok másolása az Azure AD identitás, amelynek legalább az ssms-ben, más eszközök használatával szeretné bármely felhasználó ALTER engedéllyel. Futtassa a következő T-SQL. 
    
    ```sql
    CREATE USER [your Azure AD group name] FROM EXTERNAL PROVIDER;
    ```

1. **Adja meg a szükséges engedélyekkel az Azure AD-csoport** szokásos módon SQL-felhasználók és mások. Például futtassa a következő kódot.

    ```sql
    EXEC sp_addrolemember [role name], [your Azure AD group name];
    ```

1. **Egy Azure SQL Data Warehouse társított szolgáltatás konfigurálása** az Azure Data Factoryban.

#### <a name="linked-service-example-that-uses-msi-authentication"></a>MSI-hitelesítést használó társított szolgáltatás példa

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

Szakaszok és adatkészletek definiálását tulajdonságainak teljes listáját lásd: a [adatkészletek](https://docs.microsoft.com/en-us/azure/data-factory/concepts-datasets-linked-services) cikk. Ez a szakasz az Azure SQL Data Warehouse adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Másolja az adatokat, vagy az Azure SQL Data Warehouse, állítsa be a **típus** tulajdonság, az adatkészlet **AzureSqlDWTable**. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A **típus** értékre kell állítani a tulajdonságot az adatkészlet **AzureSqlDWTable**. | Igen |
| tableName | A tábla vagy nézet az Azure SQL Data Warehouse-példányhoz, a társított szolgáltatás hivatkozik a neve. | Igen |

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
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz a forrás Azure SQL Data Warehouse és a fogadó által támogatott tulajdonságok listáját tartalmazza.

### <a name="azure-sql-data-warehouse-as-the-source"></a>Az Azure SQL Data Warehouse forrása

Adatok másolása az Azure SQL Data Warehouse, állítsa be a **típus** tulajdonság, a másolási tevékenység forrás **SqlDWSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A **típus** értékre kell állítani a másolási tevékenység forrás tulajdonság **SqlDWSource**. | Igen |
| sqlReaderQuery | Az egyéni SQL-lekérdezés segítségével olvassa el az adatokat. Példa: `select * from MyTable`. | Nem |
| sqlReaderStoredProcedureName | A tárolt eljárást, amely adatokat olvas be a forrás-tábla neve. Az utolsó SQL-utasítást a tárolt eljárás a SELECT utasítással kell lennie. | Nem |
| storedProcedureParameters | A tárolt eljárás paraméterei.<br/>Megengedett értékek: neve vagy értéke párokat. Nevek és a kis-és a paraméterek meg kell egyeznie a neveket és a kis-és nagybetűhasználatot, a tárolt eljárás paraméterértékeinek. | Nem |

### <a name="points-to-note"></a>Tudnivalók

- Ha a **sqlReaderQuery** van megadva a **SqlSource**, a másolási tevékenység a lekérdezés fut az Azure SQL Data Warehouse forrás, az adatok beolvasásához. Vagy megadhat egy tárolt eljárást. Adja meg a **sqlReaderStoredProcedureName** és **storedProcedureParameters** Ha paramétereket fogadja tárolt eljárást.
- Ha nem adja meg vagy **sqlReaderQuery** vagy **sqlReaderStoredProcedureName**, az oszlopok definiálva a **struktúra** az adatkészlet JSON szakaszában is használható a lekérdezés hozhatnak létre. `select column1, column2 from mytable` Azure SQL Data Warehouse fut. Ha az adatkészlet definíciója nem rendelkezik a **struktúra**, az összes oszlop ki van jelölve, a táblából.
- Ha használ **sqlReaderStoredProcedureName**, továbbra is meg kell adnia egy helyőrző **tableName** tulajdonságot az adatkészlet JSON.

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

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A **típus** értékre kell állítani a másolási tevékenység fogadó tulajdonságát **SqlDWSink**. | Igen |
| allowPolyBase | Azt jelzi, hogy a PolyBase, ha lehetséges, használja a BULKINSERT mechanizmus helyett. <br/><br/> Azt javasoljuk, hogy Ön adatok betöltése az SQL Data Warehouse-bA a PolyBase használatával. Tekintse meg a [bA a PolyBase használatával az adatok betöltése az Azure SQL Data Warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse) korlátozások és a részletek a következő szakaszban.<br/><br/>Engedélyezett értékek a következők **igaz** és **hamis** (alapértelmezett).  | Nem |
| polyBaseSettings | Egy csoport tulajdonságok is lehet megadni, ha a **allowPolybase** tulajdonsága **igaz**. | Nem |
| rejectValue | Megadja a szám vagy százalékos aránya, amelyek is vissza kell utasítani, mielőtt a lekérdezés nem sikerült sorokat.<br/><br/>További információ a PolyBase visszautasítási lehetőségeit argumentumok szakaszában [CREATE EXTERNAL TABLE (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx). <br/><br/>Megengedett értékek: 0 (alapértelmezett), 1, 2, stb. |Nem |
| rejectType | Megadja, hogy a **rejectValue** akkor Szövegkonstansérték vagy százalékos.<br/><br/>Engedélyezett értékek a következők **érték** (alapértelmezett), és **százalékos**. | Nem |
| rejectSampleValue | Mielőtt PolyBase újraszámítja a visszautasított sorok aránya beolvasandó sorok számát határozza meg.<br/><br/>Megengedett értékek: 1, 2, stb. | Igen, ha a **rejectType** van **százalékos**. |
| useTypeDefault | Itt adhatja meg, hogyan szeretné kezelni a PolyBase kér le adatokat a szövegfájl elválasztójellel tagolt szöveges fájlok a hiányzó értékeket.<br/><br/>További tudnivalók a ezt a tulajdonságot a következő argumentumok szakaszában [CREATE EXTERNAL FILE FORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx).<br/><br/>Engedélyezett értékek a következők **igaz** és **hamis** (alapértelmezett). | Nem |
| WriteBatchSize | Adatok beszúrása SQL-táblát, amikor a puffer mérete eléri a **writeBatchSize**. Érvényes, csak ha a PolyBase nem használja.<br/><br/>Az engedélyezett érték **egész** (sorok száma). | Nem. Az alapértelmezett érték a 10000. |
| writeBatchTimeout | Várjon, amíg a kötegelt insert művelet befejezését, mielőtt azt az időkorlátot. Érvényes, csak ha a PolyBase nem használja.<br/><br/>Az engedélyezett érték **timespan**. Példa: "00: 30:00" (30 perc). | Nem |
| preCopyScript | Adja meg a másolási tevékenység futtatása előtt az adatok Azure SQL Data Warehouse-bA írt minden egyes futtatásához egy SQL-lekérdezést. Ez a tulajdonság használatával az előre betöltött adatokat. | Nem | (#repeatability-során-példány). | A lekérdezési utasítást. | Nem |

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

Használatával [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) betöltés nagy mennyiségű adat Azure SQL Data Warehouse-ba, a nagy átviteli sebességű hatékony módja. Átviteli sebesség nagyobb nyereség láthatja az alapértelmezett BULKINSERT mechanizmus helyett a PolyBase használatával. Lásd: [teljesítményfigyelési](copy-activity-performance.md#performance-reference) részletes összehasonlítását. A használati esetek, olvassa [1 TB adat betöltése az Azure SQL Data Warehouse-bA](https://docs.microsoft.com/en-us/azure/data-factory/v1/data-factory-load-sql-data-warehouse).

* Ha a forrásadatok az Azure Blob storage vagy az Azure Data Lake Store, és kompatibilis a polybase használatával, a másolási közvetlenül az Azure SQL Data Warehouse a PolyBase használatával formátuma. További információkért lásd:  **[közvetlen másolása a PolyBase használatával](#direct-copy-by-using-polybase)**.
* A forrásadattár és formátum a PolyBase által eredetileg nem támogatott, ha a **[szakaszos Másolás a PolyBase használatával](#staged-copy-by-using-polybase)** inkább funkciót. A szakaszos másolás funkciót is, nagyobb átviteli sebességet biztosít. A PolyBase-kompatibilis formátumra alakítja az adatokat automatikusan átalakítja. És az Azure Blob storage-ban tárolja az adatokat. Majd betölti az adatokat az SQL Data Warehouse-bA.

> [!IMPORTANT]
> Vegye figyelembe, hogy a PolyBase jelenleg nem támogatott az MSI-alapú Azure AD alkalmazástoken-hitelesítésének.

### <a name="direct-copy-by-using-polybase"></a>Közvetlen másolás a PolyBase használatával

Az SQL Data Warehouse PolyBase támogatja az Azure Blob- és az Azure Data Lake Store közvetlenül. Egyszerű szolgáltatás használja forrásként, és adott fájl formátuma követelményekkel rendelkezik. Ha a forrásadatok megfelel a jelen szakaszban ismertetett feltételeknek, közvetlen másol a forrásadattárból Azure SQL Data Warehouse a polybase szolgáltatást akkor használja. Ellenkező esetben használjon [szakaszos Másolás a PolyBase használatával](#staged-copy-by-using-polybase).

> [!TIP]
> Adatok másolása hatékonyan Data Lake Store az SQL Data Warehouse, és ismerje meg alaposabban [Azure Data Factory segítségével még egyszerűbben és kényelmes megoldás az adatok alapján tárhat fel, ha a Data Lake Store használatával az SQL Data Warehouse](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/).

A követelmények nem teljesülnek, ha az Azure Data Factory ellenőrzi a beállításokat, és automatikusan visszavált az adatok áthelyezése a BULKINSERT mechanizmusa.

1. A **forrás társított szolgáltatás** típus **AzureStorage** vagy **AzureDataLakeStore** az egyszerű szolgáltatásnév hitelesítésével.
1. A **bemeneti adatkészlet** típus **AzureBlob** vagy **AzureDataLakeStoreFile**. A formátum típusa alapján `type` tulajdonságai **OrcFormat**, **ParquetFormat**, vagy **TextFormat**, az alábbi konfigurációkkal:

   1. `rowDelimiter` meg kell **\n**.
   1. `nullValue` vagy értékre van állítva **üres karakterlánc** ("") vagy alapértelmezett, balra, és `treatEmptyAsNull` nem hamis értékre van állítva.
   1. `encodingName` értéke **utf-8**, amelynek alapértelmezett értéke.
   1. `escapeChar`, `quoteChar` és `skipLineCount` nincsenek megadva. A PolyBase támogatási kihagyása fejlécsort, amely konfigurálható `firstRowAsHeader` az ADF-ben.
   1. `compression` lehet **tömörítés nélküli**, **GZip**, vagy **Deflate**.

    ```json
    "typeProperties": {
       "folderPath": "<blobpath>",
       "format": {
           "type": "TextFormat",
           "columnDelimiter": "<any delimiter>",
           "rowDelimiter": "\n",
           "nullValue": "",
           "encodingName": "utf-8",
           "firstRowAsHeader": <any>
       },
       "compression": {
           "type": "GZip",
           "level": "Optimal"
       }
    },
    ```

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

### <a name="staged-copy-by-using-polybase"></a>A PolyBase használatával szakaszos másolás

Ha a forrásadatok nem felel meg az előző szakaszban, lehetővé teszik az adatok másolása egy átmeneti előkészítési Azure Blob storage példány keresztül. Az Azure Premium Storage nem lehet. Ebben az esetben az Azure Data Factory automatikusan futtatja átalakítások az adatok PolyBase adatok formátuma követelményeinek kielégítése érdekében. Ezután használja a PolyBase adatok betöltése az SQL Data Warehouse-bA. Végül, megtisztítja az ideiglenes adatokat a blob storage-ból. Lásd: [szakaszos Másolás](copy-activity-performance.md#staged-copy) részletes másolhat adatokat egy előkészítési Azure Blob storage-példány használatával.

Ez a funkció használatához hozzon létre egy [Azure Storage társított szolgáltatás](connector-azure-blob-storage.md#linked-service-properties) hivatkozik, amely Azure storage-fiókban az átmeneti blob-tárolóval. Adja meg a `enableStaging` és `stagingSettings` tulajdonságok a másolási tevékenység az alábbi kódban látható módon:

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

A PolyBase-betöltések pedig csak 1 MB-nál kisebb méretű sorokat. Ezek nem tölthető be, VARCHR(MAX), NVARCHAR(MAX) vagy VARBINARY(MAX). További információkért lásd: [SQL Data Warehouse szolgáltatás kapacitáskorlátait](../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Ha a forrásadatok 1 MB-nál nagyobb sorral rendelkezik, érdemes a forrástáblákhoz függőlegesen felosztása több kis azokat. Győződjön meg arról, hogy az egyes sorok legnagyobb mérete nem haladja meg a határértéket. A kisebb méretű táblák ezután betöltése a PolyBase használatával, és az Azure SQL Data Warehouse egyesíti.

### <a name="sql-data-warehouse-resource-class"></a>Az SQL Data Warehouse erőforrásosztály

A felhasználót, hogy adatokat tölt be az SQL Data Warehouse polybase nagyobb erőforrásosztályt hozzárendelni a legjobb teljesítmény elérése érdekében.

### <a name="tablename-in-azure-sql-data-warehouse"></a>**Táblanév** az Azure SQL Data Warehouse

Az alábbi táblázat példákat mutat be, hogyan adhat meg a **tableName** JSON adatkészlet tulajdonság. Séma és a táblázat neve különböző kombinációit jeleníti meg.

| Adatbázis-séma | Tábla neve | **Táblanév** JSON-tulajdonság |
| --- | --- | --- |
| dbo | Táblanév | Táblanév vagy a dbo. Táblanév vagy [dbo]. [Táblanév] |
| dbo1 | Táblanév | dbo1. Táblanév vagy [dbo1]. [Táblanév] |
| dbo | My.Table | [My.Table] vagy [dbo]. [My.Table] |
| dbo1 | My.Table | [dbo1].[My.Table] |

A következő hibát látja, ha a probléma lehet, hogy a megadott érték a **tableName** tulajdonság. Lásd az előző táblázatban adja meg az értékeket a megfelelő módszer az **tableName** JSON-tulajdonságot.

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

### <a name="columns-with-default-values"></a>Az alapértelmezett értékekkel oszlopok

A PolyBase szolgáltatás, a Data Factory jelenleg csak az azonos számú oszlopot, mint a céltábla fogad el. Ilyen például, ahol az egyiket van definiálva az alapértelmezett érték négy oszlopot tartalmazó táblát. A bemeneti adatok továbbra is rendelkeznie kell négy oszlopot. Három oszlop a bemeneti adatkészlet az alábbihoz hasonló hibaüzenetet eredményez:

```
All columns of the table must be specified in the INSERT BULK statement.
```

A NULL érték az alapértelmezett érték formája. Ha az oszlop nullázható, a bemeneti adatokat az a blob az adott oszlop lehet üres. De hiányzik a bemeneti adatkészlet a nem lehet. PolyBase az Azure SQL Data Warehouse a hiányzó értékeket szúr be NULL.

## <a name="data-type-mapping-for-azure-sql-data-warehouse"></a>Az Azure SQL Data Warehouse adattípus-leképezés

Másolt adatok vagy az Azure SQL Data Warehouse, a következő hozzárendeléseket használják az Azure SQL Data Warehouse adattípusok Azure Data Factory-közbenső adattípusok. Lásd: [séma és adatok írja be a hozzárendelések](copy-activity-schema-and-type-mapping.md) megtudhatja, hogyan másolási tevékenység leképezi a forrás séma és adatok típusa a fogadó.

| Az Azure SQL Data Warehouse-adattípus | Data Factory közbenső adattípus |
|:--- |:--- |
| bigint | Int64 |
| Bináris | Byte] |
| bit | Logikai |
| CHAR | Karakterlánc, Char] |
| dátum | DateTime |
| Dátum és idő | DateTime |
| datetime2 | DateTime |
| Datetimeoffset | DateTimeOffset |
| tizedes tört | tizedes tört |
| A FILESTREAM attribútum (varbinary(max)) | Byte] |
| Lebegőpontos | Dupla |
| image | Byte] |
| int | Int32 |
| költséget takaríthat meg | tizedes tört |
| nchar | Karakterlánc, Char] |
| ntext | Karakterlánc, Char] |
| numerikus | tizedes tört |
| nvarchar | Karakterlánc, Char] |
| valódi | Önálló |
| ROWVERSION | Byte] |
| smalldatetime | DateTime |
| smallint | Int16 |
| pénz | tizedes tört |
| sql_variant | Objektum * |
| szöveg | Karakterlánc, Char] |
| time | Időtartam |
| időbélyeg | Byte] |
| tinyint | Bájt |
| UniqueIdentifier | GUID |
| varbinary | Byte] |
| varchar | Karakterlánc, Char] |
| xml | Xml |

## <a name="next-steps"></a>További lépések
Az Azure Data Factory másolási tevékenység által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak és formátumok](copy-activity-overview.md##supported-data-stores-and-formats).
