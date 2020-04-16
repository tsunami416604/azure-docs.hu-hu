---
title: Adatok másolása a Teradata Vantage szolgáltatásból az Azure Data Factory használatával
description: A Data Factory szolgáltatás Teradata-összekötője lehetővé teszi az adatok másolását a Teradata Vantage-ből a Data Factory által támogatott adattárolókba fogadóként.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: jingwang
ms.openlocfilehash: 4eed79210e3e39f82b892ac0681e161ebb59597e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418031"
---
# <a name="copy-data-from-teradata-vantage-by-using-azure-data-factory"></a>Adatok másolása a Teradata Vantage szolgáltatásból az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
>
> * [1-es verzió](v1/data-factory-onprem-teradata-connector.md)
> * [Aktuális verzió](connector-teradata.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk ismerteti, hogyan használhatja a másolási tevékenység et az Azure Data Factory adatok másolása teradata vantage. A [másolási tevékenység áttekintésére](copy-activity-overview.md)épül.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez a Teradata-összekötő a következő tevékenységek esetén támogatott:

- [Tevékenység másolása](copy-activity-overview.md) [támogatott forrás/fogadó mátrixcal](copy-activity-overview.md)
- [Keress tevékenységet](control-flow-lookup-activity.md)

A Teradata Vantage-ből adatokat másolhat bármely támogatott fogadó adattárba. A másolási tevékenység által forrásként/fogadóként támogatott adattárak listáját a [Támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblában található.

Ez a Teradata-összekötő különösen a következőket támogatja:

- Teradata **verzió 14.10, 15.0, 15.10, 16.0, 16.10 és 16.20**.
- Adatok másolása **alapfokú** vagy Windows-hitelesítéssel. **Windows**
- Párhuzamos másolás Teradata forrásból. A [részleteket a Teradata párhuzamos másolása](#parallel-copy-from-teradata) című szakaszban találja.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

Ha önkiszolgáló integrációs futásidejűt használ, vegye figyelembe, hogy a 3.18-as verziótól kezdve beépített Teradata-illesztőprogramot biztosít. Nem kell manuálisan telepítenie egyetlen illesztőprogramot sem. Az illesztőprogramnak szüksége van a "Visual C++ Redistributable 2012 Update 4" szolgáltatásra az önkiszolgáló integrációs üzemidejű gépen. Ha még nincs telepítve, töltse le [innen](https://www.microsoft.com/en-sg/download/details.aspx?id=30679).

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok a Teradata-összekötőre jellemző Data Factory-entitások definiálásához használt tulajdonságok részleteit ismertetik.

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai

A Teradata csatolt szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A típustulajdonságot **Teradata (Teradata**. | Igen |
| connectionString (kapcsolati karakterlánc) | A Teradata-példányhoz való csatlakozáshoz szükséges információkat adja meg. Lásd a következő mintákat.<br/>Az Azure Key Vaultban is elhelyezhet `password` egy jelszót, és kihúzhatja a konfigurációt a kapcsolati karakterláncból. További részleteket az [Azure Key Vault áruházi hitelesítő adataiban](store-credentials-in-key-vault.md) talál. | Igen |
| felhasználónév | Adja meg a Teradata-hoz való csatlakozáshoz kívánt felhasználónevet. A Windows-hitelesítés használatakor érvényes. | Nem |
| jelszó | Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. Azt is választhatja, hogy [az Azure Key Vaultban tárolt titkos fájlokra hivatkozik.](store-credentials-in-key-vault.md) <br>Akkor érvényes, ha Windows-hitelesítést használ, vagy jelszóra hivatkozik a Key Vaultban az alapfokú hitelesítéshez. | Nem |
| connectVia | Az adattárhoz való csatlakozáshoz használandó [integrációs futásidő.](concepts-integration-runtime.md) További információ az [Előfeltételek](#prerequisites) szakaszból. Ha nincs megadva, az alapértelmezett Azure-integrációs runtime-ot használja. |Nem |

További kapcsolati tulajdonságok, amelyeket a kapcsolati karakterláncban állíthat be esetenként:

| Tulajdonság | Leírás | Alapértelmezett érték |
|:--- |:--- |:--- |
| Karakterkészlet | A munkamenethez használandó karakterkészlet. Pl. `CharacterSet=UTF16`.<br><br/>Ez az érték lehet felhasználó által definiált karakterkészlet, vagy az alábbi előre definiált karakterkészletek egyike: <br/>- ASCII<br/>- UTF8<br/>- UTF16<br/>- LATIN1252_0A<br/>- LATIN9_0A<br/>- LATIN1_0A<br/>- Shift-JIS (Windows, DOS kompatibilis, KANJISJIS_0S)<br/>- EUC (Unix-kompatibilis, KANJIEC_0U)<br/>- IBM Mainframe (KANJIEBCDIC5035_0I)<br/>- KANJI932_1S0<br/>- BIG5 (TCHBIG5_1R0)<br/>- GB (SCHGB2312_1T0)<br/>- SCHINESE936_6R0.<br/>- TCHINESE950_8R0<br/>- NetworkKoreai (HANGULKSC5601_2R4)<br/>- HANGUL949_7R0.<br/>- ARABIC1256_6A0<br/>- CYRILLIC1251_2A0<br/>- HEBREW1255_5A0<br/>- LATIN1250_1A0<br/>- LATIN1254_7A0<br/>- LATIN1258_8A0<br/>- THAI874_4A0 | Az alapértelmezett `ASCII`érték a . |
| MaxRespMéret |Az SQL-kérelmek válaszpufferének maximális mérete kilobájtban (KB) megválaszolva. Pl. `MaxRespSize=‭10485760‬`.<br/><br/>A Teradata Database 16.00-es vagy újabb verziója esetén a maximális érték 7361536. A korábbi verziókat használó kapcsolatok esetében a maximális érték 1048576. | Az alapértelmezett `65536`érték a . |

**Példa alapszintű hitelesítésre**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>;Uid=<username>;Pwd=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Példa Windows-hitelesítésre**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>",
            "username": "<username>",
            "password": "<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

> [!NOTE]
>
> A következő hasznos tartalom továbbra is támogatott. A jövőben azonban érdemes használni az újat.

**Előző hasznos teher:**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "server": "<server>",
            "authenticationType": "<Basic/Windows>",
            "username": "<username>",
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

Ez a szakasz a Teradata adatkészlet által támogatott tulajdonságok listáját tartalmazza. Az adatkészletek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját az Adatkészletek című [témakörben található.](concepts-datasets-linked-services.md)

A Teradata adatainak másolásához a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | Az adatkészlet típustulajdonságát a ikonra `TeradataTable`kell állítani. | Igen |
| adatbázis | A Teradata-példány neve. | Nem (ha a "lekérdezés" a tevékenységforrásban meg van adva) |
| tábla | A Teradata-példányban lévő tábla neve. | Nem (ha a "lekérdezés" a tevékenységforrásban meg van adva) |

**Példa:**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "TeradataTable",
        "typeProperties": {},
        "schema": [],        
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

> [!NOTE]
>
> `RelationalTable`a típusadatkészlet továbbra is támogatott. Azonban azt javasoljuk, hogy használja az új adatkészletet.

**Előző hasznos teher:**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Ez a szakasz a Teradata forrás által támogatott tulajdonságok listáját tartalmazza. A tevékenységek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját a [Folyamatok című témakörben található.](concepts-pipelines-activities.md) 

### <a name="teradata-as-source"></a>Teradata forrásként

>[!TIP]
>Ha adatparticionálással hatékonyan szeretné betölteni az adatokat a Teradata-ból, további információ a [Teradata szakasz párhuzamos másolása című szakaszból.](#parallel-copy-from-teradata)

A Teradata adatainak másolásához a másolási tevékenység **forrásszakaszában** a következő tulajdonságok at támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának típustulajdonságát a ikonra `TeradataSource`kell állítani. | Igen |
| lekérdezés | Az adatok olvasásához használja az egyéni SQL-lekérdezést. Például: `"SELECT * FROM MyTable"`.<br>Ha engedélyezi a particionált terhelést, a lekérdezésben a megfelelő beépített partícióparamétereket kell csatlakoztatnia. Példák: A Párhuzamos másolat a [Teradata szakaszban.](#parallel-copy-from-teradata) | Nem (ha az adatkészletben lévő tábla meg van adva) |
| partitionOptions | Megadja a Teradata adatainak betöltéséhez használt adatparticionálási beállításokat. <br>Az engedélyezési értékek a következők: **Nincs** (alapértelmezett), **Kivonatoló** és **DynamicRange**.<br>Ha egy partícióbeállítás engedélyezve van `None`(azaz nem), a Teradata-adatok egyidejű betöltéséhez [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) szükséges párhuzamosság mértékét a másolási tevékenység beállítása szabályozza. | Nem |
| partitionSettings (partícióbeállításai) | Adja meg az adatparticionálás beállításainak csoportját. <br>Alkalmazza, ha a `None`partícióbeállítás nem . | Nem |
| partitionColumnName | Adja meg annak a forrásoszlopnak a nevét, amelyet a tartománypartíció vagy a kivonatoló partíció a párhuzamos másoláshoz használ. Ha nincs megadva, a tábla elsődleges indexe automatikusan észlelhető, és partícióoszlopként lesz használva. <br>Alkalmazza, ha a `Hash` `DynamicRange`partíciós beállítás vagy vagy a . Ha lekérdezéssel olvassa be a forrásadatokat, akassza be `?AdfHashPartitionCondition` vagy `?AdfRangePartitionColumnName` a WHERE záradékot. Példa a [Teradata-ból származó párhuzamos másolás szakaszban.](#parallel-copy-from-teradata) | Nem |
| partitionUpperBound között | Az adatok másolásához a partícióoszlop maximális értéke. <br>Alkalmazza, ha `DynamicRange`a partíciós beállítás . Ha lekérdezéssel olvassa be a `?AdfRangePartitionUpbound` forrásadatokat, a HOOK a WHERE záradékban. Például tekintse meg a [Párhuzamos másolat teradata szakaszban.](#parallel-copy-from-teradata) | Nem |
| partitionLowerBound | Az adatok másolásához szükséges partícióoszlop minimális értéke. <br>Alkalmazza, ha a `DynamicRange`partíciós beállítás . Ha lekérdezéssel olvassa be a forrásadatokat, a HOOK a `?AdfRangePartitionLowbound` WHERE záradékban. Például tekintse meg a [Párhuzamos másolat teradata szakaszban.](#parallel-copy-from-teradata) | Nem |

> [!NOTE]
>
> `RelationalSource`a típusmásolási forrás továbbra is támogatott, de nem támogatja a Teradata új beépített párhuzamos terhelését (partícióbeállítások). Azonban azt javasoljuk, hogy használja az új adatkészletet.

**Példa: adatok másolása egyszerű lekérdezéssel partíció nélkül**

```json
"activities":[
    {
        "name": "CopyFromTeradata",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Teradata input dataset name>",
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
                "type": "TeradataSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="parallel-copy-from-teradata"></a>Párhuzamos másolás a Teradata-ból

A Data Factory Teradata-összekötő beépített adatparticionálást biztosít a Teradata-ból párhuzamosan történő adatok másolásához. Az adatparticionálási beállításokat a másolási tevékenység **Forrás** tábláján találja.

![A partícióbeállításainak képernyőképe](./media/connector-teradata/connector-teradata-partition-options.png)

Ha engedélyezi a particionált másolást, a Data Factory párhuzamos lekérdezéseket futtat a Teradata-forráson az adatok partíciók általi betöltéséhez. A párhuzamos mértéket a [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) másolási tevékenység beállítása szabályozza. Ha például négyre állítja, `parallelCopies` a Data Factory egyidejűleg négy lekérdezést hoz létre és futtat a megadott partícióbeállítás és -beállítások alapján, és minden lekérdezés lekéri az adatok egy részét a Teradata-ból.

Javasoljuk, hogy engedélyezze a párhuzamos másolást az adatparticionálással, különösen akkor, ha nagy mennyiségű adatot tölt be a Teradata-ból. Az alábbiakban a különböző forgatókönyvekhez javasolt konfigurációkat javasoljuk. Amikor adatokat másol fájlalapú adattárba, a rendszer azt a parancsot adja meg, hogy egy mappába több fájlként írjon (csak adja meg a mappa nevét), ebben az esetben a teljesítmény jobb, mint egyetlen fájlba írni.

| Forgatókönyv                                                     | Javasolt beállítások                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Teljes terhelés a nagy asztalról.                                   | **Partíció opció**: Hash. <br><br/>A végrehajtás során a Data Factory automatikusan észleli az elsődleges index oszlop, alkalmazza a kivonatot ellene, és másolja az adatokat partíciók. |
| Nagy mennyiségű adatot tölthet be egyéni lekérdezéssel.                 | **Partíció opció**: Hash.<br>**Lekérdezés** `SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>`: .<br>**Partícióoszlop:** Adja meg a kivonatpartíció alkalmazásához használt oszlopot. Ha nincs megadva, a Data Factory automatikusan észleli a Teradata adatkészletben megadott tábla PK-oszlopát.<br><br>A végrehajtás során a `?AdfHashPartitionCondition` Data Factory lecseréli a kivonatpartíció logikáját, és elküldi a Teradata-nak. |
| Nagy mennyiségű adatot tölthet be egyéni lekérdezéssel, amelynek egész oszlopa egyenletesen elosztott értékkel rendelkezik a tartományparticionáláshoz. | **Partíció beállításai**: Dinamikus tartományú partíció.<br>**Lekérdezés** `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`: .<br>**Partícióoszlop:** Adja meg az adatok particionálásához használt oszlopot. Az egész adattípussal rendelkező oszlopra particionálhat.<br>**Partíció felső és** **alsó határa**: Adja meg, hogy a partícióoszlophoz szűrve csak az alsó és a felső tartomány között szeretne adatokat beolvasni.<br><br>A végrehajtás során a `?AdfRangePartitionColumnName` `?AdfRangePartitionUpbound`Data `?AdfRangePartitionLowbound` Factory lecseréli a , és az egyes partíciók tényleges oszlopnevét és értéktartományait, és elküldi a Teradata-nak. <br>Ha például az "ID" partícióoszlop az alsó határ 1, a felső határ pedig 80, a párhuzamos másolat pedig 4, a Data Factory 4 partícióval olvassa be az adatokat. Azonosítóik [1,20], [21, 40], [41, 60] és [61, 80] között vannak. |

**Példa: kivonatoló partícióval rendelkező lekérdezés**

```json
"source": {
    "type": "TeradataSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "Hash",
    "partitionSettings": {
        "partitionColumnName": "<hash_partition_column_name>"
    }
}
```

**Példa: lekérdezés dinamikus tartománypartícióval**

```json
"source": {
    "type": "TeradataSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<dynamic_range_partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="data-type-mapping-for-teradata"></a>A Teradata adattípus-leképezése

Amikor adatokat másol a Teradata-ból, a következő leképezések lesznek érvényesek. Ha többet szeretne tudni arról, hogy a másolási tevékenység hogyan rendeli le a forrássémát és az adattípust a fogadóhoz, olvassa el a [Séma- és adattípus-hozzárendelések című témakört.](copy-activity-schema-and-type-mapping.md)

| Teradata-adattípus | Adatgyár köztes adattípusa |
|:--- |:--- |
| BigInt között |Int64 |
| Blob |Bájt[] |
| Bájt |Bájt[] |
| ByteInt |Int16 |
| Char |Sztring |
| Clob között |Sztring |
| Dátum |DateTime |
| Decimal |Decimal |
| Double |Double |
| Grafikus |Nem támogatott. Explicit szereplős küldés alkalmazása a forráslekérdezésben. |
| Egész szám |Int32 |
| Intervallum napja |Nem támogatott. Explicit szereplős küldés alkalmazása a forráslekérdezésben. |
| Időköz naptól óráig |Nem támogatott. Explicit szereplős küldés alkalmazása a forráslekérdezésben. |
| Időköz naptól percig |Nem támogatott. Explicit szereplős küldés alkalmazása a forráslekérdezésben. |
| Időköz naptól másodpercig |Nem támogatott. Explicit szereplős küldés alkalmazása a forráslekérdezésben. |
| Időköz óra |Nem támogatott. Explicit szereplős küldés alkalmazása a forráslekérdezésben. |
| Időköz percről percre |Nem támogatott. Explicit szereplős küldés alkalmazása a forráslekérdezésben. |
| Időköz óra és másodperc között |Nem támogatott. Explicit szereplős küldés alkalmazása a forráslekérdezésben. |
| Időköz perc |Nem támogatott. Explicit szereplős küldés alkalmazása a forráslekérdezésben. |
| Időköz percről másodpercre |Nem támogatott. Explicit szereplős küldés alkalmazása a forráslekérdezésben. |
| Intervallum hónap |Nem támogatott. Explicit szereplős küldés alkalmazása a forráslekérdezésben. |
| Második időköz |Nem támogatott. Explicit szereplős küldés alkalmazása a forráslekérdezésben. |
| Intervallum éve |Nem támogatott. Explicit szereplős küldés alkalmazása a forráslekérdezésben. |
| Intervallum évről hónapra |Nem támogatott. Explicit szereplős küldés alkalmazása a forráslekérdezésben. |
| Szám |Double |
| Időszak (dátum) |Nem támogatott. Explicit szereplős küldés alkalmazása a forráslekérdezésben. |
| Időszak (idő) |Nem támogatott. Explicit szereplős küldés alkalmazása a forráslekérdezésben. |
| Időszak (időidő időzónával) |Nem támogatott. Explicit szereplős küldés alkalmazása a forráslekérdezésben. |
| Időszak (időbélyeg) |Nem támogatott. Explicit szereplős küldés alkalmazása a forráslekérdezésben. |
| Időszak (időbélyeg időzónával) |Nem támogatott. Explicit szereplős küldés alkalmazása a forráslekérdezésben. |
| SmallInt között |Int16 |
| Time |időtartam |
| Idő az időzónával |időtartam |
| Időbélyeg |DateTime |
| Időbélyeg időzónával |DateTime |
| VarByte között |Bájt[] |
| Varchar |Sztring |
| Vargrafika |Nem támogatott. Explicit szereplős küldés alkalmazása a forráslekérdezésben. |
| Xml |Nem támogatott. Explicit szereplős küldés alkalmazása a forráslekérdezésben. |


## <a name="lookup-activity-properties"></a>A keresgaszíntevékenység tulajdonságai

A tulajdonságokrészleteinek megismeréséhez ellenőrizze a [Kereskövetési tevékenységet.](control-flow-lookup-activity.md)


## <a name="next-steps"></a>További lépések
A Data Factory másolási tevékenysége által forrásként és fogadóként támogatott adattárak listáját a Támogatott adattárak című témakörben [tetszését.](copy-activity-overview.md#supported-data-stores-and-formats)
