---
title: Az Azure Machine Learning TensorFlow-modellek betanításához
description: Ismerje meg, hogyan futtathat egy csomópontos és elosztott modellek TensorFlow-betanítás a TensorFlow estimator
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 12/04/2018
ms.openlocfilehash: 5746180b894da265dfc02176c13b5f9b60f4df96
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52872781"
---
# <a name="how-to-train-tensorflow-models"></a>Hogyan TensorFlow modelleket taníthat be

Neurális hálózat (DNN) képzést nyújt tensorflow-hoz, az Azure Machine Learning biztosít egyéni `TensorFlow` osztályát az `Estimator`. Az Azure SDK `TensorFlow` estimator (, nem kell a conflated a [ `tf.estimator.Estimator` ](https://www.tensorflow.org/api_docs/python/tf/estimator/Estimator) osztály) lehetővé teszi, hogy egyszerűen a tensorflow-hoz is egyetlen csomópontot, és elosztott futtatások az Azure-beli számítási feladatok elküldéséhez.

## <a name="single-node-training"></a>Egy csomópontos képzés
A képzés a `TensorFlow` estimator hasonlít a használatával a [alap `Estimator` ](how-to-train-ml-models.md), ezért először olvassa el a cikkben található útmutató, és ellenőrizze, hogy tisztában van a bemutatott fogalmakkal.
  
TensorFlow-feladatok futtatásához, hozza létre a `TensorFlow` objektum. Már létrehozott kell a [számítási célt](how-to-set-up-training-targets.md#amlcompute) objektum `compute_target`.

```Python
from azureml.train.dnn import TensorFlow

script_params = {
    '--batch-size': 50,
    '--learning-rate': 0.01,
}

tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='train.py',
                    conda_packages=['scikit-learn'],
                    use_gpu=True)
```

Itt hogy adja meg a következő paraméterek, a TensorFlow-konstruktor:

Paraméter | Leírás
--|--
`source_directory` | Helyi könyvtár, amely tartalmazza az összes a betanítási feladathoz szükséges kódot. Ez a mappa a távoli számítási átmásolódnak a helyi gépen
`script_params` | A parancssori paraméterek, a tanítási szkriptet megadása szótárban `entry_script`, < parancssori argumentum, érték > formájában párok
`compute_target` | Arról, hogy az a tanítási szkriptet, ebben az esetben az Azure Machine Learning Compute távoli számítási célnak ([AmlCompute](how-to-set-up-training-targets.md#amlcompute)) fürt
`entry_script` | Fájl elérési útja (viszonyítva a `source_directory`), a tanítási szkriptet futtatandó távoli számítási. Ezt a fájlt, és a további fájlokat attól függ, ebben a mappában kell elhelyezni.
`conda_packages` | Szükség szerint a tanítási szkriptet conda-n keresztül kell telepíteni a Python-csomagok listáját. Ebben az esetben használja a tanítási szkriptet `sklearn` tölt be az adatokat, így adja meg ezt a csomagot kell telepíteni.  A konstruktor rendelkezik egy másik nevű paramétert `pip_packages` használható az esetleges pip csomagokat szükséges
`use_gpu` | Ezt a jelzőt `True` kihasználhatja a GPU, a betanításhoz. Alapértelmezés szerint a `False`.

Mivel a TensorFlow estimator használ, a képzési használt tároló alapértelmezés szerint a TensorFlow-csomag és a szükséges képzés a processzorok és gpu-k kapcsolódó függőségeket tartalmaznak.

Ezután küldje el a TensorFlow-feladatot:
```Python
run = exp.submit(tf_est)
```

## <a name="distributed-training"></a>Elosztott betanítás
A TensorFlow Estimator emellett lehetővé teszi különböző Azure-beli virtuális Processzor és GPU fürtök ipari méretekben a modellek betanítása. Könnyedén futtathat elosztott TensorFlow-betanítás néhány API-hívások, amíg az Azure Machine Learning fogja kezelni a háttérben, az infrastruktúra és a vezénylési ilyen számítási feladat végrehajtásához szükséges.

Az Azure Machine Learning elosztott képzési két módszert támogat, a tensorflow-hoz:
* MPI-alapú elosztott képzési használatával a [Horovod](https://github.com/uber/horovod) keretrendszer
* natív [elosztott TensorFlow](https://www.tensorflow.org/deploy/distributed) paraméter metoda keresztül

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod) Uber által fejlesztett elosztott betanítás egy kör-allreduce nyílt forráskódú keretrendszer van.

Elosztott tensorflow-hoz a Horovod keretrendszerrel futtatásához a következőképpen hozhat létre a TensorFlow-objektum:

```Python
from azureml.train.dnn import TensorFlow

tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params={},
                    compute_target=compute_target,
                    entry_script='train.py',
                    node_count=2,
                    process_count_per_node=1,
                    distributed_backend='mpi',
                    use_gpu=True)
```

A fenti kód a következő új paraméterek, a TensorFlow-konstruktor tünteti fel:

Paraméter | Leírás | Alapértelmezett
--|--|--
`node_count` | A betanítási feladathoz használandó csomópontok száma. | `1`
`process_count_per_node` | Minden egyes csomóponton futtatandó folyamatok (vagy "dolgozó szakemberek") száma.|`1`
`distributed_backend` | Háttérbeli indításakor elosztott képzés, így az a Estimator MPI-n keresztül. Ha szeretné-e a párhuzamos és elosztott képzési végez (például `node_count`> 1 vagy `process_count_per_node`> 1 vagy mindkét) beállítása MPI (és Horovod), `distributed_backend='mpi'`. Az Azure Machine Learning által használt MPI végrehajtása [nyílt MPI](https://www.open-mpi.org/). | `None`

A fenti példában fognak futni az elosztott képzési két feldolgozó egy feldolgozó csomópontonkénti.

Horovod és annak függőségeit lesz telepítve, így egyszerűen importálhatja azt a tanítási szkriptet a `train.py` módon:

```Python
import tensorflow as tf
import horovod
```

Végül küldje el a TensorFlow-feladatot:
```Python
run = exp.submit(tf_est)
```

### <a name="parameter-server"></a>Paraméterkiszolgáló
Futtathat [natív elosztott TensorFlow](https://www.tensorflow.org/deploy/distributed), melyik a paraméter kiszolgálómodellt használ. Ezzel a módszerrel, betanítását paraméter kiszolgálók és a munkavállalók fürtök között. A feldolgozók kiszámítása során képzés, a átmenetekhez, a paraméter kiszolgálók összesíteni az átmenetek során.

Hozza létre a TensorFlow-objektum:

```Python
from azureml.train.dnn import TensorFlow

tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params={},
                    compute_target=compute_target,
                    entry_script='train.py',
                    node_count=2,
                    worker_count=2,
                    parameter_server_count=1,
                    distributed_backend='ps',
                    use_gpu=True)
```

A következő paraméterek odafigyelni a TensorFlow-konstruktor a fenti kódban:

Paraméter | Leírás | Alapértelmezett
--|--|--
`worker_count` | Feldolgozók száma. | `1`
`parameter_server_count` | A paraméter-kiszolgálók száma. | `1`
`distributed_backend` | Használjon elosztott képzéshez-háttérrendszerrel. Ehhez az elosztott képzési paraméter-kiszolgálón keresztül, állítsa be `distributed_backend='ps'` | `None`

#### <a name="note-on-tfconfig"></a>Vegye figyelembe a `TF_CONFIG`
Is szüksége lesz a hálózati címek és portok, a fürt a [ `tf.train.ClusterSpec` ](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec), így az Azure Machine Learning beállítja a `TF_CONFIG` környezeti változót az Ön számára.

A `TF_CONFIG` környezeti változót a JSON-karakterláncot. Íme egy példa a változó paraméter kiszolgálóhoz:
```
TF_CONFIG='{
    "cluster": {
        "ps": ["host0:2222", "host1:2222"],
        "worker": ["host2:2222", "host3:2222", "host4:2222"],
    },
    "task": {"type": "ps", "index": 0},
    "environment": "cloud"
}'
```

Ha használ tensorflow-hoz a magas szintű [ `tf.estimator` ](https://www.tensorflow.org/api_docs/python/tf/estimator) API, a tensorflow-hoz fogja elemezni a `TF_CONFIG` változót és a fürt létrehozási specifikációja az Ön számára. 

Ha tensorflow-hoz az alacsonyabb szintű core API-k képzéshez inkább használ, elemezni kell a `TF_CONFIG` változót és a build a `tf.train.ClusterSpec` magának a betanítási kódban. A [ebben a példában](https://aka.ms/aml-notebook-tf-ps), ezért teheti **a tanítási szkriptet** módon:

```Python
import os, json
import tensorflow as tf

tf_config = os.environ.get('TF_CONFIG')
if not tf_config or tf_config == "":
    raise ValueError("TF_CONFIG not found.")
tf_config_json = json.loads(tf_config)
cluster_spec = tf.train.ClusterSpec(cluster)

```

Miután végzett a tanítási szkriptet írása, és a TensorFlow-objektum létrehozása, elküldheti a betanítási feladatot:
```Python
run = exp.submit(tf_est)
```

## <a name="examples"></a>Példák

Notebooks az elosztott deep learninget tekintse meg:
* [How-to-use-azureml/Training-with-deep-Learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>További lépések
* [Metrikák futtatása a betanítás során nyomon követése](how-to-track-experiments.md)
* [Hiperparaméterek hangolása](how-to-tune-hyperparameters.md)
* [A betanított modell üzembe helyezése](how-to-deploy-and-where.md)
