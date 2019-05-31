---
title: Adatok másolása az Azure Data Factory használatával az SAP-tábla |} A Microsoft Docs
description: Megtudhatja, hogyan másolhat adatokat SAP táblából támogatott fogadó adattárakba az Azure Data Factory-folyamatot egy másolási tevékenység használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: jingwang
ms.openlocfilehash: 4dee0e994c9e7be9677a8f1051481850990998e9
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66247169"
---
# <a name="copy-data-from-sap-table-using-azure-data-factory"></a>Adatok másolása az Azure Data Factory használatával az SAP-tábla

Ez a cikk ismerteti az Azure Data Factory a másolási tevékenység használatával adatokat másol egy SAP-tábla. Épül a [másolási tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

## <a name="supported-capabilities"></a>Támogatott képességek

Az SAP-táblából adatokat másolhatja bármely támogatott fogadó adattárba. A másolási tevékenység által, források és fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Pontosabban az SAP-tábla összekötő támogatja:

- Az adatok másolása az SAP-tábla **7.01 vagy újabb verzióját az SAP Business Suite** (a legutóbbi SAP támogatási csomag veremben lévő a 2015 évi. után kiadott) vagy **S/4HANA**.
- Az adatok másolása mindkét **SAP transzparens tábla** és **nézet**.
- Másolja az adatokat az **alapszintű hitelesítés** vagy **SNC** (biztonságos hálózati kommunikációs) Ha SNC van konfigurálva.
- Kapcsolódás **alkalmazáskiszolgáló** vagy **Message kiszolgáló**.

## <a name="prerequisites"></a>Előfeltételek

Az SAP-Table-összekötő használatához meg kell:

- Állítsa be egy helyi Integration Runtime 3.17 verziójú vagy újabb. Lásd: [helyi Integration Runtime](create-self-hosted-integration-runtime.md) részleteivel.

- Töltse le a **64 bites [SAP .NET összekötő 3.0](https://support.sap.com/en/product/connectors/msnet.html)**  SAP webhelyről, és telepítse a helyi integrációs modul gépen. Amikor telepíti, a opcionális beállítási lépéseket ablakban jelölje ki a **GAC szerelvények telepítése** lehetőséget. 

    ![SAP .NET-összekötő telepítése](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- Használja a Data Factory-tábla SAP-összekötő az SAP-felhasználó kell rendelkeznie az alábbi engedélyek: 

    - RFC engedélyezését. 
    - A "Végrehajtása" tevékenység engedélyezési objektum "S_SDSAUTH" engedéllyel.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Az alábbi szakaszok nyújtanak, amelyek meghatározzák az összekötő az SAP-tábla adott Data Factory-entitások-tulajdonságokkal kapcsolatos részletekért.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

SAP Business Warehouse nyissa meg Hub társított szolgáltatás a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot kell beállítani: **SapTable** | Igen |
| server | A kiszolgálón, amelyiken az SAP-példányt található nevére.<br/>Alkalmazható, ha csatlakozni szeretne **SAP-alkalmazáskiszolgáló**. | Nem |
| systemNumber | Az SAP-rendszer rendszer száma.<br/>Alkalmazható, ha csatlakozni szeretne **SAP-alkalmazáskiszolgáló**.<br/>Érték engedélyezett: kétjegyű tizedes tört egy karakterláncból. | Nem |
| messageServer | Az SAP Üzenetkiszolgáló állomásnevét.<br/>Alkalmazható, ha csatlakozni szeretne **SAP Üzenetkiszolgáló**. | Nem |
| messageServerService | A szolgáltatás nevének vagy portjának száma a kiszolgáló.<br/>Alkalmazható, ha csatlakozni szeretne **SAP Üzenetkiszolgáló**. | Nem |
| Rendszerazonosító | Az SAP-rendszerhez, ahol a tábla nem található a rendszerazonosító.<br/>Alkalmazható, ha csatlakozni szeretne **SAP Üzenetkiszolgáló**. | Nem |
| logonGroup | A bejelentkezés az SAP-rendszer csoport.<br/>Alkalmazható, ha csatlakozni szeretne **SAP Üzenetkiszolgáló**. | Nem |
| clientId | Az SAP-rendszer az ügyfél ügyfél-azonosítója.<br/>Érték engedélyezett: háromjegyű tizedes tört egy karakterláncból. | Igen |
| language | Az SAP-rendszer által használt nyelv. | Nem (alapértelmezett érték **EN**)|
| userName | Az SAP-kiszolgálóhoz hozzáféréssel rendelkező felhasználó nevét. | Igen |
| password | A felhasználó jelszava. Ez a mező megjelölése tárolja biztonságos helyen a Data Factory, a SecureString vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). | Igen |
| sncMode | SNC-aktiválási mutató hol helyezkedik el a tábla a SAP-kiszolgálóhoz való hozzáféréshez.<br/>Akkor alkalmazható, ha SNC SAP-kiszolgálóhoz való csatlakozáshoz használandó.<br/>Engedélyezett értékek a következők: **0** (kikapcsolva, alapértelmezett), vagy **1** (a). | Nem |
| sncMyName | Kezdeményező SNC neve, az SAP tartalmazó kiszolgálót a tábla elérésére.<br/>Akkor alkalmazható, ha `sncMode` be van kapcsolva. | Nem |
| sncPartnerName | Kommunikációs partner SNC neve, az SAP tartalmazó kiszolgálót a tábla elérésére.<br/>Akkor alkalmazható, ha `sncMode` be van kapcsolva. | Nem |
| sncLibraryPath | Külső biztonsági termék könyvtár az SAP-kiszolgálóhoz való hozzáféréshez, a tábla nem található.<br/>Akkor alkalmazható, ha `sncMode` be van kapcsolva. | Nem |
| sncQop | SNC-minőségi védelmet.<br/>Akkor alkalmazható, ha `sncMode` be van kapcsolva. <br/>Engedélyezett értékek a következők: **1** (hitelesítés), **2** (integritása), **3** (adatvédelem), **8** (alapértelmezett), **9** (maximum értéket). | Nem |
| connectVia | A [Integration Runtime](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. Egy helyi Integration Runtime szükség, az említett [Előfeltételek](#prerequisites). |Igen |

**1. példa: Kapcsolódás az SAP-alkalmazáskiszolgáló**

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
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

**2. példa: Kapcsolódás az SAP Üzenetkiszolgáló**

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "messageServer": "<message server name>",
            "messageServerService": "<service name or port>",
            "systemId": "<system id>",
            "logonGroup": "<logon group>",
            "clientId": "<client id>",
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

**3. példa: SNC-val való csatlakozás**

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            },
            "sncMode": 1,
            "sncMyName": "snc myname",
            "sncPartnerName": "snc partner name",
            "sncLibraryPath": "snc library path",
            "sncQop": "8"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Szakaszok és adatkészletek definiálását tulajdonságainak teljes listáját lásd: a [adatkészletek](concepts-datasets-linked-services.md) cikk. Ez a szakasz az SAP-Table adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Az adatok másolása a, és az SAP BW Open Hub, a következő tulajdonságok támogatottak.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot állítsa **SapTableResource**. | Igen |
| tableName | Az adatokat másolni a SAP-tábla neve. | Igen |

**Példa**

```json
{
    "name": "SAPTableDataset",
    "properties": {
        "type": "SapTableResource",
        "linkedServiceName": {
            "referenceName": "<SAP Table linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<SAP table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz az SAP-tábla forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="sap-table-as-source"></a>Az SAP-táblázat forrásaként

Adatok másolása az SAP-tábla, a következő tulajdonságok támogatottak.

| Tulajdonság                         | Leírás                                                  | Szükséges |
| :------------------------------- | :----------------------------------------------------------- | :------- |
| type                             | A type tulajdonságot állítsa **SapTableSource**.       | Igen      |
| Sorszám                         | A lekérdezni kívánt sorok száma.                              | Nem       |
| rfcTableFields                   | Az SAP-táblából másolhatja a mezők. Például: `column0, column1`. | Nem       |
| rfcTableOptions                  | Beállítások szűrése az SAP-táblában lévő sorokat. Például: `COLUMN0 EQ 'SOMEVALUE'`. | Nem       |
| customRfcReadTableFunctionModule | Egyéni RFC függvénymodul, amelyek segítségével adatokat olvasni az SAP-táblát. | Nem       |
| partitionOption                  | A partíció mechanizmus SAP tábla olvasni. A támogatott lehetőségek a következők: <br/>- **Egyik sem**<br/>- **PartitionOnInt** (normál egész szám vagy nulla, például 0000012345 a bal oldali margója egész értékek)<br/>- **PartitionOnCalendarYear** ("YYYY" formátumban 4 számjegy)<br/>- **PartitionOnCalendarMonth** (6 számjegyű "YYYYMM" formátumban)<br/>- **PartitionOnCalendarDate** (8 jegy "ÉÉÉÉHHNN" formátumban) | Nem       |
| partitionColumnName              | Az adatok particionálása az oszlop neve. | Nem       |
| partitionUpperBound              | A megadott oszlop maximális értéke `partitionColumnName` eljárást particionálása a használni kívánt. | Nem       |
| partitionLowerBound              | A megadott oszlop legkisebb értékének `partitionColumnName` eljárást particionálása a használni kívánt. | Nem       |
| maxPartitionsNumber              | Felosztása a adatokat a partíciók maximális száma. | Nem       |

>[!TIP]
>- Ha az SAP-tábla nagy mennyiségű adatok, például több milliárd sorok, `partitionOption` és `partitionSetting` az adatok ossza fel kisebb partíciókra, ebben az esetben az adatok partíciók, és mindegyik beolvasva rendszer beolvassa az egy egyetlen SAP-kiszolgálóhoz RFC-hívás.<br/>
>- Véve `partitionOption` , `partitionOnInt` tegyük fel, az egyes partíciók sorok számának számolható ki (összes sor közé eső *partitionUpperBound* és *partitionLowerBound*) /*maxPartitionsNumber*.<br/>
>- Ha szeretne további másolási felgyorsítása érdekében párhuzamosan futnak a partíciók, erősen ajánlott, hogy `maxPartitionsNumber` értékét többszörösére `parallelCopies` (további információkat talál a [párhuzamos másolási](copy-activity-performance.md#parallel-copy)).

**Példa**

```json
"activities":[
    {
        "name": "CopyFromSAPTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP Table input dataset name>",
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

## <a name="data-type-mapping-for-sap-table"></a>Az SAP-tábla adattípus-leképezés

Példatípust az adatok SAP táblából, a következő hozzárendeléseket használják az SAP-tábla adattípusai Azure Data Factory-közbenső adattípusok. Lásd: [séma és adatok írja be a hozzárendelések](copy-activity-schema-and-type-mapping.md) megismerheti, hogyan másolási tevékenység leképezi a forrás séma és adatok típusa a fogadó.

| SAP ABAP Type | Data factory közbenső adattípus |
|:--- |:--- |
| C (karakterlánc) | String |
| I (egész szám) | Int32 |
| F (Float) | Double |
| D (dátum) | String |
| T (idő) | String |
| P (BCD csomagolt, pénznem, Decimal, mennyiség) | Decimal |
| N (numerikus) | String |
| X-(bináris és nyers) | String |

## <a name="next-steps"></a>További lépések
A másolási tevékenység az Azure Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
