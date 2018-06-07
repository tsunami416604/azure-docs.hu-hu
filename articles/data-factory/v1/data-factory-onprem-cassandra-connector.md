---
title: Adatok áthelyezése a Data Factory használatával Cassandra |} Microsoft Docs
description: Tudnivalók az adatok áthelyezése az Azure Data Factory használatával a helyszíni Cassandra adatbázisból.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 085cc312-42ca-4f43-aa35-535b35a102d5
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 6e6b9bf194da17ebd03389829ba594bf3fbf1e64
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34622101"
---
# <a name="move-data-from-an-on-premises-cassandra-database-using-azure-data-factory"></a>Adatok áthelyezése az Azure Data Factory használatával a helyszíni Cassandra adatbázisból
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1. verzió – Általánosan elérhető](data-factory-onprem-cassandra-connector.md)
> * [2. verzió – Előzetes verzió](../connector-cassandra.md)

> [!NOTE]
> Ez a cikk a Data Factory általánosan elérhető 1. verziójára vonatkozik. Lásd a 2-es verziójának a Data Factory szolgáltatásnak, amely jelenleg előzetes verzióban érhető, használatakor [Cassandra összekötőt, a V2](../connector-cassandra.md).

Ez a cikk ismerteti, hogyan a másolási tevékenység során az Azure Data Factory áthelyezni az adatokat a helyszíni Cassandra adatbázisból. Buildekről nyújtanak a [adatok mozgása tevékenységek](data-factory-data-movement-activities.md) cikk, amelynek során adatátvitel a másolási tevékenység az általános áttekintést.

Egy helyszíni Cassandra adattároló adatok bármely támogatott fogadó adattárolóhoz másolhatja. A másolási tevékenység által támogatott mosdók adattárolókhoz listájáért lásd: a [adattárolókhoz támogatott](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tábla. Adat-előállító jelenleg mozgási adatok kizárólag egy Cassandra adattárból egyéb adattárakhoz, de nem az adatok áthelyezése az egyéb adattárakhoz Cassandra adattárat. 

## <a name="supported-versions"></a>Támogatott verziók
A Cassandra összekötő Cassandra a következő verzióit támogatja: 2.X.

## <a name="prerequisites"></a>Előfeltételek
A helyszíni Cassandra adatbázishoz csatlakozni az Azure Data Factory szolgáltatás telepítenie kell az adatkezelési átjáró ugyanazon a számítógépen, amelyen az adatbázis vagy egy külön számítógépen elkerülésére használják a források az adatbázissal. Az adatkezelési átjáró összetevő, amely a helyszíni adatforrások felhőszolgáltatások felügyelt és biztonságos módon. Lásd: [az adatkezelési átjáró](data-factory-data-management-gateway.md) szóló cikkben olvashat az adatkezelési átjáró. Lásd: [tárolt adatok mozgatása felhőbe helyszíni](data-factory-move-data-between-onprem-and-cloud.md) cikk lépésenkénti adatok folyamat az átjáró beállítása áthelyezni az adatokat.

Az átjáró adatbázishoz való kapcsolódáshoz a Cassandra akkor is, ha az adatbázis egy a felhőben, például egy Azure IaaS virtuális gépen kell használnia. Y is van az átjáró az azonos virtuális gépen, amelyen az adatbázist, vagy egy külön virtuális gépre mindaddig az átjáró képes kapcsolódni az adatbázishoz.  

Az átjáró telepítésekor automatikusan telepíti a Microsoft Cassandra ODBC-illesztőprogram Cassandra adatbázishoz való kapcsolódáshoz használt. Emiatt nem kell manuálisan telepítenie minden olyan illesztőprogram az átjáró számítógépe a Cassandra adatbázisból származó adatok másolásakor. 

> [!NOTE]
> Lásd: [átjáró elhárítása](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) kapcsolati/átjáró hibaelhárítási tippek a kapcsolódó problémákat.

## <a name="getting-started"></a>Első lépések
A másolási tevékenység, mely az adatok egy helyszíni Cassandra adattároló különböző eszközök/API-k használatával létrehozhat egy folyamatot. 

- Hozzon létre egy folyamatot a legegyszerűbb módja használatára a **másolása varázsló**. Lásd: [oktatóanyag: hozzon létre egy folyamatot, másolása varázslóval](data-factory-copy-data-wizard-tutorial.md) létrehozásával egy folyamatot, az adatok másolása varázsló segítségével gyorsan útmutatást. 
- Az alábbi eszközöket használhatja a folyamatokat létrehozni: **Azure-portálon**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager sablon**, **.NET API**, és **REST API**. Lásd: [másolási tevékenység oktatóanyag](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) hozzon létre egy folyamatot a másolási tevékenység részletes útmutatóját. 

Akár az eszközök vagy API-k, hajtsa végre a következő lépésekkel hozza létre egy folyamatot, amely mozgatja az adatokat a forrás-tárolóban a fogadó tárolóban:

1. Hozzon létre **összekapcsolt szolgáltatások** bemeneti és kimeneti adatok csatolásához tárolja a a data factory.
2. Hozzon létre **adatkészletek** a másolási művelet bemeneti és kimeneti adatok. 
3. Hozzon létre egy **csővezeték** , amely fogad egy bemeneti adatkészlet és egy kimeneti adatkészletet másolási tevékenységgel. 

A varázsló használatakor a Data Factory entitások (összekapcsolt szolgáltatások adatkészletek és a feldolgozási sor) JSON-definíciók automatikusan létrejönnek. Eszközök/API-k (kivéve a .NET API-t) használata esetén adja meg a Data Factory entitások a JSON formátum használatával.  Adatok másolása egy helyszíni Cassandra adattároló használt adat-előállító entitások JSON-definíciók minta, lásd: [JSON-példa: adatok másolása az Cassandra az Azure Blob](#json-example-copy-data-from-cassandra-to-azure-blob) című szakaszát. 

A következő szakaszok részletesen bemutatják való Cassandra adattároló adat-előállító tartozó entitások meghatározásához használt JSON tulajdonságokat:

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok
A következő táblázat a JSON-elemek szerepelnek Cassandra kapcsolódó szolgáltatásra vonatkozó leírást.

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| type |A type tulajdonságot kell beállítani: **OnPremisesCassandra** |Igen |
| gazdagép |Egy vagy több IP-címek vagy Cassandra kiszolgálók állomás nevét.<br/><br/>IP-címek vagy állomásnevek kiszolgálókhoz való kapcsolódáshoz összes egyidejűleg vesszővel tagolt listáját adja meg. |Igen |
| port |A TCP-portot, amelyen a Cassandra kiszolgáló ügyfélkapcsolatokat. |Nem, alapértelmezett érték: 9042 |
| authenticationType |Basic vagy Anonymous |Igen |
| felhasználónév |Adja meg a felhasználói fiók felhasználónevét. |Igen, ha authenticationType beállítása alapszintű. |
| jelszó |Adja meg a felhasználói fiók jelszavát. |Igen, ha authenticationType beállítása alapszintű. |
| gatewayName |A helyszíni Cassandra adatbázishoz való csatlakozáshoz használt átjáró neve. |Igen |
| encryptedCredential |Az átjáró által titkosított hitelesítő. |Nem |

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai
Szakaszok & meghatározása adatkészletek esetében elérhető tulajdonságok teljes listáját lásd: a [adatkészletek létrehozása](data-factory-create-datasets.md) cikk. Például struktúra, a rendelkezésre állás és a házirend a DataSet adatkészlet JSON hasonlítanak minden adatkészlet esetében (Azure SQL, az Azure blob, Azure-tábla, stb.).

A **typeProperties** szakasz eltérő adatkészlet egyes típusai és információkat nyújt azokról az adattárban adatok helyét. A typeProperties szakasz típusú adatkészlet **CassandraTable** tulajdonságai a következők

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| kulcstérértesítések használatával |Kulcstérértesítések használatával vagy séma Cassandra adatbázis nevét. |Igen (Ha **lekérdezés** a **CassandraSource** nincs definiálva). |
| tableName |A tábla Cassandra adatbázis neve. |Igen (Ha **lekérdezés** a **CassandraSource** nincs definiálva). |

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
Szakaszok & rendelkezésre álló tevékenységek meghatározó tulajdonságok teljes listáját lásd: a [létrehozása folyamatok](data-factory-create-pipelines.md) cikk. Például a nevét, leírását, valamint bemeneti és kimeneti táblák és házirend tulajdonságai minden típusú tevékenységek érhetők el.

Mivel a tevékenység typeProperties szakaszában elérhető tulajdonságok tevékenységek minden típusának függenek. A másolási tevékenység során két érték források és mosdók típusától függően.

Ha a forrás típusa van **CassandraSource**, a következő tulajdonságok érhetők el typeProperties szakaszában:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| lekérdezés |Az egyéni lekérdezés segítségével adatokat olvasni. |SQL-92 vagy CQL lekérdezés. Lásd: [CQL hivatkozás](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>SQL-lekérdezés használata esetén adja meg a **kulcstérértesítések használatával name.table neve** a lekérdezni kívánt táblázat képviseli. |Nem (ha van megadva a tableName és a dataset kulcstérértesítések használatával). |
| consistencyLevel |A konzisztencia szint határozza meg, hány replikák adatok visszatér az ügyfélalkalmazás egy olvasási kérést kell válaszolnia. Cassandra ellenőrzi a megadott számú replikákat az adatok a olvasási kérelem teljesítéséhez. |EGY, KETTŐ, HÁROM, KVÓRUM, AZ ÖSSZES, LOCAL_QUORUM EACH_QUORUM, LOCAL_ONE. Lásd: [konfigurálása az adatok konzisztenciájának](http://docs.datastax.com/en//cassandra/2.0/cassandra/dml/dml_config_consistency_c.html) részleteiről. |Nem. Alapértelmezett érték: egyet. |

## <a name="json-example-copy-data-from-cassandra-to-azure-blob"></a>JSON-példa: adatok másolása az Cassandra az Azure-Blobba
Ebben a példában a minta JSON-definíciókat tartalmazzon, segítségével hozzon létre egy folyamatot biztosít [Azure-portálon](data-factory-copy-activity-tutorial-using-azure-portal.md) vagy [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Azt illusztrálja, hogyan helyszíni Cassandra adatbázisból származó adatok másolása az Azure Blob Storage. Azonban adatok átmásolhatók a megadott mosdók bármelyikét [Itt](data-factory-data-movement-activities.md#supported-data-stores-and-formats) a másolási tevékenység során az Azure Data Factory használatával.

> [!IMPORTANT]
> Ez a minta JSON kódtöredékek biztosít. Nem tartalmazza az adat-előállítóban létrehozásának részletes leírása. Lásd: [adatokat a helyszíni helyek és a felhő közötti áthelyezése](data-factory-move-data-between-onprem-and-cloud.md) cikk lépéseit.

A minta a következő data factory entitások rendelkezik:

* A társított szolgáltatás típusa [OnPremisesCassandra](#linked-service-properties).
* A társított szolgáltatás típusa [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Bemeneti [dataset](data-factory-create-datasets.md) típusú [CassandraTable](#dataset-properties).
* Egy kimeneti [dataset](data-factory-create-datasets.md) típusú [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* A [csővezeték](data-factory-create-pipelines.md) a másolási tevékenység által használt [CassandraSource](#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

**Cassandra társított szolgáltatáshoz:**

Ez a példa a **Cassandra** társított szolgáltatás. Lásd: [Cassandra társított szolgáltatás](#linked-service-properties) szakasz szolgáltatásnak által támogatott tulajdonságokról.  

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

Beállítás **külső** való **igaz** tájékoztatja a Data Factory szolgáltatásnak, hogy az adatkészlet data factoryval való külső, és egy tevékenység adat-előállító nem hozzák.

**Az Azure Blob kimeneti adatkészlet:**

Adatot ír egy új blob minden órában (gyakoriság: óra, időköz: 1).

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

**Másolási tevékenység Cassandra és Blob fogadó egy folyamaton belül:**

A feldolgozási sor tartalmazza a másolási tevékenység, amely a bemeneti és kimeneti adatkészletek használatára van konfigurálva, és óránkénti futásra nem ütemezték. Az adatcsatorna JSON-definícióból a **forrás** típusúra **CassandraSource** és **fogadó** típusúra **BlobSink**.

Lásd: [RelationalSource típustulajdonságokat](#copy-activity-properties) a RelationalSource által támogatott tulajdonságok listája.

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

### <a name="type-mapping-for-cassandra"></a>Típus identitástagok esetén Cassandra
| Cassandra típusa | .NET-alapú típusa |
| --- | --- |
| ASCII |Karakterlánc |
| BIGINT |Int64 |
| A BLOB |Byte] |
| LOGIKAI ÉRTÉK |Logikai |
| DECIMÁLIS |Decimális |
| DUPLA |Dupla |
| LEBEGŐPONTOS |Önálló |
| INET |Karakterlánc |
| INT |Int32 |
| SZÖVEG |Karakterlánc |
| IDŐBÉLYEG |DateTime |
| TIMEUUID |GUID |
| UUID |GUID |
| VARCHAR |Karakterlánc |
| VARINT |Decimális |

> [!NOTE]
> A gyűjtemény típusa (térkép, set, lista stb.), meg [virtuális tábla használatával Cassandra gyűjteménytípusok együttműködve](#work-with-collections-using-virtual-table) szakasz.
>
> Felhasználó által definiált típusok nem támogatottak.
>
> A bináris oszlop és karakterlánc-oszlopnak hosszúságú hossza nem lehet nagyobb, mint 4000.
>
>

## <a name="work-with-collections-using-virtual-table"></a>Virtuális tábla használatával gyűjtemények használata
Az Azure Data Factory beépített ODBC-illesztőprogram használatával csatlakozhat, és másolja az adatokat a Cassandra adatbázisból. A gyűjtemény típusú térkép, a készlet és a lista az illesztőprogram renormalizes az adatok megfelelő virtuális táblákba. Pontosabban Ha a tábla gyűjtemény oszlopot tartalmaz, az illesztőprogram állít elő, a következő virtuális táblák:

* A **alaptábla**, amely tartalmazza a tényleges táblából, a gyűjtemény oszlopok ugyanazokat az adatokat. Az alaptábla ugyanazt a nevet használja, amely valós táblázatként.
* A **virtuális tábla** gyűjtemény oszlopok, amely a beágyazott adatok tárolásához. A virtuális táblákat, amelyek megfelelnek a gyűjtemények az alábbi néven valós tábla, egy elválasztó elnevezése "*vt*" és az oszlop neve.

Virtuális táblák tekintse meg az adatok a valós táblázatban, az illesztőprogram, a nem normalizált adatok eléréséhez. Lásd például című szakaszban talál információt. A tartalom Cassandra gyűjtemények kérdez le, és a virtuális tábla érheti el.

Használhatja a [másolása varázsló](data-factory-data-movement-activities.md#create-a-pipeline-with-copy-activity) intuitív táblázatok listájának megtekintése a virtuális táblázatot is Cassandra adatbázisban és található adatok megtekintésére. Is másolása varázsló az olyan lekérdezést, és az érvényesítés lehetőségre az eredményt.

### <a name="example"></a>Példa
A következő "ExampleTable" például egy egész elsődleges kulcsként megadott oszlop "pk_int", érték nevű szöveges oszlop, egy listaoszlop, térkép oszlop és egy oszlopkészlet ("StringSet" nevű) nevű tartalmazó Cassandra adatbázistábla.

| pk_int | Érték | Lista | Térkép | StringSet |
| --- | --- | --- | --- | --- |
| 1 |"a minta érték 1" |["1", "2", "3"] |{"S1": "a", "S2": "b"} |{"A", "B", "C"} |
| 3 |"a minta 3. érték" |["100", "101", "102", "105"] |{"S1": "t"} |{"A", "E"} |

Az illesztőprogram az egyetlen tábla képviselő virtuális táblákat hoz létre. A külső kulcs oszlopokra virtuális táblázatokban a valódi tábla elsődleges kulcs oszlopai hivatkozik, és melyik felel meg a virtuális tábla sorainak valós tábla sorainak jelzi.

Az első virtuális táblát kell az alaptábla "ExampleTable" nevű az alábbi táblázatban látható. A következő alaptáblában az eredeti adatbázis táblából a gyűjteményeket, amelyek ebből a táblázatból nincs megadva, és más virtuális táblák kibontva ugyanazokat az adatokat tartalmazza.

| pk_int | Érték |
| --- | --- |
| 1 |"a minta érték 1" |
| 3 |"a minta 3. érték" |

Az alábbi táblázatok bemutatják az adatokat a listában, térkép és StringSet oszlopokból renormalize virtuális táblákhoz. Az oszlopok kiderül, hogy a "_index" vagy "_kulcsvédelmi" adja meg az adatokat az eredeti lista vagy a térkép pozícióját. "_Value" végződő nevű oszlopot tartalmazhat a kibontott adatok a gyűjteményből.

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
| 3 |S1 |T |

#### <a name="table-exampletablevtstringset"></a>Table “ExampleTable_vt_StringSet”:
| pk_int | StringSet_value |
| --- | --- |
| 1 |A |
| 1 |B |
| 1 |C |
| 3 |A |
| 3 |E |

## <a name="map-source-to-sink-columns"></a>Térkép forrás oszlopok gyűjtése
A forrás oszlop szerepel a fogadó dataset adatkészlet leképezési oszlopok, lásd: [Azure Data Factory dataset oszlopai leképezési](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>A relációs források ismételhető Olvasás
Ha az adatok másolását a relációs adatokat tárol, ismételhetőség tartsa szem előtt, nem kívánt eredmények elkerülése érdekében. Az Azure Data Factoryben futtathatja a szelet manuálisan. Beállíthatja úgy is egy adatkészlet újrapróbálkozási házirendje, hogy a szelet akkor fut újra, ha hiba történik. A szelet akkor fut újra, vagy módon, ha győződjön meg arról, hogy ugyanazokat az adatokat olvasható függetlenül attól, hogy a szelet futtatása hány alkalommal kell. Lásd: [relációs források olvasni Repeatable](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Teljesítmény- és hangolása
Lásd: [másolási tevékenység teljesítmény- és hangolása útmutató](data-factory-copy-activity-performance.md) tájékozódhat az kulcsfontosságú szerepet játszik adatátvitelt jelölik a (másolási tevékenység során) az Azure Data Factory és különböző módokon optimalizálhatja azt, hogy hatás teljesítményét.
