---
title: "Adatok másolása az Azure Data Factory használatával Cassandra |} Microsoft Docs"
description: "Útmutató: adatok másolása Cassandra támogatott fogadó adattárolókhoz egy Azure Data Factory-folyamat a másolási tevékenység használatával."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2017
ms.author: jingwang
ms.openlocfilehash: eba08c38a5502368beda7ca7f84559ecca011133
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2017
---
# <a name="copy-data-from-cassandra-using-azure-data-factory"></a>Adatok másolása az Azure Data Factory használatával Cassandra
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1. verzió – Általánosan elérhető](v1/data-factory-onprem-cassandra-connector.md)
> * [2. verzió – Előzetes verzió](connector-cassandra.md)

Ez a cikk ismerteti a másolási tevékenység használata az Azure Data Factory adatok másolása Cassandra adatbázis. Buildekről nyújtanak a [másolása tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.


> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [Cassandra összekötő a V1](v1/data-factory-onprem-cassandra-connector.md).

## <a name="supported-capabilities"></a>Támogatott képességei

Adatok bármely támogatott fogadó adattárolóhoz Cassandra adatbázis másolhatja. Adattároló források/mosdók, a másolási tevékenység által támogatott listájáért lásd: a [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Konkrétan ez Cassandra az összekötő támogatja:

- Cassandra **verziók 2.X**.
- Adatok másolása **alapvető** vagy **névtelen** hitelesítés.

## <a name="prerequisites"></a>Előfeltételek

Adatok másolása nem nyilvánosan elérhető Cassandra adatbázist, akkor be kell állítania egy Self-hosted integrációs futásidejű. Lásd: [Self-hosted integrációs futásidejű](create-self-hosted-integration-runtime.md) cikk további részleteket. Az integrációs futásidejű beépített Cassandra illesztőprogram biztosít, ezért nem, manuálisan kell telepítenie minden olyan illesztőprogram Cassandra a/az adatok másolásakor kell.

## <a name="getting-started"></a>Bevezetés
A másolási tevékenység során .NET SDK, a Python SDK, az Azure PowerShell, a REST API-t vagy a Azure Resource Manager-sablon használatával hozhat létre egy folyamatot. Lásd: [másolási tevékenység oktatóanyag](quickstart-create-data-factory-dot-net.md) hozzon létre egy folyamatot a másolási tevékenység részletes útmutatóját.

A következő szakaszok részletesen bemutatják való Cassandra összekötő adat-előállító tartozó entitások meghatározásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok

A következő tulajdonságok Cassandra kapcsolódó szolgáltatás támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type |A type tulajdonságot kell beállítani: **Cassandra** |Igen |
| állomás |Egy vagy több IP-címek vagy Cassandra kiszolgálók állomás nevét.<br/>IP-címek vagy állomásnevek kiszolgálókhoz való kapcsolódáshoz összes egyidejűleg vesszővel tagolt listáját adja meg. |Igen |
| port |A TCP-portot, amelyen a Cassandra kiszolgáló ügyfélkapcsolatokat. |Nem (alapértelmezett érték 9042) |
| AuthenticationType | A Cassandra adatbázishoz való kapcsolódáshoz használt hitelesítés típusa.<br/>Két érték engedélyezett: **alapvető**, és **névtelen**. |Igen |
| felhasználónév |Adja meg a felhasználói fiók felhasználónevét. |Igen, ha authenticationType beállítása alapszintű. |
| jelszó |Adja meg a felhasználói fiók jelszavát. Ez a mező megjelölése SecureString. |Igen, ha authenticationType beállítása alapszintű. |
| connectVia | A [integrációs futásidejű](concepts-integration-runtime.md) csatlakozni az adattárolóhoz használandó. Használhatja Self-hosted integrációs futásidejű vagy Azure integrációs futásidejű (ha az adattároló nyilvánosan elérhető). Ha nincs megadva, akkor használja az alapértelmezett Azure integrációs futásidejű. |Nem |

**Példa**

```json
{
    "name": "CassandraLinkedService",
    "properties": {
        "type": "Cassandra",
        "typeProperties": {
            "host": "<host>",
            "authenticationType": "Basic",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Szakaszok és meghatározása adatkészletek esetében elérhető tulajdonságok teljes listájáért tekintse meg az adatkészletek cikket. Ez a témakör Cassandra dataset által támogatott tulajdonságokról.

Adatok másolása Cassandra, az adatkészlet típus tulajdonságának beállítása **RelationalTable**. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adathalmaz értékre kell állítani: **CassandraTable** | Igen |
| kulcstérértesítések használatával |Kulcstérértesítések használatával vagy séma Cassandra adatbázis nevét. |Nem (ha van megadva a "query" a "CassandraSource") |
| tableName |A tábla Cassandra adatbázis neve. |Nem (ha van megadva a "query" a "CassandraSource") |

**Példa**

```json
{
    "name": "CassandraDataset",
    "properties": {
        "type": "CassandraTable",
        "linkedServiceName": {
            "referenceName": "<Cassandra linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "keySpace": "<keyspace name>",
            "tableName": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai


Szakaszok és a rendelkezésre álló tevékenységek meghatározó tulajdonságok teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a témakör Cassandra forrás által támogatott tulajdonságokról.

### <a name="cassandra-as-source"></a>Cassandra forrásaként

Adatok másolása Cassandra, állítsa be a forrás típusa a másolási tevékenység **CassandraSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakasz:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot a másolási tevékenység forrás értékre kell állítani: **CassandraSource** | Igen |
| lekérdezés |Az egyéni lekérdezés segítségével adatokat olvasni. |SQL-92 vagy CQL lekérdezés. Lásd: [CQL hivatkozás](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>SQL-lekérdezés használata esetén adja meg a **kulcstérértesítések használatával name.table neve** a lekérdezni kívánt táblázat képviseli. |Nem (Ha a "tableName" és "kulcstérértesítések" használatával adatkészlet meg van adva). |
| consistencyLevel |A konzisztencia szint határozza meg, hány replikák adatok visszatér az ügyfélalkalmazás egy olvasási kérést kell válaszolnia. Cassandra ellenőrzi a megadott számú replikákat az adatok a olvasási kérelem teljesítéséhez. Lásd: [konfigurálása az adatok konzisztenciájának](http://docs.datastax.com/en//cassandra/2.0/cassandra/dml/dml_config_consistency_c.html) részleteiről.<br/><br/>Két érték engedélyezett: **egy**, **két**, **három**, **KVÓRUM**, **összes**, **LOCAL_ KVÓRUM**, **EACH_QUORUM**, és **LOCAL_ONE**. |Nem (alapértelmezett érték a `ONE`) |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromCassandra",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Cassandra input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "CassandraSource",
                "query": "select id, firstname, lastname from mykeyspace.mytable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-cassandra"></a>Adattípus-leképezést Cassandra

Az adatok másolása Cassandra, amikor az Azure Data Factory ideiglenes adattípusok a következő megfeleltetéseket használtak Cassandra adattípusokat. Lásd: [séma- és írja be a leképezéseket](copy-activity-schema-and-type-mapping.md) hogyan másolási tevékenység van leképezve a séma- és adatok típusa a fogadó tájékozódhat.

| Cassandra adattípus | Data factory ideiglenes adattípus |
|:--- |:--- |
| ASCII |Karakterlánc |
| BIGINT |Int64 |
| A BLOB |Byte] |
| LOGIKAI ÉRTÉK |Logikai érték |
| DECIMÁLIS |Decimális |
| DUPLA |Dupla |
| LEBEGŐPONTOS |Egyetlen |
| INET |Karakterlánc |
| INT |Int32 |
| SZÖVEG |Karakterlánc |
| IDŐBÉLYEG |Dátum és idő |
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

## <a name="work-with-collections-using-virtual-table"></a>Virtuális tábla használatával gyűjtemények használata

Az Azure Data Factory beépített ODBC-illesztőprogram használatával csatlakozhat, és másolja az adatokat a Cassandra adatbázisból. A gyűjtemény típusú térkép, a készlet és a lista az illesztőprogram renormalizes az adatok megfelelő virtuális táblákba. Pontosabban Ha a tábla gyűjtemény oszlopot tartalmaz, az illesztőprogram állít elő, a következő virtuális táblák:

* A **alaptábla**, amely tartalmazza a tényleges táblából, a gyűjtemény oszlopok ugyanazokat az adatokat. Az alaptábla ugyanazt a nevet használja, amely valós táblázatként.
* A **virtuális tábla** gyűjtemény oszlopok, amely a beágyazott adatok tárolásához. A virtuális táblákat, amelyek megfelelnek a gyűjtemények az alábbi néven valós tábla, egy elválasztó elnevezése "*vt*" és az oszlop neve.

Virtuális táblák tekintse meg az adatok a valós táblázatban, az illesztőprogram, a nem normalizált adatok eléréséhez. Lásd például című szakaszban talál információt. A tartalom Cassandra gyűjtemények kérdez le, és a virtuális tábla érheti el.

### <a name="example"></a>Példa

A következő "ExampleTable" például egy egész elsődleges kulcsként megadott oszlop "pk_int", érték nevű szöveges oszlop, egy listaoszlop, térkép oszlop és egy oszlopkészlet ("StringSet" nevű) nevű tartalmazó Cassandra adatbázistábla.

| pk_int | Érték | Lista | Térkép | StringSet |
| --- | --- | --- | --- | --- |
| 1 |"a minta érték 1" |["1", "2", "3"] |{"S1": "a", "S2": "b"} |{"A", "B", "C"} |
| 3 |"a minta 3. érték" |["100", "101", "102", "105"] |{"S1": "t"} |{"A", "E"} |

Az illesztőprogram az egyetlen tábla képviselő virtuális táblákat hoz létre. A külső kulcs oszlopokra virtuális táblázatokban a valódi tábla elsődleges kulcs oszlopai hivatkozik, és melyik felel meg a virtuális tábla sorainak valós tábla sorainak jelzi.

Az első virtuális táblát kell az alaptábla "ExampleTable" nevű az alábbi táblázatban látható: 

| pk_int | Érték |
| --- | --- |
| 1 |"a minta érték 1" |
| 3 |"a minta 3. érték" |

A következő alaptáblában az eredeti adatbázis táblából a gyűjteményeket, amelyek ebből a táblázatból nincs megadva, és más virtuális táblák kibontva ugyanazokat az adatokat tartalmazza.

Az alábbi táblázatok bemutatják az adatokat a listában, térkép és StringSet oszlopokból renormalize virtuális táblákhoz. Az oszlopok kiderül, hogy a "_index" vagy "_kulcsvédelmi" adja meg az adatokat az eredeti lista vagy a térkép pozícióját. "_Value" végződő nevű oszlopot tartalmazhat a kibontott adatok a gyűjteményből.

**"ExampleTable_vt_List". tábla:**

| pk_int | List_index | List_value |
| --- | --- | --- |
| 1 |0 |1 |
| 1 |1 |2 |
| 1 |2 |3 |
| 3 |0 |100 |
| 3 |1 |101 |
| 3 |2 |102 |
| 3 |3 |103 |

**"ExampleTable_vt_Map". tábla:**

| pk_int | Map_key | Map_value |
| --- | --- | --- |
| 1 |S1 |A |
| 1 |S2 |B |
| 3 |S1 |T |

**"ExampleTable_vt_StringSet". tábla:**

| pk_int | StringSet_value |
| --- | --- |
| 1 |A |
| 1 |B |
| 1 |C# |
| 3 |A |
| 3 |E |

## <a name="next-steps"></a>Következő lépések
Támogatott források és mosdók által a másolási tevékenység során az Azure Data Factory adattárolókhoz listájáért lásd: [adattárolókhoz támogatott](copy-activity-overview.md##supported-data-stores-and-formats).