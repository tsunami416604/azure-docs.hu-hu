---
title: Az OData forrásból származó adatok áthelyezése |} Microsoft Docs
description: További tudnivalók az Azure Data Factory használatával OData forrásból származó áthelyezni az adatokat.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: de28fa56-3204-4546-a4df-21a21de43ed7
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: b87ed8b9d9b43de81bfe4173d117d9f1e2bd7abd
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34622006"
---
# <a name="move-data-from-a-odata-source-using-azure-data-factory"></a>Helyezi át az adatokat a egy OData-forrásra Azure Data Factory használatával
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1. verzió – Általánosan elérhető](data-factory-odata-connector.md)
> * [2. verzió – Előzetes verzió](../connector-odata.md)

> [!NOTE]
> Ez a cikk a Data Factory általánosan elérhető 1. verziójára vonatkozik. Lásd a 2-es verziójának a Data Factory szolgáltatásnak, amely jelenleg előzetes verzióban érhető, használatakor [OData-összekötőt, a V2](../connector-odata.md).


Ez a cikk ismerteti, hogyan a másolási tevékenység során az Azure Data Factoryben az adatok mozgatása egy OData-forrásra. Buildekről nyújtanak a [adatok mozgása tevékenységek](data-factory-data-movement-activities.md) cikk, amelynek során adatátvitel a másolási tevékenység az általános áttekintést.

Adatok bármely támogatott fogadó adattárolóhoz másolhat egy OData-forrásra. A másolási tevékenység által támogatott mosdók adattárolókhoz listájáért lásd: a [adattárolókhoz támogatott](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tábla. Adat-előállító jelenleg csak áthelyezése adatokat OData forrásból származó más adattárolókhoz, de nem az egyéb adattárakhoz adatok áthelyezése egy OData-forrásra. 

## <a name="supported-versions-and-authentication-types"></a>Támogatott verziók és hitelesítési típusok
Az OData-összekötő 3.0 és 4.0-s verzióját, és mindkét felhőbeli OData adatait és a helyszíni OData források másolhatja OData-verziót támogatja. Az utóbbit telepíteni szeretné az adatkezelési átjáró. Lásd: [helyezze át az adatokat a helyszíni és a felhő között](data-factory-move-data-between-onprem-and-cloud.md) szóló cikkben olvashat az adatkezelési átjáró.

Alább hitelesítési típusok támogatottak:

* Hozzáférés **felhő** OData-adatcsatorna, használhatja, hogy a névtelen, alapszintű (felhasználónév és jelszó), vagy az Azure Active Directory-alapú OAuth-hitelesítést.
* Hozzáférés **helyszíni** OData-adatcsatorna, használja a névtelen, alapszintű (felhasználónév és jelszó), vagy a Windows-hitelesítést.

## <a name="getting-started"></a>Első lépések
A másolási tevékenység, amely helyezi át az adatokat OData forrásból származó különböző eszközök/API-k használatával létrehozhat egy folyamatot.

Hozzon létre egy folyamatot a legegyszerűbb módja használatára a **másolása varázsló**. Lásd: [oktatóanyag: hozzon létre egy folyamatot, másolása varázslóval](data-factory-copy-data-wizard-tutorial.md) létrehozásával egy folyamatot, az adatok másolása varázsló segítségével gyorsan útmutatást.

Az alábbi eszközöket használhatja a folyamatokat létrehozni: **Azure-portálon**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager sablon**, **.NET API**, és **REST API**. Lásd: [másolási tevékenység oktatóanyag](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) hozzon létre egy folyamatot a másolási tevékenység részletes útmutatóját. 

Akár az eszközök vagy API-k, hajtsa végre a következő lépésekkel hozza létre egy folyamatot, amely mozgatja az adatokat a forrás-tárolóban a fogadó tárolóban: 

1. Hozzon létre **összekapcsolt szolgáltatások** bemeneti és kimeneti adatok csatolásához tárolja a a data factory.
2. Hozzon létre **adatkészletek** a másolási művelet bemeneti és kimeneti adatok. 
3. Hozzon létre egy **csővezeték** , amely fogad egy bemeneti adatkészlet és egy kimeneti adatkészletet másolási tevékenységgel. 

A varázsló használatakor a Data Factory entitások (összekapcsolt szolgáltatások adatkészletek és a feldolgozási sor) JSON-definíciók automatikusan létrejönnek. Eszközök/API-k (kivéve a .NET API-t) használata esetén adja meg a Data Factory entitások a JSON formátum használatával.  Egy minta, amely segítségével másolja az adatokat OData forrásból származó adat-előállító entitások JSON-definíciók, lásd: [JSON-példa: adatok másolása az OData-forrásra az Azure Blob](#json-example-copy-data-from-odata-source-to-azure-blob) című szakaszát. 

A következő szakaszok részletesen bemutatják az OData-forrásra megadása a Data Factory tartozó entitások használt JSON-tulajdonságok:

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai
A következő táblázat a JSON-elemek szerepelnek kapcsolódó OData-szolgáltatásra vonatkozó leírást.

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| type |A type tulajdonságot kell beállítani: **OData** |Igen |
| url |Az OData szolgáltatási URL-címét. |Igen |
| authenticationType |Az OData-forrásra való kapcsolódáshoz használt hitelesítés típusa. <br/><br/> A felhőbeli OData a lehetséges értékek: névtelen, alapszintű és OAuth (Megjegyzés: jelenleg csak Azure Data Factory támogatási Azure Active Directory-alapú OAuth). <br/><br/> A helyszíni OData lehetséges értékei a névtelen, alapszintű és a Windows. |Igen |
| felhasználónév |Ha egyszerű hitelesítést használ, adja meg a felhasználónevet. |Igen (Ha csak az egyszerű hitelesítés használata esetén) |
| jelszó |Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. |Igen (Ha csak az egyszerű hitelesítés használata esetén) |
| authorizedCredential |Ha OAuth használ, kattintson a **engedélyezés** gombra a Data Factory másolása varázsló vagy a szerkesztőben, majd adja meg a hitelesítő adatok, akkor ez a tulajdonság értékének lesz automatikusan generált. |Igen (csak ha OAuth-hitelesítés használata esetén) |
| gatewayName |Az átjáró, amely a Data Factory szolgáltatásnak csatlakoznia a helyszíni OData-szolgáltatás neve. Csak adja meg, ha a másolt adatokat a helyszíni OData-forrásra. |Nem |

### <a name="using-basic-authentication"></a>Alapszintű hitelesítést használó
```json
{
    "name": "inputLinkedService",
    "properties":
    {
        "type": "OData",
            "typeProperties":
        {
            "url": "http://services.odata.org/OData/OData.svc",
            "authenticationType": "Basic",
            "username": "username",
            "password": "password"
        }
    }
}
```

### <a name="using-anonymous-authentication"></a>A névtelen hitelesítés segítségével
```json
{
    "name": "ODataLinkedService",
        "properties":
    {
        "type": "OData",
        "typeProperties":
        {
            "url": "http://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
        }
    }
}
```

### <a name="using-windows-authentication-accessing-on-premises-odata-source"></a>Windows-hitelesítéssel fér hozzá a helyszíni OData-forrásra
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

### <a name="using-oauth-authentication-accessing-cloud-odata-source"></a>OAuth hitelesítési elérése során a felhővel OData-forrásra
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
Szakaszok & meghatározása adatkészletek esetében elérhető tulajdonságok teljes listáját lásd: a [adatkészletek létrehozása](data-factory-create-datasets.md) cikk. Például struktúra, a rendelkezésre állás és a házirend a DataSet adatkészlet JSON hasonlítanak minden adatkészlet esetében (Azure SQL, az Azure blob, Azure-tábla, stb.).

A **typeProperties** szakasz eltérő adatkészlet egyes típusai és információkat nyújt azokról az adattárban adatok helyét. A typeProperties szakasz típusú adatkészlet **ODataResource** (amely tartalmazza a OData dataset) a következő tulajdonságokkal rendelkezik.

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| elérési út |Az OData-erőforrás elérési útja |Nem |

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
Szakaszok & rendelkezésre álló tevékenységek meghatározó tulajdonságok teljes listáját lásd: a [létrehozása folyamatok](data-factory-create-pipelines.md) cikk. Például a nevét, leírását, valamint bemeneti és kimeneti táblák és házirend tulajdonságai minden típusú tevékenységek érhetők el.

A typeProperties szakaszban a tevékenység tulajdonságai a tevékenységek minden típusának függenek. A másolási tevékenység során két érték források és mosdók típusától függően.

Ha a forrás típusa van **RelationalSource** (amely magában foglalja az OData) a következő tulajdonságok érhetők el typeProperties szakaszában:

| Tulajdonság | Leírás | Példa | Szükséges |
| --- | --- | --- | --- |
| lekérdezés |Az egyéni lekérdezés segítségével adatokat olvasni. |"? $select neve, leírása és $top = = 5" |Nem |

## <a name="type-mapping-for-odata"></a>Az OData-leképezésének
Ahogyan az a [adatok mozgása tevékenységek](data-factory-data-movement-activities.md) cikk, a másolási tevékenység az eseményforrás-típusnak a következő kétlépéses módszert típusok gyűjtése automatikus típuskonverziók hajt végre.

1. A natív eseményforrás-típusnak átalakítása .NET-típusa
2. .NET-típus konvertálása natív a fogadó típusa

Amikor adatokat OData, a következő megfeleltetéseket szolgálnak az OData-típusok .NET-típusa.

| Az OData-adattípus | .NET-típusa |
| --- | --- |
| Edm.Binary |Byte] |
| Edm.Boolean |logikai érték |
| Edm.Byte |Byte] |
| Edm.DateTime |DateTime |
| Edm.Decimal |Decimális |
| Edm.Double |Dupla |
| Edm.Single |Önálló |
| Edm.Guid |GUID |
| Edm.Int16 |Int16 |
| Edm.Int32 |Int32 |
| Edm.Int64 |Int64 |
| Edm.SByte |Int16 |
| Edm.String |Karakterlánc |
| Edm.Time |A TimeSpan |
| Edm.DateTimeOffset |DateTimeOffset |

> [!Note]
> OData összetett adattípusok pl. objektum nem támogatottak.

## <a name="json-example-copy-data-from-odata-source-to-azure-blob"></a>JSON-példa: adatok másolása az OData-forrásra az Azure-Blobba
Ebben a példában a minta JSON-definíciókat tartalmazzon, segítségével hozzon létre egy folyamatot biztosít [Azure-portálon](data-factory-copy-activity-tutorial-using-azure-portal.md) vagy [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). OData forrásból származó adatok másolása az Azure Blob Storage mutatnak. Azonban adatok átmásolhatók a megadott mosdók bármelyikét [Itt](data-factory-data-movement-activities.md#supported-data-stores-and-formats) a másolási tevékenység során az Azure Data Factory használatával. A minta a következő adat-előállító entitások rendelkezik:

1. A társított szolgáltatás típusa [OData](#linked-service-properties).
2. A társított szolgáltatás típusa [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Bemeneti [dataset](data-factory-create-datasets.md) típusú [ODataResource](#dataset-properties).
4. Egy kimeneti [dataset](data-factory-create-datasets.md) típusú [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [csővezeték](data-factory-create-pipelines.md) a másolási tevékenység által használt [RelationalSource](#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

A minta másol adatokat óránként egy OData-forrásra egy Azure-blobba lekérdezését. A mintákat a következő szakaszok ismertetik ezeket a mintákat használt JSON-tulajdonságok.

**OData társított szolgáltatás:** ebben a példában a névtelen hitelesítést használ. Lásd: [OData társított szolgáltatás](#linked-service-properties) szakasz a különböző típusú hitelesítés használható.

```json
{
    "name": "ODataLinkedService",
        "properties":
    {
        "type": "OData",
            "typeProperties":
        {
            "url": "http://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
            }
        }
}
```

**Az Azure tárolás társított szolgáltatásának:**

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

"External" beállítása: "true" arról tájékoztatja a Data Factory szolgáltatásnak, hogy az adatkészlet külső data factoryval való és adat-előállító tevékenység nem hozzák.

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

Adja meg **elérési** adatkészlet definíciója nem kötelező megadni.

**Az Azure Blob kimeneti adatkészlet:**

Adatot ír egy új blob minden órában (gyakoriság: óra, időköz: 1). A mappa elérési útját a BLOB a szelet által feldolgozott kezdési ideje alapján dinamikusan történik. A mappa elérési útját használja, év, hónap, nap és a kezdési idő órában részeit.

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


**Másolási tevékenység az OData-forrásra és Blob fogadó egy folyamaton belül:**

A feldolgozási sor tartalmazza a másolási tevékenység, amely a bemeneti és kimeneti adatkészletek használatára van konfigurálva, és óránkénti futásra nem ütemezték. Az adatcsatorna JSON-definícióból a **forrás** típusúra **RelationalSource** és **fogadó** típusúra **BlobSink**. A megadott SQL-lekérdezést a **lekérdezés** tulajdonság kiválasztja azokat a legújabb (legújabb) adatokat az OData-forrásra.

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

Adja meg **lekérdezés** az adatcsatorna definíciója nem kötelező megadni. A **URL-cím** van, hogy a Data Factory szolgáltatásnak adatok lekéréséhez használja: a társított szolgáltatás (kötelező) a megadott URL-cím + adatkészletet (nem kötelező) + (nem kötelező) a feldolgozási lekérdezés megadott elérési út.


### <a name="type-mapping-for-odata"></a>Az OData-leképezésének
Ahogyan az a [adatok mozgása tevékenységek](data-factory-data-movement-activities.md) cikk, a másolási tevékenység eseményforrás-típusnak gyűjtése típusa a következő 2. lépés – a módszert használja az automatikus típuskonverziók hajtja végre:

1. A natív eseményforrás-típusnak átalakítása .NET-típusa
2. .NET-típus konvertálása natív a fogadó típusa

OData adatok áthelyezése adatait tárolja, amikor az OData-adattípusok .NET típusú képezi le.

## <a name="map-source-to-sink-columns"></a>Térkép forrás oszlopok gyűjtése
A forrás oszlop szerepel a fogadó dataset adatkészlet leképezési oszlopok, lásd: [Azure Data Factory dataset oszlopai leképezési](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>A relációs források ismételhető Olvasás
Ha az adatok másolását a relációs adatokat tárol, ismételhetőség tartsa szem előtt, nem kívánt eredmények elkerülése érdekében. Az Azure Data Factoryben futtathatja a szelet manuálisan. Beállíthatja úgy is egy adatkészlet újrapróbálkozási házirendje, hogy a szelet akkor fut újra, ha hiba történik. A szelet akkor fut újra, vagy módon, ha győződjön meg arról, hogy ugyanazokat az adatokat olvasható függetlenül attól, hogy a szelet futtatása hány alkalommal kell. Lásd: [relációs források olvasni Repeatable](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Teljesítmény- és hangolása
Lásd: [másolási tevékenység teljesítmény- és hangolása útmutató](data-factory-copy-activity-performance.md) tájékozódhat az kulcsfontosságú szerepet játszik adatátvitelt jelölik a (másolási tevékenység során) az Azure Data Factory és különböző módokon optimalizálhatja azt, hogy hatás teljesítményét.
