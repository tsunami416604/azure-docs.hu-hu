---
title: Adatok másolása az SAP HANA-ból
description: Megtudhatja, hogyan másolhatja az adatokat az SAP HANA-ból a támogatott fogadó adattárak egy Azure Data Factory-folyamat másolási tevékenység használatával.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/17/2020
ms.openlocfilehash: fa165c21622110bb18476efdebf3264a11e26ad7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265882"
---
# <a name="copy-data-from-sap-hana-using-azure-data-factory"></a>Adatok másolása az SAP HANA-ból az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-sap-hana-connector.md)
> * [Aktuális verzió](connector-sap-hana.md)

Ez a cikk ismerteti, hogyan használhatja a másolási tevékenység az Azure Data Factory adatok másolása egy SAP HANA-adatbázisból. A [másolási tevékenység áttekintése](copy-activity-overview.md) cikkre épül, amely a másolási tevékenység általános áttekintését mutatja be.

>[!TIP]
>Ha meg szeretné tudni, hogy az ADF általános támogatást nyújt az SAP-adatintegrációs forgatókönyvhöz, tekintse meg az [SAP-adatok integrálását az Azure Data Factory használatával című tanulmányban,](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) részletes bevezetéssel, összevetéssel és útmutatással.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez az SAP HANA-összekötő a következő tevékenységek esetén támogatott:

- [Tevékenység másolása](copy-activity-overview.md) [támogatott forrás/fogadó mátrixcal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

Adatokat másolhat az SAP HANA adatbázisból bármely támogatott fogadó adattárba. A másolási tevékenység által forrásként/fogadóként támogatott adattárak listáját a [Támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblában található.

Ez az SAP HANA-összekötő kifejezetten a következőket támogatja:

- Adatok másolása az SAP HANA adatbázis bármely verziójából.
- Adatok másolása **HANA információs modellekből** (például Analytic és Calculation nézetek) és **Sor/oszlop táblákból.**
- Adatok másolása **alapfokú** vagy Windows-hitelesítéssel. **Windows**
- Párhuzamos másolás egy SAP HANA forrásból. A [részleteket az SAP HANA szakasz párhuzamos másolata](#parallel-copy-from-sap-hana) című szakaszban találja.

> [!TIP]
> Adatok másolásához az SAP HANA **adattárba,** általános ODBC-összekötő használata. Lásd: [SAP HANA-fogadó](connector-odbc.md#sap-hana-sink) a részletekkel. Vegye figyelembe, hogy az SAP HANA-összekötő és az ODBC-összekötő csatolt szolgáltatásai eltérő típusúak, így nem használható fel újra.

## <a name="prerequisites"></a>Előfeltételek

Az SAP HANA-összekötő használatához a következőket kell használnia:

- Állítsa be a saját üzemeltetésű integrációs futásidejű. A [részleteket a saját üzemeltetésű integrációs futásidejű](create-self-hosted-integration-runtime.md) cikkben találja.
- Telepítse az SAP HANA ODBC illesztőprogramot az integrációs futásidejű gépen. Az SAP HANA ODBC-illesztőprogramot az [SAP Software Download Center központból](https://support.sap.com/swdc) töltheti le. Keresés az **SAP HANA CLIENT for Windows**kulcsszóval.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok az SAP HANA-összekötőre jellemző Data Factory-entitások definiálásához használt tulajdonságok részleteit ismertetik.

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai

A következő tulajdonságok támogatottak az SAP HANA csatolt szolgáltatás:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A típustulajdonságot **saphana** típusúra kell állítani. | Igen |
| connectionString (kapcsolati karakterlánc) | Adja meg az SAP HANA-hoz való csatlakozáshoz szükséges adatokat **alapfokú hitelesítéssel** vagy **Windows-hitelesítéssel.** Lásd a következő mintákat.<br>A kapcsolati karakterláncban a kiszolgáló/port kötelező (az alapértelmezett port 30015), a felhasználónév és a jelszó pedig az alapfokú hitelesítés használatakor. További speciális beállításokat az [SAP HANA ODBC kapcsolat tulajdonságai](<https://help.sap.com/viewer/0eec0d68141541d1b07893a39944924e/2.0.02/en-US/7cab593774474f2f8db335710b2f5c50.html>)<br/>A jelszót az Azure Key Vaultban is elhelyezheti, és kihúzhatja a jelszókonfigurációt a kapcsolati karakterláncból. További részleteket az [Azure Key Vault-cikkben](store-credentials-in-key-vault.md) található Áruház hitelesítő adatai című cikkben olvashat. | Igen |
| userName (Felhasználónév) | Adja meg a felhasználónevet Windows-hitelesítés használatakor. Például: `user@domain.com` | Nem |
| jelszó | Adja meg a felhasználói fiók jelszavát. Jelölje meg ezt a mezőt SecureStringként a Data Factory biztonságos tárolásához, vagy [hivatkozzon az Azure Key Vaultban tárolt titkos fájlokra.](store-credentials-in-key-vault.md) | Nem |
| connectVia | Az adattárhoz való csatlakozáshoz használandó [integrációs futásidő.](concepts-integration-runtime.md) Az előfeltételek ben említettek szerint saját üzemeltetésű [integrációs](#prerequisites)futásidejű re van szükség. |Igen |

**Példa: alapfokú hitelesítés használata**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "connectionString": "SERVERNODE=<server>:<port (optional)>;UID=<userName>;PWD=<Password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Példa: Windows-hitelesítés használata**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "connectionString": "SERVERNODE=<server>:<port (optional)>;",
            "userName": "<username>", 
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

Ha az SAP HANA csatolt szolgáltatást a következő hasznos adattal, továbbra is támogatott, ahogy van, miközben azt javasoljuk, hogy használja az újat a jövőben.

**Példa:**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "server": "<server>:<port (optional)>",
            "authenticationType": "Basic",
            "userName": "<username>",
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

Az adatkészletek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját az [adatkészletekről](concepts-datasets-linked-services.md) szóló cikkben olvashatja. Ez a szakasz az SAP HANA adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Adatok másolása az SAP HANA-ból, a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | Az adatkészlet típustulajdonságát a következőre kell állítani: **SapHanaTable** | Igen |
| Séma | A séma neve az SAP HANA adatbázisban. | Nem (ha a "lekérdezés" a tevékenységforrásban meg van adva) |
| tábla | A tábla neve az SAP HANA adatbázisban. | Nem (ha a "lekérdezés" a tevékenységforrásban meg van adva) |

**Példa:**

```json
{
    "name": "SAPHANADataset",
    "properties": {
        "type": "SapHanaTable",
        "typeProperties": {
            "schema": "<schema name>",
            "table": "<table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP HANA linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Ha gépelt adatkészletet használt, `RelationalTable` továbbra is támogatott, amíg a rendszer az újat a jövőben használja.

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját a [Folyamatok](concepts-pipelines-activities.md) című cikkben olvashat. Ez a szakasz az SAP HANA-forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="sap-hana-as-source"></a>SAP HANA forrásként

>[!TIP]
>Adatok betöltése az SAP HANA-ból hatékonyan adatparticionálás használatával, további információ az SAP HANA szakasz [párhuzamos másolása.](#parallel-copy-from-sap-hana)

Az SAP HANA-ból származó adatok másolásához a **source** következő tulajdonságokat támogatja a másolási tevékenység forrásszakasza:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának típustulajdonságát a következőre kell állítani: **SapHanaSource** | Igen |
| lekérdezés | Megadja az SAP HANA-példány ból adatokat olvasandó SQL-lekérdezést. | Igen |
| partitionOptions | Megadja az SAP HANA-ból származó adatok betöltéséhez használt adatparticionálási beállításokat. További információ az SAP HANA szakasz párhuzamos másolása című [részéből.](#parallel-copy-from-sap-hana)<br>Az értékek a következők: **Nincs** (alapértelmezett), **PhysicalPartitionsOfTable**, **SapHanaDynamicRange**. További információ az SAP HANA szakasz párhuzamos másolása című [részéből.](#parallel-copy-from-sap-hana) `PhysicalPartitionsOfTable`csak tábla adatainak másolásakor használható, de lekérdezésre nem. <br>Ha egy partícióbeállítás engedélyezve van `None`(azaz nem), a párhuzamosság mértékét az SAP HANA-ból származó adatok egyidejű betöltéséhez a [`parallelCopies`](copy-activity-performance.md#parallel-copy) másolási tevékenység beállítása szabályozza. | False (Hamis) |
| partitionSettings (partícióbeállításai) | Adja meg az adatparticionálás beállításainak csoportját.<br>Alkalmazza, ha `SapHanaDynamicRange`a partíciós beállítás . | False (Hamis) |
| partitionColumnName | Adja meg annak a forrásoszlopnak a nevét, amelyet a partíció a párhuzamos másoláshoz használ. Ha nincs megadva, a rendszer automatikusan észleli az indexet vagy a tábla elsődleges kulcsát, és partícióoszlopként használja.<br>Alkalmazza, ha a `SapHanaDynamicRange`partíciós beállítás . Ha lekérdezéssel olvassa be a forrásadatokat, a HOOK a `?AdfHanaDynamicRangePartitionCondition` WHERE záradékban. Példa az SAP HANA szakasz [párhuzamos másolása](#parallel-copy-from-sap-hana) című témakörben. | Igen partíció `SapHanaDynamicRange` használatakor. |
| packetSize | Megadja azt a hálózati csomagméretet (kilobájtban), amelynek célja az adatok több blokkra való felosztása. Ha nagy mennyiségű adatot másolhat, a csomagméret növelése a legtöbb esetben növelheti az SAP HANA olvasási sebességét. A csomagméret beállításakor teljesítményvizsgálat ajánlott. | Nem.<br>Az alapértelmezett érték 2048 (2 MB). |

**Példa:**

```json
"activities":[
    {
        "name": "CopyFromSAPHANA",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP HANA input dataset name>",
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
                "type": "SapHanaSource",
                "query": "<SQL query for SAP HANA>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Ha gépelt másolási forrást használt, `RelationalSource` az továbbra is támogatott, miközben az újat a jövőben javasolja.

## <a name="parallel-copy-from-sap-hana"></a>Párhuzamos másolat az SAP HANA-ból

A Data Factory SAP HANA-összekötő beépített adatparticionálást biztosít az SAP HANA-ból származó adatok párhuzamos másolásához. Az adatparticionálási beállításokat a másolási tevékenység **Forrás** tábláján találja.

![A partícióbeállításainak képernyőképe](./media/connector-sap-hana/connector-sap-hana-partition-options.png)

Ha engedélyezi a particionált másolást, a Data Factory párhuzamos lekérdezéseket futtat az SAP HANA-forrással az adatok partíciók általi lekéréséhez. A párhuzamos mértéket a [`parallelCopies`](copy-activity-performance.md#parallel-copy) másolási tevékenység beállítása szabályozza. Ha például négyre állítva, `parallelCopies` a Data Factory egyidejűleg négy lekérdezést hoz létre és futtat a megadott partícióbeállítás és -beállítások alapján, és minden lekérdezés lekéri az adatok egy részét az SAP HANA-ból.

Azt javasoljuk, hogy engedélyezze a párhuzamos másolás adatparticionálás, különösen akkor, ha nagy mennyiségű adatot az SAP HANA betöltése. Az alábbiakban a különböző forgatókönyvekhez javasolt konfigurációkat javasoljuk. Amikor adatokat másol fájlalapú adattárba, ajánlott egy mappába több fájlként írni (csak mappanevet kell megadni), ebben az esetben a teljesítmény jobb, mint egyetlen fájlba írni.

| Forgatókönyv                                           | Javasolt beállítások                                           |
| -------------------------------------------------- | ------------------------------------------------------------ |
| Teljes terhelés a nagy asztalról.                        | **Partícióopció**: A tábla fizikai partíciói. <br><br/>A végrehajtás során a Data Factory automatikusan észleli a megadott SAP HANA-tábla fizikai partíciótípusát, és kiválasztja a megfelelő partícióstratégiát:<br>- **Tartományparticionálás**: A partícióoszlop és a partíciótartományok bekéselése a táblához, majd másolja az adatokat tartomány szerint. <br>- **Kivonatoló particionálás:** Használja a kivonatpartíciókulcsot partícióoszlopként, majd particionálja és másolja az adatokat az ADF számított tartományai alapján. <br>- **Ciklikus multiplexelés particionálás** vagy **nincs partíció:** Használja az elsődleges kulcsot partícióoszlopként, majd particionálja és másolja az adatokat az ADF számított tartományai alapján. |
| Nagy mennyiségű adatot tölthet be egyéni lekérdezéssel. | **Partíció opció**: Dinamikus tartománypartíció.<br>**Lekérdezés** `SELECT * FROM <TABLENAME> WHERE ?AdfHanaDynamicRangePartitionCondition AND <your_additional_where_clause>`: .<br>**Partícióoszlop:** Adja meg a dinamikus tartománypartíció alkalmazásához használt oszlopot. <br><br>A végrehajtás során a Data Factory először kiszámítja a megadott partícióoszlop értéktartományait, egyenletesen elosztja a sorokat több gyűjtőben az eltérő partícióoszlop-értékek és az ADF párhuzamos másolási beállítása szerint, majd lecseréli `?AdfHanaDynamicRangePartitionCondition` az egyes partíciók partícióoszlopérték-tartományának szűrésére, és elküldi az SAP HANA-nak.<br><br>Ha több oszlopot szeretne partícióoszlopként használni, az egyes oszlopok értékeit összefűzheti a lekérdezés egy oszlopaként, és megadhatja partícióoszlopként az ADF-ben, például `SELECT * FROM (SELECT *, CONCAT(<KeyColumn1>, <KeyColumn2>) AS PARTITIONCOLUMN FROM <TABLENAME>) WHERE ?AdfHanaDynamicRangePartitionCondition`. |

**Példa: lekérdezés tábla fizikai partícióival**

```json
"source": {
    "type": "SapHanaSource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**Példa: lekérdezés dinamikus tartománypartícióval**

```json
"source": {
    "type": "SapHanaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfHanaDynamicRangePartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "SapHanaDynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<Partition_column_name>"
    }
}
```

## <a name="data-type-mapping-for-sap-hana"></a>Az SAP HANA adattípus-leképezése

Az SAP HANA-ból származó adatok másolásakor a következő leképezések az SAP HANA adattípusokból az Azure Data Factory köztes adattípusok. A [Séma- és adattípus-hozzárendelések](copy-activity-schema-and-type-mapping.md) című témakörből megtudhatja, hogy a másolási tevékenység hogyan képezi le a forrássémát és az adattípust a fogadóhoz.

| SAP HANA adattípus | Adatgyár köztes adattípusa |
| ------------------ | ------------------------------ |
| ALFANUM           | Sztring                         |
| BIGINT között             | Int64                          |
| Bináris             | Bájt[]                         |
| BINTEXT            | Sztring                         |
| Blob               | Bájt[]                         |
| Bool               | Bájt                           |
| CLOB               | Sztring                         |
| DATE               | DateTime                       |
| Decimális            | Decimal                        |
| Dupla             | Double                         |
| Úszó              | Double                         |
| EGÉSZ SZÁM            | Int32                          |
| NCLOB között              | Sztring                         |
| NVARCHAR között           | Sztring                         |
| VALÓS SZÁM               | Egyirányú                         |
| MÁSODIK DÁTUM         | DateTime                       |
| RÖVID SZÖVEG          | Sztring                         |
| KISKEDÉK       | Decimal                        |
| SMALLINT között           | Int16                          |
| STGEOMETRYTYPE     | Bájt[]                         |
| STPOINTTYPE        | Bájt[]                         |
| TEXT               | Sztring                         |
| TIME               | időtartam                       |
| Piciszt            | Bájt                           |
| Varchar            | Sztring                         |
| Időbélyeg          | DateTime                       |
| VARBINARY KÖZÖTT          | Bájt[]                         |

## <a name="lookup-activity-properties"></a>A keresgaszíntevékenység tulajdonságai

A tulajdonságokrészleteinek megismeréséhez ellenőrizze a [Kereskövetési tevékenységet.](control-flow-lookup-activity.md)

## <a name="next-steps"></a>További lépések
A forrásként támogatott és fogadóként az Azure Data Factory másolási tevékenysége által támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats)című témakörben tetszhet.
