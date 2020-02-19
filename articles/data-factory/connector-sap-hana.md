---
title: Adatok másolása SAP HANAról
description: Megtudhatja, hogyan másolhat adatok SAP HANAból a támogatott fogadó adattárakba egy Azure Data Factory-folyamat másolási tevékenységének használatával.
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
ms.sourcegitcommit: dfa543fad47cb2df5a574931ba57d40d6a47daef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2020
ms.locfileid: "77431099"
---
# <a name="copy-data-from-sap-hana-using-azure-data-factory"></a>Adatok másolása SAP HANA használatával Azure Data Factory
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-sap-hana-connector.md)
> * [Aktuális verzió](connector-sap-hana.md)

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok SAP HANA-adatbázisból történő másolásához. A másolási [tevékenység áttekintő](copy-activity-overview.md) cikkében található, amely a másolási tevékenység általános áttekintését jeleníti meg.

>[!TIP]
>Az ADF SAP-adatintegrációs forgatókönyvre vonatkozó általános támogatásának megismeréséhez tekintse meg az [SAP-Adatintegráció Azure Data Factory tanulmány használatával](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) részletes bevezetést, comparsion és útmutatást.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez az SAP HANA-összekötő a következő tevékenységek esetén támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

Az adatok másolása SAP HANA adatbázisból bármely támogatott fogadó adattárba. A másolási tevékenység által a forrásként/mosogatóként támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblázatban tekintheti meg.

Pontosabban, ez az SAP HANA-összekötő a következőket támogatja:

- Adatok másolása SAP HANA adatbázis bármely verziójáról.
- Adatok másolása a **HANA-információs modellből** (például analitikai és számítási nézetből) és **sor/oszlop táblákból**.
- Adatok másolása az **alapszintű** vagy a **Windows** -hitelesítéssel.
- Párhuzamos másolás egy SAP HANA forrásból. A részletekért tekintse meg a [Parallel másolás SAP HANAről](#parallel-copy-from-sap-hana) című szakaszt.

> [!TIP]
> Az Adatmásolás **SAP HANA adattárba való** másolásához használja az általános ODBC-összekötőt. További részletek: [SAP HANA](connector-odbc.md#sap-hana-sink) fogadó. Figyelje meg, hogy SAP HANA összekötőhöz társított szolgáltatások és az ODBC-összekötő eltérő típusú, ezért nem használható fel újra.

## <a name="prerequisites"></a>Előfeltételek

A SAP HANA-összekötő használatához a következőket kell tennie:

- Saját üzemeltetésű Integration Runtime beállítása. További részletekért tekintse meg a saját üzemeltetésű [Integration Runtime](create-self-hosted-integration-runtime.md) szóló cikket.
- Telepítse a SAP HANA ODBC-illesztőt a Integration Runtime gépen. Az SAP HANA ODBC-illesztőprogramot az [SAP Software Download Center](https://support.sap.com/swdc)webhelyről töltheti le. Keressen rá a **Windows-ügyfél kulcsszava SAP HANA**.

## <a name="getting-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük azokat a tulajdonságokat, amelyek Data Factory SAP HANA-összekötőhöz tartozó entitások definiálásához használatosak.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

SAP HANA társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A Type tulajdonságot a következőre kell beállítani: **SapHana** | Igen |
| connectionString | Adjon meg olyan információt, amely az **alapszintű hitelesítés** vagy a **Windows-hitelesítés**használatával a SAP HANAhoz való kapcsolódáshoz szükséges. Tekintse át a következő mintákat.<br>A kapcsolódási karakterláncban a kiszolgáló/port kötelező (az alapértelmezett port 30015), és a Felhasználónév és a jelszó megadása kötelező, ha alapszintű hitelesítést használ. További speciális beállításokért lásd: [SAP HANA ODBC-kapcsolatok tulajdonságai](<https://help.sap.com/viewer/0eec0d68141541d1b07893a39944924e/2.0.02/en-US/7cab593774474f2f8db335710b2f5c50.html>)<br/>A jelszót a Azure Key Vaultban is elhelyezheti, és lekérheti a jelszó konfigurációját a kapcsolatok karakterláncán kívül. További részletekért tekintse meg a [hitelesítő adatok tárolása Azure Key Vault](store-credentials-in-key-vault.md) cikkben. | Igen |
| userName | Windows-hitelesítés használata esetén adja meg a felhasználónevet. Például: `user@domain.com` | Nem |
| jelszó | A felhasználói fiók jelszavának megadása. Megjelöli ezt a mezőt SecureString, hogy biztonságosan tárolja Data Factoryban, vagy [hivatkozjon a Azure Key Vault tárolt titkos kulcsra](store-credentials-in-key-vault.md). | Nem |
| connectVia | Az adattárhoz való kapcsolódáshoz használt [Integration Runtime](concepts-integration-runtime.md) . A saját üzemeltetésű Integration Runtime az [Előfeltételek](#prerequisites)szakaszban említettek szerint kell megadni. |Igen |

**Példa: egyszerű hitelesítés használata**

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

Ha SAP HANA társított szolgáltatást használta a következő hasznos adattartalommal, akkor továbbra is támogatott, miközben a rendszer azt javasolja, hogy az új továbbítást használja.

**Példa**

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

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját az [adatkészletek](concepts-datasets-linked-services.md) című cikkben találja. Ez a szakasz a SAP HANA adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Az adatok SAP HANAból történő másolásához a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | Az adatkészlet Type tulajdonságát a következőre kell beállítani: **SapHanaTable** | Igen |
| schema | A séma neve a SAP HANA adatbázisban. | Nem (Ha a tevékenység forrása az "query" van megadva) |
| table | A SAP HANA adatbázisban található tábla neve. | Nem (Ha a tevékenység forrása az "query" van megadva) |

**Példa**

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

Ha `RelationalTable` gépelt adatkészletet használ, a rendszer továbbra is támogatja a-t, míg a rendszer azt javasolja, hogy az új továbbítást használja.

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját a [folyamatok](concepts-pipelines-activities.md) című cikkben találja. Ez a szakasz a SAP HANA forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="sap-hana-as-source"></a>SAP HANA forrásként

>[!TIP]
>Az adatok adatparticionálással való hatékony betöltéséhez SAP HANA a [párhuzamos másolásról a SAP HANA](#parallel-copy-from-sap-hana) szakaszból tájékozódhat.

Az adatok SAP HANAból történő másolásához a másolási tevékenység **forrása** szakaszban a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának Type tulajdonságát a következőre kell beállítani: **SapHanaSource** | Igen |
| lekérdezés | Azt az SQL-lekérdezést adja meg, amely az SAP HANA-példányból olvassa be az adatok beolvasását. | Igen |
| partitionOptions | Meghatározza az adatok SAP HANAból való betöltéséhez használt adatparticionálási beállításokat. További információ a [párhuzamos másolásról SAP HANA](#parallel-copy-from-sap-hana) szakaszból.<br>Értékek engedélyezése: **None** (alapértelmezett), **PhysicalPartitionsOfTable**, **SapHanaDynamicRange**. További információ a [párhuzamos másolásról SAP HANA](#parallel-copy-from-sap-hana) szakaszból. `PhysicalPartitionsOfTable` csak táblázatból származó adatok másolása esetén használható, a lekérdezés nem lehetséges. <br>Ha engedélyezve van egy partíciós beállítás (azaz nem `None`), a párhuzamossági fok az adatok SAP HANAból való egyidejű betöltésének foka a másolási tevékenység [`parallelCopies`](copy-activity-performance.md#parallel-copy) beállításával szabályozható. | False (Hamis) |
| partitionSettings | Határozza meg az adatparticionálási beállítások csoportját.<br>Akkor alkalmazza, ha a partíció beállítása `SapHanaDynamicRange`. | False (Hamis) |
| partitionColumnName | Adja meg annak a forrás oszlopnak a nevét, amelyet a partíció a párhuzamos másoláshoz használ majd. Ha nincs megadva, a rendszer automatikusan észleli a tábla indexét vagy elsődleges kulcsát, és a partíció oszlopként használja.<br>Akkor alkalmazza, ha a partíció beállítása `SapHanaDynamicRange`. Ha lekérdezést használ a forrásadatok beolvasásához, a Hook `?AdfHanaDynamicRangePartitionCondition` a WHERE záradékban. Tekintse meg a [SAP HANA szakasz párhuzamos másolásának](#parallel-copy-from-sap-hana) példáját. | Igen, ha `SapHanaDynamicRange` partíciót használ. |
| packetSize | Meghatározza a hálózati csomagok méretét (kilobájtban), hogy több blokkra ossza fel az adatmennyiséget. Ha nagy mennyiségű adattal rendelkezik a másoláshoz, a csomagok méretének növelése a legtöbb esetben növelheti SAP HANA olvasási sebességét. A csomagok méretének beállításakor a teljesítmény tesztelése javasolt. | Nem.<br>Az alapértelmezett érték 2048 (2MB). |

**Példa**

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

Ha `RelationalSource` a beírt másolási forrást használta, a rendszer továbbra is támogatja a-t, míg a rendszer azt javasolja, hogy az új továbbítást használja.

## <a name="parallel-copy-from-sap-hana"></a>Párhuzamos másolás SAP HANAról

A Data Factory SAP HANA-összekötő beépített adatparticionálást biztosít az adatok SAP HANAból párhuzamos másolásához. Az adatparticionálási beállítások a másolási tevékenység **forrástábla** tábláján találhatók.

![Képernyőfelvétel a partíciós beállításokról](./media/connector-sap-hana/connector-sap-hana-partition-options.png)

A particionált másolás engedélyezésekor a Data Factory párhuzamos lekérdezéseket futtat a SAP HANA forrásán az adatpartíciók alapján történő lekéréséhez. A párhuzamos fokot a másolási tevékenység [`parallelCopies`](copy-activity-performance.md#parallel-copy) beállítása vezérli. Ha például a `parallelCopies`t négyre állítja be, Data Factory egyidejűleg létrehoz és futtat négy lekérdezést a megadott partíciós beállítás és beállítások alapján, és mindegyik lekérdezés az adatok egy részét kéri le a SAP HANA.

Javasoljuk, hogy engedélyezze a párhuzamos másolást az adatok particionálásával, különösen akkor, ha nagy mennyiségű adatot tölt be a SAP HANAból. Az alábbiakban a különböző forgatókönyvekhez javasolt konfigurációk szerepelnek. Az adatok file-alapú adattárba való másolása esetén ajánlott több fájlként írni egy mappába (csak a mappa nevét adja meg), ebben az esetben a teljesítmény jobb, mint egyetlen fájl írásakor.

| Forgatókönyv                                           | Javasolt beállítások                                           |
| -------------------------------------------------- | ------------------------------------------------------------ |
| Teljes terhelés a nagyméretű táblából.                        | **Partíciós beállítás**: a tábla fizikai partíciói. <br><br/>A végrehajtás során a Data Factory automatikusan észleli a megadott SAP HANA táblázat fizikai partíciójának típusát, és kiválasztja a megfelelő partíciós stratégiát:<br>- **tartomány particionálása**: szerezze be a táblához definiált partíciós oszlopot és partíciós tartományokat, majd másolja az adattartomány alapján. <br>- **kivonatoló particionálás**: használja a kivonatoló partíció kulcsát partíciós oszlopként, majd az adatokat particionálja és másolja az ADF számított tartományok alapján. <br>**ciklikus - particionálás** vagy **nincs partíció**: használja az elsődleges kulcsot partíciós oszlopként, majd particionálja és másolja az adatkészletet az ADF számított tartományai alapján. |
| Nagy mennyiségű adattal tölthetők be egyéni lekérdezések használatával. | **Partíciós beállítás**: dinamikus tartományú partíció.<br>**Lekérdezés**: `SELECT * FROM <TABLENAME> WHERE ?AdfHanaDynamicRangePartitionCondition AND <your_additional_where_clause>`.<br>**Partíciós oszlop**: a dinamikus tartományú partíció alkalmazásához használt oszlop meghatározása. <br><br>A végrehajtás során a Data Factory először kiszámítja a megadott partíciós oszlop értékeit, a sorok számának egyenletes elosztásával a partíciók oszlopainak száma és az ADF párhuzamos másolási beállítása alapján, majd lecseréli `?AdfHanaDynamicRangePartitionCondition` a partíció oszlop érték-tartományát az egyes partíciók számára, és elküldi a SAP HANAnak.<br><br>Ha több oszlopot kíván használni partíciós oszlopként, az egyes oszlopok értékeit egyetlen oszlopként összefűzve a lekérdezésben, és a partíciós oszlopként adhatja meg az ADF-ben (például `SELECT * FROM (SELECT *, CONCAT(<KeyColumn1>, <KeyColumn2>) AS PARTITIONCOLUMN FROM <TABLENAME>) WHERE ?AdfHanaDynamicRangePartitionCondition`). |

**Példa: lekérdezés egy tábla fizikai partíciókhoz**

```json
"source": {
    "type": "SapHanaSource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**Példa: lekérdezés dinamikus tartományú partícióval**

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

## <a name="data-type-mapping-for-sap-hana"></a>SAP HANA adattípusának leképezése

Az adatok SAP HANAból való másolása során a rendszer a következő leképezéseket használja SAP HANA adattípusokból Azure Data Factory köztes adattípusokra. A másolási tevékenység a forrás sémájának és adattípusának a fogadóba való leképezésével kapcsolatos tudnivalókat lásd: [séma-és adattípus-leképezések](copy-activity-schema-and-type-mapping.md) .

| SAP HANA adattípus | Data factory közbenső adattípus |
| ------------------ | ------------------------------ |
| ALPHANUM           | Sztring                         |
| BIGINT             | Int64                          |
| BINARY             | Byte[]                         |
| BINTEXT            | Sztring                         |
| BLOB               | Byte[]                         |
| BOOL               | Bájt                           |
| CLOB               | Sztring                         |
| DATE               | DateTime                       |
| DECIMAL            | tizedes tört                        |
| DOUBLE             | Dupla                         |
| FLOAT              | Dupla                         |
| INTEGER            | Int32                          |
| NCLOB              | Sztring                         |
| NVARCHAR           | Sztring                         |
| REAL               | Single                         |
| SECONDDATE         | DateTime                       |
| SHORTTEXT          | Sztring                         |
| SMALLDECIMAL       | tizedes tört                        |
| SMALLINT           | Int16                          |
| STGEOMETRYTYPE     | Byte[]                         |
| STPOINTTYPE        | Byte[]                         |
| TEXT               | Sztring                         |
| TIME               | Időtartam                       |
| TINYINT            | Bájt                           |
| VARCHAR            | Sztring                         |
| TIMESTAMP          | DateTime                       |
| VARBINARY          | Byte[]                         |

## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [keresési tevékenységet](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Következő lépések
A Azure Data Factory a másolási tevékenység által forrásként és nyelőként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
