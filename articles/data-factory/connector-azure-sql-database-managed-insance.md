---
title: Adatok másolása és az Azure SQL Database felügyelt példány az Azure Data Factory használatával |} A Microsoft Docs
description: 'Útmutató: adatok áthelyezése és az Azure SQL Database felügyelt példány az Azure Data Factory használatával.'
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: jingwang
ms.openlocfilehash: 9208ceeb760bba97c12b23a1b6e5bdff7efc9020
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67274821"
---
# <a name="copy-data-to-and-from-azure-sql-database-managed-instance-by-using-azure-data-factory"></a>Adatok másolása és az Azure SQL Database felügyelt példány az Azure Data Factory használatával

Ez a cikk ismerteti, hogyan használja a másolási tevékenység az Azure Data Factoryban az adatok másolásához, és az Azure SQL Database felügyelt példányába. Épül a [másolási tevékenység áttekintéséből](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

## <a name="supported-capabilities"></a>Támogatott képességek

Másolhat adatokat az Azure SQL Database felügyelt példányain bármely támogatott fogadó adattárba. Is másolhatja adatokat bármely támogatott forrás adattárolóból a felügyelt példányhoz. A másolási tevékenység által forrásként és fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Pontosabban az Azure SQL Database felügyelt példányába összekötő támogatja:

- Adatok másolása az SQL-hitelesítés használatával.
- Forrásként adatok beolvasása egy SQL-lekérdezést vagy tárolt eljárás használatával.
- Fogadóként adatok hozzáfűzése táblát vagy egy tárolt eljárást az egyéni logikát meghívása másolása során.

>[!NOTE]
>Az Azure SQL Database felügyelt példányain **[Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-mi-current)** jelenleg nem támogatja az összekötő által. Megkerülő megoldásként használhatja [általános ODBC-összekötő](connector-odbc.md) és az SQL Server ODBC-illesztőprogram helyi Integration Runtime-n keresztül. Hajtsa végre a [Ez az útmutató](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-mi-current) ODBC illesztőprogram letöltési és kapcsolati karakterlánc konfigurációkkal.

>[!NOTE]
>Szolgáltatás egyszerű és felügyelt identitások hitelesítése jelenleg nem támogatottak az összekötő által és a csomag hamarosan utáni engedélyezéséhez. Most a következő módokon hidalható át kiválaszthatja az Azure SQL Database-összekötő és a manuális adja meg a kiszolgáló a felügyelt példány.

## <a name="prerequisites"></a>Előfeltételek

Azure SQL Database felügyelt példányába eléréséhez  **[nyilvános végpontot](../sql-database/sql-database-managed-instance-public-endpoint-securely.md)** , használhatja az Azure által felügyelt ADF-integrációs Győződjön meg arról, nem csak engedélyezze a nyilvános végponthoz, de is engedélyezze a nyilvános végponthoz forgalmat a hálózati biztonsági csoportot, hogy képes csatlakozni az adatbázishoz, a következő ADF [Ez az útmutató](../sql-database/sql-database-managed-instance-public-endpoint-configure.md).

Az Azure SQL Database felügyelt példányain eléréséhez **privát végpontjaikat**, állítsa be egy [saját üzemeltetésű integrációs modul](create-self-hosted-integration-runtime.md) , amely hozzáfér az adatbázis. Ha üzembe helyezi a saját üzemeltetésű integrációs modult a felügyelt példány ugyanazon a virtuális hálózaton található, ellenőrizze, hogy az integrációs modult tartalmazó számítógépen, mint a felügyelt példány egy másik alhálózatot. A saját üzemeltetésű integrációs modult egy másik virtuális hálózatban, mint a felügyelt példány üzembe helyezése, ha egy virtuális hálózatok közötti társviszony vagy egy virtuális hálózat virtuális hálózati kapcsolat is használhatja. További információkért lásd: [alkalmazását az Azure SQL Database felügyelt példányába való csatlakozás](../sql-database/sql-database-managed-instance-connect-app.md).

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok segítségével határozhatók meg a Data Factory-entitások adott és az Azure SQL Database felügyelt példányába összekötő-tulajdonságokkal kapcsolatos részletekért.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Az Azure SQL Database felügyelt példányába társított szolgáltatás a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot állítsa **SqlServer**. | Igen. |
| connectionString |Ez a tulajdonság határozza meg a connectionString információkat, amelyeket a példányhoz való csatlakozáshoz a felügyelt SQL-hitelesítés használatával. További információkért lásd az alábbi példákat. <br/>Ez a mező jelölhetnek egy SecureString tárolja biztonságos helyen a Data Factoryban. Jelszó is helyezheti az Azure Key Vaultban, és ha az SQL-hitelesítés lekéréses a `password` konfigurációs ki a kapcsolati karakterláncot. A táblázat alatti a JSON-példa és [Store hitelesítő adatokat az Azure Key Vaultban](store-credentials-in-key-vault.md) további részleteket a cikkben. |Igen. |
| connectVia | Ez [integrációs modul](concepts-integration-runtime.md) az adattárban való kapcsolódásra szolgál. (Ha a felügyelt példány nyilvános végponttal rendelkezik, és lehetővé teszi az ADF eléréséhez) használhatja a helyi Integration Runtime vagy az Azure integrációs modul. Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. |Igen. |

**1. példa: SQL-hitelesítés használata** alapértelmezett port a 1433-as. SQL felügyelt példánya a nyilvános végpontot használja, ha explicit módon adja meg a portot 3342.

```json
{
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "SqlServer",
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

**2. példa: SQL-hitelesítés használata az Azure Key Vaultban jelszóval** alapértelmezett port a 1433-as. SQL felügyelt példánya a nyilvános végpontot használja, ha explicit módon adja meg a portot 3342.

```json
{
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "SqlServer",
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

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Tekintse meg az adatkészletek cikk szakaszok és adatkészletek definiálásához rendelkezésre álló tulajdonságok teljes listáját. Ez a szakasz az Azure SQL Database felügyelt példányába adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Adatok másolásához és az Azure SQL Database felügyelt példánya a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adatkészlet értékre kell állítani **SqlServerTable**. | Igen. |
| tableName |Ez a tulajdonság az a név a tábla vagy nézet, amelyre a társított szolgáltatás hivatkozik az adatbázis példányában. | A forrás nem. Igen, a fogadó. |

**Példa**

```json
{
    "name": "AzureSqlMIDataset",
    "properties":
    {
        "type": "SqlServerTable",
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

Szakaszok és a tevékenységek meghatározásához használható tulajdonságok teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz az Azure SQL Database felügyelt példányába forrásaként és fogadó által támogatott tulajdonságok listáját tartalmazza.

### <a name="azure-sql-database-managed-instance-as-a-source"></a>Az Azure SQL Database felügyelt példányain forrásként

Adatok másolása az Azure SQL Database felügyelt példányába, állítsa be a forrás típusaként a másolási tevékenység **SqlSource**. A másolási tevékenység forrás szakaszban a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrása típusa tulajdonságát állítsa **SqlSource**. | Igen. |
| sqlReaderQuery |Ez a tulajdonság az egyéni SQL-lekérdezést használ adatokat olvasni. Például: `select * from MyTable`. |Nem. |
| sqlReaderStoredProcedureName |Ez a tulajdonság, amely adatokat olvas a forrástábla tárolt eljárás neve. Az utolsó SQL-utasítást a tárolt eljárás a SELECT utasítással kell lennie. |Nem. |
| storedProcedureParameters |Ezeket a paramétereket a tárolt eljárás vonatkoznak.<br/>Megengedett értékek: neve vagy értéke párokat. A neveket és a kis-és a paraméterek meg kell egyeznie a neveket és a kis-és nagybetűhasználatot, a tárolt eljárás paraméterértékeinek. |Nem. |

Vegye figyelembe a következő szempontokat:

- Ha **sqlReaderQuery** megadott **SqlSource**, a másolási tevékenység fut ez a lekérdezés a felügyelt példány forrás, az adatok beolvasásához. Emellett megadhatja a tárolt eljárás megadásával **sqlReaderStoredProcedureName** és **storedProcedureParameters** Ha paramétereket fogadja tárolt eljárást.
- Ha nem adja meg vagy a **sqlReaderQuery** vagy **sqlReaderStoredProcedureName** tulajdonság, az adatkészlet JSON-lekérdezés összeállításához használt "struktúra" szakaszában meghatározott oszlopokat. A lekérdezés `select column1, column2 from mytable` fut a következő felügyelt példányt. Ha az adatkészlet-definícióban nem tartalmaz "szerkezet", az összes oszlop ki van jelölve, a táblából.

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

### <a name="azure-sql-database-managed-instance-as-a-sink"></a>Az Azure SQL Database felügyelt példányain fogadóként

> [!TIP]
> További információ a támogatott írási viselkedéseket, konfigurációk és az ajánlott eljárás a [ajánlott eljárás az adatok betöltését az Azure SQL Database felügyelt példányain](#best-practice-for-loading-data-into-azure-sql-database-managed-instance).

Adatok másolása az Azure SQL Database felügyelt példányába, állítsa a fogadó típusa a másolási tevékenység **SqlSink**. A másolási tevékenység fogadó szakaszban a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység fogadó típusa tulajdonságát állítsa **SqlSink**. | Igen. |
| writeBatchSize |Az SQL-táblába beilleszti sorok száma **kötegenként**.<br/>Megengedett értékek: sorok számának egész számok. Alapértelmezés szerint a Data Factory a megfelelő kötegméret sor mérete alapján dinamikus meghatározásához.  |Nem |
| writeBatchTimeout |Ez a tulajdonság határozza meg a várakozási idő a kötegelt insert művelet befejezését, mielőtt azt az időkorlátot.<br/>Megengedett értékek: az időtartam. Például "00: 30:00," vagyis 30 perc. |Nem. |
| preCopyScript |Ez a tulajdonság határozza meg, hogy a másolási tevékenység végrehajtása előtt az adatok írása a következő felügyelt példányt az SQL-lekérdezést. Indítva csak egyszer futtatni példányonkénti. Ez a tulajdonság használatával előre betöltött adatok törlése. |Nem. |
| sqlWriterStoredProcedureName |Ez a név a tárolt eljárás, amely meghatározza, hogyan alkalmazhatja a forrásadatok a célként megadott táblába. <br/>A tárolt eljárás *kötegenként meghívása*. Egy műveletet, amely csak egyszer fut le, és nem tartalmazó adatforrásból, például törlés vagy csonkolása teheti a `preCopyScript` tulajdonság. |Nem. |
| storedProcedureParameters |Ezeket a paramétereket használják a tárolt eljárást.<br/>Megengedett értékek: neve vagy értéke párokat. A neveket és a kis-és a paraméterek meg kell egyeznie a neveket és a kis-és nagybetűhasználatot, a tárolt eljárás paraméterértékeinek. |Nem. |
| sqlWriterTableType |Ez a tulajdonság adja meg egy tábla írja be a tárolt eljárás használható. A másolási tevékenység egy ideiglenes táblát a tábla típusú elérhetővé teszi az adatok áthelyezését. Tárolt eljárás kód majd egyesítheti az éppen másolt adatokat a adatokkal. |Nem. |

**1. példa: adatok hozzáfűzése**

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
                "type": "SqlSink",
                "writeBatchSize": 100000
            }
        }
    }
]
```

**2. példa: egy tárolt eljárás meghívása során másolása**

További részletek a [a egy SQL-fogadó tárolt eljárás meghívása](#invoking-stored-procedure-for-sql-sink).

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

## <a name="best-practice-for-loading-data-into-azure-sql-database-managed-instance"></a>Ajánlott eljárás az adatok betöltését az Azure SQL Database felügyelt példánya

Adatok másolása az Azure SQL Database felügyelt példánya esetén szükség lehet különböző írási viselkedés:

- **[Hozzáfűzés](#append-data)** : csak a rendelkező új rekordok; forrás adatok
- **[Upsert](#upsert-data)** : Beszúrások és frissítések; egyaránt rendelkező forrás adatok
- **[Írja felül](#overwrite-entire-table)** : Töltse be újra az egész táblát, minden alkalommal, amikor; szeretnék
- **[Az egyéni logikát írási](#write-data-with-custom-logic)** : További feldolgozás előtt a végső beszúrási a céltáblázatba történő van szükségem.

Tekintse meg a rendre szakaszok konfigurálásával az ADF és az ajánlott eljárásokat.

### <a name="append-data"></a>Adatok hozzáfűzése

Ez az Azure SQL Database felügyelt példányába fogadó összekötő alapértelmezett viselkedését, és tegye az ADF **tömeges Beszúrás** hatékonyan írni a táblában. Egyszerűen konfigurálható a forrás és fogadó ennek megfelelően a másolási tevékenység.

### <a name="upsert-data"></a>Adatok beszúrása és frissítése (upsert)

**E beállítás** (javasolt, ha különösen a nagy mennyiségű adat másolása rendelkezik): a **a legtöbb nagy teljesítményű megközelítés** upsert ehhez a rendszer a következő: 

- Először is kihasználhatja a [ideiglenes tábla](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables) a tömeges betöltés összes rekordot a másolási tevékenység használatával. Ideiglenes táblák végzett műveletek nem kerülnek naplózásra, mivel rekordok millióit, betöltheti másodpercek alatt.
- Egy tárolt eljárási tevékenység végrehajtása a alkalmazni az ADF- [EGYESÍTÉSE](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) (vagy a beszúrási/frissítés) utasítás, és használja a temp táblát az összes forrás frissít vagy szúr be egy tranzakcióként életű könyvtárgyorsítótárból mértékének csökkentésével, és olyan műveletek naplózására. A tárolt eljárási tevékenység végén ideiglenes táblára, készen áll a következő upsert ciklusig levágja. 

Tegyük fel, az Azure Data Factory, létrehozhat rendelkező folyamatot egy **másolási tevékenység** a kapcsolt egy **Stored Procedure-tevékenység** sikeres művelet. A korábbi adatokat másol a forrásadattárba a ideiglenes táblába, például: " **##UpsertTempTable**" adatkészletben levő table neveként majd az utóbbi hív meg egy tárolt eljárást az ideiglenes táblából a forrásadatok egyesítse a céltábla és karbantartása ideiglenes táblára.

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

Az adatbázis határoz meg egy tárolt eljárás egyesítési logikával, például a következő, az a fenti tárolt eljárási tevékenység mutatott. Feltéve, hogy a cél **Marketing** három oszlopokat tartalmazó táblába: **ProfileID**, **állapot**, és **kategória**, és végezze el az upsert alapján a **ProfileID** oszlop.

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

**II. lehetőség:** lehetőségként választhatja [másolási tevékenység belül tárolt eljárás meghívása](#invoking-stored-procedure-for-sql-sink), miközben Megjegyzés: Ez a megközelítés a forrástábla helyett tömeges támaszkodva minden egyes sorára hajtja végre az alapértelmezett módszer is beilleszthet a másolási tevékenység így azt nem fér el a nagy méretű upsert.

### <a name="overwrite-entire-table"></a>Írja felül a teljes tábla

Konfigurálható **preCopyScript** tulajdonság a másolási tevékenység fogadó ebben az esetben minden másolási tevékenység futtatása, ADF végrehajtja a parancsfájl először futtassa a másolat illessze be az adatokat. Például a teljes tábla felülírja a legújabb adatokkal, is megadhat egy parancsfájlt, amely először törölje az összes rekordot a forrás az új adatok tömeges-betöltés előtt.

### <a name="write-data-with-custom-logic"></a>Egyéni logikát az adatok írása

Hasonló leírtak szerint [Upsert adatok](#upsert-data) szakaszban további feldolgozás előtt a végső beszúrási a forrásadatok a céltáblázatba történő, alkalmaznia kell is egy) a nagyméretű méretezési egy ideiglenes táblát betöltési, akkor egy tárolt meghívása az eljárás, vagy b) tárolt eljárás meghívása másolása során.

## <a name="invoking-stored-procedure-for-sql-sink"></a> A egy SQL-fogadó tárolt eljárás meghívása

Adatok másolása az Azure SQL Database felügyelt példánya esetén is konfigurálni és meghívni egy felhasználó által megadott tárolt eljárást a további paraméterek.

> [!TIP]
> Tárolt eljárás meghívása dolgozza fel az adatok sor soronként tömeges művelet, amely nem ajánlott nagy méretű példány helyett. További információkat talál a [ajánlott eljárás az adatok betöltését az Azure SQL Database felügyelt példányain](#best-practice-for-loading-data-into-azure-sql-database-managed-instance).

Beépített másolási mechanizmust nem, a célt szolgálják, amikor egy tárolt eljárást használhatja például a további feldolgozás előtt a végső beszúrási a forrásadatok a céltáblázatba történő alkalmazásához. Extra feldolgozási példákat egyesítési oszlop, keresse meg a további értéket, és annak több tábla.

A következő minta bemutatja, hogyan egy tárolt eljárást az upsert ehhez az SQL Server-adatbázisban egy táblába. Tegyük fel, amelyek bemeneti és a fogadó **Marketing** tábla minden egyes háromoszloposak: **ProfileID**, **állapot**, és **kategória**. Hajtsa végre az upsert alapján a **ProfileID** oszlopot, és csak egy adott kategória alkalmazhatja azt.

**Kimeneti adatkészlet:** a "tableName" kell lennie a ugyanazon tábla típusú paraméter neve a tárolt eljárás (lásd az alábbi tárolt eljárás szkriptet).

```json
{
    "name": "AzureSqlMIDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<Managed Instance linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "Marketing"
        }
    }
}
```

Adja meg a **SQL-fogadó** a másolási tevékenység a következő szakaszban.

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
      VALUES (source.ProfileID, source.State, source.Category);
END
```

Az adatbázis sqlWriterTableType definiálhatja a táblatípus ugyanazzal a névvel. A táblatípus sémája megegyezik a sémanévvel, a bemeneti adatokat adott vissza.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL，
    [Category] [varchar](256) NOT NULL
)
```

A tárolt eljárás szolgáltatás kihasználja [tábla értékű paraméterek](https://msdn.microsoft.com/library/bb675163.aspx).

## <a name="data-type-mapping-for-azure-sql-database-managed-instance"></a>Az Azure SQL Database felügyelt példányába adattípus-leképezés

Ha és az Azure SQL Database felügyelt példánya a rendszer az adatokat másolja, a következő hozzárendeléseket a rendszer az Azure SQL Database felügyelt példányába adattípusok Azure Data Factory közbenső adattípusok használja. Című cikk nyújt tájékoztatást, hogy a másolási tevékenység leképezi a forrás séma és adatok típusa a fogadó, [séma és adatok írja be a hozzárendelések](copy-activity-schema-and-type-mapping.md).

| Az Azure SQL Database felügyelt példányain adattípus | Az Azure Data Factory köztes adatok típusa |
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
> Az adattípusok alapján, amelyek ideiglenes tizedes típusra jelenleg Azure Data Factory támogatja a legfeljebb 28 pontosság. Ha 28-nál nagyobb pontosságú igénylő adatait, fontolja meg egy karakterláncot egy SQL-lekérdezést az átalakítás.

## <a name="next-steps"></a>További lépések
A másolási tevékenység az Azure Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md##supported-data-stores-and-formats).
