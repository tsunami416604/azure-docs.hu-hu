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
ms.date: 05/06/2019
ms.custom: seodec18
ms.openlocfilehash: 82c9aa961221b582bb16438f30a0584232164393
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2019
ms.locfileid: "65915098"
---
# <a name="train-tensorflow-and-keras-models-with-azure-machine-learning-service"></a>Tensorflow-hoz és a Keras modellek Azure Machine Learning szolgáltatással

Könnyedén futtathat az Azure-beli számítási TensorFlow-betanítási feladatok használatával a [ `TensorFlow` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) estimator osztály az Azure Machine Learning SDK. A `TensorFlow` estimator irányítja az Azure Machine Learning szolgáltatás a feladat futtatásához a részletes Neurális hálózat (DNN) képzési TensorFlow-kompatibilis tárolója.

A `TensorFlow` estimator Ezenfelül készít egy absztrakciós réteget végrehajtási, ami azt jelenti, hogy könnyen konfigurálhatja az eltérő számítási célnak paraméteres fut a betanítási szkriptekhez módosítása nélkül.

## <a name="get-started"></a>Bevezetés

Mivel a `TensorFlow` estimator osztály hasonlít az alapszintű [ `Estimator` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py), azt javasoljuk, hogy először olvassa el a [Estimator útmutató cikkét](how-to-train-ml-models.md) címtérrel kapcsolatos fogalmak megismeréséhez.

Az Azure Machine Learning szolgáltatás használatába [a rövid útmutató](quickstart-run-cloud-notebook.md). Ha elkészült, rendelkezni fog egy [Azure Machine Learning-munkaterületet](concept-workspace.md) és az összes a [notebookok minta](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml) beleértve a tensorflow-hoz és a Keras képzési dnn-eket.

## <a name="single-node-training"></a>Egy csomópontos képzés

TensorFlow-feladatok futtatásához, hozza létre a [ `TensorFlow` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) objektumra, és küldje el azt.

A következő kódot egy TensorFlow estimator példányosítja és próbaképpen küldi el. A tanítási szkriptet `train.py` a megadott parancsfájl-paraméterek használatával fog futni. A feladat futni fog a GPU-kompatibilis [számítási célt](how-to-set-up-training-targets.md), és a scikit-ismerje meg, majd telepíteni a függőség beállításához `train.py`.

```Python
from azureml.train.dnn import TensorFlow

# training script parameters passed as command-line arguments
script_params = {
    '--batch-size': 50,
    '--learning-rate': 0.01,
}

# TensorFlow constructor
tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='train.py', # relative path to your TensorFlow job
                    conda_packages=['scikit-learn'],
                    use_gpu=True)

# submit the TensorFlow job
run = exp.submit(tf_est)
```

## <a name="distributed-training"></a>Elosztott betanítás

A [ `TensorFlow` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) estimator is támogatja az elosztott képzési Processzor és GPU-fürtök között. Könnyedén futtathat elosztott TensorFlow-feladatok és az Azure Machine Learning Service szolgáltatással kezelheti az infrastruktúra és a vezénylési az Ön számára.

Az Azure Machine Learning szolgáltatás elosztott képzési két módszert támogat, a tensorflow-hoz:

* [MPI-alapú](https://www.open-mpi.org/) elosztott képzési használatával a [Horovod](https://github.com/uber/horovod) keretrendszer
* Natív [elosztott TensorFlow](https://www.tensorflow.org/deploy/distributed) metoda paraméter használatával

### <a name="horovod"></a>Horovod

[Horovod](https://github.com/uber/horovod) Uber által fejlesztett elosztott betanítás egy nyílt forráskódú keretrendszer van. Biztosít egy elosztott GPU TensorFlow-feladatok egyszerű elérési útját.

Az alábbi minta egy elosztott betanítási feladat Horovod a két feldolgozó elosztva a két csomópont fut.

```Python
from azureml.train.dnn import TensorFlow

# Tensorflow constructor
tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params={},
                    compute_target=compute_target,
                    entry_script='train.py', # relative path to your TensorFlow job
                    node_count=2,
                    process_count_per_node=1,
                    distributed_backend='mpi', # specifies Horovod backend
                    use_gpu=True)

# submit the TensorFlow job
run = exp.submit(tf_est)
```

Horovod és annak függőségeit lesz telepítve, ezért importálhatja a tanítási szkriptet.

```Python
import tensorflow as tf
import horovod
```

### <a name="parameter-server"></a>Paraméterkiszolgáló

Futtathat [natív elosztott TensorFlow](https://www.tensorflow.org/deploy/distributed), melyik a paraméter kiszolgálómodellt használ. Ezzel a módszerrel, betanítását paraméter kiszolgálók és a munkavállalók fürtök között. A feldolgozók kiszámítása során képzés, a átmenetekhez, a paraméter kiszolgálók összesíteni az átmenetek során.

Az alábbi minta egy elosztott betanítási feladat paraméter metoda elosztva a két csomópont négy feldolgozók használata futtatja.

```Python
from azureml.train.dnn import TensorFlow

# Tensorflow constructor
tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params={},
                    compute_target=compute_target,
                    entry_script='train.py', # relative path to your TensorFlow job
                    node_count=2,
                    worker_count=2,
                    parameter_server_count=1,
                    distributed_backend='ps', # specifies parameter server backend
                    use_gpu=True)

# submit the TensorFlow job
run = exp.submit(tf_est)
```

#### <a name="note-on-tfconfig"></a>Vegye figyelembe a `TF_CONFIG`

Biztosítani kell a hálózati címek és portok, a fürt a [ `tf.train.ClusterSpec` ](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec), így az Azure Machine Learning beállítja a `TF_CONFIG` környezeti változót az Ön számára.

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

A tensorflow-hoz a magas szintű [ `tf.estimator` ](https://www.tensorflow.org/api_docs/python/tf/estimator) API, a tensorflow-hoz fogja elemezni a `TF_CONFIG` változót és a fürt létrehozási specifikációja az Ön számára.

A betanítási tensorflow-hoz az alacsonyabb szintű core API-k, elemezni a `TF_CONFIG` változót és a build a `tf.train.ClusterSpec` a betanítási kódban. A [ebben a példában](https://aka.ms/aml-notebook-tf-ps), ezért teheti **a tanítási szkriptet** módon:

```Python
import os, json
import tensorflow as tf

tf_config = os.environ.get('TF_CONFIG')
if not tf_config or tf_config == "":
    raise ValueError("TF_CONFIG not found.")
tf_config_json = json.loads(tf_config)
cluster_spec = tf.train.ClusterSpec(cluster)

```

## <a name="keras-support"></a>Keras-támogatás

[Keras](https://keras.io/) egy népszerű, magas szintű DNN Python API, amely támogatja a TensorFlow, CNTK és a háttérrendszerek, Theano. TensorFlow néven foghatók használja, ha a Keras hozzáadása rendkívül egyszerű, beleértve egy `pip_package` konstruktorparamétert.

Az alábbi minta példányosít egy [ `TensorFlow` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) estimator próbaképpen küldi el. A estimator futtatja a Keras tanítási szkriptet `keras_train.py`. A feladat futni fog a gpu-kompatibilis [számítási célt](how-to-set-up-training-targets.md) a Keras függőségként keresztül pip telepítve.

```Python
from azureml.train.dnn import TensorFlow

keras_est = TensorFlow(source_directory='./my-keras-proj',
                       script_params=script_params,
                       compute_target=compute_target,
                       entry_script='keras_train.py', # relative path to your TensorFlow job
                       pip_packages=['keras'], # add keras through pip
                       use_gpu=True)
```

## <a name="export-to-onnx"></a>ONNX exportálása

Az optimalizált következtetési beolvasni a [ONNX-futtatókörnyezet](concept-onnx.md), a TensorFlow betanított modell átválthat az ONNX-formátumra. Tekintse meg a [példa](https://github.com/onnx/tensorflow-onnx/blob/master/examples/call_coverter_via_python.py).

## <a name="examples"></a>Példák

A különböző keretrendszerek használatával is egyetlen csomópontot, és elosztott TensorFlow végrehajtások működő Kódminták találja [a GitHub-oldalát](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning).

## <a name="next-steps"></a>További lépések

* [Metrikák futtatása a betanítás során nyomon követése](how-to-track-experiments.md)
* [Hiperparaméterek hangolása](how-to-tune-hyperparameters.md)
* [A betanított modell üzembe helyezése](how-to-deploy-and-where.md)
