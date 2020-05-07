---
title: '& Train models készítése'
titleSuffix: Azure Machine Learning
description: Ismerje meg a különböző módszereket, amelyekkel a modelleket betaníthatja Azure Machine Learning használatával. A becslések egyszerű módszert kínál a népszerű keretrendszerek, például a Scikit-Learn, a TensorFlow, a kerasz, a PyTorch és a Chainer használatával való együttműködésre. Machine Learning a folyamatok megkönnyítik a felügyelet nélküli futtatások beosztását, a heterogén számítási környezetek használatát, valamint a munkafolyamat részeinek újrafelhasználását. A és a Run konfigurációk részletes szabályozást biztosítanak azokon a számítási céloknál, amelyeken a betanítási folyamat fut.
services: machine-learning
ms.service: machine-learning
author: Blackmist
ms.author: larryfr
ms.subservice: core
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: c75c41012928b7bffb61a00a73f314e4c372b154
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82792343"
---
# <a name="train-models-with-azure-machine-learning"></a>Modellek betanítása Azure Machine Learning

A Azure Machine Learning számos lehetőséget kínál a modellek betanítására, az SDK-t az SDK-t használó alacsony kódú megoldások, például az automatizált gépi tanulás és a vizuális tervező használatával. Az alábbi lista segítségével meghatározhatja, hogy melyik betanítási módszer legyen a legmegfelelőbb:

+ [Pythonhoz készült Azure Machine learning SDK](#python-sdk): a Python SDK számos módszert kínál a különböző képességekkel rendelkező modellek betanításához.

    | Betanítási módszer | Leírás |
    | ----- | ----- |
    | [Konfiguráció futtatása](#run-configuration) | A **modellek betanításának általános módja** egy képzési parancsfájl használata és a konfiguráció futtatása. A futtatási konfiguráció biztosítja a modell betanításához használt képzési környezet konfigurálásához szükséges információkat. Elvégezheti a futtatási konfigurációt, a betanítási szkriptet és a számítási célt (a képzési környezetet) és a betanítási feladatot. |
    | [Automatizált gépi tanulás](#automated-machine-learning) | Az automatizált gépi tanulás lehetővé teszi a **modellek széles körű adatelemzési és programozási ismeretek nélküli tanítását**. Az adatelemzési és-programozási hátterű felhasználók számára lehetővé teszi az idő és az erőforrások megtakarítását az algoritmus kiválasztásának és a hiperparaméter hangolásának automatizálásával. Az automatizált gépi tanulás használatakor nem kell aggódnia a futtatási konfiguráció definiálásával kapcsolatban. |
    | [Becslések](#estimators) | A kalkulátor osztályok megkönnyítik a **modellek betanítását a népszerű gépi tanulási keretrendszerek alapján**. A **Scikit-Learn**, a **PyTorch**, a **TensorFlow**és a **chainer**esetében a kalkulátor osztályok vannak. Létezik egy általános kalkulátor is, amely olyan keretrendszerek esetében használható, amelyek még nem rendelkeznek dedikált kalkulátor-osztállyal. A becslések használatakor nem kell aggódnia a futtatási konfiguráció definiálásával kapcsolatban. |
    | [Gépi tanulási folyamat](#machine-learning-pipeline) | A folyamatok nem egy másik betanítási módszer, hanem a **munkafolyamatok moduláris, újrafelhasználható lépésekkel való definiálásának módja**, amely magában foglalhatja a munkafolyamatok részét képező képzést is. A gépi tanulási folyamatokat az automatizált gépi tanulás, a becslések és a modellek betanítására szolgáló konfiguráció futtatásával támogatja. Mivel a folyamatok nem kifejezetten a képzésre összpontosítottak, a folyamat használatának okai a többi tanítási módszernél változatosabbek. Általában a következőket lehet használni:<br>* Olyan **felügyelet nélküli folyamatokat szeretne ütemezni** , mint például a hosszan futó betanítási feladatok vagy az adatok előkészítése.<br>* **Több olyan lépést** is használhat, amely heterogén számítási erőforrásokon és tárolási helyeken is össze van hangolva.<br>* Használja a folyamatot **újrafelhasználható sablonként** adott forgatókönyvekhez, például az újraképzéshez vagy a kötegelt pontozáshoz.<br>* A munkafolyamathoz tartozó **adatforrások, bemenetek és kimenetek nyomon követése és verziószáma** .<br>* A munkafolyamatot **különböző csapatok valósítják meg, amelyek egymástól függetlenül működnek**. A lépések ezután összekapcsolhatók egy folyamattal a munkafolyamat megvalósításához. |

+ [Python Azure Machine learning SDK](#r-sdk): az SDK a reticulate-csomagot használja a Azure Machine learning Python SDK-hoz való kötéshez. Ez lehetővé teszi a Python SDK-ban megvalósított alapvető objektumok és módszerek elérését bármely R-környezetből.

+ **Tervező**: a Azure Machine learning Designer (előzetes verzió) egyszerű belépési pontot biztosít a gépi tanuláshoz a fogalmak vagy a kis kódolási élményt biztosító felhasználók számára. Lehetővé teszi a modellek betanítását egy fogd és vidd webes KEZELŐFELÜLET használatával. A Python-kódokat használhatja a terv részeként, vagy betaníthatja a modelleket anélkül, hogy kódot kellene írnia.

+ **CLI**: a Machine learning parancssori felülete parancsokat biztosít a Azure Machine Learningekkel kapcsolatos gyakori feladatokhoz, és gyakran használják **parancsfájlok futtatására és automatizálására**. Ha például létrehozta a betanítási parancsfájlt vagy folyamatot, a CLI-vel elindíthatja a betanítást egy adott időpontban, vagy a betanításhoz használt adatfájlokat is frissíti. A betanítási modellek a betanítási feladatokat elküldő parancsokat biztosítanak. A futtatási konfigurációkkal vagy folyamatokkal küldhet feladatokat.

A képzési módszerek mindegyike különböző típusú számítási erőforrásokat használhat a betanításhoz. Ezeket az erőforrásokat együttesen [__számítási céloknak__](concept-azure-machine-learning-architecture.md#compute-targets)nevezzük. A számítási cél lehet egy helyi gép vagy egy felhőalapú erőforrás, például egy Azure Machine Learning számítási, Azure-HDInsight vagy távoli virtuális gép.

## <a name="python-sdk"></a>Python SDK

A Pythonhoz készült Azure Machine Learning SDK lehetővé teszi a gépi tanulási munkafolyamatok létrehozását és futtatását Azure Machine Learning használatával. Interaktív Python-munkamenetből, Jupyter-jegyzetfüzetből, Visual Studio Code-ból vagy más IDE-ből is használhatja a szolgáltatást.

* [Mi a Pythonhoz készült Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Az SDK telepítése vagy frissítése](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* [Fejlesztési környezet konfigurálása Azure Machine Learninghoz](how-to-configure-environment.md)

### <a name="run-configuration"></a>Konfiguráció futtatása

Azure Machine Learning használatával általános betanítási feladatok határozhatók meg a [RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py). A rendszer ezt követően a futtatási konfigurációt, valamint a betanítási parancsfájl (oka) t használja a modell számítási célra való betanításához.

A futtatási konfigurációt a helyi számítógép esetében is elindíthatja, majd igény szerint átválthat egy felhőalapú számítási célra. A számítási cél módosításakor csak a használt futtatási konfigurációt kell módosítania. A Futtatás a betanítási feladatról is naplóz adatokat, például a bemeneteket, a kimeneteket és a naplókat.

* [Mi az a futtatási konfiguráció?](concept-azure-machine-learning-architecture.md#run-configurations)
* [Oktatóanyag: az első ML-modell betanítása](tutorial-1st-experiment-sdk-train.md)
* [Példák: Jupyter Notebook betanítási modellekre vonatkozó példák](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training)
* [Útmutató: számítási célok beállítása és használata a modell betanításához](how-to-set-up-training-targets.md)

### <a name="automated-machine-learning"></a>Automatizált gépi tanulás

Adja meg az iterációkat, a hiperparaméter beállításait, a featurization és az egyéb beállításokat. A betanítás során Azure Machine Learning a különböző algoritmusokat és paramétereket párhuzamosan kísérli meg. A képzés leáll, ha eléri a megadott kilépési feltételeket. A becslések használatakor nem kell aggódnia a futtatási konfiguráció definiálásával kapcsolatban.

> [!TIP]
> A Python SDK mellett a [Azure Machine learning Studióban](https://ml.azure.com)is használhatja az automatikus ml-t.

* [Mi az automatizált gépi tanulás?](concept-automated-ml.md)
* [Oktatóanyag: az első besorolási modell létrehozása automatizált gépi tanulással](tutorial-first-experiment-automated-ml.md)
* [Oktatóanyag: automatikus gépi tanulás használata a taxi viteldíjak előrejelzéséhez](tutorial-auto-train-models.md)
* [Példák: Jupyter Notebook a gépi tanulásra vonatkozó példák](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)
* [Útmutató: automatizált ML-kísérletek konfigurálása a Pythonban](how-to-configure-auto-train.md)
* [Útmutató: az idősorozat-előrejelzési modell alapképzése](how-to-auto-train-forecast.md)
* [Útmutató: gépi tanulási kísérletek létrehozása, feltárása és üzembe helyezése Azure Machine Learning Studióval](how-to-use-automated-ml-for-ml-models.md)

### <a name="estimators"></a>Becslések

A becslések megkönnyíti a modellek betanítását népszerű ML-keretrendszerek használatával. Ha a **Scikit-Learn**, a **PyTorch**, a **TensorFlow**vagy a **chainer**használatát használja, érdemes megfontolnia a képzéshez szükséges kalkulátor használatát. Létezik egy általános kalkulátor is, amely olyan keretrendszerek esetében használható, amelyek még nem rendelkeznek dedikált kalkulátor-osztállyal. A becslések használatakor nem kell aggódnia a futtatási konfiguráció definiálásával kapcsolatban.

* [Mi a becslések?](concept-azure-machine-learning-architecture.md#estimators)
* [Oktatóanyag: képosztályozási modellek betanítása MNIST-adatokkal és scikit – további tudnivalók a Azure Machine Learning használatával](tutorial-train-models-with-aml.md)
* [Példák: Jupyter Notebook a becslések használatára](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning)
* [Útmutató: becslések létrehozása a betanításban](how-to-train-ml-models.md)

### <a name="machine-learning-pipeline"></a>Gépi tanulási folyamat

A gépi tanulási folyamatok használhatják a korábban említett tanítási módszereket (futtatási konfiguráció, becslések és automatizált gépi tanulás). A folyamatok többet mutatnak a munkafolyamatok létrehozásáról, így többek között a modellek betanítását is magukban foglalják. A folyamatokban a modelleket automatizált gépi tanulás, becslések vagy futtatási konfigurációk használatával lehet betanítani.

* [Mik a Azure Machine Learning ML-folyamatok?](concept-ml-pipelines.md)
* [Gépi tanulási folyamatokat hozhat létre és futtathat Azure Machine Learning SDK-val](how-to-create-your-first-pipeline.md)
* [Oktatóanyag: Azure Machine Learning-folyamatok használata a Batch pontozáshoz](tutorial-pipeline-batch-scoring-classification.md)
* [Példák: példák a gépi tanulási folyamatokra Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines)
* [Példák: folyamat automatikus gépi tanulással](https://aka.ms/pl-automl)
* [Példák: folyamat becslések-mel](https://aka.ms/pl-estimator)

## <a name="r-sdk"></a>R SDK

Az R SDK lehetővé teszi az R nyelv használatát a Azure Machine Learning. Az SDK a reticulate csomagot használja a Azure Machine Learning Python SDK-hoz való kötéshez. Ez lehetővé teszi a Python SDK-ban megvalósított alapvető objektumok és módszerek elérését bármely R-környezetből.

További információkért tekintse át a következő cikkeket:

* [Oktatóanyag: logisztikai regressziós modell létrehozása](tutorial-1st-r-experiment.md)
* [Az R-hez készült Azure Machine Learning SDK-referenciák](https://azure.github.io/azureml-sdk-for-r/index.html)

## <a name="azure-machine-learning-designer"></a>Azure Machine Learning Designer

A Designer lehetővé teszi a modellek betanítását egy fogd és vidd felületen a böngészőben.

+ [Mi a tervező?](concept-designer.md)
+ [Oktatóanyag: autó árának előrejelzése](tutorial-designer-automobile-price-train-score.md)
+ [Regresszió: előrejelzési ár](how-to-designer-sample-regression-automobile-price-basic.md)
+ [Besorolás: bevétel előrejelzése](how-to-designer-sample-classification-predict-income.md)
+ [Besorolás: forgalom előrejelzése, szolgáltatóváltást és értékesítés](how-to-designer-sample-classification-churn.md)
+ [Besorolás egyéni R-parancsfájllal: repülési késések előrejelzése](how-to-designer-sample-classification-flight-delay.md)
+ [Szöveg besorolása: wikipedia SP 500 adatkészlet](how-to-designer-sample-text-classification.md)

## <a name="cli"></a>parancssori felület

A Machine learning parancssori felület egy bővítmény az Azure CLI-hez. Platformfüggetlen CLI-parancsokat biztosít a Azure Machine Learning használatához. Általában a CLI segítségével automatizálhatja a feladatokat, például a gépi tanulási modellek betanítását.

* [A CLI-bővítmény használata Azure Machine Learning](reference-azure-machine-learning-cli.md)
* [MLOps az Azure-ban](https://github.com/microsoft/MLOps)

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [állíthat be képzési környezeteket](how-to-set-up-training-targets.md).
