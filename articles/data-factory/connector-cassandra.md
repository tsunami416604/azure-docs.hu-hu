---
title: Adatok másolása az Azure Data Factory használatával Cassandra |} Microsoft Docs
description: 'Útmutató: adatok másolása Cassandra támogatott fogadó adattárolókhoz egy Azure Data Factory-folyamat a másolási tevékenység használatával.'
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: jingwang
ms.openlocfilehash: a0095ae4aa50845a24cabb981399ac4035afdebe
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37051450"
---
# <a name="copy-data-from-cassandra-using-azure-data-factory"></a>Adatok másolása az Azure Data Factory használatával Cassandra
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verziójával](v1/data-factory-onprem-cassandra-connector.md)
> * [Aktuális verzió](connector-cassandra.md)

Ez a cikk ismerteti a másolási tevékenység használata az Azure Data Factory adatok másolása Cassandra adatbázis. Buildekről nyújtanak a [másolása tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

## <a name="supported-capabilities"></a>Támogatott képességei

Adatok bármely támogatott fogadó adattárolóhoz Cassandra adatbázis másolhatja. Adattároló források/mosdók, a másolási tevékenység által támogatott listájáért lásd: a [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Konkrétan ez Cassandra az összekötő támogatja:

- Cassandra **verziók 2.x és 3.x**.
- Adatok másolása **alapvető** vagy **névtelen** hitelesítés.

>[!NOTE]
>A tevékenység fut a Self-hosted integrációs futásidejű Cassandra 3.x IR 3.7-es verziója óta vagy újabb támogatott.

## <a name="prerequisites"></a>Előfeltételek

Adatok másolása nem nyilvánosan elérhető Cassandra adatbázist, akkor be kell állítania egy Self-hosted integrációs futásidejű. Lásd: [Self-hosted integrációs futásidejű](create-self-hosted-integration-runtime.md) cikk további részleteket. Az integrációs futásidejű beépített Cassandra illesztőprogram biztosít, ezért nem, manuálisan kell telepítenie minden olyan illesztőprogram Cassandra a/az adatok másolásakor kell.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok részletesen bemutatják való Cassandra összekötő adat-előállító tartozó entitások meghatározásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok

A következő tulajdonságok Cassandra kapcsolódó szolgáltatás támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type |A type tulajdonságot kell beállítani: **Cassandra** |Igen |
| gazdagép |Egy vagy több IP-címek vagy Cassandra kiszolgálók állomás nevét.<br/>IP-címek vagy állomásnevek kiszolgálókhoz való kapcsolódáshoz összes egyidejűleg vesszővel tagolt listáját adja meg. |Igen |
| port |A TCP-portot, amelyen a Cassandra kiszolgáló ügyfélkapcsolatokat. |Nem (alapértelmezett érték 9042) |
| authenticationType | A Cassandra adatbázishoz való kapcsolódáshoz használt hitelesítés típusa.<br/>Két érték engedélyezett: **alapvető**, és **névtelen**. |Igen |
| felhasználónév |Adja meg a felhasználói fiók felhasználónevét. |Igen, ha authenticationType beállítása alapszintű. |
| jelszó |Adja meg a felhasználói fiók jelszavát. Ez a mező megjelölése a SecureString tárolja biztonságos helyen, a Data factoryban vagy [hivatkozik az Azure Key Vault tárolt titkos kulcs](store-credentials-in-key-vault.md). |Igen, ha authenticationType beállítása alapszintű. |
| connectVia | A [integrációs futásidejű](concepts-integration-runtime.md) csatlakozni az adattárolóhoz használandó. Használhatja Self-hosted integrációs futásidejű vagy Azure integrációs futásidejű (ha az adattároló nyilvánosan elérhető). Ha nincs megadva, akkor használja az alapértelmezett Azure integrációs futásidejű. |Nem |

>[!NOTE]
>Kapcsolat SSL használatával Cassandra jelenleg nem támogatott.

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

Adatok másolása Cassandra, az adatkészlet típus tulajdonságának beállítása **CassandraTable**. A következő tulajdonságok támogatottak:

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
| consistencyLevel |A konzisztencia szint határozza meg, hány replikák adatok visszatér az ügyfélalkalmazás egy olvasási kérést kell válaszolnia. Cassandra ellenőrzi a megadott számú replikákat az adatok a olvasási kérelem teljesítéséhez. Lásd: [konfigurálása az adatok konzisztenciájának](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) részleteiről.<br/><br/>Két érték engedélyezett: **egy**, **két**, **három**, **KVÓRUM**, **összes**, **LOCAL_ KVÓRUM**, **EACH_QUORUM**, és **LOCAL_ONE**. |Nem (alapértelmezett érték a `ONE`) |

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
| ASCII |Sztring |
| BIGINT |Int64 |
| A BLOB |Byte] |
| LOGIKAI ÉRTÉK |Logikai |
| DECIMÁLIS |Decimális |
| DUPLA |Dupla |
| LEBEGŐPONTOS |Önálló |
| INET |Sztring |
| INT |Int32 |
| SZÖVEG |Sztring |
| IDŐBÉLYEG |DateTime |
| TIMEUUID |GUID |
| UUID |GUID |
| VARCHAR |Sztring |
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

**Table "ExampleTable_vt_Map":**

| pk_int | Map_key | Map_value |
| --- | --- | --- |
| 1 |S1 |A |
| 1 |S2 |b |
| 3 |S1 |T |

**Table "ExampleTable_vt_StringSet":**

| pk_int | StringSet_value |
| --- | --- |
| 1 |A |
| 1 |B |
| 1 |C |
| 3 |A |
| 3 |E |

## <a name="next-steps"></a>További lépések
Támogatott források és mosdók által a másolási tevékenység során az Azure Data Factory adattárolókhoz listájáért lásd: [adattárolókhoz támogatott](copy-activity-overview.md##supported-data-stores-and-formats).
