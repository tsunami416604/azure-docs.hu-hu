---
title: Netezza adatokat másol az Azure Data Factory használatával
description: Megtudhatja, hogyan másolhat adatokat a Netezza támogatott fogadó adattárakba az Azure Data Factory-folyamatot egy másolási tevékenység használatával.
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
ms.openlocfilehash: c51469997af23be7a5e1b88677ecadb37e10ac64
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78358428"
---
# <a name="copy-data-from-netezza-by-using-azure-data-factory"></a>Netezza adatokat másol az Azure Data Factory használatával

Ez a cikk az Azure Data Factory másolási tevékenység használatával adatokat másol a Netezza módját ismerteti. A cikk a [másolási tevékenységre épül Azure Data Factoryban](copy-activity-overview.md), amely a másolási tevékenység általános áttekintését mutatja be.

>[!TIP]
>A Netezza-ből az Azure-ba irányuló adatáttelepítési forgatókönyvek esetében további információ a [Azure Data Factory használatával a helyszíni Netezza-kiszolgálóról az Azure-ba való Migrálás](data-migration-guidance-netezza-azure-sqldw.md)során.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez a Netezza-összekötő a következő tevékenységek esetén támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)


Másolhat adatokat Netezza bármely támogatott fogadó adattárba. A másolási tevékenység által a forrásként és a fogadóként támogatott adattárak listájáért lásd: [támogatott adattárak és-formátumok](copy-activity-overview.md#supported-data-stores-and-formats).

Az Netezza-összekötő támogatja a forrásról való párhuzamos másolást. A részletekért tekintse meg a [Parallel másolás a Netezza](#parallel-copy-from-netezza) szakaszát.

Az Azure Data Factory-kapcsolat beépített illesztőprogramot tartalmaz. Nem kell manuálisan telepítenie az összes illesztőprogram az összekötő használatához.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Első lépések

Létrehozhat egy folyamatot, amely egy másolási tevékenységet használ a .NET SDK-t, a Python SDK-t, az Azure PowerShell, a REST API vagy egy Azure Resource Manager-sablon használatával. A másolási tevékenységgel rendelkező folyamat létrehozásával kapcsolatos részletes utasításokért tekintse meg a [másolási tevékenységről szóló oktatóanyagot](quickstart-create-data-factory-dot-net.md) .

A következő szakaszok segítségével konkrétan a Netezza-összekötő a Data Factory-entitások definiálása-tulajdonságokkal kapcsolatos részletekért.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A következő tulajdonságok támogatottak a Netezza társított szolgáltatást:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A **Type** tulajdonságot **Netezza**értékre kell beállítani. | Igen |
| connectionString | Az ODBC kapcsolati karakterlánc Netezza csatlakozni. <br/>A jelszót a Azure Key Vaultban is elhelyezheti, és lekérheti a `pwd` konfigurációt a kapcsolatok sztringből. További részletekért tekintse meg a következő mintákat, és [tárolja a hitelesítő adatokat Azure Key Vault](store-credentials-in-key-vault.md) cikkben. | Igen |
| connectVia | Az adattárhoz való kapcsolódáshoz használt [Integration Runtime](concepts-integration-runtime.md) . További tudnivalók az [Előfeltételek](#prerequisites) szakaszban olvashatók. Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. |Nem |

Egy tipikus `Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>`a kapcsolatok karakterlánca. A következő táblázat ismerteti, amely lehet további tulajdonságok:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| SecurityLevel | Az adattárban való csatlakozáshoz használja az illesztőprogram (SSL/TLS) biztonsági szintjét. Példa: `SecurityLevel=preferredSecured`. Támogatott értékei a következők:<br/>- **csak nem biztonságos** (**OnlyUnSecured**): az illesztőprogram nem használ SSL-t.<br/>- **előnyben részesített nem védett (preferredUnSecured) (alapértelmezett)** : Ha a kiszolgáló választási lehetőséget biztosít, az illesztőprogram nem használ SSL-t. <br/>- **előnyben részesített védett (preferredSecured)** : Ha a kiszolgáló választási lehetőséget biztosít, az illesztőprogram SSL-t használ. <br/>- **csak biztonságos (onlySecured)** : az illesztőprogram nem csatlakozik, ha nem érhető el SSL-kapcsolat. | Nem |
| CaCertFile | A kiszolgáló által használt SSL-tanúsítvány teljes elérési útja. Például: `CaCertFile=<cert path>;`| Igen, ha SSL engedélyezve van |

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

**Példa: a Jelszó tárolása Azure Key Vault**

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

Ez a szakasz felsorolja, amely támogatja a Netezza adatkészlet tulajdonságai.

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját lásd: [adatkészletek](concepts-datasets-linked-services.md).

Az adatok Netezza való másolásához állítsa az adatkészlet **Type (típus** ) tulajdonságát **NetezzaTable**értékre. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | Az adatkészlet Type tulajdonságát a következőre kell beállítani: **NetezzaTable** | Igen |
| schema | A séma neve. |Nem (Ha a tevékenység forrása az "query" van megadva)  |
| table | A tábla neve. |Nem (Ha a tevékenység forrása az "query" van megadva)  |
| tableName | A sémával rendelkező tábla neve. Ez a tulajdonság visszamenőleges kompatibilitás esetén támogatott. Új számítási feladatokhoz használjon `schema` és `table`. | Nem (Ha a tevékenység forrása az "query" van megadva) |

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

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját lásd: [folyamatok](concepts-pipelines-activities.md).

### <a name="netezza-as-source"></a>Netezza forrásként

>[!TIP]
>Az adatok Netezza történő hatékony betöltéséhez az adatparticionálás használatával tudjon meg többet a [párhuzamos másolásról a Netezza](#parallel-copy-from-netezza) szakaszból.

Az adatok Netezza való másolásához állítsa a **forrás** típusát a másolás tevékenység **NetezzaSource**értékére. A másolási tevékenység **forrása** szakasz a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának **Type** tulajdonságát **NetezzaSource**értékre kell állítani. | Igen |
| lekérdezés | Az egyéni SQL-lekérdezés segítségével olvassa el az adatokat. Például: `"SELECT * FROM MyTable"` | Nem (Ha a "tableName" adatkészlet paraméter van megadva) |
| partitionOptions | Meghatározza az adatok Netezza való betöltéséhez használt adatparticionálási beállításokat. <br>Értékek engedélyezése: **none** (alapértelmezett), **DataSlice**és **DynamicRange**.<br>Ha engedélyezve van egy partíciós beállítás (azaz nem `None`), az Netezza-adatbázisból származó adatok párhuzamos betöltésének fokát a másolási tevékenység [`parallelCopies`](copy-activity-performance.md#parallel-copy) beállítása szabályozza. | Nem |
| partitionSettings | Határozza meg az adatparticionálási beállítások csoportját. <br>Akkor alkalmazza, ha a partíció beállítása nem `None`. | Nem |
| partitionColumnName | Adja meg a forrás oszlop **olyan egész típusú** nevét, amelyet a párhuzamos másolási tartomány particionálásakor fog használni. Ha nincs megadva, a rendszer automatikusan felismeri a tábla elsődleges kulcsát, és a partíció oszlopként használja. <br>Akkor alkalmazza, ha a partíció beállítása `DynamicRange`. Ha lekérdezést használ a forrásadatok beolvasásához, a Hook `?AdfRangePartitionColumnName` a WHERE záradékban. Lásd: példa [párhuzamos másolással a Netezza](#parallel-copy-from-netezza) szakaszból. | Nem |
| partitionUpperBound | Az adatmásolásra szolgáló partíciós oszlop maximális értéke. <br>Akkor alkalmazza, ha a partíció beállítása `DynamicRange`. Ha lekérdezést használ a forrásadatok beolvasásához, a Hook `?AdfRangePartitionUpbound` a WHERE záradékban. Példaként tekintse meg a [Parallel másolás a Netezza](#parallel-copy-from-netezza) szakaszát. | Nem |
| partitionLowerBound | Az adatmásolásra szolgáló partíciós oszlop minimális értéke. <br>Akkor alkalmazza, ha a partíció beállítása `DynamicRange`. Ha lekérdezést használ a forrásadatok beolvasásához, a Hook `?AdfRangePartitionLowbound` a WHERE záradékban. Példaként tekintse meg a [Parallel másolás a Netezza](#parallel-copy-from-netezza) szakaszát. | Nem |

**Példa**

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

A particionált másolás engedélyezésekor a Data Factory párhuzamos lekérdezéseket futtat a Netezza-forráson az adatpartíciók betöltéséhez. A párhuzamos fokot a másolási tevékenység [`parallelCopies`](copy-activity-performance.md#parallel-copy) beállítása vezérli. Ha például a `parallelCopies`t négyre állítja be, Data Factory egyidejűleg létrehoz és futtat négy lekérdezést a megadott partíciós beállítás és beállítások alapján, és mindegyik lekérdezés a Netezza-adatbázisból származó adatok egy részét kéri le.

Javasoljuk, hogy engedélyezze a párhuzamos másolást az adatok particionálásával, különösen akkor, ha nagy mennyiségű adatmennyiséget tölt be a Netezza-adatbázisból. Az alábbiakban a különböző forgatókönyvekhez javasolt konfigurációk szerepelnek. Az adatok file-alapú adattárba való másolása során a rendszer úgy helyezi át, hogy több fájlként írjon egy mappába (csak a mappa nevét adja meg), amely esetben a teljesítmény jobb, mint egyetlen fájl írásakor.

| Forgatókönyv                                                     | Javasolt beállítások                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Teljes terhelés a nagyméretű táblából.                                   | **Partíciós beállítás**: adatszelet. <br><br/>A végrehajtás során a Data Factory automatikusan particionálja az [Netezza-t a beépített adatszeletek](https://www.ibm.com/support/knowledgecenter/en/SSULQD_7.2.1/com.ibm.nz.adm.doc/c_sysadm_data_slices_parts_disks.html)alapján, és az Adatmásolást partíciók szerint másolja. |
| Nagy mennyiségű adattal tölthetők be egyéni lekérdezések használatával.                 | **Partíciós beállítás**: adatszelet.<br>**Lekérdezés**: `SELECT * FROM <TABLENAME> WHERE mod(datasliceid, ?AdfPartitionCount) = ?AdfDataSliceCondition AND <your_additional_where_clause>`.<br>A végrehajtás során a Data Factory lecseréli a `?AdfPartitionCount` (párhuzamos másolási számmal a másolási tevékenységnél), és `?AdfDataSliceCondition` az adatszelet partíciós logikával, és elküldi a Netezza. |
| Nagy mennyiségű adattal tölthetők be egy egyéni lekérdezéssel, amely egy egész számú oszlopból álló, egyenletesen elosztott értékkel rendelkezik a tartomány particionálásához. | **Partíciós beállítások**: dinamikus tartományú partíció.<br>**Lekérdezés**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**Partíciós oszlop**: az adatparticionáláshoz használt oszlop megadására szolgál. Az oszlop egész adattípussal is particionálható.<br>**Partíció felső határa** és a **partíció alsó határa**: adja meg, hogy a partíció oszlop alapján szeretne-e szűrést végezni, hogy csak az alsó és a felső tartomány között legyenek lekérdezve.<br><br>A végrehajtás során a Data Factory lecseréli a `?AdfRangePartitionColumnName`, a `?AdfRangePartitionUpbound`és az `?AdfRangePartitionLowbound` értéket az egyes partíciók tényleges oszlop-és értéktartomány-tartományával, és elküldi a Netezza. <br>Ha például az "ID" partíciós oszlop az 1 értékre van állítva, a felső határ pedig 80, a párhuzamos másolási beállítás értéke 4, Data Factory 4 partíció alapján kérdezi le az adatmennyiséget. Az azonosítóik [1, 20], [21, 40], [41, 60] és [61, 80] között vannak. |

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

## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [keresési tevékenységet](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Következő lépések

A másolási tevékenység által támogatott adattárak listáját a Azure Data Factoryban található forrásként és nyelőként tekintse meg a [támogatott adattárak és-formátumok](copy-activity-overview.md#supported-data-stores-and-formats)című témakörben.
