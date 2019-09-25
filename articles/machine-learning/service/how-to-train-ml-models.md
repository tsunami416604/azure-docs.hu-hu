---
title: ML modellek betanítása a becslések
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan hajthat végre egycsomópontos és elosztott képzéseket hagyományos gépi tanulási és Deep learning-modellekben Azure Machine Learning kalkulátor osztály használatával
ms.author: maxluk
author: maxluk
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.date: 04/19/2019
ms.custom: seodec18
ms.openlocfilehash: 43597113c439f2b88bee0834dddc8cb37ec0202a
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2019
ms.locfileid: "71213529"
---
# <a name="train-models-with-azure-machine-learning-using-estimator"></a>Modellek betanítása Azure Machine Learning a kalkulátor használatával

A Azure Machine Learning segítségével könnyedén elküldheti a betanítási szkriptet [különféle számítási célokba](how-to-set-up-training-targets.md#compute-targets-for-training)a [RunConfiguration objektum](how-to-set-up-training-targets.md#whats-a-run-configuration) és a [ScriptRunConfig objektum](how-to-set-up-training-targets.md#submit)használatával. Ez a minta sok rugalmasságot és maximális szabályozást biztosít.

A részletes tanulási modell tanításának elősegítése érdekében a Azure Machine Learning Python SDK egy alternatív, magasabb szintű absztrakciót, a kalkulátor osztályt biztosít, amely lehetővé teszi a felhasználók számára a futtatási konfigurációk egyszerű összeállítását. Létrehozhat és használhat egy általános számítást, amellyel betaníthatja [az oktatási](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) szkriptet bármely kiválasztott képzési keretrendszer (például a scikit-Learn) bármely kiválasztott számítási célra, legyen az a helyi gép, egyetlen virtuális gép az Azure-ban vagy egy Azure-beli GPU-fürt. A PyTorch, a TensorFlow és a láncolási feladatok esetében a Azure Machine Learning a megfelelő [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)és [láncolási](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) becslések is biztosít, hogy egyszerűbbé váljon ezen keretrendszerek használata.

## <a name="train-with-an-estimator"></a>Az egy estimator betanítása

Létrehozása után a [munkaterület](concept-workspace.md) , és állítsa be a [fejlesztési környezet](how-to-configure-environment.md), az Azure Machine Learning-modell tanítása az alábbi lépésekből áll:  
1. Hozzon létre egy [távoli számítási célt](how-to-set-up-training-targets.md) (vegye figyelembe, hogy a helyi számítógépet számítási célként is használhatja)
2. Betanítási [adatok](how-to-access-data.md) feltöltése az adattárba (opcionális)
3. Hozzon létre a [tanítási szkriptet](tutorial-train-models-with-aml.md#create-a-training-script)
4. Hozzon létre egy `Estimator` objektum
5. A kalkulátor beküldése a munkaterület alá tartozó kísérleti objektumra

Ez a cikk foglalkozik a 4 – 5. lépéseket. 1 – 3. lépéseket lásd a [modell létrehozása útmutató betanításához](tutorial-train-models-with-aml.md) példaként.

### <a name="single-node-training"></a>Egy csomópontos képzés

Használata egy `Estimator` egy egycsomópontos képzéshez futtathatók az Azure-ban egy scikit távoli számítási – ismerje meg a modellt. Már létrehozott kell a [számítási célt](how-to-set-up-training-targets.md#amlcompute) objektum `compute_target` és a [adattárolója](how-to-access-data.md) objektum `ds`.

```Python
from azureml.train.estimator import Estimator

script_params = {
    '--data-folder': ds.as_mount(),
    '--regularization': 0.8
}

sk_est = Estimator(source_directory='./my-sklearn-proj',
                   script_params=script_params,
                   compute_target=compute_target,
                   entry_script='train.py',
                   conda_packages=['scikit-learn'])
```

Ez a kódrészlet Megadja, hogy a következő paramétereket a `Estimator` konstruktor.

Paraméter | Leírás
--|--
`source_directory`| Helyi könyvtár, amely tartalmazza az összes a betanítási feladathoz szükséges kódot. Ezt a mappát a rendszer átmásolja a helyi gépről a távoli számításba.
`script_params`| A szótárban adja meg azokat a parancssori argumentumokat, amelyeket át kell `entry_script`adni a betanítási `<command-line argument, value>` parancsfájlnak párok formájában. Ha részletes jelölőt szeretne megadni a `script_params`alkalmazásban `<command-line argument, "">`, használja a következőt:.
`compute_target`| Távoli számítási cél, amelyet a képzési parancsfájl fog futni, ebben az esetben egy Azure Machine Learning számítási ([AmlCompute](how-to-set-up-training-targets.md#amlcompute)-) fürt. (Vegye figyelembe, hogy bár a AmlCompute-fürt a leggyakrabban használt cél, más számítási célpontok is kiválaszthatók, például az Azure-beli virtuális gépek vagy akár a helyi számítógép is.)
`entry_script`| Fájl elérési útja (viszonyítva a `source_directory`), a tanítási szkriptet futtatandó távoli számítási. Ez a fájl és az attól függő további fájlok a mappában találhatók.
`conda_packages`| Szükség szerint a tanítási szkriptet conda-n keresztül kell telepíteni a Python-csomagok listáját.  

A konstruktornak van egy másik paramétere `pip_packages` , amelyet minden szükséges pip-csomaghoz használhat.

Most, hogy létrehozta a `Estimator` objektumazonosító, küldje el a betanítási feladat a távoli számítási hívásával futnia a `submit` működnek a [kísérlet](concept-azure-machine-learning-architecture.md#experiments) objektum `experiment`. 

```Python
run = experiment.submit(sk_est)
print(run.get_portal_url())
```

> [!IMPORTANT]
> **Különleges mappák** két mappát *kimenete* és *naplók*, speciális kezelés az Azure Machine Learning által. Betanítás, amikor nevű mappába írt fájlok során *kimenete* és *naplók* , amelyek a gyökérkönyvtár viszonyított (`./outputs` és `./logs`, illetve), a fájlok automatikusan az Töltse fel a futtatási előzmények, hogy azokhoz való hozzáférés a Futtatás befejeződése után.
>
> Hozhat létre összetevőket (például a szolgáltatásmodell-fájlokból, ellenőrzőpontok, az adatfájlok vagy ábrázolt lemezképek) betanítás során írási ezeket a `./outputs` mappát.
>
> Hasonlóképpen bármilyen naplót írhat a betanításból a `./logs` mappába. Az Azure Machine Learning használatához [TensorBoard integrációs](https://aka.ms/aml-notebook-tb) ellenőrizze, hogy a TensorBoard naplóznak ebbe a mappába. Amíg Futtatás folyamatban van, akkor fogja tudni TensorBoard elindításához, és ezek a naplók streamelése.  Később akkor is a korábbi futtatásokat bármelyik a naplók visszaállítási lehetőségével.
>
> Például írt fájlok letöltéséhez a *kimenete* mappát a helyi gépre a távoli betanítási Futtatás után: `run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`

### <a name="distributed-training-and-custom-docker-images"></a>Elosztott képzés és az egyéni Docker-rendszerképek

Két további esetben az is sokat elvégezhet a `Estimator`:
* Egyéni Docker-rendszerkép használata
* Több csomópontos fürt elosztott betanítás

A következő kód bemutatja, hogyan hajthat végre elosztott képzést egy kerasz-modellhez. Emellett az alapértelmezett Azure Machine learning rendszerképek használata helyett a Docker hub `continuumio/miniconda` egyéni Docker-rendszerképét adja meg a betanításhoz.

Már létrehozott kell a [számítási célt](how-to-set-up-training-targets.md#amlcompute) objektum `compute_target`. A estimator következőképpen hozhat létre:

```Python
from azureml.train.estimator import Estimator
from azureml.core.runconfig import MpiConfiguration

estimator = Estimator(source_directory='./my-keras-proj',
                      compute_target=compute_target,
                      entry_script='train.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=MpiConfiguration(),        
                      conda_packages=['tensorflow', 'keras'],
                      custom_docker_image='continuumio/miniconda')
```

A fenti kód mutatja meg a következő új paramétereket a `Estimator` konstruktort:

Paraméter | Leírás | Alapértelmezett
--|--|--
`custom_docker_image`| A használni kívánt kép neve. Csak adja meg a lemezkép érhető el a nyilvános docker adattárait (megkülönbözteti a kis Docker Hub). Ha privát Docker-tárházból szeretne képet használni, használja helyette a konstruktor `environment_definition` paraméterét. [Lásd: példa](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb). | `None`
`node_count`| A betanítási feladathoz használandó csomópontok száma. | `1`
`process_count_per_node`| Minden egyes csomóponton futtatandó folyamatok (vagy "dolgozó szakemberek") száma. Ebben az esetben használhatja a `2` gpu-n elérhető minden egyes csomóponton.| `1`
`distributed_training`| [MPIConfiguration]('https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py') objektum az elosztott KÉPZÉSek MPI-háttér használatával történő elindításához.  | `None`


Végül küldje el a betanítási feladatot:
```Python
run = experiment.submit(estimator)
print(run.get_portal_url())
```

## <a name="github-tracking-and-integration"></a>GitHub-követés és-integráció

Ha olyan képzést indít el, ahol a forrás könyvtára helyi git-tárház, a rendszer a tárház adatait a futtatási előzményekben tárolja. Az adattár aktuális véglegesítő AZONOSÍTÓját például az előzmények részeként naplózza a rendszer.

## <a name="examples"></a>Példák
A becslési minta alapjait megjelenítő jegyzetfüzetek esetében lásd:
* [how-to-use-azureml/training-with-deep-learning/how-to-use-estimator](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb)

Olyan jegyzetfüzetek esetében, amelyek a scikit-tanulási modellt a kalkulátor használatával végzik, lásd:
* [oktatóanyagok és img-besorolás-1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

A betanítási modellekhez tartozó jegyzetfüzetek mélyreható tanulási keretrendszer-specifikus becslések való használatával kapcsolatban lásd:
* [How-to-use-azureml/Training-with-deep-Learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>További lépések

* [Metrikák futtatása a betanítás során nyomon követése](how-to-track-experiments.md)
* [PyTorch modelleket taníthat be](how-to-train-pytorch.md)
* [TensorFlow-modellek betanításához](how-to-train-tensorflow.md)
* [Hiperparaméterek hangolása](how-to-tune-hyperparameters.md)
* [A betanított modell üzembe helyezése](how-to-deploy-and-where.md)
