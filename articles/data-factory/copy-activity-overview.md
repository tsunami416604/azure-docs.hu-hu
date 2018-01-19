---
title: "Az Azure Data Factory a másolási tevékenység |} Microsoft Docs"
description: "Ismerje meg a másolási tevékenység során az Azure Data Factory használható adatok másolása egy támogatott forráshierarchiából adattárból támogatott fogadó adattárat."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2018
ms.author: jingwang
ms.openlocfilehash: f26f36f241edba2e1fcd1156587b82b79d559e2d
ms.sourcegitcommit: 828cd4b47fbd7d7d620fbb93a592559256f9d234
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/18/2018
---
# <a name="copy-activity-in-azure-data-factory"></a>Az Azure Data Factory a másolási tevékenység

## <a name="overview"></a>Áttekintés

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1. verzió – Általánosan elérhető](v1/data-factory-data-movement-activities.md)
> * [2. verzió – Előzetes verzió](copy-activity-overview.md)

Az Azure Data Factoryben a másolási tevékenység követve másolja át az adatok között adatokat tárolja a helyszínen és a felhőben. Miután a rendszer átmásolja az adatokat, azt kell tovább át legyenek-e és elemzése. Használhatja a másolási tevékenység közzététele átalakítását és az elemzés eredményeinek az üzleti intelligenciával és alkalmazás-fogyasztás.

![Másolási tevékenység szerepe](media/copy-activity-overview/copy-activity.png)

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [másolási tevékenység során a V1](v1/data-factory-data-movement-activities.md).

Másolási tevékenység végrehajtása egy [integrációs futásidejű](concepts-integration-runtime.md). A különböző adatok másolásának esetéhez integrációs futtatókörnyezet különböző kiadása is javítható ki:

* Adatok között az adatok másolásának tárolja, hogy mindkettő nyilvánosan elérhető, amikor másolási tevékenység által kell felhatalmazott **Azure integrációs futásidejű**, vagyis a biztonságos, megbízható, méretezhető, és [világszerte elérhető](concepts-integration-runtime.md#integration-runtime-location).
* Ha az adatok másolásának/adattárolókhoz a helyszínen található, vagy a hozzáférés-vezérléssel (például Azure Virtual Network) hálózati, akkor be kell állítania egy **önállóan üzemel integrált futásidejű** építve az adatok másolását.

Integrációs futásidejű kell lennie minden forrás és a fogadó adattár társítva. További tudnivalók arról, hogyan másolási tevékenység [határozza meg, melyik IR használandó](concepts-integration-runtime.md#determining-which-ir-to-use).

Másolási tevékenység során a következő szakaszokra forrásból származó adatok másolása a fogadó végighalad. A szolgáltatás, amely a másolási tevékenység megoldásaira épül:

1. Olvassa be az adatokat a forrás-tárolóban.
2. Elvégzi a szerializálással/deszerializálással, tömörítés/kibontás, oszlopleképezés, stb. Ezek a konfigurációk a bemeneti adatkészletet, a kimeneti adatkészlet és a másolási tevékenység alapuló műveleteket végez el.
3. A fogadó vagy a cél-tárolót írja az adatokat.

![Másolási tevékenység áttekintése](media/copy-activity-overview/copy-activity-overview.png)

## <a name="supported-data-stores-and-formats"></a>Támogatott adatokat tárolja, és formázza

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

### <a name="supported-file-formats"></a>Támogatott fájlformátumok

A másolási tevékenység használhatja **másolja a fájlokat-van** között két fájl alapú adattároló, amely esetben az adatokat a rendszer átmásolja hatékonyan szerializálással/deszerializálással nélkül.

Másolási tevékenység során is támogatja a olvasását és írását megadott formátumú fájlok: **szöveg, JSON, Avro, ORC és Parquet**, és a tömörítési kodek **GZip, Deflate, BZip2 és ZipDeflate** támogatottak. Lásd: [fájl- és tömörítési formátum támogatott](supported-file-formats-and-compression-codecs.md) adatokkal.

Például a következő másolási tevékenység teheti meg:

* Másolja az adatokat a helyszíni SQL Server, és az Azure Data Lake Store írási ORC formátumban.
* Szöveges (CSV) formátumú fájlok másolását a helyszíni fájlrendszer, és az Avro formátum az Azure Blob írni.
* Tömörített fájlok másolását a helyszíni fájlrendszer, és kibontani majd föld Azure Data Lake Store.
* Adatok másolása GZip tömörített szöveges (CSV) formátumú az Azure Blob, és az Azure SQL Database írni.

## <a name="supported-regions"></a>Támogatott régiók

A szolgáltatás, amely a rendszert működtet a másolási tevékenység érhető globális régiókban és földrajzi felsorolt [Azure integrációs futásidejű helyek](concepts-integration-runtime.md#integration-runtime-location). A globálisan elérhető topológia hatékony adatmozgás, amely általában a kereszt-régió ugrások nem biztosítja. Lásd: [régiói](https://azure.microsoft.com/regions/#services) a Data Factory és az adatátvitelt jelölik a régióban rendelkezésre állását.

## <a name="configuration"></a>Konfiguráció

A másolási tevékenység az Azure Data Factory használatához meg kell:

1. **Az adattár forrás és fogadó adattár társított szolgáltatások létrehozásához.** Tekintse át az összekötő cikk "Társított szolgáltatás tulajdonságai" részt, konfigurálása és a támogatott tulajdonságok. A támogatott összekötő listáján található [adatokról és formátumok támogatott](#supported-data-stores-and-formats) szakasz.
2. **Hozzon létre a forrás és a fogadó adathalmaz.** Tekintse meg a forrás- és összekötő cikkek "Adatkészlet tulajdonságai" szakasz gyűjtése konfigurálása és a támogatott tulajdonságok.
3. **Hozzon létre egy folyamatot másolási tevékenység.** A következő szakaszban példaként szolgál.  

### <a name="syntax"></a>Szintaxis

A másolási tevékenység a következő sablon tartalmazza a támogatott tulajdonságok teljesnek. Adja meg, amelyek felelnek meg a forgatókönyvnek megfelelően.

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
            "cloudDataMovementUnits": <number>,
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
| type | A másolási tevékenység tulajdonságra kell beállítani: **másolása** | Igen |
| Bemenetek | Adja meg az adatkészlet mely pontok a forrásadatokhoz való létrehozott. Másolási tevékenység során csak egyetlen bemenetet támogatja. | Igen |
| kimenetek | Adja meg az adatkészlet mely pontok, a fogadó adatok létrehozott. Másolási tevékenység során csak egyetlen kimeneti támogatja. | Igen |
| typeProperties | Tulajdonságok másolása tevékenység konfigurálása csoportja. | Igen |
| forrás | Adja meg a másolási adatforrás típusa és a vonatkozó tulajdonságok kapcsolatos adatok lekéréséhez.<br/><br/>További részletek az összekötő a cikkben szereplő "Másolása a tevékenység tulajdonságai" szakaszából [adatokról és formátumok támogatott](#supported-data-stores-and-formats). | Igen |
| a fogadó | Adja meg a másolási a fogadó típusa és a vonatkozó tulajdonságok hogyan adatokat írni.<br/><br/>További részletek az összekötő a cikkben szereplő "Másolása a tevékenység tulajdonságai" szakaszából [adatokról és formátumok támogatott](#supported-data-stores-and-formats). | Igen |
| translator | Adjon meg explicit oszlop-hozzárendelések gyűjtése forrásból. Érvényes, ha az alapértelmezett példány viselkedés nem teljesíthető az igényeknek.<br/><br/>További részletek a [séma- és adatok hozzárendelése](copy-activity-schema-and-type-mapping.md). | Nem |
| cloudDataMovementUnits | Adja meg a powerfulness [Azure integrációs futásidejű](concepts-integration-runtime.md) építve az adatok másolását.<br/><br/>További részletek a [adatátviteli adategységek felhőalapú](copy-activity-performance.md). | Nem |
| parallelCopies | Adja meg, amelyet a másolási tevékenység gyűjtése adatok a forrás adatok írásakor vagy olvasásakor használandó párhuzamosságát.<br/><br/>További részletek a [másolási párhuzamos](copy-activity-performance.md#parallel-copy). | Nem |
| enableStaging<br/>stagingSettings | Válassza ki az átmeneti adatok helyett közvetlenül adatok gyűjtésének forrásból aa blob Storage előkészítése.<br/><br/>Ismerje meg a hasznos forgatókönyvek és a konfigurációs adatait a [másolási előkészített](copy-activity-performance.md#staged-copy). | Nem |
| enableSkipIncompatibleRow<br/>redirectIncompatibleRowSettings| Válassza ki, hogyan kezelje a nem kompatibilis sorok gyűjtése forrásból származó adatok másolásakor.<br/><br/>További részletek a [hibatűrés](copy-activity-fault-tolerance.md). | Nem |

## <a name="monitoring"></a>Figyelés

A másolási tevékenység során Azure Data Factory "Szerző és figyelés" felhasználói felületén vagy programozott módon figyelheti. Ezután összehasonlíthatja a teljesítmény és a forgatókönyvhöz a másolási tevékenység konfigurációjának [teljesítményfigyelési](copy-activity-performance.md#performance-reference) a belső fejlesztésű tesztelése.

### <a name="monitor-visually"></a>Vizuális megfigyelés

Vizuálisan figyeléséhez a másolási tevékenység során futtatni, nyissa meg a data factory -> **Szerző & figyelő** -> **figyelés lapján**, láthatja, hogy az adatcsatorna listáját fut, a "Tevékenységfutnézet"hivatkozásra **Műveletek** oszlop. 

![A figyelő folyamat fut](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)

Kattintson a folyamatot futtató tevékenységek listájának megjelenítéséhez. Az a **műveletek** oszlop, a másolási tevékenység bemeneti, kimeneti, a hibák (Ha a másolási tevékenység futtatása sikertelen lesz) és a részletek a kapcsolat van.

![A figyelő tevékenység fut](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

Kattintson a "**részletek**" hivatkozásra **műveletek** másolási tevékenység végrehajtási részletek és a teljesítményt. Az információk láthatók, többek között: fogadó, az átviteli sebesség, a lépések a megfelelő időtartam végighalad, és a konfigurációt használja a másolásának esetéhez forráskiszolgálóról másolt adatok mennyiségét.

**Példa: Azure Data Lake Store másolása Amazon S3**
![figyelése tevékenység fut részletei](./media/copy-activity-overview/monitor-activity-run-details-adls.png)

**Példa: Azure SQL Data Warehouse használata az Azure SQL Database másolását előkészített másolási**
![figyelése tevékenység fut részletei](./media/copy-activity-overview/monitor-activity-run-details-sql-dw.png)

### <a name="monitor-programmatically"></a>Programozott módon figyelése

Másolási tevékenység végrehajtási részletei és a teljesítményt is megjelennek a másolási tevékenység result futtassa -> Output szakasz. Az alábbiakban a lista tartalmazza; csak a Másolás forgatókönyvhöz alkalmazható megfelelően jelennek meg. Megtudhatja, hogyan fogja figyelni-ről futtatva [gyors üzembe helyezés, figyelés szakaszban](quickstart-create-data-factory-dot-net.md#monitor-a-pipeline-run).

| Tulajdonság neve  | Leírás | Unit (Egység) |
|:--- |:--- |:--- |
| DataRead | Forrásoldali adatok mérete | Int64 típusú érték a **bájt** |
| DataWritten | Gyűjtése írt adatok mérete | Int64 típusú érték a **bájt** |
| filesRead | Ha az adatok másolása a file storage másolásának fájlok száma. | Int64 típusú érték (egység) |
| filesWritten | A file storage adatok másolásakor másolásának fájlok száma. | Int64 típusú érték (egység) |
| rowsCopied | Másolással (bináris példány nem használható) sorok száma. | Int64 típusú érték (egység) |
| rowsSkipped | Nem kompatibilis kihagyta a sorok száma. Bekapcsolása a szolgáltatás beállítása "enableSkipIncompatibleRow" true által. | Int64 típusú érték (egység) |
| Átviteli sebesség | Átvitt adatok – amely arány | Lebegőpontos szám a **KB/s** |
| copyDuration | A másolási időtartama | Int32 típusú érték, másodpercben. |
| sqlDwPolyBase | Ha az adatok másolása az SQL Data Warehouse PolyBase használatos. | Logikai |
| redshiftUnload | Ha az adatok másolása Redshift UNLOAD használatos. | Logikai |
| hdfsDistcp | Ha az adatok másolása HDFS ból a DistCp használatos. | Logikai |
| effectiveIntegrationRuntime | Integráció Runtime(s) használt a tevékenység fut, a kell ellenőrizniük megjelenítése `<IR name> (<region if it's Azure IR>)`. | Szöveg (karakterlánc) |
| usedCloudDataMovementUnits | A hatékony felhő adatátviteli adategységek másolása során. | Int32 érték |
| usedParallelCopies | A hatékony parallelCopies másolása során. | Int32 érték|
| redirectRowPath | A blob-tároló "redirectIncompatibleRowSettings" alapján konfigurálja a rendszer kihagyta nem kompatibilis sorok a napló elérési útja. Lásd az alábbi példa. | Szöveg (karakterlánc) |
| executionDetails | További részleteket a másolási tevékenység során megy keresztül, szakaszt, és a megfelelő lépéseket, időtartama, használt konfigurációk, stb. Ez a szakasz elemzése, meg nem javasolt. | Tömb |

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
    "usedCloudDataMovementUnits": 32,
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
            "usedCloudDataMovementUnits": 32,
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

Tekintse meg a [séma- és adatok hozzárendelése](copy-activity-schema-and-type-mapping.md), amely ismerteti, hogyan másolási tevékenység leképezi a forrás-adatok gyűjtése.

## <a name="fault-tolerance"></a>Hibatűrés

Alapértelmezés szerint a másolási tevékenység leállítja az adatok másolása, és sikertelen adja vissza. Ha között a forrás és a fogadó nem kompatibilis adat. Explicit módon konfigurálhatja kihagyásához és a nem kompatibilis sor naplózása, és csak az ezen kompatibilis adatokba, így a másolás sikerült másolni. Tekintse meg a [másolási tevékenység hibatűrést](copy-activity-fault-tolerance.md) a további részletek.

## <a name="performance-and-tuning"></a>Teljesítmény és finomhangolás

Tekintse meg a [másolási tevékenység teljesítmény- és hangolási útmutató](copy-activity-performance.md), amely ismerteti az Azure Data Factory (másolási tevékenység) adatátvitel teljesítményét befolyásoló tényezők. Felsorolja a belső tesztelése során a megfigyelt teljesítmény és a másolási tevékenység teljesítményének optimalizálásához különböző módját tárgyalja.

## <a name="incremental-copy"></a>Növekményes másolása 
Adat-előállító 2-es szituációkat Növekményesen másolására különbözeti adatokat a forrás-tárolóban a cél-tárolóban. Lásd: [oktatóanyag: adatok Növekményesen másolása](tutorial-incremental-copy-overview.md). 

## <a name="read-and-write-partitioned-data"></a>Olvasási és írási particionált adatok
1-es verziójával Azure Data Factory Olvasás vagy írás a particionált SliceStart/SliceEnd/WindowStart/WindowEnd rendszerváltozók használatával támogatott. A 2-es verzióját ez a viselkedés a paraméter értékének csővezeték paraméter és a trigger elem ütemezett időpont/kezdete segítségével érhet el. További információkért lásd: [olvasására vagy írására, hogyan particionálva adatok](how-to-read-write-partitioned-data.md).

## <a name="next-steps"></a>További lépések
Tekintse meg a következő quickstarts, oktatóanyagok és minták:

- [Adatok másolása egyik helyről a azonos Azure Blob Storage tárolóban egy másik helyre](quickstart-create-data-factory-dot-net.md)
- [Adatok másolása az Azure Blob Storage az Azure SQL adatbázis](tutorial-copy-data-dot-net.md)
- [A helyszíni SQL Server az Azure-bA adatok másolása](tutorial-hybrid-copy-powershell.md)
