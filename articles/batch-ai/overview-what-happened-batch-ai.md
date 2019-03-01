---
title: Mi történik az Azure Batch AI? | Microsoft Docs
description: További tudnivalók az Azure Batch AI, mi történik, és az Azure Machine Learning szolgáltatás számítási lehetőségek.
ms.service: batch-ai
services: batch-ai
ms.topic: overview
ms.author: jmartens
author: j-martens
ms.date: 2/28/2019
ms.openlocfilehash: edd6a7e5f385d766d51631d77f5889233af2469a
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/01/2019
ms.locfileid: "57194502"
---
# <a name="whats-happening-to-azure-batch-ai"></a>Mi történik az Azure Batch AI?

**Az Azure Batch AI szolgáltatás márciusi kivonás alatt áll.** Az ipari méretekben tanítási és pontozási a Batch AI képességeit már elérhetők [Azure Machine Learning szolgáltatás](../machine-learning/service/overview-what-is-azure-ml.md), amely óta általánosan elérhető 2018. December 4.

Sok más gépi tanulási funkciókat, valamint az Azure Machine Learning szolgáltatás tartalmaz egy felhőalapú, felügyelt számítási célt képzés, üzembe helyezése és pontozás a machine learning-modellek. A számítási célnak nevezzük [Azure Machine Learning Compute](../machine-learning/service/how-to-set-up-training-targets.md#amlcompute). [Indítsa el a migrálást, és jelenleg is használó](#migrate). Az Azure Machine Learning szolgáltatás segítségével kezelheti a [Python SDK-k](../machine-learning/service/quickstart-create-workspace-with-python.md), parancssori felületet, és a [az Azure portal](../machine-learning/service/quickstart-get-started.md).

## <a name="support-timeline"></a>Támogatási idővonal

Jelenleg a meglévő Azure Batch AI előfizetéseket, mielőtt is használhatja. Azonban nem **új előfizetések** is lehetséges, és nincs új befektetések kerül sor.

Től március&nbsp;31&#x2c;&nbsp;2019, Batch AI nem használt előfizetések nem fognak működni.

## <a name="compare-to-azure-machine-learning"></a>Hasonlítsa össze az Azure Machine Learning
Egy felhőalapú szolgáltatás, amellyel betanításához, üzembe helyezése, automatizálhatja és felügyelheti a machine learning-modellek, minden a felhőbeli biztosít széles körű skála. Egy magas szintű ismeretekkel lekérése a [ebben a cikkben áttekintjük az Azure Machine Learning szolgáltatás](../machine-learning/service/overview-what-is-azure-ml.md).
 

Egy tipikus modell fejlesztési életciklus beletartozik az adat-előkészítési, a képzés és a Kísérletezési és a egy üzembe helyezési fázisnak. A teljes körű ciklus is vezényli a Machine Learning-folyamatokat használ.

![folyamatábrája](./media/overview-what-happened-batch-ai/lifecycle.png)


[További információ a szolgáltatás működése és a főbb fogalmait](../machine-learning/service/concept-azure-machine-learning-architecture.md). A modell betanítási munkafolyamat fogalmak számos hasonlóak a meglévő fogalmait a Batch AI. 

Pontosabban a következő térképét hogyan gondolja őket:
 
|A Batch AI szolgáltatás|  Azure Machine Learning szolgáltatás|
|:--:|:---:|
|Munkaterület|Munkaterület|
|Fürt|   Számítási típus `AmlCompute`|
|Fájlkiszolgálók|Adattárolók|
|Kísérletek|Kísérletek|
|Feladatok|Fut (lehetővé teszi, hogy a beágyazott fut)|
 
Íme egy másik nézet ugyanazon tábla, amely segít a további elemek megjelenítése:
 
### <a name="batch-ai-hierarchy"></a>A Batch AI-hierarchia
![folyamatábrája](./media/overview-what-happened-batch-ai/batchai-heirarchy.png) 
 
### <a name="azure-machine-learning-service-hierarchy"></a>Az Azure Machine Learning szolgáltatás hierarchia
![folyamatábrája](./media/overview-what-happened-batch-ai/azure-machine-learning-service-heirarchy.png) 

## <a name="platform-capabilities"></a>Platform képességei
Az Azure Machine Learning szolgáltatás tárházaként nagyszerű új funkciókat, beleértve egy teljes körű képzési -> üzembe helyezési vermek, amely az AI-fejlesztéshez használhatja bármely Azure-erőforrások kezelése nélkül. Ez a táblázat hasonlítja össze a két szolgáltatás között képzést funkció támogatása.

|Szolgáltatás|A Batch AI szolgáltatás|Azure Machine Learning szolgáltatás|
|-------|:-------:|:-------:|
|Virtuális gép méretének kiválasztása |PROCESSZOR ÉS GPU    |PROCESSZOR ÉS GPU-T. Emellett támogatja a FPGA következtetési|
|Mesterséges Intelligencia üzemkész fürt (illesztőprogramok, Docker, stb.)|  Igen |Igen|
|Csomópont-előkészítő| Igen|    Nem|
|Operációsrendszer-család kiválasztása   |Részleges    |Nem|
|Dedikált és LowPriority virtuális gépek  |Igen    |Igen|
|Automatikus méretezés   |Igen    |Igen (alapértelmezés)|
|Várakozási idő az automatikus skálázáshoz  |Nem |Igen|
|SSH    |Igen|   Igen|
|Fürt feladatszintű csatlakoztatás |Igen (FileShares, Blobok, az NFS, egyéni)   |Igen (csatlakoztatni, vagy töltse le az adattár)|
|Elosztott betanítás|  Igen |Igen|
|Feladat-végrehajtási mód|    Virtuális gép vagy a tároló|    Tároló|
|Egyéni Tárolórendszerképet|    Igen |Igen|
|Bármely eszközkészlet    |Igen    |Igen (futtassa Python-szkriptet)|
|JobPreparation|    Igen |még nem|
|Feladatszintű csatlakoztatás |Igen (FileShares, Blobok, az NFS, egyéni)   |Igen (FileShares, Blobok)|
|Feladat figyelése     |GetJob keresztül|    Futtatási előzmények (részletesebb információk, egyéni modult, hogy további metrikák push) keresztül|
|Feladatnaplók és fájlok/modellek beolvasása |   ListFiles és a Storage API-kon keresztül  |Összetevő szolgáltatáson keresztül|
 |Tensorboard támogatása   |Nem|    Igen|
|Virtuális gép családi szintje szerinti kvóták |Igen    |Igen (az előző kapacitás átvitelre)|
 
Az előző táblázatban kívül is BatchAI hagyományosan nem támogatott szolgáltatásokat az Azure Machine Learning szolgáltatásban.

|Szolgáltatás|A Batch AI szolgáltatás|Azure Machine Learning szolgáltatás|
|-------|:-------:|:-------:|
|Környezet előkészítése    |Nem |Igen (Conda előkészítése és feltöltése az ACR-be)|
|Hiperparaméter finomhangolása  |Nem|    Igen|
|Modellkezelés   |Nem |Igen|
|Operationalization/Deployment| Nem  |AKS és ACI-n keresztül|
|Adatok előkészítése   |Nem |Igen|
|Számítási céljainak    |Azure-beli virtuális gépek  |Helyi, BatchAI (a AmlCompute), a DataBricks, a HDInsight|
|Automatizált Machine Learning |Nem|    Igen|
|Folyamatok  |Nem |Igen|
|Batch-pontozás  |Igen    |Igen|
|Támogatási portál vagy a parancssori felületen|    Igen |Igen|


## <a name="programming-interfaces"></a>Programozási felületek

Ez a táblázat minden egyes szolgáltatáshoz elérhető különböző alkalmazásprogramozási felületek mutat be.
    
|Szolgáltatás|BatchAI szolgáltatás|Azure Machine Learning szolgáltatás|
|-------|:-------:|:-------:|
|SDK    |Java, C#, Python, Nodejs   |Python (futtatási konfigurációs alapú és a közös keretrendszer alapján estimator)|
|parancssori felület    |Igen    |még nem|
|Azure Portal   |Igen    |Igen (kivéve a feladat elküldése)|
|REST API   |Igen    |Igen, de elosztott mikroszolgáltatás-alapú között|


Az előzetes verzió a Batch AI verzióról a GA'ed Azure Machine Learning szolgáltatáshoz biztosít révén könnyebben használható, például Estimators és adattárainak fogalmakat jobb felhasználói élményt. Általánosan elérhető Azure-szolgáltatási szintű SLA-k és az ügyfél-támogatási is garantálja.

Az Azure Machine Learning szolgáltatás is teszi elérhetővé az új funkciók például automatizált, machine learning, a hiperparaméter finomhangolása és a gépi Tanulási folyamatok, amelyek hasznosak lehetnek a legtöbb nagy léptékű AI-számítási. Egy különálló szolgáltatás közötti váltás nélkül helyezheti üzembe az alkalmazásokat a betanított modell segítségével végezze el a data science hurok az adatok (a Data Prep SDK-val) egészen operacionalizálás és a modell figyelése.

<a name="migrate"></a>
## <a name="migrate"></a>Migrate (Áttelepítés)

Ismerje meg, hogyan telepítheti át, és hogyan használja a kód az Azure Machine Learning szolgáltatásból kód leképezi a [áttelepítése az Azure Machine Learning szolgáltatáshoz](how-to-migrate.md) cikk.

## <a name="get-support"></a>Támogatás kérése

A Batch AI van nyár kivonása. március 31-én kerül piacra, és regisztráljon a szolgáltatáshoz, kivéve ha szerepel az engedélyezési listán által támogatásának kivételt hozna létre az új előfizetések már blokkolja.  Fel velünk a kapcsolatot, [Azure Batch AI Training előzetes verziójának](mailto:AzureBatchAITrainingPreview@service.microsoft.com) , ha bármilyen kérdése van, vagy ha visszajelzést szeretne küldeni a migrálás az Azure Machine Learning szolgáltatáshoz.

Az Azure Machine Learning szolgáltatás egy olyan általánosan elérhető szolgáltatás. Ez azt jelenti, hogy a vállalt szolgáltatásszint-szerződéssel és különböző támogatási csomagok, amelyek közül választhatnak, származik.

Azure-infrastruktúra a Batch AI szolgáltatás vagy az Azure Machine Learning szolgáltatás révén a díjszabás nem változhat, csak az ár mindkét esetben a mögöttes számítási díj szerint. További információkért lásd: a [díjkalkulátor](https://azure.microsoft.com/pricing/details/machine-learning-service/).

A régiónkénti rendelkezésre állás, a két szolgáltatás között megtekintheti a [az Azure portal](https://azure.microsoft.com/global-infrastructure/services/?products=batch-ai,machine-learning-service&regions=all).


## <a name="next-steps"></a>További lépések

+ Ismerje meg, [áttelepítése](how-to-migrate.md) , és hogyan kód most már használhatja az Azure Machine Learning szolgáltatásból kód.

+ Olvassa el a [Azure Machine Learning szolgáltatás áttekintése](../machine-learning/service/overview-what-is-azure-ml.md).

+ [Konfigurálja a modell betanítása és számítási célt](../machine-learning/service/how-to-set-up-training-targets.md) Azure Machine Learning szolgáltatással.

+ Tekintse át a [Azure ütemterve](https://azure.microsoft.com/updates/) további más Azure-szolgáltatás frissítések.
