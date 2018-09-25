---
title: Az Azure Machine Learning gépi tanulási modellek tanítása
description: Ismerje meg, hogyan hajthat végre egy csomópontos és elosztott képzési hagyományos gép tanulási és deep learning-modellek Azure Machine Learning-szolgáltatásokkal
ms.author: minxia
author: mx-iao
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.date: 09/24/2018
ms.openlocfilehash: 50b808b5769f2160d765ecdb431d71856e651b33
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46983582"
---
# <a name="how-to-train-models-with-azure-machine-learning"></a>Hogyan lehet az Azure Machine Learning-modellek betanításához

Képzési gépi tanulási modelleket, különösen Neurális hálózatok, gyakran egy idő - és számításigényes feladat. Miután végzett a tanítási szkriptet és a egy kis részét jelentik az adatok a helyi gépen futó, valószínűleg érdemes vertikális felskálázása a számítási feladatok.

Képzési elősegítése érdekében az Azure Machine Learning Python SDK biztosít egy magas szintű absztrakció, a Estimator osztály, amely lehetővé teszi, hogy a felhasználók számára könnyen a saját az Azure-ökoszisztéma-modellek betanításához. Hozzon létre, és küldje el szeretné futtatni a távoli számítási, legyen szó egy egycsomópontos futtatható vagy elosztott képzési GPU-fürtön belül képzési kód Estimator objektum használatával. PyTorch és TensorFlow-feladatok az Azure Machine Learning is biztosít a saját egyéni PyTorch és, amelyek megkönnyítik a TensorFlow-Estimators ezek a keretrendszerek.

## <a name="train-with-an-estimator"></a>Az egy Estimator betanítása

Létrehozása után a [munkaterület](https://docs.microsoft.com/azure/machine-learning/service/concept-azure-machine-learning-architecture#workspace) , és állítsa be a [fejlesztési környezet](how-to-configure-environment.md), az Azure Machine Learning-modell tanítása az alábbi lépésekből áll:  
1. [Hozzon létre egy távoli számítási célnak](how-to-set-up-training-targets.md)
2. [Töltse fel a betanítási adatok](how-to-access-data.md) (nem kötelező)
3. A képzési parancsfájl létrehozása
4. Hozzon létre egy Estimator objektumot
5. Betanítási feladat elküldése

Ez a cikk foglalkozik a 4 – 5. lépéseket. 1 – 3. lépéseket lásd ezt [oktatóanyag](tutorial-train-models-with-aml.md) példaként.

### <a name="single-node-training"></a>Egy csomópontos képzés

A következő kód bemutatja egy egycsomópontos képzés, a scikit távoli számítási Azure-ban futó – ismerje meg a modellt. Már létrehozott kell a [számítási célt](how-to-set-up-training-targets.md#batch) objektum `compute_target` és a [adattárolója](how-to-access-data.md) objektum `ds`.

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

A fenti kódrészletben a következő paramétereket a Estimator konstruktor adja meg:
* `source_directory`: A helyi könyvtárban, amely tartalmazza az összes a betanítási feladathoz szükséges kódot. Ez a mappa a távoli számítási átmásolódnak a helyi gépen 
* `script_params`: Egy a tanítási szkriptet a parancssori argumentumok megadása szótárban `entry_script`, < parancssori argumentum, érték > formájában párok
* `compute_target`: A távoli számítási arról, hogy az a tanítási szkriptet, ebben az esetben egy [Batch AI](how-to-set-up-training-targets.md#batch) fürt
* `entry_script`: A fájl elérési útja (viszonyítva a `source_directory`), a tanítási szkriptet futtatandó távoli számítási. Ezt a fájlt, és a további fájlokat attól függ, ebben a mappában kell elhelyezni.
* `conda_packages`: Python-csomagokat a tanítási szkriptet által igényelt conda-n keresztül kell telepíteni a listája.  
A konstruktor rendelkezik egy másik nevű paramétert `pip_packages` használható az esetleges pip csomagokat szükséges

Most, hogy létrehozta a Estimator objektum, a betanítási feladat a futtatandó távoli számítási hívása keresztül küldhet a `submit` működnek a [kísérlet](concept-azure-machine-learning-architecture.md#experiment) objektum `experiment`. 

```Python
run = experiment.submit(sk_est)
print(run.get_details().status)
```

> [!IMPORTANT]
> **Különleges mappák** két mappát *kimenete* és *naplók*, speciális kezelés az Azure Machine Learning által. Során képzésekről, ha nevű mappába írt fájlok *kimenete* és *naplók* , amelyek a gyökérkönyvtár viszonyított (`./outputs` és `./logs`, illetve), ezeket a fájlokat fog kapni. automatikusan feltölti a futtatási előzmények, hogy a Futtatás befejeződése után kap azokhoz való hozzáférés nélkül. 
>
> Eléréséhez (például a szolgáltatásmodell-fájlokból, ellenőrzőpontok, az adatfájlok vagy ábrázolt lemezképek) betanítás során létrehozott írási ezeket a `./outputs` mappát.
>
> Hasonlóképpen, írjon a bármely naplókat a tanítási, futtassa a a `./logs` mappát. Az Azure Machine Learning használatához [TensorBoard integrációs](https://aka.ms/aml-notebook-tb) ellenőrizze, hogy a TensorBoard naplóznak ebbe a mappába. Amíg Futtatás folyamatban van, akkor fogja tudni TensorBoard elindításához, és ezek a naplók streamelése.  Később akkor is a korábbi futtatásokat bármelyik a naplók visszaállítási lehetőségével.
>
> Például írt fájlok letöltéséhez a *kimenete* mappát a helyi gépre a távoli betanítási Futtatás után: `run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`

### <a name="distributed-training-and-custom-docker-images"></a>Elosztott képzés és az egyéni Docker-rendszerképek

Az a Estimator is végrehajthasson két további forgatókönyv közül választhat:
1. Egyéni Docker-rendszerkép használata
2. Több csomópontos fürt elosztott betanítás

A következő kód bemutatja, hogyan elosztott betanítás egy CNTK modell elvégzésére. Emellett helyett használja az alapértelmezett Azure Machine Learning-lemezképek, azt feltételezi, hogy a saját egyéni docker-rendszerkép használata képzéshez szeretné.

Már létrehozott kell a [számítási célt](how-to-set-up-training-targets.md#batch) objektum `compute_target`. A estimator módon hozhat létre:

```Python
from azureml.train.estimator import Estimator

estimator = Estimator(source_directory='./my-cntk-proj',
                      compute_target=compute_target,
                      entry_script='train.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_backend='mpi',     
                      pip_packages=['cntk==2.5.1'],
                      custom_docker_base_image='microsoft/mmlspark:0.12')
```

A fenti kód a következő új paraméterek a Estimator konstruktor tünteti fel:
* `custom_docker_base_image`: A használni kívánt kép a neve. Adja meg a lemezkép érhető el a nyilvános docker adattárait (megkülönbözteti a kis Docker Hub) csak. Rendszerkép használatához egy privát docker-adattárból, használhatja a konstruktor `environment_definition` paraméter helyett
* `node_count`: A betanítási feladathoz használandó csomópontok száma. Ennek az argumentumnak az alapértelmezett `1`
* `process_count_per_node`: Minden egyes csomóponton futtatandó folyamatok (vagy "dolgozó szakemberek") a száma. Ebben az esetben használhatja a `2` gpu-n elérhető minden egyes csomóponton. Ennek az argumentumnak az alapértelmezett `1`
* `distributed_backend`: A háttérrendszer indításakor elosztott képzés, így az a Estimator MPI-n keresztül. Ennek az argumentumnak az alapértelmezett `None`. Ha szeretné-e a párhuzamos és elosztott képzési végez (például `node_count`> 1 vagy `process_count_per_node`> 1 vagy mindkét) állítsa be `distributed_backend='mpi'`. Az AML által használt MPI végrehajtása [nyílt MPI](https://www.open-mpi.org/).

Végül küldje el a betanítási feladatot:
```Python
run = experiment.submit(cntk_est)
```

## <a name="examples"></a>Példák
Az sklearn modell betanítása oktatóanyagot tekintse meg:
* `tutorials/01.train-models.ipynb`

Egyéni docker használatával elosztott CNTK oktatóanyagot tekintse meg:
* `training/06.distributed-cntk-with-custom-docker/06.distributed-cntk-with-custom-docker.ipynb`

Ezeket a notebookokat lekérése:

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>További lépések

* [Metrikák futtatása a betanítás során nyomon követése](how-to-track-experiments.md)
* [PyTorch modelleket taníthat be](how-to-train-pytorch.md)
* [TensorFlow-modellek betanításához](how-to-train-tensorflow.md)
* [Hiperparaméterek hangolása](how-to-tune-hyperparameters.md)
* [A betanított modell üzembe helyezése](how-to-deploy-and-where.md)
