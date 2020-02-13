---
title: Architektúra & főbb fogalmak
titleSuffix: Azure Machine Learning
description: Ismerkedjen meg a Azure Machine Learning alkotó architektúrával, kifejezésekkel, fogalmakkal és munkafolyamatokkal.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 12/27/2019
ms.custom: seodec18
ms.openlocfilehash: 5f5522201534a54f5d132257553469eed5addab3
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77169870"
---
# <a name="how-azure-machine-learning-works-architecture-and-concepts"></a>A Azure Machine Learning működése: architektúra és fogalmak

Ismerje meg a Azure Machine Learning architektúráját, fogalmait és munkafolyamatát. A szolgáltatás fő összetevői és a szolgáltatás használatának általános munkafolyamata az alábbi ábrán látható:

![Azure Machine Learning architektúra és munkafolyamat](./media/concept-azure-machine-learning-architecture/workflow.png)

## <a name="workflow"></a>Munkafolyamat

A Machine learning-modell munkafolyamata általában az alábbi sorozatot követi:

1. **Vonat**
    + Gépi tanulással kapcsolatos oktatóanyagokat fejleszthet a **Pythonban** vagy a Visual Designerben.
    + Hozzon létre és konfiguráljon egy **számítási célt**.
    + **Küldje el a parancsfájlokat** a konfigurált számítási célhoz, hogy az adott környezetben fusson. A betanítás során a parancsfájlok beolvashatók vagy írhatók az **adattárba**. A végrehajtási rekordok a **munkaterületen** **futnak** , és a **kísérletek**alatt csoportosítva lesznek.

1. **Csomag** – a megfelelő Futtatás után regisztrálja a megőrzött modellt a **modell beállításjegyzékében**.

1. **Ellenőrizze** - a naplózott metrikák **kísérletét** az aktuális és a korábbi futtatásokból. Ha a mérőszámok nem jelzik a kívánt eredményt, lépjen vissza az 1. lépéshez, és ismételje meg a parancsfájlokat.

1. **Üzembe helyezés** – a modellt használó pontozási szkript fejlesztése és **a modell üzembe helyezése** **webszolgáltatásként** az Azure-ban vagy egy **IoT Edge eszközön**.

1. **Figyelő** – figyelő a betanítási adatkészlet és a központilag telepített modellbe tartozó adat **adateltolódása** között. Ha szükséges, lépjen vissza az 1. lépésre a modell új betanítási adattal való újratanításához.

## <a name="tools-for-azure-machine-learning"></a>Azure Machine Learning eszközök

Ezeket az eszközöket Azure Machine Learning használhatja:

+  A szolgáltatással bármilyen Python-környezetben dolgozhat a [Pythonhoz készült Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)-val.
+ Az r- [Azure Machine learning SDK](https://azure.github.io/azureml-sdk-for-r/reference/index.html)-val bármely r-környezetben dolgozhat a szolgáltatással.
+ A gépi tanulási tevékenységek automatizálása a [Azure Machine learning parancssori](https://docs.microsoft.com/azure/machine-learning/reference-azure-machine-learning-cli)felülettel.
+ A [Azure Machine learning Designer (előzetes verzió)](concept-designer.md) használatával hajtsa végre a munkafolyamat lépéseit kód írása nélkül.


> [!NOTE]
> Bár ez a cikk az Azure Machine Learning által használt feltételeket és fogalmakat határozza meg, nem határozza meg az Azure platformra vonatkozó feltételeket és fogalmakat. További információ az Azure platform terminológiáról: [Microsoft Azure Szószedet](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology).

## <a name="glossary"></a>Szószedet
+ <a href="#activities">Tevékenység</a>
+ <a href="#compute-targets">Számítási célok</a>
+ <a href="#datasets-and-datastores">Adathalmaz & adattárolók</a>
+ <a href="#endpoints">Végpontok</a>
+ <a href="#environments">Környezetben</a>
+ [Becslések](#estimators)
+ <a href="#experiments">Kísérletek</a>
+ <a href="#github-tracking-and-integration">Git-követés</a>
+ <a href="#iot-module-endpoints">IoT modulok</a>
+ <a href="#logging">Logging</a>
+ <a href="#ml-pipelines">ML-folyamatok</a>
+ <a href="#models">Modellek</a>
+ <a href="#runs">Futtassa</a>
+ <a href="#run-configurations">Konfiguráció futtatása</a>
+ <a href="#snapshots">Pillanatkép</a>
+ <a href="#training-scripts">Betanítási szkript</a>
+ <a href="#web-service-endpoint">Webszolgáltatások</a>
+ <a href="#workspaces">Munkaterület</a>

### <a name="activities"></a>Tevékenységek

Egy tevékenység egy hosszú ideig futó művelet jelöli. A következő műveleteket a példák a tevékenységeket:

* Létrehozása vagy törlése egy számítási célnak
* Egy parancsfájlt futtat egy számítási célnak

A tevékenységek az SDK-n vagy a webes felületen keresztül nyújthatnak értesítéseket, így könnyen nyomon követheti a műveletek előrehaladását.

### <a name="compute-instance"></a>Számítási példány (előzetes verzió)


A **Azure Machine learning számítási példány** (korábban notebook VM) egy teljes körűen felügyelt felhőalapú munkaállomás, amely több eszközt és környezetet is tartalmaz a gépi tanuláshoz. A számítási példányok számítási célként használhatók a betanítási és a következtetési feladatokhoz. A nagyméretű feladatokhoz Azure Machine Learning a többcsomópontos skálázási képességekkel rendelkező [számítási fürtök](how-to-set-up-training-targets.md#amlcompute) jobb számítási célt választhatnak.

További információ a [számítási példányokról](concept-compute-instance.md).

### <a name="compute-targets"></a>Számítási célok

A [számítási cél](concept-compute-target.md) segítségével megadhatja azt a számítási erőforrást, amelyben a képzési parancsfájlt futtatja, vagy a szolgáltatás központi telepítését üzemelteti. Ez a hely lehet a helyi számítógép vagy egy felhőalapú számítási erőforrás.

További információ a [képzéshez és az üzembe helyezéshez rendelkezésre álló számítási célokról](concept-compute-target.md).

### <a name="datasets-and-datastores"></a>Adatkészletek és adattárolók

**Azure Machine learning adatkészletek** (előzetes verzió) segítségével könnyebben férhet hozzá az adataihoz, és dolgozhat velük. Az adathalmazok különböző forgatókönyvekben, például a modell betanítása és a folyamat létrehozása során kezelik az adatokat. Az Azure Machine Learning SDK használatával elérheti a mögöttes tárolót, feltárhatja az adatokat, és kezelheti a különböző adatkészlet-definíciók életciklusát.

Az adatkészletek olyan módszereket biztosítanak a népszerű formátumokban való használathoz, mint például a `from_delimited_files()` vagy a `to_pandas_dataframe()`használata.

További információ: [Azure Machine learning adatkészletek létrehozása és regisztrálása](how-to-create-register-datasets.md).  Az adatkészletek használatával kapcsolatos további példákért tekintse meg a [minta jegyzetfüzeteket](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/datasets).

Az **adattár** egy Azure Storage-fiókon keresztüli adattárolási absztrakt. Az adattár egy Azure BLOB-tárolót vagy egy Azure-fájlmegosztást használhat háttérbeli tárolóként. Minden munkaterülethez tartozik egy alapértelmezett adattár, amely további adattárolókat is regisztrálhat. A Python SDK API-val vagy a Azure Machine Learning CLI-vel fájlokat tárolhat és kérhet le az adattárból.

### <a name="endpoints"></a>Végpontok

A végpontok a modell példányai egy olyan webszolgáltatásba, amely a felhőben vagy egy IoT modulban üzemeltethető az integrált eszközök üzembe helyezéséhez.

#### <a name="web-service-endpoint"></a>Webszolgáltatás végpontja

A modell webszolgáltatásként való telepítésekor a végpont Azure Container Instances, Azure Kubernetes Service-ben vagy FPGA-ben is üzembe helyezhető. A szolgáltatást a modellből, a parancsfájlból és a társított fájlokból hozza létre. Ezek egy alapszintű tároló-rendszerképbe kerülnek, amely a modell végrehajtási környezetét tartalmazza. A rendszerkép egy elosztott terhelésű, HTTP-végponttal rendelkezik, amely a webszolgáltatásnak küldött pontozási kérelmeket fogadja.

Az Azure segít a webszolgáltatás figyelésében Application Insights telemetria vagy modell telemetria gyűjtésével, ha a funkció engedélyezését választotta. A telemetria adatai csak Ön számára érhetők el, és a Application Insights és a Storage-fiók példányaiban tárolódnak.

Ha engedélyezte az automatikus skálázást, az Azure automatikusan méretezi az üzembe helyezést.

Egy modell webszolgáltatásként való üzembe helyezésére példa: [lemezkép besorolási modell telepítése Azure Container Instancesban](tutorial-deploy-models-with-aml.md).

#### <a name="iot-module-endpoints"></a>IoT modul végpontjai

Az üzembe helyezett IoT-végpont egy olyan Docker-tároló, amely tartalmazza a modellt, valamint a hozzá tartozó parancsfájlt vagy alkalmazást, valamint a további függőségeket. Ezeket a modulokat a Azure IoT Edge Edge-eszközökön történő üzembe helyezésével végezheti el.

Ha engedélyezte a figyelést, az Azure a modellből gyűjt telemetria adatokat a Azure IoT Edge modulban. A telemetria adatai csak Ön számára érhetők el, és a Storage-fiók példányában vannak tárolva.

Azure IoT Edge biztosítja, hogy a modul fut, és figyeli az azt üzemeltető eszközt.

### <a name="environments"></a>Környezetek

Az Azure ML-környezetek a reprodukálható környezet létrehozásához használt konfiguráció (Docker/Python/Spark/etc) megadására szolgálnak az adatelőkészítés, a modell betanítása és a modell kiszolgálása céljából. Ezek a Azure Machine Learning munkaterületen található felügyelt és verzióval rendelkező entitások, amelyek lehetővé teszik a reprodukálható, naplózható és hordozható gépi tanulási munkafolyamatokat a különböző számítási célok között.

A helyi számítási feladatokhoz környezeti objektumokat is használhat, hogy kifejlessze a betanítási parancsfájlt, újrahasznosítsa ugyanezt a környezetet Azure Machine Learning számítási modelleken a Modelles képzések esetében, és még a modellt is üzembe helyezheti ugyanazzal a környezettel. 

Megtudhatja, [hogyan hozhat létre és kezelhet újrahasznosítható ml-környezetet](how-to-use-environments.md) a képzéshez és a következtetésekhez.

### <a name="estimators"></a>Becslések

A kisegítő lehetőségek a népszerű keretrendszerekkel való modellezésének megkönnyítésére a kalkulátor osztály lehetővé teszi a futtatási konfigurációk egyszerű összeállítását. Létrehozhat és használhat általános [becslést](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) olyan képzési parancsfájlok beküldéséhez, amelyek bármely kiválasztott tanulási keretrendszert (például scikit-Learn) használnak.

A PyTorch, a TensorFlow és a láncolási feladatok esetében a Azure Machine Learning a megfelelő [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)és [láncolási](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) becslések is biztosít, hogy egyszerűbbé váljon ezen keretrendszerek használata.

További információkért tekintse át a következő cikkeket:

* [Ml modellek betanítása a becslések](how-to-train-ml-models.md).
* [A Pytorch mély tanulási modelljeinek Kiképzése Azure Machine learning](how-to-train-pytorch.md).
* [TensorFlow-modellek betanítása és regisztrálása Azure Machine learning](how-to-train-tensorflow.md).
* [A láncolt modelleket Kitaníthatja és regisztrálhatja Azure Machine learning](how-to-train-chainer.md).

### <a name="experiments"></a>Kísérletek

A kísérlet egy adott parancsfájlból származó számos Futtatás csoportosítása. Mindig tartozik egy munkaterülethez. Ha elküldi egy futtatás, meg kell adnia egy kísérlet neve. A kísérlet keretében a Futtatás adatait tárolja. Ha elküld egy futtatást, és megad egy kísérlet nevét, amely nem létezik, automatikusan létrejön egy új kísérlet az újonnan megadott névvel.

Egy kísérletre példaként tekintse meg az [oktatóanyag: az első modell betanítása](tutorial-1st-experiment-sdk-train.md)című témakört.


### <a name="github-tracking-and-integration"></a>GitHub-követés és-integráció

Ha olyan képzést indít el, ahol a forrás könyvtára helyi git-tárház, a rendszer a tárház adatait a futtatási előzményekben tárolja. Ez a szolgáltatás a kalkulátor, a ML folyamat vagy a szkript futtatásával elküldött futtatásokkal működik. Az SDK-ból vagy Machine Learning parancssori felületről küldött futtatások esetén is működik.

További információ: git- [integráció Azure Machine Learninghoz](concept-train-model-git-integration.md).

### <a name="logging"></a>Naplózás

A megoldás fejlesztésekor a Python-szkript Azure Machine Learning Python SDK-val tetszőleges metrikákat naplózhat. A Futtatás után a metrikák lekérdezésével állapítsa meg, hogy a Futtatás előkészítette-e a telepíteni kívánt modellt.

### <a name="ml-pipelines"></a>Gépi Tanulási folyamatok

A gépi tanulási folyamatokkal olyan munkafolyamatokat hozhat létre és kezelhet, amelyek összegyűjtik a gépi tanulási fázisokat. Egy folyamat például magában foglalhatja az adatelőkészítést, a modell betanítását, a modell üzembe helyezését és a következtetések/pontozási fázisokat. Az egyes fázisok is magában foglalja a több lépésből, amelyek mindegyike a különböző számítási célnak beavatkozás nélkül futtatható. 

A folyamat lépései újrafelhasználhatók, és a későbbi lépések futtatása nélkül is futtathatók, ha az adott lépés kimenete nem változott. Például áttaníthatja a modelleket anélkül, hogy újra kellene futtatnia a költséges adatelőkészítési lépéseket, ha az adatváltozás nem változott. A folyamatok azt is lehetővé teszik, hogy az adatszakértők működjenek együtt a gépi tanulási munkafolyamatok különálló területein való munka során.

A szolgáltatással folytatott gépi tanulási folyamatokkal kapcsolatos további információkért lásd: [folyamatok és Azure Machine learning](concept-ml-pipelines.md).

### <a name="models"></a>Modellek

A legegyszerűbb esetben egy modell olyan kódot, amely veszi a bemenetnek és kimenetet. Egy gépi tanulási modellek létrehozásával magában foglalja a algoritmusok kiválasztása, megadják a data és a hangolási hiperparaméterek. Képzési, amely a betanított modell, amely magában foglalja, mi a modell a betanítási folyamat során megtanult iteratív folyamat.

Futtatás az Azure Machine Learning-modell elő. A Azure Machine Learningon kívül betanított modellt is használhat. A modelleket Azure Machine Learning munkaterületen lehet regisztrálni.

Azure Machine Learning a Framework agnosztikus. Modell létrehozásakor bármilyen népszerű Machine learning-keretrendszert használhat, például a Scikit-Learn, a XGBoost, a PyTorch, a TensorFlow és a Chainer platformot.

A Scikit-Learn és a kalkulátort használó modellek képzését bemutató példát itt talál [: oktatóanyag: képbesorolási modell Betanítása Azure Machine learning](tutorial-train-models-with-aml.md)használatával.

A **modell beállításjegyzéke** nyomon követi a Azure Machine learning munkaterület összes modelljét.

Modellek nevét és verzióját azonosítja. Minden alkalommal, amikor egy olyan modellt regisztrál, amelynek a neve megegyezik egy meglévővel, a beállításjegyzék feltételezi, hogy ez egy új verzió. A verzió növekményes, és az új modell ugyanazon a néven van regisztrálva.

A modell regisztrálása esetén további metaadat-címkéket is megadhat, majd használhatja a címkéket a modellek keresésekor.

> [!TIP]
> A regisztrált modell a modellt alkotó egy vagy több fájl logikai tárolója. Ha például több fájlban tárolt modell van, akkor egyetlen modellként regisztrálhatja őket a Azure Machine Learning munkaterületen. A regisztráció után letöltheti vagy telepítheti a regisztrált modellt, és megkapja az összes regisztrált fájlt.

Egy aktív telepítés által használt regisztrált modell nem törölhető.

A modellek regisztrálására példát a [képbesorolási modell Betanítása Azure Machine Learningkal](tutorial-train-models-with-aml.md)című témakörben talál.

### <a name="runs"></a>Fut

A Futtatás egy képzési parancsfájl egyetlen végrehajtása. Azure Machine Learning rögzíti az összes futtatást, és a következő információkat tárolja:

* A futtatással kapcsolatos metaadatok (timestamp, időtartam stb.)
* A parancsfájl által naplózott metrikák
* A kísérlet által összegyűjtött vagy kifejezetten az Ön által feltöltött kimeneti fájlok
* A könyvtár, amely tartalmazza a parancsprogramok, a Futtatás előtt pillanatképét

A futtatást akkor kell létrehoznia, ha parancsfájlt küld a modell betanításához. Futtató rendelkezhet, nulla vagy több alárendelt futtatások. Előfordulhat például, hogy a legfelső szintű Futtatás két gyermeket futtat, amelyek mindegyike rendelkezhet saját gyermekével.

### <a name="run-configurations"></a>Konfigurációk futtatása

A futtatási konfiguráció olyan utasítások összessége, amelyek meghatározzák, hogy egy parancsfájl hogyan fusson egy adott számítási célhelyen. A konfiguráció számos viselkedési definíciót tartalmaz, például azt, hogy egy meglévő Python-környezetet kíván-e használni, vagy egy specifikáció alapján létrehozott Conda-környezetet használ-e.

A futtatási konfiguráció a betanítási parancsfájlt tartalmazó könyvtárban található fájlba is megtartható, vagy egy memóriában tárolt objektumként is létrehozható, és a Futtatás elküldésére használható.

A futtatási konfigurációkat például a [modell kitanítására szolgáló számítási cél kiválasztása és használata](how-to-set-up-training-targets.md)című témakörben tekintheti meg.
### <a name="snapshots"></a>Pillanatképek

Amikor elküld egy futtatást, Azure Machine Learning tömöríti azt a könyvtárat, amely a parancsfájlt zip-fájlként tartalmazza, és elküldi a számítási célnak. Ekkor a rendszer kibontja a zip-fájlt, és ott futtatja a parancsfájlt. Az Azure Machine Learning a zip-fájl is tárolja a futtatási rekord részeként pillanatképet készít. A munkaterület segítségével bárki futtatási rekord kereshetnek és a pillanatkép letöltése.

> [!NOTE]
> Ha meg szeretné akadályozni, hogy a felesleges fájlok szerepeljenek a pillanatképben, ne hagyja figyelmen kívül a fájlt (. gitignore vagy. amlignore). Helyezze el ezt a fájlt a pillanatkép-könyvtárba, és adja hozzá a fájlneveket, hogy figyelmen kívül hagyja. A. amlignore fájl ugyanazokat a [szintaxist és mintákat használja, mint a. gitignore fájl](https://git-scm.com/docs/gitignore). Ha mindkét fájl létezik, a. amlignore fájl elsőbbséget élvez.

### <a name="training-scripts"></a>Betanítási szkriptekhez

A modell betanítását, meg kell adni a tanítási szkriptet és a kapcsolódó fájlokat tartalmazó könyvtárra. Megadhat egy kísérlet nevét is, amely a betanítás során összegyűjtött információk tárolására szolgál. A betanítás során a rendszer átmásolja a teljes könyvtárat a betanítási környezetre (számítási célra), a futtatási konfiguráció által megadott parancsfájlt pedig elindítják. A könyvtár pillanatképet is tárolja a munkaterületen a kísérlet keretében.

Példaként tekintse meg az [oktatóanyag: képbesorolási modell Betanítása Azure Machine Learningkal](tutorial-train-models-with-aml.md)című témakört.

### <a name="workspaces"></a>Munkaterületek

[A munkaterület](concept-workspace.md) a Azure Machine learning legfelső szintű erőforrása. Központi helyet biztosít a Azure Machine Learning használatakor létrehozott összes összetevővel való együttműködéshez. A munkaterületeket másokkal is megoszthatja. A munkaterületek részletes ismertetését lásd: [Mi az Azure Machine learning munkaterület?](concept-workspace.md).

### <a name="next-steps"></a>Következő lépések

A Azure Machine Learning megkezdéséhez tekintse meg a következőt:

* [Mi az Azure Machine Learning?](overview-what-is-azure-ml.md)
* [Azure Machine Learning munkaterület létrehozása](how-to-manage-workspace.md)
* [Oktatóanyag (1. rész): modell betanítása](tutorial-train-models-with-aml.md)
