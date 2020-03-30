---
title: Adatok áthelyezése az SAP HANA-ból az Azure Data Factory használatával
description: Ismerje meg, hogyan helyezheti át az adatokat az SAP HANA-ból az Azure Data Factory használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 361b98a1cde8ee5dee99a370b46d8fc8e0f5af28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265817"
---
# <a name="move-data-from-sap-hana-using-azure-data-factory"></a>Adatok áthelyezése az SAP HANA-ból az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-sap-hana-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-sap-hana.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, olvassa el az [SAP HANA-összekötőt a V2-ben.](../connector-sap-business-warehouse.md)

Ez a cikk bemutatja, hogyan használhatja a másolási tevékenység az Azure Data Factory adatok áthelyezése a helyszíni SAP HANA.This article explain how to use the Copy Activity in Azure Data Factory adatok áthelyezése a helyszíni SAP HANA.This article explain how to use the Copy Activity in Azure Data Factory from an-premises SAP HANA. Az [adatmozgatási tevékenységek](data-factory-data-movement-activities.md) cikkre épül, amely általános áttekintést nyújt az adatmozgásról a másolási tevékenységgel.

Adatokat másolhat egy helyszíni SAP HANA-adattárból bármely támogatott fogadó adattárba. A másolási tevékenység által fogadóként támogatott adattárak listáját a [Támogatott adattárak](data-factory-data-movement-activities.md#supported-data-stores-and-formats) táblában láthatja. A Data Factory jelenleg csak az SAP HANA-ról más adattárolókba történő áthelyezést támogatja, de nem támogatja az adatok más adattárakból az SAP HANA-ba való áthelyezését.

## <a name="supported-versions-and-installation"></a>Támogatott verziók és telepítés
Ez az összekötő az SAP HANA-adatbázis bármely verzióját támogatja. Támogatja az adatok másolását hana információs modellek (például analitikus és számítási nézetek) és a Sor/oszlop táblák SQL-lekérdezések használatával.

Az SAP HANA-példányhoz való csatlakozás engedélyezéséhez telepítse a következő összetevőket:
- **Adatkezelési átjáró:** Data Factory szolgáltatás támogatja a helyszíni adattárakhoz (beleértve az SAP HANA-t is) az Adatkezelési átjáró nevű összetevő használatával való csatlakozást. Az Adatkezelési átjáróról és az átjáró beállításának lépésenkénti útmutatójáról az [Adatok áthelyezése a helyszíni adattár között a felhőbeli adattárba](data-factory-move-data-between-onprem-and-cloud.md) című cikk című témakörben olvashat. Átjáró akkor is szükség van, ha az SAP HANA üzemelteti az Azure IaaS virtuális gép (VM). Az átjáró t az adattárban lévő ugyanazon virtuális gépre vagy egy másik virtuális gépre telepítheti, amíg az átjáró csatlakozhat az adatbázishoz.
- **SAP HANA ODBC illesztőprogram** az átjárógépen. Az SAP HANA ODBC-illesztőprogramot az [SAP Software Download Center központból](https://support.sap.com/swdc) töltheti le. Keresés az **SAP HANA CLIENT for Windows**kulcsszóval. 

## <a name="getting-started"></a>Első lépések
Létrehozhat egy folyamatot egy másolási tevékenységgel, amely különböző eszközök/API-k használatával áthelyezi az adatokat egy helyszíni SAP HANA-adattárból. 

- A folyamat létrehozásának legegyszerűbb módja a **Másolás varázsló**használata. Olvassa el [az oktatóanyagot: Folyamat létrehozása a Másolás varázslóval](data-factory-copy-data-wizard-tutorial.md) című témakörben egy gyors útmutatót a folyamat másolása az adatok másolása varázslóval történő létrehozásához. 
- A következő eszközökkel is létrehozhat egy folyamatot: **Visual Studio,** **Azure PowerShell**, **Azure Resource Manager sablon**, **.NET API**és REST **API.** Lásd: [Tevékenység-oktatóanyag másolása](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) című témakörben részletes útmutatást talál egy másolási tevékenységgel rendelkező folyamat létrehozásához. 

Függetlenül attól, hogy az eszközöket vagy API-kat használja, a következő lépéseket hajthatja végre egy olyan folyamat létrehozásához, amely adatokat helyezi át a forrásadattárból a fogadó adattárába:

1. **Összekapcsolt szolgáltatások** létrehozása a bemeneti és kimeneti adattárak és az adat-előállító összekapcsolására.
2. **Adatkészletek** létrehozása a másolási művelet bemeneti és kimeneti adatainak ábrázolására. 
3. Hozzon létre egy **folyamatot** egy másolási tevékenységgel, amely egy adatkészletet bemenetként, egy adatkészletet pedig kimenetként vesz fel. 

A varázsló használatakor a Data Factory entitásokhoz (csatolt szolgáltatások, adatkészletek és a folyamat) json-definíciók automatikusan létrejönnek. Eszközök/API-k használatakor (a .NET API kivételével) ezeket a Data Factory entitásokat a JSON formátum használatával definiálhatja.  A minta JSON-definíciók a Data Factory entitások, amelyek adatok másolására egy helyszíni SAP HANA, lásd: [JSON példa: Adatok másolása az SAP HANA az Azure Blob](#json-example-copy-data-from-sap-hana-to-azure-blob) szakasz ebben a cikkben. 

A következő szakaszok az SAP HANA-adattára jellemző Data Factory-entitások definiálására használt JSON-tulajdonságok részleteit ismertetik:

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai
Az alábbi táblázat az SAP HANA-hoz csatolt szolgáltatásra jellemző JSON-elemek leírását tartalmazza.

Tulajdonság | Leírás | Megengedett értékek | Kötelező
-------- | ----------- | -------------- | --------
kiszolgáló | Annak a kiszolgálónak a neve, amelyen az SAP HANA-példány található. Ha a kiszolgáló testreszabott portot `server:port`használ, adja meg a megadását. | sztring | Igen
authenticationType | A hitelesítés típusa. | Karakterlánc. "Alap" vagy "Windows" | Igen 
felhasználónév | Az SAP-kiszolgálóhoz hozzáféréssel rendelkező felhasználó neve | sztring | Igen
jelszó | A felhasználó jelszava. | sztring | Igen
átjárónév | Az átjáró neve, amelyet a Data Factory szolgáltatásnak a helyszíni SAP HANA-példányhoz való csatlakozáshoz kell használnia. | sztring | Igen
titkosított hitelesítő adatok | A titkosított hitelesítő adatok karakterlánca. | sztring | Nem

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai
Az adatkészletek definiálására szolgáló & tulajdonságok teljes listáját az [Adatkészletek létrehozása](data-factory-create-datasets.md) című cikkben olvashat. A json-i adatkészletek például a struktúra, a rendelkezésre állás és a szabályzat hasonlóak az összes adatkészlettípushoz (Azure SQL, Azure blob, Azure table stb.).

A **typeProperties** szakasz az adatkészlet egyes típusainál eltérő, és tájékoztatást nyújt az adatok helyéről az adattárban. A **RelationalTable**típusú SAP HANA adatkészlethez nem támogatottak típusspecifikus tulajdonságok. 


## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
A tevékenységek definiálására rendelkezésre álló szakaszok & tulajdonságok teljes listáját a [Folyamatok létrehozása](data-factory-create-pipelines.md) című cikkben olvashat. Tulajdonságok, mint a név, leírás, bemeneti és kimeneti táblák, a házirendek érhetők el minden típusú tevékenységek.

Mivel a tevékenység **typeProperties** szakaszában elérhető tulajdonságok az egyes tevékenységtípusoktól függően változnak. Másolási tevékenység esetén a források és a fogadók típusától függően változnak.

Ha a másolási tevékenység forrása **RelationalSource** típusú (amely tartalmazza az SAP HANA-t is), a következő tulajdonságok érhetők el a typeProperties szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| lekérdezés | Megadja az SAP HANA-példány ból adatokat olvasandó SQL-lekérdezést. | SQL-lekérdezés. | Igen |

## <a name="json-example-copy-data-from-sap-hana-to-azure-blob"></a>JSON-példa: Adatok másolása az SAP HANA-ból az Azure Blobba
Az alábbi minta minta JSON-definíciókat tartalmaz, amelyek segítségével a [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy az Azure PowerShell használatával hozhat létre egy [folyamatot.](data-factory-copy-activity-tutorial-using-powershell.md) Ez a minta bemutatja, hogyan másolhatja az adatokat egy helyszíni SAP HANA egy Azure Blob Storage.This sample bemutatj how to copy data from an on-premises SAP HANA to an Azure Blob Storage. Azonban az adatok **közvetlenül** másolhatók az [itt](data-factory-data-movement-activities.md#supported-data-stores-and-formats) felsorolt fogadók bármelyikére az Azure Data Factory másolási tevékenység használatával.  

> [!IMPORTANT]
> Ez a minta JSON-kódrészleteket biztosít. Nem tartalmazza az adat-előállító létrehozásának lépésenkénti útmutatóit. Tekintse meg [az adatok áthelyezését a helyszíni helyek és](data-factory-move-data-between-onprem-and-cloud.md) a felhőalapú cikk között, és részletes útmutatást talál.

A minta a következő adatfeldolgozó entitásokkal rendelkezik:

1. [SapHana](#linked-service-properties)típusú összekapcsolt szolgáltatás.
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)típusú kapcsolt szolgáltatás.
3. [RelationalTable](#dataset-properties)típusú bemeneti [adatkészlet.](data-factory-create-datasets.md)
4. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)típusú kimeneti [adatkészlet.](data-factory-create-datasets.md)
5. [Relációsforrást](#copy-activity-properties) és [BlobSinket](data-factory-azure-blob-connector.md#copy-activity-properties)használó másolási tevékenységgel rendelkező [folyamat.](data-factory-create-pipelines.md)

A minta adatokat másol egy SAP HANA-példány egy Azure blob óránként. Az ezekben a mintákban használt JSON-tulajdonságokat a mintákat követő szakaszok ismertetik.

Első lépésként állítsa be az adatkezelési átjárót. Az utasítások a helyszíni helyek és a felhőalapú cikk [közötti átmozgatási](data-factory-move-data-between-onprem-and-cloud.md) adatokban találhatók.

### <a name="sap-hana-linked-service"></a>SAP HANA csatolt szolgáltatás
Ez a csatolt szolgáltatás az SAP HANA-példányt az adat-előállítóhoz kapcsolja. A típus tulajdonság **saphana**lesz állítva. A typeProperties szakasz az SAP HANA-példány kapcsolati adatait tartalmazza.

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
Ez a kapcsolt szolgáltatás az Azure Storage-fiókot az adat-előállítóhoz kapcsolja. A típustulajdonság beállítása **AzureStorage**. A typeProperties szakasz az Azure Storage-fiók kapcsolatadatait tartalmazza.

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

### <a name="sap-hana-input-dataset"></a>SAP HANA bemeneti adatkészlet

Ez az adatkészlet határozza meg az SAP HANA adatkészletet. A Data Factory adatkészlet típusát RelationalTable beállításra **állítja.** Jelenleg nem ad meg semmilyen típus-specifikus tulajdonságokat egy SAP HANA adatkészlethez. A lekérdezés a tevékenység másolása definíció határozza meg, hogy milyen adatokat kell olvasni az SAP HANA-példány. 

A külső tulajdonság true értékre állítása tájékoztatja a Data Factory szolgáltatást, hogy a tábla az adat-előállítón kívül található, és nem az adat-előállító tevékenység által előállított.

A gyakoriság és az intervallum tulajdonságai határozzák meg az ütemezést. Ebben az esetben az adatokat óránként olvassa be az SAP HANA-példány. 

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
Ez az adatkészlet határozza meg a kimeneti Azure Blob adatkészletet. A típus tulajdonság az AzureBlob beállítással van beállítva. A typeProperties szakasz azt tartalmazza, hogy az SAP HANA-példányból másolt adatok hol tárolódnak. Az adatok óránként egy új blobba vannak írva (gyakoriság: óra, időköz: 1). A blob mappaelérési útja dinamikusan kiértékelve a feldolgozás alatt álló szelet kezdési időpontja alapján történik. A mappa elérési útja a kezdési időpont év-, hónap-, nap- és órarészeit használja.

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


### <a name="pipeline-with-copy-activity"></a>Folyamat másolási tevékenységgel

A folyamat tartalmaz egy másolási tevékenységet, amely a bemeneti és kimeneti adatkészletek használatára van konfigurálva, és óránként i. A folyamat JSON-definíciójában a **forrástípus** **RelationalSource** (SAP HANA forráshoz) értékre van állítva, **a fogadó** típusa pedig **BlobSink.** A **lekérdezési** tulajdonsághoz megadott SQL-lekérdezés kiválasztja a másolni kívánt adatokat az elmúlt órában.

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


### <a name="type-mapping-for-sap-hana"></a>Az SAP HANA típusleképezése
Az [adatmozgatási tevékenységekről](data-factory-data-movement-activities.md) szóló cikkben említettek szerint a Másolás tevékenység automatikus típuskonverziót hajt végre a forrástípusokról a fogadótípusokra a következő kétlépéses megközelítéssel:

1. Konvertálás natív forrástípusokból .NET-típussá
2. Konvertálás .NET típusból natív fogadótípussá

Az SAP HANA-ból történő adatok áthelyezésekek, a következő leképezések sap HANA-típusokból .NET-típusokba kerülnek.

SAP HANA típus | .NET alapú típus
------------- | ---------------
Piciszt | Bájt
SMALLINT között | Int16
INT | Int32
BIGINT között | Int64
VALÓS SZÁM | Egyirányú
Dupla | Egyirányú
Decimális | Decimal
Logikai | Bájt
Varchar | Sztring
NVARCHAR között | Sztring
CLOB | Bájt[]
ALFANUM | Sztring
Blob | Bájt[]
DATE | DateTime
TIME | időtartam
Időbélyeg | DateTime
MÁSODIK DÁTUM | DateTime

## <a name="known-limitations"></a>Ismert korlátozások
Az SAP HANA-ból származó adatok másolásakor néhány ismert korlátozás van:

- Az NVARCHAR sztringeket a rendszer legfeljebb 4000 Unicode karakter hosszúságúra csonkolja
- A SMALLDECIMAL nem támogatott
- A VARBINARY nem támogatott
- Az érvényes dátumok 1899/12/30 és 9999/12/31 közöttiek

## <a name="map-source-to-sink-columns"></a>Forrás leképezése oszlopokhoz
Ha többet szeretne tudni arról, hogy a forrásadatkészlet oszlopait a fogadó adatkészlet oszlopaihoz szeretné-e leképezni, olvassa [el az Adatkészletoszlopok leképezése az Azure Data Factoryban című témakört.](data-factory-map-columns.md)

## <a name="repeatable-read-from-relational-sources"></a>Relációs forrásokból ismételhető olvasmony
Ha relációs adattárakból másolja az adatokat, tartsa szem előtt az ismételhetőséget a nem kívánt eredmények elkerülése érdekében. Az Azure Data Factoryban manuálisan futtathatja a szeletet. Az adatkészlet újrapróbálkozási házirendje is konfigurálható, így a szelet újrafut, ha hiba történik. Ha egy szeletet mindkét irányban újrafuttat, meg kell győződnie arról, hogy ugyanazokat az adatokat olvassa el, függetlenül attól, hogy hányszor fut egy szelet. Lásd [Ismétlődő olvasmony at relációs forrásokból](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)

## <a name="performance-and-tuning"></a>Teljesítmény és hangolás
A [Tevékenység teljesítményének másolása & hangolási útmutatóban](data-factory-copy-activity-performance.md) megismerést talál az adatok (másolási tevékenység) azure Data Factory ban az adatmozgatás (másolási tevékenység) teljesítményét befolyásoló legfontosabb tényezőkről, valamint az optimalizálás különböző módjairól.
