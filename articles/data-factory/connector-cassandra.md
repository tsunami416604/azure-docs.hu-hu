---
title: Adatok másolása az Azure Data Factory használatával Cassandra |} A Microsoft Docs
description: Megtudhatja, hogyan másolhat adatokat a Cassandra támogatott fogadó adattárakba az Azure Data Factory-folyamatot egy másolási tevékenység használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: jingwang
ms.openlocfilehash: 1347012971d53728d978f378e30684311c88828b
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54022280"
---
# <a name="copy-data-from-cassandra-using-azure-data-factory"></a>Adatok másolása az Azure Data Factory használatával Cassandra
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-onprem-cassandra-connector.md)
> * [Aktuális verzió](connector-cassandra.md)

Ez a cikk ismerteti az Azure Data Factory a másolási tevékenység adatokat másol egy Cassandra-adatbázis használata. Épül a [másolási tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

## <a name="supported-capabilities"></a>Támogatott képességek

A Cassandra-adatbázis adatok másolhatja bármely támogatott fogadó adattárba. A másolási tevékenység által, források és fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Pontosabban a Cassandra-összekötő támogatja:

- Cassandra **verziók 2.x és 3.x**.
- Másolja az adatokat az **alapszintű** vagy **névtelen** hitelesítést.

>[!NOTE]
>A tevékenység fut a helyi Integration Runtime, IR 3.7-es verziója óta, vagy újabb támogatott 3.x Cassandra.

## <a name="prerequisites"></a>Előfeltételek

Adatokat másol egy Cassandra-adatbázis, amely nem érhető el nyilvánosan, akkor be kell állítania egy helyi Integration Runtime. Lásd: [helyi Integration Runtime](create-self-hosted-integration-runtime.md) további részleteket a cikkben. Az integrációs modul egy beépített Cassandra-illesztőprogram biztosít, így nem kell manuálisan telepítenie az összes illesztőprogram példatípust az adatok, és a Cassandra.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Az alábbi szakaszok nyújtanak, amelyek meghatározzák az adott Data Factory-entitások Cassandra összekötő-tulajdonságokkal kapcsolatos részletekért.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Cassandra-beli társított szolgáltatás a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type |A type tulajdonságot kell beállítani: **Cassandra** |Igen |
| gazdagép |Egy vagy több IP-címek vagy kiszolgálók Cassandra gazdagép nevét.<br/>Adja meg az IP-címek vagy az összes kiszolgálóhoz csatlakozzon egyszerre állomásnevek vesszővel tagolt listája. |Igen |
| port |A Cassandra-kiszolgáló az ügyfélkapcsolatok figyeléséhez használt TCP portra. |Nem (alapértelmezés szerint a 9042) |
| authenticationType | A Cassandra-adatbázishoz való kapcsolódáshoz használt hitelesítés típusa.<br/>Engedélyezett értékek a következők: **Alapszintű**, és **névtelen**. |Igen |
| felhasználónév |Adja meg a felhasználói fiók felhasználónevét. |Igen, ha authenticationType beállítása alapszintű. |
| jelszó |Adja meg a felhasználói fiókhoz tartozó jelszót. Ez a mező megjelölése tárolja biztonságos helyen a Data Factory, a SecureString vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). |Igen, ha authenticationType beállítása alapszintű. |
| connectVia | A [Integration Runtime](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. (Ha az adattár nyilvánosan hozzáférhető) használhatja a helyi Integration Runtime vagy az Azure integrációs modul. Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. |Nem |

>[!NOTE]
>SSL-lel Cassandra kapcsolat jelenleg nem támogatott.

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

Szakaszok és adatkészletek definiálását tulajdonságainak teljes listájáért tekintse meg az adatkészletek a cikk. Ez a szakasz a Cassandra-adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Adatmásolás Cassandra, állítsa be a type tulajdonság, az adatkészlet **CassandraTable**. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adatkészlet értékre kell állítani: **CassandraTable** | Igen |
| kulcstér |A kulcstér vagy a sémát a Cassandra-adatbázis neve. |Nem (ha van megadva a "query" a "CassandraSource") |
| tableName |A tábla, Cassandra-adatbázis neve. |Nem (ha van megadva a "query" a "CassandraSource") |

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


Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz a Cassandra-forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="cassandra-as-source"></a>Cassandra forrásként

Adatok másolása a Cassandra, állítsa be a forrás típusaként a másolási tevékenység **CassandraSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrása type tulajdonsága értékre kell állítani: **CassandraSource** | Igen |
| lekérdezés |Az egyéni lekérdezés segítségével olvassa el az adatokat. |SQL-92 vagy CQL lekérdezés. Lásd: [CQL referencia](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>SQL-lekérdezés használata esetén adja meg a **kulcstér name.table neve** a lekérdezni kívánt tábla ábrázolásához. |Nem (Ha a "tableName" és "kulcstér" adatkészletben levő meg van adva). |
| consistencyLevel |A konzisztencia szintjét adja meg, hány replikák válaszolnia kell egy olvasási kérést előtt adatokat ad vissza az ügyfélalkalmazásnak. Cassandra ellenőrzi a megadott számú replikákat az adatok az olvasási kérelem teljesítéséhez. Lásd: [adatkonzisztencia konfigurálása](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) részleteiről.<br/><br/>Engedélyezett értékek a következők: **EGY**, **két**, **három**, **KVÓRUM**, **összes**, **LOCAL_QUORUM**, **EACH_QUORUM**, és **LOCAL_ONE**. |Nem (alapértelmezett érték a `ONE`) |

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

## <a name="data-type-mapping-for-cassandra"></a>Adattípus-leképezés Cassandra

Amikor az adatok másolása a Cassandra, a következő hozzárendeléseket szolgálnak a Cassandra-adattípusok Azure Data Factory-közbenső adattípusok. Lásd: [séma és adatok írja be a hozzárendelések](copy-activity-schema-and-type-mapping.md) megismerheti, hogyan másolási tevékenység leképezi a forrás séma és adatok típusa a fogadó.

| Cassandra-adattípus | Data factory közbenső adattípus |
|:--- |:--- |
| ASCII |Karakterlánc |
| BIGINT |Int64 |
| BLOB |Byte] |
| LOGIKAI ÉRTÉK |Logikai |
| TIZEDES TÖRT |Tizedes tört |
| DUPLA |Dupla |
| LEBEGŐPONTOS |Önálló |
| INET |Karakterlánc |
| INT |Int32 |
| SZÖVEG |Karakterlánc |
| IDŐBÉLYEG |DateTime |
| TIMEUUID |GUID |
| UUID |GUID |
| VARCHAR |Karakterlánc |
| VARINT |Tizedes tört |

> [!NOTE]
> Gyűjtemény típusa (map, set, list, stb.), tekintse meg [használata virtuális tábla használatával Cassandra gyűjteménytípusai](#work-with-collections-using-virtual-table) szakaszban.
>
> Felhasználó által meghatározott típusok nem támogatottak.
>
> A bináris és karakterlánc oszlop hossza hossza nem lehet nagyobb, mint 4000.
>

## <a name="work-with-collections-using-virtual-table"></a>Virtuális tábla használatával gyűjtemények használata

Az Azure Data Factory kapcsolódni, és adatokat másol a Cassandra-adatbázis egy beépített ODBC-illesztőt használja. Gyűjtemény esetében, beleértve a térkép, set és lista az illesztőprogram renormalizes az adatok megfelelő virtuális táblákba. Pontosabban Ha egy tábla gyűjtemény oszlopokat tartalmaz, az illesztőprogram állít elő, a következő virtuális táblák:

* A **alaptábla**, kivéve a gyűjtemény oszlopok valós táblaként azonos adatokat tartalmaz, amely. Az alaptábla ugyanazt a nevet használja, mint a valódi tábla, amely azt jelöli.
* A **virtuális tábla** minden gyűjtemény oszlophoz, amely kibővíti a beágyazott adatok. A virtuális táblákat, amelyek a gyűjtemények a név a valódi tábla, egy elválasztó használatával elnevezett "*vt*" és az oszlop neve.

Tekintse meg az adatok denormalizált adatokat az illesztőprogram, a valós táblázatban virtuális táblákat. Részletekért lásd a példa részét. Cassandra-gyűjtemények tartalmának lekérdezése és a virtuális táblázatok összekapcsolása elérheti.

### <a name="example"></a>Példa

Például egy, az alábbi "ExampleTable" nem egy Cassandra-adatbázis tábla, amely egy egész elsődlegeskulcs-oszlop neve "pk_int", érték nevű szöveges oszlop, lista oszlop, egy térkép oszlop és egy oszlopkészlet (más néven "StringSet") tartalmaz.

| pk_int | Érték | Lista | Térkép | StringSet |
| --- | --- | --- | --- | --- |
| 1 |"a minta 1. érték" |["1", "2", "3"] |{"S1": "a", "S2": "b"} |{"A", "B", "C"} |
| 3 |"a mintául szolgáló 3. érték" |["100", "101", "102", "105"] |{"S1": "t"} |{"A", "E"} |

Az illesztőprogram hoz létre több virtuális táblákat, amelyek ebben a táblázatban szerepelnek. A virtuális táblák idegen kulcs oszlopát a valódi tábla elsődleges kulcs oszlopaira hivatkozik, és melyik felel meg a virtuális táblázatsor valós táblázatsor jelzi.

Az első virtuális tábla az alaptábla "ExampleTable" nevű az alábbi táblázatban látható: 

| pk_int | Érték |
| --- | --- |
| 1 |"a minta 1. érték" |
| 3 |"a mintául szolgáló 3. érték" |

Az alaptábla az eredeti adatbázis tábla kivételével a gyűjteményeket, amelyek ebből a táblázatból nincs megadva, és más virtuális táblák kibontva azonos adatokat tartalmaz.

Az alábbi táblázatokban látható a listában, a térkép és StringSet oszlop adatait normalizálását virtuális táblákhoz. Az oszlopokat "_index" vagy "_kulcsvédelmi" karakterre végződjön adja meg az adatok az eredeti listát vagy térkép pozícióját. Az "é_rtéke" karakterre végződjön oszlopokat abból a gyűjteményből kibontott adatokat tartalmazzák.

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

**Table "ExampleTable_vt_Map":**

| pk_int | Map_key | Map_value |
| --- | --- | --- |
| 1 |S1 |A |
| 1 |S2 |b |
| 3 |S1 |t |

**Table "ExampleTable_vt_StringSet":**

| pk_int | StringSet_value |
| --- | --- |
| 1 |A |
| 1 |B |
| 1 |C |
| 3 |A |
| 3 |E |

## <a name="next-steps"></a>További lépések
A másolási tevékenység az Azure Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md##supported-data-stores-and-formats).
