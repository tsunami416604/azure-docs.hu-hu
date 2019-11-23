---
title: Másolási tevékenység Azure Data Factory
description: Ismerkedjen meg a másolási tevékenységgel Azure Data Factoryban. Használhatja az adatok egy támogatott forrás adattárból egy támogatott fogadó adattárba való másolásához.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: jingwang
ms.openlocfilehash: fa2876b88a520480813ebfb8af8219d53c32057a
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075562"
---
# <a name="copy-activity-in-azure-data-factory"></a>Másolási tevékenység Azure Data Factory

> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory verzióját:"]
> * [1-es verzió](v1/data-factory-data-movement-activities.md)
> * [Aktuális verzió](copy-activity-overview.md)

Azure Data Factory a másolási tevékenységgel a helyszíni és a felhőben található adattárak között másolhatók az Adatmásolás adatai. Az Adatmásolás után más tevékenységeket is használhat a további átalakításához és elemzéséhez. A másolási tevékenységgel az üzleti intelligencia (BI) és az alkalmazások felhasználásának átalakítási és elemzési eredményeit is közzéteheti.

![A másolási tevékenység szerepe](media/copy-activity-overview/copy-activity.png)

A másolási tevékenység végrehajtása egy [integrációs](concepts-integration-runtime.md)modulon történik. Különböző típusú integrációs modulokat használhat különféle adatmásolási forgatókönyvekhez:

* Ha az adatok másolása két olyan adattár között történik, amely nyilvánosan elérhető az interneten keresztül bármely IP-címről, használhatja az Azure Integration Runtime alkalmazást a másolási tevékenységhez. Ez az integrációs modul biztonságos, megbízható, méretezhető és [globálisan elérhető](concepts-integration-runtime.md#integration-runtime-location).
* Ha a helyszíni környezetben vagy a hozzáférés-vezérléssel (például egy Azure virtuális hálózattal) rendelkező hálózaton található adattárakba másolja az adatok másolását, akkor egy saját üzemeltetésű integrációs modult kell beállítania.

Integrációs modult kell társítani az egyes forrásokhoz és fogadó adattárakhoz. További információ arról, hogy a másolási tevékenység melyik integrációs futtatókörnyezetet határozza meg. a [használandó IR meghatározása](concepts-integration-runtime.md#determining-which-ir-to-use)című témakörben talál további információt.

Az adatok forrásról fogadóba való másolásához a másolási tevékenységet futtató szolgáltatás a következő lépéseket hajtja végre:

1. Beolvassa az adatokat a forrásadattárból.
2. Szerializálási/deszerializálási, tömörítési/kibontási, oszlop-hozzárendelési és egyéb műveleteket hajt végre. Ezeket a műveleteket a bemeneti adatkészlet, a kimeneti adatkészlet és a másolási tevékenység konfigurációja alapján hajtja végre.
3. A fogadó és a cél adattárolóba írja az adatokat.

![Másolási tevékenység áttekintése](media/copy-activity-overview/copy-activity-overview.png)

## <a name="supported-data-stores-and-formats"></a>Támogatott adattárak és formátumok

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

### <a name="supported-file-formats"></a>Támogatott fájlformátumok

A másolási tevékenységgel fájlokat másolhat két fájl alapú adattár között. Ebben az esetben az Adatmásolás hatékonyan, szerializálás vagy deszerializálás nélkül történik.

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Például a következő másolási tevékenységeket végezheti el:

* Adatok másolása helyszíni SQL Server-adatbázisból, és az adatok beírása a Azure Data Lake Storage Gen2ba parketta formátumban.
* Szövegfájlok (CSV) formátumának másolása helyszíni fájlrendszerből és az Azure Blob Storage-ba való írás Avro formátumban.
* Másolja a tömörített fájlokat egy helyszíni fájlrendszerből, bontsa ki őket, és írja azokat Azure Data Lake Storage Gen2ba.
* Adatok másolása gzip tömörített szövegfájlba (CSV) az Azure Blob Storage-ból, és a Azure SQL Databasebe írás.
* Számos további tevékenység, amelyek szerializálást/deszerializálást vagy tömörítést/kibontást igényelnek.

## <a name="supported-regions"></a>Támogatott régiók

A másolási tevékenységet engedélyező szolgáltatás globálisan elérhető az [Azure Integration Runtime helyein](concepts-integration-runtime.md#integration-runtime-location)felsorolt régiókban és földrajzi területeken. A globálisan elérhető topológia általában Ezzel elkerülheti a régiók közötti útválasztók ugrásainak hatékony adatáthelyezés biztosítja. A [termékek régiónként](https://azure.microsoft.com/regions/#services) való megtekintésével ellenőrizze, hogy rendelkezésre áll-e a Data Factory és az adatáthelyezés egy adott régióban.

## <a name="configuration"></a>Konfiguráció

A másolási tevékenység Azure Data Factoryban való használatához a következőket kell tennie:

1. **Társított szolgáltatások létrehozása a forrás adattárhoz és a fogadó adattárhoz.** A konfigurációs információk és a támogatott tulajdonságok esetében tekintse meg az összekötő cikk "társított szolgáltatás tulajdonságai" szakaszát. A támogatott összekötők listáját a jelen cikk [támogatott adattárak és formátumok](#supported-data-stores-and-formats) című szakaszában találja.
2. **Hozzon létre adatkészleteket a forráshoz és a fogadóhoz.** A konfigurációs információkat és a támogatott tulajdonságokat a forrás és a fogadó összekötő cikkek "adatkészlet tulajdonságai" szakaszában találja.
3. **Hozzon létre egy folyamatot a másolási tevékenységgel.** Ez a szakasz mutatja be.

### <a name="syntax"></a>Szintaxis

A másolási tevékenység következő sablonja a támogatott tulajdonságok teljes listáját tartalmazza. Adja meg, amelyekre a saját forgatókönyvéhez igazítva.

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

#### <a name="syntax-details"></a>Szintaxis részletei

| Tulajdonság | Leírás | Kötelező? |
|:--- |:--- |:--- |
| type | Másolási tevékenység esetén állítsa a következőre: `Copy` | Igen |
| inputs | Itt adhatja meg a forrásadatok számára kimutatott adatkészletet. A másolási tevékenység csak egyetlen bemenetet támogat. | Igen |
| outputs | Itt adhatja meg a fogadó adatra mutató adatkészletet. A másolási tevékenység csak egyetlen kimenetet támogat. | Igen |
| typeProperties | A másolási tevékenység konfigurálásához adja meg a tulajdonságokat. | Igen |
| source | Adja meg a másolás forrásának típusát és a megfelelő tulajdonságokat az adatok beolvasásához.<br/><br/>További információ: "másolási tevékenység tulajdonságai" szakasz, a [támogatott adattárakban és-formátumokban](#supported-data-stores-and-formats)felsorolt összekötők című cikke. | Igen |
| sink | Adja meg a másolási fogadó típusát és a hozzá tartozó tulajdonságokat az adatíráshoz.<br/><br/>További információ: "másolási tevékenység tulajdonságai" szakasz, a [támogatott adattárakban és-formátumokban](#supported-data-stores-and-formats)felsorolt összekötők című cikke. | Igen |
| translator | Adja meg a forrás és fogadó-, a kifejezett oszlopleképezés. Ez a tulajdonság akkor érvényes, ha az alapértelmezett másolási viselkedés nem felel meg az igényeinek.<br/><br/>További információ: [séma-hozzárendelés a másolási tevékenységben](copy-activity-schema-and-type-mapping.md). | Nem |
| dataIntegrationUnits | Adja meg azt a mértéket, amely az [Azure Integration Runtime](concepts-integration-runtime.md) által az adatmásoláshoz használt teljesítmény mennyiségét jelöli. Ezeket az egységeket korábban Felhőbeli adatáthelyezési egységeknek (DMU) nevezik. <br/><br/>További információ: [adatintegrációs egységek](copy-activity-performance.md#data-integration-units). | Nem |
| parallelCopies | Itt adhatja meg azt a párhuzamosságot, amelyet a másolási tevékenység az adatok forrásból való beolvasásakor és az adatok fogadóba való írásához használni kíván.<br/><br/>További információ: [párhuzamos másolás](copy-activity-performance.md#parallel-copy). | Nem |
| enableStaging<br/>stagingSettings | Itt adhatja meg, hogy a blob Storage szolgáltatásban lévő ideiglenes adatok közvetlenül a forrásról a fogadóba másolva legyenek-e.<br/><br/>További információ a hasznos forgatókönyvekről és a konfigurációs adatokról: [szakaszos másolás](copy-activity-performance.md#staged-copy). | Nem |
| enableSkipIncompatibleRow<br/>redirectIncompatibleRowSettings| Válassza ki, hogyan kezelje a nem kompatibilis sorokat a forrásról a fogadóba másolt adatok másolása során.<br/><br/>További információ: [hibatűrés](copy-activity-fault-tolerance.md). | Nem |

## <a name="monitoring"></a>Figyelés

A másolási tevékenység futtatását a Azure Data Factory szerző & a felhasználói felület **figyelése** vagy programozott módon követheti.

### <a name="monitor-visually"></a>Vizuális megfigyelés

A másolási tevékenység futtatásának vizuális figyeléséhez nyissa meg az adatgyárat, és lépjen a **szerző & monitor**elemre. A **figyelés** lapon megtekintheti a folyamat futásának listáját a **műveletek** oszlopban található **megtekintési tevékenység futtatása** gombbal:

![Folyamatfuttatások monitorozása](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)

Válassza a tevékenységek **megtekintése futtatása** lehetőséget a folyamat futtatási tevékenységeinek megtekintéséhez. A **műveletek** oszlopban láthatók a másolási tevékenység bemenetére mutató hivatkozások, a kimenet, a hibák (ha a másolási tevékenység futtatása sikertelen) és a részletek:

![Tevékenységfuttatások monitorozása](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

A **műveletek** oszlopban a **részletek** gombra kattintva megtekintheti a másolási tevékenység végrehajtási részleteit és a teljesítmény jellemzőit. A forrásról a fogadóra, az átviteli sebességre, a másolási tevékenység menetére és a másoláshoz használt konfigurációkra vonatkozó információk, például a sorok mennyisége/száma, valamint az adatfájlok száma.

>[!TIP]
>Bizonyos helyzetekben a **teljesítmény-hangolási tippeket** is láthatja a másolás figyelése oldal tetején. Ezek a tippek ismertetik az azonosított szűk keresztmetszeteket, és információt nyújtanak arról, hogy mit kell módosítani a másolási teljesítmény növelése érdekében. Példaként tekintse meg a jelen cikk [teljesítmény és Finomhangolás](#performance-and-tuning) című szakaszát.

**Példa: másolás az Amazon S3-ból Azure Data Lake Store**
![a tevékenység futásának részletei](./media/copy-activity-overview/monitor-activity-run-details-adls.png)

**Példa: másolás Azure SQL Databaseról Azure SQL Data Warehousera előkészített másolással**
![figyelési tevékenység futtatási részletei](./media/copy-activity-overview/monitor-activity-run-details-sql-dw.png)

### <a name="monitor-programmatically"></a>Programozott figyelése

A másolási tevékenység végrehajtásának részletei és a teljesítmény jellemzői a **másolási tevékenység futtatási eredmények** > **kimenet** szakaszában is visszakerülnek. A következő lista az esetleg visszaadott tulajdonságok teljes listáját tartalmazza. Csak a másolási forgatókönyvre vonatkozó tulajdonságokat fogja látni. További információ a tevékenységek futtatásának figyeléséről: [a folyamat futásának figyelése](quickstart-create-data-factory-dot-net.md#monitor-a-pipeline-run).

| Tulajdonság neve  | Leírás | Unit (Egység) |
|:--- |:--- |:--- |
| dataRead | A forrásból beolvasott adatok mennyisége. | Int64 érték bájtban |
| dataWritten | A fogadóba írt adatmennyiség. | Int64 érték bájtban |
| filesRead | A file Storage-ból másolás során másolt fájlok száma. | Int64 típusú érték (egység) |
| filesWritten | A file Storage-ba másolás során másolt fájlok száma. | Int64 típusú érték (egység) |
| sourcePeakConnections | A másolási tevékenység futtatása során a forrás adattárban létesített egyidejű kapcsolatok maximális száma. | Int64 típusú érték (egység) |
| sinkPeakConnections | A fogadó adattárhoz a másolási tevékenység futtatása során létesített egyidejű kapcsolatok maximális száma. | Int64 típusú érték (egység) |
| rowsRead | A forrásból beolvasott sorok száma (bináris másolás esetén nem alkalmazható). | Int64 típusú érték (egység) |
| rowsCopied | A fogadóba másolt sorok száma (bináris másolás esetén nem alkalmazható). | Int64 típusú érték (egység) |
| rowsSkipped | A kihagyott inkompatibilis sorok száma. A nem kompatibilis sorok kihagyását a `enableSkipIncompatibleRow` True értékre állításával engedélyezheti. | Int64 típusú érték (egység) |
| copyDuration | A másolás futtatásának időtartama. | Int32 érték másodpercben |
| throughput | Adatátviteli sebesség. | Lebegőpontos szám (Kbit/s) |
| sourcePeakConnections | A másolási tevékenység futtatása során a forrás adattárban létesített egyidejű kapcsolatok maximális száma. | Int32 érték (nincs egység) |
| sinkPeakConnections| A fogadó adattárhoz a másolási tevékenység futtatása során létesített egyidejű kapcsolatok maximális száma.| Int32 érték (nincs egység) |
| sqlDwPolyBase | Azt jelzi, hogy a rendszer az adatok másolásakor használja-e a SQL Data Warehouse. | Logikai |
| redshiftUnload | Azt jelzi, hogy a rendszer az ELTÁVOLÍTÁSt használja-e az adatok Vöröseltolódásból történő másolásakor. | Logikai |
| hdfsDistcp | Azt határozza meg, hogy a rendszer DistCp használ-e az adatok HDFS-ből való másolásakor. | Logikai |
| effectiveIntegrationRuntime | A tevékenység futtatásához használt integrációs modul (IR) vagy futtatókörnyezet a következő formátumban: `<IR name> (<region if it's Azure IR>)`. | Szöveg (karakterlánc) |
| usedDataIntegrationUnits | A hatékony integrációs adategységek másolása során. | Int32 érték |
| usedParallelCopies | A hatékony parallelCopies másolása során. | Int32 érték |
| redirectRowPath | A `redirectIncompatibleRowSettings` tulajdonságban konfigurált blob Storage-beli kihagyott inkompatibilis sorok naplójának elérési útja. Tekintse meg a jelen cikk későbbi [hibatűrését](#fault-tolerance) . | Szöveg (karakterlánc) |
| executionDetails | További részletek a másolási tevékenység lépésein, valamint a megfelelő lépéseken, időtartamokon, konfigurációkon stb. Nem javasoljuk, hogy elemezze ezt a szakaszt, mert megváltozhat.<br/><br/>A Data Factory a `detailedDurations`alatt a különböző fázisokban töltött részletes időtartamokat (másodpercben) is jelenti. A lépések időtartama kizárólagos. Csak a megadott másolási tevékenység futtatására érvényes időtartamok jelennek meg:<br/>**Üzenetsor** -kezelő időtartama (`queuingDuration`): a másolási tevékenységnek az integrációs modulban ténylegesen elinduló ideje. Ha saját üzemeltetésű integrációs modult használ, és ez az érték nagy, ellenőrizze az IR-kapacitást és a használatot, és a számítási feladatok alapján fel-vagy leskálázást. <br/>**Másolás előtti parancsfájl időtartama** (`preCopyScriptDuration`): az az időpont, amikor a másolási tevékenység ELINDUL az IR-ben, és ha a másolási tevékenység befejezi a fogadó adattárban lévő, másolás előtti parancsfájl futtatását. A másolás előtti parancsfájl konfigurálására vonatkozik. <br/>Az **első bájtig** eltelt idő (`timeToFirstByte`): az előző lépés vége és az az idő, amikor az IR megkapja az első bájtot a forrás adattárból. A nem fájl alapú forrásokra vonatkozik. Ha ez az érték nagy, ellenőrizze és optimalizálja a lekérdezést vagy a kiszolgálót.<br/>**Átvitel időtartama** (`transferDuration`): az előző lépés vége és az az idő, amikor az IR átviszi a forrásról a fogadóba a teljes adatmennyiséget. | Tömb |
| perfRecommendation | Teljesítmény-finomhangolási tippek másolása. A részletekért lásd a [teljesítmény és hangolás](#performance-and-tuning) című témakört. | Tömb |

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

Tekintse meg a [séma-és adattípusok leképezése](copy-activity-schema-and-type-mapping.md) című témakört, amely arról nyújt tájékoztatást, hogy a másolási tevékenység hogyan képezi le a forrás adatait

## <a name="fault-tolerance"></a>Hibatűrés

Alapértelmezés szerint a másolási tevékenység leállítja az adatok másolását, és hibát ad vissza, ha a forrásoldali adatsorok nem kompatibilisek a fogadó adatsoraival. A másolás sikeres végrehajtásához beállíthatja a másolási tevékenységet úgy, hogy kihagyja és naplózza a nem kompatibilis sorokat, és csak a kompatibilis adatfájlokat másolja. Részletekért lásd a [másolási tevékenység hibatűrését](copy-activity-fault-tolerance.md) ismertető témakört.

## <a name="performance-and-tuning"></a>Teljesítmény és finomhangolás

A [másolási tevékenység teljesítményének és méretezhetőségének útmutatója](copy-activity-performance.md) ismerteti azokat a főbb tényezőket, amelyek hatással vannak az adatáthelyezés teljesítményére a Azure Data Factory másolási tevékenységén keresztül. Felsorolja továbbá a tesztelés során megfigyelt teljesítményadatokat, valamint ismerteti a másolási tevékenység teljesítményének optimalizálását.

Bizonyos helyzetekben, amikor másolási tevékenységet futtat Data Factoryban, az alábbi példában látható **teljesítmény-hangolási tippek** jelennek meg a [másolási tevékenység figyelése oldal](#monitor-visually)tetején. A tippekből megtudhatja, hogy a rendszer milyen szűk keresztmetszetet észlelt az adott másolási futtatáshoz. Emellett információt nyújtanak arról, hogy mit kell módosítani a másolási teljesítmény növelése érdekében. A teljesítmény-hangolási tippek jelenleg olyan javaslatokat biztosítanak, mint például az adatok másolása a Azure SQL Data Warehouseba, a Azure Cosmos DB RUs vagy a Azure SQL Database DTU növelése, ha az adattárban található erőforrás szűk keresztmetszetet tartalmaz, és eltávolítja szükségtelenül előkészített másolatok.

**Példa: másolás a Azure SQL Databaseba, teljesítmény-hangolási tipptel**

Ebben a példában a másolás során a Data Factory a fogadó Azure SQL Database nagy DTU-kihasználtságot követ nyomon. Ez az állapot lelassítja az írási műveleteket. A javaslat célja, hogy növelje a DTU a Azure SQL Database szinten:

![A monitoring és a Performance tuning tippek másolása](./media/copy-activity-overview/copy-monitoring-with-performance-tuning-tips.png)

## <a name="incremental-copy"></a>A növekményes másolási
A Data Factory lehetővé teszi a különbözeti adatok növekményes másolását egy forrás adattárból egy fogadó adattárba. További részletek: [oktatóanyag: adatok növekményes másolása](tutorial-incremental-copy-overview.md).

## <a name="next-steps"></a>Következő lépések
Tekintse meg a következő útmutatóink, oktatóanyagaink és példáink segítségével:

- [Adatok másolása az egyik helyről egy másik helyre ugyanazon Azure Blob Storage-fiókban](quickstart-create-data-factory-dot-net.md)
- [Adatok másolása az Azure Blob Storage-ból a Azure SQL Databaseba](tutorial-copy-data-dot-net.md)
- [Adatok másolása helyszíni SQL Server-adatbázisból az Azure-ba](tutorial-hybrid-copy-powershell.md)
