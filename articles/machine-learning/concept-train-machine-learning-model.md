---
title: Modell képzési módszerek
titleSuffix: Azure Machine Learning
description: Ismerje meg a különböző módszereket, amelyekkel betaníthatja a modellt az Azure Machine Learning segítségével. A becsesbecslők egyszerű módot kínálnak a népszerű keretrendszerek, például a Scikit-learn, a TensorFlow, a Keras, a PyTorch és a Chainer segítségével való együttműködésre. A Machine Learning-folyamatok megkönnyítik a felügyelet nélküli futtatások ütemezését, a heterogén számítási környezetek használatát és a munkafolyamat egyes részeinek újrafelhasználását. A konfigurációk futtatása pedig részletes vezérlést biztosít a számítási célok felett, amelyeken a betanítási folyamat fut.
services: machine-learning
ms.service: machine-learning
author: Blackmist
ms.author: larryfr
ms.subservice: core
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 68ad9cc47d68f7bc3ae952f7e458781cdc1c4ab2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79129772"
---
# <a name="train-models-with-azure-machine-learning"></a>Modellek betanítása az Azure Machine Learning segítségével

Az Azure Machine Learning számos lehetőséget kínál a modellek betanítására, az SDK-t használó kódelső megoldásoktól az alacsony kódú megoldásokig, például az automatizált gépi tanulásig és a vizuális tervezőig. Az alábbi lista segítségével meghatározhatja, hogy melyik betanítási módszer a megfelelő az Ön számára:

+ [Azure Machine Learning SDK python:](#python-sdk)A Python SDK számos lehetőséget biztosít a modellek betanítására, mindegyik különböző képességekkel.

    | Képzési módszer | Leírás |
    | ----- | ----- |
    | [Konfiguráció futtatása](#run-configuration) | A **modellek betanításának általános módja** a betanítási parancsfájl használata és a konfiguráció futtatása. A futtatási konfiguráció biztosítja a modell betanításához használt betanítási környezet konfigurálásához szükséges információkat. Vehet egy futtatási konfigurációt, a betanítási parancsfájlt és egy számítási célt (a betanítási környezet), és futtathat egy betanítási feladatot. |
    | [Automatizált gépi tanulás](#automated-machine-learning) | Az automatizált gépi tanulás lehetővé teszi, hogy **kiterjedt adatelemzési vagy programozási ismeretekkel rendelkező modelleket tanítson**be. Az adatelemzési és programozási háttérrel rendelkező személyek számára lehetővé teszi az idő és az erőforrások megtakarítását az algoritmusok kiválasztásának és a hiperparaméter-hangolásnak automatizálásával. Nem kell aggódnia a futtatási konfiguráció meghatározása miatt az automatizált gépi tanulás használatakor. |
    | [Becslők](#estimators) | A becses osztályok **megkönnyítik a modellek betanítását népszerű gépi tanulási keretek alapján.** Vannak estimator osztályok **Scikit-learn**, **PyTorch**, **TensorFlow**, és **Chainer**. Van egy általános becslő is, amely olyan keretrendszerekkel használható, amelyek még nem rendelkeznek külön becslési osztállyal. A becslések használatakor nem kell aggódnia a futtatási konfiguráció meghatározása miatt. |
    | [Gépi tanulási folyamat](#machine-learning-pipeline) | A folyamatok nem más betanítási módszer, hanem **egy munkafolyamat definiálásának módja moduláris, újrafelhasználható lépésekkel,** amelyek magukban foglalhatják a munkafolyamat részét is. A gépi tanulási folyamatok támogatják az automatizált gépi tanulást, a becsléseket, és a konfiguráció futtatását a modellek betanításához. Mivel a csővezetékek nem kifejezetten a képzésre összpontosítanak, a csővezeték használatának okai változatosabbak, mint a többi képzési módszer. Általában előfordulhat, hogy egy csővezeték, ha:<br>* Felügyelet **nélküli folyamatokat szeretne ütemezni,** mint például a hosszú ideig futó képzési feladatok vagy az adatok előkészítése.<br>* Használjon **több lépést,** amelyek koordinált között heterogén számítási erőforrások és tárolási helyeken.<br>* Használja a folyamatot **újrafelhasználható sablonként** bizonyos forgatókönyvekhez, például átképzéshez vagy kötegelt pontozáshoz.<br>* **Nyomon követheti és verziószámadatokat, bemeneteket és kimeneteket** követhet nyomon a munkafolyamathoz.<br>* A **munkafolyamatot különböző csapatok valósítják meg, amelyek egymástól függetlenül dolgoznak bizonyos lépéseken.** A lépések ezután összeilleszthetők egy folyamatban a munkafolyamat megvalósításához. |

+ [Azure Machine Learning SDK pythonhoz:](#r-sdk)Az SDK az újratiktetési csomagot használja az Azure Machine Learning Python SDK-hoz való kötéshez. Ez lehetővé teszi a python SDK-ban bármely R-környezetben megvalósított alapvető objektumok és metódusok elérését.

+ **Tervező:** Az Azure Machine Learning tervezője (előzetes verzió) egyszerű belépési pontot biztosít a gépi tanulásba a fogalmak gyártásigazolásához, vagy a kevés kódolási tapasztalattal rendelkező felhasználók számára. Ez lehetővé teszi, hogy a vonat modellek segítségével drag and drop web-alapú felhasználói felület. Használhatja a Python-kódot a terv részeként, vagy a vonat modellek kód írása nélkül.

+ **CLI**: A gépi tanulási CLI parancsokat biztosít az Azure Machine Learning gyakori feladataihoz, és gyakran használják **parancsfájlok futtatásához és automatizálásához.** Ha például létrehozott egy betanítási parancsfájlt vagy folyamatot, a CLI segítségével indíthat betanítási futtatást ütemezés szerint, vagy ha a betanításhoz használt adatfájlok frissülnek. A betanítási modellek, parancsokat biztosít, amelyek benyújtási feladatok. Futó konfigurációk vagy folyamatok használatával küldhet el feladatokat.

Ezek a betanítási módszerek mindegyike különböző típusú számítási erőforrásokat használhat a képzéshez. Ezeket az erőforrásokat együttesen [__számítási céloknak nevezzük.__](concept-azure-machine-learning-architecture.md#compute-targets) A számítási cél lehet egy helyi gép vagy egy felhőalapú erőforrás, például egy Azure Machine Learning Compute, Az Azure HDInsight vagy egy távoli virtuális gép.

## <a name="python-sdk"></a>Python SDK

Az Azure Machine Learning SDK python-hoz lehetővé teszi, hogy az Azure Machine Learning használatával gépi tanulási munkafolyamatokat hozzon létre és futtasson. A szolgáltatás egy interaktív Python-munkamenetből, jupyter-jegyzetfüzetekből, Visual Studio-kódból vagy más IDE-ből kommunikálhat.

* [Mi az Azure Machine Learning SDK pythonhoz](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Az SDK telepítése vagy frissítése](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* [Fejlesztői környezet konfigurálása az Azure Machine Learninghez](how-to-configure-environment.md)

### <a name="run-configuration"></a>Konfiguráció futtatása

Az Azure Machine Learning általános betanítási feladata definiálható a [RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py)használatával. A futtatási konfiguráció ezután a betanítási parancsfájl(ok) használatával egy számítási célon egy modell betanításához.

Lehet kezdeni egy futtatási konfiguráció a helyi számítógépen, majd váltson át egy felhőalapú számítási cél szükség szerint. A számítási cél módosításakor csak a használt futtatási konfigurációt módosítja. A futtatás a betanítási feladatadatait is naplózza, például a bemeneteket, a kimeneteket és a naplókat.

* [Mi az a futtatási konfiguráció?](concept-azure-machine-learning-architecture.md#run-configurations)
* [Oktatóanyag: Az első ML-modell betanítása](tutorial-1st-experiment-sdk-train.md)
* [Példák: Jupyter Notebook példák képzési modellek](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training)
* [Útmutató: Számítási célok beállítása és használata a modellbetanításhoz](how-to-set-up-training-targets.md)

### <a name="automated-machine-learning"></a>Automatizált gépi tanulás

Adja meg az ismétléseket, a hiperparaméter-beállításokat, a featurization-t és az egyéb beállításokat. A betanítás során az Azure Machine Learning különböző algoritmusokat és paramétereket próbál párhuzamosan. Az edzés akkor áll le, ha eléri a megadott kilépési feltételeket. A becslések használatakor nem kell aggódnia a futtatási konfiguráció meghatározása miatt.

> [!TIP]
> A Python SDK mellett az Azure Machine [Learning stúdión](https://ml.azure.com)keresztül is használhatja az automatikus ml-t.

* [Mi az automatizált gépi tanulás?](concept-automated-ml.md)
* [Oktatóanyag: Az első osztályozási modell létrehozása automatizált gépi tanulással](tutorial-first-experiment-automated-ml.md)
* [Oktatóanyag: Automatikus gépi tanulás használata a taxidíjak előrejelzéséhez](tutorial-auto-train-models.md)
* [Példák: Jupyter Notebook példák automatizált gépi tanulás](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)
* [Útmutató: Automatikus ML-kísérletek konfigurálása pythonban](how-to-configure-auto-train.md)
* [Útmutató: Idősorozat-előrejelzési modell automatikus betanítása](how-to-auto-train-forecast.md)
* [Útmutató: Automatizált gépi tanulási kísérletek létrehozása, felfedezése és üzembe helyezése az Azure Machine Learning stúdióval](how-to-use-automated-ml-for-ml-models.md)

### <a name="estimators"></a>Becslők

A becsesedések megkönnyítik a modellek betanítását a népszerű ml-es keretrendszerek használatával. Ha **scikit-learn**, **PyTorch**, **TensorFlow**vagy **Chainer**, akkor érdemes egy estimator a képzés. Van egy általános becslő is, amely olyan keretrendszerekkel használható, amelyek még nem rendelkeznek külön becslési osztállyal. A becslések használatakor nem kell aggódnia a futtatási konfiguráció meghatározása miatt.

* [Mik azok a becslők?](concept-azure-machine-learning-architecture.md#estimators)
* [Oktatóanyag: Képbesorolási modellek betanítása MNIST-adatokkal és scikit-learn használatával az Azure Machine Learning használatával](tutorial-train-models-with-aml.md)
* [Példák: Jupyter Notebook példák a becslők használatára](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning)
* [Útmutató: Becslés létrehozása a képzésben](how-to-train-ml-models.md)

### <a name="machine-learning-pipeline"></a>Gépi tanulási folyamat

A gépi tanulási folyamatok használhatják a korábban említett betanítási módszereket (konfiguráció futtatása, estimators, és automatizált gépi tanulás). A folyamatok inkább egy munkafolyamat létrehozásáról szólnak, így nem csak a modellek betanítását foglalják magukban. Egy folyamat, betaníthatja a modell automatizált gépi tanulás, estimators, vagy konfigurációk futtatása.

* [Mik azok a gépi folyamatok az Azure Machine Learningben?](concept-ml-pipelines.md)
* [Gépi tanulási folyamatok létrehozása és futtatása az Azure Machine Learning SDK-val](how-to-create-your-first-pipeline.md)
* [Oktatóanyag: Azure Machine Learning-folyamatok használata kötegelt pontozáshoz](tutorial-pipeline-batch-scoring-classification.md)
* [Példák: Jupyter Notebook példák gépi tanulási folyamatokhoz](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines)
* [Példák: Folyamat automatizált gépi tanulással](https://aka.ms/pl-automl)
* [Példák: Csővezeték becslőkkel](https://aka.ms/pl-estimator)

## <a name="r-sdk"></a>R SDK

Az R SDK lehetővé teszi az R-nyelv használatát az Azure Machine Learning használatával. Az SDK a retikulálási csomagot használja az Azure Machine Learning Python SDK-hoz való kötéshez. Ez lehetővé teszi a python SDK-ban bármely R-környezetben megvalósított alapvető objektumok és metódusok elérését.

További információkért tekintse át a következő cikkeket:

* [Oktatóanyag: Logisztikai regressziós modell létrehozása](tutorial-1st-r-experiment.md)
* [Azure Machine Learning SDK R-referenciához](https://azure.github.io/azureml-sdk-for-r/index.html)

## <a name="azure-machine-learning-designer"></a>Az Azure Machine Learning tervezője

A tervező lehetővé teszi, hogy a modelleket a webböngészőben húzási felülethasználatával tanítsa be.

+ [Mi a tervező?](concept-designer.md)
+ [Bemutató : Megjósolni autó ára](tutorial-designer-automobile-price-train-score.md)
+ [Regresszió: Ár előrejelzése](how-to-designer-sample-regression-automobile-price-basic.md)
+ [Besorolás: A jövedelem előrejelzése](how-to-designer-sample-classification-predict-income.md)
+ [Besorolás: Megjósolni lemorzsolódás, étvágygerjesztő, és up-selling](how-to-designer-sample-classification-churn.md)
+ [Besorolás egyéni R-parancsfájllal: A járatkésések előrejelzése](how-to-designer-sample-classification-flight-delay.md)
+ [Szövegbesorolás: Wikipedia SP 500 adatkészlet](how-to-designer-sample-text-classification.md)

## <a name="cli"></a>parancssori felület

A gépi tanulási CLI az Azure CLI bővítménye. Platformfüggetlen CLI-parancsokat biztosít az Azure Machine Learning használatával való munkához. A CLI általában a feladatok automatizálására, például egy gépi tanulási modell betanítására használható.

* [A CLI-bővítmény használata az Azure Machine Learninghez](reference-azure-machine-learning-cli.md)
* [MLOps az Azure-ban](https://github.com/microsoft/MLOps)

## <a name="next-steps"></a>További lépések

További információ a [képzési környezetek beállításáról.](how-to-set-up-training-targets.md)
