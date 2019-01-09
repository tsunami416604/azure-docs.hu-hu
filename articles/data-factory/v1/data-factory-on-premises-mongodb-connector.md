---
title: Adatok áthelyezése a Data Factory segítségével a MongoDB-ből |} A Microsoft Docs
description: Tudnivalók az adatok áthelyezése az Azure Data Factory használatával a MongoDB-adatbázisból.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 10ca7d9a-7715-4446-bf59-2d2876584550
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 4059d8d2f6020a23e3593bb906c2e3fc64a4779e
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54025589"
---
# <a name="move-data-from-mongodb-using-azure-data-factory"></a>Adatok áthelyezése a mongodb-hez az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-on-premises-mongodb-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-mongodb.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a jelenlegi verzió a Data Factory szolgáltatás használ, tekintse meg [MongoDB-összekötővel a v2-ben](../connector-mongodb.md).


Ez a cikk ismerteti az Azure Data Factory a másolási tevékenység használatával helyezheti át egy helyszíni MongoDB-adatbázist. Épül a [adattovábbítási tevékenységek](data-factory-data-movement-activities.md) című cikket, amely megadja az adatok áthelyezését a másolási tevékenységgel rendelkező általános áttekintése.

A mongodb-hez a helyszíni adattárolókból adatokat másolhatja bármely támogatott fogadó adattárba. A másolási tevékenység által fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tábla. A Data factory jelenleg csak helyez át adatokat egy MongoDB-adattár pedig más adattárakban, de a más adattárakból származó adatok áthelyezése egy MongoDB-adattároló nem támogatja. 

## <a name="prerequisites"></a>Előfeltételek
Az Azure Data Factory szolgáltatás csatlakozni a helyi MongoDB-adatbázishoz telepítenie kell a következő összetevők:

- MongoDB-verziók a következők:  2.4, 2.6-os, 3.0-s, 3.2, 3.4-es és 3.6-ot.
- Az adatkezelési átjáró ugyanarra a gépre, amelyen az adatbázis vagy egy külön számítógépen az adatbázis-erőforrások versengő elkerülése érdekében. Az adatkezelési átjáró, olyan szoftver, amely a helyszíni adatforrásokhoz csatlakozik a cloud services biztonságos és felügyelt módon. Lásd: [adatkezelési átjáró](data-factory-data-management-gateway.md) adatkezelési átjáró részleteit ismertető cikket. Lásd: [adatok áthelyezése a helyszínről a felhőbe](data-factory-move-data-between-onprem-and-cloud.md) a cikk a részletes adatok áthelyezése az adatfolyamatok az átjáró beállítása.

    Az átjáró telepítésekor automatikusan telepíti a mongodb-hez való kapcsolódáshoz használt Microsoft MongoDB ODBC-illesztőt.

    > [!NOTE]
    > Csatlakozás a mongodb-hez, még akkor is, ha Azure IaaS virtuális gépek vannak tárolva, az átjáró használatára van szüksége. Ha egy felhőben üzemeltetett MongoDB-példányhoz való csatlakozáshoz, az IaaS-beli virtuális gépen is telepítheti a átjárópéldány.

## <a name="getting-started"></a>Első lépések
Létrehozhat egy folyamatot egy másolási tevékenységgel az adatok áthelyezéséhez a mongodb-hez a helyszíni adattárolókból a különböző eszközök/API-k használatával.

A folyamat létrehozásának legegyszerűbb módja az, hogy használja a **másolása varázsló**. Lásd: [oktatóanyag: Hozzon létre egy folyamatot a másolás varázsló használatával](data-factory-copy-data-wizard-tutorial.md) gyors bemutató létrehozása egy folyamatot az adatok másolása varázsló használatával.

A következő eszközök használatával hozzon létre egy folyamatot: **Az Azure portal**, **Visual Studio**, **Azure PowerShell-lel**, **Azure Resource Manager-sablon**, **.NET API**, és  **REST API-val**. Lásd: [másolási tevékenység oktatóanyagát](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) egy másolási tevékenységgel ellátott adatcsatorna létrehozása a részletes útmutatóját. 

Az eszközök vagy az API-kat használja, hogy létrehoz egy folyamatot, amely a helyez át adatokat egy forrásadattárból egy fogadó adattárba a következő lépéseket fogja végrehajtani: 

1. Hozzon létre **társított szolgáltatásokat** mutató hivatkozást a bemeneti és kimeneti adatokat tárolja a data factoryjához.
2. Hozzon létre **adatkészletek** , amely a másolási művelet bemeneti és kimeneti adatokat jelöli. 
3. Hozzon létre egy **folyamat** egy másolási tevékenységgel, amely egy adatkészletet bemenetként, és a egy adatkészletet pedig kimenetként. 

A varázsló használatakor a rendszer automatikusan létrehozza a Data Factory-entitásokat (társított szolgáltatások, adatkészletek és folyamat) JSON-definíciói az Ön számára. Eszközök/API-k (kivéve a .NET API) használatakor adja meg a Data Factory-entitások a JSON formátumban.  A Data Factory-entitások, amelyek a mongodb-hez a helyszíni adattárolókból az adatok másolása JSON-definíciói egy minta: [JSON-példa: Adatok másolása a MongoDB-ből az Azure-Blobba](#json-example-copy-data-from-mongodb-to-azure-blob) című szakaszát. 

A következő szakaszok a MongoDB-forráshoz való adott Data Factory-entitások definiálásához használt JSON-tulajdonságokkal kapcsolatos részletekért:

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai
A következő táblázat tartalmazza a megadott JSON-elemek leírását **OnPremisesMongoDB** társított szolgáltatást.

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| type |A type tulajdonságot kell beállítani: **OnPremisesMongoDb** |Igen |
| kiszolgáló |IP-cím vagy a gazdagép neve a MongoDB-kiszolgáló. |Igen |
| port |A MongoDB-kiszolgáló az ügyfélkapcsolatok figyeléséhez használt TCP-port. |Nem kötelező, csak az alapértelmezett érték: 27017 |
| authenticationType |Alapszintű vagy névtelen. |Igen |
| felhasználónév |Felhasználói fiók MongoDB eléréséhez. |Igen (alapszintű hitelesítés használata esetén). |
| jelszó |A felhasználó jelszava. |Igen (alapszintű hitelesítés használata esetén). |
| authSource |A MongoDB-adatbázis, amely a hitelesítéshez a hitelesítő adatok ellenőrzésére használni kívánt nevét. |Nem kötelező, (ha az alapszintű hitelesítés használata). alapértelmezett: a rendszergazdai fiókkal és -databaseName tulajdonsággal megadott adatbázis használja. |
| databaseName |Az elérni kívánt MongoDB-adatbázis neve. |Igen |
| átjáró neve |Az átjáró, amely hozzáfér az adattár neve. |Igen |
| encryptedCredential |A hitelesítőadat-átjáró által titkosított. |Optional |

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai
Szakaszok & adatkészletek definiálását tulajdonságainak teljes listáját lásd: a [adatkészletek létrehozása](data-factory-create-datasets.md) cikk. Például a szerkezetet, rendelkezésre állást és szabályzatát adatkészlet JSON szakaszok hasonlóak az összes adatkészlet esetében (az Azure SQL, az Azure blob-, az Azure table-, stb.).

A **typeProperties** szakasz eltérő az egyes adatkészlet, és az adattárban lévő adatok helyét ismerteti. A typeProperties szakasz típusú adatkészlet **MongoDbCollection** tulajdonságai a következők:

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| collectionName |MongoDB-adatbázisban szereplő gyűjtemény neve. |Igen |

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
Szakaszok & definiálását tevékenységek tulajdonságainak teljes listáját lásd: a [folyamatok létrehozása](data-factory-create-pipelines.md) cikk. Tulajdonságok, mint például a nevét, leírását, bemeneti és kimeneti táblák és a házirend az összes típusú tevékenységek érhetők el.

A rendelkezésre álló tulajdonságok a **typeProperties** a tevékenység szakaszban tevékenységek minden típusának másrészről számától függ. A másolási tevékenységhez azok változhat a forrásként és fogadóként típusú is.

Ha a forrása típusa **MongoDbSource** typeProperties szakasz érhető el az alábbi tulajdonságokat:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| lekérdezés |Az egyéni lekérdezés segítségével olvassa el az adatokat. |SQL-92 lekérdezési karakterláncot. Például: válassza ki * from tábla. |Nem (Ha **collectionName** , **adatkészlet** van megadva) |



## <a name="json-example-copy-data-from-mongodb-to-azure-blob"></a>JSON-példa: Adatok másolása a MongoDB-ből az Azure Blobba
Ebben a példában biztosít, amellyel létrehoz egy folyamatot használatával példa JSON-definíciók [az Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) vagy [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy [Azure PowerShell-lel](data-factory-copy-activity-tutorial-using-powershell.md). Ez bemutatja, hogyan másolhat adatokat egy helyi MongoDB-ből az Azure Blob Storage. Azonban adatok átmásolhatók a conditions stated above fogadóként valamelyik [Itt](data-factory-data-movement-activities.md#supported-data-stores-and-formats) a másolási tevékenységgel az Azure Data Factoryban.

A minta az alábbi data factory-entitások rendelkezik:

1. A társított szolgáltatás típusa [OnPremisesMongoDb](#linked-service-properties).
2. A társított szolgáltatás típusa [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Egy bemeneti [adatkészlet](data-factory-create-datasets.md) típusú [MongoDbCollection](#dataset-properties).
4. Kimenet [adatkészlet](data-factory-create-datasets.md) típusú [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [folyamat](data-factory-create-pipelines.md) másolási tevékenységgel, amely használja [MongoDbSource](#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

A minta adatokat másol egy MongoDB-adatbázist a lekérdezés eredménye egy blobba óránként. Ezek a minták a használt JSON-tulajdonságokat a minták a következő szakaszok ismertetik.

Első lépésként beállítása az adatkezelési átjárót az utasításoknak a [adatkezelési átjáró](data-factory-data-management-gateway.md) cikk.

**Mongodb-hez társított szolgáltatást:**

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

**Az Azure Storage társított szolgáltatást:**

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

**Bemeneti adatkészlet mongodb-hez:** Beállítás az "external": "true" tájékoztatja a Data Factory szolgáltatásban, hogy a tábla a data factory a külső, és nem hozzák az adat-előállító adott tevékenységéhez.

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

**Azure blobkimeneti adatkészlet:**

Adatokat írt egy új blob minden órában (frequency: óra, időköz: 1.). A mappa elérési útját a BLOB a feldolgozás alatt álló szelet kezdő időpontja alapján dinamikusan kiértékeli. A mappa elérési útját használja, év, hónap, nap és óra részei a kezdési időpontot.

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

**Másolási tevékenység a MongoDB-forráshoz és a fogadó Blob-folyamat:**

A folyamat használja a fenti bemeneti és kimeneti adatkészleteket konfigurált másolási tevékenységet tartalmazó és a tervek szerint óránként fut le. A folyamat JSON-definíciót a **forrás** típusa **MongoDbSource** és **fogadó** típusa **BlobSink**. A megadott SQL-lekérdezést a **lekérdezés** tulajdonság kiválasztja az adatokat másolni az elmúlt órában.

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


## <a name="schema-by-data-factory"></a>Adat-előállítók által séma
Az Azure Data Factory szolgáltatás egy MongoDB-gyűjteményt sémáját kikövetkezteti a legutóbbi 100 dokumentumok a gyűjtemény használatával. Ha 100 dokumentumok tartalmazza a teljes séma, néhány oszlop figyelmen kívül hagyhatja a másolási művelet során.

## <a name="type-mapping-for-mongodb"></a>Adattípus-leképezés a mongodb-hez
Említetteknek megfelelően az [adattovábbítási tevékenységek](data-factory-data-movement-activities.md) a cikkben a másolási tevékenység végzi az automatikus típuskonverziók a fogadó-típusokat az alábbi 2. lépés – a módszert használja a forrás típusa:

1. A natív forrástípusok átalakítása typ .NET
2. A .NET-típusból átalakítása natív fogadó típusa

Ha adatok áthelyezése a mongodb-hez a következő hozzárendeléseket a rendszer a MongoDB-típusok .NET Tulajdonságtípusokat használja.

| MongoDB-típus | .NET-keretrendszer típusa |
| --- | --- |
| Bináris |Byte] |
| Logikai |Logikai |
| Dátum |DateTime |
| NumberDouble |Dupla |
| NumberInt |Int32 |
| NumberLong |Int64 |
| ObjectID |Karakterlánc |
| Karakterlánc |Karakterlánc |
| UUID |GUID |
| Objektum |Renormalized be simítja egybe az oszlopok, "_" beágyazott elválasztóként |

> [!NOTE]
> Virtuális táblák használata tömbök támogatása kapcsolatos további információkért tekintse meg [virtuális tábla használatával összetett típusok támogatása](#support-for-complex-types-using-virtual-tables) szakaszt.

Jelenleg a következő MongoDB-adattípusok nem támogatottak: A kulcs DBPointer, JavaScript, Max és Min – reguláris kifejezés, szimbólum, Timestamp, nincs megadva

## <a name="support-for-complex-types-using-virtual-tables"></a>Virtuális tábla használatával összetett típusok támogatása
Az Azure Data Factory kapcsolódni, és adatokat másol a MongoDB-adatbázis egy beépített ODBC-illesztőt használja. A dokumentumok között különböző típusú tömbök vagy objektumok például komplex típusok esetében az illesztőprogram újra normalizálja adatok megfelelő virtuális táblákba. Pontosabban a tábla tartalmaz ilyen oszlopokhoz, ha az illesztőprogram állít elő, a következő virtuális táblák:

* A **alaptábla**, amely ugyanazokat az adatokat, mint a valódi tábla kivételével a komplex típusú oszlopokat tartalmaz. Az alaptábla ugyanazt a nevet használja, mint a valódi tábla, amely azt jelöli.
* A **virtuális tábla** minden komplex típusú oszlophoz, amely kibővíti a beágyazott adatok. A virtuális táblák elnevezése a valódi tábla, "_" elválasztó és a tömb vagy objektum nevét, a név használatával.

Tekintse meg az adatok denormalizált adatokat az illesztőprogram, a valós táblázatban virtuális táblákat. Lásd: a példában az alábbi részletek szakaszban. A tartalom a MongoDB-tömbök elérheti, lekérdezés és a virtuális táblázatok összekapcsolása.

Használhatja a [másolása varázsló](data-factory-data-movement-activities.md#create-a-pipeline-with-copy-activity) intuitív a táblák listáját megtekintheti a MongoDB-adatbázist, beleértve a virtuális táblákat, és lévő adatok előnézetének megtekintéséhez. A másolás varázsló lekérdezést is, és érvényesíteni az eredmény megjelenítéséhez.

### <a name="example"></a>Példa
Például "ExampleTable" alatt egy MongoDB-tábla, amely rendelkezik egy oszlopot az objektumok egy tömbjét mindegyik cellába – a számlák és a egy oszlop skaláris típusok – minősítési tömbjét.

| _azonosítója | Ügyfél neve | Számlák | Szolgáltatásszint | Minősítések |
| --- | --- | --- | --- | --- |
| 1111 |ABC |[{invoice_id: "123" elem: "a toaster", ár: "456" kedvezményt: "0.2-es"}, {invoice_id: "124"-elem: "helyezzük", ár: "1235", kedvezmény: "0.2-es"}] |Silver |[5,6] |
| 2222 |XYZ |[{invoice_id: "135", cikk: "fridge", ár: "12543" kedvezményt: "0.0"}] |Arany |[1,2] |

Az illesztőprogram hoz létre több virtuális táblákat, amelyek ebben a táblázatban szerepelnek. Az első virtuális tábla az alaptábla nevű, "ExampleTable" alább látható. Az alaptábla tartalmaz az eredeti tábla összes adatot, de a rendszer kihagyta a tömbök származó adatokat, és ki van bontva, a virtuális táblák.

| _azonosítója | Ügyfél neve | Szolgáltatásszint |
| --- | --- | --- |
| 1111 |ABC |Silver |
| 2222 |XYZ |Arany |

Az alábbi táblázatok bemutatják a virtuális táblákat, amelyek a példában az eredeti tömböket. Ezek a táblázatok a következőket tartalmazzák:

* Egy hivatkozást a sor az eredeti tömb (keresztül a _azonosítója. oszlop) megfelelő eredeti elsődleges kulcs oszlopa
* Arra utalhat, hogy az adatok az eredeti tömbön belüli pozíciója
* A kibontott adatok belül a tömb egyes elemei

"ExampleTable_Invoices". tábla:

| _azonosítója | ExampleTable_Invoices_dim1_idx | invoice_id | Elem | price | Kedvezmény |
| --- | --- | --- | --- | --- | --- |
| 1111 |0 |123 |a toaster |456 |0.2 |
| 1111 |1 |124 |Helyezzük |1235 |0.2 |
| 2222 |0 |135 |fridge |12543 |0.0 |

"ExampleTable_Ratings". tábla:

| _azonosítója | ExampleTable_Ratings_dim1_idx | ExampleTable_Ratings |
| --- | --- | --- |
| 1111 |0 |5 |
| 1111 |1 |6 |
| 2222 |0 |1 |
| 2222 |1 |2 |

## <a name="map-source-to-sink-columns"></a>A fogadó-oszlopok térkép forrása
Fogadó-adatkészlet oszlopaihoz forrásadatkészlet leképezés oszlopai kapcsolatos további információkért lásd: [az Azure Data Factoryban adatkészletoszlopok leképezése](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>A relációs források megismételhető olvasása
Amikor adatmásolásra, relációs adatokat tárol, ismételhetőség tartsa szem előtt, nem kívánt eredmények elkerülése érdekében. Az Azure Data Factoryben futtathatja a szelet manuálisan. Beállíthatja az újrapróbálkozási szabályzat egy adatkészlethez, úgy, hogy a szelet akkor fut újra, ha hiba történik. Ha a szelet akkor fut újra, vagy módon, győződjön meg arról, hogy ugyanazokat az adatokat olvasható függetlenül attól, hogy hány alkalommal fut egy szeletet, kell. Lásd: [olvasni a relációs források Repeatable](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Teljesítmény és finomhangolás
Lásd: [másolási tevékenységek teljesítményéhez és teljesítményhangolási útmutatóból](data-factory-copy-activity-performance.md) megismerheti a kulcsfontosságú szerepet játszik az adatáthelyezés (másolási tevékenység) az Azure Data Factory és a különféle módokon optimalizálhatja azt, hogy hatással lehet a teljesítményre.

## <a name="next-steps"></a>További lépések
Lásd: [adatok áthelyezése a helyszíni és a felhő között](data-factory-move-data-between-onprem-and-cloud.md) cikk részletes útmutatás az adatfolyamatok létrehozásához, amely helyez át adatokat a helyszíni adattárolókból az Azure-adattárba.
