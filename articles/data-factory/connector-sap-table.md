---
title: Adatok másolása SAP-táblából
description: Megtudhatja, hogyan másolhatja az adatokat egy SAP-táblából a támogatott fogadó adattárak egy Azure Data Factory-folyamat másolási tevékenység használatával.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/02/2019
ms.openlocfilehash: e98ff7fd914bb86cae256bb1bf6c19086758d463
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371540"
---
# <a name="copy-data-from-an-sap-table-by-using-azure-data-factory"></a>Adatok másolása SAP-táblából az Azure Data Factory használatával

Ez a cikk ismerteti, hogyan használhatja a másolási tevékenység et az Azure Data Factory adatok másolása egy SAP-táblából. További információt a [Tevékenység másolása – áttekintés című témakörben talál.](copy-activity-overview.md)

>[!TIP]
>Ha meg szeretné tudni, hogy az ADF általános támogatást nyújt az SAP-adatintegrációs forgatókönyvhöz, tekintse meg az [SAP-adatok integrálását az Azure Data Factory használatával című tanulmányban,](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) részletes bevezetéssel, összevetéssel és útmutatással.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez az SAP-táblaösszekötő a következő tevékenységek esetén támogatott:

- [Tevékenység másolása](copy-activity-overview.md) [támogatott forrás/fogadó mátrixcal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

Az SAP-táblából adatokat másolhat bármely támogatott fogadó adattárba. A másolási tevékenység által forrásként vagy fogadóként támogatott adattárak listáját a [Támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblában található.

Ez az SAP-táblaösszekötő kifejezetten a következőket támogatja:

- Adatok másolása SAP-táblából:

  - SAP ERP központi összetevő (SAP ECC) 7.01-es vagy újabb verziója (egy 2015 után kiadott legutóbbi SAP támogatási csomagveremben).
  - SAP Business Warehouse (SAP BW) 7.01-es vagy újabb verziója (egy 2015 után kiadott legutóbbi SAP támogatási csomagveremben).
  - SAP S/4HANA.
  - Egyéb termékek az SAP Business Suite 7.01-es vagy újabb verziójában (egy 2015 után kiadott legutóbbi SAP támogatási csomagveremben).

- Adatok másolása egy SAP transzparens táblából, egy készletezött táblából, egy fürtözött táblából és egy nézetből.
- Adatok másolása alapfokú hitelesítéssel vagy biztonságos hálózati kommunikációval (SNC), ha az SNC konfigurálva van.
- Sap-alkalmazáskiszolgálóhoz vagy SAP-üzenetkiszolgálóhoz való csatlakozás.

## <a name="prerequisites"></a>Előfeltételek

Az SAP-táblaösszekötő használatához a következőket kell használnia:

- Saját üzemeltetésű integrációs futásidő beállítása (3.17-es vagy újabb verzió). További információt a [Saját üzemeltetésű integrációs futásidő létrehozása és konfigurálása című témakörben talál.](create-self-hosted-integration-runtime.md)

- Töltse le a 64 bites [SAP Connector for Microsoft .NET 3.0-t](https://support.sap.com/en/product/connectors/msnet.html) az SAP webhelyéről, és telepítse azt az önkiszolgáló integrációs futásidejű gépre. A telepítés során győződjön meg arról, hogy a **Választható beállítási lépések** ablakban a **Szerelések telepítése gac-ra** lehetőséget választotta.

  ![Sap-összekötő telepítése .NET-hez](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- A Data Factory SAP-táblaösszekötőben használt SAP-felhasználónak a következő engedélyekkel kell rendelkeznie:

  - Engedélyezés a távoli függvényhívás (RFC) célhelyének használatára.
  - A S_SDSAUTH engedélyezési objektum végrehajtása tevékenységhez szükséges engedélyek.

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok az SAP-táblaösszekötőre jellemző Data Factory-entitások definiálásához használt tulajdonságok részleteit ismertetik.

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai

Az SAP BW Open Hub csatolt szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| `type` | A `type` tulajdonságot a `SapTable`ikonra kell állítani. | Igen |
| `server` | Annak a kiszolgálónak a neve, amelyen az SAP-példány található.<br/>Sap-alkalmazáskiszolgálóhoz való csatlakozáshoz használható. | Nem |
| `systemNumber` | Az SAP-rendszer rendszerszáma.<br/>Sap-alkalmazáskiszolgálóhoz való csatlakozáshoz használható.<br/>Megengedett érték: Karakterláncként ábrázolt kétjegyű tizedesszám. | Nem |
| `messageServer` | Az SAP-üzenetkiszolgáló állomásneve.<br/>Sap-üzenetkiszolgálóhoz való csatlakozáshoz használható. | Nem |
| `messageServerService` | Az üzenetkiszolgáló szolgáltatásneve vagy portszáma.<br/>Sap-üzenetkiszolgálóhoz való csatlakozáshoz használható. | Nem |
| `systemId` | Annak az SAP-rendszernek az azonosítója, amelyben a tábla található.<br/>Sap-üzenetkiszolgálóhoz való csatlakozáshoz használható. | Nem |
| `logonGroup` | Az SAP-rendszer bejelentkezési csoportja.<br/>Sap-üzenetkiszolgálóhoz való csatlakozáshoz használható. | Nem |
| `clientId` | Az ügyfél azonosítója az SAP-rendszerben.<br/>Megengedett érték: Karakterláncként ábrázolt háromjegyű tizedesszám. | Igen |
| `language` | Az SAP-rendszer által használt nyelv.<br/>Az alapértelmezett `EN`érték a .| Nem |
| `userName` | Az SAP-kiszolgálóhoz hozzáféréssel rendelkező felhasználó neve. | Igen |
| `password` | A felhasználó jelszava. Jelölje meg ezt `SecureString` a mezőt azzal a típussal, amely biztonságosan tárolja a Data Factory-ban, vagy [hivatkozzon az Azure Key Vaultban tárolt titkos fájlokra.](store-credentials-in-key-vault.md) | Igen |
| `sncMode` | Az SNC aktiválási jelzője az SAP-kiszolgáló eléréséhez, ahol a tábla található.<br/>Használja, ha az SNC-t szeretné használni az SAP-kiszolgálóhoz való csatlakozáshoz.<br/>Az engedélyezett `0` értékek (ki, `1` alapértelmezett) vagy (be) | Nem |
| `sncMyName` | A kezdeményező SNC-neve az SAP-kiszolgáló eléréséhez, ahol a tábla található.<br/>Akkor `sncMode` érvényes, ha be van kapcsolva. | Nem |
| `sncPartnerName` | A kommunikációs partner SNC-neve az SAP-kiszolgáló eléréséhez, ahol a tábla található.<br/>Akkor `sncMode` érvényes, ha be van kapcsolva. | Nem |
| `sncLibraryPath` | A külső biztonsági termék könyvtára az SAP-kiszolgáló eléréséhez, ahol a tábla található.<br/>Akkor `sncMode` érvényes, ha be van kapcsolva. | Nem |
| `sncQop` | Az SNC protection minőségi szintje.<br/>Akkor `sncMode` érvényes, ha be van kapcsolva. <br/>Az engedélyezett `1` értékek `2` a következők: `3` (Hitelesítés), (integritás), (Adatvédelem), `8` (Alapértelmezett), `9` (Maximális). | Nem |
| `connectVia` | Az adattárhoz való csatlakozáshoz használandó [integrációs futásidő.](concepts-integration-runtime.md) Saját üzemeltetésű integrációs futásidejűre van szükség, ahogy azt az Előfeltételek című részben korábban [említettük.](#prerequisites) |Igen |

**1. példa: Csatlakozás SAP-alkalmazáskiszolgálóhoz**

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

### <a name="example-2-connect-to-an-sap-message-server"></a>2. példa: Csatlakozás SAP-üzenetkiszolgálóhoz

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

### <a name="example-3-connect-by-using-snc"></a>3. példa: Csatlakozás Az SNC használatával

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

Az adatkészletek definiálásának szakaszainak és tulajdonságainak teljes listáját az Adatkészletek című [témakörben található.](concepts-datasets-linked-services.md) A következő szakasz az SAP-tábla adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Adatok másolása az SAP BW Open Hub csatolt szolgáltatásból és az SAP BW Open Hub csatolt szolgáltatásba:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| `type` | A `type` tulajdonságot a `SapTableResource`ikonra kell állítani. | Igen |
| `tableName` | Az adatok másolni az SAP-tábla neve. | Igen |

### <a name="example"></a>Példa

```json
{
    "name": "SAPTableDataset",
    "properties": {
        "type": "SapTableResource",
        "typeProperties": {
            "tableName": "<SAP table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP table linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálásának szakaszainak és tulajdonságainak teljes listáját a Folyamatok című [témakörben található.](concepts-pipelines-activities.md) A következő szakasz az SAP-táblaforrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="sap-table-as-source"></a>SAP-tábla forrásként

Az SAP-táblából történő adatok másolásához a következő tulajdonságok támogatottak:

| Tulajdonság                         | Leírás                                                  | Kötelező |
| :------------------------------- | :----------------------------------------------------------- | :------- |
| `type`                             | A `type` tulajdonságot a `SapTableSource`ikonra kell állítani.         | Igen      |
| `rowCount`                         | A beolvasandó sorok száma.                              | Nem       |
| `rfcTableFields`                   | Az SAP-táblából másolandó mezők (oszlopok). Például: `column0, column1`. | Nem       |
| `rfcTableOptions`                  | Az SAP-tábla sorainak szűrésére rendelkezésre áll. Például: `COLUMN0 EQ 'SOMEVALUE'`. Lásd még az SAP-lekérdezésoperátor tábla később ebben a cikkben. | Nem       |
| `customRfcReadTableFunctionModule` | Egyéni RFC-függvénymodul, amely sap-táblából adatok olvasására használható.<br>Egy egyéni RFC-függvénymodul segítségével megadhatja, hogy az adatok at az SAP-rendszer, és visszaad a Data Factory. Az egyéni függvénymodulnak olyan felülettel kell rendelkeznie, amelyet `/SAPDS/RFC_READ_TABLE2`megvalósítottak (import, export, táblák), amely hasonló a hoz, amely a Data Factory által használt alapértelmezett felület. | Nem       |
| `partitionOption`                  | Az SAP-táblából olvasandó partíciós mechanizmus. A támogatott lehetőségek a következők: <ul><li>`None`</li><li>`PartitionOnInt`(normál egész vagy egész értékek, balra nulla párnázattal, például `0000012345`)</li><li>`PartitionOnCalendarYear`(4 számjegy "ÉÉÉ" formátumban)</li><li>`PartitionOnCalendarMonth`(6 számjegy "ÉÉÉÉMm" formátumban)</li><li>`PartitionOnCalendarDate`(8 számjegy "ÉÉÉÉHHS" formátumban)</li></ul> | Nem       |
| `partitionColumnName`              | Az adatok particionálásához használt oszlop neve.                | Nem       |
| `partitionUpperBound`              | A particionálás folytatásához `partitionColumnName` a megadott oszlop maximális értéke. | Nem       |
| `partitionLowerBound`              | Az oszlopban `partitionColumnName` megadott minimális érték a particionálás folytatásához lesz használva. (Megjegyzés: `partitionLowerBound` nem lehet "0", `PartitionOnInt`ha partíció opció ) | Nem       |
| `maxPartitionsNumber`              | Az adatok felosztásához legfeljebb hány partícióra bontandó partíció.     | Nem       |

>[!TIP]
>Ha az SAP-tábla nagy mennyiségű adatot tartalmaz, például több milliárd sort, használja, `partitionOption` és `partitionSetting` az adatok felosztása kisebb partíciókra. Ebben az esetben az adatok olvasása partíciónként, és minden adatpartíció lekérése az SAP-kiszolgálóról egyetlen RFC-híváson keresztül.<br/>
<br/>
>`partitionOption` Példaként `partitionOnInt` véve az egyes partíciók sorainak számát a következő képlettel `partitionUpperBound` számítjuk ki: (a sorok között és `partitionLowerBound`a )/`maxPartitionsNumber`között esik.<br/>
<br/>
>Az adatpartíciók párhuzamos betöltéséhez a másolás felgyorsításához a [`parallelCopies`](copy-activity-performance.md#parallel-copy) párhuzamos mértéket a másolási tevékenység beállítása szabályozza. Ha például négyre állítva, `parallelCopies` a Data Factory egyidejűleg négy lekérdezést hoz létre és futtat a megadott partícióbeállítás és -beállítások alapján, és minden lekérdezés lekéri az adatok egy részét az SAP-táblából. Javasoljuk, hogy `maxPartitionsNumber` az `parallelCopies` ingatlan értékének többszörösét tegye meg. Amikor adatokat másol fájlalapú adattárba, azt is megkell parancsolni, hogy több fájlként írjon egy mappába (csak adja meg a mappa nevét), ebben az esetben a teljesítmény jobb, mint egyetlen fájlba írni.

A `rfcTableOptions`alkalmazásban a következő általános SAP-lekérdezésoperátorok segítségével szűrheti a sorokat:

| Művelet | Leírás |
| :------- | :------- |
| `EQ` | Egyenlő |
| `NE` | Nem egyenlő |
| `LT` | Kisebb mint |
| `LE` | Kisebb vagy egyenlő |
| `GT` | Nagyobb, mint |
| `GE` | Nagyobb vagy egyenlő |
| `LIKE` | Mint például`LIKE 'Emma%'` |

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
            },
            "parallelCopies": 4
        }
    }
]
```

## <a name="data-type-mappings-for-an-sap-table"></a>SAP-tábla adattípus-leképezései

Amikor adatokat másol egy SAP-táblából, a következő leképezések az SAP-tábla adattípusaiból az Azure Data Factory köztes adattípusai. Ha meg szeretné tudni, hogy a másolási tevékenység hogyan rendeli le a forrássémát és az adattípust a fogadóhoz, olvassa el a [Séma- és adattípus-hozzárendelések című témakört.](copy-activity-schema-and-type-mapping.md)

| SAP ABAP-típus | Adatgyár köztes adattípusa |
|:--- |:--- |
| `C`(Karakterlánc) | `String` |
| `I`(Egész szám) | `Int32` |
| `F`(Lebeg) | `Double` |
| `D`(Dátum) | `String` |
| `T`(Idő) | `String` |
| `P`(BCD csomagolt, pénznem, decimális, mennyiség) | `Decimal` |
| `N`(Numerikus) | `String` |
| `X`(Bináris és nyers) | `String` |

## <a name="lookup-activity-properties"></a>A keresgaszíntevékenység tulajdonságai

A tulajdonságokrészleteinek megismeréséhez ellenőrizze a [Kereskövetési tevékenységet.](control-flow-lookup-activity.md)


## <a name="next-steps"></a>További lépések

Az Azure Data Factory másolási tevékenysége által forrásként és fogadóként támogatott adattárak listáját a [Támogatott adattárak című témakörben tetszését.](copy-activity-overview.md#supported-data-stores-and-formats)
