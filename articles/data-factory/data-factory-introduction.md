---
title: "A Data Factory nevű adatintegrációs szolgáltatás ismertetése | Microsoft Docs"
description: "A témakör ismerteti, hogy mi is az Azure Data Factory: egy felhőalapú adatintegrációs szolgáltatás, amellyel előkészíthető és automatizálható az adatok továbbítása és átalakítása."
keywords: "adatintegrálás, felhőalapú adatintegráció, mi az az azure data factory"
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
ms.date: 04/21/2017
ms.author: shlo
translationtype: Human Translation
ms.sourcegitcommit: 260208e7c7a08110eb3c885ef86ec4c18ff42fc9
ms.openlocfilehash: 40552b5d3cea5b04826c08e7b4b1d046a9fcefba
ms.lasthandoff: 04/23/2017


---
# <a name="introduction-to-azure-data-factory-service-a-data-integration-service-in-the-cloud"></a>Az Azure Data Factory, egy adatintegrációs felhőalapú szolgáltatás ismertetése
## <a name="what-is-azure-data-factory"></a>Mi az az Azure Data Factory?
A big data világában hogyan aknázhatja ki a vállalkozás a meglévő adatait? Lehetséges a felhőben létrehozott adatokat feldúsítani a helyi vagy más különálló adatforrásokból származó referenciaadatokkal? Ehhez egy olyan platformra van szükség, amely források széles palettájáról képes az adatokat összesíteni és feldolgozni. Az Azure Data Factory egy felhőalapú adatintegrációs szolgáltatás, amellyel előkészíthető és automatizálható az adatok **továbbítása** és **átalakítása**. Olyan adatintegrációs megoldásokat hozhat létre, amelyek különálló adattárakból származó bemeneti adatokkal is képesek dolgozni, és amelyekkel az adatok átalakíthatók/feldolgozhatók, a kimeneti adatok pedig más adattárakban közzétehetők. 

![Ábra: Data Factory áttekintése, adatintegrációs szolgáltatás](./media/data-factory-introduction/what-is-azure-data-factory.png)

**1. ábra** Különböző adatforrásokból származó adatokkal is dolgozhat, előkészítheti, átalakíthatja, elemezheti azokat, és azonnal felhasználható adatokat tehet közzé.


## <a name="what-does-it-offer"></a>Mire használható? 
Hagyományosan az adatintegrációs projektek ETL-folyamatok köré épülnek, amelyek adatokat gyűjtenek a különféle adatforrásokból a szervezeten belül, majd átalakítják ezeket az adatokat, hogy megfeleljenek valamely vállalati adattárház (EDW) célsémájának, és végül feltöltik az adatokat az EDW adattárházba, amint az az alábbi ábrán látható. A BI-elemző megoldások ezután az EDW adattárházhoz fordulnak mint egyetlen hiteles forráshoz.

![Hagyományos ETL](media/data-factory-introduction/traditional-etl.png)
**Hagyományos ETL**

Manapság a vállalkozások adatkörnyezete egyre gyorsuló iramban tágul a mennyiség, a változatosság és az összetettség tekintetében egyaránt, amint az az alábbi ábrán látható. A környezet a helyszínen és a felhőben keletkező különféle formájú és sebességű adatokkal minden eddiginél szerteágazóbb. Az adatfeldolgozásnak különböző földrajzi helyszíneken átívelően kell megvalósulnia, és különféle nyílt forrású szoftvereket, kereskedelmi megoldásokat és egyéni, költséges, nehezen integrálható és karbantartható feldolgozó szolgáltatásokat is igénybe vesz. A mai változó big data-környezethez való alkalmazkodáshoz szükséges rugalmasságot megadja a hagyományos EDW adattárház a modern információ-előállítási rendszerekhez szükséges képességekkel való kibővítésének lehetősége. Az Azure Data Factory az az összegző platform, amellyel a hagyományos EDW adattárházak és a változó adatkörnyezet együttes használatával az adatalapú döntéshozáshoz szükséges összes rendelkezésre álló adatot felhasználhatja.

![Új big data-környezet](media/data-factory-introduction/new-big-data-landscape.png)
**Új big data-környezet**

Az Azure Data Factory segítségével a vállalatok kiaknázhatják ezt a sokszínűséget, mivel egy olyan platformot kínál, amelyen **az adatfeldolgozási, -tárolási és -továbbítási szolgáltatások információ-előállítási folyamatokba rendezhetőek**, és a megbízható adateszközök kezelhetőek.

Az Azure Data Factory szolgáltatás segítségével:
- **Könnyedén dolgozhat különféle adattárolási és -feldolgozási rendszerekkel**. 

    A vállalatok a legkülönfélébb adatokkal rendelkeznek a legkülönfélébb forrásokból. Az információ-előállítási rendszerek kiépítésének első lépése az összes szükséges adatforrás és feldolgozó, például az SaaS-szolgáltatások, a fájlmegosztások, az FTP-k vagy a webszolgáltatások összekapcsolása, és az adatok igényalapú átmozgatása egy központi helyre a további feldolgozás előtt.

    A Data Factory nélkül a vállalatoknak egyéni adattovábbítási összetevőket kell készíteniük vagy egyéni szolgáltatásokat kell írniuk az adatforrások és feldolgozók integrálására. Az ilyen rendszerek költségesek, nehezen integrálhatóak és tarthatók karban, és gyakorta nem áll rendelkezésre az a vállalati szintű felügyeleti, riasztási és vezérlési funkcionalitás, amelyet egy teljes mértékben felügyelt szolgáltatás biztosítani képes.

    A Data Factory segítségével a Másolási tevékenység keretében az adatok egyazon adatfolyamatban helyszíni és felhőalapú forrásadattárakból egyaránt továbbíthatóak egy, a felhőben lévő adattárba további elemzésre. Begyűjtheti például az adatokat egy Azure Data Lake Store tárolóból, és később átalakíthatja azokat egy Azure Data Lake Analytics számítási szolgáltatás használatával. Vagy begyűjtheti az adatokat egy Azure Blob Storage tárolóból, és később átalakíthatja azokat egy Azure HDInsight Hadoop-fürt használatával.
- **Átalakíthatja az adatokat megbízható információkká**. 

    Miután az adatok megjelentek a központi adattárban a felhőben, a megfelelő adatfolyamatok kialakításával és megvalósításával megbízhatóan állíthat elő átalakított adatokat egy fenntartható és szabályozható séma szerint az üzemi környezetek megbízható adatokkal való kiszolgálása érdekében. Az Azure Data Factoryben az adatok átalakítását átalakítási műveletek, például a Hive, a Pig, a MapReduce vagy az Azure Machine Learning kötegelt végrehajtás, valamint az Azure HDInsight Hadoop-fürtön, Azure Machine Learning virtuális gépeken vagy Azure Batch-készletekben lévő virtuális gépeken futó egyéni C#-műveletek végzik.
- **Az adatfolyamatokat egyazon helyen felügyelheti**.

    A változatos adatportfóliók esetében lényeges, hogy megbízható és teljes képpel rendelkezhessen a tárolási, feldolgozási és adattovábbítási szolgáltatásokról. A Data Factory segítségével gyorsan felmérheti a teljes adatfolyamat állapotát, meghatározhatja a hibákat, és megteheti az esetleg szükséges ellenintézkedéseket. Az adatok származása és a különféle forrásokból származó adatok összefüggései vizuálisan nyomon követhetőek. A feladatok végrehajtásának, a rendszer állapotának és a függőségeknek a teljes időbeli vetülete egyetlen felügyeleti irányítópulton megtekinthető.

## <a name="how-does-it-work"></a>Hogyan működik? 
Az információ-előállítás három szakaszra osztható az Azure Data Factoryben:

![A három információ-előállítási szakasz](media/data-factory-introduction/three-information-production-stages.png)

- **Kapcsolódás és begyűjtés**: Ebben a szakaszban a rendszer az adatokat a különböző forrásokból egy helyre gyűjti.
- **Átalakítás és bővítés**: Ebben a szakaszban a rendszer feldolgozza és átalakítja a begyűjtött adatokat.
- **Közzététel**: Ebben a szakaszban a rendszer közzéteszi az adatokat, hogy azokat BI-eszközök, elemzési eszközök és egyéb alkalmazások felhasználhassák.

## <a name="key-components"></a>A legfontosabb összetevők
Az Azure-előfizetések több Azure Data Factory-példányt (más néven adat-előállítókat) is tartalmazhatnak. Az Azure Data Factory négy fő összetevőből áll, amelyek együtt alkotják azt a platformot, amelyen egyszerű vagy összetett adattovábbítási és átalakítási folyamatokat alakíthat ki az adatfolyama számára.

### <a name="activity"></a>Tevékenység
A tevékenységek meghatározzák az adatokon végrehajtandó műveleteket. A másolási tevékenység használatával például az egyik adattárból a másikba másolhatja az adatokat. Hasonlóképpen, egy Hive-tevékenység használatával Hive-lekérdezést futtathat egy Azure HDInsight-fürtön az adatok átalakításához és elemzéséhez. A Data Factory két típusú tevékenységet támogat: az adattovábbítási tevékenységeket és az adatátalakítási tevékenységeket.

Minden tevékenység nulla vagy több bemeneti adatkészletet képes fogadni, és egy vagy több kimeneti adatkészletet képes előállítani. 


### <a name="data-movement-activities"></a>Adattovábbítási tevékenységek
A Data Factory másolási tevékenysége adatokat másol egy forrásadattárból egy fogadó adattárba. A Data Factory a következő adattárakat támogatja. Az adatok bármilyen forrásból bármilyen fogadóba másolhatók. Az adattárra kattintva megtudhatja, hogy az adott tárolóba, illetve tárolóból hogyan másolhat adatokat.

[!INCLUDE [data-factory-supported-data-stores](../../includes/data-factory-supported-data-stores.md)]

További információkért tekintse meg az [adattovábbítási tevékenységekről](data-factory-data-movement-activities.md) szóló cikket.

### <a name="data-transformation-activities"></a>Adatátalakítási tevékenységek
[!INCLUDE [data-factory-transformation-activities](../../includes/data-factory-transformation-activities.md)]

További információkért tekintse meg az [adatátalakítási tevékenységekről](data-factory-data-transformation-activities.md) szóló cikket.

Ha olyan adattárból/adattárba szeretne adatokat továbbítani, amely nem támogatja a másolási tevékenységet, vagy saját logika szerint szeretne adatátalakítást végezni, hozzon létre egy **egyéni .NET-tevékenységet**. További információ az egyéni tevékenységek létrehozásával és használatával kapcsolatban: [Egyéni tevékenységek használata Azure Data Factory-folyamatban](data-factory-use-custom-activities.md).

### <a name="pipeline"></a>Folyamat
A folyamatok tevékenységek csoportjai. A folyamatban lévő tevékenységek együtt egy feladatot hajtanak végre. Például a folyamat tartalmazhat egy csoport műveletet, amelyek adatokat fogadnak egy Azure blobból, majd egy Hive-lekérdezést futtatnak egy HDInsight-fürtön a naplóadatok particionálásához. A folyamatok használatának az az előnye, hogy így a tevékenységek egy készletben kezelhetők, nem pedig külön-külön. Például maga a folyamat helyezhető üzembe és ütemezhető, nem a tevékenységek egymástól függetlenül.

### <a name="datasets"></a>Adathalmazok
Az adatkészletek adatstruktúrákat jelölnek az adattárakon belül, amelyek egyszerűen rámutatnak vagy meghivatkozzák az adatokat, amelyeket a tevékenységekben be- vagy kimenetként használni szeretne. Az Azure Blob-adatkészlet például meghatározza a blobtárolót és azt az Azure Blob Storage-mappát, amelyből a folyamat beolvassa az adatokat. 

### <a name="linked-services"></a>Társított szolgáltatások
A társított szolgáltatások nagyon hasonlóak a kapcsolati karakterláncokhoz, amelyek meghatározzák azokat a kapcsolati információkat, amelyeket a Data Factory a külső erőforrásokhoz történő csatlakozáshoz igényel. Tulajdonképpen az adatkészlet jelöli az adatok struktúráját, míg a társított szolgáltatás határozza meg az adatforrással való kapcsolatot.  A társított szolgáltatásokat két célból használjuk a Data Factoryban:

* Egy **adattár**, többek között például egy helyszíni SQL Server, Oracle-adatbázis, fájlmegosztás vagy egy Azure Blob Storage-fiók jelölésére. A támogatott adattárak listája az [Adattovábbítási tevékenységek](#data-movement-activities) című részben található.
* Olyan **számítási erőforrás** jelölésére, amelyen végrehajtható a tevékenység. A HDInsightHive-tevékenység végrehajtása például egy HDInsight Hadoop-fürtön történik. A támogatott számítási környezetek listája az [Adatátalakítási tevékenységek](#data-transformation-activities) szakaszban található.

### <a name="relationship-between-data-factory-entities"></a>Data Factory-entitások közötti kapcsolatok
![Ábra: A Data Factory áttekintése, felhőalapú adatintegrációs szolgáltatás – főbb fogalmak](./media/data-factory-introduction/data-integration-service-key-concepts.png)
**2. ábra.** Az adatkészlet, a tevékenység, a folyamat és a társított szolgáltatás közötti kapcsolatok

## <a name="supported-regions"></a>Támogatott régiók
Jelenleg az **USA nyugati régiójában**, az **USA keleti régiójában** és az **észak-európai** régióban hozhat létre data factoryt. A data factory azonban más Azure-régiókban lévő adattárakhoz és számítási szolgáltatásokhoz is hozzáférhet az adatok adattárak közötti mozgatása vagy az adatok számítási szolgáltatásokkal történő feldolgozása érdekében.

Maga az Azure Data Factory nem tárol adatokat. Lehetővé teszi viszont olyan adatvezérelt munkafolyamatok létrehozását, amelyekkel előkészíthető a [támogatott adattárak](data-factory-data-movement-activities.md#supported-data-stores-and-formats) közötti adatmozgás és az adatok [számítási szolgáltatásokkal](data-factory-compute-linked-services.md) történő feldolgozása más régiókban, illetve helyszíni környezetben. Lehetővé teszi továbbá a [munkafolyamatok figyelését és kezelését](data-factory-monitor-manage-pipelines.md) mind szoftveres, mind pedig felhasználói felületi mechanizmusokkal.

Bár a Data Factory csak az **USA nyugati régiójában**, az **USA keleti régiójában** és az **észak-európai** régióban érhető el, az adatok Data Factoryval történő áthelyezését biztosító szolgáltatás [globálisan](data-factory-data-movement-activities.md#global) számos régióban elérhető. Ha az adattár tűzfal mögött található, akkor a helyszíni környezetben telepített [adatkezelési átjáró](data-factory-move-data-between-onprem-and-cloud.md) végzi az adatok áthelyezését.

Tegyük fel például, hogy számítási környezetei, mint például az Azure HDInsight-fürt és az Azure Machine Learning a nyugat-európai régión kívül futnak. Létrehozhat egy Azure Data Factory-példányt Észak-Európában, és felhasználhatja a Nyugat-Európában lévő számítási környezetein futtatott feladatok ütemezéséhez. A Data Factory néhány ezredmásodperc alatt aktiválja a feladatot a számítási környezetben, a feladatnak a számítási környezetben való futtatásához szükséges idő viszont nem változik.

A későbbiekben szeretnénk minden olyan régióra kiterjeszteni az Azure Data Factoryt, ahol támogatott az Azure használata.

## <a name="next-steps"></a>Következő lépések
A következő oktatóanyagok részletes utasításait követve megtudhatja, hogyan építhet ki adatfolyamatokkal rendelkező data factorykat:

| Oktatóanyag | Leírás |
| --- | --- |
| [Két felhőalapú adattár közötti adatáthelyezés](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) |Ebben az oktatóanyagban olyan folyamattal rendelkező data factoryt hoz létre, amely Blob Storage-ból SQL-adatbázisba **helyez át adatokat**. |
| [Adatok átalakítása Hadoop-fürttel](data-factory-build-your-first-pipeline.md) |Az oktatóanyag során kiépíti az első Azure data factoryját egy olyan adatfolyamattal, amely egy Azure HDInsight (Hadoop) fürtön futtatott Hive-parancsprogrammal **dolgozza fel az adatokat**. |
| [Egy helyszíni és egy felhőalapú adattár közötti adatáthelyezés adatkezelési átjáró segítségével](data-factory-move-data-between-onprem-and-cloud.md) |Ebben az oktatóanyagban olyan folyamattal rendelkező data factoryt épít ki, amely egy **helyszíni** SQL Server-adatbázisból Azure-blobba **helyez át adatokat**. A bemutató részeként telepíti és konfigurálja az adatkezelési átjárót a gépen. |

