---
title: Építészet & kulcsfontosságú fogalmak
titleSuffix: Azure Machine Learning
description: Ismerje meg az Azure Machine Learninget kifutó architektúrát, kifejezéseket, fogalmakat és munkafolyamatokat.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 03/17/2020
ms.custom: seoapril2019, seodec18
ms.openlocfilehash: 9f1d23f11cf73680a8861c9f1ac6cbd40ad497a4
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81257334"
---
# <a name="how-azure-machine-learning-works-architecture-and-concepts"></a>Az Azure Machine Learning működése: Architektúra és fogalmak

Ismerje meg az Azure Machine Learning architektúráját, fogalmait és munkafolyamatait. A szolgáltatás fő összetevői és a szolgáltatás használatának általános munkafolyamata az alábbi ábrán látható:

![Azure Machine Learning architektúra és munkafolyamat](./media/concept-azure-machine-learning-architecture/workflow.png)

## <a name="workflow"></a>Munkafolyamat

A gépi tanulási modell munkafolyamata általában ezt a sorozatot követi:

1. **Betanítás**
    + Gépi tanulási oktatószkripteket fejleszthet **Pythonban,** **R-ben**vagy a vizuális tervezővel.
    + Számítási cél létrehozása és **konfigurálása.**
    + **Küldje el a parancsfájlokat** egy konfigurált számítási célnak, amely ebben a környezetben fut. A betanítás során a parancsfájlok olvashatnak vagy írhatnak **az adattárakból.** A betanítás során létrehozott naplók és kimenetek a **munkaterületen** **fut,** és **kísérletek**alatt vannak csoportosítva.

1. **Csomag** - Miután kielégítő futtatást talált, regisztrálja a megőrzött modellt a **modell rendszerleíró adatbázisában.**

1. **A** - **kísérlet lekérdezése az** aktuális és a korábbi futtatások naplózott metrikáinak lekérdezése. Ha a metrikák nem jelzik a kívánt eredményt, hurkoljon vissza az 1.

1. **Üzembe helyezés** – Dolgozzon ki egy pontozási parancsfájlt, amely a modellt használja, és **a modell üzembe helyezése** **webszolgáltatásként** az Azure-ban, vagy egy **IoT Edge-eszköz.**

1. **Figyelő** – A betanítási adatkészlet és az üzembe helyezett modell adatok közötti **adateltolódás** figyelése. Ha szükséges, lépjen vissza az 1.

## <a name="tools-for-azure-machine-learning"></a>Eszközök az Azure Machine Learninghez

Használja ezeket az eszközöket az Azure Machine Learninghez:

+  A szolgáltatás bármely Python-környezetben az [Azure Machine Learning SDK python-hoz.](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
+ A szolgáltatás bármely R-környezetben kommunikálhat az [R-hez.](https://azure.github.io/azureml-sdk-for-r/reference/index.html)
+ Automatizálja gépi tanulási tevékenységeit az [Azure Machine Learning CLI segítségével.](https://docs.microsoft.com/azure/machine-learning/reference-azure-machine-learning-cli)
+ Az [Azure Machine Learning tervezője (előzetes verzió)](concept-designer.md) használatával kód írása nélkül hajthatja végre a munkafolyamat lépéseit.


> [!NOTE]
> Bár ez a cikk az Azure Machine Learning által használt kifejezéseket és fogalmakat határozza meg, nem határozza meg az Azure platform feltételeit és fogalmait. Az Azure platformterminológiájáról a [Microsoft Azure szószedetében](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology)talál további információt.

## <a name="glossary"></a>Szószedet

* [Tevékenység](#activities)
* [Munkaterület](#workspaces)
    * [Kísérletek](#experiments)
        * [Futtassa a következőt:](#runs) 
            * [Konfiguráció futtatása](#run-configurations)
            * [Pillanatkép](#snapshots)
            * [Git-követés](#github-tracking-and-integration)
            * [Naplózás](#logging)
    * [Gépi tanulási folyamatok](#ml-pipelines)
    * [Modellek](#models)
        * [Környezetek](#environments)
        * [Betanítási parancsfájl](#training-scripts)
        * [Becslők](#estimators)
    * [Végpontok](#endpoints)
        * [Webszolgáltatás](#web-service-endpoint)
        * [IoT-modulok](#iot-module-endpoints)
    * [Adatkészlet & adattárak](#datasets-and-datastores)
    * [Számítási célok](#compute-targets)

### <a name="activities"></a>Tevékenységek

A tevékenység hosszú ideig futó műveletet jelent. A következő műveletek példák a tevékenységekre:

* Számítási cél létrehozása vagy törlése
* Parancsfájl futtatása számítási célon

A tevékenységek értesítéseket adhatnak az SDK-n vagy a webes felhasználói felületen keresztül, így könnyen figyelheti a műveletek előrehaladását.

### <a name="workspaces"></a>Munkaterületek

[A munkaterület](concept-workspace.md) az Azure Machine Learning legfelső szintű erőforrása. Az Azure Machine Learning használatakor létrehozott összes összetevővel egy központosított helyet biztosít. A munkaterületet megoszthatja másokkal. A munkaterületek részletes leírását a [Mi az Azure Machine Learning-munkaterület.](concept-workspace.md)

### <a name="experiments"></a>Kísérletek

A kísérlet egy adott parancsfájlból származó számos futtatás csoportosítása. Mindig egy munkaterülethez tartozik. Amikor beküld egy futtatást, megad egy kísérlet nevét. A futtatáshoz szükséges adatokat a kísérlet tárolja. Ha beküld egy futtatást, és olyan kísérletnevet ad meg, amely nem létezik, a rendszer automatikusan új kísérletet hoz létre ezzel az újonnan megadott névvel.

Egy kísérlet használatának például az [Oktatóanyag: Az első modell betanítása](tutorial-1st-experiment-sdk-train.md)című témakörben látható.

### <a name="runs"></a>Futtatás

A futtatás egy betanítási parancsfájl egyetlen végrehajtása. A kísérletek általában több futtatást tartalmaznak.

Az Azure Machine Learning összes enfut, és tárolja a következő információkat a kísérletben:

* A futtatás metaadatai (időbélyeg, időtartam és így tovább)
* A parancsfájl által naplózott mérőszámok
* A kísérlet által automatikusan gyűjtött vagy az Ön által kifejezetten feltöltött kimeneti fájlok
* A parancsfájlokat tartalmazó könyvtár pillanatképe a futtatás előtt

Akkor hoz létre egy futtatást, amikor beküld egy parancsfájlt egy modell betanításához. Egy futtatáshoz nulla vagy több gyermekfuttatás is lehet. A legfelső szintű futtatáshoz például két gyermek futtatása lehet, amelyek mindegyike saját gyermekfuttatással rendelkezhet.

### <a name="run-configurations"></a>Konfigurációk futtatása

A futtatási konfiguráció olyan utasítások készlete, amelyek meghatározzák, hogyan kell futtatni egy parancsfájlt egy megadott számítási célban. A konfiguráció viselkedésdefiníciók széles körét tartalmazza, például azt, hogy egy meglévő Python-környezetet vagy egy specifikációból létrehozott Conda-környezetet használjon.The configuration includes a wide set of behavior definitions, such is whether to use an existing Python environment or to use a Conda environment that's built from a specification.

A futtatási konfiguráció megőrizhető a könyvtárban található olyan fájlban, amely a betanítási parancsfájlt tartalmazza, vagy memóriabeli objektumként is létrehozható, és futtatás küldésére használható.

Például futtassa a konfigurációkat, olvassa el [a Számítási cél kiválasztása és használata a modell betanításához című témakört.](how-to-set-up-training-targets.md)

### <a name="snapshots"></a>Pillanatképek

Futtatás kor az Azure Machine Learning tömöríti a parancsfájlt zip-fájlként tartalmazó könyvtárat, és elküldi azt a számítási célnak. A zip fájlt ezután kibontják, és a parancsfájl ott fut. Az Azure Machine Learning a zip-fájlt pillanatképként is tárolja a futtatási rekord részeként. Bárki, aki hozzáfér a munkaterülethez, tallózhat egy futtatási rekordban, és letöltheti a pillanatképet.

> [!NOTE]
> Ha meg szeretné akadályozni, hogy a szükségtelen fájlok bekerüljenek a pillanatképbe, készítsen figyelmen kívül hagyásfájlt (.gitignore vagy .amlignore). Helyezze el ezt a fájlt a Pillanatkép könyvtárba, és adja hozzá a figyelmen kívül hagyandó fájlneveket. Az .amlignore fájl [ugyanazt a szintaxist és mintázatot használja, mint a .gitignore fájl](https://git-scm.com/docs/gitignore). Ha mindkét fájl létezik, az .amlignore fájl élvez elsőbbséget.

### <a name="github-tracking-and-integration"></a>GitHub nyomon követése és integrációja

Amikor elindítja a betanítási futtatás, ahol a forráskönyvtár egy helyi Git-tárház, a tárház adatait a futtatási előzmények tárolja. Ez egy estimator, ML-folyamat vagy parancsfájlfuttatással elküldött futtatásokkal működik. Az SDK-ból vagy a Machine Learning CLI-ből küldött futtatások esetén is működik.

További információ: [Git integration for Azure Machine Learning.](concept-train-model-git-integration.md)

### <a name="logging"></a>Naplózás

A megoldás fejlesztése során használja az Azure Machine Learning Python SDK-t a Python-parancsfájlban tetszőleges metrikák naplózásához. A futtatás után a metrikák lekérdezése annak megállapítására, hogy a futtatás hozta létre a telepíteni kívánt modellt.

### <a name="ml-pipelines"></a>ML-folyamatok

A gépi tanulási folyamatok segítségével hozhat létre és kezelhet olyan munkafolyamatokat, amelyek összefűzik a gépi tanulási fázisokat. Egy folyamat például adatelőkészítést, modellbetanítást, modelltelepítést és következtetési/pontozási fázisokat tartalmazhat. Minden fázis több lépést is magában foglalhat, amelyek mindegyike felügyelet nélkül futtatható különböző számítási célokban. 

A folyamat lépései újrafelhasználhatók, és az előző lépések újrafuttatása nélkül futtathatók, ha a lépések kimenete nem változott. Például újrabetanítása egy modell nélkül újrafuttatása költséges adat-előkészítési lépéseket, ha az adatok nem változtak. A folyamatok lehetővé teszik az adatszakértők számára, hogy együttműködjenek, miközben egy gépi tanulási munkafolyamat különböző területein dolgoznak.

A szolgáltatással rendelkező gépi tanulási folyamatokról a [Folyamatok és az Azure Machine Learning](concept-ml-pipelines.md)című témakörben talál további információt.

### <a name="models"></a>Modellek

A legegyszerűbb, a modell egy kóddarab, amely egy bemeneti és kimeneti eredményez. A gépi tanulási modell létrehozása magában foglalja egy algoritmus kiválasztását, adatok biztosítását és a hiperparaméterek finomhangolását. A betanítás egy iteratív folyamat, amely egy betanított modellt hoz létre, amely magában foglalja a modell által a betanítási folyamat során tanult.

A modellt az Azure Machine Learning egy futtatása állítja elő. Az Azure Machine Learningen kívül betanított modellt is használhat. Egy modell regisztrálhat egy Azure Machine Learning-munkaterületen.

Az Azure Machine Learning a keretrendszer agnosztikus. Amikor létrehoz egy modellt, bármilyen népszerű gépi tanulási keretrendszert használhat, például a Scikit-learn, az XGBoost, a PyTorch, a TensorFlow és a Chainer.

Egy példa a scikit-learn és egy becslést használó modell betanítására: [Oktatóanyag: Képbesorolási modell betanítása az Azure Machine Learning használatával című témakörben.](tutorial-train-models-with-aml.md)

A **modell beállításjegyzék** nyomon követi az Azure Machine Learning-munkaterületen lévő összes modellt.

A modelleket név és verzió alapján azonosítják. Minden alkalommal, amikor regisztrál egy modellt, amelynek neve megegyezik egy meglévővel, a beállításjegyzék feltételezi, hogy ez egy új verzió. A verzió növekszik, és az új modell ugyanazon a néven van regisztrálva.

A modell regisztrálásakor további metaadat-címkéket adhat meg, majd a címkéket használhatja a modellek keresésekor.

> [!TIP]
> A regisztrált modell a modellt egy vagy több fájlt tartalmazó logikai tároló. Ha például egy modell, amely több fájlban van tárolva, regisztrálhatja őket egyetlen modellként az Azure Machine Learning-munkaterületen. A regisztráció után letöltheti vagy telepítheti a regisztrált modellt, és megkaphatja a regisztrált összes fájlt.

Egy aktív központi telepítés által használt regisztrált modell nem törölhető.

Egy modell regisztrálása például: [Képbesorolási modell betanítása az Azure Machine Learning segítségével című témakörben.](tutorial-train-models-with-aml.md)

### <a name="environments"></a>Környezetek

Az Azure ML-környezetek a konfiguráció (Docker / Python / Spark / stb.) megadására szolgálnak, amelyek reprodukálható környezetet hoznak létre az adatok előkészítéséhez, a modellbetanításhoz és a modellkiszolgáláshoz. Ezek felügyelt és verziózott entitások az Azure Machine Learning-munkaterületen belül, amelyek lehetővé teszik a reprodukálható, auditálható és hordozható gépi tanulási munkafolyamatok különböző számítási célok között.

A helyi számítási környezetben egy környezeti objektum segítségével fejlesztheti a betanítási parancsfájlt, újra felhasználhatja ugyanazt a környezetet az Azure Machine Learning Compute-ban a modellnagyításhoz, és akár üzembe is helyezheti a modellt ugyanazzal a környezettel. 

Ismerje [meg, hogyan hozhat létre és kezelhet újrafelhasználható pénzmosási környezetet](how-to-use-environments.md) a képzéshez és a következtetéshez.

### <a name="training-scripts"></a>Betanítási parancsfájlok

Modell betanításához adja meg a betanítási parancsfájlt és a kapcsolódó fájlokat tartalmazó könyvtárat. Megadhat egy kísérlet nevet is, amely a betanítás során gyűjtött adatok tárolására szolgál. A betanítás során a teljes könyvtár a betanítási környezetbe (számítási cél), és a parancsfájl, amely a futtatási konfiguráció által meghatározott elindul. A könyvtár pillanatképe is a munkaterületen tárolja a kísérlet alatt.

Például az [Oktatóanyag: Képbesorolási modell betanítása az Azure Machine Learning segítségével című témakörben.](tutorial-train-models-with-aml.md)

### <a name="estimators"></a>Becslők

A modellbetanítás megkönnyítése érdekében a becslőosztály lehetővé teszi a futtatási konfigurációk egyszerű megalkotását. Létrehozhat és használhat egy általános [becseseket,](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) hogy olyan képzési parancsfájlokat küldjön be, amelyek bármilyen választott tanulási keretrendszert használnak (például scikit-learn).

A PyTorch, TensorFlow és Chainer feladatok hoz, az Azure Machine Learning is rendelkezik a megfelelő [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)és [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) becslések egyszerűsítése ezen keretrendszerek használatával.

További információkért tekintse át a következő cikkeket:

* [Az ML modellek betanítása becsléssel](how-to-train-ml-models.md).
* [Az Azure Machine Learning segítségével nagy méretekben tanítsa be a Pytorch mélytanulási modelljeit.](how-to-train-pytorch.md)
* [Az Azure Machine Learning segítségével nagy méretekben taníthatja be és regisztrálhatja a TensorFlow-modelleket.](how-to-train-tensorflow.md)
* [Az Azure Machine Learning segítségével nagy méretekben taníthatja be és regisztrálhatja a Chainer-modelleket.](how-to-train-ml-models.md)

### <a name="endpoints"></a>Végpontok

A végpont a modell példányosítása a felhőben üzemeltethető webszolgáltatásba vagy egy Integrált eszközközponti telepítéshez használható IoT-modulba.

#### <a name="web-service-endpoint"></a>Webszolgáltatás végpontja

Egy modell webszolgáltatásként történő üzembe helyezésekor a végpont telepíthető az Azure Container Instances, az Azure Kubernetes-szolgáltatás vagy fpga-k. A szolgáltatást a modellből, a parancsfájlból és a kapcsolódó fájlokból hozhatja létre. Ezek egy alaptárolórendszerképbe kerülnek, amely a modell végrehajtási környezetét tartalmazza. A rendszerkép egy terheléselosztással kiegyensúlyozott, HTTP-végpont, amely megkapja a pontozási kérelmeket, amelyek a webszolgáltatásnak küldött.

Az Azure segít a webszolgáltatás figyelésében az Application Insights telemetriai adatok vagy modell telemetriai adatok gyűjtésével, ha úgy döntött, hogy engedélyezi ezt a funkciót. A telemetriai adatok csak Az Ön számára érhető el, és az Application Insights és a tárfiók példányai tárolja.

Ha engedélyezte az automatikus skálázást, az Azure automatikusan méretezi a központi telepítést.

Egy modell webszolgáltatásként való üzembe helyezésének például: [Lemezképbesorolási modell üzembe helyezése az Azure Container Instances alkalmazásban](tutorial-deploy-models-with-aml.md)című témakörben talál.

#### <a name="iot-module-endpoints"></a>IoT-modul végpontjai

Az üzembe helyezett IoT-modul végpontja egy Docker-tároló, amely tartalmazza a modell és a kapcsolódó parancsfájl vagy alkalmazás, valamint minden további függőségek. Ezeket a modulokat az Azure IoT Edge peremhálózati eszközökön való üzembe helyezésével telepítheti.

Ha engedélyezte a figyelést, az Azure telemetriai adatokat gyűjt a modellből az Azure IoT Edge modulon belül. A telemetriai adatok csak az Ön számára érhető el, és a tárfiók példányban tárolja.

Az Azure IoT Edge biztosítja, hogy a modul fut, és figyeli az eszközt, amely üzemelteti.


### <a name="compute-instance-preview"></a><a name="compute-instance"></a>Számítási példány (előzetes verzió)

Az **Azure Machine Learning számítási példány** (korábbi néven Notebook VM) egy teljes körűen felügyelt felhőalapú munkaállomás, amely több eszközt és gépi tanuláshoz telepített környezetet tartalmaz. Számítási példányok számítási célként használhatók a betanításhoz és a feladatok hozadékoláshoz. Nagy feladatok esetén az [Azure Machine Learning számítási fürtök](how-to-set-up-training-targets.md#amlcompute) többcsomópontos skálázási képességekkel jobb számítási célválasztás.

További információ a [számítási példányokról.](concept-compute-instance.md)

### <a name="datasets-and-datastores"></a>Adatkészletek és adattárak

**Az Azure Machine Learning-adatkészletek** (előzetes verzió) megkönnyítik az adatok elérését és az okkal való munkát. Az adatkészletek különböző forgatókönyvekben, például a modellbetanításban és a folyamatlétrehozásban kezelik az adatokat. Az Azure Machine Learning SDK használatával elérheti az alapul szolgáló tárhelyet, adatokat fedezhet fel, és kezelheti a különböző adatkészlet-definíciók életciklusát.

Az adatkészletek lekínálják az adatok népszerű formátumban történő használatát, például a használatával `from_delimited_files()` vagy `to_pandas_dataframe()`a használatával.

További információ: [Azure Machine Learning-adatkészletek létrehozása és regisztrálása.](how-to-create-register-datasets.md)  Az adatkészletek használatára vonatkozó további [példákat a mintajegyzetfüzetekben](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/datasets)talál.

Az **adattár** egy tárolási absztrakció egy Azure-tárfiók felett. Az adattár használhatja egy Azure blob tároló vagy egy Azure-fájlmegosztás, mint a háttér-tároló. Minden munkaterület rendelkezik egy alapértelmezett adattár, és regisztrálhat további adattárak. A Python SDK API-val vagy az Azure Machine Learning CLI használatával tárolja és olvassa be a fájlokat az adattárból.

### <a name="compute-targets"></a>Számítási célok

A [számítási cél](concept-compute-target.md) lehetővé teszi, hogy adja meg a számítási erőforrást, ahol futtatja a betanítási parancsfájlt, vagy a szolgáltatás központi telepítését. Ez a hely lehet a helyi gép vagy egy felhőalapú számítási erőforrás.

További információ a [betanításhoz és üzembe helyezéshez rendelkezésre álló számítási célokról.](concept-compute-target.md)

### <a name="next-steps"></a>További lépések

Az Azure Machine Learning első lépései:

* [Mi az Azure Machine Learning?](overview-what-is-azure-ml.md)
* [Azure Machine Learning-munkaterület létrehozása](how-to-manage-workspace.md)
* [Oktatóanyag (1. rész): Modell betanítása](tutorial-train-models-with-aml.md)
