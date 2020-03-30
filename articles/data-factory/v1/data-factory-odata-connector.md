---
title: Adatok áthelyezése OData-forrásokból
description: Ismerje meg, hogyan helyezhetát át az adatokat az Azure Data Factory használatával az OData-forrásokból.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: de28fa56-3204-4546-a4df-21a21de43ed7
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 95f92d4e5616d7754c355610685701a8e089b84e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265908"
---
# <a name="move-data-from-an-odata-source-using-azure-data-factory"></a>Adatok áthelyezése OData-forrásból az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-odata-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-odata.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, olvassa el az [OData-összekötő a V2](../connector-odata.md)alkalmazásban című témakört.


Ez a cikk bemutatja, hogyan használhatja a másolási tevékenység az Azure Data Factory adatok áthelyezése egy OData-forrásból. Az [adatmozgatási tevékenységek](data-factory-data-movement-activities.md) cikkre épül, amely általános áttekintést nyújt az adatmozgásról a másolási tevékenységgel.

Az OData-forrásból adatokat bármely támogatott fogadó adattárba másolhat. A másolási tevékenység által fogadóként támogatott adattárak listáját a [Támogatott adattárak](data-factory-data-movement-activities.md#supported-data-stores-and-formats) táblában láthatja. A Data Factory jelenleg csak az odata-forrásból más adattárolókba való adatáthelyezést támogatja, más adattárakból más adattárolókból nem áthelyezheti az adatokat.

## <a name="supported-versions-and-authentication-types"></a>Támogatott verziók és hitelesítési típusok
Ez az OData-összekötő támogatja az OData 3.0-s és 4.0-s verzióját, és adatokat másolhat a felhőbeli OData és a helyszíni OData-forrásokból is. Az utóbbi esetében telepítenie kell az adatkezelési átjárót. Az Adatkezelési átjáróval kapcsolatos részletekért olvassa el az [Adatok áthelyezése a helyszíni és a felhőbeli](data-factory-move-data-between-onprem-and-cloud.md) környezet között című témakört.

Az alábbi hitelesítési típusok támogatottak:

* A **felhőbeli** OData-adatcsatorna eléréséhez névtelen, alapszintű (felhasználónév és jelszó) vagy Azure Active Directory-alapú OAuth-hitelesítést használhat.
* A helyszíni OData-adatcsatorna eléréséhez névtelen, alapszintű (felhasználónév és jelszó) vagy **Windows-hitelesítést** használhat.

## <a name="getting-started"></a>Első lépések
Létrehozhat egy folyamatot egy másolási tevékenységgel, amely különböző eszközök/API-k használatával áthelyezi az adatokat egy OData-forrásból.

A folyamat létrehozásának legegyszerűbb módja a **Másolás varázsló**használata. Olvassa el [az oktatóanyagot: Folyamat létrehozása a Másolás varázslóval](data-factory-copy-data-wizard-tutorial.md) című témakörben egy gyors útmutatót a folyamat másolása az adatok másolása varázslóval történő létrehozásához.

A következő eszközökkel is létrehozhat egy folyamatot: **Visual Studio,** **Azure PowerShell**, **Azure Resource Manager sablon**, **.NET API**és REST **API.** Lásd: [Tevékenység-oktatóanyag másolása](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) című témakörben részletes útmutatást talál egy másolási tevékenységgel rendelkező folyamat létrehozásához.

Függetlenül attól, hogy az eszközöket vagy API-kat használja, a következő lépéseket hajthatja végre egy olyan folyamat létrehozásához, amely adatokat helyezi át a forrásadattárból a fogadó adattárába:

1. **Összekapcsolt szolgáltatások** létrehozása a bemeneti és kimeneti adattárak és az adat-előállító összekapcsolására.
2. **Adatkészletek** létrehozása a másolási művelet bemeneti és kimeneti adatainak ábrázolására.
3. Hozzon létre egy **folyamatot** egy másolási tevékenységgel, amely egy adatkészletet bemenetként, egy adatkészletet pedig kimenetként vesz fel.

A varázsló használatakor a Data Factory entitásokhoz (csatolt szolgáltatások, adatkészletek és a folyamat) json-definíciók automatikusan létrejönnek. Eszközök/API-k használatakor (a .NET API kivételével) ezeket a Data Factory entitásokat a JSON formátum használatával definiálhatja.  Az OData-forrásból származó adatok másolásához használt Data Factory-entitások JSON-definícióival rendelkező minta a [JSON-példa: Adatok másolása Az OData-forrásból](#json-example-copy-data-from-odata-source-to-azure-blob) az Azure Blob szakasza ebben a cikkben.

A következő szakaszok az OData-forrásra jellemző Data Factory-entitások definiálására használt JSON-tulajdonságok részleteit ismertetik:

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai
Az alábbi táblázat az OData-kapcsolt szolgáltatásra jellemző JSON-elemek leírását tartalmazza.

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| type |A típustulajdonságot a következő re kell állítani: **OData** |Igen |
| url |Az OData szolgáltatás url-címe. |Igen |
| authenticationType |Az OData-forráshoz való csatlakozáshoz használt hitelesítés típusa. <br/><br/> A felhőalapú OData lehetséges értékek névtelen, egyszerű és OAuth (megjegyzés Az Azure Data Factory jelenleg csak az Azure Active Directory alapú OAuth támogatja). <br/><br/> A helyszíni OData lehetséges értékek névtelen, egyszerű és Windows. |Igen |
| felhasználónév |Adja meg a felhasználónevet, ha alapfokú hitelesítést használ. |Igen (csak akkor, ha alapfokú hitelesítést használ) |
| jelszó |Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. |Igen (csak akkor, ha alapfokú hitelesítést használ) |
| authorizedCredential |OAuth használata esetén kattintson az **Engedélyezés** gombra a Data Factory Copy wizard vagy a Szerkesztő programban, és adja meg a hitelesítő adatait, majd a tulajdonság értéke automatikusan létrejön. |Igen (csak akkor, ha OAuth-hitelesítést használ) |
| átjárónév |Annak az átjárónak a neve, amelyet a Data Factory szolgáltatásnak a helyszíni OData szolgáltatáshoz való csatlakozáshoz kell használnia. Csak akkor adja meg, ha a helyszíni OData forrásból másolja az adatokat. |Nem |

### <a name="using-basic-authentication"></a>Egyszerű hitelesítés használata
```json
{
    "name": "inputLinkedService",
    "properties":
    {
        "type": "OData",
        "typeProperties":
        {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Basic",
            "username": "username",
            "password": "password"
        }
    }
}
```

### <a name="using-anonymous-authentication"></a>Névtelen hitelesítés használata
```json
{
    "name": "ODataLinkedService",
    "properties":
    {
        "type": "OData",
        "typeProperties":
        {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
        }
    }
}
```

### <a name="using-windows-authentication-accessing-on-premises-odata-source"></a>A helyszíni OData-forráshoz való Hozzáférés Windows-hitelesítése
```json
{
    "name": "inputLinkedService",
    "properties":
    {
        "type": "OData",
        "typeProperties":
        {
            "url": "<endpoint of on-premises OData source e.g. Dynamics CRM>",
            "authenticationType": "Windows",
            "username": "domain\\user",
            "password": "password",
            "gatewayName": "mygateway"
        }
    }
}
```

### <a name="using-oauth-authentication-accessing-cloud-odata-source"></a>A felhőbeli OData-forrás eléréséhez hozzáférő OAuth-hitelesítés használata
```json
{
    "name": "inputLinkedService",
    "properties":
    {
        "type": "OData",
            "typeProperties":
        {
            "url": "<endpoint of cloud OData source e.g. https://<tenant>.crm.dynamics.com/XRMServices/2011/OrganizationData.svc>",
            "authenticationType": "OAuth",
            "authorizedCredential": "<auto generated by clicking the Authorize button on UI>"
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai
Az adatkészletek definiálására szolgáló & tulajdonságok teljes listáját az [Adatkészletek létrehozása](data-factory-create-datasets.md) című cikkben olvashat. A json-i adatkészletek például a struktúra, a rendelkezésre állás és a szabályzat hasonlóak az összes adatkészlettípushoz (Azure SQL, Azure blob, Azure table stb.).

A **typeProperties** szakasz az adatkészlet egyes típusainál eltérő, és tájékoztatást nyújt az adatok helyéről az adattárban. Az **ODataResource** típusú adatkészlet typeProperties szakasza (amely az OData adatkészletet is tartalmazza) a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| path |Az OData erőforrás elérési útja |Nem |

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
A tevékenységek definiálására rendelkezésre álló szakaszok & tulajdonságok teljes listáját a [Folyamatok létrehozása](data-factory-create-pipelines.md) című cikkben olvashat. Tulajdonságok, például név, leírás, bemeneti és kimeneti táblák és házirend állnak rendelkezésre minden típusú tevékenységek.

A tevékenység típustulajdonságai szakaszában elérhető tulajdonságok viszont az egyes tevékenységtípusoktól függően változnak. Másolási tevékenység esetén a források és a fogadók típusától függően változnak.

Ha a forrás **RelationalSource** típusú (amely magában foglalja az OData-t is) a következő tulajdonságok érhetők el a typeProperties szakaszban:

| Tulajdonság | Leírás | Példa | Kötelező |
| --- | --- | --- | --- |
| lekérdezés |Az adatok olvasásához használja az egyéni lekérdezést. |"?$select=Név, leírás&$top=5" |Nem |

## <a name="type-mapping-for-odata"></a>Típusleképezés az OData-hoz
Az [adatmozgatási tevékenységek](data-factory-data-movement-activities.md) ről szóló cikkben említettek szerint a Másolás tevékenység automatikus típuskonverziót hajt végre a forrástípusokról a fogadótípusokra a következő kétlépéses megközelítéssel.

1. Konvertálás natív forrástípusokból .NET-típussá
2. Konvertálás .NET típusból natív fogadótípussá

Amikor adatokat helyez át az OData-ból, a következő leképezések szolgálnak az OData típusokból a .NET típusba.

| OData-adatok típusa | .NET típus |
| --- | --- |
| Edm.Bináris |Bájt[] |
| Edm.Boolean |Logikai |
| Edm.Bájt |Bájt[] |
| Edm.DateTime |DateTime |
| Edm.Decim |Decimal |
| Edm.Double |Double |
| Edm.Egyetlen |Egyirányú |
| Edm.Guid |Guid |
| Edm.Int16 |Int16 |
| Edm.Int32 |Int32 |
| Edm.Int64 |Int64 |
| Edm.SByte |Int16 |
| Edm.String |Sztring |
| Edm.Idő |időtartam |
| Edm.DateTimeOffset |DateTimeOffset (Dátumidő-eltolás) |

> [!Note]
> Az OData összetett adattípusai, például az objektum nem támogatott.

## <a name="json-example-copy-data-from-odata-source-to-azure-blob"></a>JSON-példa: Adatok másolása Az OData-forrásból az Azure Blobba
Ebben a példában minta JSON-definíciók, amelyek segítségével hozzon létre egy folyamatot a [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy az [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)használatával. Azt mutatják be, hogyan másolhatja az adatokat egy OData-forrásból egy Azure Blob Storage. Azonban az adatok átmásolhatók az [itt](data-factory-data-movement-activities.md#supported-data-stores-and-formats) megadott fogadók bármelyikébe az Azure Data Factory másolási tevékenység használatával. A minta a következő Data Factory entitásokkal rendelkezik:

1. [OData](#linked-service-properties)típusú csatolt szolgáltatás .
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)típusú kapcsolt szolgáltatás.
3. [ODataResource](#dataset-properties)típusú bemeneti [adatkészlet.](data-factory-create-datasets.md)
4. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)típusú kimeneti [adatkészlet.](data-factory-create-datasets.md)
5. [Relációsforrást](#copy-activity-properties) és [BlobSinket](data-factory-azure-blob-connector.md#copy-activity-properties)használó másolási tevékenységgel rendelkező [folyamat.](data-factory-create-pipelines.md)

A minta óránként másolja az adatokat egy OData-forrás lekérdezéséből egy Azure blobba. Az ezekben a mintákban használt JSON-tulajdonságokat a mintákat követő szakaszok ismertetik.

**OData csatolt szolgáltatás:** Ez a példa a Névtelen hitelesítést használja. Az [OData-alapú szolgáltatás](#linked-service-properties) szakaszban a különböző típusú hitelesítéseket használhatja.

```json
{
    "name": "ODataLinkedService",
    "properties":
    {
        "type": "OData",
        "typeProperties":
        {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
        }
    }
}
```

**Azure Storage-hoz kapcsolódó szolgáltatás:**

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

**OData bemeneti adatkészlet:**

"külső" beállítás: az "igaz" tájékoztatja a Data Factory szolgáltatást, hogy az adatkészlet az adat-előállítón kívül található, és nem az adat-előállító tevékenység által előállított.

```json
{
    "name": "ODataDataset",
    "properties":
    {
        "type": "ODataResource",
        "typeProperties":
        {
            "path": "Products"
        },
        "linkedServiceName": "ODataLinkedService",
        "structure": [],
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "retryInterval": "00:01:00",
            "retryTimeout": "00:10:00",
            "maximumRetry": 3
        }
    }
}
```

Az **elérési út** megadása az adatkészlet definíciójában nem kötelező.

**Azure Blob kimeneti adatkészlet:**

Az adatok óránként egy új blobba (gyakoriság: óra, időköz: 1) kerül beírásra. A blob mappaelérési útja dinamikusan kiértékelve a feldolgozás alatt álló szelet kezdési időpontja alapján történik. A mappa elérési útja a kezdési időpont év-, hónap-, nap- és órarészeit használja.

```json
{
    "name": "AzureBlobODataDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/odata/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Tevékenység másolása odataforrással és blobfogadóval rendelkező folyamatban:**

A folyamat tartalmaz egy másolási tevékenységet, amely a bemeneti és kimeneti adatkészletek használatára van konfigurálva, és óránként i. A folyamat JSON-definíciójában a **forrástípus** **RelationalSource** lesz állítva, **a fogadó** típusa pedig **BlobSink**. A **lekérdezési** tulajdonsághoz megadott SQL-lekérdezés az OData-forrás legújabb (legújabb) adatait választja ki.

```json
{
    "name": "CopyODataToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "?$select=Name, Description&$top=5",
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "ODataDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobODataDataSet"
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
                "name": "ODataToBlob"
            }
        ],
        "start": "2017-02-01T18:00:00Z",
        "end": "2017-02-03T19:00:00Z"
    }
}
```

A **lekérdezés** megadása a folyamatdefinícióban nem kötelező. A Data Factory szolgáltatás által az adatok lekéréséhez használt **URL** a következő: a csatolt szolgáltatásban megadott URL-cím (kötelező) + az adatkészletben megadott elérési út (nem kötelező) + lekérdezés a folyamatban (nem kötelező).

### <a name="type-mapping-for-odata"></a>Az OData típusleképezése
Az [adatmozgatási tevékenységekről](data-factory-data-movement-activities.md) szóló cikkben említettek szerint a Másolás tevékenység automatikus típuskonverziót hajt végre a forrástípusokról a fogadótípusokra a következő kétlépéses megközelítéssel:

1. Konvertálás natív forrástípusokból .NET-típussá
2. Konvertálás .NET típusból natív fogadótípussá

Amikor adatokat helyez át az OData-adattárakból, az OData-adattípusok .NET-típusokra lesznek leképezve.

## <a name="map-source-to-sink-columns"></a>Forrás leképezése oszlopokhoz
Ha többet szeretne tudni arról, hogy a forrásadatkészlet oszlopait a fogadó adatkészlet oszlopaihoz szeretné-e leképezni, olvassa [el az Adatkészletoszlopok leképezése az Azure Data Factoryban című témakört.](data-factory-map-columns.md)

## <a name="repeatable-read-from-relational-sources"></a>Relációs forrásokból ismételhető olvasmony
Ha relációs adattárakból másolja az adatokat, tartsa szem előtt az ismételhetőséget a nem kívánt eredmények elkerülése érdekében. Az Azure Data Factoryban manuálisan futtathatja a szeletet. Az adatkészlet újrapróbálkozási házirendje is konfigurálható, így a szelet újrafut, ha hiba történik. Ha egy szeletet mindkét irányban újrafuttat, meg kell győződnie arról, hogy ugyanazokat az adatokat olvassa el, függetlenül attól, hogy hányszor fut egy szelet. Lásd: [Ismételhető olvasás relációs forrásokból](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Teljesítmény és hangolás
A [Tevékenység teljesítményének másolása & hangolási útmutatóban](data-factory-copy-activity-performance.md) megismerést talál az adatok (másolási tevékenység) azure Data Factory ban az adatmozgatás (másolási tevékenység) teljesítményét befolyásoló legfontosabb tényezőkről, valamint az optimalizálás különböző módjairól.
