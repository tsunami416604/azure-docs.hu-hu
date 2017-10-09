---
title: "A Data Factory nevű adatintegrációs szolgáltatás ismertetése | Microsoft Docs"
description: "A témakör ismerteti, hogy mi is az Azure Data Factory: egy felhőalapú adatintegrációs szolgáltatás, amellyel előkészíthető és automatizálható az adatok továbbítása és átalakítása."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: cec68cb5-ca0d-473b-8ae8-35de949a009e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/14/2017
ms.author: shlo
robots: noindex
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 8d4d574dee4b993d8a464482b244e1f63ade8a57
ms.contentlocale: hu-hu
ms.lasthandoff: 09/25/2017

---
# <a name="introduction-to-azure-data-factory"></a>Az Azure Data Factory bemutatása 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1. verzió – Általánosan elérhető](data-factory-introduction.md)
> * [2. verzió – Előzetes verzió](../introduction.md)

> [!NOTE]
> Ez a cikk a Data Factory általánosan elérhető 1. verziójára vonatkozik. Ha a Data Factory szolgáltatás előzetes verzióban elérhető 2. verzióját használja, lásd: [A Data Factory 2. verziójának bemutatása](../introduction.md).


## <a name="what-is-azure-data-factory"></a>Mi az az Azure Data Factory?
A big data világában hogyan aknázhatja ki a vállalkozás a meglévő adatait? Lehetséges a felhőben létrehozott adatokat feldúsítani a helyi vagy más különálló adatforrásokból származó referenciaadatokkal? Például egy játékokkal foglalkozó vállalat rengeteg, a játékok által készített naplót gyűjt össze a felhőben. Ezen naplókat szeretné elemezni, hogy betekintést nyerhessen az ügyfelek preferenciáiba, demográfiai adataiba és felhasználói viselkedésébe, hogy ezek alapján azonosítsa az értékesítési és keresztértékesítési lehetőségeket, új funkciókat fejlesszen az üzleti növekedés elősegítése érdekében, és jobb felhasználói élményt nyújtson az ügyfeleknek. 

A naplók elemzéséhez a vállalatnak a helyszíni adattárban tárolt referenciaadatokat kell felhasználnia, mint például az ügyféladatokat, a játékadatokat és a reklámkampány-adatokat. Ennélfogva a vállalat szeretne hozzáférni a felhőbeli adattárban található naplóadatokhoz és a helyszíni adattárban található referenciaadatokhoz. Ezután feldolgozza az adatokat a Hadoop használatával a felhőben (Azure HDInsight), és az eredményeket közzéteszi egy felhőbeli adattárházban, például egy Azure SQL Data Warehouse-ban, vagy egy helyszíni adattárolóban, például egy SQL Server-kiszolgálón. Ezt a munkafolyamatot hetente egyszer szeretné futtatni. 

Mindehhez egy olyan platformra van szüksége, amellyel létrehozhat egy munkafolyamatot, amely képes kiolvasni az adatokat a helyszíni és a felhőbeli adattárolókból; átalakítani és feldolgozni az adatokat létező számítási szolgáltatások, például a Hadoop használatával; és közzétenni az eredményeket helyszíni vagy felhőbeli adattárolókon a BI-alkalmazások általi felhasználáshoz. 

![Data Factory – áttekintés](media/data-factory-introduction/what-is-azure-data-factory.png) 

Az Azure Data Factory az ilyen helyzetekben használható platform. Ez egy **felhőalapú adatintegrációs szolgáltatás, amely lehetővé teszi olyan, a felhőben futó, adatvezérelt munkafolyamatok létrehozását, amelyek irányítják és automatizálják az adatok átvitelét és átalakítását**. Az Azure Data Factory segítségével létrehozhatók és ütemezhetők a különböző adattárolókból adatokat beolvasó adatvezérelt munkafolyamatok, feldolgozhatók és átalakíthatók az adatok különböző számítási szolgáltatások használatával (pl. Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics és Azure Machine Learning), és a kimeneti adatok közzétehetők olyan adattárakban, mint például az Azure SQL Data Warehouse, ahonnan az üzleti intelligenciára épülő (BI-) alkalmazások felhasználhatják őket.  

Ez inkább egy kinyerési és betöltési (EL), majd egy átalakítási és betöltési (TL) platform, mintsem egy hagyományos kinyerési, átalakítási és betöltési (ETL) platform. Az elvégzett átalakítások célja az adatok számítási szolgáltatások által való átalakítása/feldolgozása, mintsem a származtatott oszlopok hozzáadásához, sorok megszámlálásához vagy adatok rendezéséhez használatos átalakítások elvégzése. 

Jelenleg az Azure Data Factoryben a munkafolyamatok által felhasznált és előállított adatok **időszeletekre osztott adatok** (óránként, naponta, hetente stb.). Például beállítható, hogy egy folyamat naponta egyszer olvasson bemeneti adatokat, dolgozza fel őket, és hozzon létre kimeneti adatokat. A munkafolyamatok egyetlen alkalommal is futtathatók.  
  

## <a name="how-does-it-work"></a>Hogyan működik? 
Az adatvezérelt munkafolyamatok az Azure Data Factoryben általában a következő három lépést hajtják végre:

![Az Azure Data Factory három szakasza](media/data-factory-introduction/three-information-production-stages.png)

### <a name="connect-and-collect"></a>Csatlakozás és összegyűjtés
A vállalatok a legkülönfélébb adatokkal rendelkeznek a legkülönfélébb forrásokból. Az információ-előállítási rendszerek kiépítésének első lépése az összes szükséges adatforrás és feldolgozó, például az SaaS-szolgáltatások, a fájlmegosztások, az FTP-k vagy a webszolgáltatások összekapcsolása, és az adatok igényalapú átmozgatása egy központi helyre a további feldolgozás előtt.

A Data Factory nélkül a vállalatoknak egyéni adattovábbítási összetevőket kell készíteniük vagy egyéni szolgáltatásokat kell írniuk az adatforrások és feldolgozók integrálására. Az ilyen rendszerek költségesek, nehezen integrálhatóak és tarthatók karban, és gyakorta nem áll rendelkezésre az a vállalati szintű felügyeleti, riasztási és vezérlési funkcionalitás, amelyet egy teljes mértékben felügyelt szolgáltatás biztosítani képes.

A Data Factory segítségével a Másolási tevékenység keretében az adatok egyazon adatfolyamatban helyszíni és felhőalapú forrásadattárakból egyaránt továbbíthatóak egy, a felhőben lévő adattárba további elemzésre. Begyűjtheti például az adatokat egy Azure Data Lake Store tárolóból, és később átalakíthatja azokat egy Azure Data Lake Analytics számítási szolgáltatás használatával. Vagy begyűjtheti az adatokat egy Azure Blob Storage tárolóból, és később átalakíthatja azokat egy Azure HDInsight Hadoop-fürt használatával.

### <a name="transform-and-enrich"></a>Átalakítás és bővítés
Ha az adatok már jelen vannak egy központi adattárban a felhőben, akkor olyan számítási szolgáltatásokkal dolgozhatók fel és alakíthatók át, mint a HDInsight Hadoop, a Spark, a Data Lake Analytics és a Machine Learning. Az átalakított adatok megbízhatóan állíthatók elő egy fenntartható és szabályozható séma szerint, az éles környezetek megbízható adatokkal való kiszolgálása érdekében. 

### <a name="publish"></a>Közzététel 
Az átalakított adatok a felhőből áthelyezhetők egy helyszíni forrásra, például egy SQL-kiszolgálóra, vagy a felhőbeli tárolóforrásokban tartható az üzleti intelligenciaára épülő (BI-) és elemzőeszközök, illetve egyéb alkalmazások általi felhasználásra.

## <a name="key-components"></a>A legfontosabb összetevők
Az Azure-előfizetések több Azure Data Factory-példányt (más néven adat-előállítókat) is tartalmazhatnak. Az Azure Data Factory négy fő összetevőből áll, amelyek együtt alkotják azt a platformot, amelyen létrehozhatók az adatvezérelt munkafolyamatok, és amelyeknek a lépései áthelyezik és átalakítják az adatokat. 

### <a name="pipeline"></a>Folyamat
Az adat-előállító egy vagy több folyamattal rendelkezhet. A folyamatok tevékenységek csoportjai. A folyamatban lévő tevékenységek együtt egy feladatot hajtanak végre. Például a folyamat tartalmazhat egy csoportnyi műveletet, amelyek adatokat fogadnak egy Azure -blobból, majd egy Hive-lekérdezést futtatnak egy HDInsight-fürtön az adatok particionálásához. A folyamatok használatának az az előnye, hogy így a tevékenységek egy készletben kezelhetők, nem pedig külön-külön. Például maga a folyamat helyezhető üzembe és ütemezhető, nem a tevékenységek egymástól függetlenül. 

### <a name="activity"></a>Tevékenység
Egy folyamat egy vagy több tevékenységgel rendelkezhet. A tevékenységek meghatározzák az adatokon végrehajtandó műveleteket. A másolási tevékenység használatával például az egyik adattárból a másikba másolhatja az adatokat. Hasonlóképpen, egy Hive-tevékenység használatával Hive-lekérdezést futtathat egy Azure HDInsight-fürtön az adatok átalakításához és elemzéséhez. A Data Factory két típusú tevékenységet támogat: az adattovábbítási tevékenységeket és az adatátalakítási tevékenységeket.

### <a name="data-movement-activities"></a>Adattovábbítási tevékenységek
A Data Factory másolási tevékenysége adatokat másol egy forrásadattárból egy fogadó adattárba. A Data Factory a következő adattárakat támogatja. Az adatok bármilyen forrásból bármilyen fogadóba másolhatók. Az adattárra kattintva megtudhatja, hogy az adott tárolóba, illetve tárolóból hogyan másolhat adatokat.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

További információkért tekintse meg az [adattovábbítási tevékenységekről](data-factory-data-movement-activities.md) szóló cikket.

### <a name="data-transformation-activities"></a>Adatátalakítási tevékenységek
[!INCLUDE [data-factory-transformation-activities](../../../includes/data-factory-transformation-activities.md)]

További információkért tekintse meg az [adatátalakítási tevékenységekről](data-factory-data-transformation-activities.md) szóló cikket.

### <a name="custom-net-activities"></a>Egyéni .NET-tevékenységek
Ha olyan adattárból/adattárba szeretne adatokat továbbítani, amely nem támogatja a másolási tevékenységet, vagy saját logika szerint szeretne adatátalakítást végezni, hozzon létre egy **egyéni .NET-tevékenységet**. További információ az egyéni tevékenységek létrehozásával és használatával kapcsolatban: [Egyéni tevékenységek használata Azure Data Factory-folyamatban](data-factory-use-custom-activities.md).

### <a name="datasets"></a>Adathalmazok
Minden tevékenység nulla vagy több adatkészletet fogad bemenetként, és egy vagy több adatkészletet állít elő kimenetként. Az adatkészletek adatstruktúrákat jelölnek az adattárakon belül, amelyek egyszerűen rámutatnak vagy meghivatkozzák az adatokat, amelyeket a tevékenységekben be- vagy kimenetként használni szeretne. Az Azure Blob-adatkészlet például meghatározza a blobtárolót és azt az Azure Blob Storage-mappát, amelyből a folyamat beolvassa az adatokat. Az Azure SQL Table adatkészlet megadhatja, hogy a tevékenység melyik táblára írja a kimeneti adatokat. 

### <a name="linked-services"></a>Társított szolgáltatások
A társított szolgáltatások nagyon hasonlóak a kapcsolati karakterláncokhoz, amelyek meghatározzák azokat a kapcsolati információkat, amelyeket a Data Factory a külső erőforrásokhoz történő csatlakozáshoz igényel. Tulajdonképpen a társított szolgáltatás határozza meg az adatforrással való kapcsolatot, míg az adatkészlet jelöli az adatok struktúráját. Az Azure Storage társított szolgáltatása például kapcsolati karakterláncot szolgáltat az Azure Storage-fiókhoz való csatlakozáshoz. Az Azure Blob-adatkészlet pedig meghatározza a blobtárolót és az adatokat tartalmazó mappát.   

A társított szolgáltatásokat két célból használjuk a Data Factoryban:

* Egy **adattár**, többek között például egy helyszíni SQL Server, Oracle-adatbázis, fájlmegosztás vagy egy Azure Blob Storage-fiók jelölésére. A támogatott adattárak listája az [Adattovábbítási tevékenységek](#data-movement-activities) című részben található.
* Olyan **számítási erőforrás** jelölésére, amelyen végrehajtható a tevékenység. A HDInsightHive-tevékenység végrehajtása például egy HDInsight Hadoop-fürtön történik. A támogatott számítási környezetek listája az [Adatátalakítási tevékenységek](#data-transformation-activities) szakaszban található.

### <a name="relationship-between-data-factory-entities"></a>Data Factory-entitások közötti kapcsolatok
![Ábra: A Data Factory áttekintése, felhőalapú adatintegrációs szolgáltatás – főbb fogalmak](./media/data-factory-introduction/data-integration-service-key-concepts.png)
**2. ábra.** Az adatkészlet, a tevékenység, a folyamat és a társított szolgáltatás közötti kapcsolatok

## <a name="supported-regions"></a>Támogatott régiók
Jelenleg az **USA nyugati régiójában**, az **USA keleti régiójában** és az **észak-európai** régióban hozhat létre data factoryt. A data factory azonban más Azure-régiókban lévő adattárakhoz és számítási szolgáltatásokhoz is hozzáférhet az adatok adattárak közötti mozgatása vagy az adatok számítási szolgáltatásokkal történő feldolgozása érdekében.

Maga az Azure Data Factory nem tárol adatokat. Lehetővé teszi viszont olyan adatvezérelt munkafolyamatok létrehozását, amelyekkel előkészíthető a [támogatott adattárak](#data-movement-activities) közötti adatmozgás és az adatok [számítási szolgáltatásokkal](#data-transformation-activities) történő feldolgozása más régiókban, illetve helyszíni környezetben. Lehetővé teszi továbbá a [munkafolyamatok figyelését és kezelését](data-factory-monitor-manage-pipelines.md) mind szoftveres, mind pedig felhasználói felületi mechanizmusokkal.

Bár a Data Factory csak az **USA nyugati régiójában**, az **USA keleti régiójában** és az **észak-európai** régióban érhető el, az adatok Data Factoryval történő áthelyezését biztosító szolgáltatás [globálisan](data-factory-data-movement-activities.md#global) számos régióban elérhető. Ha az adattár tűzfal mögött található, akkor a helyszíni környezetben telepített [adatkezelési átjáró](data-factory-move-data-between-onprem-and-cloud.md) végzi az adatok áthelyezését.

Tegyük fel például, hogy számítási környezetei, mint például az Azure HDInsight-fürt és az Azure Machine Learning a nyugat-európai régión kívül futnak. Létrehozhat egy Azure Data Factory-példányt Észak-Európában, és felhasználhatja a Nyugat-Európában lévő számítási környezetein futtatott feladatok ütemezéséhez. A Data Factory néhány ezredmásodperc alatt aktiválja a feladatot a számítási környezetben, a feladatnak a számítási környezetben való futtatásához szükséges idő viszont nem változik.

## <a name="get-started-with-creating-a-pipeline"></a>Bevezetés a folyamatok létrehozásába
Az Azure Data Factoryben a következő eszközök és API-k használhatók adatfolyamatok létrehozására: 

- Azure Portal
- Visual Studio
- PowerShell
- .NET API
- REST API
- Azure Resource Manager-sablon 

A következő oktatóanyagok részletes utasításait követve megtudhatja, hogyan építhet ki adatfolyamatokkal rendelkező data factorykat:

| Oktatóanyag | Leírás |
| --- | --- |
| [Két felhőalapú adattár közötti adatáthelyezés](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) |Ebben az oktatóanyagban olyan folyamattal rendelkező data factoryt hoz létre, amely Blob Storage-ból SQL-adatbázisba **helyez át adatokat**. |
| [Adatok átalakítása Hadoop-fürttel](data-factory-build-your-first-pipeline.md) |Az oktatóanyag során kiépíti az első Azure data factoryját egy olyan adatfolyamattal, amely egy Azure HDInsight (Hadoop) fürtön futtatott Hive-parancsprogrammal **dolgozza fel az adatokat**. |
| [Egy helyszíni és egy felhőalapú adattár közötti adatáthelyezés adatkezelési átjáró segítségével](data-factory-move-data-between-onprem-and-cloud.md) |Ebben az oktatóanyagban olyan folyamattal rendelkező data factoryt épít ki, amely egy **helyszíni** SQL Server-adatbázisból Azure-blobba **helyez át adatokat**. A bemutató részeként telepíti és konfigurálja az adatkezelési átjárót a gépen. |

