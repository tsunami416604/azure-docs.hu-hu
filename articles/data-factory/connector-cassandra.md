---
title: Adatok másolása a Cassandrából az Azure Data Factory használatával
description: Megtudhatja, hogyan másolhatja az adatokat a Cassandra a támogatott fogadó adattárak használatával egy másolási tevékenység egy Azure Data Factory-folyamat.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: 6aac3f1bb29ccfab0de4cb0a807ca0831a62af7e
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991552"
---
# <a name="copy-data-from-cassandra-using-azure-data-factory"></a>Adatok másolása a Cassandrából az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-onprem-cassandra-connector.md)
> * [Aktuális verzió](connector-cassandra.md)

Ez a cikk ismerteti, hogyan használhatja a másolási tevékenység az Azure Data Factory adatok másolása a Cassandra adatbázisból. A [másolási tevékenység áttekintése](copy-activity-overview.md) cikkre épül, amely a másolási tevékenység általános áttekintését mutatja be.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez a Cassandra-összekötő a következő tevékenységek esetén támogatott:

- [Tevékenység másolása](copy-activity-overview.md) [támogatott forrás/fogadó mátrixcal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

A Cassandra adatbázisból adatokat másolhat bármely támogatott fogadó adattárba. A másolási tevékenység által forrásként/fogadóként támogatott adattárak listáját a [Támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblában található.

Pontosabban, ez a Cassandra csatlakozó támogatja:

- Cassandra **verziók 2.x és 3.x**.
- Adatok másolása **alapszintű** vagy **névtelen** hitelesítéssel.

>[!NOTE]
>A saját üzemeltetésű integrációs futtatóön futó tevékenységek esetében a Cassandra 3.x a 3.7-es vagy újabb infravörös verzió óta támogatott.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

Az integrációs futásidejű beépített Cassandra-illesztőprogramot biztosít, ezért nem kell manuálisan telepítenie egyetlen illesztőprogramot sem, amikor adatokat másol/szeretne Cassandrába.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok a Cassandra-összekötőre jellemző Data Factory-entitások definiálásához használt tulajdonságok részleteit ismertetik.

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai

A Cassandra csatolt szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type |A típustulajdonságnak a következőre kell állítani: **Cassandra** |Igen |
| gazda |Cassandra kiszolgálók egy vagy több IP-címe vagy állomásneve.<br/>Adja meg az IP-címek vagy állomásnevek vesszővel tagolt listáját, hogy az összes kiszolgálóhoz egyidejűleg kapcsolódhassanak. |Igen |
| port |A Cassandra-kiszolgáló által az ügyfélkapcsolatok figyelésére használt TCP-port. |Nem (az alapértelmezett érték 9042) |
| authenticationType | A Cassandra adatbázishoz való csatlakozáshoz használt hitelesítés típusa.<br/>Az engedélyezett értékek a következők: **Alap**szintű és **Névtelen**. |Igen |
| felhasználónév |Adja meg a felhasználói fiók felhasználónevét. |Igen, ha az authenticationType alapszintű. |
| jelszó |Adja meg a felhasználói fiók jelszavát. Jelölje meg ezt a mezőt SecureStringként a Data Factory biztonságos tárolásához, vagy [hivatkozzon az Azure Key Vaultban tárolt titkos fájlokra.](store-credentials-in-key-vault.md) |Igen, ha az authenticationType alapszintű. |
| connectVia | Az adattárhoz való csatlakozáshoz használandó [integrációs futásidő.](concepts-integration-runtime.md) További információ az [Előfeltételek](#prerequisites) szakaszból. Ha nincs megadva, az alapértelmezett Azure-integrációs runtime-ot használja. |Nem |

>[!NOTE]
>Jelenleg a TLS használatával lévő Cassandra-hoz való csatlakozás nem támogatott.

**Példa:**

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

Az adatkészletek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját az [adatkészletekről](concepts-datasets-linked-services.md) szóló cikkben olvashatja. Ez a szakasz a Cassandra adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Ha adatokat szeretne másolni a Cassandrából, állítsa az adatkészlet típustulajdonságát **CassandraTable**beállításra. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | Az adatkészlet típustulajdonságát a következőre kell állítani: **CassandraTable** | Igen |
| kulcstér |A Cassandra adatbázis kulcsterének vagy sémának a neve. |Nem (ha a "CassandraSource" "lekérdezés" van megadva) |
| tableName |A Cassandra adatbázis táblájának neve. |Nem (ha a "CassandraSource" "lekérdezés" van megadva) |

**Példa:**

```json
{
    "name": "CassandraDataset",
    "properties": {
        "type": "CassandraTable",
        "typeProperties": {
            "keySpace": "<keyspace name>",
            "tableName": "<table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Cassandra linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai


A tevékenységek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját a [Folyamatok](concepts-pipelines-activities.md) című cikkben olvashat. Ez a szakasz a Cassandra-forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="cassandra-as-source"></a>Cassandra mint forrás

Ha adatokat szeretne másolni a Cassandrából, állítsa be a forrástípusát a másolási tevékenységben a **CassandraSource lehetőségre.** A másolási tevékenység **forrásszakaszában** a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának típustulajdonságát a következőre kell állítani: **CassandraSource** | Igen |
| lekérdezés |Az adatok olvasásához használja az egyéni lekérdezést. SQL-92 lekérdezés vagy CQL-lekérdezés. Lásd [cql hivatkozás](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>SQL-lekérdezés használatakor adja meg **a keyspace name.table nevet** a lekérdezni kívánt tábla ábrázolására. |Nem (ha az adatkészletben a "tableName" és a "keyspace" van megadva). |
| konzisztenciaSzint |A konzisztenciaszint azt határozza meg, hogy hány replikának kell válaszolnia egy olvasási kérelemre, mielőtt adatokat ad vissza az ügyfélalkalmazásnak. Cassandra ellenőrzi a megadott számú replikák adatok at az olvasási kérelem teljesítéséhez. A részleteket az [Adatkonzisztencia konfigurálása](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) című témakörben találja.<br/><br/>Megengedett értékek: **egy,** **kettő,** **három,** **határozatképesség**, **MIND**, **LOCAL_QUORUM**, **EACH_QUORUM**és **LOCAL_ONE.** |Nem (az `ONE`alapértelmezett ) |

**Példa:**

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

## <a name="data-type-mapping-for-cassandra"></a>Cassandra adattípus-leképezése

A Cassandra adatok másolásakor a következő leképezések a Cassandra adattípusokból az Azure Data Factory köztes adattípusai. A [Séma- és adattípus-hozzárendelések](copy-activity-schema-and-type-mapping.md) című témakörből megtudhatja, hogy a másolási tevékenység hogyan képezi le a forrássémát és az adattípust a fogadóhoz.

| Cassandra adattípus | Adatgyár köztes adattípusa |
|:--- |:--- |
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

## <a name="work-with-collections-using-virtual-table"></a>Gyűjtemények használata virtuális táblával

Az Azure Data Factory egy beépített ODBC-illesztőprogramot használ a Cassandra-adatbázishoz való csatlakozáshoz és az adatok másolásához. A gyűjteménytípusok, beleértve a térképet, a készletet és a listát, az illesztőprogram újranormalizálja az adatokat a megfelelő virtuális táblákba. Ha egy tábla gyűjteményoszlopokat tartalmaz, az illesztőprogram a következő virtuális táblákat hozza létre:

* Egy **alaptábla**, amely ugyanazokat az adatokat tartalmazza, mint a valódi tábla, kivéve a gyűjtemény oszlopokat. Az alaptábla ugyanazt a nevet használja, mint az általa képviselt valódi tábla.
* Virtuális **tábla** minden egyes gyűjteményoszlophoz, amely kibővíti a beágyazott adatokat. A gyűjteményeket képviselő virtuális táblák elnevezése a valódi tábla, az elválasztó "*vt*" és az oszlop neve alapján kerül elnevezésre.

A virtuális táblák a valós táblában lévő adatokra hivatkoznak, így az illesztőprogram hozzáférhet a denormalizált adatokhoz. A részleteket lásd a Példa szakaszban. Cassandra-gyűjtemények tartalmát a virtuális táblák lekérdezésével és az azokhoz való csatlakozással érheti el.

### <a name="example"></a>Példa

A következő "ExampleTable" például egy Cassandra adatbázistábla, amely egy "pk_int" nevű egész elsődleges kulcsoszlopot, egy érték nevű szövegoszlopot, egy listaoszlopot, egy térképoszlopot és egy "StringSet" nevű készletoszlopot tartalmaz.

| pk_int | Érték | Lista | Térkép | Karakterlánc-készlet |
| --- | --- | --- | --- | --- |
| 1 |"1. mintaérték" |["1", "2", "3"] |{"S1": "a", "S2": "b"} |{"A", "B", "C"} |
| 3 |"3. mintaérték" |["100", "101", "102", "105"] |{"S1": "t"} |{"A", "E"} |

Az illesztőprogram több virtuális táblát hozna létre, amelyek ezt az egyetlen táblát képviselik. A virtuális táblák idegen kulcsoszlopai a valós tábla elsődleges kulcsoszlopaira hivatkoznak, és jelzik, hogy a virtuális táblasor melyik valós táblasornak felel meg.

Az első virtuális tábla az "ExampleTable" nevű alaptábla az alábbi táblázatban látható: 

| pk_int | Érték |
| --- | --- |
| 1 |"1. mintaérték" |
| 3 |"3. mintaérték" |

Az alaptábla ugyanazokat az adatokat tartalmazza, mint az eredeti adatbázistábla, kivéve a gyűjteményeket, amelyek kimaradnak ebből a táblából, és más virtuális táblákban vannak kibontva.

Az alábbi táblázatok azokat a virtuális táblákat mutatják be, amelyek újranormalizálják a Lista, a Térkép és a StringSet oszlopokból származó adatokat. Az "_index" vagy "_key" végződésű oszlopok jelzik az adatok helyét az eredeti listában vagy térképen. Az "_value" végződésű oszlopok a gyűjteményből származó kibontott adatokat tartalmazzák.

**"ExampleTable_vt_List" táblázat:**

| pk_int | List_index | List_value |
| --- | --- | --- |
| 1 |0 |1 |
| 1 |1 |2 |
| 1 |2 |3 |
| 3 |0 |100 |
| 3 |1 |101 |
| 3 |2 |102 |
| 3 |3 |103 |

**"ExampleTable_vt_Map" táblázat:**

| pk_int | Map_key | Map_value |
| --- | --- | --- |
| 1 |S1 |A |
| 1 |S2 |b |
| 3 |S1 |t |

**"ExampleTable_vt_StringSet" táblázat:**

| pk_int | StringSet_value |
| --- | --- |
| 1 |A |
| 1 |B |
| 1 |C# |
| 3 |A |
| 3 |E |

## <a name="lookup-activity-properties"></a>A keresgaszíntevékenység tulajdonságai

A tulajdonságokrészleteinek megismeréséhez ellenőrizze a [Kereskövetési tevékenységet.](control-flow-lookup-activity.md)

## <a name="next-steps"></a>További lépések
A forrásként támogatott és fogadóként az Azure Data Factory másolási tevékenysége által támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats)című témakörben tetszhet.
