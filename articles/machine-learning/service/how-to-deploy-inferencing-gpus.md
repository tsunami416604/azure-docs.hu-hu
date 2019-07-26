---
title: Modell üzembe helyezése a GPU-val való következtetéshez
titleSuffix: Azure Machine Learning service
description: Ebből a cikkből megtudhatja, hogyan használhatja a Azure Machine Learning szolgáltatást egy GPU-t támogató Tensorflow mély tanulási modell webszolgáltatásként való üzembe helyezésére. a szolgáltatás és a pontszámokra vonatkozó következtetések.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 07/24/2019
ms.openlocfilehash: 520e7fe953256e4c489e4c540493d9f74dda3aef
ms.sourcegitcommit: 5604661655840c428045eb837fb8704dca811da0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2019
ms.locfileid: "68494358"
---
# <a name="deploy-a-deep-learning-model-for-inference-with-gpu"></a>Mély tanulási modell üzembe helyezése a GPU-val való következtetéshez

Ez a cikk bemutatja, hogyan használható a Azure Machine Learning szolgáltatás egy GPU-t támogató modell webszolgáltatásként való üzembe helyezéséhez. A cikkben található információk az Azure Kubernetes Service (ak) modell üzembe helyezésén alapulnak. Az AK-fürt egy GPU-erőforrást biztosít, amelyet a modell használ a következtetésekhez.

A következtetés vagy a modell pontozása az a fázis, amelyben az üzembe helyezett modellt használják az előrejelzések készítéséhez. A GPU-k használata a CPU-k helyett teljesítménybeli előnyökkel jár a nagyon párhuzamosítható számításokhoz.

> [!TIP]
> Bár ebben a cikkben a kódrészletek usee egy TensorFlow modellt, a GPU-t támogató Machine learning-keretrendszerre is alkalmazhatja az adatokat.

## <a name="prerequisites"></a>Előfeltételek

* Az Azure Machine Learning szolgáltatás munkaterületén. További információ: [Azure Machine learning szolgáltatás munkaterületének létrehozása](setup-create-workspace.md).

* A Azure Machine Learning SDK-val telepített Python-fejlesztési környezet. További információ: a munkaterület létrehozása című cikk [PYTHON SDK](setup-create-workspace.md#sdk) szakasza.

* GPU-t használó regisztrált modell.

    * A modellek regisztrálásával kapcsolatos további információkért lásd: [modellek üzembe helyezése](../service/how-to-deploy-and-where.md#registermodel).

    * A dokumentum létrehozásához használt Tensorflow-modell létrehozásához és regisztrálásához tekintse [meg a Tensorflow-modell](how-to-train-tensorflow.md)betanítása című témakört.

* Általános ismeretek a [modellek üzembe helyezésének módjáról és helyéről](how-to-deploy-and-where.md).

## <a name="connect-to-your-workspace"></a>Kapcsolódás a munkaterülethez

Egy meglévő munkaterülethez való kapcsolódáshoz használja a következő kódot:

> [!IMPORTANT]
> Ez a kódrészlet arra vár, hogy a munkaterület-konfiguráció az aktuális könyvtárba vagy a szülőbe legyen mentve. További információ a munkaterület létrehozásáról és a konfiguráció fájlra való mentéséről: [Azure Machine learning szolgáltatás munkaterületének létrehozása](setup-create-workspace.md).

```python
from azureml.core import Workspace

# Connect to the workspace
ws = Workspace.from_config()
```

## <a name="create-a-kubernetes-cluster-with-gpus"></a>Kubernetes-fürt létrehozása GPU-val

Az Azure Kubernetes szolgáltatás számos különböző GPU-beállítást biztosít. A Model következtetéséhez bármelyiket használhatja. Tekintse meg [az N sorozatú virtuális gépek listáját](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series) a képességek és költségek teljes körű lebontásához.

A következő kód bemutatja, hogyan hozhat létre új AK-fürtöt a munkaterülethez:

```python
from azureml.core.compute import ComputeTarget, AksCompute
from azureml.exceptions import ComputeTargetException

# Choose a name for your cluster
aks_name = "aks-gpu"

# Check to see if the cluster already exists
try:
    aks_target = ComputeTarget(workspace=ws, name=aks_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    # Provision AKS cluster with GPU machine
    prov_config = AksCompute.provisioning_configuration(vm_size="Standard_NC6")

    # Create the cluster
    aks_target = ComputeTarget.create(
        workspace=ws, name=aks_name, provisioning_configuration=prov_config
    )

    aks_target.wait_for_completion(show_output=True)
```

> [!IMPORTANT]
> Az Azure számlázni fogja, amíg az AK-fürt létezik. Ha végzett, mindenképp törölje az AK-fürtöt.

További információ az Azure Kubernetes szolgáltatás Azure Machine Learning szolgáltatással való használatáról: a [telepítés és a hol](how-to-deploy-and-where.md#deploy-aks).

## <a name="write-the-entry-script"></a>A bejegyzés parancsfájljának írása

A bejegyzési parancsfájl fogadja a webszolgáltatásnak küldött adatok fogadását, átadja a modellnek, és visszaadja a pontozás eredményét. A következő szkript betölti a Tensorflow modellt az indításkor, majd a modell használatával szerzi be az adatgyűjtést.

> [!TIP]
> A bejegyzési parancsfájl a modellre jellemző. A szkriptnek például tudnia kell, hogy milyen keretrendszert kell használni a modellhez, az adatformátumokhoz stb.

```python
import json
import numpy as np
import os
import tensorflow as tf

from azureml.core.model import Model


def init():
    global X, output, sess
    tf.reset_default_graph()
    model_root = Model.get_model_path('tf-dnn-mnist')
    saver = tf.train.import_meta_graph(
        os.path.join(model_root, 'mnist-tf.model.meta'))
    X = tf.get_default_graph().get_tensor_by_name("network/X:0")
    output = tf.get_default_graph().get_tensor_by_name("network/output/MatMul:0")

    sess = tf.Session()
    saver.restore(sess, os.path.join(model_root, 'mnist-tf.model'))


def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    out = output.eval(session=sess, feed_dict={X: data})
    y_hat = np.argmax(out, axis=1)
    return y_hat.tolist()
```

A fájl neve `score.py`. A beléptetési parancsfájlokkal kapcsolatos további információkért lásd: [how and hol üzembe helyezés](how-to-deploy-and-where.md).

## <a name="define-the-conda-environment"></a>A Conda-környezet definiálása

A Conda-környezeti fájl meghatározza a szolgáltatás függőségeit. Magában foglalja a modell és a beléptetési parancsfájl által igényelt függőségeket is. A következő YAML határozzák meg egy Tensorflow-modell környezetét. Ez határozza `tensorflow-gpu`meg, hogy a rendszer milyen GPU-t használ az üzemelő példányban:

```yaml
name: project_environment
dependencies:
  # The python interpreter version.
  # Currently Azure ML only supports 3.5.2 and later.
- python=3.6.2

- pip:
  - azureml-defaults==1.0.43.*
- numpy
- tensorflow-gpu=1.12
channels:
- conda-forge
```

Ebben a példában a fájl a következőként lesz `myenv.yml`mentve:.

## <a name="define-the-deployment-configuration"></a>A központi telepítési konfiguráció megadása

A központi telepítési konfiguráció meghatározza a webszolgáltatás futtatásához használt Azure Kubernetes szolgáltatási környezetet:

```python
from azureml.core.webservice import AksWebservice

gpu_aks_config = AksWebservice.deploy_configuration(autoscale_enabled=False,
                                                    num_replicas=3,
                                                    cpu_cores=2,
                                                    memory_gb=4)
```

További információt a [AksService. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none-)dokumentációjában talál.

## <a name="define-the-inference-configuration"></a>A következtetési konfiguráció megadása

A következtetési konfiguráció a bejegyzési parancsfájl és a Conda környezeti fájlra mutat. Lehetővé teszi a GPU támogatását is, amely telepíti a CUDA-t a webszolgáltatás számára létrehozott Docker-rendszerképbe:

```python
from azureml.core.model import InferenceConfig

inference_config = InferenceConfig(runtime="python",
                                   entry_script="score.py",
                                   conda_file="myenv.yml",
                                   enable_gpu=True)
```

További információkért tekintse meg a [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)dokumentációját.

## <a name="deploy-the-model"></a>A modell üzembe helyezése

Telepítse a modellt az AK-fürtre, és várja meg, amíg létrehozza a szolgáltatást.

```python
from azureml.core.model import Model

# Name of the web service that is deployed
aks_service_name = 'aks-dnn-mnist'
# Get the registerd model
model = Model(ws, "tf-dnn-mnist")
# Deploy the model
aks_service = Model.deploy(ws,
                           models=[model],
                           inference_config=inference_config,
                           deployment_config=gpu_aks_config,
                           deployment_target=aks_target,
                           name=aks_service_name)

aks_service.wait_for_deployment(show_output=True)
print(aks_service.state)
```

> [!NOTE]
> Ha az `InferenceConfig` objektumnak `enable_gpu=True`van, akkor `deployment_target` a paraméternek egy GPU-t biztosító fürtre kell hivatkoznia. Ellenkező esetben a telepítés sikertelen lesz.

További információ: a [modell](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py)dokumentációja.

## <a name="issue-a-sample-query-to-your-service"></a>Lekérdezés kiadása a szolgáltatásnak

Tesztelési lekérdezés küldése az üzembe helyezett modellnek. Ha JPEG-képet küld a modellnek, az a képet is leállítja. A következő mintakód-minta letölti a teszteket, majd kiválasztja a szolgáltatásnak küldendő véletlenszerű tesztet. 

```python
# Used to test your webservice
import os
import urllib
import gzip
import numpy as np
import struct
import requests

# load compressed MNIST gz files and return numpy arrays
def load_data(filename, label=False):
    with gzip.open(filename) as gz:
        struct.unpack('I', gz.read(4))
        n_items = struct.unpack('>I', gz.read(4))
        if not label:
            n_rows = struct.unpack('>I', gz.read(4))[0]
            n_cols = struct.unpack('>I', gz.read(4))[0]
            res = np.frombuffer(gz.read(n_items[0] * n_rows * n_cols), dtype=np.uint8)
            res = res.reshape(n_items[0], n_rows * n_cols)
        else:
            res = np.frombuffer(gz.read(n_items[0]), dtype=np.uint8)
            res = res.reshape(n_items[0], 1)
    return res

# one-hot encode a 1-D array
def one_hot_encode(array, num_of_classes):
    return np.eye(num_of_classes)[array.reshape(-1)]

# Download test data
os.makedirs('./data/mnist', exist_ok=True)
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename='./data/mnist/test-images.gz')
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz', filename='./data/mnist/test-labels.gz')

# Load test data from model training
X_test = load_data('./data/mnist/test-images.gz', False) / 255.0
y_test = load_data('./data/mnist/test-labels.gz', True).reshape(-1)

# send a random row from the test set to score
random_index = np.random.randint(0, len(X_test)-1)
input_data = "{\"data\": [" + str(list(X_test[random_index])) + "]}"

api_key = aks_service.get_keys()[0]
headers = {'Content-Type': 'application/json',
           'Authorization': ('Bearer ' + api_key)}
resp = requests.post(aks_service.scoring_uri, input_data, headers=headers)

print("POST to url", aks_service.scoring_uri)
print("label:", y_test[random_index])
print("prediction:", resp.text)
```

Az ügyfélalkalmazások létrehozásával kapcsolatos további információkért lásd: [ügyfél létrehozása a telepített webszolgáltatás](how-to-consume-web-service.md)használatára.

## <a name="clean-up-the-resources"></a>Az erőforrások tisztítása

Ha az AK-fürtöt kifejezetten ehhez a példához hozta létre, törölje az erőforrásokat, miután elkészült.

> [!IMPORTANT]
> Az Azure-számlázás az AK-fürt üzembe helyezésének időtartamán alapul. Ha elkészült, ügyeljen rá, hogy megtisztítsa.

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>További lépések

* [Modell üzembe helyezése a FPGA](../service/how-to-deploy-fpga-web-service.md)
* [Modell üzembe helyezése a ONNX](../service/concept-onnx.md#deploy-onnx-models-in-azure)
* [Tensorflow DNN-modellek betanítása](../service/how-to-train-tensorflow.md)
