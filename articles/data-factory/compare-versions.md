---
title: "Az Azure Data Factory 1. és 2. verziójának összehasonlítása | Microsoft Docs"
description: "Ez a cikk az Azure Data Factory 1. és 2. verzióját hasonlítja össze."
services: data-factory
documentationcenter: 
author: kromerm
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/20/2017
ms.author: makromer
ms.openlocfilehash: f55348e9974de17c6d7e704e185f1ea9b21ff66e
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2017
---
# <a name="compare-azure-data-factory-versions-1-and-2"></a>Az Azure Data Factory 1. és 2. verziójának összehasonlítása
Ez a cikk az Azure Data Factory 2. (V2) és 1. (V1) verzióját hasonlítja össze. A szolgáltatás 1. verziójának bemutatását a [Data Factory, 1. verzió – bemutatás](v1/data-factory-introduction.md), a 2. verziójának bemutatását pedig a [Data Factory, 2. verzió – bemutatás](introduction.md) című részben találja.

## <a name="feature-comparison"></a>Szolgáltatások összehasonlítása
Az alábbi táblázat az 1. és 2. verzió szolgáltatásait hasonlítja össze. 

| Szolgáltatás | 1. verzió | 2. verzió | 
| ------- | --------- | --------- | 
| Adathalmazok | Azon adatok elnevezett nézete, amelyek azokra az adatokra hivatkoznak, amelyeket a tevékenységekben használni szeretne be- vagy kimenetként. Az adatkészletek adatokat határoznak meg a különböző adattárakban, például táblákban, fájlokban, mappákban és dokumentumokban. Az Azure Blob-adatkészlet például meghatározza a blobtárolót és azt a Blob Storage-mappát, amelyből a tevékenység beolvassa az adatokat.<br/><br/>A **rendelkezésre állás** megadja az adatkészlet feldolgozási időszakának felosztási modelljét (például óránként, naponta stb.). | Az adatkészletek változatlanok a 2. verzióban. Az adatkészletek **rendelkezésre állásának** ütemezését azonban nem kell megadnia. Megadhat egy eseményindító erőforrást, amely folyamatokat ütemezhet egy óraütemező paradigmából. További információkért lásd: [Eseményindítók](concepts-pipeline-execution-triggers.md#triggers) és [Adatkészletek](concepts-datasets-linked-services.md). | 
| Társított szolgáltatások | A társított szolgáltatások nagyon hasonlóak a kapcsolati karakterláncokhoz, amelyek meghatározzák azokat a kapcsolati információkat, amelyeket a Data Factory a külső erőforrásokhoz történő csatlakozáshoz igényel. | Ugyanúgy, mint a Data Factory V1 esetében, de egy új **connectVia** tulajdonsággal a Data Factory V2 Integrációs modulja számítási környezetének használatához. További információkért lásd: [Integrációs modulok](concepts-integration-runtime.md) és [Társított szolgáltatások tulajdonságai az Azure Blob Storage-hoz](connector-azure-blob-storage.md#linked-service-properties). |
| Folyamatok | A data factory egy vagy több folyamattal rendelkezhet. A folyamatok olyan tevékenységek logikus csoportosításai, amelyek együttesen vesznek részt egy feladat végrehajtásában. A startTime, endTime, isPaused értékeket használta a folyamatok futtatásához és ütemezéséhez. | A folyamatok továbbra is tevékenységek csoportjai, amelyeket adatokon kell elvégezni. A folyamat tevékenységeinek ütemezése azonban két új eseményindító erőforrásba lett elkülönítve. A Data Factory V2 folyamatai leginkább „munkafolyamat-egységeknek” tekinthetők, amelyek külön ütemezhetők az eseményindítókon keresztül. <br/><br/>A Data Factory V2-ben a folyamatok nem rendelkeznek végrehajtási időszakokkal. A Data Factory V1-ben található startTime, endTime és isPaused fogalmak már nem találhatók meg a Data Factory V2-ben. További információkat a [Folyamat-végrehajtás és eseményindítók](concepts-pipeline-execution-triggers.md) és a [Folyamatok és tevékenységek](concepts-pipelines-activities.md) című cikkben talál. |
| Tevékenységek | A tevékenységek meghatározzák a folyamatban található adatokon végrehajtandó műveleteket. Az adatáthelyezés (másolási tevékenység) és az adatátalakító tevékenységek (például Hive, Pig és MapReduce) támogatottak. | A Data Factory V2-ben a tevékenységek továbbra is egy folyamaton belül megadott műveletek. A V2-ben új, [átvitelvezérlési tevékenységek](concepts-pipelines-activities.md#control-activities) jelentek meg. Ezeket a tevékenységek az átvitelvezérlésben (hurkolás és elágaztatás) használhatja. A V1-ben támogatott adatáthelyezési és az adatátalakítási tevékenységeket a V2 is támogatja. A V2-ben adatkészletek használata nélkül is megadhat átalakítási tevékenységeket. |
| Hibrid adatáthelyezés és tevékenységküldés | Korábban az [Adatkezelési átjáró](v1/data-factory-data-management-gateway.md) néven ismert szolgáltatás támogatta az adatok áthelyezését a helyszíni rendszer és a felhő között. Ennek a neve mostantól Integrációs modul.| Az adatkezelési átjárót már Saját üzemeltetésű integrációs modulnak nevezzük. Ugyanazokkal a képességekkel rendelkezik, mint a V1-ben. <br/><br/> Az Azure-SSIS integrációs modul a V2-ben támogatja továbbá az SQL Server Integration Services- (SSIS)-csomagok üzembe helyezését és futtatását a felhőben. További részletekért tekintse meg az [Integrációs modul](concepts-integration-runtime.md) című cikket.|
| Paraméterek | NA | A paraméterek a folyamatokban megadott írásvédett konfigurációs beállítások kulcs-érték párjai. Argumentumokat adhat át a paramétereknek, ha manuálisan hajtja végre a folyamatot. Ha ütemező eseményindítót használ, az eseményindító is átadhat értékeket a paramétereknek. A folyamatok tevékenységei a paraméterértékeket dolgozzák fel.  |
| Kifejezések | A Data Factory V1 lehetővé teszi a függvények és rendszerváltozók használatát az adatkijelölési lekérdezésekben és a tevékenységek/adatkészletek tulajdonságaiban. | A Data Factory V2-ben egy JSON-karakterlánc értéken belül bárhol használhat kifejezéseket. További információk: [Kifejezések és függvények a V2-ben](control-flow-expression-language-functions.md).|
| Folyamatfuttatások | NA | A folyamat-végrehajtás egyetlen példánya. Tegyük fel például, hogy rendelkezik egy délelőtt 8-kor, 9-kor és 10-kor végbemenő folyamattal. Ebben az esetben három külön folyamatfuttatás lenne. Minden folyamathoz tartozik egy egyedi folyamatfuttatási azonosító. Ez az adott folyamatfuttatást egyedien meghatározó GUID. A folyamatfuttatások példányosítása jellemzően az argumentumoknak a folyamatokban meghatározott paraméterekhez történő továbbításával történik. |
| Tevékenységfuttatások | NA | Egy tevékenységfuttatás egyetlen példánya egy folyamatban. | 
| Eseményindító-futtatások | NA | Az eseményindító-végrehajtás egyetlen példánya. További információ: [Eseményindítók](concepts-pipeline-execution-triggers.md). |
| Ütemezés | Az ütemezés a folyamat kezdési/befejezési idején és az adatkészlet rendelkezésre állásán alapszik. | Ütemező eseményindító vagy végrehajtás külső ütemezőn keresztül. További információ: [Folyamat-végrehajtás és eseményindítók](concepts-pipeline-execution-triggers.md). |

A következő szakaszok további információt nyújtanak a 2. verzió képességeiről: 

## <a name="control-flow"></a>Átvitelvezérlés  
A modern adattárházak különféle integrációs folyamatainak és mintáinak támogatásához a Data Factory V2 egy olyan új, rugalmas adatfolyamat-modell használatát teszi lehetővé, amely nincs idősorozat-adatokhoz kötve. Néhány példa a mostantól elérhető vált folyamatokra:   

### <a name="chaining-activities"></a>Láncolási tevékenységek
Az 1. verzióban egy tevékenység kimenetét egy másik tevékenység bemeneteként kellett konfigurálnia ahhoz, hogy összekapcsolja őket. A V2-ben egy folyamaton belüli sorozatba láncolhatja össze a tevékenységeket. A **dependsOn** tulajdonság tevékenység-meghatározásban történő használatával egy fölérendelt tevékenységhez láncolhatja azt. További információkat és példákat a [Folyamatok és tevékenységek](concepts-pipelines-activities.md#multiple-activities-in-a-pipeline) és az [Elágaztatási és láncolási tevékenységek](tutorial-control-flow.md) című cikkekben talál. 

### <a name="branching-activities"></a>Elágaztatási tevékenységek
A V2-ben tevékenységeket ágaztathat el egy folyamaton belül. Az [If-condition tevékenység](control-flow-if-condition-activity.md) ugyanazokat a funkciókat biztosítja, mint a programnyelvek `if` utasítása. Egy tevékenységkészletet futtat le, ha a feltétel `true` értéket ad vissza, és egy másik tevékenységkészletet, ha a feltétel `false` értéket ad vissza. Elágaztatási tevékenységekre az [Elágaztatási és láncolási tevékenységek](tutorial-control-flow.md) oktatóanyagban talál példát.

### <a name="parameters"></a>Paraméterek 
A paraméterek folyamatszinten határozhatók meg, az argumentumok pedig a folyamat igény szerinti meghívása során vagy egy eseményindítóból adhatók át. A tevékenységek képesek a folyamat számára továbbított argumentumok feldolgozására. További információ: [Folyamatok és eseményindítók](concepts-pipeline-execution-triggers.md). 

### <a name="custom-state-passing"></a>Egyéniállapot-átadás
A tevékenységkimeneteket (például állapotot) a folyamat soron következő tevékenysége képes feldolgozni. Például egy tevékenység JSON-meghatározásában hozzáférhet az előző tevékenység kimenetéhez a következő szintaxis használatával: `@activity('NameofPreviousActivity').output.value`. Ez a szolgáltatás lehetővé teszi olyan munkafolyamatok kiépítését, ahol az értékek tevékenységeken keresztül adhatók át.

### <a name="looping-containers"></a>Tárolók hurkolása
A [ForEach tevékenység](control-flow-for-each-activity.md) ismétlődő átvitelvezérlést határoz meg a folyamatban. Ez a tevékenység egy gyűjtemény megismétlésére, valamint egy megadott ciklustevékenység végrehajtására szolgál. E tevékenység ciklusos megvalósítása hasonló a Foreach ciklusos szerkezetéhez a programozási nyelvek esetében. 

Az [Until](control-flow-until-activity.md) tevékenység ugyanazokat a funkciókat biztosítja, mint a programnyelvek Do-Until ciklusos szerkezete. Egy tevékenységkészletet futtat le ciklusosan addig, amíg a tevékenységhez rendelt feltétel igaz értéket nem ad vissza. Megadhat egy időtúllépési értéket az Until tevékenységhez a Data Factoryban.  

### <a name="trigger-based-flows"></a>Eseményindító-alapú folyamatok
A folyamatok igény szerint vagy időpont alapján indíthatók. A [Folyamatok és eseményindítók](concepts-pipeline-execution-triggers.md) című cikk részletes információkat tartalmaz az eseményindítókról. 

### <a name="invoke-a-pipeline-from-another-pipeline"></a>Folyamat meghívása egy másik folyamatból
A [Folyamat végrehajtása tevékenység](control-flow-execute-pipeline-activity.md) lehetővé teszi egy Data Factory-folyamat számára egy másik folyamat meghívását.

### <a name="delta-flows"></a>Változási folyamatok
A fő használati eset az ETL-mintákban a „változásbetöltések”, vagy csak az olyan adatok betöltése, amelyek módosultak a folyamat utolsó ismétlése óta. A 2. verzió új képességei, például a [keresési tevékenység](control-flow-lookup-activity.md), a rugalmas ütemezés vagy az átvitelvezérlés természetes módon teszik lehetővé ezt a használati esetet. Részletes utasításokat tartalmazó oktatóanyagért lásd: [Oktatóanyag: Növekményes másolat](tutorial-incremental-copy-powershell.md).

### <a name="other-control-flow-activities"></a>Egyéb átvitelvezérlési tevékenységek
Az alábbiakban néhány további, a Data Factory V2 által támogatott átvitelvezérlési tevékenység található: 

Vezérlési tevékenység | Leírás
---------------- | -----------
[ForEach tevékenység](control-flow-for-each-activity.md) | A ForEach tevékenység ismétlődő átvitelvezérlést határoz meg a folyamatban. Ez a tevékenység egy gyűjtemény megismétlésére, valamint egy megadott ciklustevékenység végrehajtására szolgál. E tevékenység ciklusos megvalósítása hasonló a Foreach ciklusos szerkezetéhez a programozási nyelvek esetében.
[Webes tevékenység](control-flow-web-activity.md) | A webes tevékenység segítségével meghívható egy egyéni REST-végpont egy Data Factory-folyamatból. Az adatkészleteket és a társított szolgáltatásokat továbbíthatja a tevékenység számára felhasználásra vagy elérés céljára. 
[Keresési tevékenység](control-flow-lookup-activity.md) | A Keresési tevékenység segítségével bármely külső forrásból kiolvashat vagy megkereshet egy rekordot, táblanevet vagy értéket. Erre a kimenetre a későbbi tevékenységek is hivatkozhatnak. 
[Metaadatok beolvasása tevékenység](control-flow-get-metadata-activity.md) | A metaadatok beolvasása tevékenység segítségével az Azure Data Factory bármely metaadatát lekérheti. 
[Wait tevékenység](control-flow-wait-activity.md) | A folyamat a megadott időtartam elteltéig várakozik (szünetel).

## <a name="deploy-ssis-packages-to-azure"></a>SSIS-csomagok üzembe helyezése az Azure-ban 
Ha át szeretné helyezni az SQL Server Integration Services (SSIS) számítási feladatokat a felhőbe, hozzon létre egy 2. verziós Data Factoryt, és helyezzen üzembe ott egy Azure-SSIS integrációs modult. Az Azure-SSIS integrációs modul egy, az SSIS-csomagok felhőben történő futtatására kijelölt Azure-beli virtuális gépekből (csomópontokból) álló, teljesen felügyelt fürt. Miután üzembe helyezte az Azure-SSIS integrációs modult, ugyanazokat az eszközöket használhatja, amelyeket az SSIS-csomagok helyszíni SSIS-környezetben való üzembe helyezéséhez használt. Például az SQL Server Data Tools (SSDT) vagy az SQL Server Management Studio (SSMS) használatával üzembe helyezhet SSIS-csomagokat ebben az Azure-beli modulban. Lépésenkénti útmutatásért lásd a következő oktatóanyagot: [SQL Server Integration Services-csomagok üzembe helyezése az Azure-ban](tutorial-deploy-ssis-packages-azure.md). 

## <a name="flexible-scheduling"></a>Rugalmas ütemezés
A Data Factory V2-ben nem kell megadnia az adatkészletek rendelkezésre állásának ütemezését. Megadhat egy eseményindító erőforrást, amely folyamatokat ütemezhet egy óraütemező paradigmából. Paramétereket is átadhat a folyamatoknak egy eseményindítóból egy rugalmas ütemezési/végrehajtási modell érdekében. A Data Factory V2-ben a folyamatok nem rendelkeznek végrehajtási időszakokkal. A Data Factory V1-ben található startTime, endTime és isPaused fogalmak már nem találhatók meg a Data Factory V2-ben. Ebben a cikkben megtekintheti, hogyan hozhat létre, majd ütemezhet egy folyamatot a Data Factory V2-ben: [Folyamat-végrehajtás és eseményindítók](concepts-pipeline-execution-triggers.md).

## <a name="support-for-more-data-stores"></a>További adattárak támogatása
A V2 a V1-nél több adattárba vagy adattárból történő másolást támogat. A támogatott adattárak listáját az alábbi cikkekben találja:

- [V1 – támogatott adattárak](v1/data-factory-data-movement-activities.md#supported-data-stores-and-formats)
- [V2 – támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats)

## <a name="support-for-on-demand-spark-cluster"></a>Igény szerinti Spark-fürt támogatása
A V2 támogatja az igény szerinti HDInsight-fürt létrehozását. Igény szerinti Spark-fürt létrehozásához Sparkként határozza meg a fürt típusát az igény szerinti HDInsight társított szolgáltatás meghatározásában. Ezután úgy konfigurálhatja a Spark-tevékenységet a folyamatban, hogy ezt a társított szolgáltatást használja. A tevékenység futásidőben való végrehajtásakor a Data Factory szolgáltatás automatikusan létrehozza a Spark-fürtöt. További információkért tekintse át a következő cikkeket:

- [Spark-tevékenység a V2-ben](transform-data-using-spark.md)
- [Azure HDInsight igény szerinti társított szolgáltatás](compute-linked-services.md#azure-hdinsight-on-demand-linked-service)

## <a name="custom-activities"></a>Egyéni tevékenységek
A V1-ben úgy valósíthat meg (Egyéni) DotNet tevékenységi kódokat, hogy létrehoz egy .Net-osztálytár projektek egy olyan osztállyal, amely megvalósítja az IDotNetActivity felület Végrehajtási metódusát. Ezért az egyéni kódot .NET Framework 4.5.2-ben kell megírni és Windows-alapú Azure Batch Pool-csomópontokon kell végrehajtani. 

A V2 Egyéni tevékenységei esetében nem kell .Net felületet megvalósítani. Közvetlenül futtathat parancsokat és szkripteket, valamint végrehajtható fájlként futtathatja összesített egyéni kódjait. 

További információ: [Az egyéni tevékenységek közötti különbségek a V1-ben és a V2-ben](transform-data-using-dotnet-custom-activity.md#difference-between-custom-activity-in-azure-data-factory-v2-and-custom-dotnet-activity-in-azure-data-factory-v1).

## <a name="sdks"></a>SDK-k
A Data Factory 2. verziója SDK-k szélesebb választékát biztosítja, amelyeket folyamatok létrehozásához, kezeléséhez és monitorozásához használhat.

- **.NET SDK**: A .NET SDK frissült a V2-ben. 
- **PowerShell**: A PowerShell-parancsmagok frissültek a V2-ben. A V2 parancsmagjainak nevében szerepel a **DataFactoryV2** tag. Például: Get-AzureRmDataFactoryV2. 
- **Python SDK**: Ez az SDK a V2-ben lett bevezetve.
- **REST API**: A REST API frissült a V2-ben. 

A V2-ben frissített SDK-k visszamenőlegesen nem kompatibilisek az 1. verzió ügyfeleivel. 

## <a name="authoring-experience"></a>Létrehozási feladatok
A Data Factory V1 az Azure Portal Data Factory Editor eszközének használatával teszi lehetővé folyamatok létrehozását. A Data Factory V2 jelenleg a data factoryk létrehozásának csak a programozott módon (.NET SDK, REST API, PowerShell, Python stb.) történő létrehozását támogatja. A felhasználói felület még nem támogatott.  A Data Factory V1 szintén támogatja az SDK, REST és PowerShell létrehozási támogatást.

## <a name="monitoring-experience"></a>Monitorozási feladatok
A V2-ben a data factorykat az [Azure Monitor](monitor-using-azure-monitor.md) segítségével is monitorozhatja. Az új PowerShell-parancsmagok támogatják az [integrációs modulok](monitor-integration-runtime.md) monitorozását. A V1 és a V2 is támogatja a vizuális monitorozást a monitorozási alkalmazáson keresztül, amely az Azure Portalról indítható el.


## <a name="next-steps"></a>Következő lépések
A következő gyors útmutatók részletes utasításait végrehajtva megismerkedhet az adat-előállítók létrehozásának módjával: [PowerShell](quickstart-create-data-factory-powershell.md), [.NET](quickstart-create-data-factory-dot-net.md), [Python](quickstart-create-data-factory-python.md), [REST API](quickstart-create-data-factory-rest-api.md). 
