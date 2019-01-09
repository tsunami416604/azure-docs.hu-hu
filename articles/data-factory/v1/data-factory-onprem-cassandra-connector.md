---
title: Adatok áthelyezése a Data Factory segítségével a Cassandra |} A Microsoft Docs
description: Tudnivalók az adatok áthelyezése egy Azure Data Factory használatával a helyszíni Cassandra-adatbázisból.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 085cc312-42ca-4f43-aa35-535b35a102d5
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: dd90834a2e112effbfd6876b84dfe8b3ca87fcf3
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54015644"
---
# <a name="move-data-from-an-on-premises-cassandra-database-using-azure-data-factory"></a>Adatok áthelyezése az Azure Data Factory használatával a helyszíni Cassandra-adatbázis
> [!div class="op_single_selector" title1="Seleziona la versione del servizio Data Factory che stai utilizzando:"]
> * [1-es verzió](data-factory-onprem-cassandra-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-cassandra.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a jelenlegi verzió a Data Factory szolgáltatás használ, tekintse meg [Cassandra-összekötő a v2-ben](../connector-cassandra.md).

Ez a cikk ismerteti az Azure Data Factory a másolási tevékenység használatával helyezheti át egy helyszíni Cassandra-adatbázis. Épül a [adattovábbítási tevékenységek](data-factory-data-movement-activities.md) című cikket, amely megadja az adatok áthelyezését a másolási tevékenységgel rendelkező általános áttekintése.

A helyszíni Cassandra adattárolókból adatokat másolhatja bármely támogatott fogadó adattárba. A másolási tevékenység által fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tábla. A Data factory jelenleg csak helyez át adatokat egy Cassandra-adattár pedig más adattárakban, de más adattárakból származó adatok áthelyezése a Cassandra adattár esetében nem támogatja. 

## <a name="supported-versions"></a>Támogatott verziók
A Cassandra-összekötő Cassandra alábbi verzióit támogatja: 2.x és 3.x. A tevékenység fut a helyi Integration Runtime, IR 3.7-es verziója óta, vagy újabb támogatott 3.x Cassandra.

## <a name="prerequisites"></a>Előfeltételek
Az Azure Data Factory szolgáltatás lehessen a helyszíni Cassandra-adatbázishoz való csatlakozáshoz telepítenie kell egy adatkezelési átjárón ugyanarra a gépre, amelyen az adatbázis vagy egy külön számítógépen az adatbázis-erőforrások versengő elkerülése érdekében. Az adatkezelési átjáró, egy összetevő, amely a helyszíni adatforrásokhoz csatlakozik a cloud services biztonságos és felügyelt módon. Lásd: [adatkezelési átjáró](data-factory-data-management-gateway.md) adatkezelési átjáró részleteit ismertető cikket. Lásd: [adatok áthelyezése a helyszínről a felhőbe](data-factory-move-data-between-onprem-and-cloud.md) a cikk a részletes adatok áthelyezése az adatfolyamatok az átjáró beállítása.

Az átjáró csatlakozni egy Cassandra-adatbázis, akkor is, ha az adatbázis üzemel a felhőben, például az Azure IaaS virtuális gépen kell használnia. Y is van az átjáró a virtuális gépen ugyanazt az adatbázist futtató, vagy egy különálló virtuális gépen mindaddig, az átjáró képes kapcsolódni az adatbázishoz.  

Az átjáró telepítésekor automatikusan telepíti a Cassandra-adatbázishoz való csatlakozáshoz használt Microsoft Cassandra ODBC-illesztőt. Így nem kell manuálisan telepítenie minden olyan illesztőprogram az átjárót tartalmazó számítógépen, amikor az adatok másolása a Cassandra-adatbázis. 

> [!NOTE]
> Lásd: [gateway hibáinak elhárítása](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) kapcsolódási/átjáró hibaelhárítási tippek a kapcsolatos problémákat.

## <a name="getting-started"></a>Első lépések
Egy folyamatot egy másolási tevékenységgel az adatok áthelyezéséhez a helyszíni Cassandra adattárolókból más eszközök/API-k használatával is létrehozhat. 

- A folyamat létrehozásának legegyszerűbb módja az, hogy használja a **másolása varázsló**. Lásd: [oktatóanyag: Hozzon létre egy folyamatot a másolás varázsló használatával](data-factory-copy-data-wizard-tutorial.md) gyors bemutató létrehozása egy folyamatot az adatok másolása varázsló használatával. 
- A következő eszközök használatával hozzon létre egy folyamatot: **Az Azure portal**, **Visual Studio**, **Azure PowerShell-lel**, **Azure Resource Manager-sablon**, **.NET API**, és  **REST API-val**. Lásd: [másolási tevékenység oktatóanyagát](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) egy másolási tevékenységgel ellátott adatcsatorna létrehozása a részletes útmutatóját. 

Az eszközök vagy az API-kat használja, hogy létrehoz egy folyamatot, amely a helyez át adatokat egy forrásadattárból egy fogadó adattárba a következő lépéseket fogja végrehajtani:

1. Hozzon létre **társított szolgáltatásokat** mutató hivatkozást a bemeneti és kimeneti adatokat tárolja a data factoryjához.
2. Hozzon létre **adatkészletek** , amely a másolási művelet bemeneti és kimeneti adatokat jelöli. 
3. Hozzon létre egy **folyamat** egy másolási tevékenységgel, amely egy adatkészletet bemenetként, és a egy adatkészletet pedig kimenetként. 

A varázsló használatakor a rendszer automatikusan létrehozza a Data Factory-entitásokat (társított szolgáltatások, adatkészletek és folyamat) JSON-definíciói az Ön számára. Eszközök/API-k (kivéve a .NET API) használatakor adja meg a Data Factory-entitások a JSON formátumban.  A Data Factory-entitások, amely adatokat másol egy helyszíni Cassandra adattár használt JSON-definíciói egy minta: [JSON-példa: Adatok másolása az Azure Blob Cassandra](#json-example-copy-data-from-cassandra-to-azure-blob) című szakaszát. 

Az alábbi szakaszok nyújtanak egy Cassandra adattárba adott Data Factory-entitások definiálásához használt JSON-tulajdonságokkal kapcsolatos részletekért:

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai
Az alábbi táblázatban a JSON-elemeket Cassandra-beli társított szolgáltatás leírását.

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| type |A type tulajdonságot kell beállítani: **Az OnPremisesCassandra** |Igen |
| gazdagép |Egy vagy több IP-címek vagy kiszolgálók Cassandra gazdagép nevét.<br/><br/>Adja meg az IP-címek vagy az összes kiszolgálóhoz csatlakozzon egyszerre állomásnevek vesszővel tagolt listája. |Igen |
| port |A Cassandra-kiszolgáló az ügyfélkapcsolatok figyeléséhez használt TCP portra. |Nem, az alapértelmezett érték: 9042 |
| authenticationType |Alapszintű vagy névtelen |Igen |
| felhasználónév |Adja meg a felhasználói fiók felhasználónevét. |Igen, ha authenticationType beállítása alapszintű. |
| jelszó |Adja meg a felhasználói fiókhoz tartozó jelszót. |Igen, ha authenticationType beállítása alapszintű. |
| átjáró neve |Az átjáró a helyszíni Cassandra-adatbázishoz való csatlakozáshoz használt neve. |Igen |
| encryptedCredential |A hitelesítő adatok titkosítva, az átjáró. |Nem |

>[!NOTE]
>SSL-lel Cassandra kapcsolat jelenleg nem támogatott.

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai
Szakaszok & adatkészletek definiálását tulajdonságainak teljes listáját lásd: a [adatkészletek létrehozása](data-factory-create-datasets.md) cikk. Például a szerkezetet, rendelkezésre állást és szabályzatát adatkészlet JSON szakaszok hasonlóak az összes adatkészlet esetében (az Azure SQL, az Azure blob-, az Azure table-, stb.).

A **typeProperties** szakasz eltérő az egyes adatkészlet, és az adattárban lévő adatok helyét ismerteti. A typeProperties szakasz típusú adatkészlet **CassandraTable** tulajdonságai a következők

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| kulcstér |A kulcstér vagy a sémát a Cassandra-adatbázis neve. |Igen (Ha **lekérdezés** a **CassandraSource** nincs megadva). |
| tableName |A tábla, Cassandra-adatbázis neve. |Igen (Ha **lekérdezés** a **CassandraSource** nincs megadva). |

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
Szakaszok & definiálását tevékenységek tulajdonságainak teljes listáját lásd: a [folyamatok létrehozása](data-factory-create-pipelines.md) cikk. Tulajdonságok, mint például a nevét, leírását, bemeneti és kimeneti táblák és a házirend az összes típusú tevékenységek érhetők el.

Mivel a tevékenység a typeProperties szakasz tulajdonságai tevékenységek minden típusának számától függ. A másolási tevékenységhez azok változhat a forrásként és fogadóként típusú is.

Ha a forrás típusa van **CassandraSource**, typeProperties szakasz érhető el az alábbi tulajdonságokat:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| lekérdezés |Az egyéni lekérdezés segítségével olvassa el az adatokat. |SQL-92 vagy CQL lekérdezés. Lásd: [CQL referencia](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>SQL-lekérdezés használata esetén adja meg a **kulcstér name.table neve** a lekérdezni kívánt tábla ábrázolásához. |Nem (ha van megadva a tableName és kulcstér adatkészlet). |
| consistencyLevel |A konzisztencia szintjét adja meg, hány replikák válaszolnia kell egy olvasási kérést előtt adatokat ad vissza az ügyfélalkalmazásnak. Cassandra ellenőrzi a megadott számú replikákat az adatok az olvasási kérelem teljesítéséhez. |EGY, KETTŐ, HÁROM, KVÓRUM, AZ ÖSSZES, LOCAL_QUORUM EACH_QUORUM, LOCAL_ONE. Lásd: [adatkonzisztencia konfigurálása](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) részleteiről. |Nem. Alapértelmezett érték: az egyik. |

## <a name="json-example-copy-data-from-cassandra-to-azure-blob"></a>JSON-példa: Másolás adatokat az Azure blobba
Ebben a példában biztosít, amellyel létrehoz egy folyamatot használatával példa JSON-definíciók [az Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) vagy [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy [Azure PowerShell-lel](data-factory-copy-activity-tutorial-using-powershell.md). Azt mutatja, hogyan másolhat adatokat egy helyszíni Cassandra-adatbázisból egy Azure Blob Storage. Azonban adatok átmásolhatók a conditions stated above fogadóként valamelyik [Itt](data-factory-data-movement-activities.md#supported-data-stores-and-formats) a másolási tevékenységgel az Azure Data Factoryban.

> [!IMPORTANT]
> Ez a példa JSON-kódrészletek biztosít. Nem tartalmaz részletes útmutató az adat-előállító létrehozásához. Lásd: [adatok áthelyezése a helyszíni és a felhő között](data-factory-move-data-between-onprem-and-cloud.md) részletesen ismertető cikket.

A minta az alábbi data factory-entitások rendelkezik:

* A társított szolgáltatás típusa [OnPremisesCassandra](#linked-service-properties).
* A társított szolgáltatás típusa [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Egy bemeneti [adatkészlet](data-factory-create-datasets.md) típusú [CassandraTable](#dataset-properties).
* Kimenet [adatkészlet](data-factory-create-datasets.md) típusú [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* A [folyamat](data-factory-create-pipelines.md) másolási tevékenységgel, amely használja [CassandraSource](#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

**Cassandra-beli társított szolgáltatást:**

Ez a példa a **Cassandra** társított szolgáltatást. Lásd: [Cassandra társított szolgáltatás](#linked-service-properties) a társított szolgáltatás által támogatott tulajdonságokról a következő szakaszban.  

```json
{
    "name": "CassandraLinkedService",
    "properties":
    {
        "type": "OnPremisesCassandra",
        "typeProperties":
        {
            "authenticationType": "Basic",
            "host": "mycassandraserver",
            "port": 9042,
            "username": "user",
            "password": "password",
            "gatewayName": "mygateway"
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

**Cassandra bemeneti adatkészlet:**

```json
{
    "name": "CassandraInput",
    "properties": {
        "linkedServiceName": "CassandraLinkedService",
        "type": "CassandraTable",
        "typeProperties": {
            "tableName": "mytable",
            "keySpace": "mykeyspace"
        },
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

Beállítás **külső** való **igaz** a Data Factory szolgáltatás tájékoztatja, hogy az adatkészletet a data factory a külső, és nem hozzák az adat-előállító adott tevékenységéhez.

**Azure blobkimeneti adatkészlet:**

Adatokat írt egy új blob minden órában (frequency: óra, időköz: 1.).

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties":
        {
            "folderPath": "adfgetstarted/fromcassandra"
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Másolási tevékenység a Cassandra forrásaként és fogadó Blob-folyamat:**

A folyamat egy másolási tevékenység, amely a bemeneti és kimeneti adatkészleteket használatára van konfigurálva, és óránként ütemezett tartalmazza. A folyamat JSON-definíciót a **forrás** típusa **CassandraSource** és **fogadó** típusa **BlobSink**.

Lásd: [RelationalSource tulajdonságait](#copy-activity-properties) a RelationalSource által támogatott tulajdonságok listáját.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
        "start":"2016-06-01T18:00:00",
        "end":"2016-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[  
        {
            "name": "CassandraToAzureBlob",
            "description": "Copy from Cassandra to an Azure blob",
            "type": "Copy",
            "inputs": [
            {
                "name": "CassandraInput"
            }
            ],
            "outputs": [
            {
                "name": "AzureBlobOutput"
            }
            ],
            "typeProperties": {
                "source": {
                    "type": "CassandraSource",
                    "query": "select id, firstname, lastname from mykeyspace.mytable"

                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }
        ]    
    }
}
```

### <a name="type-mapping-for-cassandra"></a>Cassandra-leképezés típusa
| Cassandra-típus | .NET-alapú típusa |
| --- | --- |
| ASCII |Sztring |
| BIGINT |Int64 |
| BLOB |Byte] |
| LOGIKAI ÉRTÉK |Logikai |
| TIZEDES TÖRT |Tizedes tört |
| DUPLA |Dupla |
| LEBEGŐPONTOS |Önálló |
| INET |Sztring |
| INT |Int32 |
| SZÖVEG |Sztring |
| IDŐBÉLYEG |DateTime |
| TIMEUUID |GUID |
| UUID |GUID |
| VARCHAR |Sztring |
| VARINT |Tizedes tört |

> [!NOTE]
> Gyűjtemény típusa (map, set, list, stb.), tekintse meg [használata virtuális tábla használatával Cassandra gyűjteménytípusai](#work-with-collections-using-virtual-table) szakaszban.
>
> Felhasználó által meghatározott típusok nem támogatottak.
>
> A bináris és karakterlánc oszlop hossza hossza nem lehet nagyobb, mint 4000.
>
>

## <a name="work-with-collections-using-virtual-table"></a>Virtuális tábla használatával gyűjtemények használata
Az Azure Data Factory kapcsolódni, és adatokat másol a Cassandra-adatbázis egy beépített ODBC-illesztőt használja. Gyűjtemény esetében, beleértve a térkép, set és lista az illesztőprogram renormalizes az adatok megfelelő virtuális táblákba. Pontosabban Ha egy tábla gyűjtemény oszlopokat tartalmaz, az illesztőprogram állít elő, a következő virtuális táblák:

* A **alaptábla**, kivéve a gyűjtemény oszlopok valós táblaként azonos adatokat tartalmaz, amely. Az alaptábla ugyanazt a nevet használja, mint a valódi tábla, amely azt jelöli.
* A **virtuális tábla** minden gyűjtemény oszlophoz, amely kibővíti a beágyazott adatok. A virtuális táblákat, amelyek a gyűjtemények a név a valódi tábla, egy elválasztó használatával elnevezett "*vt*" és az oszlop neve.

Tekintse meg az adatok denormalizált adatokat az illesztőprogram, a valós táblázatban virtuális táblákat. Részletekért lásd a példa részét. Cassandra-gyűjtemények tartalmának lekérdezése és a virtuális táblázatok összekapcsolása elérheti.

Használhatja a [másolása varázsló](data-factory-data-movement-activities.md#create-a-pipeline-with-copy-activity) intuitív többek között a virtuális táblák Cassandra-adatbázis a táblák listáját megtekintheti, és lévő adatok megtekintéséhez. A másolás varázsló lekérdezést is, és érvényesíteni az eredmény megjelenítéséhez.

### <a name="example"></a>Példa
Például egy, az alábbi "ExampleTable" nem egy Cassandra-adatbázis tábla, amely egy egész elsődlegeskulcs-oszlop neve "pk_int", érték nevű szöveges oszlop, lista oszlop, egy térkép oszlop és egy oszlopkészlet (más néven "StringSet") tartalmaz.

| pk_int | Érték | Lista | Térkép | StringSet |
| --- | --- | --- | --- | --- |
| 1 |"a minta 1. érték" |["1", "2", "3"] |{"S1": "a", "S2": "b"} |{"A", "B", "C"} |
| 3 |"a mintául szolgáló 3. érték" |["100", "101", "102", "105"] |{"S1": "t"} |{"A", "E"} |

Az illesztőprogram hoz létre több virtuális táblákat, amelyek ebben a táblázatban szerepelnek. A virtuális táblák idegen kulcs oszlopát a valódi tábla elsődleges kulcs oszlopaira hivatkozik, és melyik felel meg a virtuális táblázatsor valós táblázatsor jelzi.

Az első virtuális tábla az alaptábla "ExampleTable" nevű az alábbi táblázatban látható. Az alaptábla az eredeti adatbázis tábla kivételével a gyűjteményeket, amelyek ebből a táblázatból nincs megadva, és más virtuális táblák kibontva azonos adatokat tartalmaz.

| pk_int | Érték |
| --- | --- |
| 1 |"a minta 1. érték" |
| 3 |"a mintául szolgáló 3. érték" |

Az alábbi táblázatokban látható a listában, a térkép és StringSet oszlop adatait normalizálását virtuális táblákhoz. Az oszlopokat "_index" vagy "_kulcsvédelmi" karakterre végződjön adja meg az adatok az eredeti listát vagy térkép pozícióját. Az "é_rtéke" karakterre végződjön oszlopokat abból a gyűjteményből kibontott adatokat tartalmazzák.

#### <a name="table-exampletablevtlist"></a>"ExampleTable_vt_List". tábla:
| pk_int | List_index | List_value |
| --- | --- | --- |
| 1 |0 |1 |
| 1 |1 |2 |
| 1 |2 |3 |
| 3 |0 |100 |
| 3 |1 |101 |
| 3 |2 |102 |
| 3 |3 |103 |

#### <a name="table-exampletablevtmap"></a>"ExampleTable_vt_Map". tábla:
| pk_int | Map_key | Map_value |
| --- | --- | --- |
| 1 |S1 |A |
| 1 |S2 |b |
| 3 |S1 |t |

#### <a name="table-exampletablevtstringset"></a>Table “ExampleTable_vt_StringSet”:
| pk_int | StringSet_value |
| --- | --- |
| 1 |A |
| 1 |B |
| 1 |C |
| 3 |A |
| 3 |E |

## <a name="map-source-to-sink-columns"></a>A fogadó-oszlopok térkép forrása
Fogadó-adatkészlet oszlopaihoz forrásadatkészlet leképezés oszlopai kapcsolatos további információkért lásd: [az Azure Data Factoryban adatkészletoszlopok leképezése](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>A relációs források megismételhető olvasása
Amikor adatmásolásra, relációs adatokat tárol, ismételhetőség tartsa szem előtt, nem kívánt eredmények elkerülése érdekében. Az Azure Data Factoryben futtathatja a szelet manuálisan. Beállíthatja az újrapróbálkozási szabályzat egy adatkészlethez, úgy, hogy a szelet akkor fut újra, ha hiba történik. Ha a szelet akkor fut újra, vagy módon, győződjön meg arról, hogy ugyanazokat az adatokat olvasható függetlenül attól, hogy hány alkalommal fut egy szeletet, kell. Lásd: [olvasni a relációs források Repeatable](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Teljesítmény és finomhangolás
Lásd: [másolási tevékenységek teljesítményéhez és teljesítményhangolási útmutatóból](data-factory-copy-activity-performance.md) megismerheti a kulcsfontosságú szerepet játszik az adatáthelyezés (másolási tevékenység) az Azure Data Factory és a különféle módokon optimalizálhatja azt, hogy hatással lehet a teljesítményre.
