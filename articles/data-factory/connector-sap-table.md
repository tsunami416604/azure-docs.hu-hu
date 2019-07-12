---
title: Adatok másolása egy SAP-táblából az Azure Data Factory használatával |} A Microsoft Docs
description: Megtudhatja, hogyan másolhat adatokat egy SAP-táblából támogatott fogadó adattárakba az Azure Data Factory-folyamatot egy másolási tevékenység használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: jingwang
ms.openlocfilehash: 9216f5c00cbdac273b562736abdd1c812d172237
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827758"
---
# <a name="copy-data-from-an-sap-table-by-using-azure-data-factory"></a>Adatok másolása az Azure Data Factory használatával egy SAP-táblából

Ez a cikk ismerteti az Azure Data Factory a másolási tevékenység használatával adatokat másol egy SAP-tábla. További információkért lásd: [másolási tevékenység áttekintése](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Támogatott képességek

Egy SAP-táblából adatokat másolhatja bármely támogatott fogadó adattárba. A másolási tevékenység által források vagy fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Pontosabban az SAP-tábla összekötő támogatja:

- Adatok másolása az SAP táblából:

  - Az SAP ERP központi összetevő, (SAP ECC) 7.01 vagy újabb verziójára (a legutóbbi SAP támogatási csomag veremben lévő 2015. után kiadott).
  - SAP Business Warehouse (SAP BW) 7.01 vagy újabb verziója.
  - SAP S/4HANA-T.
  - Egyéb termékek, az SAP Business Suite 7.01 vagy újabb verziója.

- Az adatok másolása is egy SAP transzparens tábla, egy készletezett tábla, egy fürtözött tábla és nézet.
- Adatok másolása az egyszerű hitelesítés vagy biztonságos hálózati kommunikációs (SNC) használatával, ha SNC van konfigurálva.
- Az SAP-alkalmazáskiszolgáló vagy SAP üzenetkiszolgáló csatlakozik.

## <a name="prerequisites"></a>Előfeltételek

Az SAP-tábla összekötő használatához meg kell:

- Állítsa be egy saját üzemeltetésű integrációs modul (3.17 vagy újabb verzió). További információkért lásd: [létrehozása és a egy saját üzemeltetésű integrációs modul konfigurálása](create-self-hosted-integration-runtime.md).

- Töltse le a 64 bites [SAP-összekötő a Microsoft .NET-hez 3.0](https://support.sap.com/en/product/connectors/msnet.html) SAP webhelyről, és telepítse a saját üzemeltetésű integrációs modult tartalmazó számítógépen. A telepítés során mindenképpen jelölje ki a **GAC szerelvények telepítése** beállítást a **opcionális beállítási lépéseket** ablak.

  ![Az SAP-összekötő telepítése a .NET-hez](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- A SAP-felhasználó, aki a Data Factory SAP tábla Connector használja a következő engedélyekkel kell rendelkeznie:

  - A távoli függvény hívása (RFC) destinations engedélyezési.
  - A végrehajtás tevékenység engedélyt az S_SDSAUTH objektum.

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok segítségével határozhatók meg a Data Factory-entitások adott és az SAP-tábla összekötő-tulajdonságokkal kapcsolatos részletekért.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Az SAP BW Open Hub társított szolgáltatás a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| `type` | A `type` tulajdonságot állítsa `SapTable`. | Igen |
| `server` | A kiszolgálón, amelyen az SAP-példányt található nevére.<br/>Csatlakozhat egy SAP-alkalmazáskiszolgáló. | Nem |
| `systemNumber` | Az SAP-rendszer rendszer száma.<br/>Csatlakozhat egy SAP-alkalmazáskiszolgáló.<br/>Engedélyezett érték: Egy karakterlánccal kétjegyű tizedes tört. | Nem |
| `messageServer` | Az SAP üzenetkiszolgáló állomásneve.<br/>Csatlakozhat egy SAP üzenetkiszolgáló. | Nem |
| `messageServerService` | A szolgáltatás nevének vagy portjának száma a kiszolgáló.<br/>Csatlakozhat egy SAP üzenetkiszolgáló. | Nem |
| `systemId` | Az SAP-rendszerhez, ahol a táblázat megtalálható azonosítója.<br/>Csatlakozhat egy SAP üzenetkiszolgáló. | Nem |
| `logonGroup` | A bejelentkezés az SAP-rendszer csoport.<br/>Csatlakozhat egy SAP üzenetkiszolgáló. | Nem |
| `clientId` | Az SAP-rendszer az ügyfél azonosítója.<br/>Engedélyezett érték: Egy karakterlánccal háromjegyű tizedes tört. | Igen |
| `language` | Az SAP-rendszer által használt nyelv.<br/>Alapértelmezett érték `EN`.| Nem |
| `userName` | Az SAP-kiszolgálóhoz hozzáféréssel rendelkező felhasználó nevét. | Igen |
| `password` | A felhasználó jelszava. Jelölje meg ezt a mezőt a `SecureString` tárolja biztonságos helyen a Data Factory áttekintése, típus vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). | Igen |
| `sncMode` | SNC aktiválási mutató hol helyezkedik el a tábla a SAP-kiszolgálóhoz való hozzáféréshez.<br/>Ha az SAP-kiszolgálóhoz való csatlakozáshoz SNC használni kívánt használja.<br/>Engedélyezett értékek a következők `0` (kikapcsolva, az alapértelmezés) vagy `1` (a). | Nem |
| `sncMyName` | A kezdeményező SNC neve, az SAP tartalmazó kiszolgálót a tábla elérésére.<br/>A következő esetekben alkalmazható `sncMode` be van kapcsolva. | Nem |
| `sncPartnerName` | A kommunikáció partner SNC nevére, az SAP tartalmazó kiszolgálót a tábla elérésére.<br/>A következő esetekben alkalmazható `sncMode` be van kapcsolva. | Nem |
| `sncLibraryPath` | A külső biztonsági termék könyvtár az SAP-kiszolgálóhoz való hozzáféréshez, a tábla nem található.<br/>A következő esetekben alkalmazható `sncMode` be van kapcsolva. | Nem |
| `sncQop` | Az alkalmazandó védelmi minőségi SNC szint.<br/>A következő esetekben alkalmazható `sncMode` be van kapcsolva. <br/>Engedélyezett értékek a következők `1` (hitelesítés), `2` (integritása), `3` (adatvédelem), `8` (alapértelmezett), `9` (maximum értéket). | Nem |
| `connectVia` | A [integrációs modul](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. Egy saját üzemeltetésű integrációs modul megadása kötelező, ahogy korábban említettük [Előfeltételek](#prerequisites). |Igen |

**1. példa: Csatlakozás egy SAP-alkalmazáskiszolgáló**

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="example-2-connect-to-an-sap-message-server"></a>2\. példa Csatlakozás egy SAP üzenetkiszolgáló

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "messageServer": "<message server name>",
            "messageServerService": "<service name or port>",
            "systemId": "<system ID>",
            "logonGroup": "<logon group>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            }
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="example-3-connect-by-using-snc"></a>3\. példa: Csatlakozás SNC használatával

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            },
            "sncMode": 1,
            "sncMyName": "<SNC myname>",
            "sncPartnerName": "<SNC partner name>",
            "sncLibraryPath": "<SNC library path>",
            "sncQop": "8"
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

A szakaszok és szolgáló adatkészletek tulajdonságok teljes listáját lásd: [adatkészletek](concepts-datasets-linked-services.md). A következő szakasz az SAP-table adatkészlet által támogatott tulajdonságokról listáját tartalmazza.

Az adatok másolása a, és az SAP BW Open Hub társított szolgáltatáshoz, a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| `type` | A `type` tulajdonságot állítsa `SapTableResource`. | Igen |
| `tableName` | Az adatokat másolni a SAP-tábla neve. | Igen |

### <a name="example"></a>Példa

```json
{
    "name": "SAPTableDataset",
    "properties": {
        "type": "SapTableResource",
        "linkedServiceName": {
            "referenceName": "<SAP table linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<SAP table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A szakaszok és a tevékenységek meghatározása tulajdonságainak teljes listáját lásd: [folyamatok](concepts-pipelines-activities.md). A következő szakasz az SAP-tábla forrás által támogatott tulajdonságokról listáját tartalmazza.

### <a name="sap-table-as-a-source"></a>Forrásként az SAP-tábla

Adatok másolása az SAP-tábla, a következő tulajdonságok támogatottak:

| Tulajdonság                         | Leírás                                                  | Szükséges |
| :------------------------------- | :----------------------------------------------------------- | :------- |
| `type`                             | A `type` tulajdonságot állítsa `SapTableSource`.         | Igen      |
| `rowCount`                         | A lekérdezni kívánt sorok száma.                              | Nem       |
| `rfcTableFields`                   | A mező (oszlop) másolni az SAP-táblából. Például: `column0, column1`. | Nem       |
| `rfcTableOptions`                  | A beállítások szűrése egy SAP-táblában lévő sorokat. Például: `COLUMN0 EQ 'SOMEVALUE'`. Lásd még az SAP lekérdezési operátor táblát a cikk későbbi részében. | Nem       |
| `customRfcReadTableFunctionModule` | Egy egyéni RFC függvénymodul, amelyek segítségével adatokat olvasni az SAP-táblát.<br>Egy RFC egyéni függvénymodul segítségével az adatok az SAP-rendszerhez lekért és adott vissza a Data Factory meghatározásához. Az egyéni függvénymodul rendelkeznie kell egy felület implementálva (importálás, exportálás, táblákat) hasonló `/SAPDS/RFC_READ_TABLE2`, ez az adat-előállítók által használt alapértelmezett felületet. | Nem       |
| `partitionOption`                  | A partíció mechanizmus beolvasni egy SAP-táblából. A támogatott lehetőségek a következők: <ul><li>`None`</li><li>`PartitionOnInt` (normál egész szám vagy nulla padding a bal oldalon, mint például az egész számok `0000012345`)</li><li>`PartitionOnCalendarYear` (4 számjegy, a következő formátumban: "YYYY")</li><li>`PartitionOnCalendarMonth` (6 számjegyű, a következő formátumban: "YYYYMM")</li><li>`PartitionOnCalendarDate` (8 jegy, a következő formátumban: "ÉÉÉÉHHNN")</li></ul> | Nem       |
| `partitionColumnName`              | Az adatok particionálásához használt oszlop neve.                | Nem       |
| `partitionUpperBound`              | A megadott oszlop maximális értéke `partitionColumnName` particionálás folytatásához használni kívánt. | Nem       |
| `partitionLowerBound`              | A megadott oszlop legkisebb értékének `partitionColumnName` particionálás folytatásához használni kívánt. | Nem       |
| `maxPartitionsNumber`              | Felosztása a adatokat a partíciók maximális száma.     | Nem       |

>[!TIP]
>Ha az SAP-tábla nagy mennyiségű adatot, például több milliárd sorból, `partitionOption` és `partitionSetting` az adatok ossza fel kisebb partíciókra. Ebben az esetben az adatok partíciónként olvasható, és mindegyik rendszer beolvassa az SAP-kiszolgálóhoz egy RFC-hívással.<br/>
<br/>
>Véve `partitionOption` , `partitionOnInt` tegyük fel, az egyes partíciók sorok számának ezzel a képlettel kiszámítani: (összes sor közé eső `partitionUpperBound` és `partitionLowerBound`) /`maxPartitionsNumber`.<br/>
<br/>
>A partíciók másolása felgyorsítása érdekében párhuzamosan is futtatni, javasoljuk, hogy így `maxPartitionsNumber` értékét többszöröse az `parallelCopies` tulajdonság. További információkért lásd: [másolási párhuzamos](copy-activity-performance.md#parallel-copy).

A `rfcTableOptions`, az alábbi SAP gyakori lekérdezés operátorok segítségével a sorok szűrése:

| Művelet | Leírás |
| :------- | :------- |
| `EQ` | Egyenlő |
| `NE` | Nem egyenlő |
| `LT` | Kisebb, mint |
| `LE` | Kisebb, vagy egyenlő |
| `GT` | Nagyobb, mint |
| `GE` | Nagyobb, vagy egyenlő |
| `LIKE` | Csakúgy, mint az `LIKE 'Emma%'` |

### <a name="example"></a>Példa

```json
"activities":[
    {
        "name": "CopyFromSAPTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP table input dataset name>",
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
                "type": "SapTableSource",
                "partitionOption": "PartitionOnInt",
                "partitionSettings": {
                     "partitionColumnName": "<partition column name>",
                     "partitionUpperBound": "2000",
                     "partitionLowerBound": "1",
                     "maxPartitionsNumber": 500
                 }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mappings-for-an-sap-table"></a>Adattípus-leképezések alkalmazását egy SAP-tábla

Másolunk adatokat egy SAP-táblából, ha az Azure Data Factory-közbenső adattípusok a következő hozzárendeléseket használtak az SAP-tábla adattípusai. Hogyan a másolási tevékenység leképezi a forrás séma és adatok típusa a fogadó kapcsolatban lásd: [séma és adatok írja be a hozzárendelések](copy-activity-schema-and-type-mapping.md).

| SAP ABAP Type | Data Factory közbenső adattípus |
|:--- |:--- |
| `C` (Karakterlánc) | `String` |
| `I` (Egész szám) | `Int32` |
| `F` (Float) | `Double` |
| `D` (Dátum) | `String` |
| `T` (Idő) | `String` |
| `P` (BCD csomagolt, pénznem, Decimal, mennyiség) | `Decimal` |
| `N` (Numerikus) | `String` |
| `X` (A bináris és a nyers) | `String` |

## <a name="next-steps"></a>További lépések

Az a másolási tevékenység az Azure Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
