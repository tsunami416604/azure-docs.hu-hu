---
title: Adatok áthelyezése az Azure Data Factory használatával az SAP HANA-ból |} A Microsoft Docs
description: Tudnivalók az adatok áthelyezése az Azure Data Factory használatával az SAP HANA-ból.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 96d16552cfadca9b345d0f0cd0a344249897f571
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54020948"
---
# <a name="move-data-from-sap-hana-using-azure-data-factory"></a>Adatok áthelyezése az SAP HANA az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-sap-hana-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-sap-hana.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a jelenlegi verzió a Data Factory szolgáltatás használ, tekintse meg [SAP HANA-összekötő a v2-ben](../connector-sap-business-warehouse.md).

Ez a cikk bemutatja, hogyan kell használni a másolási tevékenység az Azure Data Factoryban az adatok áthelyezése egy helyszíni SAP HANA-ból. Épül a [adattovábbítási tevékenységek](data-factory-data-movement-activities.md) című cikket, amely megadja az adatok áthelyezését a másolási tevékenységgel rendelkező általános áttekintése.

Másolhat adatokat egy helyszíni SAP HANA-adattár bármely támogatott fogadó adattárba. A másolási tevékenység által fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tábla. A Data factory jelenleg csak helyez át adatokat egy SAP HANA pedig más adattárakban, de amely adatokat helyez át más adattárakban egy SAP Hana-hoz a nem támogatja.

## <a name="supported-versions-and-installation"></a>Támogatott verziók és telepítés
Ez az összekötő támogatja az SAP HANA-adatbázis bármely verzióját. Támogatja a HANA információk modellek (például az elemzési és számítási nézeteket) és a sorhoz/oszlophoz táblák SQL-lekérdezések használatával adatmásolásra.

Ahhoz, hogy a kapcsolat az SAP HANA-példányra, az alábbi összetevők telepítése:
- **Az adatkezelési átjáró**: A Data Factory szolgáltatás támogatja a Data Management Gateway összetevőt használja a helyszíni adattárak (például SAP HANA) csatlakozik. Az adatkezelési átjáró és az átjáró útmutatással kapcsolatos további információkért lásd: [áthelyezése a helyszíni adatok között adattárak felhőbeli adattárolókon](data-factory-move-data-between-onprem-and-cloud.md) cikk. Átjáróra szükség, akkor is, ha az SAP HANA az Azure IaaS virtuális gépen (VM) üzemel. Telepítheti az átjáró adattárként ugyanazon a virtuális Gépen vagy egy másik virtuális gép mindaddig, amíg az átjáró képes kapcsolódni az adatbázishoz.
- **Az SAP HANA ODBC-illesztő** az átjárót tartalmazó számítógépen. Az SAP HANA ODBC-illesztőprogramot az letöltheti a [SAP Software Download Center](https://support.sap.com/swdc). A keresést a kulcsszóval **SAP HANA CLIENT for Windows**. 

## <a name="getting-started"></a>Első lépések
Egy folyamatot egy másolási tevékenységgel, amely helyez át adatokat egy helyszíni SAP HANA adatokat az adattárból más eszközök/API-k használatával is létrehozhat. 

- A folyamat létrehozásának legegyszerűbb módja az, hogy használja a **másolása varázsló**. Lásd: [oktatóanyag: Hozzon létre egy folyamatot a másolás varázsló használatával](data-factory-copy-data-wizard-tutorial.md) gyors bemutató létrehozása egy folyamatot az adatok másolása varázsló használatával. 
- A következő eszközök használatával hozzon létre egy folyamatot: **Az Azure portal**, **Visual Studio**, **Azure PowerShell-lel**, **Azure Resource Manager-sablon**, **.NET API**, és  **REST API-val**. Lásd: [másolási tevékenység oktatóanyagát](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) egy másolási tevékenységgel ellátott adatcsatorna létrehozása a részletes útmutatóját. 

Az eszközök vagy az API-kat használja, hogy létrehoz egy folyamatot, amely a helyez át adatokat egy forrásadattárból egy fogadó adattárba a következő lépéseket fogja végrehajtani:

1. Hozzon létre **társított szolgáltatásokat** mutató hivatkozást a bemeneti és kimeneti adatokat tárolja a data factoryjához.
2. Hozzon létre **adatkészletek** , amely a másolási művelet bemeneti és kimeneti adatokat jelöli. 
3. Hozzon létre egy **folyamat** egy másolási tevékenységgel, amely egy adatkészletet bemenetként, és a egy adatkészletet pedig kimenetként. 

A varázsló használatakor a rendszer automatikusan létrehozza a Data Factory-entitásokat (társított szolgáltatások, adatkészletek és folyamat) JSON-definíciói az Ön számára. Eszközök/API-k (kivéve a .NET API) használatakor adja meg a Data Factory-entitások a JSON formátumban.  A Data Factory-entitások, amely adatokat másol egy helyszíni SAP HANA használt JSON-definíciói egy minta: [JSON-példa: Adatok másolása az SAP HANA az Azure Blob](#json-example-copy-data-from-sap-hana-to-azure-blob) című szakaszát. 

A következő szakaszok az SAP HANA-adattárba adott Data Factory-entitások definiálásához használt JSON-tulajdonságokkal kapcsolatos részletekért:

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai
A következő táblázat a JSON-elemeket az SAP HANA-beli társított szolgáltatás leírását.

Tulajdonság | Leírás | Megengedett értékek | Szükséges
-------- | ----------- | -------------- | --------
kiszolgáló | A kiszolgálóra, amelyen az SAP HANA-példány neve. Ha a kiszolgáló egy egyéni portot használ, adja meg a `server:port`. | sztring | Igen
authenticationType | Hitelesítés típusa. | karakterlánc. "Alapszintű" vagy "Windows" | Igen 
felhasználónév | Az SAP-kiszolgálóhoz hozzáféréssel rendelkező felhasználó neve | sztring | Igen
jelszó | A felhasználó jelszava. | sztring | Igen
átjáró neve | Az átjáró által a Data Factory szolgáltatás a helyszíni SAP HANA-példányhoz való csatlakozáshoz használandó neve. | sztring | Igen
encryptedCredential | A titkosított hitelesítő adatok karakterlánca. | sztring | Nem

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai
Szakaszok & adatkészletek definiálását tulajdonságainak teljes listáját lásd: a [adatkészletek létrehozása](data-factory-create-datasets.md) cikk. Például a szerkezetet, rendelkezésre állást és szabályzatát adatkészlet JSON szakaszok hasonlóak az összes adatkészlet esetében (az Azure SQL, az Azure blob-, az Azure table-, stb.).

A **typeProperties** szakasz eltérő az egyes adatkészlet, és az adattárban lévő adatok helyét ismerteti. Nincsenek az SAP HANA-adatkészlet típusa támogatott típus-specifikus tulajdonságai **RelationalTable**. 


## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
Szakaszok & definiálását tevékenységek tulajdonságainak teljes listáját lásd: a [folyamatok létrehozása](data-factory-create-pipelines.md) cikk. Név, leírás, bemeneti és kimeneti táblák, például a tulajdonságok akkor, házirend érhető el az összes típusú tevékenységet.

Mivel a tulajdonságok érhetők el a **typeProperties** a tevékenység szakaszban tevékenységek minden típusának számától függ. A másolási tevékenységhez azok változhat a forrásként és fogadóként típusú is.

Ha a másolási tevékenység forrása típusa **RelationalSource** (amely tartalmazza a SAP HANA), a következő tulajdonságok typeProperties szakasz érhető el:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| lekérdezés | Adja meg az SQL-lekérdezést az SAP HANA-példány adatokat olvasni. | SQL-lekérdezést. | Igen |

## <a name="json-example-copy-data-from-sap-hana-to-azure-blob"></a>JSON-példa: Adatok másolása az SAP HANA az Azure Blob
Az alábbi mintában példa JSON-definíciók, amelyek segítségével hozzon létre egy folyamatot biztosít [az Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) vagy [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy [Azure PowerShell-lel](data-factory-copy-activity-tutorial-using-powershell.md). Ez a példa bemutatja, hogyan másolhat adatokat egy helyszíni SAP HANA-ból egy Azure Blob Storage. Azonban az adatok átmásolhatók **közvetlenül** a fogadóként valamelyik felsorolt [Itt](data-factory-data-movement-activities.md#supported-data-stores-and-formats) a másolási tevékenységgel az Azure Data Factoryban.  

> [!IMPORTANT]
> Ez a példa JSON-kódrészletek biztosít. Nem tartalmaz részletes útmutató az adat-előállító létrehozásához. Lásd: [adatok áthelyezése a helyszíni és a felhő között](data-factory-move-data-between-onprem-and-cloud.md) részletesen ismertető cikket.

A minta az alábbi data factory-entitások rendelkezik:

1. A társított szolgáltatás típusa [SapHana](#linked-service-properties).
2. A társított szolgáltatás típusa [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Egy bemeneti [adatkészlet](data-factory-create-datasets.md) típusú [RelationalTable](#dataset-properties).
4. Kimenet [adatkészlet](data-factory-create-datasets.md) típusú [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [folyamat](data-factory-create-pipelines.md) másolási tevékenységgel, amely használja [RelationalSource](#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

A minta adatokat másol egy SAP HANA-példány Azure-blobba óránként. Ezek a minták a használt JSON-tulajdonságokat a minták a következő szakaszok ismertetik.

Első lépésként a telepítő az adatkezelési átjárót. A rendszer az utasításokat a [adatok áthelyezése a helyszíni és a felhő között](data-factory-move-data-between-onprem-and-cloud.md) cikk.

### <a name="sap-hana-linked-service"></a>SAP HANA-beli társított szolgáltatás
A társított szolgáltatás az SAP HANA-példány a data factoryhoz. A type tulajdonság értéke **SapHana**. A typeProperties szakasz az SAP HANA-példány kapcsolódási adatokat szolgáltat.

```json
{
    "name": "SapHanaLinkedService",
    "properties":
    {
        "type": "SapHana",
        "typeProperties":
        {
            "server": "<server name>",
            "authenticationType": "<Basic, or Windows>",
            "username": "<SAP user>",
            "password": "<Password for SAP user>",
            "gatewayName": "<gateway name>"
        }
    }
}

```

### <a name="azure-storage-linked-service"></a>Azure Storage társított szolgáltatás
A társított szolgáltatás az Azure Storage-fiókot a data factoryhoz. A type tulajdonság értéke **AzureStorage**. A typeProperties szakasz az Azure Storage-fiók kapcsolódási adatokat szolgáltat.

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

### <a name="sap-hana-input-dataset"></a>SAP HANA-bemeneti adatkészlet

Ez az adatkészlet határozza meg az SAP HANA-adatkészletet. A Data Factory-adatkészletben típus beállítása **RelationalTable**. Jelenleg nincs megadva az SAP HANA-adatkészlet típusa jellemző tulajdonságokat. A lekérdezés, a másolási tevékenység meghatározásában határoz meg, hogy milyen adatokat olvasni az SAP HANA-példány. 

A Data Factory szolgáltatás külső tulajdonság beállítása igaz értékre tájékoztatja a, hogy a tábla a data factory a külső, és nem hozzák az adat-előállító adott tevékenységéhez.

Gyakorisággal és időközzel tulajdonságait határozza meg az ütemezést. Ebben az esetben az adatok van olvasni az SAP HANA-példány óránként. 

```json
{
    "name": "SapHanaDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapHanaLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

### <a name="azure-blob-output-dataset"></a>Azure Blob kimeneti adatkészlet
Ez az adatkészlet határozza meg, hogy a kimenet az Azure Blob-adatkészlet. A tulajdonság beállítása az Azure Blobba. A typeProperties szakasz tartalmazza, az SAP HANA-példány másolt adatokat tároló. Az adatok írása egy új blob minden órában (frequency: óra, interval: 1.). A mappa elérési útját a BLOB a feldolgozás alatt álló szelet kezdő időpontja alapján dinamikusan kiértékeli. A mappa elérési útját használja, év, hónap, nap és óra részei a kezdési időpontot.

```json
{
    "name": "AzureBlobDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/saphana/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
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
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```


### <a name="pipeline-with-copy-activity"></a>Másolási tevékenységgel rendelkező folyamat

A folyamat egy másolási tevékenység, amely a bemeneti és kimeneti adatkészleteket használatára van konfigurálva, és óránként ütemezett tartalmazza. A folyamat JSON-definícióját a **forrás** típusa **RelationalSource** (az SAP HANA-forrás) és **fogadó** típusa **BlobSink**. A megadott SQL-lekérdezést a **lekérdezés** tulajdonság kiválasztja az adatokat másolni az elmúlt órában.

```json
{
    "name": "CopySapHanaToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "<SQL Query for HANA>"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "SapHanaDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "SapHanaToBlob"
            }
        ],
        "start": "2017-03-01T18:00:00Z",
        "end": "2017-03-01T19:00:00Z"
    }
}
```


### <a name="type-mapping-for-sap-hana"></a>SAP HANA-leképezés típusa
Ahogy korábban már említettük, az a [adattovábbítási tevékenységek](data-factory-data-movement-activities.md) a cikkben a másolási tevékenység végzi az automatikus típuskonverziók a fogadó-típusokat az alábbi kétlépéses módszer a forrás-típusok közül:

1. A natív forrástípusok átalakítása typ .NET
2. A .NET-típusból átalakítása natív fogadó típusa

Adatok áthelyezése az SAP HANA-ból, ha a következő hozzárendeléseket használják az SAP HANA-típusok közül .NET típusú.

Az SAP HANA típusa | .NET-alapú típusa
------------- | ---------------
TINYINT | Bájt
SMALLINT | Int16
INT | Int32
BIGINT | Int64
VALÓDI | Önálló
DUPLA | Önálló
TIZEDES TÖRT | Tizedes tört
LOGIKAI ÉRTÉK | Bájt
VARCHAR | Karakterlánc
NVARCHAR | Karakterlánc
CLOB | Byte]
ALPHANUM | Karakterlánc
BLOB | Byte]
DATE | DateTime
TIME | Időtartam
IDŐBÉLYEG | DateTime
SECONDDATE | DateTime

## <a name="known-limitations"></a>Ismert korlátozások
Ha az adatok másolása az SAP HANA, van néhány ismert korlátozásai:

- Az NVARCHAR karakterláncokat csonkolja hosszabb 4000 Unicode karakter
- A SMALLDECIMAL nem támogatott.
- A VARBINARY nem támogatott
- Érvényes dátumok 1899/12/30 közötti és 9999/12/31 közöttiek

## <a name="map-source-to-sink-columns"></a>A fogadó-oszlopok térkép forrása
Fogadó-adatkészlet oszlopaihoz forrásadatkészlet leképezés oszlopai kapcsolatos további információkért lásd: [az Azure Data Factoryban adatkészletoszlopok leképezése](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>A relációs források megismételhető olvasása
Amikor adatmásolásra, relációs adatokat tárol, ismételhetőség tartsa szem előtt, nem kívánt eredmények elkerülése érdekében. Az Azure Data Factoryben futtathatja a szelet manuálisan. Beállíthatja az újrapróbálkozási szabályzat egy adatkészlethez, úgy, hogy a szelet akkor fut újra, ha hiba történik. Ha a szelet akkor fut újra, vagy módon, győződjön meg arról, hogy ugyanazokat az adatokat olvasható függetlenül attól, hogy hány alkalommal fut egy szeletet, kell. Lásd: [Repeatable olvasni a relációs források](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)

## <a name="performance-and-tuning"></a>Teljesítmény és finomhangolás
Lásd: [másolási tevékenységek teljesítményéhez és teljesítményhangolási útmutatóból](data-factory-copy-activity-performance.md) megismerheti a kulcsfontosságú szerepet játszik az adatáthelyezés (másolási tevékenység) az Azure Data Factory és a különféle módokon optimalizálhatja azt, hogy hatással lehet a teljesítményre.
