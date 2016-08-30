<properties 
    pageTitle="Az Azure Data Factory bemutatása" 
    description="Megtudhatja, hogyan állíthat össze az Azure Data Factory szolgáltatás használatával adatfeldolgozási, adattárolási és adattovábbítási szolgáltatásokat olyan folyamatok létrehozásához, amelyek megbízható információkat állítanak elő." 
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
    ms.date="04/26/2016" 
    ms.author="spelluru"/>

# Az Azure Data Factory szolgáltatás bemutatása

## Áttekintés
A Data Factory egy felhőalapú adatintegrációs szolgáltatás, amellyel előkészíthető és automatizálható az adatok továbbítása és átalakítása. Egy gyártóüzemhez hasonlóan, amely berendezések működtetésével átalakítja a nyersanyagokat késztermékekké, a Data Factory a nyers adatokat összegyűjtő meglévő szolgáltatásokat hangolja össze az adatok azonnal felhasználható információkká történő átalakítására. 

A Data Factory a helyszíni és felhőbeli adatforrások, valamint a szolgáltatottszoftver-megoldások esetén is felhasználható az adatok fogadására, előkészítésére, átalakítására, elemzésére és közzétételére.  A Data Factory használatával a szolgáltatásokat felügyelt adatfolyam-folyamatokká állíthatja össze, és átalakítja az adatokat például az [Azure HDInsight (Hadoop)](http://azure.microsoft.com/documentation/services/hdinsight/) és az [Azure Batch](https://azure.microsoft.com/documentation/services/batch/) használatával a Big Data-számítási igények kielégítéséhez, vagy az [Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/) használatával az elemzési megoldások működtetéséhez.  Az egyszerű, táblázatos figyelési nézeten túlmutatva a Data Factory látványos vizualizációival gyorsan megjeleníthetők a folyamatok közötti leszármaztatások és függőségek. Egyetlen, egységesített nézetben figyelheti az összes adatfolyam-folyamatát, így egyszerűen kiszűrheti a problémákat és beállíthatja a figyelési riasztásokat.

![Áttekintés](./media/data-factory-introduction/data-factory-overview.png)

**1. ábra** Számos különböző helyszíni adatforrásból gyűjtheti össze az adatokat, amelyeket feldolgozhat és előkészíthet, különféle átalakításokkal rendszerezhet és elemezhet, majd azonnal felhasználható adatokat tehet közzé.

A Data Factoryt bármikor igénybe veheti, amikor különböző típusú és méretű adatokat kell összegyűjtenie, átalakítania és közzétennie a hatékonyabb elemzések érdekében – mindezt kiszámítható ütemezéssel. A Data Factory segítségével magas rendelkezésre állású adatfolyam-folyamatok készíthetők különféle esetekben és iparágakban, az elemzési folyamatok iránti igények kielégítése érdekében.  Az online viszonteladók személyre szabott [termékajánlatokat](data-factory-product-reco-usecase.md) készíthetnek a szolgáltatással az ügyfelek böngészési szokásai alapján. A játékfejlesztők felmérhetik [marketingkampányaik hatékonyságát](data-factory-customer-profiling-usecase.md). Az [Ügyfél-esettanulmányok](data-factory-customer-case-studies.md) áttekintésével közvetlenül az ügyfeleinktől tudhatja meg, hogyan és miért használják a Data Factoryt. 

> [AZURE.VIDEO azure-data-factory-overview]

## Fő fogalmak

Az Azure Data Factory néhány főbb entitásának együttes használatával meghatározhatók a bemeneti és kimeneti adatok, a feldolgozási események, valamint a kívánt adatfolyam végrehajtásához szükséges ütemezés és erőforrások.

![Fő fogalmak](./media/data-factory-introduction/key-concepts.png)

**2. ábra** Az adatkészlet, a tevékenység, a folyamat és a társított szolgáltatás közötti kapcsolatok


### Tevékenységek
A tevékenységek meghatározzák az adatokon végrehajtandó műveleteket. Minden tevékenység nulla vagy több [adatkészletet](data-factory-create-datasets.md) fogad bemenetként, és egy vagy több adatkészletet állít elő kimenetként. A tevékenység a vezénylés egysége az Azure Data Factoryban. A [Másolási tevékenység](data-factory-data-movement-activities.md) használatával például vezényelheti az adatok egyik adatkészletből a másikba történő másolását. Egy [Hive-tevékenység](data-factory-data-transformation-activities.md) használatával hasonlóképpen Hive-lekérdezést futtathat egy Azure HDInsight-fürtön az adatok átalakításához és elemzéséhez. Az Azure Data Factory az adatátalakítási, -elemzési és -továbbítási tevékenységek széles skáláját kínálja. 

### Folyamatok
A [folyamatok](data-factory-create-pipelines.md) a tevékenységek logikus csoportosításai. A használatukkal a tevékenységek egységgé csoportosíthatók, amelyben együttesen hajtanak végre egy feladatot. Átalakítási tevékenységek sorozatára lehet például szükség a naplófájlok adatainak megtisztításához.  Ez a sorozat összetett ütemezéssel és függőségekkel rendelkezhet, amelyeket elő kell készíteni és automatizálni kell. Az összes ilyen tevékenység egyetlen „CleanLogFiles” elnevezésű folyamatba csoportosítható.  A „CleanLogFiles” folyamat ezután egyetlen egységként üzembe helyezhető, ütemezhető vagy törölhető ahelyett, hogy minden egyes tevékenységet külön-külön kellene kezelni.

### Adatkészletek
Az [adatkészletek](data-factory-create-datasets.md) elnevezett hivatkozások/mutatók azokra az adatokra, amelyeket a tevékenység bemeneteként vagy kimeneteként kíván használni. Az adatkészletek adatszerkezeteket határoznak meg a különböző adattárakban, többek között a táblákban, fájlokban, mappákban és dokumentumokban.

### Társított szolgáltatások
A társított szolgáltatások meghatározzák azokat az információkat, amelyeket a Data Factory a külső erőforrásokhoz történő csatlakozáshoz igényel.  A társított szolgáltatásokat két célból használjuk a Data Factoryban:

- Egy adattár, többek között például egy helyszíni SQL Server, Oracle DB, fájlmegosztás vagy egy Azure Blob Storage-fiók jelölésére.   Mint már említettük, az adatkészletek a Data Factoryhoz a társított szolgáltatásokkal csatlakoztatott adattárakon belüli adatszerkezeteket jelölik.
- Olyan számítási erőforrás jelölésére, amelyen végrehajtható a tevékenység.  A „HDInsightHive-tevékenység” végrehajtása például egy HDInsight Hadoop-fürtön történik.

Az adatbázis, a tevékenység, a folyamat és a társított szolgáltatás négy egyszerű fogalmának ismeretében készen áll a szolgáltatás használatának megkezdésére.  [Létrehozhatja első folyamatát](data-factory-build-your-first-pipeline.md) az alapoktól, vagy üzembe helyezhet egy előre elkészített mintát a [Data Factory-mintákat](data-factory-samples.md) ismertető cikk utasításainak követésével. 

## Támogatott régiók
Jelenleg az **USA nyugati régiójában**, az **USA keleti régiójában** és az **észak-európai** régióban hozhat létre data factoryt. A data factory azonban más Azure-régiókban lévő adattárakhoz és számítási szolgáltatásokhoz is hozzáférhet az adatok adattárak közötti mozgatása vagy az adatok számítási szolgáltatásokkal történő feldolgozása érdekében. 

Maga az Azure Data Factory nem tárol adatokat. Lehetővé teszi viszont olyan adatvezérelt munkafolyamatok létrehozását, amelyekkel előkészíthető a [támogatott adattárak](data-factory-data-movement-activities.md#supported-data-stores) közötti adatmozgás és az adatok [számítási szolgáltatásokkal](data-factory-compute-linked-services.md) történő feldolgozása más régiókban, illetve helyszíni környezetben. Lehetővé teszi továbbá a [munkafolyamatok figyelését és kezelését](data-factory-monitor-manage-pipelines.md) mind szoftveres, mind pedig felhasználói felületi mechanizmusokkal. 

Ne feledje, hogy bár az Azure Data Factory csak az **USA nyugati régiójában**, az **USA keleti régiójában** és az **észak-európai** régióban érhető el, az adatok Data Factoryval történő áthelyezését biztosító szolgáltatás [globálisan](data-factory-data-movement-activities.md#global) számos régióban elérhető. Ha az adattár tűzfal mögött található, akkor a helyszíni környezetben telepített [adatkezelési átjáró](data-factory-move-data-between-onprem-and-cloud.md) végzi az adatok áthelyezését. 

Tegyük fel például, hogy számítási környezete(i), mint például az Azure HDInsight-fürt és az Azure Machine Learning a nyugat-európai régión kívül futnak. Létrehozhat egy Azure Data Factory-példányt Észak-Európában, és felhasználhatja a Nyugat-Európában lévő számítási környezetein futtatott feladatok ütemezéséhez. A Data Factory szolgáltatás néhány ezredmásodperc alatt elindítja a feladatot a számítási környezetben, a feladat végrehajtásához viszont ugyanannyi időre van szükség.

A későbbiekben szeretnénk minden olyan régióra kiterjeszteni az Azure Data Factoryt, ahol támogatott az Azure használata.
  




<!--HONumber=jun16_HO2-->


