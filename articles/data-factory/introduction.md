---
title: Bevezetés az Azure Data Factory használatába | Microsoft Docs
description: 'A témakör ismerteti, hogy mi is az Azure Data Factory: egy felhőalapú adatintegrációs szolgáltatás, amellyel előkészíthető és automatizálható az adatok továbbítása és átalakítása.'
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/11/2018
ms.author: shlo
ms.openlocfilehash: ae0247df0bbde07e755bd910bca08b8703969cec
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37047275"
---
# <a name="introduction-to-azure-data-factory"></a>Az Azure Data Factory bemutatása 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verzió](v1/data-factory-introduction.md)
> * [Aktuális verzió](introduction.md)

A big data világában a nyers, rendezetlen adatok tárolása leggyakrabban relációs, nem relációs vagy egyéb tárolórendszerekben történik. A nyers adatok önmagukban azonban nem biztosítanak megfelelő környezetet, sem értelmezhető elemzési lehetőségeket az elemzők, adatelemzők vagy üzleti döntéshozók számára. 

A big data esetében olyan szolgáltatásra van szükség, amely lehetővé teszi az irányítási és üzembe helyezési folyamatok számára, hogy ezt a hatalmas nyers adatmennyiséget a gyakorlatban használható üzleti elemzésekké alakítsák. Az Azure Data Factory egy ilyen, az összetett hibrid ETL-, ELT- és adatintegrációs projektek kezelésre szolgáló felügyelt felhőszolgáltatás.

Képzeljünk el például egy játékokkal foglalkozó vállalatot, amely több petabájtnyi, a játékok által készített naplót gyűjt össze a felhőben. A vállalat e naplókat szeretné elemezni, hogy betekintést nyerhessen az ügyfelek preferenciáiba, demográfiai adataiba és felhasználói viselkedésébe, hogy ezek alapján azonosítsa az értékesítési és keresztértékesítési lehetőségeket, új funkciókat fejlesszen az üzleti növekedés elősegítése érdekében, és jobb felhasználói élményt nyújtson az ügyfeleinek.

A naplók elemzéséhez a vállalatnak a helyszíni adattárban tárolt referenciaadatokat kell felhasználnia, mint például az ügyféladatokat, a játékadatokat és a reklámkampány-adatokat. A vállalat úgy kívánja hasznosítani ezeket a helyszíni adattárakból származó adatokat, hogy azokat további, a felhőalapú adattárban lévő naplóadatokkal kombinálja. 

Az elemzések előállításához a vállalat egy felhőalapú Spark-fürt (Azure HDInsight) használatával szeretné feldolgozni az egyesített adatokat, majd pedig egy felhőbeli adattárházban (például az Azure SQL Data Warehouse-ban) kívánja közzétenni az átalakított adatokat a jelentéskészítés megkönnyítése érdekében. A vállalat automatizálni, illetve napi rendszerességgel monitorozni és kezelni kívánja ezt a munkafolyamatot. Ezen kívül végre is kívánja hajtani azt, ha fájlok kerülnek egy blobtárolóba.

Az Azure Data Factory az a platform, amely az ilyen adatforgatókönyvek esetében sikeresen használható. Ez egy *felhőalapú adatintegrációs szolgáltatás, amely lehetővé teszi olyan, a felhőben futó, adatvezérelt munkafolyamatok létrehozását, amelyek irányítják és automatizálják az adatok átvitelét és átalakítását*. Az Azure Data Factory segítségével létrehozhatók és ütemezhetők a különböző adattárolókból adatokat beolvasó adatvezérelt munkafolyamatok, feldolgozhatók és átalakíthatók az adatok különböző számítási szolgáltatások használatával (például Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics és Azure Machine Learning), 

továbbá a kimeneti adatok közzétehetők olyan adattárakban, mint az Azure SQL Data Warehouse, ahonnan az üzleti intelligenciára épülő (BI-) alkalmazások felhasználhatják őket. Végső soron az Azure Data Factory segítségével a nyers adatokat használható adattárakba rendezhetjük, így jobb üzleti döntéseket hozhatunk.

![A Data Factory felső szintű nézete](media/introduction/big-picture.png)

## <a name="how-does-it-work"></a>Hogyan működik?
Az egyes folyamatok (adatvezérelt munkafolyamatok) az Azure Data Factoryben általában a következő négy lépést hajtják végre:

![Az adatvezérelt munkafolyamatok négy lépése](media/introduction/four-steps-of-a-workflow.png)

### <a name="connect-and-collect"></a>Csatlakozás és összegyűjtés

A vállalatok a legkülönfélébb adatokkal rendelkeznek a legkülönfélébb (helyszíni, felhőbeli, strukturált, strukturálatlan és részben strukturált) forrásokból, amelyek ráadásul eltérő időközzel és sebességgel érkeznek be. 

Az információ-előállítási rendszerek kiépítésének első lépése az összes szükséges adatforrás és feldolgozó, például az SaaS-szolgáltatások, az adatbázisok, a fájlmegosztások és az FTP-alapú webszolgáltatások összekapcsolása. A következő lépés az adatok igényalapú átmozgatása egy központi helyre a további feldolgozás előtt.

A Data Factory nélkül a vállalatoknak egyéni adattovábbítási összetevőket kell készíteniük vagy egyéni szolgáltatásokat kell írniuk az adatforrások és feldolgozók integrálására. Az ilyen rendszerek költségesek, nehezen integrálhatóak és tarthatók karban. Továbbá gyakran nem áll rendelkezésre az a vállalati szintű monitorozási, riasztási és vezérlési funkcionalitás, amelyet egy teljes mértékben felügyelt szolgáltatás biztosítani képes.

A Data Factory segítségével a [Másolási tevékenység](copy-activity-overview.md) keretében az adatok egyazon adatfolyamatban helyszíni és felhőalapú forrásadattárakból egyaránt továbbíthatóak egy, a felhőben lévő adattárba további elemzésre. Begyűjtheti például az adatokat egy Azure Data Lake Store-tárolóból, és később átalakíthatja őket egy Azure Data Lake Analytics számítási szolgáltatás használatával. Esetleg begyűjtheti az adatokat egy Azure Blob Storage tárolóból, és később átalakíthatja azokat egy Azure HDInsight Hadoop-fürt használatával.

### <a name="transform-and-enrich"></a>Átalakítás és bővítés
Ha az adatok már jelen vannak egy központi adattárban a felhőben, akkor olyan számítási szolgáltatásokkal dolgozhatók fel és alakíthatók át, mint a HDInsight Hadoop, a Spark, a Data Lake Analytics és a Machine Learning. Az átalakított adatok megbízhatóan állíthatók elő egy fenntartható és szabályozható séma szerint, az éles környezetek megbízható adatokkal való kiszolgálása érdekében.

### <a name="publish"></a>Közzététel
Miután a nyers adatok át lettek alakítva üzleti használatra kész formába, feltöltheti azokat az Azure Data Warehouse-ba, az Azure SQL Database-be, az Azure CosmosDB-be vagy bármely olyan elemzési motorra, amelyre üzleti felhasználói intelligens üzleti eszközeikkel rámutathatnak.

### <a name="monitor"></a>Figyelés
Miután sikeresen kiépítette és üzembe helyezte az adatintegrációs folyamatot, amely üzleti értéket állít elő a feldolgozott adatokból, kövesse figyelemmel az ütemezett tevékenységek és folyamatok sikerességi arányát. Az Azure Data Factory beépített folyamatmonitorozási támogatással rendelkezik az Azure Monitor, az API, a PowerShell, a Log Analytics, valamint az Azure Portal állapotpaneljei révén.

## <a name="top-level-concepts"></a>Legfelső szintű fogalmak
Az Azure-előfizetések több Azure Data Factory-példányt (más néven adat-előállítókat) is tartalmazhatnak. Az Azure Data Factory négy fő összetevőből áll. Ezek együtt alkotják azt a platformot, amelyen létrehozhatók olyan adatvezérelt munkafolyamatok, amelyeknek a lépései áthelyezik és átalakítják az adatokat.

### <a name="pipeline"></a>Folyamat
Az adat-előállító egy vagy több folyamattal rendelkezhet. A folyamatok a tevékenységek logikus csoportosításai egy adott munkaegység végrehajtásához. A folyamatban lévő tevékenységek együtt egy feladatot hajtanak végre. Például a folyamat tartalmazhat egy csoportnyi műveletet, amelyek adatokat fogadnak egy Azure-blobból, majd egy Hive-lekérdezést futtatnak egy HDInsight-fürtön az adatok particionálásához. 

A folyamatok használatának az az előnye, hogy így a tevékenységek egy készletben kezelhetők, nem pedig külön-külön. Egy adott folyamat tevékenységei összefűzhetők, így azok egymás után vagy egymástól függetlenül, párhuzamosan üzemeltethetők.

### <a name="activity"></a>Tevékenység
Egy folyamatban a tevékenységek a feldolgozási lépéseket jelentik. A másolási tevékenység használatával például az egyik adattárból a másikba másolhatja az adatokat. Hasonlóképpen, egy Hive-tevékenység használatával Hive-lekérdezést futtathat egy Azure HDInsight-fürtön az adatok átalakításához és elemzéséhez. A Data Factory három típusú tevékenységet támogat: az adattovábbítási tevékenységeket, az adat-átalakítási tevékenységeket és a vezérlési tevékenységeket.

### <a name="datasets"></a>Adathalmazok
Az adatkészletek adatstruktúrákat jelölnek az adattárakon belül, amelyek egyszerűen rámutatnak vagy meghivatkozzák az adatokat, amelyeket a tevékenységekben be- vagy kimenetként használni szeretne. 

### <a name="linked-services"></a>Társított szolgáltatások
A társított szolgáltatások nagyon hasonlóak a kapcsolati sztringekhoz, amelyek meghatározzák azokat a kapcsolati információkat, amelyeket a Data Factory a külső erőforrásokhoz történő csatlakozáshoz igényel. Tulajdonképpen a társított szolgáltatás határozza meg az adatforrással való kapcsolatot, míg az adatkészlet jelöli az adatok struktúráját. Az Azure Storage társított szolgáltatása például kapcsolati sztringet szolgáltat az Azure Storage-fiókhoz való csatlakozáshoz. Az Azure Blob-adatkészlet pedig meghatározza a blobtárolót és az adatokat tartalmazó mappát.

A társított szolgáltatásokat két célból használjuk a Data Factoryban:

- Egy **adattár**, például egy helyszíni SQL Server-adatbázis, Oracle-adatbázis, fájlmegosztás vagy egy Azure Blob Storage-fiók jelölésére. A támogatott adattárak listáját lásd a [másolási tevékenység](copy-activity-overview.md) cikkben.

- Olyan **számítási erőforrás** jelölésére, amelyen végrehajtható a tevékenység. A HDInsightHive-tevékenység végrehajtása például egy HDInsight Hadoop-fürtön történik. Az átalakítási tevékenységek és a támogatott számítási környezetek listáját lásd az [adatok átalakítása](transform-data.md) cikkben.

### <a name="triggers"></a>Eseményindítók
Az eseményindítók olyan feldolgozási egységek, amelyek meghatározzák, hogy mikor kezdődjön egy folyamat végrehajtása. A különböző típusú eseményekhez eltérő típusú eseményindítók tartoznak.

### <a name="pipeline-runs"></a>Folyamatfuttatások
A folyamatfuttatások a folyamat-végrehajtás egy-egy példányát jelentik. A folyamatfuttatások példányosítása jellemzően az argumentumoknak a folyamatokban meghatározott paraméterekhez történő továbbításával történik. Az argumentumok továbbítása történhet manuálisan vagy az eseményindító meghatározásán keresztül is.

### <a name="parameters"></a>Paraméterek
A paraméterek írásvédett konfigurációk kulcs-érték párjainak tekintendők.  A paraméterek meghatározása a folyamatban történik. Az e paraméterekhez tartozó argumentumok továbbítása a végrehajtás során történik a futtatási környezetből, amelyet egy eseményindító vagy egy manuálisan végrehajtott folyamat hoz létre. A folyamatok tevékenységei a paraméterértékeket dolgozzák fel.

Az adatkészletek szigorúan típusos paraméterek és újrahasznosítható/hivatkozható entitások együttesei. A tevékenységek hivatkozhatnak adatkészletekre, és képesek az adatkészlet-meghatározásban megadott tulajdonságok feldolgozására.

A társított szolgáltatások szintén adattárak vagy számítási környezetek kapcsolatadatait tartalmazó, szigorúan típusos paraméterek. Ezenkívül újrahasznosítható/hivatkozható entitások.

### <a name="control-flow"></a>Átvitelvezérlés
Átvitelvezérlés: folyamattevékenységek vezénylése, például egy sorozat láncolási tevékenységei, elágaztatás és olyan paraméterek, amelyek folyamatszinten határozhatók meg, valamint olyan argumentumok, amelyek a folyamat igény szerinti meghívása során vagy egy eseményindítóból továbbítódnak. Ilyen tevékenységek az egyéniállapot-átadás és a tárolók hurkolása, vagyis a For-each iterátorok is.


A Data Factory alapelveivel kapcsolatban további információkat a következő cikkekben talál:

- [Adatkészletek és társított szolgáltatások](concepts-datasets-linked-services.md)
- [Folyamatok és tevékenységek](concepts-pipelines-activities.md)
- [Integrációs modul](concepts-integration-runtime.md)

## <a name="supported-regions"></a>Támogatott régiók

Jelenleg az USA keleti régiójában, az USA 2. keleti régiójában és a nyugat-európai régióban hozhat létre data factoryt. A data factory azonban más Azure-régiókban lévő adattárakhoz és számítási szolgáltatásokhoz is hozzáférhet az adatok adattárak közötti mozgatása vagy az adatok számítási szolgáltatásokkal történő feldolgozása érdekében.

Maga az Azure Data Factory nem tárol adatokat. Lehetővé teszi viszont olyan adatvezérelt munkafolyamatok létrehozását, amelyekkel előkészíthető a támogatott adattárak közötti adatmozgás és az adatok számítási szolgáltatásokkal történő feldolgozása más régiókban, illetve helyszíni környezetben. Lehetővé teszi továbbá a munkafolyamatok monitorozását és kezelését mind szoftveres, mind pedig felhasználói felületi mechanizmusokkal.

Bár a Data Factory csak az USA keleti régiójában, az USA 2. keleti régiójában, illetve a nyugat-európai régióban érhető el, az adatok Data Factoryval történő áthelyezését biztosító szolgáltatás globálisan számos régióban elérhető. Ha az adattár tűzfal mögött található, akkor a helyszíni környezetben telepített helyi Integration Runtime végzi az adatok áthelyezését.

Tegyük fel például, hogy számítási környezetei, mint például az Azure HDInsight-fürt és az Azure Machine Learning a nyugat-európai régión kívül futnak. Létrehozhat egy Azure Data Factory-példányt az USA keleti vagy 2. keleti régiójában, és felhasználhatja a Nyugat-Európában lévő számítási környezetein futtatott feladatok ütemezéséhez. A Data Factory néhány ezredmásodperc alatt aktiválja a feladatot a számítási környezetben, a feladatnak a számítási környezetben való futtatásához szükséges idő viszont nem változik.

## <a name="compare-with-version-1"></a>Összehasonlítás az 1-es verzióval
A Data Factory szolgáltatás 1-es verziója és aktuális verziója közötti különbségek listájáért lásd: [Összehasonlítás az 1-es verzióval](compare-versions.md). 

## <a name="next-steps"></a>További lépések
Bevezetés a Data Factory-folyamatok létrehozásába az alábbi eszközök/SDK-k valamelyikével: 

- [Data Factory felhasználói felülete az Azure Portalon](quickstart-create-data-factory-portal.md)
- [Adatok másolása eszköz az Azure Portalon](quickstart-create-data-factory-copy-data-tool.md)
- [PowerShell](quickstart-create-data-factory-powershell.md)
- [.NET](quickstart-create-data-factory-dot-net.md)
- [Python](quickstart-create-data-factory-python.md)
- [REST](quickstart-create-data-factory-rest-api.md)
- [Azure Resource Manager-sablon](quickstart-create-data-factory-resource-manager-template.md)
 
