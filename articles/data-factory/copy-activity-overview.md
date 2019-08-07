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
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: jingwang
ms.openlocfilehash: ae8b2bb7cce545ab9c0aa0c9d4d682089cc482ab
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68827485"
---
# <a name="copy-activity-in-azure-data-factory"></a>Az Azure Data Factory másolási tevékenysége

## <a name="overview"></a>Áttekintés

> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-data-movement-activities.md)
> * [Aktuális verzió](copy-activity-overview.md)

Az Azure Data Factory a másolási tevékenység használatával között adatokat tárolja a helyszíni és felhőbeli adatok másolása. Az Adatmásolást követően további tevékenységekkel is átalakíthatja és elemezheti a fájlokat. A másolási tevékenység használatával Adatátalakítási és elemzési eredmények az üzleti intelligenciára épülő (BI) és az alkalmazás elküldött közzététele.

![Szerepkör-másolási tevékenység](media/copy-activity-overview/copy-activity.png)

A másolási tevékenység végrehajtása a- [Integration Runtime](concepts-integration-runtime.md). A különböző adatmásolási forgatókönyvek esetében a Integration Runtime különböző ízeket használhat:

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

A másolási tevékenység a megadott formátumú fájlok olvasását és írását is támogatja: **Szöveg, JSON, Avro, ork és parketta**, valamint fájlok tömörítése és kibontása a következő kodekekkel: **Gzip, deflate, BZip2 és ZipDeflate**. Lásd: [támogatott fájl- és tömörítési formátumok](supported-file-formats-and-compression-codecs.md) adatokkal.

Ha például a következő másolási tevékenységek teheti:

* Az Adatmásolás a helyszíni SQL Serverban, és a Azure Data Lake Storage Gen2ba való írás parketta formátumban.
* Fájlok másolása a helyszíni fájlrendszer (CSV) szöveges formátumban, és Avro formátumban az Azure-Blobba írni.
* Másolja a tömörített fájlokat a helyszíni fájlrendszerből, majd bontsa ki a kibontást, majd Azure Data Lake Storage Gen2.
* A GZip tömörített szöveg (CSV) formátumú adatok másolása az Azure-Blobból, és az Azure SQL Database írási.
* És sok más esetben szerializált/deszerializálás vagy tömörítés/kitömörítés szükséges.

## <a name="supported-regions"></a>Támogatott régiók

A másolási tevékenység biztosító szolgáltatás érhető globálisan régiókban és földrajzi területeken szereplő [Azure integrációs modul helye](concepts-integration-runtime.md#integration-runtime-location). A globálisan elérhető topológia általában Ezzel elkerülheti a régiók közötti útválasztók ugrásainak hatékony adatáthelyezés biztosítja. Lásd: [-szolgáltatások régió szerint](https://azure.microsoft.com/regions/#services) Data Factory és a egy régióban Adatáthelyezés rendelkezésre állását.

## <a name="configuration"></a>Konfiguráció

Az Azure Data Factory másolási tevékenység használatához kell tennie:

1. **Hozzon létre a társított szolgáltatást a céladattára pedig a fogadó adattárba.** Tekintse át az összekötő cikk "Társított szolgáltatások tulajdonságai" részt konfigurálása és a támogatott tulajdonságok. A támogatott összekötő listát annak [támogatott adattárak és formátumok](#supported-data-stores-and-formats) szakaszban.
2. **A forrás és fogadó-adatkészletek létrehozása.** Tekintse át a forrás és a fogadó összekötő cikk "adatkészlet tulajdonságai" című szakaszát, amely bemutatja, hogyan konfigurálhatja és támogatja a tulajdonságait.
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
| type | A másolási tevékenység Type tulajdonságát a következőre kell beállítani: **Másolja** | Igen |
| inputs | Adja meg az adatkészlet mely mutat a forrásadatok létrehozott. A másolási tevékenység csak egy egyetlen bemeneti támogatja. | Igen |
| outputs | Adja meg az adatkészlet mely mutat a fogadó adatait létrehozott. A másolási tevékenység csak egyetlen kimeneti támogatja. | Igen |
| typeProperties | Egy csoport tulajdonságainak konfigurálása a másolási tevékenység. | Igen |
| source | Adja meg a másolat forrástípusa és a vonatkozó tulajdonságok adatok lekérésével.<br/><br/>További részletek az összekötő a cikkben szereplő "Másolási tevékenység tulajdonságai" szakaszban [támogatott adattárak és formátumok](#supported-data-stores-and-formats). | Igen |
| sink | Adja meg a másolat fogadótípusa és a vonatkozó tulajdonságok hogyan szeretne adatokat írni.<br/><br/>További részletek az összekötő a cikkben szereplő "Másolási tevékenység tulajdonságai" szakaszban [támogatott adattárak és formátumok](#supported-data-stores-and-formats). | Igen |
| translator | Adja meg a forrás és fogadó-, a kifejezett oszlopleképezés. Érvényes, ha az alapértelmezett másolási viselkedés nem teljesíthető az igényeknek.<br/><br/>A részletek [séma és adatok Típusleképezés](copy-activity-schema-and-type-mapping.md). | Nem |
| dataIntegrationUnits | Adja meg annak a powerfulness [Azure integrációs modul](concepts-integration-runtime.md) megjelenő új adatok másolását. Korábbi nevén a felhőbeli adatok adatáthelyezési egységek (DMU). <br/><br/>A részletek [integrációs adategységek](copy-activity-performance.md#data-integration-units). | Nem |
| parallelCopies | Adja meg, amelyeket szeretne használni a forrás- és adatok írása az adatok olvasásakor a fogadó a másolási tevékenység párhuzamosságát.<br/><br/>A részletek [másolási párhuzamos](copy-activity-performance.md#parallel-copy). | Nem |
| enableStaging<br/>stagingSettings | Megadhatja, hogy az ideiglenes adatok egy blob Storage-tárolóban legyenek átmásolva közvetlenül a forrásról a fogadóba.<br/><br/>Ismerje meg, a hasznos forgatókönyveket és a konfigurációs részleteket [szakaszos Másolás](copy-activity-performance.md#staged-copy). | Nem |
| enableSkipIncompatibleRow<br/>redirectIncompatibleRowSettings| Válassza ki, hogyan legyen kezelve az inkompatibilis sorok, ha másol adatokat a forrás és fogadó-, a.<br/><br/>A részletek [hibatűrés](copy-activity-fault-tolerance.md). | Nem |

## <a name="monitoring"></a>Figyelés

A másolási tevékenység futtatása az Azure Data Factory "Létrehozás és Monitorozás" felhasználói felületen vagy programozás útján követheti nyomon. Ezután összehasonlíthatja a teljesítmény és a konfiguráció a másolási tevékenység, megoldásait [teljesítményfigyelési](copy-activity-performance.md#performance-reference) házon belüli tesztelési szakaszából.

### <a name="monitor-visually"></a>Vizuális megfigyelés

A másolási tevékenység futtatási vizuálisan monitorozásához válassza a data factory -> **létrehozás és Monitorozás** -> **figyelés lapon**, folyamat listáját a "Tevékenységfuttatásokmegtekintése"hivatkozástfoglátni, **Műveletek** oszlop.

![Folyamatfuttatások monitorozása](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)

Kattintson ide a folyamat futásának tevékenységek listájának megjelenítéséhez. Az a **műveletek** oszlop, rendelkezik a másolási tevékenység bemeneti, kimeneti, hibák (Ha nem sikerül a másolási tevékenység futtatási) és részleteket mutató hivatkozásokat.

![Tevékenységfuttatások monitorozása](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

Kattintson a "**részletek**" hivatkozásra **műveletek** látható a másolási tevékenység végrehajtásának részletei és teljesítményt nyújt. Megjeleníti a adatait az adatok többek között kötet/sor/fájlokat másolni a forrás, fogadó-, az átviteli sebesség, halad végig a megfelelő időtartamot, és a használt konfigurációk a másolási forgatókönyv lépéseit.

>[!TIP]
>Bizonyos esetekben a másolás figyelése oldal tetején a "**Performance tuning tippek**" is látható, amely az észlelt szűk keresztmetszetet mutatja, és végigvezeti Önt a másolási sebesség növelésének a megváltoztatásával kapcsolatban, a részletekről [itt](#performance-and-tuning)talál példát.

**Példa: másolja az Amazon S3-ból az Azure Data Lake Store**
![figyelő tevékenységfuttatás részletei](./media/copy-activity-overview/monitor-activity-run-details-adls.png)

**Példa: példány az Azure SQL Database-ből az Azure SQL Data Warehouse használatával előkészített másolási**
![figyelő tevékenységfuttatás részletei](./media/copy-activity-overview/monitor-activity-run-details-sql-dw.png)

### <a name="monitor-programmatically"></a>Programozott figyelése

A másolási tevékenység végrehajtásának részletei és a teljesítménnyel kapcsolatos jellemzők a másolási tevékenység futtatási eredmény – > kimenet szakaszában is visszakerülnek. Az alábbi, a teljesség; csak a Másolás forgatókönyvéhez alkalmazható azokat jelennek meg. Ismerje meg, hogyan figyelheti a tevékenységfuttatási [rövid útmutató a figyelés szakaszban](quickstart-create-data-factory-dot-net.md#monitor-a-pipeline-run).

| Tulajdonság neve  | Leírás | Unit (Egység) |
|:--- |:--- |:--- |
| dataRead | Olvassa el a forrásból származó adatok mérete | Int64 típusú érték a **bájtok** |
| dataWritten | A fogadó írt adatok mérete | Int64 típusú érték a **bájtok** |
| filesRead | A fájlok másolását, amikor az adatok másolása a file storage száma. | Int64 típusú érték (egység) |
| filesWritten | A fájlok másolását, amikor az adatok másolása a file Storage száma. | Int64 típusú érték (egység) |
| sourcePeakConnections | A másolási tevékenység futtatása során a forrás adattárban létesített egyidejű kapcsolatok maximális száma. | Int64 típusú érték (egység) |
| sinkPeakConnections | A másolási tevékenység futtatása során a fogadó adattár számára létesített egyidejű kapcsolatok maximális száma. | Int64 típusú érték (egység) |
| rowsRead | A forrásból beolvasott sorok száma (bináris másolás esetén nem alkalmazható). | Int64 típusú érték (egység) |
| rowsCopied | A fogadóba másolandó sorok száma (bináris másolás esetén nem alkalmazható). | Int64 típusú érték (egység) |
| rowsSkipped | Kihagyta a inkompatibilis sorok száma. A szolgáltatás által beállított "enableSkipIncompatibleRow" true bekapcsolhatja. | Int64 típusú érték (egység) |
| copyDuration | A másolás időtartama. | Másodpercek alatt Int32 érték |
| throughput | Az adatátviteli arány | Lebegőpontos szám a **KB/s** |
| sourcePeakConnections | A forrás adattár számára a másolás során létesített egyidejű kapcsolatok maximális száma. | Int32 érték |
| sinkPeakConnections| A fogadó adattárhoz a másolás során létesített egyidejű kapcsolatok maximális száma.| Int32 érték |
| sqlDwPolyBase | A PolyBase használata, amikor az adatok másolása az SQL Data Warehouse-bA. | Logikai |
| redshiftUnload | Ha az adatok másolása Redshift eltávolítása használatos. | Logikai |
| hdfsDistcp | A DistCp használata, ha másol adatokat a HDFS-ből. | Logikai |
| effectiveIntegrationRuntime | Show, amelyet integrációs Runtime(s) biztosítson hatékony eszközöket a tevékenység fut, az formátumban `<IR name> (<region if it's Azure IR>)`. | Szöveg (karakterlánc) |
| usedDataIntegrationUnits | A hatékony integrációs adategységek másolása során. | Int32 érték |
| usedParallelCopies | A hatékony parallelCopies másolása során. | Int32 érték |
| redirectRowPath | A blob storage "redirectIncompatibleRowSettings" területen konfigurálja a rendszer kihagyta inkompatibilis sorok a napló elérési útja. Lásd az alábbi példában. | Szöveg (karakterlánc) |
| executionDetails | További részleteket a szakasz a másolási tevékenység halad végig, és a megfelelő lépéseket, időtartam, használt konfigurációk stb. Nem javasoljuk ebben a szakaszban elemezni, előfordulhat, hogy módosítása esetén.<br/><br/>Az ADF azt is jelenti, hogy a részletes időtartam (másodpercben) a következő `detailedDurations`részekben foglalt lépésekből áll:<br/>- **Üzenetsor** -kezelő időtartama (`queuingDuration`): Az az idő, ameddig a másolási tevékenység ténylegesen nem indul el az integrációs modulban. Ha saját üzemeltetésű integrációs modult használ, és ez az érték nagy, javasoljuk, hogy ellenőrizze az IR-kapacitást és a használatot, és a számítási feladatok alapján felskálázást és kicsinyítést. <br/>- **Parancsfájl-másolás előtti idő** (`preCopyScriptDuration`): A fogadó adattárba való másolás előtti parancsfájl végrehajtásának ideje. A másolás előtti parancsfájl konfigurálásakor alkalmazandó. <br/>- **Az első bájtig eltelt idő** (`timeToFirstByte`): Az az időpont, ameddig az integrációs modul megkapja az első bájtot a forrás adattárból. Alkalmazás nem fájl alapú forrásra. Ha ez az érték nagy, javasoljuk, hogy ellenőrizze és optimalizálja a lekérdezést vagy a kiszolgálót.<br/>- **Átvitel időtartama** (`transferDuration`): Az az idő, amellyel az integrációs modul az összes, a forrásról a fogadóra irányuló adatátvitelt az első bájt beszerzése után | Array |
| perfRecommendation | Teljesítmény-finomhangolási tippek másolása. A részletekért lásd a [teljesítmény és hangolás](#performance-and-tuning) szakaszt. | Array |

```json
"output": {
    "dataRead": 6198358,
    "dataWritten": 19169324,
    "filesRead": 1,
    "sourcePeakConnections": 1,
    "sinkPeakConnections": 2,
    "rowsRead": 39614,
    "rowsCopied": 39614,
    "copyDuration": 1325,
    "throughput": 4.568,
    "errors": [],
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (West US)",
    "usedDataIntegrationUnits": 4,
    "usedParallelCopies": 1,
    "executionDetails": [
        {
            "source": {
                "type": "AzureBlobStorage"
            },
            "sink": {
                "type": "AzureSqlDatabase"
            },
            "status": "Succeeded",
            "start": "2019-08-06T01:01:36.7778286Z",
            "duration": 1325,
            "usedDataIntegrationUnits": 4,
            "usedParallelCopies": 1,
            "detailedDurations": {
                "queuingDuration": 2,
                "preCopyScriptDuration": 12,
                "transferDuration": 1311
            }
        }
    ],
    "perfRecommendation": [
        {
            "Tip": "Sink Azure SQL Database: The DTU utilization was high during the copy activity run. To achieve better performance, you are suggested to scale the database to a higher tier than the current 1600 DTUs.",
            "ReferUrl": "https://go.microsoft.com/fwlink/?linkid=2043368",
            "RuleName": "AzureDBTierUpgradePerfRecommendRule"
        }
    ]
}
```

## <a name="schema-and-data-type-mapping"></a>Séma- és adattípus-leképezés

Tekintse meg a [séma és adatok Típusleképezés](copy-activity-schema-and-type-mapping.md), amely azt ismerteti, hogyan képezi le a másolási tevékenység fogadó a forrásadatokat.

## <a name="fault-tolerance"></a>Hibatűrés

Alapértelmezés szerint a másolási tevékenység leállítja az adatok másolását, és hibát ad vissza, ha a forrás és a fogadó nem kompatibilis adatokkal találkozik. Explicit módon konfigurálhatja, hagyja ki, és jelentkezzen az inkompatibilis sorok, és csak a sikeres volt-e a másoláshoz ezeket kompatibilis adatokat másolni. Tekintse meg a [másolási tevékenység a hibatűrés](copy-activity-fault-tolerance.md) a további részleteket.

## <a name="performance-and-tuning"></a>Teljesítmény és finomhangolás

Tekintse meg a [másolási tevékenységek teljesítményéről és finomhangolásáról szóló útmutató](copy-activity-performance.md), amely ismerteti az adatáthelyezés (másolási tevékenység) az Azure Data Factoryban teljesítményét befolyásoló tényezők. Felsorolja a megfigyelt teljesítmény belső tesztelése során és a különböző módjait a másolási tevékenység teljesítményének ismerteti.

Bizonyos esetekben, amikor másolási tevékenységet hajt végre az ADF-ben, a [másolási tevékenység figyelése lap](#monitor-visually) tetején, a következő példában látható módon "**Performance tuning tippek**" jelenik meg. Ez nem csak azt jelzi, hogy milyen szűk keresztmetszetet észlelt a rendszer a megadott másolási futtatáshoz, de a másolási sebesség növelését is ismerteti. A teljesítmény-hangolási tippek jelenleg olyan javaslatokat biztosítanak, mint például az adatok Azure SQL Data Warehouseba való másolásakor a következők: a Azure Cosmos DB RU vagy az Azure SQL DB DTU növelését, ha az adattárolási oldalon lévő erőforrás szűk keresztmetszetű, a szükségtelen fázisok eltávolításához másolás stb. A teljesítmény-hangolási szabályok fokozatos bővítése is megtörténik.

**Példa: másolás az Azure SQL DB-be teljesítmény-hangolási tippekkel**

Ebben a példában a másolás futtatása során az ADF azt jelzi, hogy a fogadó Azure SQL-ADATBÁZISa magas DTU-kihasználtságot eredményez, ami lelassítja az írási műveleteket, így a javaslat az Azure SQL DB-szint nagyobb DTU való növelését teszi elérhetővé.

![A monitoring és a Performance tuning tippek másolása](./media/copy-activity-overview/copy-monitoring-with-performance-tuning-tips.png)

## <a name="incremental-copy"></a>A növekményes másolási
A Data Factory olyan forgatókönyveket támogat, amelyekkel növekményes módon másolhat különbözeti adattárakat a fogadó adattárba. Lásd: [oktatóanyag: adatok növekményes másolása az](tutorial-incremental-copy-overview.md).

## <a name="read-and-write-partitioned-data"></a>Olvasási és írási particionált adatok
Azure Data Factory 1-es verzió támogatott SliceStart/SliceEnd/WindowStart WindowEnd rendszerváltozók használatával particionált adatok írása és olvasása. A jelenlegi verzióban a paraméter értékeként egy folyamat paraméter és a trigger kezdő időpontja és ütemezett időpontja használatával érheti el ezt a viselkedést. További információkért lásd: [olvasására vagy írására hogyan particionált adatok](how-to-read-write-partitioned-data.md).

## <a name="next-steps"></a>További lépések
Tekintse meg a következő útmutatóink, oktatóanyagaink és példáink segítségével:

- [Adatok másolása egyik helyről egy másik helyre az ugyanazon az Azure Blob Storage](quickstart-create-data-factory-dot-net.md)
- [Adatok másolása az Azure Blob Storage-ból az Azure SQL Database](tutorial-copy-data-dot-net.md)
- [Adatok másolása helyszíni SQL Serverről az Azure-bA](tutorial-hybrid-copy-powershell.md)
