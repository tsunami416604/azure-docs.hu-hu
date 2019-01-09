---
title: Adatok áthelyezése az Azure Data Factory használatával DB2 |} A Microsoft Docs
description: 'Útmutató: adatok áthelyezése egy helyszíni DB2-adatbázisból az Azure Data Factory másolási tevékenység használatával'
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: c1644e17-4560-46bb-bf3c-b923126671f1
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: c7a3893c35031d05ea8aade0ad5d30b5a56176fd
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54015134"
---
# <a name="move-data-from-db2-by-using-azure-data-factory-copy-activity"></a>Adatok áthelyezése az Azure Data Factory másolási tevékenység használatával DB2
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-onprem-db2-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-db2.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a jelenlegi verzió a Data Factory szolgáltatás használ, tekintse meg [DB2-összekötő a v2-ben](../connector-db2.md).


Ez a cikk bemutatja, hogyan használhatja a másolási tevékenység az Azure Data Factory adatokat másol egy helyszíni DB2-adatbázis egy adattárba. A bármilyen, amely szerepel a támogatott fogadóként is másolhatja az adatokat a [Data Factory adatáthelyezési tevékenységek](data-factory-data-movement-activities.md#supported-data-stores-and-formats) cikk. Ez a témakör a Data Factory című cikket, amely áttekintést nyújt az adatok áthelyezése másolási tevékenység használatával, és felsorolja a támogatott data store kombinációk épül. 

A Data Factory jelenleg csak helyez át adatokat egy DB2-adatbázishoz való támogatja egy [támogatott fogadó adattárba](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Egy DB2-adatbázis nem támogatott, amely adatokat helyez át más adatokat tárolja.

## <a name="prerequisites"></a>Előfeltételek
A Data Factory támogatja használó csatlakozik egy helyszíni DB2-adatbázishoz a [adatkezelési átjáró](data-factory-data-management-gateway.md). Állítsa be az átjáró adatfolyamat az adatok áthelyezése a lépésenkénti útmutatójáért lásd: a [adatok áthelyezése a helyszínről a felhőbe](data-factory-move-data-between-onprem-and-cloud.md) cikk.

Egy átjáróra szükség, akkor is, ha Azure IaaS virtuális Gépen lévő üzemeltetett a DB2. Az átjáró telepíthető adattárként azonos IaaS virtuális Gépen. Ha az átjáró képes kapcsolódni az adatbázishoz, az átjáró is telepítheti egy másik virtuális gépen.

Az adatkezelési átjárót egy beépített DB2-illesztőprogram biztosít, így nem kell manuálisan az adatok másolása a DB2-illesztőprogram telepítése.

> [!NOTE]
> Tippek a kapcsolat és az átjáró-problémák hibaelhárítása: a [gateway hibáinak elhárítása](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) cikk.


## <a name="supported-versions"></a>Támogatott verziók
A Data Factory DB2-összekötő a következő IBM DB2-platformokat és verziókat elosztott relációs adatbázis architektúra (DRDA) SQL Access Manager verziókkal 9, 10-es és 11 támogatja:

* Z/os verziót 11.1 IBM DB2-höz
* Z/os verziót 10.1 IBM DB2-höz
* I (AS400) verzió 7.2 IBM DB2-höz
* I (AS400) 7.1-es verzió az IBM DB2-höz
* IBM DB2, Linux, UNIX és a Windows (LUW) 11-es verzió
* A verzió 10,5 LUW IBM DB2-höz
* A verzió 10.1 LUW IBM DB2-höz

> [!TIP]
> Ha a hibaüzenet jelenhet meg: "egy SQL-utasítás végrehajtási kérelméhez tartozó csomag nem található. SQLSTATE 51002 SQLCODE =-805, = "oka szükséges csomag nem a normál felhasználó az operációs rendszer számára jön létre. A probléma megoldásához kövesse ezeket az utasításokat a DB2-kiszolgáló típusának:
> - A DB2 i (AS400): Lehetővé teszik a kiemelt felhasználó, a másolási tevékenység futtatása előtt a normál felhasználói gyűjtemény létrehozása. A gyűjtemény létrehozásához használja a parancsot: `create collection <username>`
> - A – z/OS- vagy LUW DB2: A magas jogosultságú fiók – kiemelt felhasználó vagy rendszergazda, amelynek a csomag hatóságok és kötési, BINDADD, támogatás hajtsa végre a nyilvános engedélyeket – egyszer futtatni a másolási használata. A szükséges csomag automatikusan jön létre a másolás során. A későbbiekben válthat a normál felhasználó számára az ezt követő másolási futtatások.

## <a name="getting-started"></a>Első lépések
Létrehozhat egy folyamatot egy másolási tevékenységgel az adatok áthelyezése a helyszíni DB2 adattárolókból a különböző eszközök és API-k használatával: 

- A folyamat létrehozásának legegyszerűbb módja, hogy az Azure Data Factory Copy varázslót használja. A folyamatot a másolás varázsló használatával történő létrehozásának egy gyors bemutatóért lásd: a [oktatóanyag: Hozzon létre egy folyamatot a másolás varázsló használatával](data-factory-copy-data-wizard-tutorial.md). 
- Eszközök segítségével hozzon létre egy folyamatot, beleértve az Azure Portalon, a Visual Studio, az Azure PowerShell, Azure Resource Manager-sablon, a .NET API és a REST API-t. Egy másolási tevékenységgel ellátott adatcsatorna létrehozása a lépésenkénti útmutatójáért lásd: a [másolási tevékenység oktatóanyagát](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). 

Az eszközök vagy az API-kat használja, hogy létrehoz egy folyamatot, amely a helyez át adatokat egy forrásadattárból egy fogadó adattárba a következő lépéseket fogja végrehajtani:

1. A bemeneti és kimeneti adattárak az adat-előállítóhoz társított szolgáltatások létrehozása.
2. Adatkészleteket hoz létre, a másolási művelet bemeneti és kimeneti adatokat képviselik. 
3. Létrehoz egy folyamatot egy másolási tevékenység, amely egy adatkészletet bemenetként, és a egy adatkészletet pedig kimenetként. 

Ha használja a Másolás varázslót, a JSON-definíciók az adat-Előállítóhoz társított szolgáltatások, adatkészletek és folyamat entitásokat automatikusan jönnek létre az Ön számára. Amikor az eszközök vagy az API-k (kivéve a .NET API), meghatározhatja a Data Factory-entitások a JSON formátumban. A [JSON-példa: Adatok másolása az Azure Blob storage DB2](#json-example-copy-data-from-db2-to-azure-blob) jeleníti meg, amely adatokat másol egy helyszíni DB2-adattár segítségével a Data Factory-entitások a JSON-definíciói.

A következő szakaszok konkrétan egy DB2-adattárat a Data Factory-entitások definiálásához használt JSON-tulajdonságokkal kapcsolatos részleteket.

## <a name="db2-linked-service-properties"></a>DB2-beli társított szolgáltatás tulajdonságai
Az alábbi táblázat jellemző egy DB2-beli társított szolgáltatás JSON-tulajdonságokat.

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| **type** |Ezt a tulajdonságot állítsa **OnPremisesDb2**. |Igen |
| **server** |A DB2-kiszolgáló neve. |Igen |
| **database** |A DB2-adatbázis neve. |Igen |
| **schema** |A séma, a DB2-adatbázis neve. Ez a tulajdonság nem kis-és nagybetűket. |Nem |
| **authenticationType** |A DB2-adatbázishoz való kapcsolódáshoz használt hitelesítés típusa. A lehetséges értékek a következők: Névtelen, alapszintű és a Windows. |Igen |
| **felhasználónév** |Alapszintű vagy Windows-hitelesítés használata esetén a felhasználói fiók nevét. |Nem |
| **jelszó** |A felhasználói fiók jelszava. |Nem |
| **gatewayName** |Az átjáró által a Data Factory szolgáltatás a helyszíni DB2-adatbázishoz való csatlakozáshoz használandó neve. |Igen |

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai
A szakaszok és definiálása az adatkészletek rendelkezésre álló tulajdonságok listáját lásd: a [adatkészletek létrehozása](data-factory-create-datasets.md) cikk. Részei, mint például **struktúra**, **rendelkezésre állási**, és a **házirend** JSON adatkészletek hasonlóak az összes adatkészlet esetében (az Azure SQL, Azure Blob storage, az Azure Table storage és így tovább).

A **typeProperties** szakasz eltérő az egyes adatkészlet, és az adattárban lévő adatok helyét ismerteti. A **typeProperties** szakasz egy adatkészlet típusú **RelationalTable**, amely tartalmazza a DB2-adathalmaz rendelkezik a következő tulajdonság:

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| **Táblanév** |A tábla a DB2-adatbázispéldányban, amely hivatkozik a társított szolgáltatás neve. Ez a tulajdonság nem kis-és nagybetűket. |Nem (Ha a **lekérdezés** egy másolási tevékenységgel típusú tulajdonsága **RelationalSource** van megadva) |

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
A szakaszok és másolási tevékenységek definiálását tulajdonságok listáját lásd: a [folyamatok létrehozása](data-factory-create-pipelines.md) cikk. Például a másolási tevékenység tulajdonságai, **neve**, **leírása**, **bemenetek** tábla, **kimenete** táblát, és **házirend**, minden típusú tevékenységek érhetők el. Az elérhető tulajdonságok a **typeProperties** a tevékenység szakaszban eltérőek lehetnek a tevékenységek minden típusának. A másolási tevékenység a tulajdonságok a típusú adatok forrásként és fogadóként változhat.

A másolási tevékenység, ha a forrás típusa **RelationalSource** (amely tartalmazza a DB2), a következő tulajdonságok érhetők el a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| **Lekérdezés** |Az egyéni lekérdezés használata az adatok olvasásához. |SQL-lekérdezési karakterláncot. Például:`"query": "select * from "MySchema"."MyTable""` |Nem (Ha a **tableName** adatkészlet tulajdonság meg van adva) |

> [!NOTE]
> Séma-és tábla-és nagybetűk. A lekérdezési utasítás az idézőjelek közé kell tenni a tulajdonságnevek használatával "" (dupla idézőjel).

## <a name="json-example-copy-data-from-db2-to-azure-blob-storage"></a>JSON-példa: DB2 az Azure Blob storage-adatok másolása
Ebben a példában példa JSON-definíciók, amelyek segítségével hozzon létre egy folyamatot biztosít a [az Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), vagy [Azure PowerShell-lel](data-factory-copy-activity-tutorial-using-powershell.md). A példa bemutatja, hogyan másolhat adatokat egy DB2-adatbázisból Blob Storage. Azonban adatok átmásolhatók [valamennyi támogatott adat tárolása a fogadó típusa](data-factory-data-movement-activities.md#supported-data-stores-and-formats) Azure Data Factory másolási tevékenység használatával.

A minta az alábbi Data Factory-entitások rendelkezik:

- Egy DB2-beli társított szolgáltatást típusú [OnPremisesDb2](data-factory-onprem-db2-connector.md#linked-service-properties)
- Egy Azure Blob storage társított szolgáltatás típusa [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
- Egy bemeneti [adatkészlet](data-factory-create-datasets.md) típusú [RelationalTable](data-factory-onprem-db2-connector.md#dataset-properties)
- Kimenet [adatkészlet](data-factory-create-datasets.md) típusú [Azure Blobba](data-factory-azure-blob-connector.md#dataset-properties)
- A [folyamat](data-factory-create-pipelines.md) egy másolási tevékenységgel, amely használja a [RelationalSource](data-factory-onprem-db2-connector.md#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) tulajdonságai

A minta adatokat másol egy lekérdezés eredménye egy DB2-adatbázishoz az Azure-blobba óránként. A példában használt JSON-tulajdonságokat az entitás meghatározásokat a következő szakaszok ismertetik.

Első lépésként telepítse, és konfigurálja a data gateway. Utasítások találhatók a [adatok áthelyezése a helyszíni és a felhő között](data-factory-move-data-between-onprem-and-cloud.md) cikk.

**DB2-beli társított szolgáltatás**

```json
{
    "name": "OnPremDb2LinkedService",
    "properties": {
        "type": "OnPremisesDb2",
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

**Az Azure Blob storage-beli társított szolgáltatás**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorageLinkedService",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
        }
    }
}
```

**Bemeneti adatkészlet DB2**

A minta azt feltételezi, hogy létrehozott egy táblát DB2 "MyTable", amely rendelkezik az idősorozat-adatok a "timestamp" címkével ellátott oszlop neve.

A **külső** tulajdonság értéke "true". Ez a beállítás a Data Factory szolgáltatás tájékoztatja az, hogy ez az adatkészlet a data factory a külső, és nem egy adat-előállító tevékenység által előállított. Figyelje meg, hogy a **típus** tulajdonsága **RelationalTable**.


```json
{
    "name": "Db2DataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremDb2LinkedService",
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

**Azure blobkimeneti adatkészlet**

Adatok írása egy új blob minden órában beállításával a **gyakorisága** "Hour" tulajdonságot és a **időköz** tulajdonságot 1-re. A **folderPath** tulajdonság a blob dinamikusan abban az esetben a alapján a feldolgozás alatt álló szelet kezdő időpontja. A mappa elérési útját használja, az év, hónap, nap és a kezdési időpont órás részeit.

```json
{
    "name": "AzureBlobDb2DataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/db2/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**A másolási tevékenység folyamat**

A folyamat egy másolási tevékenység, amely a megadott bemeneti és kimeneti adatkészleteket van konfigurálva, és amely ütemezett óránként tartalmazza. A folyamat JSON-definíciójában a **forrás** típusa **RelationalSource** és a **fogadó** típusa **BlobSink**. A megadott SQL-lekérdezést a **lekérdezés** tulajdonság kiválasztja az adatokat az "Orders" táblából.

```json
{
    "name": "CopyDb2ToBlob",
    "properties": {
        "description": "pipeline for the copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "select * from \"Orders\""
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "inputs": [
                    {
                        "name": "Db2DataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobDb2DataSet"
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
                "name": "Db2ToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```

## <a name="type-mapping-for-db2"></a>DB2-leképezés típusa
Ahogy korábban már említettük, az a [adattovábbítási tevékenységek](data-factory-data-movement-activities.md) cikk, a másolási tevékenység hajt végre automatikus típuskonverziók a fogadó típusa a következő kétlépéses módszer használatával a forrás típusa:

1. Egy natív forrás típusa átalakítása typ .NET
2. Typ .NET átalakítása egy natív fogadó típusa

A következő hozzárendeléseket használják, amikor a másolási tevékenység alakítja az adatokat egy DB2 írja be a .NET-típus:

| DB2-adatbázis típusa | .NET-keretrendszer típusa |
| --- | --- |
| SmallInt |Int16 |
| Egész szám |Int32 |
| BigInt |Int64 |
| Real |Önálló |
| Dupla |Dupla |
| Lebegőpontos |Dupla |
| tizedes tört |Tizedes tört |
| DecimalFloat |Tizedes tört |
| Numerikus |Tizedes tört |
| Dátum |DateTime |
| Time |Időtartam |
| Időbélyeg |DateTime |
| Xml |Byte] |
| CHAR |Karakterlánc |
| VarChar |Karakterlánc |
| LongVarChar |Karakterlánc |
| DB2DynArray |Karakterlánc |
| Bináris |Byte] |
| VarBinary |Byte] |
| LongVarBinary |Byte] |
| Kép |Karakterlánc |
| VarGraphic |Karakterlánc |
| LongVarGraphic |Karakterlánc |
| CLOB |Karakterlánc |
| Blob |Byte] |
| DbClob |Karakterlánc |
| SmallInt |Int16 |
| Egész szám |Int32 |
| BigInt |Int64 |
| Real |Önálló |
| Dupla |Dupla |
| Lebegőpontos |Dupla |
| tizedes tört |Tizedes tört |
| DecimalFloat |Tizedes tört |
| Numerikus |Tizedes tört |
| Dátum |DateTime |
| Time |Időtartam |
| Időbélyeg |DateTime |
| Xml |Byte] |
| CHAR |Karakterlánc |

## <a name="map-source-to-sink-columns"></a>A fogadó-oszlopok térkép forrása
A forrásadatkészlet oszlopok leképezése a fogadó-adatkészlet az oszlopok kapcsolatban lásd: [az Azure Data Factoryban adatkészletoszlopok leképezése](data-factory-map-columns.md).

## <a name="repeatable-reads-from-relational-sources"></a>A relációs források megismételhető olvasások
Amikor adatokat másol egy relációs adattároló, ismételhetőség tartsa szem előtt, nem kívánt eredmények elkerülése érdekében. Az Azure Data Factoryben futtathatja a szelet manuálisan. Beállíthatja az ismételt **házirend** tulajdonságot egy adatkészlet szelet újrafuttatása, ha hiba történik. Győződjön meg arról, hogy ugyanazokat az adatokat függetlenül attól, hogy hány alkalommal fut újra a szeletet, és hogyan futtassa újból a szeletet függetlenül van-e olvasni. További információkért lásd: [Repeatable beolvassa a relációs források](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Teljesítmény és finomhangolás
További információ a másolási tevékenység és a teljesítmény optimalizálása teljesítményét befolyásoló legfontosabb tényezők a [másolási tevékenységek teljesítményéről és finomhangolási útmutató](data-factory-copy-activity-performance.md).
