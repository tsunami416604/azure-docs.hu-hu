---
title: "Adatok áthelyezése az Azure Data Factory használatával SAP Business Warehouse |} Microsoft Docs"
description: "További tudnivalók az SAP Business Warehouse Azure Data Factory használatával áthelyezni az adatokat."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: b81dc9f13533eaeec56625ede0e4c534b83e7cf7
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2018
---
# <a name="move-data-from-sap-business-warehouse-using-azure-data-factory"></a>Helyezze át az adatokat az SAP Business Warehouse Azure Data Factory használatával
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1. verzió – Általánosan elérhető](data-factory-sap-business-warehouse-connector.md)
> * [2. verzió – Előzetes verzió](../connector-sap-business-warehouse.md)

> [!NOTE]
> Ez a cikk a Data Factory általánosan elérhető 1. verziójára vonatkozik. Lásd a 2-es verziójának a Data Factory szolgáltatásnak, amely jelenleg előzetes verzióban érhető, használatakor [SAP Business Warehouse-összekötőt, a V2](../connector-sap-business-warehouse.md).


Ez a cikk ismerteti, hogyan a másolási tevékenység során az Azure Data Factory áthelyezni az adatokat a egy helyszíni SAP Business adatraktár (BW). Buildekről nyújtanak a [adatok mozgása tevékenységek](data-factory-data-movement-activities.md) cikk, amelynek során adatátvitel a másolási tevékenység az általános áttekintést.

Egy helyszíni SAP Business Warehouse adattároló adatok bármely támogatott fogadó adattárolóhoz másolhatja. A másolási tevékenység által támogatott mosdók adattárolókhoz listájáért lásd: a [adattárolókhoz támogatott](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tábla. Adat-előállító jelenleg csak áthelyezése adatait egy SAP Business Warehouse egyéb adattárakhoz, de nem az egyéb adattárakhoz adatok áthelyezése egy SAP Business Warehouse. 

## <a name="supported-versions-and-installation"></a>Támogatott verziók és telepítés
Ez az összekötő támogatja az SAP Business Warehouse verzió 7.x. Támogatja az adatok másolását előforduló infocubes-értékeket és QueryCubes (többek között a következőket BEx lekérdezések) segítségével MDX-lekérdezésekben.

Ahhoz, hogy a kapcsolat az SAP BW-példányra, a következő összetevők telepítése:
- **Az adatkezelési átjáró**: Data Factory támogatja a helyszíni adatokhoz való kapcsolódásról (beleértve az SAP Business Warehouse) az adatkezelési átjáró összetevő használatával nevezik. Az adatkezelési átjáró és az átjáró beállításának lépéseit, lásd: [áthelyezése a helyszíni adatok között adattároló adattár felhőbe](data-factory-move-data-between-onprem-and-cloud.md) cikk. Átjáróra szükség, akkor is, ha az SAP Business Warehouse Azure IaaS virtuális gépként (VM) van tárolva. Az átjárót telepítheti a adattárként azonos virtuális Gépen vagy a másik virtuális gép mindaddig, amíg az átjáró képes kapcsolódni az adatbázishoz.
- **SAP NetWeaver könyvtár** az átjárót működtető gépen. A SAP rendszergazdájától, vagy közvetlenül az SAP Netweaver könyvtár kaphat a [SAP szoftverletöltő központból](https://support.sap.com/swdc). Keresse meg a **SAP Megjegyzés #1025361** lekérni a legújabb verziót a letöltési helyét. Győződjön meg arról, hogy a SAP NetWeaver könyvtárban (32 bites vagy 64 bites) architektúrája megegyezik-e az átjáró telepítése. Telepítse az SAP NetWeaver RFC SDK az SAP Megjegyzés szereplő összes fájlt. Az SAP NetWeaver könyvtár is szerepel a SAP Client Tools telepítését.

> [!TIP]
> Helyezze a NetWeaver RFC SDK kinyert a system32 mappába a dll-fájl.

## <a name="getting-started"></a>Első lépések
A másolási tevékenység, mely az adatok egy helyszíni Cassandra adattároló különböző eszközök/API-k használatával létrehozhat egy folyamatot. 

- Hozzon létre egy folyamatot a legegyszerűbb módja használatára a **másolása varázsló**. Lásd: [oktatóanyag: hozzon létre egy folyamatot, másolása varázslóval](data-factory-copy-data-wizard-tutorial.md) létrehozásával egy folyamatot, az adatok másolása varázsló segítségével gyorsan útmutatást. 
- Az alábbi eszközöket használhatja a folyamatokat létrehozni: **Azure-portálon**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager sablon**, **.NET API**, és **REST API**. Lásd: [másolási tevékenység oktatóanyag](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) hozzon létre egy folyamatot a másolási tevékenység részletes útmutatóját. 

Akár az eszközök vagy API-k, hajtsa végre a következő lépésekkel hozza létre egy folyamatot, amely mozgatja az adatokat a forrás-tárolóban a fogadó tárolóban:

1. Hozzon létre **összekapcsolt szolgáltatások** bemeneti és kimeneti adatok csatolásához tárolja a a data factory.
2. Hozzon létre **adatkészletek** a másolási művelet bemeneti és kimeneti adatok. 
3. Hozzon létre egy **csővezeték** , amely fogad egy bemeneti adatkészlet és egy kimeneti adatkészletet másolási tevékenységgel. 

A varázsló használatakor a Data Factory entitások (összekapcsolt szolgáltatások adatkészletek és a feldolgozási sor) JSON-definíciók automatikusan létrejönnek. Eszközök/API-k (kivéve a .NET API-t) használata esetén adja meg a Data Factory entitások a JSON formátum használatával.  Adatok másolása egy helyszíni SAP Business Warehouse használt adat-előállító entitások JSON-definíciók minta, lásd: [JSON-példa: adatok másolása az SAP Business Warehouse az Azure Blob](#json-example-copy-data-from-sap-business-warehouse-to-azure-blob) című szakaszát. 

A következő szakaszok részletesen bemutatják, amely segítségével határozza meg a Data Factory tartozó entitások SAP BW adattárolóihoz JSON-tulajdonságok:

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok
A következő táblázat a JSON-elemek szerepelnek SAP Business Warehouse (BW) kapcsolódó szolgáltatásra vonatkozó leírást.

Tulajdonság | Leírás | Megengedett értékek | Szükséges
-------- | ----------- | -------------- | --------
kiszolgáló | A kiszolgálóra az SAP BW-példány neve. | karakterlánc | Igen
systemNumber | Az SAP BW rendszer rendszer száma. | Kétjegyű tizedes tört karakterláncból. | Igen
clientId | Az ügyfél számára a SAP W rendszer ügyfél-azonosítója. | Három számjegyből tizedes tört karakterláncból. | Igen
felhasználónév | Az SAP-kiszolgálóhoz hozzáféréssel rendelkező felhasználó neve | karakterlánc | Igen
jelszó | A felhasználó jelszavát. | karakterlánc | Igen
gatewayName | Az átjáró, amely a Data Factory szolgáltatásnak csatlakoznia a helyszíni SAP BW-példányra neve. | karakterlánc | Igen
encryptedCredential | A titkosított hitelesítő adatokban karakterlánc. | karakterlánc | Nem

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai
Szakaszok & meghatározása adatkészletek esetében elérhető tulajdonságok teljes listáját lásd: a [adatkészletek létrehozása](data-factory-create-datasets.md) cikk. Például struktúra, a rendelkezésre állás és a házirend a DataSet adatkészlet JSON hasonlítanak minden adatkészlet esetében (Azure SQL, az Azure blob, Azure-tábla, stb.).

A **typeProperties** szakasz eltérő adatkészlet egyes típusai és információkat nyújt azokról az adattárban adatok helyét. Nincsenek az SAP BW adatkészlet típusú támogatott típusra vonatkozó tulajdonságok **RelationalTable**. 


## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
Szakaszok & rendelkezésre álló tevékenységek meghatározó tulajdonságok teljes listáját lásd: a [létrehozása folyamatok](data-factory-create-pipelines.md) cikk. A rendszer például név, leírás, a bemeneti és kimeneti táblák tulajdonságokat olyan szabályzatok állnak rendelkezésre az összes tevékenység.

Mivel a tulajdonságok érhetők el a **typeProperties** szakasz a tevékenység tevékenységek minden típusának függenek. A másolási tevékenység során két érték források és mosdók típusától függően.

Ha a másolási tevékenység forrása típusa **RelationalSource** (amely tartalmazza az SAP BW), a következő tulajdonságok érhetők el typeProperties szakaszában:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| lekérdezés | Meghatározza az MDX-lekérdezés adatokat olvasni az SAP BW-példány. | MDX-lekérdezés. | Igen |


## <a name="json-example-copy-data-from-sap-business-warehouse-to-azure-blob"></a>JSON-példa: adatok másolása az SAP Business Warehouse az Azure-Blobba
Az alábbi példa minta JSON-definíciókat tartalmazzon, segítségével hozzon létre egy folyamatot biztosít [Azure-portálon](data-factory-copy-activity-tutorial-using-azure-portal.md) vagy [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Ez a példa bemutatja, hogyan adatok másolása az Azure Blob Storage egy helyszíni SAP Business Warehouse. Azonban az adatok átmásolhatók **közvetlenül** bármely, a megadott nyelő [Itt](data-factory-data-movement-activities.md#supported-data-stores-and-formats) a másolási tevékenység során az Azure Data Factory használatával.  

> [!IMPORTANT]
> Ez a minta JSON kódtöredékek biztosít. Nem tartalmazza az adat-előállítóban létrehozásának részletes leírása. Lásd: [adatokat a helyszíni helyek és a felhő közötti áthelyezése](data-factory-move-data-between-onprem-and-cloud.md) cikk lépéseit.

A minta a következő data factory entitások rendelkezik:

1. A társított szolgáltatás típusa [SapBw](#linked-service-properties).
2. A társított szolgáltatás típusa [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Bemeneti [dataset](data-factory-create-datasets.md) típusú [RelationalTable](#dataset-properties).
4. Egy kimeneti [dataset](data-factory-create-datasets.md) típusú [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [csővezeték](data-factory-create-pipelines.md) a másolási tevékenység által használt [RelationalSource](#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

A minta adatokat másolja át egy SAP Business Warehouse-példányból egy Azure blob óránként. A mintákat a következő szakaszok ismertetik ezeket a mintákat használt JSON-tulajdonságok.

Első lépésként a telepítő az adatkezelési átjáró. Az utasítások szerepelnek a [adatokat a helyszíni helyek és a felhő közötti áthelyezése](data-factory-move-data-between-onprem-and-cloud.md) cikk.

### <a name="sap-business-warehouse-linked-service"></a>SAP Business Warehouse társított szolgáltatás
A kapcsolódó szolgáltatás hivatkozások az SAP BW-példányt az adat-előállítóban. A type tulajdonság beállítása **SapBw**. A typeProperties a témakör az SAP BW-példány-kapcsolódási információt. 

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
A kapcsolódó szolgáltatás hivatkozások az Azure Storage-fiókban az adat-előállítóban. A type tulajdonság beállítása **AzureStorage**. A typeProperties a témakör az Azure Storage-fiók kapcsolódási információt.

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
Ez az adatkészlet meghatározása az SAP Business Warehouse-adatkészlet. A Data Factory adatkészlet típus beállítása **RelationalTable**. Jelenleg nincs megadva az SAP BW adatkészlethez típusra vonatkozó tulajdonsága. A lekérdezés a másolási tevékenység definícióban határozza meg, milyen adatokat olvasni az SAP BW-példány. 

A Data Factory szolgáltatásnak külső tulajdonság beállítása TRUE arról értesíti az, hogy a tábla külső data factoryval való, és nem hozzák adat-előállító tevékenység.

Gyakoriság és időköz tulajdonság határozza meg az ütemezés. Ebben az esetben az adatok olvasható az SAP BW példányból óránként. 

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
Ez az adatkészlet a kimenetet Azure Blob-adathalmazra határozza meg. A type tulajdonság értéke AzureBlob. A typeProperties a témakör az SAP BW-példány a másolt adatok tárolására. Az adatok írása egy új blob minden órában (gyakoriság: óra, időköz: 1). A mappa elérési útját a BLOB a szelet által feldolgozott kezdési ideje alapján dinamikusan történik. A mappa elérési útját használja, év, hónap, nap és a kezdési idő órában részeit.

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


### <a name="pipeline-with-copy-activity"></a>A másolási tevékenység-feldolgozási folyamat
A feldolgozási sor tartalmazza a másolási tevékenység, amely a bemeneti és kimeneti adatkészletek használatára van konfigurálva, és óránkénti futásra nem ütemezték. Az adatcsatorna JSON-definícióból a **forrás** típusúra **RelationalSource** (az SAP BW forrás) és **fogadó** típusúra **BlobSink**. A megadott lekérdezést az **lekérdezés** tulajdonság kiválasztása az adatok másolása az elmúlt órában.

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



### <a name="type-mapping-for-sap-bw"></a>Az SAP BW Programhoz leképezésének
Ahogyan az a [adatok mozgása tevékenységek](data-factory-data-movement-activities.md) cikk, a másolási tevékenység az eseményforrás-típusnak a következő kétlépéses módszert típusok gyűjtése automatikus típuskonverziók hajtja végre:

1. A natív eseményforrás-típusnak átalakítása .NET-típusa
2. .NET-típus konvertálása natív a fogadó típusa

Ha adatok áthelyezése az SAP BW Programhoz, a következő megfeleltetéseket használ az SAP BW típusok .NET típusú.

A ABAP szótár típusú adatok | .NET-adattípus
-------------------------------- | --------------
ACCP |  Int
KARAKTER | Karakterlánc
CLNT | Karakterlánc
CURR | Decimális
CUKY | Karakterlánc
DEC | Decimális
FLTP | Dupla
INT1 | Bájt
INT2 | Int16
INT4 | Int
LANG | Karakterlánc
LCHR | Karakterlánc
LRAW | Byte]
PREC | Int16
QUAN | Decimális
RAW | Byte]
RAWSTRING | Byte]
STRING | Karakterlánc
EGYSÉG | Karakterlánc
DATS | Karakterlánc
NUMC | Karakterlánc
TIMS | Karakterlánc

> [!NOTE]
> Képezze le a fogadó adatkészletből oszlopok forrás adatkészletből oszlopokat, lásd: [Azure Data Factory dataset oszlopai leképezési](data-factory-map-columns.md).


## <a name="map-source-to-sink-columns"></a>Térkép forrás oszlopok gyűjtése
A forrás oszlop szerepel a fogadó dataset adatkészlet leképezési oszlopok, lásd: [Azure Data Factory dataset oszlopai leképezési](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>A relációs források ismételhető Olvasás
Ha az adatok másolását a relációs adatokat tárol, ismételhetőség tartsa szem előtt, nem kívánt eredmények elkerülése érdekében. Az Azure Data Factoryben futtathatja a szelet manuálisan. Beállíthatja úgy is egy adatkészlet újrapróbálkozási házirendje, hogy a szelet akkor fut újra, ha hiba történik. A szelet akkor fut újra, vagy módon, ha győződjön meg arról, hogy ugyanazokat az adatokat olvasható függetlenül attól, hogy a szelet futtatása hány alkalommal kell. Lásd: [Repeatable olvasni a relációs források](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)

## <a name="performance-and-tuning"></a>Teljesítmény- és hangolása
Lásd: [másolási tevékenység teljesítmény- és hangolása útmutató](data-factory-copy-activity-performance.md) tájékozódhat az kulcsfontosságú szerepet játszik adatátvitelt jelölik a (másolási tevékenység során) az Azure Data Factory és különböző módokon optimalizálhatja azt, hogy hatás teljesítményét.
