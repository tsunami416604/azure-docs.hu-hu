---
title: Adatok áthelyezése a Sybase szolgáltatásból az Azure Data Factory használatával
description: Ismerje meg, hogyan helyezheti át az adatokat a Sybase-adatbázisból az Azure Data Factory használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: b379ee10-0ff5-4974-8c87-c95f82f1c5c6
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/02/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: cefa0c15dd50f95780034dcb63f888a2e1c6b65e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281248"
---
# <a name="move-data-from-sybase-using-azure-data-factory"></a>Adatok áthelyezése a Sybase szolgáltatásból az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-onprem-sybase-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-sybase.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, olvassa el a [Sybase-összekötő a V2 alkalmazásban című témakört.](../connector-sybase.md)

Ez a cikk bemutatja, hogyan használhatja a másolási tevékenység az Azure Data Factory adatok áthelyezése egy helyszíni Sybase-adatbázisból. Az [adatmozgatási tevékenységek](data-factory-data-movement-activities.md) cikkre épül, amely általános áttekintést nyújt az adatmozgásról a másolási tevékenységgel.

A helyszíni Sybase-adattárból adatokat másolhat bármely támogatott fogadó adattárba. A másolási tevékenység által fogadóként támogatott adattárak listáját a [Támogatott adattárak](data-factory-data-movement-activities.md#supported-data-stores-and-formats) táblában láthatja. A Data Factory jelenleg csak a Sybase-adattárakból más adattárolókba való átmozgatását támogatja, de nem támogatja az adatok más adattárakból a Sybase-adattárba való áthelyezését. 

## <a name="prerequisites"></a>Előfeltételek
A Data Factory szolgáltatás támogatja a helyszíni Sybase-forrásokhoz való csatlakozást az adatkezelési átjáró használatával. Tekintse meg [az adatok áthelyezését a helyszíni helyek és a felhőalapú](data-factory-move-data-between-onprem-and-cloud.md) cikk között, és ismerje meg az Adatkezelési átjárót és az átjáró beállításának lépésenkénti útmutatóját.

Átjáró akkor is szükség van, ha a Sybase-adatbázis üzemelteti egy Azure IaaS virtuális gép. Az átjáró t az adattárban lévő IaaS virtuális gépre vagy egy másik virtuális gépre telepítheti, feltéve, hogy az átjáró csatlakozhat az adatbázishoz.

> [!NOTE]
> A kapcsolatokkal/átjáróval kapcsolatos problémák elhárításával kapcsolatos tippek az [átjáróval kapcsolatos problémák elhárítása](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) című témakörben olvashat.

## <a name="supported-versions-and-installation"></a>Támogatott verziók és telepítés
Ahhoz, hogy az Adatkezelési átjáró a Sybase-adatbázishoz kapcsolódjon, telepítenie kell a [Sybase iAnywhere.Data.SQLAnywhere](https://go.microsoft.com/fwlink/?linkid=324846) 16 vagy újabb rendszer adatszolgáltatóját ugyanarra a rendszerre, mint az adatkezelési átjáró. 

Az SAP Sybase SQL Anywhere (ASA) 16-os és újabb verziója támogatott; Az IQ és az ASE nem támogatott.

## <a name="getting-started"></a>Első lépések
Létrehozhat egy folyamatot egy másolási tevékenységgel, amely különböző eszközök/API-k használatával áthelyezi az adatokat egy helyszíni Cassandra-adattárból. 

- A folyamat létrehozásának legegyszerűbb módja a **Másolás varázsló**használata. Olvassa el [az oktatóanyagot: Folyamat létrehozása a Másolás varázslóval](data-factory-copy-data-wizard-tutorial.md) című témakörben egy gyors útmutatót a folyamat másolása az adatok másolása varázslóval történő létrehozásához. 
- A következő eszközökkel is létrehozhat egy folyamatot: **Visual Studio,** **Azure PowerShell**, **Azure Resource Manager sablon**, **.NET API**és REST **API.** Lásd: [Tevékenység-oktatóanyag másolása](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) című témakörben részletes útmutatást talál egy másolási tevékenységgel rendelkező folyamat létrehozásához. 

Függetlenül attól, hogy az eszközöket vagy API-kat használja, a következő lépéseket hajthatja végre egy olyan folyamat létrehozásához, amely adatokat helyezi át a forrásadattárból a fogadó adattárába:

1. **Összekapcsolt szolgáltatások** létrehozása a bemeneti és kimeneti adattárak és az adat-előállító összekapcsolására.
2. **Adatkészletek** létrehozása a másolási művelet bemeneti és kimeneti adatainak ábrázolására. 
3. Hozzon létre egy **folyamatot** egy másolási tevékenységgel, amely egy adatkészletet bemenetként, egy adatkészletet pedig kimenetként vesz fel. 

A varázsló használatakor a Data Factory entitásokhoz (csatolt szolgáltatások, adatkészletek és a folyamat) json-definíciók automatikusan létrejönnek. Eszközök/API-k használatakor (a .NET API kivételével) ezeket a Data Factory entitásokat a JSON formátum használatával definiálhatja.  A minta JSON-definíciók data factory entitások, amelyek adatok másolására egy helyszíni Sybase-adattól, lásd: [JSON példa: Adatok másolása a Sybase az Azure Blob](#json-example-copy-data-from-sybase-to-azure-blob) szakasz ebben a cikkben. 

A következő szakaszok a Sybase-adattára jellemző Data Factory-entitások definiálására használt JSON-tulajdonságok részleteit ismertetik:

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai
Az alábbi táblázat a Sybase-hivatkozott szolgáltatásra jellemző JSON-elemek leírását tartalmazza.

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| type |A típustulajdonságnak a következő re van állítva: **OnPremisesSybase** |Igen |
| kiszolgáló |A Sybase-kiszolgáló neve. |Igen |
| adatbázis |A Sybase adatbázis neve. |Igen |
| Séma |A séma neve az adatbázisban. |Nem |
| authenticationType |A Sybase adatbázishoz való csatlakozáshoz használt hitelesítés típusa. Lehetséges értékek: Névtelen, Egyszerű és Windows. |Igen |
| felhasználónév |Adja meg a felhasználónevet, ha alapfokú vagy Windows-hitelesítést használ. |Nem |
| jelszó |Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. |Nem |
| átjárónév |Annak az átjárónak a neve, amelyet a Data Factory szolgáltatásnak a helyszíni Sybase-adatbázishoz való csatlakozáshoz kell használnia. |Igen |

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai
Az adatkészletek definiálására szolgáló & tulajdonságok teljes listáját az [Adatkészletek létrehozása](data-factory-create-datasets.md) című cikkben olvashat. A json-i adatkészletek például a struktúra, a rendelkezésre állás és a szabályzat hasonlóak az összes adatkészlettípushoz (Azure SQL, Azure blob, Azure table stb.).

A typeProperties szakasz az adatkészlet egyes típusainál eltérő, és tájékoztatást nyújt az adatok helyéről az adattárban. A **RelationalTable** típusú adatkészlet **typeProperties** szakasza (amely a Sybase adatkészletet is tartalmazza) a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| tableName |A tábla neve a Sybase adatbázis példányban, amelyre a csatolt szolgáltatás hivatkozik. |Nem (ha a **RelationalSource** **lekérdezése** meg van adva) |

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
A tevékenységek definiálására rendelkezésre álló szakaszok & tulajdonságok teljes listáját a [Folyamatok létrehozása](data-factory-create-pipelines.md) című cikkben olvashat. Tulajdonságok, például név, leírás, bemeneti és kimeneti táblák és házirend állnak rendelkezésre minden típusú tevékenységek.

Mivel a tevékenység typeProperties szakaszában elérhető tulajdonságok az egyes tevékenységtípusoktól függően változnak. Másolási tevékenység esetén a források és a fogadók típusától függően változnak.

Ha a forrás **RelationalSource** típusú (amely magában foglalja a Sybase-t is), a következő tulajdonságok érhetők el a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| lekérdezés |Az adatok olvasásához használja az egyéni lekérdezést. |SQL lekérdezési karakterlánc. Például: válassza a * lehetőséget a MyTable táblából. |Nem (ha **a tableName** of **dataset** meg van adva) |


## <a name="json-example-copy-data-from-sybase-to-azure-blob"></a>JSON-példa: Adatok másolása a Sybase-ről az Azure Blobba
A következő példa minta JSON-definíciókat tartalmaz, amelyek segítségével létrehozhat egy folyamatot a [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy az [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)használatával. Ezek azt mutatják, hogyan másolhatja az adatokat a Sybase-adatbázisból az Azure Blob Storage-ba. Azonban az adatok átmásolhatók az [itt](data-factory-data-movement-activities.md#supported-data-stores-and-formats) megadott fogadók bármelyikébe az Azure Data Factory másolási tevékenység használatával.   

A minta a következő adatfeldolgozó entitásokkal rendelkezik:

1. [OnPremisesSybase](data-factory-onprem-sybase-connector.md#linked-service-properties)típusú összekapcsolt szolgáltatás.
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)típusú kedvelt szolgáltatás.
3. [RelationalTable](data-factory-onprem-sybase-connector.md#dataset-properties)típusú bemeneti [adatkészlet.](data-factory-create-datasets.md)
4. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)típusú kimeneti [adatkészlet.](data-factory-create-datasets.md)
5. A [RelationalSource](data-factory-onprem-sybase-connector.md#copy-activity-properties) és [a BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)programot használó másolási tevékenységet használó [folyamat.](data-factory-create-pipelines.md)

A minta adatokat másol egy lekérdezéseredmény Sybase adatbázis egy blob óránként. Az ezekben a mintákban használt JSON-tulajdonságokat a mintákat követő szakaszok ismertetik.

Első lépésként állítsa be az adatkezelési átjárót. Az utasítások a helyszíni helyek és a felhőalapú cikk [közötti átmozgatási](data-factory-move-data-between-onprem-and-cloud.md) adatokban találhatók.

**Sybase kapcsolt szolgáltatás:**

```JSON
{
    "name": "OnPremSybaseLinkedService",
    "properties": {
        "type": "OnPremisesSybase",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```

**Azure Blob storage-hoz csatolt szolgáltatás:**

```JSON
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorageLinkedService",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
        }
    }
}
```

**Sybase bemeneti adatkészlet:**

A minta feltételezi, hogy létrehozott egy "MyTable" táblát a Sybase-ben, és tartalmaz egy "időbélyeg" nevű oszlopot az idősorozat-adatokhoz.

"külső" beállítás: true tájékoztatja a Data Factory szolgáltatást, hogy ez az adatkészlet az adat-előállítón kívül található, és nem az adat-előállító tevékenység által előállított. Figyelje meg, hogy a csatolt szolgáltatás **típusa** **RelationalTable**.

```JSON
{
    "name": "SybaseDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremSybaseLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
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

**Azure Blob kimeneti adatkészlet:**

Az adatok óránként egy új blobba (gyakoriság: óra, időköz: 1) kerül beírásra. A blob mappaelérési útja dinamikusan kiértékelve a feldolgozás alatt álló szelet kezdési időpontja alapján történik. A mappa elérési útja a kezdési időpont év-, hónap-, nap- és órarészeit használja.

```JSON
{
    "name": "AzureBlobSybaseDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/sybase/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Folyamat másolási tevékenységgel:**

A folyamat tartalmaz egy másolási tevékenységet, amely a bemeneti és kimeneti adatkészletek használatára van konfigurálva, és az óránkénti futtatásra van ütemezve. A folyamat JSON-definíciójában a **forrástípus** **RelationalSource** lesz állítva, **a fogadó** típusa pedig **BlobSink**. A **lekérdezési** tulajdonsághoz megadott SQL-lekérdezés a DBA-ból választja ki az adatokat. Rendelések tábla az adatbázisban.

```JSON
{
    "name": "CopySybaseToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "select * from DBA.Orders"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "inputs": [
                    {
                        "name": "SybaseDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobSybaseDataSet"
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
                "name": "SybaseToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```

## <a name="type-mapping-for-sybase"></a>A Sybase típusleképezése
Az [Adatmozgatási tevékenységek](data-factory-data-movement-activities.md) cikkben említettek szerint a Másolás tevékenység automatikus típuskonverziót hajt végre a forrástípusokról a fogadótípusokra a következő kétlépéses megközelítéssel:

1. Konvertálás natív forrástípusokból .NET-típussá
2. Konvertálás .NET típusból natív fogadótípussá

A Sybase támogatja a T-SQL és a T-SQL típusokat. Az SQL-típusoktól a .NET-típusig történő leképezési táblát az [Azure SQL Connector](data-factory-azure-sql-connector.md) cikkében olvashat.

## <a name="map-source-to-sink-columns"></a>Forrás leképezése oszlopokhoz
Ha többet szeretne tudni arról, hogy a forrásadatkészlet oszlopait a fogadó adatkészlet oszlopaihoz szeretné-e leképezni, olvassa [el az Adatkészletoszlopok leképezése az Azure Data Factoryban című témakört.](data-factory-map-columns.md)

## <a name="repeatable-read-from-relational-sources"></a>Relációs forrásokból ismételhető olvasmony
Ha relációs adattárakból másolja az adatokat, tartsa szem előtt az ismételhetőséget a nem kívánt eredmények elkerülése érdekében. Az Azure Data Factoryban manuálisan futtathatja a szeletet. Az adatkészlet újrapróbálkozási házirendje is konfigurálható, így a szelet újrafut, ha hiba történik. Ha egy szeletet mindkét irányban újrafuttat, meg kell győződnie arról, hogy ugyanazokat az adatokat olvassa el, függetlenül attól, hogy hányszor fut egy szelet. Lásd: [Ismételhető olvasás relációs forrásokból](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Teljesítmény és hangolás
A [Tevékenység teljesítményének másolása & hangolási útmutatóban](data-factory-copy-activity-performance.md) megismerést talál az adatok (másolási tevékenység) azure Data Factory ban az adatmozgatás (másolási tevékenység) teljesítményét befolyásoló legfontosabb tényezőkről, valamint az optimalizálás különböző módjairól.
