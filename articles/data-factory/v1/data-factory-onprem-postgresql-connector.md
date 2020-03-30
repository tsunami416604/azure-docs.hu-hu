---
title: Adatok áthelyezése a PostgreSQL-ből az Azure Data Factory használatával
description: Ismerje meg, hogyan helyezhetát át az adatokat a PostgreSQL adatbázisból az Azure Data Factory használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 888d9ebc-2500-4071-b6d1-0f6bd1b5997c
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 37c83e77cadae002ff701a08c4b36a86f7cab9a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281235"
---
# <a name="move-data-from-postgresql-using-azure-data-factory"></a>Adatok áthelyezése a PostgreSQL-ből az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-onprem-postgresql-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-postgresql.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, olvassa el a [PostgreSQL-összekötő t a V2 alkalmazásban.](../connector-postgresql.md)


Ez a cikk bemutatja, hogyan használhatja a másolási tevékenység az Azure Data Factory adatok áthelyezése egy helyszíni PostgreSQL-adatbázisból. Az [adatmozgatási tevékenységek](data-factory-data-movement-activities.md) cikkre épül, amely általános áttekintést nyújt az adatmozgásról a másolási tevékenységgel.

A helyszíni PostgreSQL-adattárból adatokat másolhat bármely támogatott fogadó adattárba. A másolási tevékenység által fogadóként támogatott adattárak listáját a [támogatott adattárak című témakörben tetszésszerint.](data-factory-data-movement-activities.md#supported-data-stores-and-formats) A Data factory jelenleg támogatja az adatok PostgreSQL adatbázisból más adattárolókba való áthelyezését, de nem azt, hogy az adatokat más adattárakból postgreSQL adatbázisba helyezjék át.

## <a name="prerequisites"></a>Előfeltételek

A Data Factory szolgáltatás támogatja a helyszíni PostgreSQL-forrásokhoz való csatlakozást az adatkezelési átjáró használatával. Tekintse meg [az adatok áthelyezését a helyszíni helyek és a felhőalapú](data-factory-move-data-between-onprem-and-cloud.md) cikk között, és ismerje meg az Adatkezelési átjárót és az átjáró beállításának lépésenkénti útmutatóját.

Átjáró akkor is szükség van, ha a PostgreSQL-adatbázis üzemelteti az Azure IaaS virtuális gép. Az átjárót telepítheti ugyanazon az IaaS virtuális gépre, mint az adattárban, vagy egy másik virtuális gépre, amíg az átjáró csatlakozhat az adatbázishoz.

> [!NOTE]
> A kapcsolatokkal/átjáróval kapcsolatos problémák elhárításával kapcsolatos tippek az [átjáróval kapcsolatos problémák elhárítása](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) című témakörben olvashat.

## <a name="supported-versions-and-installation"></a>Támogatott verziók és telepítés
Ahhoz, hogy az Adatkezelési átjáró csatlakozzon a PostgreSQL adatbázishoz, telepítse a [PostgreSQL Ngpsql adatszolgáltatóját](https://go.microsoft.com/fwlink/?linkid=282716) a 2.0.12 és 3.1.9 közötti verzióval ugyanarra a rendszerre, mint az adatkezelési átjáró. PostgreSQL 7.4-es és újabb verzió támogatott.

## <a name="getting-started"></a>Első lépések
Létrehozhat egy folyamatot egy másolási tevékenységgel, amely különböző eszközök/API-k használatával áthelyezi az adatokat egy helyszíni PostgreSQL-adattárból.

- A folyamat létrehozásának legegyszerűbb módja a **Másolás varázsló**használata. Olvassa el [az oktatóanyagot: Folyamat létrehozása a Másolás varázslóval](data-factory-copy-data-wizard-tutorial.md) című témakörben egy gyors útmutatót a folyamat másolása az adatok másolása varázslóval történő létrehozásához.
- A következő eszközökkel is létrehozhat egy folyamatot:
  - Visual Studio
  - Azure PowerShell
  - Azure Resource Manager-sablon
  - .NET API
  - REST API

    Lásd: [Tevékenység-oktatóanyag másolása](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) című témakörben részletes útmutatást talál egy másolási tevékenységgel rendelkező folyamat létrehozásához.

Függetlenül attól, hogy az eszközöket vagy API-kat használja, a következő lépéseket hajthatja végre egy olyan folyamat létrehozásához, amely adatokat helyezi át a forrásadattárból a fogadó adattárába:

1. **Összekapcsolt szolgáltatások** létrehozása a bemeneti és kimeneti adattárak és az adat-előállító összekapcsolására.
2. **Adatkészletek** létrehozása a másolási művelet bemeneti és kimeneti adatainak ábrázolására.
3. Hozzon létre egy **folyamatot** egy másolási tevékenységgel, amely egy adatkészletet bemenetként, egy adatkészletet pedig kimenetként vesz fel.

A varázsló használatakor a Data Factory entitásokhoz (csatolt szolgáltatások, adatkészletek és a folyamat) json-definíciók automatikusan létrejönnek. Eszközök/API-k használatakor (a .NET API kivételével) ezeket a Data Factory entitásokat a JSON formátum használatával definiálhatja. A helyszíni PostgreSQL-adattatárolóból adatok másolására használt Data Factory-entitások JSON-definícióival rendelkező minta a [JSON-példában: Adatok másolása a PostgreSQL-ből](#json-example-copy-data-from-postgresql-to-azure-blob) az Azure Blob szakaszba című témakörben.

A következő szakaszok a PostgreSQL-adattára jellemző Data Factory-entitások definiálására használt JSON-tulajdonságok részleteit ismertetik:

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai
Az alábbi táblázat a PostgreSQL-hez csatolt szolgáltatásra jellemző JSON-elemek leírását tartalmazza.

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| type |A típustulajdonságnak a következő re van állítva: **OnPremisesPostgreSql** |Igen |
| kiszolgáló |A PostgreSQL kiszolgáló neve. |Igen |
| adatbázis |A PostgreSQL adatbázis neve. |Igen |
| Séma |A séma neve az adatbázisban. A sémaneve a kis- és nagybetűket nem figyelembe. |Nem |
| authenticationType |A PostgreSQL adatbázishoz való csatlakozáshoz használt hitelesítés típusa. Lehetséges értékek: Névtelen, Egyszerű és Windows. |Igen |
| felhasználónév |Adja meg a felhasználónevet, ha alapfokú vagy Windows-hitelesítést használ. |Nem |
| jelszó |Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. |Nem |
| átjárónév |Annak az átjárónak a neve, amelyet a Data Factory szolgáltatásnak a helyszíni PostgreSQL-adatbázishoz való csatlakozáshoz kell használnia. |Igen |

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai
Az adatkészletek definiálására szolgáló & tulajdonságok teljes listáját az [Adatkészletek létrehozása](data-factory-create-datasets.md) című cikkben olvashat. A JSON adatkészletek szerkezete, rendelkezésre állása és házirendje minden adatkészlettípushoz hasonlóak.

A typeProperties szakasz az adatkészlet egyes típusainál eltérő, és tájékoztatást nyújt az adatok helyéről az adattárban. A **RelationalTable** típusú adatkészlet typeProperties szakasza (amely a PostgreSQL adatkészletet is tartalmazza) a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| tableName |A tábla neve a PostgreSQL adatbázis-példányban, amelyre a csatolt szolgáltatás hivatkozik. A táblanév a kis- és nagybetűk et is figyelembe nem egyezteti. |Nem (ha a **RelationalSource** **lekérdezése** meg van adva) |

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
A tevékenységek definiálására rendelkezésre álló szakaszok & tulajdonságok teljes listáját a [Folyamatok létrehozása](data-factory-create-pipelines.md) című cikkben olvashat. Tulajdonságok, például név, leírás, bemeneti és kimeneti táblák és házirend állnak rendelkezésre minden típusú tevékenységek.

Mivel a tevékenység typeProperties szakaszában elérhető tulajdonságok az egyes tevékenységtípusoktól függően változnak. Másolási tevékenység esetén a források és a fogadók típusától függően változnak.

Ha a forrás **RelationalSource** típusú (amely tartalmazza a PostgreSQL-t is), a következő tulajdonságok érhetők el a typeProperties szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| lekérdezés |Az adatok olvasásához használja az egyéni lekérdezést. |SQL lekérdezési karakterlánc. Például: `"query": "select * from \"MySchema\".\"MyTable\""`. |Nem (ha **a tableName** of **dataset** meg van adva) |

> [!NOTE]
> A séma- és táblanevekben a kis- és nagybetűket nem lehet figyelembe. A lekérdezésbe `""` (dupla idézőjelek) mellékeljük őket.

**Példa:**

 `"query": "select * from \"MySchema\".\"MyTable\""`

## <a name="json-example-copy-data-from-postgresql-to-azure-blob"></a>JSON-példa: Adatok másolása a PostgreSQL-ről az Azure Blobba
Ebben a példában minta JSON-definíciók, amelyek segítségével hozzon létre egy folyamatot a [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy az [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)használatával. Ezek azt mutatják, hogyan másolhatja az adatokat a PostgreSQL-adatbázisból az Azure Blob Storage-ba. Azonban az adatok átmásolhatók az [itt](data-factory-data-movement-activities.md#supported-data-stores-and-formats) megadott fogadók bármelyikébe az Azure Data Factory másolási tevékenység használatával.

> [!IMPORTANT]
> Ez a minta JSON-kódrészleteket biztosít. Nem tartalmazza az adat-előállító létrehozásának lépésenkénti útmutatóit. Tekintse meg [az adatok áthelyezését a helyszíni helyek és](data-factory-move-data-between-onprem-and-cloud.md) a felhőalapú cikk között, és részletes útmutatást talál.

A minta a következő adatfeldolgozó entitásokkal rendelkezik:

1. [OnPremisesPostgreSql](data-factory-onprem-postgresql-connector.md#linked-service-properties)típusú összekapcsolt szolgáltatás.
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)típusú kapcsolt szolgáltatás.
3. [RelationalTable](data-factory-onprem-postgresql-connector.md#dataset-properties)típusú bemeneti [adatkészlet.](data-factory-create-datasets.md)
4. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)típusú kimeneti [adatkészlet.](data-factory-create-datasets.md)
5. A [RelationalSource](data-factory-onprem-postgresql-connector.md#copy-activity-properties) és [a BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)programot használó másolási tevékenységet használó [folyamat.](data-factory-create-pipelines.md)

A minta óránként másolja az adatokat egy lekérdezéseredményből a PostgreSQL-adatbázisból egy blobba. Az ezekben a mintákban használt JSON-tulajdonságokat a mintákat követő szakaszok ismertetik.

Első lépésként állítsa be az adatkezelési átjárót. Az utasítások a helyszíni helyek és a felhőalapú cikk [közötti átmozgatási](data-factory-move-data-between-onprem-and-cloud.md) adatokban találhatók.

**PostgreSQL kapcsolt szolgáltatás:**

```json
{
    "name": "OnPremPostgreSqlLinkedService",
    "properties": {
        "type": "OnPremisesPostgreSql",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```
**Azure Blob storage-hoz csatolt szolgáltatás:**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
        }
    }
}
```
**PostgreSQL bemeneti adatkészlet:**

A minta feltételezi, hogy létrehozott egy "MyTable" táblát a PostgreSQL-ben, és tartalmaz egy "időbélyeg" nevű oszlopot az idősorozat-adatokhoz.

A `"external": true` beállítás tájékoztatja a Data Factory szolgáltatást, hogy az adatkészlet az adat-előállítón kívül található, és nem az adat-előállító tevékenység által előállított.

```json
{
    "name": "PostgreSqlDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremPostgreSqlLinkedService",
        "typeProperties": {},
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

**Azure Blob kimeneti adatkészlet:**

Az adatok óránként egy új blobba (gyakoriság: óra, időköz: 1) kerül beírásra. A blob mappaelérési útja és fájlneve dinamikusan kiértékelésre kerül a feldolgozás alatt álló szelet kezdési időpontja alapján. A mappa elérési útja a kezdési időpont év-, hónap-, nap- és órarészeit használja.

```json
{
    "name": "AzureBlobPostgreSqlDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/postgresql/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Folyamat másolási tevékenységgel:**

A folyamat tartalmaz egy másolási tevékenységet, amely a bemeneti és kimeneti adatkészletek használatára van konfigurálva, és az óránkénti futtatásra van ütemezve. A folyamat JSON-definíciójában a **forrástípus** **RelationalSource** lesz állítva, **a fogadó** típusa pedig **BlobSink**. A **lekérdezési** tulajdonsághoz megadott SQL-lekérdezés a PostgreSQL-adatbázis public.usstates táblájából választja ki az adatokat.

```json
{
    "name": "CopyPostgreSqlToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "select * from \"public\".\"usstates\""
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "inputs": [
                    {
                        "name": "PostgreSqlDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobPostgreSqlDataSet"
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
                "name": "PostgreSqlToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```
## <a name="type-mapping-for-postgresql"></a>A PostgreSQL típusleképezése
Az [adatmozgatási tevékenységek](data-factory-data-movement-activities.md) cikkben említettek szerint a Másolástevékenység automatikus típuskonverziókat hajt végre a forrástípusokról a fogadótípusokra a következő kétlépéses megközelítéssel:

1. Konvertálás natív forrástípusokból .NET-típussá
2. Konvertálás .NET típusból natív fogadótípussá

Amikor adatokat helyez át a PostgreSQL-be, a következő leképezéseket használja a PostgreSQL típusról a .NET típusra.

| PostgreSQL adatbázis típusa | PostgresSQL aliasok | .NET keretrendszer típusa |
| --- | --- | --- |
| abstime | |Datetime |
| bigint |int8 |Int64 |
| bigsoros között |soros8 |Int64 |
| bit [(n)] | |Bájt[], karakterlánc |
| bit változó [ (n) ] |varbit között |Bájt[], karakterlánc |
| logikai |Bool |Logikai |
| Doboz | |Bájt[], karakterlánc |
| bájt | |Bájt[], karakterlánc |
| karakter [(n)] |karakter [(n)] |Sztring |
| karakter változó [(n)] |varchar [(n)] |Sztring |
| Cid | |Sztring |
| cidr | |Sztring |
| kör | |Bájt[], karakterlánc |
| dátum | |Datetime |
| dátumtartomány | |Sztring |
| dupla pontosság |úszó8 |Double |
| inet | |Bájt[], karakterlánc |
| intarry (intarry) | |Sztring |
| int4range | |Sztring |
| int8range | |Sztring |
| egész szám |int, int4 |Int32 |
| intervallum [mezők] [(p)] | |Időtartomány |
| json | |Sztring |
| jsonb között | |Bájt[] |
| vonal | |Bájt[], karakterlánc |
| lseg között | |Bájt[], karakterlánc |
| macaddr között | |Bájt[], karakterlánc |
| Pénzt | |Decimal |
| numerikus [(p, s)] |decimális [(p, s)] |Decimal |
| numrange (számtartomány) | |Sztring |
| Oid | |Int32 |
| path | |Bájt[], karakterlánc |
| pg_lsn | |Int64 |
| Pont | |Bájt[], karakterlánc |
| Sokszög | |Bájt[], karakterlánc |
| real |úszó4 |Egyirányú |
| smallint |int2 |Int16 |
| smallserial között |soros2 |Int16 |
| Sorozat |soros4 |Int32 |
| szöveg | |Sztring |

## <a name="map-source-to-sink-columns"></a>Forrás leképezése oszlopokhoz
Ha többet szeretne tudni arról, hogy a forrásadatkészlet oszlopait a fogadó adatkészlet oszlopaihoz szeretné-e leképezni, olvassa [el az Adatkészletoszlopok leképezése az Azure Data Factoryban című témakört.](data-factory-map-columns.md)

## <a name="repeatable-read-from-relational-sources"></a>Relációs forrásokból ismételhető olvasmony
Ha relációs adattárakból másolja az adatokat, tartsa szem előtt az ismételhetőséget a nem kívánt eredmények elkerülése érdekében. Az Azure Data Factoryban manuálisan futtathatja a szeletet. Az adatkészlet újrapróbálkozási házirendje is konfigurálható, így a szelet újrafut, ha hiba történik. Ha egy szeletet mindkét irányban újrafuttat, meg kell győződnie arról, hogy ugyanazokat az adatokat olvassa el, függetlenül attól, hogy hányszor fut egy szelet. Lásd: [Ismételhető olvasás relációs forrásokból](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Teljesítmény és hangolás
A [Tevékenység teljesítményének másolása & hangolási útmutatóban](data-factory-copy-activity-performance.md) megismerést talál az adatok (másolási tevékenység) azure Data Factory ban az adatmozgatás (másolási tevékenység) teljesítményét befolyásoló legfontosabb tényezőkről, valamint az optimalizálás különböző módjairól.
