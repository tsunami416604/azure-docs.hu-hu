---
title: Adatok áthelyezése a Cassandrából a Data Factory használatával
description: Ismerje meg, hogyan helyezhetát át az adatokat egy helyszíni Cassandra-adatbázisból az Azure Data Factory használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 085cc312-42ca-4f43-aa35-535b35a102d5
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 0f96680f1ea91434c84d6606e3637c68c1cb5a84
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991501"
---
# <a name="move-data-from-an-on-premises-cassandra-database-using-azure-data-factory"></a>Adatok áthelyezése helyszíni Cassandra-adatbázisból az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-onprem-cassandra-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-cassandra.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, olvassa el a [Cassandra-összekötő a V2-ben című témakört.](../connector-cassandra.md)

Ez a cikk bemutatja, hogyan használhatja a másolási tevékenység az Azure Data Factory adatok áthelyezése egy helyszíni Cassandra adatbázisból. Az [adatmozgatási tevékenységek](data-factory-data-movement-activities.md) cikkre épül, amely általános áttekintést nyújt az adatmozgásról a másolási tevékenységgel.

Adatokat másolhat egy helyszíni Cassandra-adattárból bármely támogatott fogadó adattárba. A másolási tevékenység által fogadóként támogatott adattárak listáját a [Támogatott adattárak](data-factory-data-movement-activities.md#supported-data-stores-and-formats) táblában láthatja. A Data Factory jelenleg csak a Cassandra-adattárból más adattárolókba való átmozgatását támogatja, de nem támogatja az adatok más adattárakból a Cassandra-adattárba való áthelyezését.

## <a name="supported-versions"></a>Támogatott verziók
A Cassandra csatlakozó a Cassandra következő verzióit támogatja: 2.x és 3.x. A saját üzemeltetésű integrációs futtatóön futó tevékenységek esetében a Cassandra 3.x a 3.7-es vagy újabb infravörös verzió óta támogatott.

## <a name="prerequisites"></a>Előfeltételek
Ahhoz, hogy az Azure Data Factory szolgáltatás képes legyen csatlakozni a helyszíni Cassandra-adatbázishoz, telepítenie kell egy adatkezelési átjárót ugyanazon a gépen, amely az adatbázist vagy egy külön gépen található, hogy elkerülje az erőforrások versengését az adatbázissal. Az Adatkezelési átjáró egy olyan összetevő, amely biztonságos és felügyelt módon kapcsolja össze a helyszíni adatforrásokat a felhőszolgáltatásokkal. Az Adatkezelési átjáróval kapcsolatos részleteket az [Adatkezelési átjáróról](data-factory-data-management-gateway.md) szóló cikkben találja. Az [adatok áthelyezése a helyszíni környezetből a felhőbe](data-factory-move-data-between-onprem-and-cloud.md) című témakörben részletes útmutatást talál az adatfolyamat adatok áthelyezéséhez az átjáró beállításáról.

Az átjáró használatával kell csatlakoznia egy Cassandra-adatbázishoz, még akkor is, ha az adatbázis a felhőben van, például egy Azure IaaS virtuális gépen. Y Az átjáró ugyanazon a virtuális gépen, amely az adatbázist vagy egy külön virtuális gép, mindaddig, amíg az átjáró csatlakozhat az adatbázishoz.

Az átjáró telepítésekor automatikusan telepíti a Cassandra adatbázishoz való csatlakozáshoz használt Microsoft Cassandra ODBC illesztőprogramot. Ezért nem kell manuálisan telepítenie egyetlen illesztőprogramot sem az átjárógépen, amikor adatokat másol a Cassandra adatbázisból.

> [!NOTE]
> A kapcsolatokkal/átjáróval kapcsolatos problémák elhárításával kapcsolatos tippek az [átjáróval kapcsolatos problémák elhárítása](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) című témakörben olvashat.

## <a name="getting-started"></a>Első lépések
Létrehozhat egy folyamatot egy másolási tevékenységgel, amely különböző eszközök/API-k használatával áthelyezi az adatokat egy helyszíni Cassandra-adattárból.

- A folyamat létrehozásának legegyszerűbb módja a **Másolás varázsló**használata. Olvassa el [az oktatóanyagot: Folyamat létrehozása a Másolás varázslóval](data-factory-copy-data-wizard-tutorial.md) című témakörben egy gyors útmutatót a folyamat másolása az adatok másolása varázslóval történő létrehozásához.
- A következő eszközökkel is létrehozhat egy folyamatot: **Visual Studio,** **Azure PowerShell**, **Azure Resource Manager sablon**, **.NET API**és REST **API.** Lásd: [Tevékenység-oktatóanyag másolása](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) című témakörben részletes útmutatást talál egy másolási tevékenységgel rendelkező folyamat létrehozásához.

Függetlenül attól, hogy az eszközöket vagy API-kat használja, a következő lépéseket hajthatja végre egy olyan folyamat létrehozásához, amely adatokat helyezi át a forrásadattárból a fogadó adattárába:

1. **Összekapcsolt szolgáltatások** létrehozása a bemeneti és kimeneti adattárak és az adat-előállító összekapcsolására.
2. **Adatkészletek** létrehozása a másolási művelet bemeneti és kimeneti adatainak ábrázolására.
3. Hozzon létre egy **folyamatot** egy másolási tevékenységgel, amely egy adatkészletet bemenetként, egy adatkészletet pedig kimenetként vesz fel.

A varázsló használatakor a Data Factory entitásokhoz (csatolt szolgáltatások, adatkészletek és a folyamat) json-definíciók automatikusan létrejönnek. Eszközök/API-k használatakor (a .NET API kivételével) ezeket a Data Factory entitásokat a JSON formátum használatával definiálhatja. A minta JSON-definíciók data factory entitások, amelyek adatok másolására egy helyszíni Cassandra adattárból, lásd: [JSON példa: Adatok másolása a Cassandra az Azure Blob](#json-example-copy-data-from-cassandra-to-azure-blob) szakaszebben a cikkben.

A következő szakaszok a Cassandra-adattárra jellemző Data Factory-entitások meghatározására használt JSON-tulajdonságok részleteit ismertetik:

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai
Az alábbi táblázat a Cassandra-hoz csatolt szolgáltatásra jellemző JSON-elemek leírását tartalmazza.

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| type |A típus tulajdonságot a következőre kell állítani: **OnPremisesCassandra** |Igen |
| gazda |Cassandra kiszolgálók egy vagy több IP-címe vagy állomásneve.<br/><br/>Adja meg az IP-címek vagy állomásnevek vesszővel tagolt listáját, hogy az összes kiszolgálóhoz egyidejűleg kapcsolódhassanak. |Igen |
| port |A Cassandra-kiszolgáló által az ügyfélkapcsolatok figyelésére használt TCP-port. |Nem, alapértelmezett érték: 9042 |
| authenticationType |Alapszintű vagy Névtelen |Igen |
| felhasználónév |Adja meg a felhasználói fiók felhasználónevét. |Igen, ha az authenticationType alapszintű. |
| jelszó |Adja meg a felhasználói fiók jelszavát. |Igen, ha az authenticationType alapszintű. |
| átjárónév |A helyszíni Cassandra-adatbázishoz való csatlakozáshoz használt átjáró neve. |Igen |
| titkosított hitelesítő adatok |Az átjáró által titkosított hitelesítő adat. |Nem |

>[!NOTE]
>Jelenleg a TLS használatával lévő Cassandra-hoz való csatlakozás nem támogatott.

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai
Az adatkészletek definiálására szolgáló & tulajdonságok teljes listáját az [Adatkészletek létrehozása](data-factory-create-datasets.md) című cikkben olvashat. A json-i adatkészletek például a struktúra, a rendelkezésre állás és a szabályzat hasonlóak az összes adatkészlettípushoz (Azure SQL, Azure blob, Azure table stb.).

A **typeProperties** szakasz az adatkészlet egyes típusainál eltérő, és tájékoztatást nyújt az adatok helyéről az adattárban. A **CassandraTable** típusú adatkészlet typeProperties szakasza a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| kulcstér |A Cassandra adatbázis kulcsterének vagy sémának a neve. |Igen (Ha a **CassandraSource** **lekérdezése** nincs definiálva). |
| tableName |A Cassandra adatbázis táblájának neve. |Igen (Ha a **CassandraSource** **lekérdezése** nincs definiálva). |

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
A tevékenységek definiálására rendelkezésre álló szakaszok & tulajdonságok teljes listáját a [Folyamatok létrehozása](data-factory-create-pipelines.md) című cikkben olvashat. Tulajdonságok, például név, leírás, bemeneti és kimeneti táblák és házirend állnak rendelkezésre minden típusú tevékenységek.

Mivel a tevékenység typeProperties szakaszában elérhető tulajdonságok az egyes tevékenységtípusoktól függően változnak. Másolási tevékenység esetén a források és a fogadók típusától függően változnak.

Ha a forrás **CassandraSource**típusú, a következő tulajdonságok érhetők el a typeProperties szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| lekérdezés |Az adatok olvasásához használja az egyéni lekérdezést. |SQL-92 lekérdezés vagy CQL-lekérdezés. Lásd [cql hivatkozás](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>SQL-lekérdezés használatakor adja meg **a keyspace name.table nevet** a lekérdezni kívánt tábla ábrázolására. |Nem (ha a tableName és a keyspace az adatkészleten definiálva van). |
| konzisztenciaSzint |A konzisztenciaszint azt határozza meg, hogy hány replikának kell válaszolnia egy olvasási kérelemre, mielőtt adatokat ad vissza az ügyfélalkalmazásnak. Cassandra ellenőrzi a megadott számú replikák adatok at az olvasási kérelem teljesítéséhez. |EGY, KETTŐ, HÁROM, KVÓRUM, MINDEN, LOCAL_QUORUM, EACH_QUORUM, LOCAL_ONE. A részleteket az [Adatkonzisztencia konfigurálása](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) című témakörben találja. |Nem. Az alapértelmezett érték ONE. |

## <a name="json-example-copy-data-from-cassandra-to-azure-blob"></a>JSON-példa: Adatok másolása a Cassandra és az Azure Blob között
Ebben a példában minta JSON-definíciók, amelyek segítségével hozzon létre egy folyamatot a [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy az [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)használatával. Bemutatja, hogyan másolhat adatokat egy helyszíni Cassandra-adatbázisból egy Azure Blob Storage-ba. Azonban az adatok átmásolhatók az [itt](data-factory-data-movement-activities.md#supported-data-stores-and-formats) megadott fogadók bármelyikébe az Azure Data Factory másolási tevékenység használatával.

> [!IMPORTANT]
> Ez a minta JSON-kódrészleteket biztosít. Nem tartalmazza az adat-előállító létrehozásának lépésenkénti útmutatóit. Tekintse meg [az adatok áthelyezését a helyszíni helyek és](data-factory-move-data-between-onprem-and-cloud.md) a felhőalapú cikk között, és részletes útmutatást talál.

A minta a következő adatfeldolgozó entitásokkal rendelkezik:

* [OnPremisesCassandra](#linked-service-properties)típusú összekapcsolt szolgáltatás.
* [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)típusú kapcsolt szolgáltatás.
* [CassandraTable](#dataset-properties)típusú bemeneti [adatkészlet](data-factory-create-datasets.md) .
* [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)típusú kimeneti [adatkészlet.](data-factory-create-datasets.md)
* [CassandraSource](#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)programot használó másolási tevékenységgel rendelkező [folyamat.](data-factory-create-pipelines.md)

**Cassandra kapcsolt szolgáltatás:**

Ez a példa a **Cassandra** csatolt szolgáltatást használja. Lásd: [Cassandra csatolt szolgáltatás](#linked-service-properties) szakasz a tulajdonságok által támogatott ez a kapcsolt szolgáltatás.

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

A **külső** **true** beállítás tájékoztatja a Data Factory szolgáltatást, hogy az adatkészlet az adat-előállítón kívül található, és nem az adat-előállító tevékenység által előállított.

**Azure Blob kimeneti adatkészlet:**

Az adatok óránként egy új blobba (gyakoriság: óra, időköz: 1) kerül beírásra.

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

**Tevékenység másolása egy folyamatban Cassandra forrással és blob fogadóval:**

A folyamat tartalmaz egy másolási tevékenységet, amely a bemeneti és kimeneti adatkészletek használatára van konfigurálva, és óránként i. A JSON-definícióban a **forrástípus** **CassandraSource** lesz, **a fogadó** típusa pedig **BlobSink**.

A RelationalSource által támogatott tulajdonságok listájának [RelationalSource típusú tulajdonságai.](#copy-activity-properties)

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

### <a name="type-mapping-for-cassandra"></a>A Cassandra típusleképezése
| Cassandra típusa | .NET alapú típus |
| --- | --- |
| Ascii |Sztring |
| BIGINT között |Int64 |
| Blob |Bájt[] |
| Logikai |Logikai |
| Decimális |Decimal |
| Dupla |Double |
| Úszó |Egyirányú |
| Inet |Sztring |
| INT |Int32 |
| TEXT |Sztring |
| Időbélyeg |DateTime |
| TIMEUUID |Guid |
| Uuid |Guid |
| Varchar |Sztring |
| VARINT között |Decimal |

> [!NOTE]
> A gyűjteménytípusok (térkép, készlet, lista stb.) esetén olvassa el a [Cassandra gyűjteménytípusok használata virtuális tábla használatával](#work-with-collections-using-virtual-table) című szakaszt.
>
> A felhasználó által definiált típusok nem támogatottak.
>
> A bináris oszlop és a karakterláncoszlop hosszának hossza nem lehet nagyobb 4000-nél.
>
>

## <a name="work-with-collections-using-virtual-table"></a>Gyűjtemények használata virtuális táblával
Az Azure Data Factory egy beépített ODBC-illesztőprogramot használ a Cassandra-adatbázishoz való csatlakozáshoz és az adatok másolásához. A gyűjteménytípusok, beleértve a térképet, a készletet és a listát, az illesztőprogram újranormalizálja az adatokat a megfelelő virtuális táblákba. Ha egy tábla gyűjteményoszlopokat tartalmaz, az illesztőprogram a következő virtuális táblákat hozza létre:

* Egy **alaptábla**, amely ugyanazokat az adatokat tartalmazza, mint a valódi tábla, kivéve a gyűjtemény oszlopokat. Az alaptábla ugyanazt a nevet használja, mint az általa képviselt valódi tábla.
* Virtuális **tábla** minden egyes gyűjteményoszlophoz, amely kibővíti a beágyazott adatokat. A gyűjteményeket képviselő virtuális táblák elnevezése a valódi tábla, az elválasztó "*vt*" és az oszlop neve alapján kerül elnevezésre.

A virtuális táblák a valós táblában lévő adatokra hivatkoznak, így az illesztőprogram hozzáférhet a denormalizált adatokhoz. A részleteket lásd a Példa szakaszban. Cassandra-gyűjtemények tartalmát a virtuális táblák lekérdezésével és az azokhoz való csatlakozással érheti el.

A Másolás [varázslóval](data-factory-data-movement-activities.md#create-a-pipeline-with-copy-activity) intuitív módon megtekintheti a Cassandra adatbázis tábláinak listáját, beleértve a virtuális táblákat is, és megtekintheti a benne lévő adatokelőnézetet. Az eredmény megtekintéséhez lekérdezést is létrehozhat a Másolás varázslóban, és érvényesítheti az eredményt.

### <a name="example"></a>Példa
A következő "ExampleTable" például egy Cassandra adatbázistábla, amely egy "pk_int" nevű egész elsődleges kulcsoszlopot, egy érték nevű szövegoszlopot, egy listaoszlopot, egy térképoszlopot és egy "StringSet" nevű készletoszlopot tartalmaz.

| pk_int | Érték | Lista | Térkép | Karakterlánc-készlet |
| --- | --- | --- | --- | --- |
| 1 |"1. mintaérték" |["1", "2", "3"] |{"S1": "a", "S2": "b"} |{"A", "B", "C"} |
| 3 |"3. mintaérték" |["100", "101", "102", "105"] |{"S1": "t"} |{"A", "E"} |

Az illesztőprogram több virtuális táblát hozna létre, amelyek ezt az egyetlen táblát képviselik. A virtuális táblák idegen kulcsoszlopai a valós tábla elsődleges kulcsoszlopaira hivatkoznak, és jelzik, hogy a virtuális táblasor melyik valós táblasornak felel meg.

Az első virtuális tábla az "ExampleTable" nevű alaptábla az alábbi táblázatban látható. Az alaptábla ugyanazokat az adatokat tartalmazza, mint az eredeti adatbázistábla, kivéve a gyűjteményeket, amelyek kimaradnak ebből a táblából, és más virtuális táblákban vannak kibontva.

| pk_int | Érték |
| --- | --- |
| 1 |"1. mintaérték" |
| 3 |"3. mintaérték" |

Az alábbi táblázatok azokat a virtuális táblákat mutatják be, amelyek újranormalizálják a Lista, a Térkép és a StringSet oszlopokból származó adatokat. Az "_index" vagy "_key" végződésű oszlopok jelzik az adatok helyét az eredeti listában vagy térképen. Az "_value" végződésű oszlopok a gyűjteményből származó kibontott adatokat tartalmazzák.

#### <a name="table-exampletable_vt_list"></a>"ExampleTable_vt_List" táblázat:
| pk_int | List_index | List_value |
| --- | --- | --- |
| 1 |0 |1 |
| 1 |1 |2 |
| 1 |2 |3 |
| 3 |0 |100 |
| 3 |1 |101 |
| 3 |2 |102 |
| 3 |3 |103 |

#### <a name="table-exampletable_vt_map"></a>"ExampleTable_vt_Map" táblázat:
| pk_int | Map_key | Map_value |
| --- | --- | --- |
| 1 |S1 |A |
| 1 |S2 |b |
| 3 |S1 |t |

#### <a name="table-exampletable_vt_stringset"></a>"ExampleTable_vt_StringSet" táblázat:
| pk_int | StringSet_value |
| --- | --- |
| 1 |A |
| 1 |B |
| 1 |C# |
| 3 |A |
| 3 |E |

## <a name="map-source-to-sink-columns"></a>Forrás leképezése oszlopokhoz
Ha többet szeretne tudni arról, hogy a forrásadatkészlet oszlopait a fogadó adatkészlet oszlopaihoz szeretné-e leképezni, olvassa [el az Adatkészletoszlopok leképezése az Azure Data Factoryban című témakört.](data-factory-map-columns.md)

## <a name="repeatable-read-from-relational-sources"></a>Relációs forrásokból ismételhető olvasmony
Ha relációs adattárakból másolja az adatokat, tartsa szem előtt az ismételhetőséget a nem kívánt eredmények elkerülése érdekében. Az Azure Data Factoryban manuálisan futtathatja a szeletet. Az adatkészlet újrapróbálkozási házirendje is konfigurálható, így a szelet újrafut, ha hiba történik. Ha egy szeletet mindkét irányban újrafuttat, meg kell győződnie arról, hogy ugyanazokat az adatokat olvassa el, függetlenül attól, hogy hányszor fut egy szelet. Lásd: [Ismételhető olvasás relációs forrásokból](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Teljesítmény és hangolás
A [Tevékenység teljesítményének másolása & hangolási útmutatóban](data-factory-copy-activity-performance.md) megismerést talál az adatok (másolási tevékenység) azure Data Factory ban az adatmozgatás (másolási tevékenység) teljesítményét befolyásoló legfontosabb tényezőkről, valamint az optimalizálás különböző módjairól.
