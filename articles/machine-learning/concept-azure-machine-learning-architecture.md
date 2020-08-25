---
title: Architektúra & főbb fogalmak
titleSuffix: Azure Machine Learning
description: Ismerkedjen meg a Azure Machine Learning alkotó architektúrával, kifejezésekkel és fogalmakkal.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 08/20/2020
ms.custom: seoapril2019, seodec18
ms.openlocfilehash: d7bad24510f74a7fadd74328e24ea22855e6fe02
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750859"
---
# <a name="how-azure-machine-learning-works-architecture-and-concepts"></a>A Azure Machine Learning működése: architektúra és fogalmak

Ismerje meg a Azure Machine Learning architektúráját és fogalmait.

> [!NOTE]
> Bár ez a cikk az Azure Machine Learning által használt feltételeket és fogalmakat határozza meg, nem határozza meg az Azure platformra vonatkozó feltételeket és fogalmakat. További információ az Azure platform terminológiáról: [Microsoft Azure Szószedet](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology).

## <a name="workspace"></a><a name="workspace"></a> Munkaterület

:::image type="content" source="media/concept-azure-machine-learning-architecture/architecture.svg" alt-text="Azure Machine Learning architektúra":::

A [Machine learning-munkaterület](concept-workspace.md) a Azure Machine learning legfelső szintű erőforrása.  A munkaterület a következő központi hely:
* Kezelheti a modellek betanításához és üzembe helyezéséhez használt erőforrásokat, például a [számításokat](#compute-instance)
* Tárolja a Azure Machine Learning használatakor létrehozott eszközöket, beleértve a következőket:
  * [Környezetek](#environments)
  * [Futtatás](#runs)
  * [Pipelines](#ml-pipelines)
  * [Adatkészletek](#datasets-and-datastores)
  * [Modellek](#models)
  * [Végpontok](#endpoints)

A munkaterület más Azure-erőforrásokat is tartalmaz, amelyeket a munkaterület használ:

+ [Azure Container Registry](https://azure.microsoft.com/services/container-registry/): regisztrálja a betanítás során használt Docker-tárolókat, és amikor üzembe helyezi a modellt. A költségek csökkentése érdekében az ACR a telepítési lemezképek létrehozása előtt **betöltődik** .
+ [Azure Storage-fiók](https://azure.microsoft.com/services/storage/): a munkaterület alapértelmezett adattára.  A Azure Machine Learning számítási példányokhoz használt Jupyter-jegyzetfüzeteket is itt tárolja.
+ [Azure Application Insights](https://azure.microsoft.com/services/application-insights/): a modellekkel kapcsolatos figyelési információkat tárolja.
+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/): a számítási célok és a munkaterület által igényelt egyéb bizalmas információk által használt titkokat tárolja.

A munkaterületeket másokkal is megoszthatja.

## <a name="studio"></a>Studio

[Azure Machine learning Studio](https://ml.azure.com) a munkaterületen található összes összetevőt webes nézetben jeleníti meg.  Ezen a portálon a Azure Machine Learning részét képező interaktív eszközök is elérhetők:

+ [Azure Machine learning Designer (előzetes verzió)](concept-designer.md) a munkafolyamat lépéseinek kód írása nélkül történő elvégzéséhez
+ Webes felület az [automatizált gépi tanuláshoz](concept-automated-ml.md)
+ [Adatcímkéző projektek](how-to-create-labeling-projects.md) az adatcímkékhez tartozó projektek létrehozásához, kezeléséhez és figyeléséhez

##  <a name="computes"></a>Kiszámítja

<a name="compute-targets"></a> A [számítási cél](concept-compute-target.md) az a számítógép vagy gép, amelyben a betanítási szkriptet futtatja, vagy üzemelteti a szolgáltatás központi telepítését. Ez a hely lehet a helyi számítógép vagy egy távoli számítási erőforrás.

Azure Machine Learning két teljes körűen felügyelt felhőalapú számítási erőforrást vezet be, amelyek gépi tanulási feladatokhoz vannak konfigurálva:

* <a name="compute-instance"></a>**Számítási példány** ([Computeinstance](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computeinstance?view=azure-ml-py)): a számítási példány egy virtuális gép (VM), amely több eszközt és környezetet is tartalmaz a gépi tanuláshoz. Használjon számítási példányt a fejlesztői munkaállomáson, hogy megkezdje a telepítés nélkül megjelenő minta-jegyzetfüzetek futtatását. Számítási célként is használható a betanítási és a következtetési feladatokhoz.
* **Számítási fürtök** ([amlcompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py)): több csomópontos skálázási képességekkel rendelkező virtuális gépek fürtje. Automatikusan méretezi a feladatok elküldésekor. A nagyméretű feladatokhoz és a gyártáshoz alkalmasabb számítási célokhoz. Használjon képzési számítási célt vagy fejlesztési/tesztelési célú üzembe helyezést.

A számítási célok betanításával kapcsolatos további információkért lásd: [számítási célok betanítása](concept-compute-target.md#train).  További információ az üzembe helyezési számítási célokról: [telepítési célok](concept-compute-target.md#deploy).

## <a name="datasets-and-datastores"></a>Adatkészletek és adattárolók

[**Azure Machine learning adatkészletek**](concept-data.md#datasets)  megkönnyítik az adataik elérését és a velük való munkát. Az adathalmazok különböző forgatókönyvekben, például a modell betanítása és a folyamat létrehozása során kezelik az adatokat. Az Azure Machine Learning SDK használatával elérheti a mögöttes tárolót, feltárhatja az adatokat, és kezelheti a különböző adatkészlet-definíciók életciklusát.

Az adatkészletek olyan módszereket biztosítanak, amelyekkel az adatokat népszerű formátumokban, például a vagy a használatával dolgozhatják fel `from_delimited_files()` `to_pandas_dataframe()` .

További információ: [Azure Machine learning adatkészletek létrehozása és regisztrálása](how-to-create-register-datasets.md).  Az adatkészletek használatával kapcsolatos további példákért tekintse meg a [minta jegyzetfüzeteket](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/datasets-tutorial).

Az [**adattár**](concept-data.md#datastores) egy Azure Storage-fiókon keresztüli adattárolási absztrakt. Minden munkaterülethez tartozik egy alapértelmezett adattár, amely további adattárolókat is regisztrálhat. A Python SDK API-val vagy a Azure Machine Learning CLI-vel fájlokat tárolhat és kérhet le az adattárból. 

## <a name="models"></a>Modellek

Legegyszerűbben a modell egy olyan kódrészlet, amely bemenetet és kimenetet hoz létre. A gépi tanulási modell létrehozásához ki kell választania egy algoritmust, amely lehetővé teszi az adatgyűjtést és a [hiperparaméterek beállítása hangolását](how-to-tune-hyperparameters.md). A képzés egy olyan ismétlődő folyamat, amely egy betanított modellt hoz létre, amely bemutatja a modell betanítási folyamatát.

A modellt a Azure Machine Learning egy [kísérletének](#experiments) [futtatása](#runs) állítja elő. A Azure Machine Learningon kívül betanított modellt is használhat. Ezután [regisztrálja a modellt](#register-model) a munkaterületen.

Azure Machine Learning a Framework agnosztikus. Modell létrehozásakor bármilyen népszerű Machine learning-keretrendszert használhat, például a Scikit-Learn, a XGBoost, a PyTorch, a TensorFlow és a Chainer platformot.

Példa a modell Scikit-Learn használatával történő betanítására [: oktatóanyag: képbesorolási modell Betanítása Azure Machine Learningsal](tutorial-train-models-with-aml.md).

### <a name="model-registry"></a><a name="register-model"></a> Modell beállításjegyzéke
[Munkaterület](#workspace)  >  **Modell beállításjegyzéke**

A **modell beállításjegyzékében** nyomon követheti a Azure Machine learning munkaterület összes modelljét.

A modelleket név és verzió alapján azonosítjuk. Minden alkalommal, amikor egy olyan modellt regisztrál, amelynek a neve megegyezik egy meglévővel, a beállításjegyzék feltételezi, hogy ez egy új verzió. A verzió növekményes, és az új modell ugyanazon a néven van regisztrálva.

A modell regisztrálása esetén további metaadat-címkéket is megadhat, majd használhatja a címkéket a modellek keresésekor.

> [!TIP]
> A regisztrált modell a modellt alkotó egy vagy több fájl logikai tárolója. Ha például több fájlban tárolt modell van, akkor egyetlen modellként regisztrálhatja őket a Azure Machine Learning munkaterületen. A regisztráció után letöltheti vagy telepítheti a regisztrált modellt, és megkapja az összes regisztrált fájlt.

Egy aktív telepítés által használt regisztrált modell nem törölhető.

A modellek regisztrálására példát a [képbesorolási modell Betanítása Azure Machine Learningkal](tutorial-train-models-with-aml.md)című témakörben talál.


### <a name="environments"></a>Környezetek

[Munkaterület](#workspace)  >  **Környezetek**

A [környezet](concept-environments.md) az a környezet beágyazása, ahol a gépi tanulási modell betanítása vagy pontozása zajlik. A környezet meghatározza a Python-csomagokat, a környezeti változókat és a szoftver beállításait a képzés és a pontozási szkriptek köré.

A kódok használatával kapcsolatban tekintse meg a [környezetek használatáról](how-to-use-environments.md#manage-environments)szóló témakör "környezetek kezelése" című szakaszát.

### <a name="experiments"></a>Kísérletek

[Munkaterület](#workspace)  >  **Kísérletek**

A kísérlet egy adott parancsfájlból származó számos Futtatás csoportosítása. Mindig egy munkaterülethez tartozik. Futtatáskor a kísérlet nevét adja meg. A futtatással kapcsolatos információkat a kísérlet alatt tárolja a rendszer. Ha a név nem létezik a kísérlet elküldésekor, automatikusan létrejön egy új kísérlet.

Egy kísérletre példaként tekintse meg az [oktatóanyag: az első modell betanítása](tutorial-1st-experiment-sdk-train.md)című témakört.

### <a name="runs"></a>Futtatás

[Munkaterület](#workspace)  >  [Kísérletek](#experiments)  >  **Futtatás**

A Futtatás egy képzési parancsfájl egyetlen végrehajtása. Egy kísérlet általában több futtatást is tartalmaz.

Azure Machine Learning rögzíti az összes futtatást, és a következő információkat tárolja a kísérletben:

* A futtatással kapcsolatos metaadatok (timestamp, időtartam stb.)
* A parancsfájl által naplózott metrikák
* A kísérlet által összegyűjtött vagy kifejezetten az Ön által feltöltött kimeneti fájlok
* A futtatást megelőzően a parancsfájlokat tartalmazó könyvtár pillanatképe

A futtatást akkor kell létrehoznia, ha parancsfájlt küld a modell betanításához. Egy Futtatás nulla vagy több gyermek futtatásával is rendelkezhet. Előfordulhat például, hogy a legfelső szintű Futtatás két gyermeket futtat, amelyek mindegyike rendelkezhet saját gyermekével.

### <a name="run-configurations"></a>Konfigurációk futtatása

[Munkaterület](#workspace)  >  [Kísérletek](#experiments)  >  [Futtatás](#runs)  >  **Konfiguráció futtatása**

A futtatási konfiguráció olyan utasítások összessége, amelyek meghatározzák, hogy egy parancsfájl hogyan fusson egy adott számítási célhelyen. A konfiguráció számos viselkedési definíciót tartalmaz, például azt, hogy egy meglévő Python-környezetet kíván-e használni, vagy egy specifikáció alapján létrehozott Conda-környezetet használ-e.

A futtatási konfiguráció a betanítási parancsfájlt tartalmazó könyvtárban található fájlban maradhat.   Vagy egy memóriában tárolt objektumként is létrehozható, és futtatásra is elküldhető.

A futtatási konfigurációkat például a [modell kitanítására szolgáló számítási cél kiválasztása és használata](how-to-set-up-training-targets.md)című témakörben tekintheti meg.

### <a name="estimators"></a>Becslések

A kisegítő lehetőségek a népszerű keretrendszerekkel való modellezésének megkönnyítésére a kalkulátor osztály lehetővé teszi a futtatási konfigurációk egyszerű összeállítását. Létrehozhat és használhat általános [becslést](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) olyan képzési parancsfájlok beküldéséhez, amelyek bármely kiválasztott tanulási keretrendszert (például scikit-Learn) használnak.

A PyTorch, a TensorFlow és a láncolási feladatok esetében a Azure Machine Learning a megfelelő [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)és [láncolási](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) becslések is biztosít, hogy egyszerűbbé váljon ezen keretrendszerek használata.

További információkért tekintse át a következő cikkeket:

* [Ml modellek betanítása a becslések](how-to-train-ml-models.md).
* [A Pytorch mély tanulási modelljeinek Kiképzése Azure Machine learning](how-to-train-pytorch.md).
* [TensorFlow-modellek betanítása és regisztrálása Azure Machine learning](how-to-train-tensorflow.md).
* [A láncolt modelleket Kitaníthatja és regisztrálhatja Azure Machine learning](how-to-train-ml-models.md).

### <a name="snapshots"></a>Pillanatképek

[Munkaterület](#workspace)  >  [Kísérletek](#experiments)  >  [Futtatás](#runs)  >  **Pillanatkép**

Amikor elküld egy futtatást, Azure Machine Learning tömöríti azt a könyvtárat, amely a parancsfájlt zip-fájlként tartalmazza, és elküldi a számítási célnak. Ekkor a rendszer kibontja a zip-fájlt, és ott futtatja a parancsfájlt. Azure Machine Learning a zip-fájlt pillanatképként tárolja a futtatási rekord részeként. Bárki, aki hozzáféréssel rendelkezik a munkaterülethez, böngészhet egy futtatási rekordot, és letöltheti a pillanatképet.


### <a name="logging"></a>Naplózás

A megoldás fejlesztésekor a Python-szkript Azure Machine Learning Python SDK-val tetszőleges metrikákat naplózhat. A Futtatás után a metrikák lekérdezésével állapítsa meg, hogy a Futtatás előkészítette-e a telepíteni kívánt modellt.


> [!NOTE]
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]

### <a name="git-tracking-and-integration"></a>Git-követés és-integráció

Ha olyan képzést indít el, ahol a forrás könyvtára helyi git-tárház, a rendszer a tárház adatait a futtatási előzményekben tárolja. Ez a szolgáltatás a kalkulátor, a ML folyamat vagy a szkript futtatásával elküldött futtatásokkal működik. Az SDK-ból vagy Machine Learning parancssori felületről küldött futtatások esetén is működik.

További információ: git- [integráció Azure Machine Learninghoz](concept-train-model-git-integration.md).

## <a name="deployment"></a>Üzembe helyezés

A [regisztrált modellt](#register-model) szolgáltatás-végpontként kell üzembe helyezni. A következő összetevőkre van szüksége:

* **Környezet**. Ez a környezet beágyazza a modellnek a következtetéshez való futtatásához szükséges függőségeket.
* **Pontozási kód**. Ez a szkript fogadja a kéréseket, a modell használatával szerzi be a kérelmeket, és visszaadja az eredményeket.
* **Következtetési konfiguráció**. A következtetési konfiguráció határozza meg a környezetet, a bejegyzés parancsfájlját és a modell szolgáltatásként való futtatásához szükséges egyéb összetevőket.

További információ ezekről az összetevőkről: [modellek üzembe helyezése Azure Machine Learningsal](how-to-deploy-and-where.md).

### <a name="endpoints"></a>Végpontok

[Munkaterület](#workspace)  >  **Végpontok**

A végpontok a modell példányai egy olyan webszolgáltatásba, amely a felhőben vagy egy IoT modulban üzemeltethető az integrált eszközök üzembe helyezéséhez.

#### <a name="web-service-endpoint"></a>Webszolgáltatás végpontja

A modell webszolgáltatásként való telepítésekor a végpont Azure Container Instances, Azure Kubernetes Service-ben vagy FPGA-ben is üzembe helyezhető. A szolgáltatást a modellből, a parancsfájlból és a társított fájlokból hozza létre. Ezek egy alap tároló-rendszerképbe kerülnek, amely a modell végrehajtási környezetét tartalmazza. A rendszerkép egy elosztott terhelésű, HTTP-végponttal rendelkezik, amely a webszolgáltatásnak küldött pontozási kérelmeket fogadja.

A webszolgáltatások figyeléséhez Application Insights telemetria vagy Model telemetria is engedélyezheti. A telemetria-adatbázis csak Ön számára érhető el.  A rendszer a Application Insights és a Storage-fiók példányaiban tárolja.

Ha engedélyezte az automatikus skálázást, az Azure automatikusan méretezi az üzembe helyezést.

Egy modell webszolgáltatásként való üzembe helyezésére példa: [lemezkép besorolási modell telepítése Azure Container Instancesban](tutorial-deploy-models-with-aml.md).

#### <a name="iot-module-endpoints"></a>IoT modul végpontjai

Az üzembe helyezett IoT-végpont egy olyan Docker-tároló, amely tartalmazza a modellt, valamint a hozzá tartozó parancsfájlt vagy alkalmazást, valamint a további függőségeket. Ezeket a modulokat a Azure IoT Edge Edge-eszközökön történő üzembe helyezésével végezheti el.

Ha engedélyezte a figyelést, az Azure a modellből gyűjt telemetria adatokat a Azure IoT Edge modulban. A telemetria adatai csak Ön számára érhetők el, és a Storage-fiók példányában vannak tárolva.

Azure IoT Edge biztosítja, hogy a modul fut, és figyeli az azt üzemeltető eszközt.
. 
## <a name="automation"></a>Automatizálás

### <a name="azure-machine-learning-cli"></a>Azure Machine Learning parancssori felület 

A [Azure Machine learning CLI](reference-azure-machine-learning-cli.md) az Azure CLI egy platformfüggetlen parancssori felülete az Azure platformhoz. Ez a bővítmény parancsokat biztosít a gépi tanulási tevékenységek automatizálásához.

### <a name="ml-pipelines"></a>ML-folyamatok

[A gépi tanulási](concept-ml-pipelines.md) folyamatokkal olyan munkafolyamatokat hozhat létre és kezelhet, amelyek összegyűjtik a gépi tanulási fázisokat. Egy folyamat például magában foglalhatja az adatelőkészítést, a modell betanítását, a modell üzembe helyezését és a következtetések/pontozási fázisokat. Az egyes fázisok több lépést is magukban foglalhatnak, amelyek mindegyike felügyelet nélkül futtatható különböző számítási célokban. 

A folyamat lépései újrafelhasználhatók, és az előző lépések újbóli futtatása nélkül is futtathatók, ha a lépések eredménye nem módosult. Például áttaníthatja a modelleket anélkül, hogy újra kellene futtatnia a költséges adatelőkészítési lépéseket, ha az adatváltozás nem változott. A folyamatok azt is lehetővé teszik, hogy az adatszakértők működjenek együtt a gépi tanulási munkafolyamatok különálló területein való munka során.

## <a name="interacting-with-machine-learning"></a>Interakció a gépi tanulással

> [!IMPORTANT]
> Az alábbi megjelölésű eszközök (előzetes verzió) jelenleg nyilvános előzetes verzióban érhetők el.
> Az előzetes verzió szolgáltatási szintű szerződés nélkül van megadva, és nem ajánlott éles számítási feladatokhoz. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

+  A szolgáltatással bármilyen Python-környezetben dolgozhat a [Pythonhoz készült Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)-val.
+ A szolgáltatás használata bármely R-környezetben az R- [hez készült Azure Machine learning SDK](https://azure.github.io/azureml-sdk-for-r/reference/index.html) -val (előzetes verzió).
+ A [Azure Machine learning Designer (előzetes verzió)](concept-designer.md) használatával hajtsa végre a munkafolyamat lépéseit kód írása nélkül. ( [Vállalati munkaterület](concept-workspace.md#upgrade)) a Designer használatához szükséges.)
+ Az automatizáláshoz használja a [Azure Machine learning CLI](https://docs.microsoft.com/azure/machine-learning/reference-azure-machine-learning-cli) -t.
+ A [számos Modelles megoldás-gyorsító](https://aka.ms/many-models) (előzetes verzió) a Azure Machine Learningra épít, és lehetővé teszi több száz vagy akár több ezer gépi tanulási modell betanítását, üzemeltetését és felügyeletét.

## <a name="next-steps"></a>További lépések

A Azure Machine Learning megkezdéséhez tekintse meg a következőt:

* [Mi az Azure Machine Learning?](overview-what-is-azure-ml.md)
* [Azure Machine Learning munkaterület létrehozása](how-to-manage-workspace.md)
* [Oktatóanyag (1. rész): modell betanítása](tutorial-train-models-with-aml.md)
