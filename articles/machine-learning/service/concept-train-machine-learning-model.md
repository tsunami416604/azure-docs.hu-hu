---
title: Modellek képzési módszerei
titleSuffix: Azure Machine Learning
description: Ismerje meg a különböző módszereket, amelyekkel a modelleket betaníthatja Azure Machine Learning használatával. A becslések egyszerű módszert kínál a népszerű keretrendszerek, például a Scikit-Learn, a TensorFlow, a kerasz, a PyTorch és a Chainer használatával való együttműködésre. Machine Learning folyamatokkal könnyedén ütemezhet felügyelet nélküli futtatásokat, különböző számítási környezeteket használhat, és felhasználhatja a munkafolyamat egyes részeit. A és a Run konfigurációk részletes szabályozást biztosítanak azokon a számítási céloknál, amelyeken a betanítási folyamat fut.
services: machine-learning
ms.service: machine-learning
author: Blackmist
ms.author: larryfr
ms.subservice: core
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: a755fe1607e581cb0a25eb9bd90c2ba223829a46
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350591"
---
# <a name="train-models-with-azure-machine-learning"></a>Az Azure Machine Learning-modellek betanításához

A Azure Machine Learning számos lehetőséget kínál a modellek betanítására, az SDK-t az SDK-t használó alacsony kódú megoldások, például az automatizált gépi tanulás és a vizualizációs felület használatával. Az alábbi lista segítségével meghatározhatja, hogy melyik betanítási módszer legyen a legmegfelelőbb:

+ [A Pythonhoz készült Azure Machine learning SDK](#python-sdk): A Python SDK számos módszert kínál a különböző képességekkel rendelkező modellek betanítására.

    | Betanítási módszer | Leírás |
    | ----- | ----- |
    | [Konfiguráció futtatása](#run-configuration) | A **modellek betanításának általános módja** egy képzési parancsfájl használata és a konfiguráció futtatása. A futtatási konfiguráció biztosítja a modell betanításához használt képzési környezet konfigurálásához szükséges információkat. Elvégezheti a futtatási konfigurációt, a betanítási szkriptet és a számítási célt (a képzési környezetet) és a betanítási feladatot. |
    | [Automatizált gépi tanulás](#automated-machine-learning) | Az automatizált gépi tanulás lehetővé teszi a **modellek széles körű adatelemzési és programozási ismeretek nélküli tanítását**. Az adatelemzési és-programozási hátterű felhasználók számára lehetővé teszi az idő és az erőforrások megtakarítását az algoritmus kiválasztásának és a hiperparaméter hangolásának automatizálásával. Az automatizált gépi tanulás használatakor nem kell aggódnia a futtatási konfiguráció definiálásával kapcsolatban. |
    | [Becslések](#estimators) | A kalkulátor osztályok megkönnyítik a **modellek betanítását a népszerű gépi tanulási keretrendszerek alapján**. A **Scikit-Learn**, a **PyTorch**, a **TensorFlow**és a **chainer**esetében a kalkulátor osztályok vannak. Létezik egy általános kalkulátor is, amely olyan keretrendszerek esetében használható, amelyek még nem rendelkeznek dedikált kalkulátor-osztállyal. A becslések használatakor nem kell aggódnia a futtatási konfiguráció definiálásával kapcsolatban. |
    | [Gépi tanulási folyamat](#machine-learning-pipeline) | A folyamatok nem egy másik betanítási módszer, hanem a **munkafolyamatok moduláris, újrafelhasználható lépésekkel való definiálásának módja**, amely magában foglalhatja a munkafolyamatok részét képező képzést is. A gépi tanulási folyamatokat az automatizált gépi tanulás, a becslések és a modellek betanítására szolgáló konfiguráció futtatásával támogatja. Mivel a folyamatok nem kifejezetten a képzésre összpontosítottak, a folyamat használatának okai a többi tanítási módszernél változatosabbek. Általában a következőket lehet használni:<br>* Olyan **felügyelet nélküli folyamatokat szeretne ütemezni** , mint például a hosszan futó betanítási feladatok vagy az adatok előkészítése.<br>* **Több olyan lépést** is használhat, amely heterogén számítási erőforrásokon és tárolási helyeken is össze van hangolva.<br>* Használja a folyamatot **újrafelhasználható sablonként** adott forgatókönyvekhez, például az újraképzéshez vagy a kötegelt pontozáshoz.<br>@no__t – 0 a munkafolyamathoz tartozó**adatforrások, bemenetek és kimenetek nyomon követése és verziószáma** .<br>* A munkafolyamatot **különböző csapatok valósítják meg, amelyek egymástól függetlenül működnek**. A lépések ezután összekapcsolhatók egy folyamattal a munkafolyamat megvalósításához. |

+ **Vizuális felület**: A Azure Machine Learning __Visual Interface__ egyszerű belépési pontot biztosít a gépi tanuláshoz a fogalmak vagy a kis kódolási élményt biztosító felhasználók számára. Lehetővé teszi a modellek betanítását egy fogd és vidd webes KEZELŐFELÜLET használatával. A Python-kódokat használhatja a terv részeként, vagy betaníthatja a modelleket anélkül, hogy kódot kellene írnia.

+ **CLI**: A Machine learning parancssori felülete parancsokat biztosít a Azure Machine Learningekkel kapcsolatos gyakori feladatokhoz, és gyakran a **parancsfájlok futtatásához és a feladatok automatizálásához**használatos. Ha például létrehozta a betanítási parancsfájlt vagy folyamatot, a CLI-vel elindíthatja a betanítást egy adott időpontban, vagy a betanításhoz használt adatfájlokat is frissíti. A betanítási modellek a betanítási feladatokat elküldő parancsokat biztosítanak. A futtatási konfigurációkkal vagy folyamatokkal küldhet feladatokat.

A képzési módszerek mindegyike különböző típusú számítási erőforrásokat használhat a betanításhoz. Ezeket az erőforrásokat együttesen [__számítási céloknak__](concept-azure-machine-learning-architecture.md#compute-targets)nevezzük. A számítási cél lehet egy helyi gép vagy egy felhőalapú erőforrás, például egy Azure Machine Learning számítási, Azure-HDInsight vagy távoli virtuális gép.

## <a name="python-sdk"></a>Python SDK

A Pythonhoz készült Azure Machine Learning SDK lehetővé teszi a gépi tanulási munkafolyamatok létrehozását és futtatását Azure Machine Learning használatával. Interaktív Python-munkamenetből, Jupyter-jegyzetfüzetből, Visual Studio Code-ból vagy más IDE-ből is használhatja a szolgáltatást.

* [Mi a Pythonhoz készült Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Az SDK telepítése/frissítése](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* [Fejlesztési környezet konfigurálása Azure Machine Learninghoz](how-to-configure-environment.md)

### <a name="run-configuration"></a>Futtatási konfigurációt

Azure Machine Learning használatával általános betanítási feladatok határozhatók meg a [RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py). A rendszer ezt követően a futtatási konfigurációt, valamint a betanítási parancsfájl (oka) t használja a modell számítási célra való betanításához.

A futtatási konfigurációt a helyi számítógép esetében is elindíthatja, majd igény szerint átválthat egy felhőalapú számítási célra. A számítási cél módosításakor csak a használt futtatási konfigurációt kell módosítania. A Futtatás a betanítási feladatról is naplóz adatokat, például a bemeneteket, a kimeneteket és a naplókat.

* [Mi az a futtatási konfiguráció?](concept-azure-machine-learning-architecture.md#run-configurations)
* [Oktatóanyag: Az első ML-modell betanítása @ no__t-0
* @no__t – 0Examples: Jupyter Notebook példák a képzési modellekre @ no__t-0
* [Útmutató: Számítási célok beállítása és használata a modell betanításához @ no__t-0

### <a name="automated-machine-learning"></a>Automatizált Machine Learning

Adja meg az iterációkat, a hiperparaméter beállításait, a featurization és az egyéb beállításokat. A betanítás során Azure Machine Learning a különböző algoritmusokat és paramétereket párhuzamosan kísérli meg. A képzés leáll, ha eléri a megadott kilépési feltételeket. A becslések használatakor nem kell aggódnia a futtatási konfiguráció definiálásával kapcsolatban.

> [!TIP]
> A Python SDK kívül a munkaterület kezdőlapján [(előzetes verzió)](https://ml.azure.com)is használhatja az automatikus ml-t.

* [Mi az az automatizált gépi tanulás?](concept-automated-ml.md)
* [Oktatóanyag: Hozza létre első besorolási modelljét automatikus Machine learning @ no__t-0
* [Oktatóanyag: Automatikus gépi tanulás használata a taxi viteldíjak előrejelzéséhez @ no__t-0
* @no__t – 0Examples: Jupyter Notebook példák a gépi tanulásra @ no__t-0
* [Útmutató: Automatizált ML-kísérletek konfigurálása a Python @ no__t-0
* [Útmutató: Idősorozat-előrejelzési modell autotrainer @ no__t-0
* [Útmutató: Automatikus gépi tanulási kísérletek létrehozása, megismerése és üzembe helyezése Azure Machine Learning munkaterületének kezdőlapján (előzetes verzió) @no__t – 0

### <a name="estimators"></a>Becslések

A becslések megkönnyíti a modellek betanítását népszerű ML-keretrendszerek használatával. Ha a **Scikit-Learn**, a **PyTorch**, a **TensorFlow**vagy a **chainer**használatát használja, érdemes megfontolnia a képzéshez szükséges kalkulátor használatát. Létezik egy általános kalkulátor is, amely olyan keretrendszerek esetében használható, amelyek még nem rendelkeznek dedikált kalkulátor-osztállyal. A becslések használatakor nem kell aggódnia a futtatási konfiguráció definiálásával kapcsolatban.

* [Mi a becslések?](concept-azure-machine-learning-architecture.md#estimators)
* [Oktatóanyag: Képosztályozási modellek betanítása MNIST-adatokkal és scikit – további tudnivalók a Azure Machine Learning @ no__t-0 használatával
* @no__t – 0Examples: Jupyter Notebook példák a becslések @ no__t-0 használatára
* [Útmutató: Becslések létrehozása a Training @ no__t-0

### <a name="machine-learning-pipeline"></a>Gépi tanulási folyamat

A gépi tanulási folyamatok használhatják a korábban említett tanítási módszereket (futtatási konfiguráció, becslések és automatizált gépi tanulás). A folyamatok többet mutatnak a munkafolyamatok létrehozásáról, így többek között a modellek betanítását is magukban foglalják. A folyamatokban a modelleket automatizált gépi tanulás, becslések vagy futtatási konfigurációk használatával lehet betanítani.

* [Mik a Azure Machine Learning ML-folyamatok?](concept-ml-pipelines.md)
* [Gépi tanulási folyamatokat hozhat létre és futtathat Azure Machine Learning SDK-val](how-to-create-your-first-pipeline.md)
* [Oktatóanyag: Azure Machine Learning folyamatok használata a Batch pontozáshoz @ no__t-0
* @no__t – 0Examples: Jupyter Notebook példák a gépi tanulási folyamatokra @ no__t-0
* @no__t – 0Examples: Folyamat automatikus gépi tanulással @ no__t-0
* @no__t – 0Examples: Folyamat a becslések @ no__t-0

## <a name="visual-interface"></a>Vizuális felület

A vizualizációs felület (előzetes verzió) lehetővé teszi a modellek betanítását a böngészőben a drag and drop felülettel.

+ [Mi a vizuális felület?](ui-concept-visual-interface.md)
+ @no__t – 0Tutorial: Autó árának előrejelzése @ no__t-0
+ @no__t – 0Regression: Előre jelzett ár](how-to-ui-sample-regression-predict-automobile-price-basic.md)
+ @no__t – 0Classification: Hitelkockázat előrejelzése](how-to-ui-sample-classification-predict-credit-risk-basic.md)
+ @no__t – 0Classification: A forgalom előrejelzése, a szolgáltatóváltást és az értékesítés](how-to-ui-sample-classification-predict-churn.md)

## <a name="cli"></a>parancssori felület

A Machine learning parancssori felület egy bővítmény az Azure CLI-hez. Platformfüggetlen CLI-parancsokat biztosít a Azure Machine Learning használatához. Általában a CLI segítségével automatizálhatja a feladatokat, például a gépi tanulási modellek betanítását.

* [A CLI-bővítmény használata Azure Machine Learning](reference-azure-machine-learning-cli.md)
* [MLOps az Azure-ban](https://github.com/microsoft/MLOps)

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [állíthat be képzési környezeteket](how-to-set-up-training-targets.md).
