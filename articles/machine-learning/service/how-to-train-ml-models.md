---
title: Egy Estimator osztály használatával az Azure Machine Learning, machine learning-modellek betanításához.
description: Ismerje meg, hogyan hajthat végre egy csomópontos és elosztott képzési hagyományos gép tanulási és deep learning-modellek Azure Machine Learning services Estimator osztály használatával
ms.author: minxia
author: mx-iao
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.date: 09/24/2018
ms.openlocfilehash: c47761c184d0e6c091ff49b3eca2fdf89574b49d
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2018
ms.locfileid: "49114859"
---
# <a name="how-to-train-models-with-azure-machine-learning"></a>Hogyan lehet az Azure Machine Learning-modellek betanításához

Képzési gépi tanulási modelleket, különösen Neurális hálózatok, gyakran egy idő - és számításigényes feladat. Miután végzett a tanítási szkriptet és a egy kis részét jelentik az adatok a helyi gépen futó, valószínűleg érdemes vertikális felskálázása a számítási feladatok.

Képzési elősegítése érdekében az Azure Machine Learning Python SDK biztosít egy magas szintű absztrakció, a estimator osztály, amely lehetővé teszi, hogy a felhasználók számára könnyen a saját az Azure-ökoszisztéma-modellek betanításához. Létrehozhat és használhat egy `Estimator` objektum szeretné futtatni a távoli számítási, legyen szó egy egycsomópontos futtatható vagy elosztott képzési GPU-fürtön belül képzési kód elküldéséhez. PyTorch és TensorFlow-feladatok az Azure Machine Learning is biztosít megfelelő egyéni `PyTorch` és `TensorFlow` estimators egyszerűsítése érdekében ezek a keretrendszerek használatával.

## <a name="train-with-an-estimator"></a>Az egy estimator betanítása

Létrehozása után a [munkaterület](concept-azure-machine-learning-architecture.md#workspace) , és állítsa be a [fejlesztési környezet](how-to-configure-environment.md), az Azure Machine Learning-modell tanítása az alábbi lépésekből áll:  
1. Hozzon létre egy [távoli számítási célnak](how-to-set-up-training-targets.md)
2. Töltse fel a [betanítási adatok](how-to-access-data.md) (nem kötelező)
3. Hozzon létre a [tanítási szkriptet](tutorial-train-models-with-aml.md#create-a-training-script)
4. Hozzon létre egy `Estimator` objektum
5. Betanítási feladat elküldése

Ez a cikk foglalkozik a 4 – 5. lépéseket. 1 – 3. lépéseket lásd a [modell létrehozása útmutató betanításához](tutorial-train-models-with-aml.md) példaként.

### <a name="single-node-training"></a>Egy csomópontos képzés

Használata egy `Estimator` egy egycsomópontos képzéshez futtathatók az Azure-ban egy scikit távoli számítási – ismerje meg a modellt. Már létrehozott kell a [számítási célt](how-to-set-up-training-targets.md#batch) objektum `compute_target` és a [adattárolója](how-to-access-data.md) objektum `ds`.

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
`compute_target`| Távoli számítási arról, hogy az a tanítási szkriptet, ebben az esetben egy [Batch AI](how-to-set-up-training-targets.md#batch) fürt
`entry_script`| Fájl elérési útja (viszonyítva a `source_directory`), a tanítási szkriptet futtatandó távoli számítási. Ezt a fájlt, és a további fájlokat attól függ, ebben a mappában kell elhelyezni.
`conda_packages`| Szükség szerint a tanítási szkriptet conda-n keresztül kell telepíteni a Python-csomagok listáját.  
A konstruktor rendelkezik egy másik nevű paramétert `pip_packages` , amelyet használhat az esetleges pip csomagokat szükséges

Most, hogy létrehozta a `Estimator` objektumazonosító, küldje el a betanítási feladat a távoli számítási hívásával futnia a `submit` működnek a [kísérlet](concept-azure-machine-learning-architecture.md#experiment) objektum `experiment`. 

```Python
run = experiment.submit(sk_est)
print(run.get_details().status)
```

> [!IMPORTANT]
> **Különleges mappák** két mappát *kimenete* és *naplók*, speciális kezelés az Azure Machine Learning által. Betanítás, amikor nevű mappába írt fájlok során *kimenete* és *naplók* , amelyek a gyökérkönyvtár viszonyított (`./outputs` és `./logs`, illetve), a fájlok automatikusan az Töltse fel a futtatási előzmények, hogy azokhoz való hozzáférés a Futtatás befejeződése után.
>
> Hozhat létre összetevőket (például a szolgáltatásmodell-fájlokból, ellenőrzőpontok, az adatfájlok vagy ábrázolt lemezképek) betanítás során írási ezeket a `./outputs` mappát.
>
> Ehhez hasonlóan a tanítási, futtassa a bármely naplók ír a `./logs` mappát. Az Azure Machine Learning használatához [TensorBoard integrációs](https://aka.ms/aml-notebook-tb) ellenőrizze, hogy a TensorBoard naplóznak ebbe a mappába. Amíg Futtatás folyamatban van, akkor fogja tudni TensorBoard elindításához, és ezek a naplók streamelése.  Később akkor is a korábbi futtatásokat bármelyik a naplók visszaállítási lehetőségével.
>
> Például írt fájlok letöltéséhez a *kimenete* mappát a helyi gépre a távoli betanítási Futtatás után: `run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`

### <a name="distributed-training-and-custom-docker-images"></a>Elosztott képzés és az egyéni Docker-rendszerképek

Két további esetben az is sokat elvégezhet a `Estimator`:
* Egyéni Docker-rendszerkép használata
* Több csomópontos fürt elosztott betanítás

A következő kód bemutatja, hogyan elosztott betanítás egy CNTK modell elvégzésére. Emellett helyett használja az alapértelmezett Azure Machine Learning-lemezképek, azt feltételezi, hogy a saját egyéni docker-rendszerképet használ a betanítási.

Már létrehozott kell a [számítási célt](how-to-set-up-training-targets.md#batch) objektum `compute_target`. A estimator következőképpen hozhat létre:

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

A fenti kód mutatja meg a következő új paramétereket a `Estimator` konstruktort:

Paraméter | Leírás | Alapértelmezett
--|--|--
`custom_docker_base_image`| A használni kívánt kép neve. Csak adja meg a lemezkép érhető el a nyilvános docker adattárait (megkülönbözteti a kis Docker Hub). Rendszerkép használatához egy privát docker-adattárból, használhatja a konstruktor `environment_definition` paraméter helyett | `None`
`node_count`| A betanítási feladathoz használandó csomópontok száma. | `1`
`process_count_per_node`| Minden egyes csomóponton futtatandó folyamatok (vagy "dolgozó szakemberek") száma. Ebben az esetben használhatja a `2` gpu-n elérhető minden egyes csomóponton.| `1`
`distributed_backend`| Háttérbeli indításakor elosztott képzés, így az a Estimator MPI-n keresztül.  Párhuzamos és elosztott képzési elvégzésére (pl. `node_count`> 1 vagy `process_count_per_node`> 1 vagy mindkettő) állítsa be `distributed_backend='mpi'`. Az AML által használt MPI végrehajtása [nyílt MPI](https://www.open-mpi.org/).| `None`

Végül küldje el a betanítási feladatot:
```Python
run = experiment.submit(cntk_est)
```

## <a name="examples"></a>Példák
Az sklearn modell betanítása oktatóanyagot tekintse meg:
* [tutorials/01.train-models.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/01.train-models.ipynb)

Egyéni docker használatával elosztott CNTK oktatóanyagot tekintse meg:
* [képzési/06.distributed – cntk-az-custom-docker](https://github.com/Azure/MachineLearningNotebooks/blob/master/training/06.distributed-cntk-with-custom-docker)

Ezeket a notebookokat lekérése:

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>További lépések

* [Metrikák futtatása a betanítás során nyomon követése](how-to-track-experiments.md)
* [PyTorch modelleket taníthat be](how-to-train-pytorch.md)
* [TensorFlow-modellek betanításához](how-to-train-tensorflow.md)
* [Hiperparaméterek hangolása](how-to-tune-hyperparameters.md)
* [A betanított modell üzembe helyezése](how-to-deploy-and-where.md)
