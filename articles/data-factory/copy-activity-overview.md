---
title: Másolási tevékenység az Azure Data Factoryban |} A Microsoft Docs
description: További információ az Azure Data Factoryban a másolási tevékenység használható egy támogatott forrásadattárból adatokat másol egy támogatott fogadó adattárba.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: jingwang
ms.openlocfilehash: df1fbcb09310985b7ca9d9fd2e7a987fc6e2b2dc
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49457083"
---
# <a name="copy-activity-in-azure-data-factory"></a>Az Azure Data Factory másolási tevékenysége

## <a name="overview"></a>Áttekintés

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verzió](v1/data-factory-data-movement-activities.md)
> * [Aktuális verzió](copy-activity-overview.md)

Az Azure Data Factory a másolási tevékenység használatával között adatokat tárolja a helyszíni és felhőbeli adatok másolása. Után az adatok másolását követően képes lehet további alakította át és elemzése. A másolási tevékenység használatával Adatátalakítási és elemzési eredmények az üzleti intelligenciára épülő (BI) és az alkalmazás elküldött közzététele.

![Szerepkör-másolási tevékenység](media/copy-activity-overview/copy-activity.png)

A másolási tevékenység végrehajtása a- [Integration Runtime](concepts-integration-runtime.md). A különböző adatok másolásának esetéhez hasonlóan az integrációs különböző íz is javítható:

* Adatok közötti másolást tárolja, hogy mindkettő nyilvánosan elérhető, a másolási tevékenység által is infógrafikát **Azure integrációs modul**, biztonságos, megbízható, méretezhető, azaz és [globálisan elérhető](concepts-integration-runtime.md#integration-runtime-location).
* Ha a helyszíni adatok másolása Azure blobból vagy az adattárakban található, vagy egy hálózati hozzáférés-vezérléssel (például az Azure virtuális hálózat), állítsa be kell egy **saját üzemeltetésű integrált futásidejű** megjelenő új adatok másolását.

Az Integration Runtime kell lennie minden forrás- és fogadó adattár társítva. További részleteket az másolási tevékenység [meghatározza a használandó integrációs modul](concepts-integration-runtime.md#determining-which-ir-to-use).

A másolási tevékenység adatokat másol a forrásadattárból egy fogadó, a következő szakaszokra végighalad. A másolási tevékenység biztosító szolgáltatás:

1. Beolvassa az adatokat a forrásadattárból.
2. Végrehajtja a szerializálás/deszerializálás, tömörítése és kibontása, oszlopleképezés, stb. Ezeket a műveleteket a konfiguráció a bemeneti adatkészlet, a kimeneti adatkészlet és a másolási tevékenység alapján hajtja végre.
3. A fogadó és a cél adattárolóba írja az adatokat.

![Másolási tevékenység áttekintése](media/copy-activity-overview/copy-activity-overview.png)

## <a name="supported-data-stores-and-formats"></a>Támogatott adattárak és formátumok

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

### <a name="supported-file-formats"></a>Támogatott fájlformátumok

A másolási tevékenység használható **másolja a fájlokat,-van** két fájlalapú adattárak között, amelyben az esetben az adatok másolódik hatékonyan bármely szerializálás/deszerializálás nélkül.

A másolási tevékenység is támogatja a olvasását és írását megadott formátumú: **szöveg, JSON, Avro, ORC és a parquet eszközökben**, és a tömörítési kodek **GZip, Deflate, BZip2 és ZipDeflate** támogatottak. Lásd: [támogatott fájl- és tömörítési formátumok](supported-file-formats-and-compression-codecs.md) adatokkal.

Ha például a következő másolási tevékenységek teheti:

* Adatok másolása helyszíni SQL Server és írni az Azure Data Lake Store ORC formátumban.
* Fájlok másolása a helyszíni fájlrendszer (CSV) szöveges formátumban, és Avro formátumban az Azure-Blobba írni.
* Tömörített fájlok másolása a helyszíni fájlrendszerben és az Azure Data Lake Store föld majd kibontása a.
* A GZip tömörített szöveg (CSV) formátumú adatok másolása az Azure-Blobból, és az Azure SQL Database írási.

## <a name="supported-regions"></a>Támogatott régiók

A másolási tevékenység biztosító szolgáltatás érhető globálisan régiókban és földrajzi területeken szereplő [Azure integrációs modul helye](concepts-integration-runtime.md#integration-runtime-location). A globálisan elérhető topológia általában Ezzel elkerülheti a régiók közötti útválasztók ugrásainak hatékony adatáthelyezés biztosítja. Lásd: [-szolgáltatások régió szerint](https://azure.microsoft.com/regions/#services) Data Factory és a egy régióban Adatáthelyezés rendelkezésre állását.

## <a name="configuration"></a>Konfiguráció

Az Azure Data Factory másolási tevékenység használatához kell tennie:

1. **Hozzon létre a társított szolgáltatást a céladattára pedig a fogadó adattárba.** Tekintse át az összekötő cikk "Társított szolgáltatások tulajdonságai" részt konfigurálása és a támogatott tulajdonságok. A támogatott összekötő listát annak [támogatott adattárak és formátumok](#supported-data-stores-and-formats) szakaszban.
2. **A forrás és fogadó-adatkészletek létrehozása.** Tekintse meg a forrás és fogadó-összekötő cikkek "Adatkészlet tulajdonságai" szakasz konfigurálása és a támogatott tulajdonságok.
3. **Másolási tevékenységgel rendelkező folyamat létrehozása.** Ez a szakasz mutatja be.  

### <a name="syntax"></a>Szintaxis

Az alábbi sablont, a másolási tevékenység támogatott tulajdonságok teljesnek tartalmazza. Adja meg, amelyekre a saját forgatókönyvéhez igazítva.

```json
"activities":[
    {
        "name": "CopyActivityTemplate",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<source dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<sink dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>",
                <properties>
            },
            "sink": {
                "type": "<sink type>"
                <properties>
            },
            "translator":
            {
                "type": "TabularTranslator",
                "columnMappings": "<column mapping>"
            },
            "dataIntegrationUnits": <number>,
            "parallelCopies": <number>,
            "enableStaging": true/false,
            "stagingSettings": {
                <properties>
            },
            "enableSkipIncompatibleRow": true/false,
            "redirectIncompatibleRowSettings": {
                <properties>
            }
        }
    }
]
```

### <a name="syntax-details"></a>Szintaxis részletei

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység type tulajdonsága értékre kell állítani: **másolása** | Igen |
| bemenetek | Adja meg az adatkészlet mely mutat a forrásadatok létrehozott. A másolási tevékenység csak egy egyetlen bemeneti támogatja. | Igen |
| kimenetek | Adja meg az adatkészlet mely mutat a fogadó adatait létrehozott. A másolási tevékenység csak egyetlen kimeneti támogatja. | Igen |
| typeProperties | Egy csoport tulajdonságainak konfigurálása a másolási tevékenység. | Igen |
| source | Adja meg a másolat forrástípusa és a vonatkozó tulajdonságok adatok lekérésével.<br/><br/>További részletek az összekötő a cikkben szereplő "Másolási tevékenység tulajdonságai" szakaszban [támogatott adattárak és formátumok](#supported-data-stores-and-formats). | Igen |
| fogadó | Adja meg a másolat fogadótípusa és a vonatkozó tulajdonságok hogyan szeretne adatokat írni.<br/><br/>További részletek az összekötő a cikkben szereplő "Másolási tevékenység tulajdonságai" szakaszban [támogatott adattárak és formátumok](#supported-data-stores-and-formats). | Igen |
| Translator | Adja meg a forrás és fogadó-, a kifejezett oszlopleképezés. Érvényes, ha az alapértelmezett másolási viselkedés nem teljesíthető az igényeknek.<br/><br/>A részletek [séma és adatok Típusleképezés](copy-activity-schema-and-type-mapping.md). | Nem |
| dataIntegrationUnits | Adja meg annak a powerfulness [Azure integrációs modul](concepts-integration-runtime.md) megjelenő új adatok másolását. Korábbi nevén a felhőbeli adatok adatáthelyezési egységek (DMU). <br/><br/>A részletek [integrációs adategységek](copy-activity-performance.md#data-integration-units). | Nem |
| parallelCopies | Adja meg, amelyeket szeretne használni a forrás- és adatok írása az adatok olvasásakor a fogadó a másolási tevékenység párhuzamosságát.<br/><br/>A részletek [másolási párhuzamos](copy-activity-performance.md#parallel-copy). | Nem |
| enableStaging<br/>stagingSettings | Válassza ki az átmeneti adatok helyett az adatok közvetlenül másolása a forrás és fogadó-, a regisztrációhoz egy blob storage-ban előkészítéséhez.<br/><br/>Ismerje meg, a hasznos forgatókönyveket és a konfigurációs részleteket [szakaszos Másolás](copy-activity-performance.md#staged-copy). | Nem |
| enableSkipIncompatibleRow<br/>redirectIncompatibleRowSettings| Válassza ki, hogyan legyen kezelve az inkompatibilis sorok, ha másol adatokat a forrás és fogadó-, a.<br/><br/>A részletek [hibatűrés](copy-activity-fault-tolerance.md). | Nem |

## <a name="monitoring"></a>Figyelés

A másolási tevékenység futtatása az Azure Data Factory "Létrehozás és Monitorozás" felhasználói felületen vagy programozás útján követheti nyomon. Ezután összehasonlíthatja a teljesítmény és a konfiguráció a másolási tevékenység, megoldásait [teljesítményfigyelési](copy-activity-performance.md#performance-reference) házon belüli tesztelési szakaszából.

### <a name="monitor-visually"></a>Vizuális megfigyelés

A másolási tevékenység futtatási vizuálisan monitorozásához válassza a data factory -> **létrehozás és Monitorozás** -> **figyelés lapon**, folyamat listáját a "Tevékenységfuttatásokmegtekintése"hivatkozástfoglátni, **Műveletek** oszlop. 

![Folyamatfuttatások monitorozása](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)

Kattintson ide a folyamat futásának tevékenységek listájának megjelenítéséhez. Az a **műveletek** oszlop, rendelkezik a másolási tevékenység bemeneti, kimeneti, hibák (Ha nem sikerül a másolási tevékenység futtatási) és részleteket mutató hivatkozásokat.

![Tevékenységfuttatások monitorozása](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

Kattintson a "**részletek**" hivatkozásra **műveletek** látható a másolási tevékenység végrehajtásának részletei és teljesítményt nyújt. Megjeleníti a adatait az adatok többek között kötet/sor/fájlokat másolni a forrás, fogadó-, az átviteli sebesség, halad végig a megfelelő időtartamot, és a használt konfigurációk a másolási forgatókönyv lépéseit.

**Példa: másolja az Amazon S3-ból az Azure Data Lake Store**
![figyelő tevékenységfuttatás részletei](./media/copy-activity-overview/monitor-activity-run-details-adls.png)

**Példa: példány az Azure SQL Database-ből az Azure SQL Data Warehouse használatával előkészített másolási**
![figyelő tevékenységfuttatás részletei](./media/copy-activity-overview/monitor-activity-run-details-sql-dw.png)

### <a name="monitor-programmatically"></a>Programozott figyelése

Másolási tevékenység végrehajtásának részletei és teljesítményjellemzők is megjelennek a másolási tevékenység futtatásának eredménye -> kimeneti szakaszban. Az alábbi, a teljesség; csak a Másolás forgatókönyvéhez alkalmazható azokat jelennek meg. Ismerje meg, hogyan figyelheti a tevékenységfuttatási [rövid útmutató a figyelés szakaszban](quickstart-create-data-factory-dot-net.md#monitor-a-pipeline-run).

| Tulajdonság neve  | Leírás | Unit (Egység) |
|:--- |:--- |:--- |
| DataRead | Olvassa el a forrásból származó adatok mérete | Int64 típusú érték a **bájtok** |
| DataWritten | A fogadó írt adatok mérete | Int64 típusú érték a **bájtok** |
| filesRead | A fájlok másolását, amikor az adatok másolása a file storage száma. | Int64 típusú érték (egység) |
| filesWritten | A fájlok másolását, amikor az adatok másolása a file Storage száma. | Int64 típusú érték (egység) |
| rowsCopied | (Ez nem vonatkozik a bináris másolat) másolását sorok száma. | Int64 típusú érték (egység) |
| rowsSkipped | Kihagyta a inkompatibilis sorok száma. A szolgáltatás által beállított "enableSkipIncompatibleRow" true bekapcsolhatja. | Int64 típusú érték (egység) |
| Átviteli sebesség | Adatok továbbításának arány | Lebegőpontos szám a **KB/s** |
| copyDuration | A Másolás időtartama | Másodpercek alatt Int32 érték |
| sqlDwPolyBase | A PolyBase használata, amikor az adatok másolása az SQL Data Warehouse-bA. | Logikai |
| redshiftUnload | Ha az adatok másolása Redshift eltávolítása használatos. | Logikai |
| hdfsDistcp | A DistCp használata, ha másol adatokat a HDFS-ből. | Logikai |
| effectiveIntegrationRuntime | Show, amelyet integrációs Runtime(s) biztosítson hatékony eszközöket a tevékenység fut, az formátumban `<IR name> (<region if it's Azure IR>)`. | Szöveg (karakterlánc) |
| usedDataIntegrationUnits | A hatékony integrációs adategységek másolása során. | Int32 érték |
| usedParallelCopies | A hatékony parallelCopies másolása során. | Int32 érték|
| redirectRowPath | A blob storage "redirectIncompatibleRowSettings" területen konfigurálja a rendszer kihagyta inkompatibilis sorok a napló elérési útja. Lásd az alábbi példában. | Szöveg (karakterlánc) |
| executionDetails | További részleteket a szakasz a másolási tevékenység halad végig, és a megfelelő lépéseket, időtartam, használt konfigurációk stb. Nem javasoljuk ebben a szakaszban elemezni, előfordulhat, hogy módosítása esetén. | Tömb |

```json
"output": {
    "dataRead": 107280845500,
    "dataWritten": 107280845500,
    "filesRead": 10,
    "filesWritten": 10,
    "copyDuration": 224,
    "throughput": 467707.344,
    "errors": [],
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US 2)",
    "usedDataIntegrationUnits": 32,
    "usedParallelCopies": 8,
    "executionDetails": [
        {
            "source": {
                "type": "AmazonS3"
            },
            "sink": {
                "type": "AzureDataLakeStore"
            },
            "status": "Succeeded",
            "start": "2018-01-17T15:13:00.3515165Z",
            "duration": 221,
            "usedDataIntegrationUnits": 32,
            "usedParallelCopies": 8,
            "detailedDurations": {
                "queuingDuration": 2,
                "transferDuration": 219
            }
        }
    ]
}
```

## <a name="schema-and-data-type-mapping"></a>Séma- és adattípus-leképezés

Tekintse meg a [séma és adatok Típusleképezés](copy-activity-schema-and-type-mapping.md), amely azt ismerteti, hogyan képezi le a másolási tevékenység fogadó a forrásadatokat.

## <a name="fault-tolerance"></a>Hibatűrés

Alapértelmezés szerint a másolási tevékenység leállítja az adatok másolását, és hibát ad vissza, ha inkompatibilis adatokat a forrás és fogadó között. Explicit módon konfigurálhatja, hagyja ki, és jelentkezzen az inkompatibilis sorok, és csak a sikeres volt-e a másoláshoz ezeket kompatibilis adatokat másolni. Tekintse meg a [másolási tevékenység a hibatűrés](copy-activity-fault-tolerance.md) a további részleteket.

## <a name="performance-and-tuning"></a>Teljesítmény és finomhangolás

Tekintse meg a [másolási tevékenységek teljesítményéről és finomhangolásáról szóló útmutató](copy-activity-performance.md), amely ismerteti az adatáthelyezés (másolási tevékenység) az Azure Data Factoryban teljesítményét befolyásoló tényezők. Felsorolja a megfigyelt teljesítmény belső tesztelése során és a különböző módjait a másolási tevékenység teljesítményének ismerteti.

## <a name="incremental-copy"></a>A növekményes másolási 
A Data Factory eseteket támogatja a növekményes másolása különbözeti adatokat egy forrásadattárból egy céladattárba. Lásd: [oktatóanyag: adatok növekményes másolása az](tutorial-incremental-copy-overview.md). 

## <a name="read-and-write-partitioned-data"></a>Olvasási és írási particionált adatok
Azure Data Factory 1-es verzió támogatott SliceStart/SliceEnd/WindowStart WindowEnd rendszerváltozók használatával particionált adatok írása és olvasása. A jelenlegi verzióban a paraméter értékeként egy folyamat paraméter és a trigger kezdő időpontja és ütemezett időpontja használatával érheti el ezt a viselkedést. További információkért lásd: [olvasására vagy írására hogyan particionált adatok](how-to-read-write-partitioned-data.md).

## <a name="next-steps"></a>További lépések
Tekintse meg a következő útmutatóink, oktatóanyagaink és példáink segítségével:

- [Adatok másolása egyik helyről egy másik helyre az ugyanazon az Azure Blob Storage](quickstart-create-data-factory-dot-net.md)
- [Adatok másolása az Azure Blob Storage-ból az Azure SQL Database](tutorial-copy-data-dot-net.md)
- [Adatok másolása helyszíni SQL Serverről az Azure-bA](tutorial-hybrid-copy-powershell.md)
