---
title: Adatok áthelyezése a MongoDB-ból
description: Ismerje meg, hogyan helyezhetát át az adatokat a MongoDB-adatbázisból az Azure Data Factory használatával.
services: data-factory
author: linda33wj
ms.author: jingwang
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/13/2018
ms.openlocfilehash: edddd100bddab1d642a8169353298a2d20620274
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281339"
---
# <a name="move-data-from-mongodb-using-azure-data-factory"></a>Adatok áthelyezése a MongoDB-ból az Azure Data Factory használatával

> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-on-premises-mongodb-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-mongodb.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, olvassa el a [MongoDB-összekötő t a V2 alkalmazásban.](../connector-mongodb.md)


Ez a cikk bemutatja, hogyan használhatja a másolási tevékenység az Azure Data Factory adatok áthelyezése egy helyszíni MongoDB-adatbázisból. Az [adatmozgatási tevékenységek](data-factory-data-movement-activities.md) cikkre épül, amely általános áttekintést nyújt az adatmozgásról a másolási tevékenységgel.

A helyszíni MongoDB-adattárból adatokat másolhat bármely támogatott fogadó adattárba. A másolási tevékenység által fogadóként támogatott adattárak listáját a [Támogatott adattárak](data-factory-data-movement-activities.md#supported-data-stores-and-formats) táblában láthatja. A Data Factory jelenleg csak a MongoDB-adattárból más adattárolókba való átmozgatását támogatja, de nem támogatja az adatok más adattárakból a MongoDB adattárba való áthelyezését.

## <a name="prerequisites"></a>Előfeltételek
Ahhoz, hogy az Azure Data Factory szolgáltatás csatlakozni tudja a helyszíni MongoDB-adatbázisához, telepítenie kell a következő összetevőket:

- A támogatott MongoDB verziók a következők: 2.4, 2.6, 3.0, 3.2, 3.4 és 3.6.
- Adatkezelési átjáró ugyanazon a gépen, amely az adatbázist üzemelteti, vagy egy külön gépen, hogy elkerülje az erőforrások versengését az adatbázissal. Az Adatkezelési átjáró egy olyan szoftver, amely biztonságos és felügyelt módon kapcsolja össze a helyszíni adatforrásokat a felhőszolgáltatásokkal. Az Adatkezelési átjáróval kapcsolatos részleteket az [Adatkezelési átjáróról](data-factory-data-management-gateway.md) szóló cikkben találja. Az [adatok áthelyezése a helyszíni környezetből a felhőbe](data-factory-move-data-between-onprem-and-cloud.md) című témakörben részletes útmutatást talál az adatfolyamat adatok áthelyezéséhez az átjáró beállításáról.

    Az átjáró telepítésekor automatikusan telepíti a MongoDB-hoz való csatlakozáshoz használt Microsoft MongoDB ODBC illesztőprogramot.

    > [!NOTE]
    > Az átjáró használatával kell csatlakoznia a MongoDB-hoz, még akkor is, ha az Az Azure IaaS virtuális gépeken található. Ha a felhőben üzemeltetett MongoDB egy példányához próbál csatlakozni, telepítheti az átjárópéldányt az IaaS virtuális gépbe is.

## <a name="getting-started"></a>Első lépések
Létrehozhat egy folyamatot egy másolási tevékenységgel, amely különböző eszközök/API-k használatával áthelyezi az adatokat egy helyszíni MongoDB-adattárból.

A folyamat létrehozásának legegyszerűbb módja a **Másolás varázsló**használata. Olvassa el [az oktatóanyagot: Folyamat létrehozása a Másolás varázslóval](data-factory-copy-data-wizard-tutorial.md) című témakörben egy gyors útmutatót a folyamat másolása az adatok másolása varázslóval történő létrehozásához.

A következő eszközökkel is létrehozhat egy folyamatot: **Visual Studio,** **Azure PowerShell**, **Azure Resource Manager sablon**, **.NET API**és REST **API.** Lásd: [Tevékenység-oktatóanyag másolása](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) című témakörben részletes útmutatást talál egy másolási tevékenységgel rendelkező folyamat létrehozásához.

Függetlenül attól, hogy az eszközöket vagy API-kat használja, a következő lépéseket hajthatja végre egy olyan folyamat létrehozásához, amely adatokat helyezi át a forrásadattárból a fogadó adattárába:

1. **Összekapcsolt szolgáltatások** létrehozása a bemeneti és kimeneti adattárak és az adat-előállító összekapcsolására.
2. **Adatkészletek** létrehozása a másolási művelet bemeneti és kimeneti adatainak ábrázolására.
3. Hozzon létre egy **folyamatot** egy másolási tevékenységgel, amely egy adatkészletet bemenetként, egy adatkészletet pedig kimenetként vesz fel.

A varázsló használatakor a Data Factory entitásokhoz (csatolt szolgáltatások, adatkészletek és a folyamat) json-definíciók automatikusan létrejönnek. Eszközök/API-k használatakor (a .NET API kivételével) ezeket a Data Factory entitásokat a JSON formátum használatával definiálhatja.  A helyszíni MongoDB-adattárból adatok másolására használt Data Factory-entitások JSON-definícióival rendelkező minta a [JSON-példa: Adatok másolása a MongoDB-ból](#json-example-copy-data-from-mongodb-to-azure-blob) az Azure Blob szakaszba című témakörben található.

A következő szakaszok a MongoDB-forrásra jellemző Data Factory-entitások definiálására használt JSON-tulajdonságok részleteit ismertetik:

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai
Az alábbi táblázat az **OnPremisesMongoDB** csatolt szolgáltatásra jellemző JSON-elemek leírását tartalmazza.

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| type |A típustulajdonságnak a következő re van állítva: **OnPremisesMongoDb** |Igen |
| kiszolgáló |A MongoDB-kiszolgáló IP-címe vagy állomásneve. |Igen |
| port |TCP-port, amelyet a MongoDB-kiszolgáló az ügyfélkapcsolatok figyelésére használ. |Nem kötelező, alapértelmezett érték: 27017 |
| authenticationType |Alapszintű vagy Névtelen. |Igen |
| felhasználónév |Felhasználói fiók a MongoDB eléréséhez. |Igen (ha egyszerű hitelesítést használ). |
| jelszó |A felhasználó jelszava. |Igen (ha egyszerű hitelesítést használ). |
| authForrás |A hitelesítő adatok hitelesítéséhez használni kívánt MongoDB-adatbázis neve. |Nem kötelező (ha alapfokú hitelesítést használ). default: a rendszergazdai fiókot és az databaseName tulajdonsággal megadott adatbázist használja. |
| adatbázisneve |Az elérni kívánt MongoDB-adatbázis neve. |Igen |
| átjárónév |Az adattárhoz hozzáférő átjáró neve. |Igen |
| titkosított hitelesítő adatok |Az átjáró által titkosított hitelesítő adat. |Optional |

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai
Az adatkészletek definiálására szolgáló & tulajdonságok teljes listáját az [Adatkészletek létrehozása](data-factory-create-datasets.md) című cikkben olvashat. A json-i adatkészletek például a struktúra, a rendelkezésre állás és a szabályzat hasonlóak az összes adatkészlettípushoz (Azure SQL, Azure blob, Azure table stb.).

A **typeProperties** szakasz az adatkészlet egyes típusainál eltérő, és tájékoztatást nyújt az adatok helyéről az adattárban. A **MongoDbCollection** típusú adatkészlet typeProperties szakasza a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| Lekérdezés_neve |A gyűjtemény neve a MongoDB adatbázisban. |Igen |

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
A tevékenységek definiálására rendelkezésre álló szakaszok & tulajdonságok teljes listáját a [Folyamatok létrehozása](data-factory-create-pipelines.md) című cikkben olvashat. Tulajdonságok, például név, leírás, bemeneti és kimeneti táblák és házirend állnak rendelkezésre minden típusú tevékenységek.

A tevékenység **típustulajdonságai** szakaszában elérhető tulajdonságok viszont az egyes tevékenységtípusoktól függően változnak. Másolási tevékenység esetén a források és a fogadók típusától függően változnak.

Ha a forrás **MongoDbSource** típusú, a következő tulajdonságok érhetők el a typeProperties szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| lekérdezés |Az adatok olvasásához használja az egyéni lekérdezést. |SQL-92 lekérdezési karakterlánc. Például: válassza a * lehetőséget a MyTable táblából. |Nem (ha **gyűjteményAz** **adatkészlet** neve meg van adva) |



## <a name="json-example-copy-data-from-mongodb-to-azure-blob"></a>JSON-példa: Adatok másolása a MongoDB-ból az Azure Blobba
Ebben a példában minta JSON-definíciók, amelyek segítségével hozzon létre egy folyamatot a [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy az [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)használatával. Bemutatja, hogyan másolhat adatokat egy helyszíni MongoDB-ból egy Azure Blob Storage-ba. Azonban az adatok átmásolhatók az [itt](data-factory-data-movement-activities.md#supported-data-stores-and-formats) megadott fogadók bármelyikébe az Azure Data Factory másolási tevékenység használatával.

A minta a következő adatfeldolgozó entitásokkal rendelkezik:

1. [OnPremisesMongoDb](#linked-service-properties)típusú csatolt szolgáltatás.
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)típusú kapcsolt szolgáltatás.
3. [MongoDbCollection](#dataset-properties)típusú bemeneti [adatkészlet.](data-factory-create-datasets.md)
4. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)típusú kimeneti [adatkészlet.](data-factory-create-datasets.md)
5. [MongoDbSource](#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)programot használó másolási tevékenységgel rendelkező [folyamat.](data-factory-create-pipelines.md)

A minta óránként másolja az adatokat egy lekérdezéseredményből a MongoDB adatbázisból egy blobba. Az ezekben a mintákban használt JSON-tulajdonságokat a mintákat követő szakaszok ismertetik.

Első lépésként állítsa be az adatkezelési átjárót az [Adatkezelési átjáró](data-factory-data-management-gateway.md) cikkutasításainak megfelelően.

**MongoDB kapcsolt szolgáltatás:**

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

**MongoDB bemeneti adatkészlet:** A "külső": "true" beállítás tájékoztatja a Data Factory szolgáltatást, hogy a tábla az adat-előállítón kívül található, és nem az adat-előállító tevékenység által előállított.

```json
{
    "name": "MongoDbInputDataset",
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

**Azure Blob kimeneti adatkészlet:**

Az adatok óránként egy új blobba (gyakoriság: óra, időköz: 1) kerül beírásra. A blob mappaelérési útja dinamikusan kiértékelve a feldolgozás alatt álló szelet kezdési időpontja alapján történik. A mappa elérési útja a kezdési időpont év-, hónap-, nap- és órarészeit használja.

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

**Tevékenység másolása egy folyamat ban MongoDB forrás és Blob sink:**

A folyamat tartalmaz egy másolási tevékenységet, amely a fenti bemeneti és kimeneti adatkészletek használatára van konfigurálva, és óránként i. A JSON-definícióban a **forrástípus** **MongoDbSource-ra** van állítva, **a fogadó** típusa pedig **BlobSink**. A **lekérdezési** tulajdonsághoz megadott SQL-lekérdezés kiválasztja a másolni kívánt adatokat az elmúlt órában.

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
                        "query": "$$Text.Format('select * from MyTable where LastModifiedDate >= {{ts\'{0:yyyy-MM-dd HH:mm:ss}\'}} AND LastModifiedDate < {{ts\'{1:yyyy-MM-dd HH:mm:ss}\'}}', WindowStart, WindowEnd)"
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


## <a name="schema-by-data-factory"></a>Séma adatgyár szerint
Az Azure Data Factory szolgáltatás a gyűjtemény legújabb 100 dokumentumának használatával a MongoDB-gyűjteményséből következtet. Ha ez a 100 dokumentum nem tartalmaz teljes sémát, előfordulhat, hogy egyes oszlopok figyelmen kívül hagynak a másolási művelet során.

## <a name="type-mapping-for-mongodb"></a>A MongoDB típusleképezése
Az [adatmozgatási tevékenységekről](data-factory-data-movement-activities.md) szóló cikkben említettek szerint a Másolás tevékenység automatikus típuskonverziót hajt végre a forrástípusokról a fogadótípusokra a következő kétlépéses megközelítéssel:

1. Konvertálás natív forrástípusokból .NET-típussá
2. Konvertálás .NET típusból natív fogadótípussá

Amikor adatokat helyez át a MongoDB-ba, a következő leképezéseket használjuk a MongoDB típusokból a .NET típusokba.

| MongoDB típusa | .NET keretrendszer típusa |
| --- | --- |
| Bináris |Bájt[] |
| Logikai |Logikai |
| Dátum |DateTime |
| SzámDupla |Double |
| Számint |Int32 |
| Számhosszú |Int64 |
| ObjectID |Sztring |
| Sztring |Sztring |
| Uuid |Guid |
| Objektum |Újranormalizálva összeolvasztó oszlopokká, "_" elválasztóként |

> [!NOTE]
> A virtuális táblákat használó tömbök támogatásáról az alábbi, [virtuális táblákat használó összetett típusok támogatása](#support-for-complex-types-using-virtual-tables) című szakaszban olvashat.

Jelenleg a következő MongoDB adattípusok nem támogatottak: DBPointer, JavaScript, Max/Min kulcs, Reguláris kifejezés, Szimbólum, Időbélyeg, Meghatározatlan

## <a name="support-for-complex-types-using-virtual-tables"></a>Virtuális táblákat használó összetett típusok támogatása
Az Azure Data Factory egy beépített ODBC-illesztőprogramot használ a MongoDB-adatbázishoz való csatlakozáshoz és az adatok másolásához. Összetett típusok, például tömbök vagy különböző típusú objektumok esetén az illesztőprogram újra normalizálja az adatokat a megfelelő virtuális táblákká. Ha egy tábla ilyen oszlopokat tartalmaz, az illesztőprogram a következő virtuális táblákat hozza létre:

* Egy **alaptábla**, amely ugyanazokat az adatokat tartalmazza, mint a valós tábla, kivéve az összetett típusoszlopokat. Az alaptábla ugyanazt a nevet használja, mint az általa képviselt valódi tábla.
* Virtuális **tábla** minden egyes összetett típusoszlophoz, amely kibővíti a beágyazott adatokat. A virtuális táblák elnevezése a valódi tábla, az elválasztó "_" és a tömb vagy objektum nevével van elnevezve.

A virtuális táblák a valós táblában lévő adatokra hivatkoznak, így az illesztőprogram hozzáférhet a denormalizált adatokhoz. Lásd: Példa szakasz az alábbi részletekben. A MongoDB tömbök tartalmát a virtuális táblák lekérdezésével és az azokhoz való csatlakozással érheti el.

A Másolás [varázslóval](data-factory-data-movement-activities.md#create-a-pipeline-with-copy-activity) intuitív módon megtekintheti a MongoDB adatbázis tábláinak listáját, beleértve a virtuális táblákat is, és megtekintheti a benne lévő adatokelőnézetet. Az eredmény megtekintéséhez lekérdezést is létrehozhat a Másolás varázslóban, és érvényesítheti az eredményt.

### <a name="example"></a>Példa
Az alábbi "ExampleTable" például egy MongoDB-táblázat, amely minden cellában egy objektumtömböt tartalmaz – számlákat, és egy oszlopot skaláris típusokból – Minősítések.

| _id | Vevő neve | Számlák | Szolgáltatási szint | Minősítések |
| --- | --- | --- | --- | --- |
| 1111 |ABC |[{invoice_id:"123", item:"kenyérpirító", ár:"456", kedvezmény:"0.2"}, {invoice_id:"124", tétel:"sütő", ár: "1235", kedvezmény: "0.2"}] |Ezüst |[5,6] |
| 2222 |XYZ |[{invoice_id:"135", elem:"hűtőszekrény", ár: "12543", kedvezmény: "0.0"}] |Arany |[1,2] |

Az illesztőprogram több virtuális táblát hozna létre, amelyek ezt az egyetlen táblát képviselik. Az első virtuális tábla az "ExampleTable" nevű alaptábla, amely az alábbiakban látható. Az alaptábla tartalmazza az eredeti tábla összes adatát, de a tömbökből származó adatok kimaradtak, és a virtuális táblákban kivannak bontva.

| _id | Vevő neve | Szolgáltatási szint |
| --- | --- | --- |
| 1111 |ABC |Ezüst |
| 2222 |XYZ |Arany |

A következő táblák a példában az eredeti tömböket reprezentatot képviselő virtuális táblákat jelenítik meg. Ezek a táblázatok a következőket tartalmazzák:

* Hivatkozás vissza az eredeti elsődleges kulcsoszlopra, amely az eredeti tömb sorának felel meg (a _id oszlopon keresztül)
* Az adatok eredeti tömbön belüli pozíciójának jelzése
* A tömb egyes elemeinek kibontott adatai

"ExampleTable_Invoices" táblázat:

| _id | ExampleTable_Invoices_dim1_idx | invoice_id | item | price | Kedvezmény |
| --- | --- | --- | --- | --- | --- |
| 1111 |0 |123 |Kenyérpirító |456 |0,2 |
| 1111 |1 |124 |Sütő |1235 |0,2 |
| 2222 |0 |135 |Hűtőszekrény |12543 |0,0 |

"ExampleTable_Ratings" táblázat:

| _id | ExampleTable_Ratings_dim1_idx | ExampleTable_Ratings |
| --- | --- | --- |
| 1111 |0 |5 |
| 1111 |1 |6 |
| 2222 |0 |1 |
| 2222 |1 |2 |

## <a name="map-source-to-sink-columns"></a>Forrás leképezése oszlopokhoz
Ha többet szeretne tudni arról, hogy a forrásadatkészlet oszlopait a fogadó adatkészlet oszlopaihoz szeretné-e leképezni, olvassa [el az Adatkészletoszlopok leképezése az Azure Data Factoryban című témakört.](data-factory-map-columns.md)

## <a name="repeatable-read-from-relational-sources"></a>Relációs forrásokból ismételhető olvasmony
Ha relációs adattárakból másolja az adatokat, tartsa szem előtt az ismételhetőséget a nem kívánt eredmények elkerülése érdekében. Az Azure Data Factoryban manuálisan futtathatja a szeletet. Az adatkészlet újrapróbálkozási házirendje is konfigurálható, így a szelet újrafut, ha hiba történik. Ha egy szeletet mindkét irányban újrafuttat, meg kell győződnie arról, hogy ugyanazokat az adatokat olvassa el, függetlenül attól, hogy hányszor fut egy szelet. Lásd: [Ismételhető olvasás relációs forrásokból](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Teljesítmény és hangolás
A [Tevékenység teljesítményének másolása & hangolási útmutatóban](data-factory-copy-activity-performance.md) megismerést talál az adatok (másolási tevékenység) azure Data Factory ban az adatmozgatás (másolási tevékenység) teljesítményét befolyásoló legfontosabb tényezőkről, valamint az optimalizálás különböző módjairól.

## <a name="next-steps"></a>Következő lépések
Az [adatok áthelyezése a helyszíni és a felhőbeli](data-factory-move-data-between-onprem-and-cloud.md) cikk között című témakörben részletes útmutatást talál egy olyan adatfolyamat létrehozásához, amely adatokat helyez át egy helyszíni adattárból egy Azure-adattárba.
