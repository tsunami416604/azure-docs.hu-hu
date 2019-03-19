---
title: Gépi Tanulási modelleket taníthat be a estimators
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan hajthat végre egy csomópontos és elosztott képzési hagyományos gép tanulási és deep learning-modellek Azure Machine Learning services Estimator osztály használatával
ms.author: minxia
author: mx-iao
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.date: 2/14/2019
ms.custom: seodec18
ms.openlocfilehash: 818b6e5994a4f5b9d21d511f0a31eab6e00033f7
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58012476"
---
# <a name="train-models-with-azure-machine-learning-using-estimator"></a>Az Azure Machine Learning használatával estimator modelleket taníthat be

Az Azure Machine Learning, egyszerűen küldhet a tanítási szkriptet [különböző számítási céljainak](how-to-set-up-training-targets.md#compute-targets-for-training)révén [RunConfiguration objektum](how-to-set-up-training-targets.md#whats-a-run-configuration) és [ScriptRunConfig objektum](how-to-set-up-training-targets.md#submit). A minta rugalmasság és a maximális vezérlő rengeteg biztosítja.

Megkönnyítése érdekében deep learning-modell betanítása, az Azure Machine Learning Python SDK biztosít egy alternatív magasabb szintű absztrakció, a estimator osztály, amely lehetővé teszi a felhasználóknak egyszerűen futtassa szerkezet konfigurációkat. Létrehozhat és használhat egy általános [Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) elküldeni a tanítási szkriptet minden olyan választja learning keretrendszer használatával (például a scikit-további) szeretné futó bármilyen számítási célnak, hogy a helyi gépre, egyetlen virtuális Gépet az Azure-ban vagy egy GPU a fürt az Azure-ban. A PyTorch, a tensorflow-hoz és a Chainer feladatokhoz az Azure Machine Learning is biztosít megfelelő [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) és [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) estimators ezek használatának leegyszerűsítése érdekében keretrendszereket.

## <a name="train-with-an-estimator"></a>Az egy estimator betanítása

Létrehozása után a [munkaterület](concept-azure-machine-learning-architecture.md#workspace) , és állítsa be a [fejlesztési környezet](how-to-configure-environment.md), az Azure Machine Learning-modell tanítása az alábbi lépésekből áll:  
1. Hozzon létre egy [távoli számítási célnak](how-to-set-up-training-targets.md) (használhatja a helyi számítógép számítási célként. megjegyzés)
2. Töltse fel a [betanítási adatok](how-to-access-data.md) az adattároló (nem kötelező)
3. Hozzon létre a [tanítási szkriptet](tutorial-train-models-with-aml.md#create-a-training-script)
4. Hozzon létre egy `Estimator` objektum
5. A munkaterületen kísérletek objektumhoz estimator beküldése

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
`source_directory`| Helyi könyvtár, amely tartalmazza az összes a betanítási feladathoz szükséges kódot. Ez a mappa a távoli számítási átmásolódnak a helyi gépen 
`script_params`| A parancssori paraméterek, a tanítási szkriptet megadása szótárban `entry_script`, < parancssori argumentum, érték > formájában párok
`compute_target`| Arról, hogy az a tanítási szkriptet, ebben az esetben az Azure Machine Learning Compute távoli számítási célnak ([AmlCompute](how-to-set-up-training-targets.md#amlcompute)) fürt. (Vegye figyelembe, hogy annak ellenére, hogy a fürt AmlCompute célja a gyakran használt, lehetőség arra is választhat más számítási cél típusok, például az Azure virtuális gépek vagy akár a helyi számítógépen.)
`entry_script`| Fájl elérési útja (viszonyítva a `source_directory`), a tanítási szkriptet futtatandó távoli számítási. Ezt a fájlt, és a további fájlokat attól függ, ebben a mappában kell elhelyezni.
`conda_packages`| Szükség szerint a tanítási szkriptet conda-n keresztül kell telepíteni a Python-csomagok listáját.  

A konstruktor rendelkezik egy másik nevű paramétert `pip_packages` , amelyet használhat az esetleges pip csomagokat szükséges

Most, hogy létrehozta a `Estimator` objektumazonosító, küldje el a betanítási feladat a távoli számítási hívásával futnia a `submit` működnek a [kísérlet](concept-azure-machine-learning-architecture.md#experiment) objektum `experiment`. 

```Python
run = experiment.submit(sk_est)
print(run.get_portal_url())
```

> [!IMPORTANT]
> **Különleges mappák** két mappát *kimenete* és *naplók*, speciális kezelés az Azure Machine Learning által. Betanítás, amikor nevű mappába írt fájlok során *kimenete* és *naplók* , amelyek a gyökérkönyvtár viszonyított (`./outputs` és `./logs`, illetve), a fájlok automatikusan az Töltse fel a futtatási előzmények, hogy azokhoz való hozzáférés a Futtatás befejeződése után.
>
> Hozhat létre összetevőket (például a szolgáltatásmodell-fájlokból, ellenőrzőpontok, az adatfájlok vagy ábrázolt lemezképek) betanítás során írási ezeket a `./outputs` mappát.
>
> Hasonlóképpen, írjon a bármely naplókat a tanítási, futtassa a a `./logs` mappát. Az Azure Machine Learning használatához [TensorBoard integrációs](https://aka.ms/aml-notebook-tb) ellenőrizze, hogy a TensorBoard naplóznak ebbe a mappába. Amíg Futtatás folyamatban van, akkor fogja tudni TensorBoard elindításához, és ezek a naplók streamelése.  Később akkor is a korábbi futtatásokat bármelyik a naplók visszaállítási lehetőségével.
>
> Például írt fájlok letöltéséhez a *kimenete* mappát a helyi gépre a távoli betanítási Futtatás után: `run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`

### <a name="distributed-training-and-custom-docker-images"></a>Elosztott képzés és az egyéni Docker-rendszerképek

Két további esetben az is sokat elvégezhet a `Estimator`:
* Egyéni Docker-rendszerkép használata
* Több csomópontos fürt elosztott betanítás

A következő kód bemutatja, hogyan Keras modell elosztott képzési elvégzésére. Emellett helyett használja az alapértelmezett Azure Machine Learning-lemezképek, adja meg egy egyéni docker-rendszerképet a Docker Hubból `continuumio/miniconda` képzéshez.

Már létrehozott kell a [számítási célt](how-to-set-up-training-targets.md#amlcompute) objektum `compute_target`. A estimator következőképpen hozhat létre:

```Python
from azureml.train.estimator import Estimator

estimator = Estimator(source_directory='./my-keras-proj',
                      compute_target=compute_target,
                      entry_script='train.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_backend='mpi',     
                      conda_packages=['tensorflow', 'keras'],
                      custom_docker_base_image='continuumio/miniconda')
```

A fenti kód mutatja meg a következő új paramétereket a `Estimator` konstruktort:

Paraméter | Leírás | Alapértelmezett
--|--|--
`custom_docker_base_image`| A használni kívánt kép neve. Csak adja meg a lemezkép érhető el a nyilvános docker adattárait (megkülönbözteti a kis Docker Hub). Rendszerkép használatához egy privát docker-adattárból, használhatja a konstruktor `environment_definition` paraméter helyett. [Példa](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb). | `None`
`node_count`| A betanítási feladathoz használandó csomópontok száma. | `1`
`process_count_per_node`| Minden egyes csomóponton futtatandó folyamatok (vagy "dolgozó szakemberek") száma. Ebben az esetben használhatja a `2` gpu-n elérhető minden egyes csomóponton.| `1`
`distributed_backend`| Háttérbeli indításakor elosztott képzés, így az a Estimator MPI-n keresztül.  Párhuzamos és elosztott képzési elvégzésére (pl. `node_count`> 1 vagy `process_count_per_node`> 1 vagy mindkettő) állítsa be `distributed_backend='mpi'`. Az AML által használt MPI végrehajtása [nyílt MPI](https://www.open-mpi.org/).| `None`

Végül küldje el a betanítási feladatot:
```Python
run = experiment.submit(estimator)
print(run.get_portal_url())
```

## <a name="examples"></a>Példák
Ez a jegyzetfüzet bemutatja az alapokat, estimator mintát lásd:
* [how-to-use-azureml/training-with-deep-learning/how-to-use-estimator](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb)

A notebook, amely egy scikit betanítja-modell további estimator használja, lásd:
* [oktatóanyagok és img-besorolás-1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

Notebookok a meghatározott estimators deep learning-keretrendszer használatával modellek betanítása lásd:
* [How-to-use-azureml/Training-with-deep-Learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>További lépések

* [Metrikák futtatása a betanítás során nyomon követése](how-to-track-experiments.md)
* [PyTorch modelleket taníthat be](how-to-train-pytorch.md)
* [TensorFlow-modellek betanításához](how-to-train-tensorflow.md)
* [Hiperparaméterek hangolása](how-to-tune-hyperparameters.md)
* [A betanított modell üzembe helyezése](how-to-deploy-and-where.md)
