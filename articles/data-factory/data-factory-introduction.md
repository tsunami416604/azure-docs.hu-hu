<properties 
    pageTitle="A Data Factory nevű adatintegrációs szolgáltatás ismertetése | Microsoft Azure" 
    description="A témakör ismerteti, hogy mi is az Azure Data Factory: egy felhőalapú adatintegrációs szolgáltatás, amellyel előkészíthető és automatizálható az adatok továbbítása és átalakítása." 
    keywords="adatintegrálás, felhőalapú adatintegráció, mi az az azure data factory"
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="09/08/2016" 
    ms.author="spelluru"/>


# Az Azure Data Factory, egy adatintegrációs felhőalapú szolgáltatás ismertetése

## Mi az az Azure Data Factory? 
A Data Factory egy felhőalapú adatintegrációs szolgáltatás, amellyel előkészíthető és automatizálható az adatok továbbítása és átalakítása. Egy gyártóüzemhez hasonlóan, amely berendezések működtetésével átalakítja a nyersanyagokat késztermékekké, a Data Factory a nyers adatokat összegyűjtő meglévő szolgáltatásokat hangolja össze az adatok azonnal felhasználható információkká történő átalakítására. 

A Data Factory a helyszíni és felhőbeli adatforrások, valamint a szolgáltatottszoftver-megoldások esetén is felhasználható az adatok fogadására, előkészítésére, átalakítására, elemzésére és közzétételére. A Data Factory segítségével a szolgáltatásokat felügyelt adatfolyamatokká állíthatja össze, és átalakíthatja az adatokat például az [Azure HDInsight (Hadoop)](http://azure.microsoft.com/documentation/services/hdinsight/) és az [Azure Batch](https://azure.microsoft.com/documentation/services/batch/) segítségével a Big Data-számítási igények kielégítéséhez, vagy az [Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/) használatával az elemzési megoldások működtetéséhez. Az egyszerű, táblázatos figyelési nézeten túlmutatva a Data Factory látványos vizualizációival gyorsan megjeleníthetők a folyamatok közötti leszármaztatások és függőségek. Egyetlen egyesített nézetben figyelheti az adatfolyamatokat, így könnyen kiszűrheti a problémákat, és beállíthatja a figyelési riasztásokat.

![Ábra: Data Factory áttekintése, adatintegrációs szolgáltatás](./media/data-factory-introduction/what-is-azure-data-factory.png)

**1. ábra** Számos különböző helyszíni adatforrásból gyűjtheti össze az adatokat, ezeket betöltheti, előkészítheti, átalakíthatja és elemezheti, majd használatra kész adatokat tehet közzé.

A Data Factoryt bármikor igénybe veheti, amikor különböző típusú és méretű adatokat kell összegyűjtenie, átalakítania és közzétennie a hatékonyabb elemzések érdekében – mindezt kiszámítható ütemezéssel. A Data Factory segítségével magas rendelkezésre állású adatfolyam-folyamatok készíthetők különféle esetekben és iparágakban, az elemzési folyamatok iránti igények kielégítése érdekében. Az online viszonteladók személyre szabott [termékajánlatokat](data-factory-product-reco-usecase.md) készíthetnek a szolgáltatással az ügyfelek böngészési szokásai alapján. A játékfejlesztők felmérhetik [marketingkampányaik hatékonyságát](data-factory-customer-profiling-usecase.md). Az [Ügyfél-esettanulmányok](data-factory-customer-case-studies.md) áttekintésével közvetlenül az ügyfeleinktől tudhatja meg, hogyan és miért használják a Data Factoryt. 

> [AZURE.VIDEO azure-data-factory-overview]

## Fő fogalmak

A Data Factory néhány főbb entitásának együttes használatával meghatározhatók a bemeneti és kimeneti adatok, a feldolgozási események, valamint a kívánt adatfeldolgozási folyamat végrehajtásához szükséges ütemezés és erőforrások.

![Ábra: Data Factory áttekintése, felhőalapú adatintegrációs szolgáltatás – fő fogalmak](./media/data-factory-introduction/data-integration-service-key-concepts.png)

**2. ábra** Az adatkészlet, a tevékenység, a folyamat és a társított szolgáltatás közötti kapcsolatok

### Folyamatok
A [folyamatok](data-factory-create-pipelines.md) a tevékenységek logikus csoportosításai. A használatukkal a tevékenységek egységgé csoportosíthatók, amelyben együttesen hajtanak végre egy feladatot. Átalakítási tevékenységek sorozatára lehet például szükség a naplófájlok adatainak megtisztításához. Ez a sorozat összetett ütemezéssel és függőségekkel rendelkezhet, amelyeket elő kell készíteni és automatizálni kell. Az összes ilyen tevékenység csoportosítható egyetlen „CleanLogFiles” elnevezésű folyamatba. A „CleanLogFiles” folyamat ezután egyetlen egységként üzembe helyezhető, ütemezhető vagy törölhető ahelyett, hogy minden egyes tevékenységet külön-külön kellene kezelni.

### Tevékenységek
A tevékenységek meghatározzák az adatokon végrehajtandó műveleteket. Minden tevékenység nulla vagy több [adatkészletet](data-factory-create-datasets.md) fogad bemenetként, és egy vagy több adatkészletet állít elő kimenetként. A tevékenység a vezénylés egysége az Azure Data Factoryban. A [Másolási tevékenység](data-factory-data-movement-activities.md) használatával például vezényelheti az adatok egyik adatkészletből a másikba történő másolását. Egy [Hive-tevékenység](data-factory-data-transformation-activities.md) használatával hasonlóképpen Hive-lekérdezést futtathat egy Azure HDInsight-fürtön az adatok átalakításához és elemzéséhez. Az Azure Data Factory az adatátalakítási, -elemzési és -továbbítási tevékenységek széles skáláját kínálja. 

### Adathalmazok
Az [adatkészletek](data-factory-create-datasets.md) elnevezett hivatkozások/mutatók azokra az adatokra, amelyeket a tevékenység bemeneteként vagy kimeneteként kíván használni. Az adatkészletek adatszerkezeteket határoznak meg a különböző adattárakban, többek között a táblákban, fájlokban, mappákban és dokumentumokban.

### Társított szolgáltatások
A társított szolgáltatások meghatározzák azokat az információkat, amelyeket a Data Factory a külső erőforrásokhoz történő csatlakozáshoz igényel. A társított szolgáltatásokat két célból használjuk a Data Factoryban:

- Egy adattár, többek között például egy helyszíni SQL Server, Oracle DB, fájlmegosztás vagy egy Azure Blob Storage-fiók jelölésére. Mint már említettük, az adathalmazok a Data Factoryhoz a társított szolgáltatásokkal csatlakoztatott adattárakon belüli adatszerkezeteket jelölik.
- Olyan számítási erőforrás jelölésére, amelyen végrehajtható a tevékenység. A „HDInsightHive-tevékenység” végrehajtása például egy HDInsight Hadoop-fürtön történik.

Az adatbázis, a tevékenység, a folyamat és a társított szolgáltatás négy egyszerű fogalmának ismeretében készen áll a szolgáltatás használatának megkezdésére. [Létrehozhatja első folyamatát](data-factory-build-your-first-pipeline.md), vagy üzembe helyezhet egy előre elkészített mintát a [Data Factory-mintákat](data-factory-samples.md) ismertető cikk utasításai alapján. 

## Támogatott régiók
Jelenleg az **USA nyugati régiójában**, az **USA keleti régiójában** és az **észak-európai** régióban hozhat létre data factoryt. A data factory azonban más Azure-régiókban lévő adattárakhoz és számítási szolgáltatásokhoz is hozzáférhet az adatok adattárak közötti mozgatása vagy az adatok számítási szolgáltatásokkal történő feldolgozása érdekében. 

Maga az Azure Data Factory nem tárol adatokat. Lehetővé teszi viszont olyan adatvezérelt munkafolyamatok létrehozását, amelyekkel előkészíthető a [támogatott adattárak](data-factory-data-movement-activities.md#supported-data-stores) közötti adatmozgás és az adatok [számítási szolgáltatásokkal](data-factory-compute-linked-services.md) történő feldolgozása más régiókban, illetve helyszíni környezetben. Lehetővé teszi továbbá a [munkafolyamatok figyelését és kezelését](data-factory-monitor-manage-pipelines.md) mind szoftveres, mind pedig felhasználói felületi mechanizmusokkal. 

Bár az Azure Data Factory csak az **USA nyugati régiójában**, az **USA keleti régiójában** és az **észak-európai** régióban érhető el, az adatok Data Factoryval történő áthelyezését biztosító szolgáltatás [globálisan](data-factory-data-movement-activities.md#global) számos régióban elérhető. Ha az adattár tűzfal mögött található, akkor a helyszíni környezetben telepített [adatkezelési átjáró](data-factory-move-data-between-onprem-and-cloud.md) végzi az adatok áthelyezését. 

Tegyük fel például, hogy számítási környezetei, mint például az Azure HDInsight-fürt és az Azure Machine Learning a nyugat-európai régión kívül futnak. Létrehozhat egy Azure Data Factory-példányt Észak-Európában, és felhasználhatja a Nyugat-Európában lévő számítási környezetein futtatott feladatok ütemezéséhez. A Data Factory néhány ezredmásodperc alatt aktiválja a feladatot a számítási környezetben, a feladatnak a számítási környezetben való futtatásához szükséges idő viszont nem változik.

A későbbiekben szeretnénk minden olyan régióra kiterjeszteni az Azure Data Factoryt, ahol támogatott az Azure használata.
  
## Következő lépések
A következő oktatóanyagok részletes utasításait követve megtudhatja, hogyan építhet ki adatfolyamatokkal rendelkező data factorykat. 

Oktatóanyag | Leírás
-------- | -----------
[Az adatokat Hadoop-fürttel feldolgozó adatfolyamat kiépítése](data-factory-build-your-first-pipeline.md) | Az oktatóanyag során kiépíti az első Azure data factoryját egy olyan adatfolyamattal, amely egy Azure HDInsight (Hadoop) fürtön futtatott Hive-parancsprogrammal **dolgozza fel az adatokat**. |
[Adatfolyamat kiépítése két felhőalapú adattár közötti adatáthelyezéshez](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) | Ebben az oktatóanyagban olyan folyamattal rendelkező data factoryt hoz létre, amely Blob Storage-ból SQL-adatbázisba **helyez át adatokat**.
[Adatfolyamat kiépítése egy helyszíni és egy felhőalapú adattár közötti adatáthelyezéshez adatkezelési átjáró segítségével](data-factory-move-data-between-onprem-and-cloud.md) | Ebben az oktatóanyagban olyan folyamattal rendelkező data factoryt épít ki, amely egy **helyszíni** SQL Server-adatbázisból Azure-blobba **helyez át adatokat**. A bemutató részeként telepíti és konfigurálja az adatkezelési átjárót a gépen. 


<!--HONumber=Sep16_HO3-->


