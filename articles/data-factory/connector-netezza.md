---
title: Netezza adatokat másol az Azure Data Factory használatával |} A Microsoft Docs
description: Megtudhatja, hogyan másolhat adatokat a Netezza támogatott fogadó adattárakba az Azure Data Factory-folyamatot egy másolási tevékenység használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: jingwang
ms.openlocfilehash: 7664c2f4fd08e06b51734b5508871b67d1a1b7c9
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/03/2019
ms.locfileid: "70231402"
---
# <a name="copy-data-from-netezza-by-using-azure-data-factory"></a>Netezza adatokat másol az Azure Data Factory használatával

Ez a cikk az Azure Data Factory másolási tevékenység használatával adatokat másol a Netezza módját ismerteti. A cikk számos tekintetben [másolási tevékenységgel az Azure Data Factoryban](copy-activity-overview.md), amely megadja, hogy a másolási tevékenység általános áttekintést.

## <a name="supported-capabilities"></a>Támogatott képességek

Másolhat adatokat Netezza bármely támogatott fogadó adattárba. Az adatok listáját tárolja, hogy a másolási tevékenység támogatja a forrásként és fogadóként, lásd: [támogatott adattárak és formátumok](copy-activity-overview.md#supported-data-stores-and-formats).

Az Netezza-összekötő támogatja a forrásról való párhuzamos másolást. A részletekért tekintse meg a [Parallel másolás a Netezza](#parallel-copy-from-netezza) szakaszát.

Az Azure Data Factory-kapcsolat beépített illesztőprogramot tartalmaz. Nem kell manuálisan telepítenie az összes illesztőprogram az összekötő használatához.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Bevezetés

Létrehozhat egy folyamatot, amely egy másolási tevékenységet használ a .NET SDK-t, a Python SDK-t, az Azure PowerShell, a REST API vagy egy Azure Resource Manager-sablon használatával. Tekintse meg a [másolási tevékenység oktatóanyagát](quickstart-create-data-factory-dot-net.md) kapcsolatos lépésenkénti útmutatót egy másolási tevékenységgel rendelkező folyamat létrehozása.

A következő szakaszok segítségével konkrétan a Netezza-összekötő a Data Factory-entitások definiálása-tulajdonságokkal kapcsolatos részletekért.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A következő tulajdonságok támogatottak a Netezza társított szolgáltatást:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A **típus** tulajdonságot állítsa **Netezza**. | Igen |
| connectionString | Az ODBC kapcsolati karakterlánc Netezza csatlakozni. <br/>A mező megjelölése SecureString, hogy biztonságosan tárolja Data Factoryban. A jelszót a Azure Key Vaultban is elhelyezheti, és `pwd` lekérheti a konfigurációt a kapcsolatok sztringből. További részletekért tekintse meg a következő mintákat, és [tárolja a hitelesítő adatokat Azure Key Vault](store-credentials-in-key-vault.md) cikkben. | Igen |
| connectVia | A [Integration Runtime](concepts-integration-runtime.md) kapcsolódni az adattárhoz. További tudnivalók az [Előfeltételek](#prerequisites) szakaszban olvashatók. Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. |Nem |

Egy tipikus kapcsolati karakterlánc `Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>`. A következő táblázat ismerteti, amely lehet további tulajdonságok:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| SecurityLevel | Az adattárban való csatlakozáshoz használja az illesztőprogram (SSL/TLS) biztonsági szintjét. Példa: `SecurityLevel=preferredSecured`. Támogatott értékei a következők:<br/>- **Csak nem biztonságos** (**onlyUnSecured**): Az illesztőprogram nem használ SSL-t.<br/>- **Előnyben részesített nem védett (preferredUnSecured) (alapértelmezett)** : Ha a kiszolgáló választási lehetőséget biztosít, az illesztőprogram nem használ SSL-t. <br/>- **Előnyben részesített biztonságos (preferredSecured)** : Ha a kiszolgáló választási lehetőséget biztosít, az illesztőprogram SSL-t használ. <br/>- **Csak biztonságos (onlySecured)** : Az illesztőprogram nem csatlakozik, ha nem érhető el SSL-kapcsolat. | Nem |
| CaCertFile | A kiszolgáló által használt SSL-tanúsítvány teljes elérési útja. Például: `CaCertFile=<cert path>;`| Igen, ha SSL engedélyezve van |

**Példa**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Példa: a Jelszó tárolása Azure Key Vault**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": {
                 "type": "SecureString",
                 "value": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;"
            },
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

Ez a szakasz felsorolja, amely támogatja a Netezza adatkészlet tulajdonságai.

Szakaszok és adatkészletek definiálását tulajdonságok teljes listáját lásd: [adatkészletek](concepts-datasets-linked-services.md).

Adatok másolása Netezza, állítsa be a **típusa** tulajdonság, az adatkészlet **NetezzaTable**. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | Az adatkészlet Type tulajdonságát a következőre kell beállítani: **NetezzaTable** | Igen |
| schema | A séma neve. |Nem (Ha a tevékenység forrása az "query" van megadva)  |
| table | A tábla neve. |Nem (Ha a tevékenység forrása az "query" van megadva)  |
| tableName | A sémával rendelkező tábla neve. Ez a tulajdonság visszamenőleges kompatibilitás esetén támogatott. A `schema` és`table` az új számítási feladatok használata. | Nem (Ha a tevékenység forrása az "query" van megadva) |

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

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Ez a szakasz felsorolja, amely támogatja a Netezza forrás tulajdonságai.

Szakaszok és a tevékenységek definiálását tulajdonságok teljes listáját lásd: [folyamatok](concepts-pipelines-activities.md).

### <a name="netezza-as-source"></a>Netezza forrásként

>[!TIP]
>Az adatok Netezza történő hatékony betöltéséhez az adatparticionálás használatával tudjon meg többet a [párhuzamos másolásról a Netezza](#parallel-copy-from-netezza) szakaszból.

Adatok másolása Netezza, állítsa be a **forrás** írja be a másolási tevékenység **NetezzaSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A **típus** értékre kell állítani a másolási tevékenység forrás tulajdonság **NetezzaSource**. | Igen |
| query | Az egyéni SQL-lekérdezés segítségével olvassa el az adatokat. Például: `"SELECT * FROM MyTable"` | Nem (Ha a "tableName" adatkészlet paraméter van megadva) |
| partitionOptions | Meghatározza az adatok Netezza való betöltéséhez használt adatparticionálási beállításokat. <br>Értékek engedélyezése: **Nincs** (alapértelmezett), **DataSlice** és **DynamicRange**.<br>Ha engedélyezve van egy partíciós beállítás (azaz nem `None`), a Netezza-adatbázisból származó adatok párhuzamos betöltésének foka a másolási tevékenység [`parallelCopies`](copy-activity-performance.md#parallel-copy) beállításával szabályozható. | Nem |
| partitionSettings | Határozza meg az adatparticionálási beállítások csoportját. <br>Akkor alkalmazza, ha a `None`partíció beállítása nem. | Nem |
| partitionColumnName | Adja meg a forrás oszlop **olyan egész típusú** nevét, amelyet a párhuzamos másolási tartomány particionálásakor fog használni. Ha nincs megadva, a rendszer automatikusan észleli a tábla elsődleges kulcsát, és a partíció oszlopként használja. <br>Akkor alkalmazza, ha a partíciós beállítás van `DynamicRange`. Ha lekérdezést használ a forrásadatok beolvasásához, `?AdfRangePartitionColumnName` akkor a WHERE záradékot kell beolvasnia. Lásd: példa [párhuzamos másolással a Netezza](#parallel-copy-from-netezza) szakaszból. | Nem |
| partitionUpperBound | Az adatmásolásra szolgáló partíciós oszlop maximális értéke. <br>Akkor alkalmazza, ha a `DynamicRange`partíció lehetőség van. Ha lekérdezést használ a forrásadatok beolvasásához, a `?AdfRangePartitionUpbound` where záradékban lévő hookot. Példaként tekintse meg a [Parallel másolás a Netezza](#parallel-copy-from-netezza) szakaszát. | Nem |
| partitionLowerBound | Az adatmásolásra szolgáló partíciós oszlop minimális értéke. <br>Akkor alkalmazza, ha a partíciós beállítás van `DynamicRange`. Ha lekérdezést használ a forrásadatok beolvasásához, a WHERE záradékban lévő hookot `?AdfRangePartitionLowbound` . Példaként tekintse meg a [Parallel másolás a Netezza](#parallel-copy-from-netezza) szakaszát. | Nem |

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

## <a name="parallel-copy-from-netezza"></a>Párhuzamos másolás a Netezza

A Data Factory Netezza-összekötő beépített adatparticionálást biztosít az adatok párhuzamos Netezza való másolásához. Az adatparticionálási beállítások a másolási tevékenység **forrástábla** tábláján találhatók.

![Képernyőfelvétel a partíciós beállításokról](./media/connector-netezza/connector-netezza-partition-options.png)

A particionált másolás engedélyezésekor a Data Factory párhuzamos lekérdezéseket futtat a Netezza-forráson az adatpartíciók betöltéséhez. A párhuzamos mértéket a másolási [`parallelCopies`](copy-activity-performance.md#parallel-copy) tevékenység beállításai vezérlik. Ha például a négy értékre `parallelCopies` van állítva, Data Factory egyidejűleg létrehoz és futtat négy lekérdezést a megadott partíciós beállítás és beállítások alapján, és mindegyik lekérdezés a Netezza-adatbázisból származó adatok egy részét kéri le.

Javasoljuk, hogy engedélyezze a párhuzamos másolást az adatok particionálásával, különösen akkor, ha nagy mennyiségű adatmennyiséget tölt be a Netezza-adatbázisból. Az alábbiakban a különböző forgatókönyvekhez javasolt konfigurációk szerepelnek. Az adatok file-alapú adattárba való másolása során a rendszer úgy helyezi át, hogy több fájlként írjon egy mappába (csak a mappa nevét adja meg), amely esetben a teljesítmény jobb, mint egyetlen fájl írásakor.

| Forgatókönyv                                                     | Javasolt beállítások                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Teljes terhelés a nagyméretű táblából.                                   | **Partíciós beállítás**: Adatszelet <br><br/>A végrehajtás során a Data Factory automatikusan particionálja az [Netezza-](https://www.ibm.com/support/knowledgecenter/en/SSULQD_7.2.1/com.ibm.nz.adm.doc/c_sysadm_data_slices_parts_disks.html)t a beépített adatszeletek alapján, és az Adatmásolást partíciók szerint másolja. |
| Nagy mennyiségű adattal tölthetők be egyéni lekérdezések használatával.                 | **Partíciós beállítás**: Adatszelet<br>**Lekérdezés**: `SELECT * FROM <TABLENAME> WHERE mod(datasliceid, ?AdfPartitionCount) = ?AdfDataSliceCondition AND <your_additional_where_clause>`.<br>A végrehajtás során Data Factory lecseréli `?AdfPartitionCount` (párhuzamos másolási számmal a másolási tevékenységnél) és `?AdfDataSliceCondition` az adatszelet partíciós logikáját, és elküldi a Netezza. |
| Nagy mennyiségű adattal tölthetők be egy egyéni lekérdezéssel, amely egy egész számú oszlopból álló, egyenletesen elosztott értékkel rendelkezik a tartomány particionálásához. | **Partíciós beállítások**: Dinamikus tartomány partíciója.<br>**Lekérdezés**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**Partíciós oszlop**: Az adatparticionáláshoz használt oszlop meghatározása. Az oszlop egész adattípussal is particionálható.<br>**Partíció felső határa** és a **partíció alsó határa**: Adja meg, hogy a partíció oszlop alapján szeretne-e szűrést végezni, hogy csak az alsó és a felső tartomány között legyenek az adatlekérdezések.<br><br>A végrehajtás során a Data Factory `?AdfRangePartitionColumnName`lecseréli `?AdfRangePartitionLowbound` `?AdfRangePartitionUpbound`, és az egyes partíciók tényleges oszlop-és értéktartomány-tartományát, és elküldi a Netezza. <br>Ha például az "ID" partíciós oszlop az 1 értékre van állítva, a felső határ pedig 80, a párhuzamos másolási beállítás értéke 4, Data Factory 4 partíció alapján kérdezi le az adatmennyiséget. Az azonosítóik [1, 20], [21, 40], [41, 60] és [61, 80] között vannak. |

**Példa: lekérdezés adatszelet-partícióval**

```json
"source": {
    "type": "NetezzaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE mod(datasliceid, ?AdfPartitionCount) = ?AdfDataSliceCondition AND <your_additional_where_clause>",
    "partitionOption": "DataSlice"
}
```

**Példa: lekérdezés dinamikus tartományú partícióval**

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

## <a name="next-steps"></a>További lépések

Másolási tevékenységet támogatja az adatforrások és az Azure Data Factoryban fogadók adattárak listáját lásd: [támogatott adattárak és formátumok](copy-activity-overview.md#supported-data-stores-and-formats).
