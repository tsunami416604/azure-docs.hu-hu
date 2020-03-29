---
title: Adatok áthelyezése a DB2-ből az Azure Data Factory használatával
description: Megtudhatja, hogy miként helyezhet át adatokat egy helyszíni DB2-adatbázisból az Azure Data Factory Copy Activity használatával
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: c1644e17-4560-46bb-bf3c-b923126671f1
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: e5d2c6b0460c3a7566adb17601aceb57e57f4d0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74931788"
---
# <a name="move-data-from-db2-by-using-azure-data-factory-copy-activity"></a>Adatok áthelyezése a DB2-ből az Azure Data Factory Copy Activity használatával
> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-onprem-db2-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-db2.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, olvassa el a [DB2-összekötő t a V2 alkalmazásban.](../connector-db2.md)


Ez a cikk azt ismerteti, hogyan használhatja a másolási tevékenység az Azure Data Factory adatok másolása egy helyszíni DB2 adatbázis egy adattárba. Az adatokat bármely olyan tárolóba másolhatja, amely támogatott fogadóként szerepel a [Data Factory adatmozgatási tevékenységekről](data-factory-data-movement-activities.md#supported-data-stores-and-formats) szóló cikkben. Ez a témakör a Data Factory cikkre épül, amely áttekintést nyújt az adatok mozgatásáról a Másolási tevékenység használatával, és felsorolja a támogatott adattár-kombinációkat. 

A Data Factory jelenleg csak a DB2-adatbázisból a [támogatott fogadóadattárba](data-factory-data-movement-activities.md#supported-data-stores-and-formats)való átmozgatását támogatja. Más adattárakból adatbázisba való áthelyezése nem támogatott.

## <a name="prerequisites"></a>Előfeltételek
A Data Factory támogatja a helyszíni DB2-adatbázishoz való csatlakozást az [adatkezelési átjáró](data-factory-data-management-gateway.md)használatával. Az adatok áthelyezése az átjáró adatfolyamatának beállításához lépésenkénti útmutatást az [Adatok áthelyezése a helyszíni](data-factory-move-data-between-onprem-and-cloud.md) környezetből a felhőbe című cikkben talál.

Egy átjáró szükséges akkor is, ha a DB2 üzemelteti az Azure IaaS virtuális gép. Az átjáró t az adattárhoz hasonló IaaS virtuális gépre telepítheti. Ha az átjáró tud csatlakozni az adatbázishoz, telepítheti az átjárót egy másik virtuális gépre.

Az adatkezelési átjáró beépített DB2-illesztőprogramot biztosít, így nem kell manuálisan telepítenie egy illesztőprogramot az adatok DB2-ből történő másolásához.

> [!NOTE]
> A kapcsolatokkal és az átjáróval kapcsolatos problémák elhárításával kapcsolatos tippek az [átjáróval kapcsolatos problémák elhárítása](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) című cikkben olvashat.


## <a name="supported-versions"></a>Támogatott verziók
A Data Factory DB2 összekötő a következő IBM DB2 platformokat és -verziókat támogatja a DRDA SQL Access Manager 9-es, 10-es és 11-es verziójával:

* IBM DB2 z/OS 11.1-es verzióhoz
* IBM DB2 z/OS 10.1-es verzióhoz
* IBM DB2 az i (AS400) 7.2-es verziójához
* IBM DB2 az i (AS400) 7.1-es verziójához
* IBM DB2 Linux, UNIX és Windows (LUW) 11-es verzióhoz
* IBM DB2 a LUW 10.5-ös verziójához
* IBM DB2 a LUW 10.1-es verziójához

> [!TIP]
> Ha a következő hibaüzenet jelenik meg: "Az SQL utasítás-végrehajtási kérelemnek megfelelő csomag nem található. SQLSTATE=51002 SQLCODE=-805," az ok a szükséges csomag nem jön létre a normál felhasználó az operációs rendszer. A probléma megoldásához kövesse a DB2 kiszolgálótípusra vonatkozó utasításokat:
> - DB2 az i (AS400): Hagyja, hogy egy energiafelhasználó hozza létre a gyűjteményt a normál felhasználó számára a Másolási tevékenység futtatása előtt. A gyűjtemény létrehozásához használja a következő parancsot:`create collection <username>`
> - DB2 z/OS vagy LUW esetén: Használjon magas jogosultságú fiókot – olyan kiemelt felhasználót vagy rendszergazdát, akinek csomaghatóságai és BIND, BINDADD, GRANT EXECUTE A NYILVÁNOS ENGEDÉLYEKNEK – a példány egyszeri futtatásához. A szükséges csomag automatikusan létrejön a másolat során. Ezt követően visszaválthat a normál felhasználóra a következő példányfuttatáshoz.

## <a name="getting-started"></a>Első lépések
Létrehozhat egy másolási tevékenységet használó folyamatot, amely különböző eszközök és API-k használatával áthelyezi az adatokat egy helyszíni DB2-adattárból: 

- A folyamat létrehozásának legegyszerűbb módja az Azure Data Factory Copy wizard használata. A folyamat másolása varázslóval történő létrehozásáról az [Oktatóanyag: Folyamat létrehozása a Másolás varázslóval című oktatóanyagcímű témakörben](data-factory-copy-data-wizard-tutorial.md)tud hatja el a folyamatot. 
- Az eszközök segítségével is létrehozhat egy folyamatot, beleértve a Visual Studio, az Azure PowerShell, az Azure Resource Manager-sablon, a .NET API és a REST API. A másolási tevékenységgel rendelkező folyamat létrehozásának lépésenkénti útmutatója a [Tevékenység másolása oktatóanyagban található.](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) 

Függetlenül attól, hogy az eszközöket vagy API-kat használja, a következő lépéseket hajthatja végre egy olyan folyamat létrehozásához, amely adatokat helyezi át a forrásadattárból a fogadó adattárába:

1. Összekapcsolt szolgáltatások létrehozása a bemeneti és kimeneti adattárak és az adat-előállító összekapcsolására.
2. Adatkészletek létrehozása a másolási művelet bemeneti és kimeneti adatainak ábrázolására. 
3. Hozzon létre egy folyamatot egy másolási tevékenységgel, amely egy adatkészletet bemenetként, egy adatkészletet pedig kimenetként vesz fel. 

A Másolás varázsló használatakor a Data Factory csatolt szolgáltatásaihoz, adatkészleteihez és folyamatentitásokhoz automatikusan json-definíciók jönnek létre. Eszközök vagy API-k használataesetén (a .NET API kivételével) a Data Factory entitásokat a JSON formátum használatával definiálhatja. A JSON-példa: Adatok másolása a DB2-ből az Azure Blob storage-ba a Data Factory entitások JSON-definícióit jeleníti meg, amelyek a helyszíni DB2-adattárból történő adatok másolására szolgálnak.

A következő szakaszok a Db2-adattára jellemző Data Factory-entitások meghatározásához használt JSON-tulajdonságok részleteit ismertetik.

## <a name="db2-linked-service-properties"></a>DB2 csatolt szolgáltatás tulajdonságai
Az alábbi táblázat a DB2-hez csatolt szolgáltatásra jellemző JSON-tulajdonságokat sorolja fel.

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| **Típus** |Ezt a tulajdonságot **OnPremisesDb2 (OnPremisesDb2)** típusúra kell állítani. |Igen |
| **Szerver** |A DB2 kiszolgáló neve. |Igen |
| **Adatbázis** |A DB2 adatbázis neve. |Igen |
| **Séma** |A séma neve a DB2 adatbázisban. Ez a tulajdonság nem i. |Nem |
| **authenticationType** |A DB2 adatbázishoz való csatlakozáshoz használt hitelesítés típusa. A lehetséges értékek: Névtelen, Egyszerű és Windows. |Igen |
| **Felhasználónév** |A felhasználói fiók neve, ha alapszintű vagy Windows-hitelesítést használ. |Nem |
| **alaphelyzetbe állítása** |A felhasználói fiók jelszava. |Nem |
| **átjárónév** |Annak az átjárónak a neve, amelyet a Data Factory szolgáltatásnak a helyszíni DB2-adatbázishoz való csatlakozáshoz kell használnia. |Igen |

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai
Az adatkészletek definiálására rendelkezésre álló szakaszok és tulajdonságok listáját az [Adatkészletek létrehozása](data-factory-create-datasets.md) című cikkben olvashat. A szakaszok, például **a struktúra,** a **rendelkezésre állás**és a JSON adatkészlet **szabályzata** hasonlóak az összes adatkészlettípushoz (SQL, Azure Blob storage, Azure Table storage és így tovább).

A **typeProperties** szakasz az adatkészlet egyes típusainál eltérő, és tájékoztatást nyújt az adatok helyéről az adattárban. A **RelationalTable**típusú adatkészlet **typeProperties** szakasza , amely a DB2 adatkészletet tartalmazza, a következő tulajdonsággal rendelkezik:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| **táblaneve** |A tábla neve a DB2 adatbázispéldányban, amelyre a csatolt szolgáltatás hivatkozik. Ez a tulajdonság nem i. |Nem (ha a **RelationalSource** típusú másolási tevékenység **lekérdezési** tulajdonsága meg van adva) |

## <a name="copy-activity-properties"></a>Tevékenység tulajdonságainak másolása
A másolási tevékenységek definiálására rendelkezésre álló szakaszok és tulajdonságok listáját a [Folyamatok létrehozása](data-factory-create-pipelines.md) című cikkben olvashat. A Másolási tevékenység tulajdonságai, például **a név,** **a leírás,** **a bemeneti** tábla, **a kimeneti** tábla és a **házirend**minden típusú tevékenységhez elérhetők. A tevékenység **typeProperties** szakaszában elérhető tulajdonságok tevékenységtípusonként eltérőek lehetnek. A Másolási tevékenység esetében a tulajdonságok az adatforrások és fogadók típusától függően változnak.

A Másolási tevékenység esetében, ha a forrás **RelationalSource** típusú (amely magában foglalja a DB2-t is), a következő tulajdonságok érhetők el a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| **Lekérdezés** |Az adatok olvasásához használja az egyéni lekérdezést. |SQL lekérdezési karakterlánc. Például:`"query": "select * from "MySchema"."MyTable""` |Nem (ha egy adatkészlet **tableName** tulajdonsága meg van adva) |

> [!NOTE]
> A séma- és táblanevekben a kis- és nagybetűket nem lehet figyelembe. A lekérdezési utasításban csatolja a tulajdonságneveket a "" (dupla idézőjelek) használatával.

## <a name="json-example-copy-data-from-db2-to-azure-blob-storage"></a>JSON-példa: Adatok másolása a DB2-ből az Azure Blob storage-ba
Ebben a példában minta JSON-definíciók, amelyek segítségével hozzon létre egy folyamatot a [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), vagy az [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)használatával. A példa bemutatja, hogyan másolhatja az adatokat egy DB2-adatbázisból a Blob storage-ba. Az adatok azonban bármely [támogatott adattár-fogadó típusba](data-factory-data-movement-activities.md#supported-data-stores-and-formats) másolhatók az Azure Data Factory Copy Activity használatával.

A minta a következő Data Factory entitásokkal rendelkezik:

- [OnPremisesDb2](data-factory-onprem-db2-connector.md) típusú DB2-vel összekapcsolt szolgáltatás
- AzureBlob-tárolóhoz kapcsolt [AzureStorage-szolgáltatás](data-factory-azure-blob-connector.md#linked-service-properties)
- [RelationalTable](data-factory-onprem-db2-connector.md#dataset-properties) típusú bemeneti [adatkészlet](data-factory-create-datasets.md)
- [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) típusú kimeneti [adatkészlet](data-factory-create-datasets.md)
- A [RelationalSource](data-factory-onprem-db2-connector.md#copy-activity-properties) és a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) tulajdonságokat használó másolási tevékenységet használó [folyamat](data-factory-create-pipelines.md)

A minta adatokat másol egy lekérdezés eredménye egy DB2-adatbázis egy Azure blob óránként. A mintában használt JSON-tulajdonságokat az entitásdefiníciókat követő szakaszok ismertetik.

Első lépésként telepítsen és konfiguráljon egy adatátjárót. Az utasítások a helyszíni helyek és a felhőalapú cikk [közötti áthelyezési adatokban](data-factory-move-data-between-onprem-and-cloud.md) találhatók.

**DB2 csatolt szolgáltatás**

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

**Azure Blob storage-hoz csatolt szolgáltatás**

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

A minta feltételezi, hogy létrehozott egy "MyTable" nevű táblát a DB2-ben, amelyaz idősorozat-adatokhoz "időbélyeg" feliratú oszlopot használ.

A **külső** tulajdonság értéke "true". Ez a beállítás tájékoztatja a Data Factory szolgáltatást, hogy ez az adatkészlet az adat-előállítón kívül található, és nem az adat-előállító tevékenység által előállított. Figyelje meg, hogy a **típus** tulajdonság **relationalTable (RelationalTable )** lesz állítva.


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

Az adatok óránként egy új blobba írásra kerül, ha a **gyakorisági** tulajdonságot "Óra" értékre, az **intervallum** tulajdonságot pedig 1-re állítja. A blob **folderPath** tulajdonságát dinamikusan értékeli ki a feldolgozás alatt álló szelet kezdési időpontja. A mappa elérési útja a kezdési időpont év-, hónap-, nap- és órarészeit használja.

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

**Folyamat a másolási tevékenységhez**

A folyamat egy olyan másolási tevékenységet tartalmaz, amely a megadott bemeneti és kimeneti adatkészletek használatára van konfigurálva, és amely óránként fut. A folyamat JSON-definíciójában a **forrástípus** **RelationalSource,** a **fogadó** típusa pedig **BlobSink**lesz. A **lekérdezési** tulajdonsághoz megadott SQL-lekérdezés a "Rendelések" táblából választja ki az adatokat.

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

## <a name="type-mapping-for-db2"></a>Típusleképezés a DB2-hez
Az [adatmozgatási tevékenységekről](data-factory-data-movement-activities.md) szóló cikkben említettek szerint a Másolási tevékenység automatikus típuskonverziót hajt végre a forrástípusból a fogadótípusba a következő kétlépéses megközelítés használatával:

1. Konvertálás natív forrástípusból .NET típussá
2. Konvertálás .NET típusból natív fogadótípussá

A következő leképezések akkor használatosak, amikor a Másolási tevékenység az adatokat DB2 típusból .NET típussá alakítja:

| DB2 adatbázistípusa | .NET keretrendszer típusa |
| --- | --- |
| SmallInt között |Int16 |
| Egész szám |Int32 |
| BigInt között |Int64 |
| Valódi |Egyirányú |
| Double |Double |
| Float |Double |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| Numerikus |Decimal |
| Dátum |DateTime |
| Time |időtartam |
| Időbélyeg |DateTime |
| Xml |Bájt[] |
| Char |Sztring |
| Varchar |Sztring |
| LongVarChar között |Sztring |
| DB2DynArray |Sztring |
| Bináris |Bájt[] |
| VarBináris |Bájt[] |
| LongVarBinary között |Bájt[] |
| Grafikus |Sztring |
| Vargrafika |Sztring |
| LongVarGrafika |Sztring |
| Clob között |Sztring |
| Blob |Bájt[] |
| DbClob között |Sztring |
| SmallInt között |Int16 |
| Egész szám |Int32 |
| BigInt között |Int64 |
| Valódi |Egyirányú |
| Double |Double |
| Float |Double |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| Numerikus |Decimal |
| Dátum |DateTime |
| Time |időtartam |
| Időbélyeg |DateTime |
| Xml |Bájt[] |
| Char |Sztring |

## <a name="map-source-to-sink-columns"></a>Forrás leképezése oszlopokhoz
Ha meg szeretné tudni, hogyan képezheti le a forrásadatkészlet oszlopait a fogadó adatkészlet oszlopaihoz, olvassa [el az Adatkészletoszlopok leképezése az Azure Data Factoryban című témakört.](data-factory-map-columns.md)

## <a name="repeatable-reads-from-relational-sources"></a>Relációs forrásokból megismételhető olvasások
Amikor egy relációs adattárból másolja az adatokat, tartsa szem előtt az ismételhetőséget a nem kívánt eredmények elkerülése érdekében. Az Azure Data Factoryban manuálisan futtathatja a szeletet. Az adatkészlet **újrapróbálkozási házirend-tulajdonságát** is beállíthatja úgy, hogy hiba esetén újrafusson egy szeletet. Győződjön meg arról, hogy ugyanazokat az adatokat olvassa be a rendszer, függetlenül attól, hogy hányszor futtassa újra a szeletet, és függetlenül attól, hogy hogyan futtatja újra a szeletet. További információ: [Relációs forrásokból származó Megismételhető olvasások](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Teljesítmény és finomhangolás
Ismerje meg a másolási tevékenység teljesítményét befolyásoló legfontosabb tényezőket, valamint a teljesítmény optimalizálásának módjait a [Másolási teljesítmény és a hangolás útmutatójában.](data-factory-copy-activity-performance.md)
