---
title: Adatok másolása a Netezza-ból az Azure Data Factory használatával
description: Megtudhatja, hogyan másolhatja az adatokat a Netezza-ból a támogatott fogadó adattárakba egy Azure Data Factory-folyamat másolási tevékenységének használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: jingwang
ms.openlocfilehash: 89efa8dc9989f693964415741299042c63f93780
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418116"
---
# <a name="copy-data-from-netezza-by-using-azure-data-factory"></a>Adatok másolása a Netezza-ból az Azure Data Factory használatával
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk bemutatja, hogyan használhatja a másolási tevékenység az Azure Data Factory adatok másolása netezza. A cikk az [Azure Data Factory másolási tevékenységére](copy-activity-overview.md)épül, amely általános áttekintést nyújt a másolási tevékenységről.

>[!TIP]
>A Netezza-ból az Azure-ba történő adatáttelepítési forgatókönyvről az [Azure Data Factory használatával a helyszíni Netezza kiszolgálóról az Azure-ba való átáttelepítésről](data-migration-guidance-netezza-azure-sqldw.md)olvashat bővebben.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez a Netezza-összekötő a következő tevékenységek esetén támogatott:

- [Tevékenység másolása](copy-activity-overview.md) [támogatott forrás/fogadó mátrixcal](copy-activity-overview.md)
- [Keress tevékenységet](control-flow-lookup-activity.md)


A Netezza-ból adatokat másolhat bármely támogatott fogadó adattárba. A Másolási tevékenység által forrásként és fogadóként támogatott adattárak listáját a [Támogatott adattárak és -formátumok című témakörben tetszhet.](copy-activity-overview.md#supported-data-stores-and-formats)

A Netezza összekötő támogatja a forrásból történő párhuzamos másolást. A [részleteket a Netezza párhuzamos másolata](#parallel-copy-from-netezza) című részében találja.

Az Azure Data Factory beépített illesztőprogramot biztosít a kapcsolat engedélyezéséhez. Az összekötő használatához nem kell manuálisan telepítenie egyetlen illesztőprogramot sem.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Bevezetés

Létrehozhat egy folyamatot, amely egy másolási tevékenységet használ a .NET SDK, a Python SDK, az Azure PowerShell, a REST API vagy egy Azure Resource Manager sablon használatával. Tekintse meg a [Másolási tevékenység oktatóanyag](quickstart-create-data-factory-dot-net.md) lépésről-lépésre, hogyan hozhat létre egy folyamat, amely egy másolási tevékenységet.

A következő szakaszok a Netezza-összekötőre jellemző Data Factory-entitások definiálásához használható tulajdonságok részleteit ismertetik.

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai

A Netezza kapcsolt szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A **típustulajdonságot** **Netezza**tulajdonságra kell állítani. | Igen |
| connectionString (kapcsolati karakterlánc) | OdBC kapcsolati karakterlánc a Netezza-hoz való csatlakozáshoz. <br/>Az Azure Key Vaultban is felhelyezhet idúra, és kihúzhatja a `pwd` konfigurációt a kapcsolati karakterláncból. További részleteket az [Azure Key Vault-cikkben](store-credentials-in-key-vault.md) a következő minták és áruházi hitelesítő adatok című cikkben talál. | Igen |
| connectVia | Az adattárhoz való csatlakozáshoz használt [integrációs futásidő.](concepts-integration-runtime.md) További információ az [Előfeltételek](#prerequisites) szakaszból. Ha nincs megadva, a rendszer az alapértelmezett Azure-integrációs futásidőt használja. |Nem |

Egy tipikus kapcsolati karakterlánc a `Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>`. Az alábbi táblázat további, beállítható tulajdonságokat ismertet:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| Biztonsági szint | Az illesztőprogram által az adattárhoz való csatlakozáshoz használt biztonsági szint (SSL/TLS). Példa: `SecurityLevel=preferredSecured`. A támogatott értékek a következők:<br/>- **Csak nem biztonságos** **(csak nem biztonságos):** Az illesztőprogram nem használja a TLS-t.<br/>- **Előnyben részesített nem biztonságos (előnyben részesített Nem biztonságos) (alapértelmezett)**: Ha a kiszolgáló választási lehetőséget biztosít, az illesztőprogram nem használja a TLS-t. <br/>- **Előnyben részesített biztonságos (preferredSecured)**: Ha a kiszolgáló választási lehetőséget biztosít, az illesztőprogram a TLS-t használja. <br/>- **Csak biztonságos (csak biztonságos)**: Az illesztőprogram csak akkor csatlakozik, ha rendelkezésre áll TLS-kapcsolat. | Nem |
| CaCertFájl | A kiszolgáló által használt TLS/SSL-tanúsítvány teljes elérési útja. Például: `CaCertFile=<cert path>;`| Igen, ha a TLS engedélyezve van |

**Példa**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Példa: jelszó tárolása az Azure Key Vaultban**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;",
            "pwd": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
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

Ez a szakasz a Netezza adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Az adatkészletek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját az Adatkészletek című [témakörben található.](concepts-datasets-linked-services.md)

Ha adatokat szeretne másolni a Netezza-ból, állítsa az adatkészlet **típustulajdonságát** **NetezzaTable beállításra.** A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | Az adatkészlet típustulajdonságát a következőre kell állítani: **NetezzaTable** | Igen |
| Séma | A séma neve. |Nem (ha a "lekérdezés" a tevékenységforrásban meg van adva)  |
| tábla | A tábla neve. |Nem (ha a "lekérdezés" a tevékenységforrásban meg van adva)  |
| tableName | A sémával rendelkező tábla neve. Ez a tulajdonság a visszamenőleges kompatibilitás érdekében támogatott. Használja `schema` `table` és új munkaterheléshez. | Nem (ha a "lekérdezés" a tevékenységforrásban meg van adva) |

**Példa**

```json
{
    "name": "NetezzaDataset",
    "properties": {
        "type": "NetezzaTable",
        "linkedServiceName": {
            "referenceName": "<Netezza linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Tevékenység tulajdonságainak másolása

Ez a szakasz a Netezza-forrás által támogatott tulajdonságok listáját tartalmazza.

A tevékenységek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját a [Folyamatok című témakörben található.](concepts-pipelines-activities.md)

### <a name="netezza-as-source"></a>Netezza mint forrás

>[!TIP]
>Ha az adatokat hatékonyan szeretné betölteni a Netezza-ból az adatparticionálás használatával, további információ a [Netezza párhuzamos másolása szakaszból.](#parallel-copy-from-netezza)

Ha adatokat szeretne másolni a Netezza-ból, állítsa be a **forrástípust** a Tevékenység másolása mezőbe **Source**. A következő tulajdonságokat támogatja a Másolási tevékenység **forrásszakasz:**

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A Másolási tevékenység forrás **típustulajdonságát** **NetezzaSource (NetezzaSource )** tulajdonságra kell állítani. | Igen |
| lekérdezés | Az adatok olvasásához használja az egyéni SQL-lekérdezést. Például: `"SELECT * FROM MyTable"` | Nem (ha az adatkészletben a "tableName" van megadva) |
| partitionOptions | Megadja a Netezza-ból származó adatok betöltéséhez használt adatparticionálási beállításokat. <br>Az engedélyezési értékek a következők: **Nincs** (alapértelmezett), **DataSlice**és **DynamicRange**.<br>Ha egy partícióbeállítás engedélyezve van `None`(azaz nem), a Netezza adatbázisból származó adatok egyidejű [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) betöltéséhez szükséges párhuzamosság mértékét a másolási tevékenység beállítása szabályozza. | Nem |
| partitionSettings (partícióbeállításai) | Adja meg az adatparticionálás beállításainak csoportját. <br>Alkalmazza, ha a `None`partícióbeállítás nem . | Nem |
| partitionColumnName | Adja meg a forrásoszlop nevét **egész típusban,** amelyet a tartománypartidatás a párhuzamos másoláshoz használ. Ha nincs megadva, a tábla elsődleges kulcsa automatikusan észlelhető, és partícióoszlopként lesz használva. <br>Alkalmazza, ha a `DynamicRange`partíciós beállítás . Ha lekérdezéssel olvassa be a forrásadatokat, a HOOK a `?AdfRangePartitionColumnName` WHERE záradékban. Lásd a példát a [Netezza párhuzamos másolása szakaszban.](#parallel-copy-from-netezza) | Nem |
| partitionUpperBound között | Az adatok másolásához a partícióoszlop maximális értéke. <br>Alkalmazza, ha `DynamicRange`a partíciós beállítás . Ha lekérdezéssel olvassa be a `?AdfRangePartitionUpbound` forrásadatokat, a HOOK a WHERE záradékban. Például tekintse meg a [Netezza párhuzamos másolata](#parallel-copy-from-netezza) szakaszt. | Nem |
| partitionLowerBound | Az adatok másolásához szükséges partícióoszlop minimális értéke. <br>Alkalmazza, ha a `DynamicRange`partíciós beállítás . Ha lekérdezéssel olvassa be a forrásadatokat, a HOOK a `?AdfRangePartitionLowbound` WHERE záradékban. Például tekintse meg a [Netezza párhuzamos másolata](#parallel-copy-from-netezza) szakaszt. | Nem |

**Példa:**

```json
"activities":[
    {
        "name": "CopyFromNetezza",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Netezza input dataset name>",
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
                "type": "NetezzaSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="parallel-copy-from-netezza"></a>Párhuzamos másolat a Netezza-ról

A Data Factory Netezza összekötő beépített adatparticionálást biztosít a Netezza adatainak párhuzamos másolásához. Az adatparticionálási beállításokat a másolási tevékenység **Forrás** tábláján találja.

![A partícióbeállításainak képernyőképe](./media/connector-netezza/connector-netezza-partition-options.png)

Ha engedélyezi a particionált másolást, a Data Factory párhuzamos lekérdezéseket futtat a Netezza forráson az adatok partíciók általi betöltéséhez. A párhuzamos mértéket a [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) másolási tevékenység beállítása szabályozza. Ha például négyre állítja, `parallelCopies` a Data Factory egyidejűleg négy lekérdezést hoz létre és futtat a megadott partícióbeállítás és -beállítások alapján, és minden lekérdezés lekéri az adatok egy részét a Netezza adatbázisból.

Javasoljuk, hogy engedélyezze a párhuzamos másolást az adatparticionálással, különösen akkor, ha nagy mennyiségű adatot tölt be a Netezza adatbázisból. Az alábbiakban a különböző forgatókönyvekhez javasolt konfigurációkat javasoljuk. Amikor adatokat másol fájlalapú adattárba, a rendszer azt a parancsot adja meg, hogy egy mappába több fájlként írjon (csak adja meg a mappa nevét), ebben az esetben a teljesítmény jobb, mint egyetlen fájlba írni.

| Forgatókönyv                                                     | Javasolt beállítások                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Teljes terhelés a nagy asztalról.                                   | **Partíció opció**: Adatszelet. <br><br/>A végrehajtás során a Data Factory automatikusan particionálja az adatokat a [Netezza beépített adatszeletei](https://www.ibm.com/support/knowledgecenter/en/SSULQD_7.2.1/com.ibm.nz.adm.doc/c_sysadm_data_slices_parts_disks.html)alapján, és partíciók szerint másolja az adatokat. |
| Nagy mennyiségű adatot tölthet be egyéni lekérdezéssel.                 | **Partíció opció**: Adatszelet.<br>**Lekérdezés** `SELECT * FROM <TABLENAME> WHERE mod(datasliceid, ?AdfPartitionCount) = ?AdfDataSliceCondition AND <your_additional_where_clause>`: .<br>A végrehajtás során a `?AdfPartitionCount` Data Factory lecseréli (a `?AdfDataSliceCondition` másolási tevékenységre beállított párhuzamos másolási számmal) és az adatszelet partíciólogikájára, és elküldi a Netezza-nak. |
| Nagy mennyiségű adatot tölthet be egyéni lekérdezéssel, amelynek egész oszlopa egyenletesen elosztott értékkel rendelkezik a tartományparticionáláshoz. | **Partíció beállításai**: Dinamikus tartományú partíció.<br>**Lekérdezés** `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`: .<br>**Partícióoszlop:** Adja meg az adatok particionálásához használt oszlopot. Az egész adattípussal rendelkező oszlopra particionálhat.<br>**Partíció felső és** **alsó határa**: Adja meg, hogy a partícióoszlophoz szűrve csak az alsó és a felső tartomány között szeretne adatokat beolvasni.<br><br>A végrehajtás során a `?AdfRangePartitionColumnName` `?AdfRangePartitionUpbound`Data `?AdfRangePartitionLowbound` Factory lecseréli a , és az egyes partíciók tényleges oszlopnevét és értéktartományait, és elküldi a Netezza-nak. <br>Ha például az "ID" partícióoszlop az alsó határ 1, a felső határ pedig 80, a párhuzamos másolat pedig 4, a Data Factory 4 partícióval olvassa be az adatokat. Azonosítóik [1,20], [21, 40], [41, 60] és [61, 80] között vannak. |

**Példa: lekérdezés adatszelet-partícióval**

```json
"source": {
    "type": "NetezzaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE mod(datasliceid, ?AdfPartitionCount) = ?AdfDataSliceCondition AND <your_additional_where_clause>",
    "partitionOption": "DataSlice"
}
```

**Példa: lekérdezés dinamikus tartománypartícióval**

```json
"source": {
    "type": "NetezzaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<dynamic_range_partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="lookup-activity-properties"></a>A keresgaszíntevékenység tulajdonságai

A tulajdonságokrészleteinek megismeréséhez ellenőrizze a [Kereskövetési tevékenységet.](control-flow-lookup-activity.md)


## <a name="next-steps"></a>További lépések

Az Azure Data Factory ban a Másolási tevékenység által adatforrásként és fogadóként támogatott adattárak listáját a [Támogatott adattárak és -formátumok című témakörben tetszhet.](copy-activity-overview.md#supported-data-stores-and-formats)
