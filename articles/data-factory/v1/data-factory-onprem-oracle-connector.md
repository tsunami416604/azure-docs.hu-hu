---
title: Adatok másolása, vagy az Oracle Data Factory használatával |} A Microsoft Docs
description: Ismerje meg, hogyan másolhat adatokat, vagy egy helyszíni Oracle-adatbázisból az Azure Data Factory használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 3c20aa95-a8a1-4aae-9180-a6a16d64a109
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 1ccf66da14bbbd4993f29da2e40d996cb564864e
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54024909"
---
# <a name="copy-data-to-or-from-oracle-on-premises-by-using-azure-data-factory"></a>Adatok másolása, vagy a helyszíni Oracle az Azure Data Factory használatával

> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-onprem-oracle-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-oracle.md)

> [!NOTE]
> Ez a cikk az Azure Data Factory 1-es verziójára vonatkozik. Ha a jelenlegi verzió az Azure Data Factory szolgáltatás használ, tekintse meg [Oracle-összekötő a v2-ben](../connector-oracle.md).


Ez a cikk bemutatja, hogyan lehet Azure Data Factory másolási tevékenység használatával helyezze át az adatokat, vagy egy helyszíni Oracle-adatbázisból. A cikk számos tekintetben [adattovábbítási tevékenységek](data-factory-data-movement-activities.md), amely adatáthelyezés általános áttekintést mutat be a másolási tevékenység használatával.

## <a name="supported-scenarios"></a>Támogatott esetek

Adatokat másolja *Oracle-adatbázisból* tárolja, a következő adatokat:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

A következő adattárakból származó adatokat másolja *Oracle-adatbázishoz*:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="prerequisites"></a>Előfeltételek

A Data Factory támogatja az adatkezelési átjárót használó helyszíni Oracle-adatforrások csatlakozik. Lásd: [adatkezelési átjáró](data-factory-data-management-gateway.md) tudhat meg többet az adatkezelési átjáró. Adatok áthelyezése az átjáró az adatok beállításával kapcsolatos részletes utasításokért lásd: [adatok áthelyezése a helyszínről a felhőbe](data-factory-move-data-between-onprem-and-cloud.md).

Az átjáróra szükség, akkor is, ha az Oracle Azure-infrastruktúrák szolgáltatásként (IaaS) virtuális gép üzemel. Telepítheti az átjáró adattárként azonos IaaS virtuális Gépen vagy egy másik virtuális gép mindaddig, amíg az átjáró képes kapcsolódni az adatbázishoz.

> [!NOTE]
> Tippek a kapcsolat és az átjáróval kapcsolatos hibák elhárítása: [gateway hibáinak elhárítása](data-factory-data-management-gateway.md#troubleshooting-gateway-issues).

## <a name="supported-versions-and-installation"></a>Támogatott verziók és telepítés

Az Oracle-összekötő illesztőprogramok két verziója támogatja:

- **Oracle (ajánlott) Microsoft-illesztőprogram**: Az adatkezelési átjáró 2.7-es verzió kezdve a Microsoft Oracle-illesztőprogram automatikusan települ az átjáróval. Nem kell telepíteni vagy frissíteni az illesztőprogram csatlakozni és Oracle-ig. Jobb másolási teljesítmény akkor is jelentkezhet, ha az illesztőprogram használatával. Oracle-adatbázisok ezen verziói támogatottak:
    - Oracle 12c R1 (12.1)
    - Oracle 11g R1 vagy R2 (11.1, 11.2)
    - Oracle 10g R1 vagy R2 (10.1, 10,2)
    - Oracle 9i R1 vagy R2 (9.0.1, 9.2)
    - Oracle-8i R3 (8.1.7-es)

    > [!NOTE]
    > Oracle-proxy kiszolgáló nem támogatott.

    > [!IMPORTANT]
    > Jelenleg a Microsoft Oracle-illesztőprogram támogatja az Oracle csak adatmásolásra. Az illesztőprogram nem támogatja az írást és Oracle-ig. A teszt kapcsolat funkció az adatkezelési átjárót a **diagnosztikai** lap nem támogatja az illesztőprogramot. Is használhatja a másolás varázsló a kapcsolat ellenőrzéséhez.
    >

- **Oracle-adatszolgáltató a .NET-hez**: Oracle-adatszolgáltatójának használatával másolja az adatokat, vagy Oracle. Ez az összetevő megtalálható [Oracle Data Access összetevői a Windows](http://www.oracle.com/technetwork/topics/dotnet/downloads/). Telepítse a megfelelő verzió (32 bites vagy 64 bites) a gépen, amelyen az átjáró telepítve van. [Oracle-adatszolgáltató a .NET 12.1](http://docs.oracle.com/database/121/ODPNT/InstallSystemRequirements.htm#ODPNT149) Oracle Database 10 g Release 2 és újabb verziói is hozzáférhetnek.

    Ha **XCopy telepítési**, végezze el a readme.htm fájlban leírt lépéseket. Javasoljuk, hogy válassza a telepítő, amely a felhasználói felület (nem az XCopy telepítő).

    A szolgáltató telepítése után indítsa újra a adatkezelési átjárót a gépen a szolgáltatások kisalkalmazásával vagy a Data Management Gateway Configuration Manager használatával.  

A másolás varázsló használatával hozhat létre a másolási folyamat, ha az illesztőprogram típus autodetermined. A Microsoft-illesztőprogram alapértelmezés szerint használja, ha az átjáró verziószáma 2.7-es verziónál korábbi vagy Oracle pedig a fogadó választja.

## <a name="get-started"></a>Bevezetés

Egy másolási tevékenységgel rendelkező folyamatot hozhat létre. A folyamat helyez át adatokat, vagy egy helyszíni Oracle-adatbázisból a különböző eszközök vagy API-k használatával.

A folyamat létrehozásának legegyszerűbb módja, hogy a Másolás varázslóval. Lásd: [oktatóanyag: Hozzon létre egy folyamatot a másolás varázsló használatával](data-factory-copy-data-wizard-tutorial.md) gyors bemutató folyamat létrehozása adatok másolása varázsló használatával.

Használhatja a következő eszközök egyikét egy folyamat létrehozásához: a **az Azure portal**, **Visual Studio**, **Azure PowerShell-lel**, egy **Azure Resource Manager sablon**, a **.NET API**, vagy a **REST API-val**. Tekintse meg a [másolási tevékenység oktatóanyagát](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) kapcsolatos lépésenkénti útmutatót egy másolási tevékenységgel rendelkező folyamat létrehozása.

Az eszközök vagy az API-kat használja, hogy a következő lépéseket egy folyamatot helyez át adatokat egy forrásadattárból egy fogadó adattárba hozunk létre:

1. Hozzon létre egy **adat-előállító**. Adat-előállító egy vagy több folyamattal is tartalmazhat. 
2. Hozzon létre **társított szolgáltatásokat** mutató hivatkozást a bemeneti és kimeneti adatokat tárolja a data factoryjához. Például ha a másolt adatok Oracle-adatbázisból Azure Blob Storage, hozzon létre két társított szolgáltatást, az Oracle database és az Azure storage-fiók összekapcsolása a data factory. Konkrétan Oracle a társított szolgáltatás tulajdonságait, lásd: [társított szolgáltatások tulajdonságai](#linked-service-properties).
3. Hozzon létre **adatkészletek** , amely a másolási művelet bemeneti és kimeneti adatokat jelöli. A példában az előző lépésben az Oracle-adatbázis, amely tartalmazza a bemeneti adatokat adja meg a táblát egy adatkészletet hoz létre. Megadja a blobtárolót és a mappát, amely tárolja az adatokat másolja az Oracle-adatbázisból egy másik adatkészletet hoz létre. Oracle adott adatkészlet tulajdonságai, lásd: [adatkészlet tulajdonságai](#dataset-properties).
4. Hozzon létre egy **folyamat** , amely rendelkezik egy másolási tevékenység, amely egy adatkészletet bemenetként, és a egy adatkészletet pedig kimenetként. Az előző példában használhatja **OracleSource** forrásként és **BlobSink** a másolási tevékenység fogadóként. Hasonlóképpen, ha az Oracle-adatbázis másolása az Azure Blob storage-ból, használhatja a **BlobSource** és **Oraclesinkben** a másolási tevékenység. A másolási tevékenység tulajdonságok, amelyek Oracle-adatbázishoz meghatározott, lásd: [másolási tevékenység tulajdonságai](#copy-activity-properties). A forrás vagy a fogadó adattár használatával kapcsolatos részletekért válassza ki az előző szakaszban az adattárhoz mutató hivatkozás. 

A varázsló használatakor a Data Factory-entitások JSON-definíciói automatikusan jönnek létre az Ön számára: társított szolgáltatásokat, adatkészleteket és a folyamatot. Amikor az eszközök vagy az API-k (kivéve a .NET API), meghatározhatja a Data Factory-entitások a JSON formátumban.  A minták használatával másolja az adatokat, vagy a helyszíni Oracle-adatbázisból Data Factory-entitások JSON-definíciói rendelkező, lásd: [JSON példák](#json-examples-for-copying-data-to-and-from-oracle-database).

A következő szakaszok a Data Factory-entitások definiálásához használt JSON-tulajdonságokkal kapcsolatos részletekért.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A következő táblázat ismerteti, amelyek adott, az Oracle-beli társított szolgáltatás JSON-elemek:

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| type |A **típus** tulajdonságot állítsa **OnPremisesOracle**. |Igen |
| driverType | Adja meg az illesztőprogramok másolhat adatokat, vagy az Oracle-adatbázishoz. Engedélyezett értékek a következők **Microsoft** és **ODP** (alapértelmezett). Lásd: [verziójától és a telepítés támogatott](#supported-versions-and-installation) illesztőprogram adatai számára. | Nem |
| kapcsolati Sztringje | Adja meg az információkat, amelyeket szeretne csatlakozni az Oracle database-példány a **connectionString** tulajdonság. | Igen |
| átjáró neve | Az átjáró a helyszíni Oracle-kiszolgálóhoz való csatlakozáshoz használt neve. |Igen |

**Példa: A Microsoft-illesztő használatával**

> [!TIP]
> Ha hibaüzenet jelenik meg, amely szerint a "ORA-01025: UPI paraméter engedélyezett tartományon kívül esik"és az Oracle-verzió 8i, adjon hozzá `WireProtocolMode=1` a kapcsolati karakterláncot, és próbálkozzon újra:

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString":"Host=<host>;Port=<port>;Sid=<service ID>;User Id=<user name>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Példa: A ODP illesztőprogram segítségével**

Engedélyezett formátumokat kapcsolatos további információkért lásd: [Oracle-adatszolgáltató a .NET ODP](https://www.connectionstrings.com/oracle-data-provider-for-net-odp-net/).

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "connectionString": "Data Source=(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=<host name>)(PORT=<port number>))(CONNECT_DATA=(SERVICE_NAME=<service ID>)));
User Id=<user name>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Szakaszok és adatkészletek definiálását tulajdonságok teljes listáját lásd: [adatkészletek létrehozása](data-factory-create-datasets.md). 

Egy adatkészlet JSON-fájlt, például a szerkezetet, rendelkezésre állást és házirendet, a szakaszait hasonlóak az összes adatkészlet esetében (például az Oracle, az Azure Blob storage és az Azure Table storage).

A **typeProperties** szakasz eltérő az egyes adatkészlet, és az adattárban lévő adatok helyét ismerteti. A **typeProperties** szakasz az adatkészlet típusa **OracleTable** tulajdonságai a következők:

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| tableName |Az Oracle-adatbázis, amelyre a társított szolgáltatás hivatkozik a tábla neve. |Nem (Ha **oracleReaderQuery** vagy **OracleSource** van megadva) |

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és a tevékenységek definiálását tulajdonságok teljes listáját lásd: [folyamatok létrehozása](data-factory-create-pipelines.md). 

Tulajdonságok, például a nevét, leírását, bemeneti és kimeneti táblák és a házirend az összes típusú tevékenységek érhetők el.

> [!NOTE]
> A másolási tevékenység csak egy bemenettel rendelkezik, és csak egy kimenetet.

Az elérhető tulajdonságok köre az **typeProperties** a tevékenység szakaszban tevékenységek minden típusának számától függ. Másolási tevékenység tulajdonságai forrásaként és fogadó típusától függően eltérőek lehetnek.

### <a name="oraclesource"></a>OracleSource

A másolási tevékenység, ha a forrás a **OracleSource** típusa, a következő tulajdonságok érhetők el a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| oracleReaderQuery |Az egyéni lekérdezés segítségével olvassa el az adatokat. |Egy SQL-lekérdezési karakterláncot. Például "kiválasztása \* a **MyTable**". <br/><br/>Ha nincs megadva, az SQL-utasítás végrehajtása: "kiválasztása \* a **MyTable**" |Nem<br />(Ha **tableName** , **adatkészlet** van megadva) |

### <a name="oraclesink"></a>Oraclesinkben

**Oraclesinkben** támogatja a következő tulajdonságokkal:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| writeBatchTimeout |A várakozási idő a köteg beszúrási művelet befejezését, mielőtt azt az időkorlátot. |**Időtartam**<br/><br/> Példa: 00:30:00 (30 perc) |Nem |
| WriteBatchSize |Szúr be adatokat az SQL-táblába, amikor a puffer mérete eléri a értékét **writeBatchSize**. |Egész szám (sorok száma) |Nem (alapértelmezett: 100) |
| sqlWriterCleanupScript |Adja meg egy lekérdezést a másolási tevékenység végrehajtásához, hogy az adott szeletre vonatkozó adatok törlődnek. |A lekérdezési utasítást. |Nem |
| sliceIdentifierColumnName |Meghatározza a másolási tevékenységhez egy automatikusan létrehozott szelet azonosító töltse ki az oszlop neve.  Az érték **sliceIdentifierColumnName** mikor futtassa újra a adott szeletre adatainak szolgál. |Az oszlop nevét, amely rendelkezik az adattípus oszlop **binary(32)**. |Nem |

## <a name="json-examples-for-copying-data-to-and-from-the-oracle-database"></a>JSON-példák az adatok másolása, és az Oracle-adatbázisból

Az alábbi példák megadják példa JSON-definíciók, amelyek segítségével hozzon létre egy folyamatot a [az Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), vagy [Azure PowerShell-lel](data-factory-copy-activity-tutorial-using-powershell.md). A példák bemutatják, hogyan másolhat adatokat a vagy Oracle-adatbázishoz, és az Azure Blob storage-ból. Azonban adatok átmásolhatók a felsorolt fogadóként valamelyik [támogatott adattárak és formátumok](data-factory-data-movement-activities.md#supported-data-stores-and-formats) másolási tevékenységgel az Azure Data Factory használatával.   

**Példa: Oracle az Azure Blob storage-adatok másolása**

A minta az alábbi Data Factory-entitások rendelkezik:

* A társított szolgáltatás típusa [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties).
* A társított szolgáltatás típusa [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Egy bemeneti [adatkészlet](data-factory-create-datasets.md) típusú [OracleTable](data-factory-onprem-oracle-connector.md#dataset-properties).
* Kimenet [adatkészlet](data-factory-create-datasets.md) típusú [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* A [folyamat](data-factory-create-pipelines.md) egy másolási tevékenységgel, amely használja [OracleSource](data-factory-onprem-oracle-connector.md#copy-activity-properties) forrásként és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) pedig a fogadó.

A minta adatokat másol egy helyszíni Oracle-adatbázis egy táblájából blob óránként. A mintában használt különböző tulajdonságokat kapcsolatos további információkért tekintse meg a mintákat a következő szakaszok.

**Oracle-beli társított szolgáltatás**

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString":"Host=<host>;Port=<port>;Sid=<service ID>;User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Az Azure Blob storage-beli társított szolgáltatás**

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
        }
    }
}
```

**Oracle bemeneti adatkészlet**

A minta azt feltételezi, hogy létrehozott egy tábla **MyTable** az Oracle-ben. Egy nevű oszlopot tartalmaz **timestampcolumn** idősorozat-adatok számára.

Beállítás **külső**: **igaz** a Data Factory szolgáltatás tájékoztatja, hogy az adatkészletet a data factory a külső és, hogy az adatkészlet adat-előállító tevékenység nem eredményezett.

```json
{
    "name": "OracleInput",
    "properties": {
        "type": "OracleTable",
        "linkedServiceName": "OnPremisesOracleLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "offset": "01:00:00",
            "interval": "1",
            "anchorDateTime": "2014-02-27T12:00:00",
            "frequency": "Hour"
        },
        "policy": {     
            "externalData": {        
                "retryInterval": "00:01:00",    
                "retryTimeout": "00:10:00",       
                "maximumRetry": 3       
            }     
        }
    }
}
```

**Azure blobkimeneti adatkészlet**

Adatok írása egy új blob minden órában (**gyakorisága**: **óra**, **időköz**: **1**). A mappa elérési útját és nevét a BLOB dinamikusan a feldolgozás alatt álló szelet kezdő időpontja alapján értékeli ki. A mappa elérési útját használja, az év, hónap, nap és a kezdő időpontja óra részét.

```json
{
    "name": "AzureBlobOutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ],
            "format": {
                "type": "TextFormat",
                "columnDelimiter": "\t",
                "rowDelimiter": "\n"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Egy másolási tevékenységgel rendelkező folyamat**

A folyamat egy másolási tevékenység, amely a bemeneti és kimeneti adatkészleteket használatára konfigurált és óránkénti tartalmazza. A folyamat JSON-definíciót a **forrás** típusa **OracleSource** és a **fogadó** típusa **BlobSink**.  Az SQL-lekérdezést, amely szerint kell megadni a **oracleReaderQuery** tulajdonság kiválasztja az adatokat másolni az elmúlt órában.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for a copy activity",
        "activities":[  
            {
                "name": "OracletoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": " OracleInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "OracleSource",
                        "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "OldestFirst",
                    "retry": 0,
                    "timeout": "01:00:00"
                }
            }
        ]
    }
}
```

**Példa: Adatok másolása az Azure Blob storage-ból és Oracle-ig**

Ez a minta bemutatja, hogyan másolhat adatokat egy Azure Blob storage-fiókból egy helyszíni Oracle-adatbázishoz. Azonban adatokat másolja *közvetlenül* bármelyik a felsorolt forrásokat [támogatott adattárak és formátumok](data-factory-data-movement-activities.md#supported-data-stores-and-formats) másolási tevékenységgel az Azure Data Factory használatával.  

A minta az alábbi Data Factory-entitások rendelkezik:

* A társított szolgáltatás típusa [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties).
* A társított szolgáltatás típusa [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Egy bemeneti [adatkészlet](data-factory-create-datasets.md) típusú [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Kimenet [adatkészlet](data-factory-create-datasets.md) típusú [OracleTable](data-factory-onprem-oracle-connector.md#dataset-properties).
* A [folyamat](data-factory-create-pipelines.md) , amely rendelkezik egy másolási tevékenység által használt [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) forrásként [Oraclesinkben](data-factory-onprem-oracle-connector.md#copy-activity-properties) pedig a fogadó.

A minta adatokat másol egy blobot egy helyszíni Oracle database egyik táblájába óránként. A mintában használt különböző tulajdonságokat kapcsolatos további információkért tekintse meg a mintákat a következő szakaszok.

**Oracle-beli társított szolgáltatás**

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "connectionString": "Data Source=(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=<host name>)(PORT=<port number>))(CONNECT_DATA=(SERVICE_NAME=<service ID>)));
            User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Az Azure Blob storage-beli társított szolgáltatás**

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
        }
    }
}
```

**Azure blobbemeneti adatkészlet**

Adatok felülettől új blob minden órában (**gyakorisága**: **óra**, **időköz**: **1**). A mappa elérési útját és nevét a BLOB dinamikusan a feldolgozás alatt álló szelet kezdő időpontja alapján értékeli ki. A mappa elérési útját használja, az év, hónap és nap részét a kezdési időpontot. A fájl nevét a kezdő időpontja óra részét használja. A beállítás **külső**: **igaz** a Data Factory szolgáltatás tájékoztatja, hogy ez a táblázat a data factory a külső, és nem hozzák az adat-előállító adott tevékenységéhez.

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                }
            ],
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            }
        },
        "external": true,
        "availability": {
            "frequency": "Day",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

**Oracle kimeneti adatkészlete**

A minta azt feltételezi, hogy létrehozott egy tábla **MyTable** az Oracle-ben. Az Oracle-t az azonos számú oszlopot, várt a blob CSV-fájl tartalmazza a tábla létrehozásához. Új sorok hozzáadódnak a tábla minden órában.

```json
{
    "name": "OracleOutput",
    "properties": {
        "type": "OracleTable",
        "linkedServiceName": "OnPremisesOracleLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "availability": {
            "frequency": "Day",
            "interval": "1"
        }
    }
}
```

**Egy másolási tevékenységgel rendelkező folyamat**

A folyamat egy másolási tevékenység, amely a bemeneti és kimeneti adatkészleteket használja, és minden órában futtatott, ütemezett tartalmazza. A folyamat JSON-definíciót a **forrás** típusa **BlobSource** és a **fogadó** típusa **Oraclesinkben**.  

```json
{  
    "name":"SamplePipeline",
    "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-05T19:00:00",
        "description":"pipeline with a copy activity",
        "activities":[  
            {
                "name": "AzureBlobtoOracle",
                "description": "Copy Activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "OracleOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "OracleSink"
                    }
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "OldestFirst",
                    "retry": 0,
                    "timeout": "01:00:00"
                }
            }
        ]
    }
}
```


## <a name="troubleshooting-tips"></a>Hibaelhárítási tippek

### <a name="problem-1-net-framework-data-provider"></a>1. hiba: .NET-keretrendszer adatszolgáltatója

**Hibaüzenet**

    Copy activity met invalid parameters: 'UnknownParameterName', Detailed message: Unable to find the requested .Net Framework Data Provider. It may not be installed.  

**Lehetséges okok**

* Oracle .NET Framework Data Provider nem lett telepítve.
* A .NET keretrendszer adatszolgáltatója az Oracle lett telepítve a .NET-keretrendszer 2.0, és nem található a .NET keretrendszer 4.0-s mappákban.

**Felbontás**

* Ha még nem telepítette, Oracle, a .NET-szolgáltatója [telepítheti](http://www.oracle.com/technetwork/topics/dotnet/downloads/), és ismételje meg a forgatókönyvet.
* Ha a szolgáltató telepítése után is a hibaüzenetet látja, hajtsa végre az alábbi lépéseket:
   1. Nyissa meg a gép konfigurációs fájlját a mappából a .NET 2.0-s < rendszerlemez\>: \Windows\Microsoft.NET\Framework64\v2.0.50727\CONFIG\machine.config.
   2. Keresse meg **Oracle-adatszolgáltató a .NET-hez**. Egy bejegyzés található, ahogyan az alábbi minta alapján elvileg **system.data** > **DbProviderFactories**: `<add name="Oracle Data Provider for .NET" invariant="Oracle.DataAccess.Client" description="Oracle Data Provider for .NET" type="Oracle.DataAccess.Client.OracleClientFactory, Oracle.DataAccess, Version=2.112.3.0, Culture=neutral, PublicKeyToken=89b483f429c47342" />`
* Ez a bejegyzés átmásolása a machine.config fájlban a következő .NET 4.0-s: < rendszerlemez\>: \Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config. Ezután módosítsa a verzió 4.xxx.x.x.
* Telepítés < ODP.NET telepített elérési\>a globális szerelvény-gyorsítótárban (GAC) futtatásával \11.2.0\client_1\odp.net\bin\4\Oracle.DataAccess.dll **gacutil /i [a szolgáltató elérési út]**.

### <a name="problem-2-datetime-formatting"></a>2. hiba: Dátum és idő formázása

**Hibaüzenet**

    Message=Operation failed in Oracle Database with the following error: 'ORA-01861: literal does not match format string'.,Source=,''Type=Oracle.DataAccess.Client.OracleException,Message=ORA-01861: literal does not match format string,Source=Oracle Data Provider for .NET,'.

**Felbontás**

Előfordulhat, hogy a lekérdezési karakterláncot a a másolási tevékenység miként dátumok vannak konfigurálva, az Oracle-adatbázis alapján módosítani kell. Íme egy példa (használatával a **to_date** függvény):

    "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= to_date(\\'{0:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\')  AND timestampcolumn < to_date(\\'{1:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') ', WindowStart, WindowEnd)"


## <a name="type-mapping-for-oracle"></a>Oracle-leképezés típusa

Az említett [adattovábbítási tevékenységek](data-factory-data-movement-activities.md), másolási tevékenység végzi az automatikus típuskonverziók a fogadó-típusokat az alábbi kétlépéses módszer használatával a forrás típusa:

1. A natív forrástípusok átalakítása .NET-típus.
2. A .NET-típus átalakítása a natív fogadó típusa.

Ha áthelyezi adatait az Oracle, a következő hozzárendeléseket használatosak, Oracle adattípust .NET-típus, és ez fordítva is igaz:

| Oracle-adattípus | .NET-keretrendszer adattípus |
| --- | --- |
| BFILE |Byte] |
| BLOB |Byte]<br/>(csak a támogatott Oracle 10g és későbbi verziók és a egy Microsoft-illesztőprogram) |
| CHAR |Sztring |
| CLOB |Sztring |
| DATE |DateTime |
| LEBEGŐPONTOS |Tizedes tört, karakterlánc (Ha a pontosság > 28) |
| EGÉSZ SZÁM |Tizedes tört, karakterlánc (Ha a pontosság > 28) |
| INTERVALLUM ÉV, HÓNAP |Int32 |
| INTERVALLUM NAP – MÁSODPERC |Időtartam |
| HOSSZÚ |Sztring |
| MENNYI IDEIG NYERS |Byte] |
| NCHAR |Sztring |
| NCLOB |Sztring |
| SZÁM |Tizedes tört, karakterlánc (Ha a pontosság > 28) |
| NVARCHAR2 |Sztring |
| RAW |Byte] |
| }, ROWID |Sztring |
| IDŐBÉLYEG |DateTime |
| A HELYI IDŐZÓNA IDŐBÉLYEG |DateTime |
| AZ IDŐZÓNA IDŐBÉLYEG |DateTime |
| ELŐJEL NÉLKÜLI EGÉSZ SZÁM |Szám |
| VARCHAR2 |Sztring |
| XML |Sztring |

> [!NOTE]
> Az adattípusok **IDŐKÖZ YEAR TO hónap** és **IDŐKÖZ nap TO második** egy Microsoft-illesztőprogram használata esetén nem támogatottak.

## <a name="map-source-to-sink-columns"></a>A fogadó-oszlopok térkép forrása

A fogadó-adatkészlet oszlopaihoz a forrásadatkészlet leképezés oszlopai kapcsolatos további információkért lásd: [adat-előállítóban adatkészletoszlopok leképezése](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>A relációs források megismételhető olvasása

Amikor relációs adattárakból származó adatokat másolja, ismételhetőség tartsa szem előtt, nem kívánt eredmények elkerülése érdekében. Az Azure Data Factoryben manuálisan futtathatja a szelet. Beállíthatja úgy is egy újrapróbálkozási házirend egy adatkészletet úgy, hogy a szelet akkor fut újra, ha hiba történik. Ha a szelet akkor fut újra, vagy manuálisan, vagy egy újrapróbálkozási házirend, ügyeljen arra, hogy ugyanazokat az adatokat függetlenül attól, hogy olvassa el több alkalommal szelet futtatja. További információkért lásd: [olvasni a relációs források Repeatable](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Teljesítmény és finomhangolás

Tekintse meg a [másolási tevékenységek teljesítményéről és finomhangolásáról szóló útmutató](data-factory-copy-activity-performance.md) való adatáthelyezés (másolási tevékenység) az Azure Data Factoryban teljesítményét befolyásoló tényezők ismertetése. Több módszert is optimalizálhatja azt is megtudhatják.
