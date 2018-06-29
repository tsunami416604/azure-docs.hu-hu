---
title: Adatok áthelyezése a Data Factory használatával MongoDB |} Microsoft Docs
description: Tudnivalók az adatok áthelyezése az Azure Data Factory használatával MongoDB-adatbázisból.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 10ca7d9a-7715-4446-bf59-2d2876584550
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 7c6751a0432d66aee0ff3056b212dc1b348e333f
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37045826"
---
# <a name="move-data-from-mongodb-using-azure-data-factory"></a>Helyezze át az adatokat a MongoDB Azure Data Factory használatával
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verziójával](data-factory-on-premises-mongodb-connector.md)
> * [(Az aktuális verzió) 2-es verzió](../connector-mongodb.md)

> [!NOTE]
> Ez a cikk a Data Factory 1 verziójára vonatkozik. A Data Factory szolgáltatásnak aktuális verziójának használatakor lásd [MongoDB-összekötőt, a V2](../connector-mongodb.md).


Ez a cikk ismerteti, hogyan a másolási tevékenység során az Azure Data Factoryben az adatok mozgatása egy helyszíni MongoDB-adatbázist. Buildekről nyújtanak a [adatok mozgása tevékenységek](data-factory-data-movement-activities.md) cikk, amelynek során adatátvitel a másolási tevékenység az általános áttekintést.

Egy helyszíni MongoDB adattároló adatok bármely támogatott fogadó adattárolóhoz másolhatja. A másolási tevékenység által támogatott mosdók adattárolókhoz listájáért lásd: a [adattárolókhoz támogatott](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tábla. Adat-előállító jelenleg csak áthelyezése adatait a MongoDB-tárolóban egyéb adattárakhoz, de nem az adatok áthelyezése az egyéb adattárakhoz egy MongoDB adattárral. 

## <a name="prerequisites"></a>Előfeltételek
Az Azure Data Factory szolgáltatás kell kapcsolódnia kell a helyszíni MongoDB-adatbázist a következő összetevőket kell telepíteni:

- MongoDB-verziók a következők: 2.4, 2.6, 3.0-s, 3.2-es, 3.4 és 3.6.
- Az adatkezelési átjáró ugyanazon a számítógépen, amelyen az adatbázis vagy egy külön számítógépen elkerülésére használják a források az adatbázissal. Az adatkezelési átjáró olyan szoftver, a helyszíni adatforrások csatlakozik a felhőalapú szolgáltatások biztonságának és kezelésének módja. Lásd: [az adatkezelési átjáró](data-factory-data-management-gateway.md) szóló cikkben olvashat az adatkezelési átjáró. Lásd: [tárolt adatok mozgatása felhőbe helyszíni](data-factory-move-data-between-onprem-and-cloud.md) cikk lépésenkénti adatok folyamat az átjáró beállítása áthelyezni az adatokat.

    Az átjáró telepítésekor automatikusan telepíti a MongoDB való kapcsolódáshoz használt Microsoft MongoDB ODBC-illesztőprogram.

    > [!NOTE]
    > Az átjáró használatához a MongoDB kapcsolódni, akkor is, ha az Azure IaaS virtuális gépeken fut. kell. Ha próbál csatlakozni a felhőben üzemeltetett MongoDB példánya, az infrastruktúra-szolgáltatási virtuális gép is az átjárópéldány is telepítheti.

## <a name="getting-started"></a>Első lépések
A másolási tevékenység, mely az adatok egy helyszíni MongoDB adattároló különböző eszközök/API-k használatával létrehozhat egy folyamatot.

Hozzon létre egy folyamatot a legegyszerűbb módja használatára a **másolása varázsló**. Lásd: [oktatóanyag: hozzon létre egy folyamatot, másolása varázslóval](data-factory-copy-data-wizard-tutorial.md) létrehozásával egy folyamatot, az adatok másolása varázsló segítségével gyorsan útmutatást.

Az alábbi eszközöket használhatja a folyamatokat létrehozni: **Azure-portálon**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager-sablon** , **.NET API**, és **REST API-t**. Lásd: [másolási tevékenység oktatóanyag](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) hozzon létre egy folyamatot a másolási tevékenység részletes útmutatóját. 

Akár az eszközök vagy API-k, hajtsa végre a következő lépésekkel hozza létre egy folyamatot, amely mozgatja az adatokat a forrás-tárolóban a fogadó tárolóban: 

1. Hozzon létre **összekapcsolt szolgáltatások** bemeneti és kimeneti adatok csatolásához tárolja a a data factory.
2. Hozzon létre **adatkészletek** a másolási művelet bemeneti és kimeneti adatok. 
3. Hozzon létre egy **csővezeték** , amely fogad egy bemeneti adatkészlet és egy kimeneti adatkészletet másolási tevékenységgel. 

A varázsló használatakor a Data Factory entitások (összekapcsolt szolgáltatások adatkészletek és a feldolgozási sor) JSON-definíciók automatikusan létrejönnek. Eszközök/API-k (kivéve a .NET API-t) használata esetén adja meg a Data Factory entitások a JSON formátum használatával.  Adatok másolása egy helyszíni MongoDB adattároló használt adat-előállító entitások JSON-definíciók minta, lásd: [JSON-példa: adatok másolása az MongoDB az Azure Blob](#json-example-copy-data-from-mongodb-to-azure-blob) című szakaszát. 

A következő szakaszok részletesen bemutatják, amely segítségével határozza meg a Data Factory tartozó entitások MongoDB forrás JSON-tulajdonságok:

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok
A következő táblázat tartalmazza a JSON-elemek szerepelnek jellemző leírást **OnPremisesMongoDB** társított szolgáltatás.

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| type |A type tulajdonságot kell beállítani: **OnPremisesMongoDb** |Igen |
| kiszolgáló |Kiszolgáló IP-címét vagy állomásnevét kiszolgálónevét a mongodb-Protokolltámogatással. |Igen |
| port |A MongoDB-kiszolgálóhoz a kapcsolatok figyelésére használt TCP portot. |Nem kötelező, alapértelmezett érték: 27017 |
| authenticationType |Alapszintű, vagy névtelen. |Igen |
| felhasználónév |Felhasználói fiók MongoDB eléréséhez. |Igen (Ha alapszintű hitelesítést használ). |
| jelszó |A felhasználó jelszavát. |Igen (Ha alapszintű hitelesítést használ). |
| authSource |A MongoDB-adatbázist, amely a hitelesítő adatok kereséséhez használni kívánt nevét. |Választható (Ha alapszintű hitelesítést használ). alapértelmezett: a rendszergazdai fiókot és a databaseName tulajdonsággal megadott adatbázis használ. |
| databaseName |A MongoDB-adatbázist, amely az elérni kívánt nevét. |Igen |
| gatewayName |Az átjáró, aki hozzáfér az adattár neve. |Igen |
| encryptedCredential |A hitelesítőadat-átjáró által titkosított. |Optional |

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai
Szakaszok & meghatározása adatkészletek esetében elérhető tulajdonságok teljes listáját lásd: a [adatkészletek létrehozása](data-factory-create-datasets.md) cikk. Például struktúra, a rendelkezésre állás és a házirend a DataSet adatkészlet JSON hasonlítanak minden adatkészlet esetében (Azure SQL, az Azure blob, Azure-tábla, stb.).

A **typeProperties** szakasz eltérő adatkészlet egyes típusai és információkat nyújt azokról az adattárban adatok helyét. A typeProperties szakasz típusú adatkészlet **MongoDbCollection** tulajdonságai a következők:

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| collectionName |A MongoDB-adatbázist a gyűjtemény nevét. |Igen |

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
Szakaszok & rendelkezésre álló tevékenységek meghatározó tulajdonságok teljes listáját lásd: a [létrehozása folyamatok](data-factory-create-pipelines.md) cikk. Például a nevét, leírását, valamint bemeneti és kimeneti táblák és házirend tulajdonságai minden típusú tevékenységek érhetők el.

Tulajdonságok érhetők el a **typeProperties** szakasz a tevékenység viszont eltérőek a tevékenységek minden típusának. A másolási tevékenység során két érték források és mosdók típusától függően.

Ha a forrás típusa nem **MongoDbSource** typeProperties szakaszában érhetők a következő tulajdonságokkal:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| lekérdezés |Az egyéni lekérdezés segítségével adatokat olvasni. |SQL-92 lekérdezési karakterlánc. Például: Válasszon * from tábla. |Nem (Ha **collectionName** a **dataset** van megadva) |



## <a name="json-example-copy-data-from-mongodb-to-azure-blob"></a>JSON-példa: adatok másolása az MongoDB az Azure-Blobba
Ebben a példában a minta JSON-definíciókat tartalmazzon, segítségével hozzon létre egy folyamatot biztosít [Azure-portálon](data-factory-copy-activity-tutorial-using-azure-portal.md) vagy [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Azt illusztrálja, hogyan adatok másolása egy helyszíni MongoDB egy Azure Blob Storage tárolóban. Azonban adatok átmásolhatók a megadott mosdók bármelyikét [Itt](data-factory-data-movement-activities.md#supported-data-stores-and-formats) a másolási tevékenység során az Azure Data Factory használatával.

A minta a következő data factory entitások rendelkezik:

1. A társított szolgáltatás típusa [OnPremisesMongoDb](#linked-service-properties).
2. A társított szolgáltatás típusa [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Bemeneti [dataset](data-factory-create-datasets.md) típusú [MongoDbCollection](#dataset-properties).
4. Egy kimeneti [dataset](data-factory-create-datasets.md) típusú [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [csővezeték](data-factory-create-pipelines.md) a másolási tevékenység által használt [MongoDbSource](#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

A minta másol adatokat egy lekérdezés eredményét a MongoDB adatbázis blob minden órában. A mintákat a következő szakaszok ismertetik ezeket a mintákat használt JSON-tulajdonságok.

Első lépésként, a telepítő az adatkezelési átjáró szerint utasításait a [az adatkezelési átjáró](data-factory-data-management-gateway.md) cikk.

**MongoDB társított szolgáltatáshoz:**

```json
{
    "name": "OnPremisesMongoDbLinkedService",
    "properties":
    {
        "type": "OnPremisesMongoDb",
        "typeProperties":
        {
            "authenticationType": "<Basic or Anonymous>",
            "server": "< The IP address or host name of the MongoDB server >",  
            "port": "<The number of the TCP port that the MongoDB server uses to listen for client connections.>",
            "username": "<username>",
            "password": "<password>",
           "authSource": "< The database that you want to use to check your credentials for authentication. >",
            "databaseName": "<database name>",
            "gatewayName": "<mygateway>"
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

**MongoDB bemeneti adatkészlet:** "external" beállítása: "true" arról tájékoztatja a Data Factory szolgáltatásnak, hogy a tábla külső data factoryval való, és nem hozzák adat-előállító tevékenység.

```json
{
     "name":  "MongoDbInputDataset",
    "properties": {
        "type": "MongoDbCollection",
        "linkedServiceName": "OnPremisesMongoDbLinkedService",
        "typeProperties": {
            "collectionName": "<Collection name>"    
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

**Az Azure Blob kimeneti adatkészlet:**

Adatot ír egy új blob minden órában (gyakoriság: óra, időköz: 1). A mappa elérési útját a BLOB a szelet által feldolgozott kezdési ideje alapján dinamikusan történik. A mappa elérési útját használja, év, hónap, nap és a kezdési idő órában részeit.

```json
{
    "name": "AzureBlobOutputDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/frommongodb/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Másolási tevékenység a MongoDB-forrás és a Blob fogadó egy folyamaton belül:**

A feldolgozási sor tartalmazza a másolási tevékenység során használja a fenti bemeneti és kimeneti adatkészletek konfigurált és óránkénti futásra nem ütemezték. Az adatcsatorna JSON-definícióból a **forrás** típusúra **MongoDbSource** és **fogadó** típusúra **BlobSink**. A megadott SQL-lekérdezést a **lekérdezés** tulajdonság kiválasztása az adatok másolása az elmúlt órában.

```json
{
    "name": "CopyMongoDBToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "MongoDbSource",
                        "query": "$$Text.Format('select * from  MyTable where LastModifiedDate >= {{ts\'{0:yyyy-MM-dd HH:mm:ss}\'}} AND LastModifiedDate < {{ts\'{1:yyyy-MM-dd HH:mm:ss}\'}}', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "MongoDbInputDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutputDataSet"
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
                "name": "MongoDBToAzureBlob"
            }
        ],
        "start": "2016-06-01T18:00:00Z",
        "end": "2016-06-01T19:00:00Z"
    }
}
```


## <a name="schema-by-data-factory"></a>Adat-előállító sémája
Az Azure Data Factory szolgáltatásnak a MongoDB-gyűjteményből séma kikövetkezteti a legújabb 100 dokumentumok használatával a gyűjteményben. A 100 dokumentumok teljes séma nem tartalmaznak, ha azokat az oszlopokat is figyelmen kívül hagyja a másolási művelet során.

## <a name="type-mapping-for-mongodb"></a>Mongodb-protokolltámogatással leképezésének
Ahogyan az a [adatok mozgása tevékenységek](data-factory-data-movement-activities.md) cikk, a másolási tevékenység eseményforrás-típusnak gyűjtése típusa a következő 2. lépés – a módszert használja az automatikus típuskonverziók hajtja végre:

1. A natív eseményforrás-típusnak átalakítása .NET-típusa
2. .NET-típus konvertálása natív a fogadó típusa

Ha az adatok áthelyezése a MongoDB .NET típusú a következő megfeleltetéseket használtak MongoDB típusok.

| MongoDB-típus | .NET-keretrendszer típusa |
| --- | --- |
| Bináris |Byte] |
| Logikai |Logikai |
| Dátum |DateTime |
| NumberDouble |Dupla |
| NumberInt |Int32 |
| NumberLong |Int64 |
| ObjectID |Sztring |
| Sztring |Sztring |
| UUID |GUID |
| Objektum |Renormalized történő egybesimítására "_" beágyazott elválasztójelként oszlopok |

> [!NOTE]
> Virtuális táblák használata tömbök-támogatással kapcsolatos további tudnivalókért tekintse meg [támogatja az összetett típusok virtuális táblák használata](#support-for-complex-types-using-virtual-tables) az alábbi szakasz.

Jelenleg a következő MongoDB-adattípusok nem támogatottak: DBPointer, JavaScript, Max percenkénti kulcs, reguláris kifejezés, szimbólum, Timestamp, meghatározatlan

## <a name="support-for-complex-types-using-virtual-tables"></a>Virtuális táblák használata összetett típusok támogatása
Az Azure Data Factory beépített ODBC-illesztőprogram használatával csatlakozhat, és másolja az adatokat a MongoDB-adatbázisból. A dokumentumok között különböző típusú tömb, vagy objektumok például összetett típusok az illesztőprogram újra normalizálja adatok megfelelő virtuális táblákba. Pontosabban Ha a tábla tartalmaz ilyen oszlopok, az illesztőprogram állít elő, a következő virtuális táblák:

* A **alaptábla**, amely tartalmazza a tényleges táblából, az összetett típusú oszlopok ugyanazokat az adatokat. Az alaptábla ugyanazt a nevet használja, amely valós táblázatként.
* A **virtuális tábla** minden összetett típusú oszlophoz, amely kiterjeszti a beágyazott adatok. A virtuális táblák neve a valódi tábla, "_" elválasztó és a nevét, a tömb vagy objektum nevével.

Virtuális táblák tekintse meg az adatok a valós táblázatban, az illesztőprogram, a nem normalizált adatok eléréséhez. Részletekért lásd: Példa szakaszban olvashatók. A MongoDB-tömbök tartalmának lekérdezése, és a virtuális tábla érheti el.

Használhatja a [másolása varázsló](data-factory-data-movement-activities.md#create-a-pipeline-with-copy-activity) intuitív táblázatok listájának megtekintése a MongoDB-adatbázist, beleértve a virtuális táblák és található adatok megtekintésére. Is másolása varázsló az olyan lekérdezést, és az érvényesítés lehetőségre az eredményt.

### <a name="example"></a>Példa
"ExampleTable" alatt például MongoDB tábla egy objektumokból álló tömb egy oszlopot az egyes cellák – számlákat és a skaláris típusok – minősítések tömbje egy oszlop.

| _id | Ügyfél neve | Számlák | Szolgáltatásszint | Minősítések |
| --- | --- | --- | --- | --- |
| 1111 |ABC |[{invoice_id: "123", cikk: "toaster", az ár: "456" kedvezményes: "0,2"}, {invoice_id: "124" elem: "helyezzük", az ár: "1235" kedvezményes: "0,2"}] |Silver |[5,6] |
| 2222 |XYZ |[{invoice_id: "135", cikk: "kombinált hűtőszekrények", az ár: "12543" kedvezménnyel: "0,0"}] |Arany |[1,2] |

Az illesztőprogram az egyetlen tábla képviselő virtuális táblákat hoz létre. Az első virtuális táblát kell az alaptábla nevű "ExampleTable" alább látható. Az alaptábla az eredeti tábla összes adatot tartalmaz, de az adatokat a tömbök kimaradt, és a virtuális táblázatokban ki van bontva.

| _id | Ügyfél neve | Szolgáltatásszint |
| --- | --- | --- |
| 1111 |ABC |Silver |
| 2222 |XYZ |Arany |

Az alábbi táblázatok bemutatják a virtuális táblákat, amelyek megfelelnek a példában az eredeti tömbök. Ezek a táblázatok tartalmazzák a következő:

* Vissza az eredeti elsődleges kulcsként megadott oszlop (keresztül a _id. oszlop) eredeti tömb sorának megfelelő mutató hivatkozás
* Utalhat, hogy az adatok az eredeti tömbön belüli pozíciója
* A kibontott adatok belül a tömb egyes elemei

"ExampleTable_Invoices". tábla:

| _id | ExampleTable_Invoices_dim1_idx | invoice_id | Elem | price | Kedvezmény |
| --- | --- | --- | --- | --- | --- |
| 1111 |0 |123 |a toaster |456 |0.2 |
| 1111 |1 |124 |Helyezzük |1235 |0.2 |
| 2222 |0 |135 |kombinált hűtőszekrények |12543 |0.0 |

"ExampleTable_Ratings". tábla:

| _id | ExampleTable_Ratings_dim1_idx | ExampleTable_Ratings |
| --- | --- | --- |
| 1111 |0 |5 |
| 1111 |1 |6 |
| 2222 |0 |1 |
| 2222 |1 |2 |

## <a name="map-source-to-sink-columns"></a>Térkép forrás oszlopok gyűjtése
A forrás oszlop szerepel a fogadó dataset adatkészlet leképezési oszlopok, lásd: [Azure Data Factory dataset oszlopai leképezési](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>A relációs források ismételhető Olvasás
Ha az adatok másolását a relációs adatokat tárol, ismételhetőség tartsa szem előtt, nem kívánt eredmények elkerülése érdekében. Az Azure Data Factoryben futtathatja a szelet manuálisan. Beállíthatja úgy is egy adatkészlet újrapróbálkozási házirendje, hogy a szelet akkor fut újra, ha hiba történik. A szelet akkor fut újra, vagy módon, ha győződjön meg arról, hogy ugyanazokat az adatokat olvasható függetlenül attól, hogy a szelet futtatása hány alkalommal kell. Lásd: [relációs források olvasni Repeatable](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Teljesítmény- és hangolása
Lásd: [másolási tevékenység teljesítmény- és hangolása útmutató](data-factory-copy-activity-performance.md) tájékozódhat az kulcsfontosságú szerepet játszik adatátvitelt jelölik a (másolási tevékenység során) az Azure Data Factory és különböző módokon optimalizálhatja azt, hogy hatás teljesítményét.

## <a name="next-steps"></a>További lépések
Lásd: [helyezze át az adatokat a helyszíni és a felhő között](data-factory-move-data-between-onprem-and-cloud.md) cikk részletes utasításokat az adatok folyamat létrehozása, amely mozgatja az adatokat kereshet ki egy a helyszíni adatok Azure adattárolóihoz.
