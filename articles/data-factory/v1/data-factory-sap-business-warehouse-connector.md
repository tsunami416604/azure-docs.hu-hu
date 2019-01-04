---
title: Adatok áthelyezése az SAP Business warehouse-hoz az Azure Data Factory használatával |} A Microsoft Docs
description: Adatok áthelyezése az Azure Data Factory használatával az SAP Business Warehouse megismerése
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
ms.openlocfilehash: 9e972ee64d60f0fc9703e766c3ab45c3057c32a2
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54019877"
---
# <a name="move-data-from-sap-business-warehouse-using-azure-data-factory"></a>Adatok áthelyezése az SAP Business Warehouse Azure Data Factory használatával
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verzió](data-factory-sap-business-warehouse-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-sap-business-warehouse.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a jelenlegi verzió a Data Factory szolgáltatás használ, tekintse meg [SAP Business Warehouse-összekötő a v2-ben](../connector-sap-business-warehouse.md).


Ez a cikk bemutatja, hogyan használható a másolási tevékenység az Azure Data Factory egy helyszíni SAP Business Warehouse (BW) az adatok áthelyezése. Épül a [adattovábbítási tevékenységek](data-factory-data-movement-activities.md) című cikket, amely megadja az adatok áthelyezését a másolási tevékenységgel rendelkező általános áttekintése.

Adatok egy helyszíni SAP Business Warehouse adatokat az adattárból másolhatja bármely támogatott fogadó adattárba. A másolási tevékenység által fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tábla. A Data factory jelenleg csak helyez át adatokat az SAP Business Warehouse pedig más adattárakban, de a más adattárakból származó adatok áthelyezése az SAP Business Warehouse nem támogatja. 

## <a name="supported-versions-and-installation"></a>Támogatott verziók és telepítés
Ez az összekötő támogatja az SAP Business Warehouse verzió 7.x. Támogatja a másolási előforduló infocubes-értékeket és adatait (például a BEx-lekérdezések) QueryCubes MDX-lekérdezésekkel.

Ahhoz, hogy a kapcsolat az SAP BW-példányra, az alábbi összetevők telepítése:
- **Az adatkezelési átjáró**: Data Factory szolgáltatás támogatja (beleértve az SAP Business Warehouse) helyszíni adattárak csatlakozik az adatkezelési átjáró használatával egy összetevő neve. Az adatkezelési átjáró és az átjáró útmutatással kapcsolatos további információkért lásd: [áthelyezése a helyszíni adatok között adattárak felhőbeli adattárolókon](data-factory-move-data-between-onprem-and-cloud.md) cikk. Átjáróra szükség, akkor is, ha az SAP Business warehouse-bA az Azure IaaS virtuális gépen (VM) üzemel. Telepítheti az átjáró adattárként ugyanazon a virtuális Gépen vagy egy másik virtuális gép mindaddig, amíg az átjáró képes kapcsolódni az adatbázishoz.
- **SAP NetWeaver könyvtár** az átjárót tartalmazó számítógépen. Az SAP Netweaver-kódtár az SAP-rendszergazdától, vagy közvetlenül a beszerezheti a [SAP Software Download Center](https://support.sap.com/swdc). Keresse meg a **SAP Note #1025361** a letöltési hely a legújabb verziójának beolvasásához. Győződjön meg arról, hogy az SAP NetWeaver könyvtár (32 bites vagy 64 bites) architektúrája megegyezik-e a kapcsolódásiátjáró-telepítés. Ezután telepítse az összes fájlt is tartalmaz az SAP NetWeaver RFC SDK, az SAP-Jegyzetnek megfelelően. Az SAP NetWeaver-kódtár az SAP Client Tools telepítése is megtalálható.

> [!TIP]
> Helyezze a NetWeaver RFC SDK kinyert a system32 mappába DLL-ek.

## <a name="getting-started"></a>Első lépések
Egy folyamatot egy másolási tevékenységgel az adatok áthelyezéséhez a helyszíni Cassandra adattárolókból más eszközök/API-k használatával is létrehozhat. 

- A folyamat létrehozásának legegyszerűbb módja az, hogy használja a **másolása varázsló**. Lásd: [oktatóanyag: Hozzon létre egy folyamatot a másolás varázsló használatával](data-factory-copy-data-wizard-tutorial.md) gyors bemutató létrehozása egy folyamatot az adatok másolása varázsló használatával. 
- A következő eszközök használatával hozzon létre egy folyamatot: **Az Azure portal**, **Visual Studio**, **Azure PowerShell-lel**, **Azure Resource Manager-sablon**, **.NET API**, és  **REST API-val**. Lásd: [másolási tevékenység oktatóanyagát](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) egy másolási tevékenységgel ellátott adatcsatorna létrehozása a részletes útmutatóját. 

Az eszközök vagy az API-kat használja, hogy létrehoz egy folyamatot, amely a helyez át adatokat egy forrásadattárból egy fogadó adattárba a következő lépéseket fogja végrehajtani:

1. Hozzon létre **társított szolgáltatásokat** mutató hivatkozást a bemeneti és kimeneti adatokat tárolja a data factoryjához.
2. Hozzon létre **adatkészletek** , amely a másolási művelet bemeneti és kimeneti adatokat jelöli. 
3. Hozzon létre egy **folyamat** egy másolási tevékenységgel, amely egy adatkészletet bemenetként, és a egy adatkészletet pedig kimenetként. 

A varázsló használatakor a rendszer automatikusan létrehozza a Data Factory-entitásokat (társított szolgáltatások, adatkészletek és folyamat) JSON-definíciói az Ön számára. Eszközök/API-k (kivéve a .NET API) használatakor adja meg a Data Factory-entitások a JSON formátumban.  A Data Factory-entitások, amely adatokat másol egy helyszíni SAP Business warehouse-hoz használt JSON-definíciói egy minta: [JSON-példa: Adatok másolása az SAP Business warehouse-hoz az Azure Blob](#json-example-copy-data-from-sap-business-warehouse-to-azure-blob) című szakaszát. 

A következő szakaszok az SAP BW-adattárba adott Data Factory-entitások definiálásához használt JSON-tulajdonságokkal kapcsolatos részletekért:

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai
A következő táblázat a JSON-elemeket az SAP Business Warehouse (BW) társított szolgáltatás leírását.

Tulajdonság | Leírás | Megengedett értékek | Szükséges
-------- | ----------- | -------------- | --------
kiszolgáló | A kiszolgálóra, amelyen az SAP BW-példány neve. | sztring | Igen
systemNumber | Az SAP BW-rendszer rendszer száma. | Kétjegyű tizedes tört egy karakterláncból. | Igen
clientId | Az SAP W rendszerben az ügyfél ügyfél-azonosítója. | Három számjegyű tizedes tört egy karakterláncból. | Igen
felhasználónév | Az SAP-kiszolgálóhoz hozzáféréssel rendelkező felhasználó neve | sztring | Igen
jelszó | A felhasználó jelszava. | sztring | Igen
átjáró neve | Az átjáró által a Data Factory szolgáltatás a helyszíni SAP BW-példányhoz való csatlakozáshoz használandó neve. | sztring | Igen
encryptedCredential | A titkosított hitelesítő adatok karakterlánca. | sztring | Nem

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai
Szakaszok & adatkészletek definiálását tulajdonságainak teljes listáját lásd: a [adatkészletek létrehozása](data-factory-create-datasets.md) cikk. Például a szerkezetet, rendelkezésre állást és szabályzatát adatkészlet JSON szakaszok hasonlóak az összes adatkészlet esetében (az Azure SQL, az Azure blob-, az Azure table-, stb.).

A **typeProperties** szakasz eltérő az egyes adatkészlet, és az adattárban lévő adatok helyét ismerteti. Nincsenek az SAP BW-adatkészlet típusa támogatott típus-specifikus tulajdonságai **RelationalTable**. 


## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
Szakaszok & definiálását tevékenységek tulajdonságainak teljes listáját lásd: a [folyamatok létrehozása](data-factory-create-pipelines.md) cikk. Név, leírás, bemeneti és kimeneti táblák, például a tulajdonságok akkor, házirend érhető el az összes típusú tevékenységet.

Mivel a tulajdonságok érhetők el a **typeProperties** a tevékenység szakaszban tevékenységek minden típusának számától függ. A másolási tevékenységhez azok változhat a forrásként és fogadóként típusú is.

Ha a másolási tevékenység forrása típusa **RelationalSource** (amely tartalmazza az SAP BW), a következő tulajdonságok typeProperties szakasz érhető el:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| lekérdezés | Meghatározza az MDX-lekérdezés adatokat olvasni az SAP BW-példány. | MDX-lekérdezés. | Igen |


## <a name="json-example-copy-data-from-sap-business-warehouse-to-azure-blob"></a>JSON-példa: Adatok másolása az SAP Business warehouse-hoz az Azure Blob
Az alábbi példa mintául szolgáló JSON-definíciók, amelyek segítségével létrehoz egy folyamatot használatával tartalmaz [az Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) vagy [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy [Azure PowerShell-lel](data-factory-copy-activity-tutorial-using-powershell.md). Ez a példa bemutatja, hogyan adatokat másol egy helyszíni SAP Business Warehouse egy Azure Blob Storage. Azonban az adatok átmásolhatók **közvetlenül** a conditions stated above fogadóként valamelyik [Itt](data-factory-data-movement-activities.md#supported-data-stores-and-formats) a másolási tevékenységgel az Azure Data Factoryban.  

> [!IMPORTANT]
> Ez a példa JSON-kódrészletek biztosít. Nem tartalmaz részletes útmutató az adat-előállító létrehozásához. Lásd: [adatok áthelyezése a helyszíni és a felhő között](data-factory-move-data-between-onprem-and-cloud.md) részletesen ismertető cikket.

A minta az alábbi data factory-entitások rendelkezik:

1. A társított szolgáltatás típusa [SapBw](#linked-service-properties).
2. A társított szolgáltatás típusa [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Egy bemeneti [adatkészlet](data-factory-create-datasets.md) típusú [RelationalTable](#dataset-properties).
4. Kimenet [adatkészlet](data-factory-create-datasets.md) típusú [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [folyamat](data-factory-create-pipelines.md) másolási tevékenységgel, amely használja [RelationalSource](#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

A minta adatokat másol egy SAP Business Warehouse-példány Azure-blobba óránként. Ezek a minták a használt JSON-tulajdonságokat a minták a következő szakaszok ismertetik.

Első lépésként a telepítő az adatkezelési átjárót. A rendszer az utasításokat a [adatok áthelyezése a helyszíni és a felhő között](data-factory-move-data-between-onprem-and-cloud.md) cikk.

### <a name="sap-business-warehouse-linked-service"></a>SAP Business Warehouse-beli társított szolgáltatás
A társított szolgáltatás az SAP BW-példány a data factoryhoz. A type tulajdonság értéke **SapBw**. A typeProperties szakasz az SAP BW-példány kapcsolódási adatokat szolgáltat. 

```json
{
    "name": "SapBwLinkedService",
    "properties":
    {
        "type": "SapBw",
        "typeProperties":
        {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
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

### <a name="sap-bw-input-dataset"></a>SAP BW bemeneti adatkészlet
Ez az adatkészlet határozza meg az SAP Business Warehouse-adatkészletet. A Data Factory-adatkészletben típus beállítása **RelationalTable**. Jelenleg nincs megadva az SAP BW-adatkészlet típusa jellemző tulajdonságokat. A lekérdezés, a másolási tevékenység meghatározásában határoz meg, hogy milyen adatokat olvasni az SAP BW-példány. 

A Data Factory szolgáltatás külső tulajdonság beállítása igaz értékre tájékoztatja a, hogy a tábla a data factory a külső, és nem hozzák az adat-előállító adott tevékenységéhez.

Gyakorisággal és időközzel tulajdonságait határozza meg az ütemezést. Ebben az esetben az adatok van olvasni az SAP BW-példány óránként. 

```json
{
    "name": "SapBwDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapBwLinkedService",
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
Ez az adatkészlet határozza meg, hogy a kimenet az Azure Blob-adatkészlet. A tulajdonság beállítása az Azure Blobba. A typeProperties szakasz tartalmazza, az SAP BW-példány másolt adatokat tároló. Az adatok írása egy új blob minden órában (frequency: óra, interval: 1.). A mappa elérési útját a BLOB a feldolgozás alatt álló szelet kezdő időpontja alapján dinamikusan kiértékeli. A mappa elérési útját használja, év, hónap, nap és óra részei a kezdési időpontot.

```json
{
    "name": "AzureBlobDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/sapbw/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
A folyamat egy másolási tevékenység, amely a bemeneti és kimeneti adatkészleteket használatára van konfigurálva, és óránként ütemezett tartalmazza. A folyamat JSON-definíciót a **forrás** típusa **RelationalSource** (az SAP BW forrás) és **fogadó** típusa **BlobSink**. A megadott lekérdezés a **lekérdezés** tulajdonság kiválasztja az adatokat másolni az elmúlt órában.

```json
{
    "name": "CopySapBwToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "<MDX query for SAP BW>"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "SapBwDataset"
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
                "name": "SapBwToBlob"
            }
        ],
        "start": "2017-03-01T18:00:00Z",
        "end": "2017-03-01T19:00:00Z"
    }
}
```



### <a name="type-mapping-for-sap-bw"></a>SAP BW-leképezés típusa
Ahogy korábban már említettük, az a [adattovábbítási tevékenységek](data-factory-data-movement-activities.md) a cikkben a másolási tevékenység végzi az automatikus típuskonverziók a fogadó-típusokat az alábbi kétlépéses módszer a forrás-típusok közül:

1. A natív forrástípusok átalakítása typ .NET
2. A .NET-típusból átalakítása natív fogadó típusa

Ha az SAP BW helyez át adatokat, a következő hozzárendeléseket a rendszer SAP BW-típusok közül .NET Tulajdonságtípusokat használja.

A ABAP szótárban adattípus | .NET-adattípus
-------------------------------- | --------------
ACCP |  Int
CHAR | Sztring
CLNT | Sztring
PÉNZNEM | Tizedes tört
CUKY | Sztring
DEC | Tizedes tört
FLTP | Dupla
INT1 | Bájt
INT2 | Int16
INT4 | Int
NYELV | Sztring
LCHR | Sztring
LRAW | Byte]
PREC | Int16
QUAN | Tizedes tört
RAW | Byte]
RAWSTRING | Byte]
STRING | Sztring
EGYSÉG | Sztring
DATS | Sztring
NUMC | Sztring
TIMS | Sztring

> [!NOTE]
> Fogadó-adatkészlet az oszlopok a forrásadatkészlet oszlopok leképezésére, lásd: [az Azure Data Factoryban adatkészletoszlopok leképezése](data-factory-map-columns.md).


## <a name="map-source-to-sink-columns"></a>A fogadó-oszlopok térkép forrása
Fogadó-adatkészlet oszlopaihoz forrásadatkészlet leképezés oszlopai kapcsolatos további információkért lásd: [az Azure Data Factoryban adatkészletoszlopok leképezése](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>A relációs források megismételhető olvasása
Amikor adatmásolásra, relációs adatokat tárol, ismételhetőség tartsa szem előtt, nem kívánt eredmények elkerülése érdekében. Az Azure Data Factoryben futtathatja a szelet manuálisan. Beállíthatja az újrapróbálkozási szabályzat egy adatkészlethez, úgy, hogy a szelet akkor fut újra, ha hiba történik. Ha a szelet akkor fut újra, vagy módon, győződjön meg arról, hogy ugyanazokat az adatokat olvasható függetlenül attól, hogy hány alkalommal fut egy szeletet, kell. Lásd: [Repeatable olvasni a relációs források](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)

## <a name="performance-and-tuning"></a>Teljesítmény és finomhangolás
Lásd: [másolási tevékenységek teljesítményéhez és teljesítményhangolási útmutatóból](data-factory-copy-activity-performance.md) megismerheti a kulcsfontosságú szerepet játszik az adatáthelyezés (másolási tevékenység) az Azure Data Factory és a különféle módokon optimalizálhatja azt, hogy hatással lehet a teljesítményre.
