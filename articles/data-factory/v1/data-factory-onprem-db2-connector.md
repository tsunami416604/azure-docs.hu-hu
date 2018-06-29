---
title: Adatok áthelyezése DB2 Azure Data Factory használatával |} Microsoft Docs
description: 'Útmutató: Azure Data Factory másolási tevékenység segítségével áthelyezni az adatokat a helyszíni DB2-adatbázishoz'
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: c1644e17-4560-46bb-bf3c-b923126671f1
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 88e56f522545f9c1f38bf0d0fdbcebdc171c294b
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37046530"
---
# <a name="move-data-from-db2-by-using-azure-data-factory-copy-activity"></a>Azure Data Factory másolási tevékenység segítségével DB2 tárolt adatok mozgatása
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verziójával](data-factory-onprem-db2-connector.md)
> * [(Az aktuális verzió) 2-es verzió](../connector-db2.md)

> [!NOTE]
> Ez a cikk a Data Factory 1 verziójára vonatkozik. A Data Factory szolgáltatásnak aktuális verziójának használatakor lásd [DB2-összekötő a V2](../connector-db2.md).


Ez a cikk azt ismerteti, hogyan használható másolási tevékenység az Azure Data Factory adatok másolása egy helyszíni DB2-adatbázishoz a tárolóban. Bármely tároló, amely egy támogatott fogadó a blokkolandóként adatokat másolhat a [adat-előállító adatok mozgása tevékenységek](data-factory-data-movement-activities.md#supported-data-stores-and-formats) cikk. Ez a témakör a Data Factory cikket, amely áttekintést nyújt az adatátvitelt jelölik a másolási tevékenység segítségével, és felsorolja a támogatott adatokat tároló kombinációk épül. 

Adat-előállító jelenleg egy DB2-adatbázisból való áthelyezése adatok kizárólag egy [támogatott fogadó adattár](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Egy DB2-adatbázishoz nem támogatott az adatok áthelyezését más adatokat tárolja.

## <a name="prerequisites"></a>Előfeltételek
Adat-előállító támogatja a helyszíni DB2-adatbázishoz való kapcsolódás a [az adatkezelési átjáró](data-factory-data-management-gateway.md). Hozzon létre az átjáró adatok feldolgozási sor az adatok áthelyezése a részletes útmutatót lásd: a [tárolt adatok mozgatása felhőbe helyszíni](data-factory-move-data-between-onprem-and-cloud.md) cikk.

Átjáróra szükség, még akkor is, ha a DB2 Azure IaaS virtuális üzemelteti. Az átjáró telepíthető ugyanabból az infrastruktúra-szolgáltatási virtuális adattárként. Az átjáró képes kapcsolódni az adatbázishoz, ha az átjáró telepíthető egy másik virtuális Gépet.

Az adatkezelési átjáró egy beépített DB2-illesztőprogram biztosít, így nem kell manuálisan az adatokat másolni DB2 illesztőprogramot telepíteni.

> [!NOTE]
> Kapcsolat és az átjáró problémák hibaelhárításával kapcsolatos tippek, tekintse meg a [átjáró elhárítása](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) cikk.


## <a name="supported-versions"></a>Támogatott verziók
A Data Factory DB2-összekötő a következő IBM DB2-platformok és 11, 9, 10 elosztott relációs adatbázis architektúra (DRDA) SQL hozzáférés-kezelési verzióival verziókat támogatja:

* IBM DB2 z/os 11.1 verziója
* IBM DB2 z/OS 10.1 verziója
* IBM DB2-i (AS400) verziójának 7.2
* IBM DB2-i (AS400) 7.1-es verziójához
* IBM DB2 Linux, UNIX és a Windows (LUW) 11-es verzió
* IBM DB2 a LUW 10.5 verziója
* IBM DB2 a LUW 10.1 verziója

> [!TIP]
> Ha a hibaüzenet jelenhet meg: "a megfelelő SQL-utasítás végrehajtása kérelmet csomag nem található. SQLSTATE 51002 SQLCODE =-805, = "oka egy szükséges csomag nem lesz létrehozva a normál felhasználói az operációs rendszer. A probléma megoldásához kövesse az alábbi utasításokat a DB2-kiszolgáló típusának:
> - A DB2 i (AS400): lehetővé teszik a kiemelt felhasználó, a másolási tevékenység futtatása előtt a normál felhasználó gyűjtemény létrehozása. A gyűjtemény létrehozásához használja a parancsot: `create collection <username>`
> - A z/OS- vagy LUW DB2: magas jogosultsági fiókkal--egy kiemelt felhasználói vagy a felügyeleti csomag hitelesítésszolgáltatók és a kötési, BINDADD, végrehajtási JOGOT nyilvános engedélyek - példányt is futtatni a egyszer. A szükséges csomag automatikusan jön létre a másolás során. Ezután válthat a normál felhasználók a következő másolási kísérletekhez.

## <a name="getting-started"></a>Első lépések
A másolási tevékenység tárolt adatok mozgatása egy helyszíni DB2 adattároló különböző eszközöket és API-k segítségével létrehozhat egy folyamatot: 

- Hozzon létre egy folyamatot legegyszerűbb módja az Azure Data Factory másolása varázsló használatával. A folyamat létrehozása a varázsló segítségével gyorsan útmutatást lásd: a [oktatóanyag: hozzon létre egy folyamatot a másolása varázslóval](data-factory-copy-data-wizard-tutorial.md). 
- Eszközök segítségével hozzon létre egy folyamatot, beleértve az Azure-portálon, a Visual Studio, Azure PowerShell, az Azure Resource Manager-sablon, a .NET API és a REST API-t. Hozzon létre egy folyamatot a másolási tevékenység lépésenkénti útmutatójáért tekintse meg a [másolási tevékenység az oktatóanyag](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). 

Akár az eszközök vagy API-k, hajtsa végre a következő lépésekkel hozza létre egy folyamatot, amely mozgatja az adatokat a forrás-tárolóban a fogadó tárolóban:

1. Bemeneti hivatkozásra, és a kimeneti adatok az áruházakkal, a data factory társított szolgáltatások létrehozásához.
2. A másolási művelet bemeneti és kimeneti adatok adatkészletek létrehozása. 
3. Hozzon létre egy folyamatot, amely fogad egy bemeneti adatkészlet és egy kimeneti adatkészletet a másolási tevékenység. 

Használatakor a másolása varázsló, a JSON-definíciókat a Data Factory kapcsolt szolgáltatások, az adatkészletek és az adatcsatorna entitások automatikusan létrejönnek. Eszközök vagy API-k (kivéve a .NET API-t) használ, amikor az a JSON formátum használatával adja meg a Data Factory entitások. A [JSON-példa: adatok másolása az DB2 az Azure Blob Storage tárolóban](#json-example-copy-data-from-db2-to-azure-blob) jeleníti meg a JSON-definíciókat a Data Factory entitások adatok másolása egy helyszíni DB2 adattároló használt.

A következő szakaszok részletesen bemutatják a JSON tulajdonságokat, amelyeket a Data Factory entitásokat a DB2-tárolóban megadott meghatározásához használják.

## <a name="db2-linked-service-properties"></a>Kapcsolódó DB2 szolgáltatás tulajdonságai
Az alábbi táblázat a DB2 rendszerhez kapcsolódó szolgáltatásra vonatkozó JSON tulajdonságokat.

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| **type** |Ez a tulajdonság értékre kell állítani **OnPremisesDb2**. |Igen |
| **server** |A DB2-kiszolgáló nevét. |Igen |
| **database** |Neve a DB2-adatbázishoz. |Igen |
| **schema** |A DB2-adatbázishoz a séma neve. Ez a tulajdonság a kis-és nagybetűket. |Nem |
| **authenticationType** |A DB2-adatbázishoz való kapcsolódáshoz használt hitelesítés típusa. A lehetséges értékek: névtelen, alapszintű és a Windows. |Igen |
| **felhasználónév** |A Basic vagy Windows-hitelesítés használata esetén a felhasználói fiók nevét. |Nem |
| **jelszó** |A felhasználói fiók jelszavát. |Nem |
| **gatewayName** |Az átjáró, amely használatával a Data Factory szolgáltatásnak csatlakoznia a helyszíni DB2-adatbázishoz való kapcsolódáshoz neve. |Igen |

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai
A szakaszok és meghatározásához adatkészletek rendelkezésre álló tulajdonságok listáját lásd: a [adatkészletek létrehozása](data-factory-create-datasets.md) cikk. Szakasz, például a **struktúra**, **rendelkezésre állási**, és a **házirend** adatkészlet JSON hasonlóak az összes adatkészlet esetében (Azure SQL, Azure Blob Storage tárolóban, az Azure Table-tároló és így tovább).

A **typeProperties** szakasz eltérő adatkészlet egyes típusai és információkat nyújt azokról az adattárban adatok helyét. A **typeProperties** szakasz egy adatkészlet típusú **RelationalTable**, mely tartalmazza a DB2 adatkészlet a következő tulajdonság tartozik:

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| **Táblanév** |A tábla, amelyre a társított szolgáltatás hivatkozik DB2-adatbázispéldány neve. Ez a tulajdonság a kis-és nagybetűket. |Nem (Ha a **lekérdezés** tulajdonság típusa másolási tevékenység **RelationalSource** van megadva) |

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
A szakaszok és definiálása a másolási tevékenység rendelkezésre álló tulajdonságok listáját lásd: a [létrehozása folyamatok](data-factory-create-pipelines.md) cikk. Másolja a tevékenység tulajdonságai, például a **neve**, **leírás**, **bemenetek** tábla, **kimenete** tábla, és **házirend**, minden típusú tevékenységek érhetők el. Az elérhető tulajdonságok a **typeProperties** szakasz a tevékenység eltérőek az egyes tevékenységhez. A másolási tevékenység során a tulajdonságok az adatforrások és mosdók függenek.

A másolási tevékenység, ha az adatforrás típusú **RelationalSource** (amely tartalmazza a DB2 rendszerhez), a következő tulajdonságok érhetők el a **typeProperties** szakasz:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| **lekérdezés** |Az egyéni lekérdezés segítségével olvassa el az adatokat. |SQL-lekérdezési karakterlánc. Például:`"query": "select * from "MySchema"."MyTable""` |Nem (Ha a **tableName** a DataSet adatkészlet tulajdonság meg van adva) |

> [!NOTE]
> Séma-és tábla-és nagybetűk. A lekérdezés utasításban, tegye a tulajdonságnevek használatával "" (idézőjelek).

## <a name="json-example-copy-data-from-db2-to-azure-blob-storage"></a>JSON-példa: adatok másolása az DB2 az Azure Blob-tároló
Ebben a példában a minta JSON-definíciókat tartalmazzon, segítségével hozzon létre egy folyamatot biztosít a [Azure-portálon](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), vagy [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). A példa bemutatja, hogyan egy DB2-adatbázishoz a Blob storage adatokat másolni. Azonban adatokat másolhat [bármely támogatott adatok tárolásához a fogadó típusa](data-factory-data-movement-activities.md#supported-data-stores-and-formats) Azure Data Factory másolási tevékenység használatával.

A minta a következő adat-előállító entitások rendelkezik:

- Egy DB2 társított szolgáltatás típusa [OnPremisesDb2](data-factory-onprem-db2-connector.md#linked-service-properties)
- Egy Azure Blob storage társított szolgáltatás típusa [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
- Bemeneti [dataset](data-factory-create-datasets.md) típusú [RelationalTable](data-factory-onprem-db2-connector.md#dataset-properties)
- Egy kimeneti [dataset](data-factory-create-datasets.md) típusú [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
- A [csővezeték](data-factory-create-pipelines.md) , a másolási tevékenység által használt a [RelationalSource](data-factory-onprem-db2-connector.md#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) tulajdonságai

A minta másol adatokat egy lekérdezés eredményét DB2-adatbázisban egy Azure blob óránként. A JSON-tulajdonságok a mintában használt entitás definíciókat Ez a rész ismerteti.

Első lépésként telepítse, és konfigurálja a data gateway. Utasítások szerepelnek a [adatokat a helyszíni helyek és a felhő közötti áthelyezése](data-factory-move-data-between-onprem-and-cloud.md) cikk.

**DB2 társított szolgáltatás**

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

**Az Azure Blob storage társított szolgáltatás**

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

**DB2 bemeneti adatkészlet**

A példa feltételezi, hogy létrehozott egy táblát a "MyTable" oszlop adatsorozat időadatok "időbélyegzőjét" címkével rendelkező nevű DB2.

A **külső** tulajdonság értéke "true"értékre. Ez a beállítás arról értesíti a Data Factory szolgáltatásnak, hogy ehhez az adatkészlethez az adat-előállítóban külső, és egy tevékenység adat-előállító nem hozzák. Figyelje meg, hogy a **típus** tulajdonsága **RelationalTable**.


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

Adatot ír egy új blob óránként úgy, hogy a **gyakoriság** "Hour" tulajdonságot, és a **időköz** 1 tulajdonság. A **folderPath** tulajdonság a blob dinamikusan ki lesz értékelve az alapján a szelet feldolgozása folyamatban van, a kezdési idejét. A mappa elérési útját használja, év, hónap, nap, és a kezdési idő órában részeit.

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

**A másolási tevékenységhez folyamat**

A feldolgozási sor tartalmazza a másolási tevékenység, amely a megadott bemeneti és kimeneti adatkészletek van konfigurálva, és amely óránkénti futásra nem ütemezték. Az adatcsatorna JSON-definícióban a **forrás** típusúra **RelationalSource** és a **fogadó** típusúra **BlobSink**. A megadott SQL-lekérdezést a **lekérdezés** tulajdonság kiválasztja az adatokat a "Rendelések" táblából.

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

## <a name="type-mapping-for-db2"></a>Típusleképezés a DB2 rendszerhez
Ahogyan az a [adatok mozgása tevékenységek](data-factory-data-movement-activities.md) cikk, a másolási tevékenység automatikus típuskonverziók forrástípus gyűjtése típus a következő kétlépcsős megközelítést használatával hajtja végre:

1. Egy natív forrástípus átalakítása .NET-típus
2. .NET-típus konvertálása a natív a fogadó típusa

A következő megfeleltetéseket szolgálnak, amikor a másolási tevékenység konvertálja az adatokat egy DB2-típusból .NET-típus:

| DB2-adatbázishoz típusa | .NET-keretrendszer típusa |
| --- | --- |
| SmallInt |Int16 |
| Egész szám |Int32 |
| BigInt |Int64 |
| Real |Önálló |
| Dupla |Dupla |
| Lebegőpontos |Dupla |
| Decimális |Decimális |
| DecimalFloat |Decimális |
| Numerikus |Decimális |
| Dátum |DateTime |
| Time |A TimeSpan |
| Időbélyeg |DateTime |
| Xml |Byte] |
| karakter |Sztring |
| VarChar |Sztring |
| LongVarChar |Sztring |
| DB2DynArray |Sztring |
| Bináris |Byte] |
| VarBinary |Byte] |
| LongVarBinary |Byte] |
| Kép |Sztring |
| VarGraphic |Sztring |
| LongVarGraphic |Sztring |
| CLOB |Sztring |
| Blob |Byte] |
| DbClob |Sztring |
| SmallInt |Int16 |
| Egész szám |Int32 |
| BigInt |Int64 |
| Real |Önálló |
| Dupla |Dupla |
| Lebegőpontos |Dupla |
| Decimális |Decimális |
| DecimalFloat |Decimális |
| Numerikus |Decimális |
| Dátum |DateTime |
| Time |A TimeSpan |
| Időbélyeg |DateTime |
| Xml |Byte] |
| karakter |Sztring |

## <a name="map-source-to-sink-columns"></a>Térkép forrás oszlopok gyűjtése
A forrás adatkészletben levő oszlopok hozzárendelése oszlop szerepel a fogadó dataset, lásd: [Azure Data Factory dataset oszlopai leképezési](data-factory-map-columns.md).

## <a name="repeatable-reads-from-relational-sources"></a>A relációs források ismételhető olvasási műveletek
Ha az adatokat másolni relációs adattároló, ismételhetőség tartsa szem előtt, nem kívánt eredmények elkerülése érdekében. Az Azure Data Factoryben futtathatja a szelet manuálisan. Úgy konfigurálhatja az újrapróbálkozási **házirend** tulajdonságot egy adatkészlet szelet újrafuttathatja, amikor hiba történik. Győződjön meg arról, hogy ugyanazokat az adatokat olvasni, függetlenül attól, hogy hányszor a szelet akkor fut újra, és hogyan futtassa újból a szeletet függetlenül. További információkért lásd: [Repeatable olvassa be az relációs források](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Teljesítmény és finomhangolás
További tudnivalók a másolási tevékenység és a teljesítmény optimalizálása teljesítményt befolyásoló legfontosabb tényezők a [másolási tevékenység teljesítmény- és hangolása útmutató](data-factory-copy-activity-performance.md).
