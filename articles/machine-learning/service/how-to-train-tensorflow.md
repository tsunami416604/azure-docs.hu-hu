---
title: A TensorFlow & Keras-modellek betanításához
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan futtathat egy csomópontos és elosztott tensorflow-hoz és a Keras modellek-betanítás a tensorflow-hoz és a Keras estimators
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 04/19/2019
ms.custom: seodec18
ms.openlocfilehash: 78db7d21774750892c831ac220244c54594b78f3
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2019
ms.locfileid: "59548354"
---
# <a name="train-tensorflow-and-keras-models-with-azure-machine-learning-service"></a>Tensorflow-hoz és a Keras modellek Azure Machine Learning szolgáltatással

Neurális hálózat (DNN) képzést nyújt tensorflow-hoz, az Azure Machine Learning biztosít egyéni `TensorFlow` osztályát az `Estimator`. Az Azure SDK [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) estimator (, nem kell a conflated a [ `tf.estimator.Estimator` ](https://www.tensorflow.org/api_docs/python/tf/estimator/Estimator) osztály) lehetővé teszi, hogy könnyedén beküldhető TensorFlow betanítási feladatokat az Azure-ban egyaránt egycsomópontos, és elosztott futtatások a számítási.

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
                    conda_packages=['scikit-learn'], # in case you need scikit-learn in train.py
                    use_gpu=True)
```

Itt hogy adja meg a következő paraméterek, a TensorFlow-konstruktor:

Paraméter | Leírás
--|--
`source_directory` | Helyi könyvtár, amely tartalmazza az összes a betanítási feladathoz szükséges kódot. Ez a mappa a távoli számítási átmásolódnak a helyi gépen
`script_params` | A parancssori paraméterek, a tanítási szkriptet megadása szótárban `entry_script`, < parancssori argumentum, érték > formájában párokat.  Adja meg a részletes azt a jelzőt, `script_params`, használjon `<command-line argument, "">`.
`compute_target` | Arról, hogy az a tanítási szkriptet, ebben az esetben az Azure Machine Learning Compute távoli számítási célnak ([AmlCompute](how-to-set-up-training-targets.md#amlcompute)) fürt
`entry_script` | Fájl elérési útja (viszonyítva a `source_directory`), a tanítási szkriptet futtatandó távoli számítási. Ezt a fájlt, és a további fájlokat attól függ, ebben a mappában kell elhelyezni.
`conda_packages` | Szükség szerint a tanítási szkriptet conda-n keresztül kell telepíteni a Python-csomagok listáját. Ebben az esetben használja a tanítási szkriptet `sklearn` tölt be az adatokat, így adja meg ezt a csomagot kell telepíteni.  A konstruktor rendelkezik egy másik nevű paramétert `pip_packages` használható az esetleges pip csomagokat szükséges
`use_gpu` | Ezt a jelzőt `True` kihasználhatja a GPU, a betanításhoz. Alapértelmezés szerint a `False`.

Mivel a TensorFlow estimator használ, a képzési használt tároló alapértelmezés szerint a TensorFlow-csomag és a szükséges képzés a processzorok és gpu-k kapcsolódó függőségeket tartalmaznak.

Ezután küldje el a TensorFlow-feladatot:
```Python
run = exp.submit(tf_est)
```

## <a name="keras-support"></a>Keras-támogatás
[Keras](https://keras.io/) egy népszerű magas szintű DNN Python API, amely támogatja a TensorFlow, CNTK vagy Theano háttérrendszerek szerint. TensorFlow-háttérrendszer-t használja, ha egyszerűen használhatja a TensFlow estimator Keras modell betanításához. Íme egy példa egy TensorFlow estimator hozzáadott Keras:

```Python
from azureml.train.dnn import TensorFlow

keras_est = TensorFlow(source_directory='./my-keras-proj',
                       script_params=script_params,
                       compute_target=compute_target,
                       entry_script='keras_train.py',
                       pip_packages=['keras'], # just add keras through pip
                       use_gpu=True)
```
A fenti TensorFlow estimator konstruktor arra utasítja az Azure Machine Learning szolgáltatás Keras telepítéséhez a pip, a végrehajtási környezetben keresztül. És a `keras_train.py` importálhatja a Keras API egy Keras-modell betanításához. Egy teljes példát felfedezése [a Jupyter notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb).

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
`distributed_backend` | Háttérbeli indításakor elosztott képzés, így az a Estimator MPI-n keresztül. Ha szeretné-e a párhuzamos és elosztott képzési végez (például `node_count`> 1 vagy `process_count_per_node`> 1 vagy mindkét) beállítása MPI (és Horovod) `distributed_backend='mpi'`. Az Azure Machine Learning által használt MPI végrehajtása [nyílt MPI](https://www.open-mpi.org/). | `None`

A fenti példában fognak futni az elosztott képzési két feldolgozó egy feldolgozó csomópontonkénti.

Horovod és annak függőségeit lesz telepítve, így importálhat a tanítási szkriptet `train.py` módon:

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

Ismerje meg a különböző [notebooks az elosztott mély tanulás a Githubon](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>További lépések
* [Metrikák futtatása a betanítás során nyomon követése](how-to-track-experiments.md)
* [Hiperparaméterek hangolása](how-to-tune-hyperparameters.md)
* [A betanított modell üzembe helyezése](how-to-deploy-and-where.md)
