---
title: Modell üzembe helyezése a GPU-val való következtetéshez
titleSuffix: Azure Machine Learning
description: Ez a cikk bemutatja, hogyan használhatja az Azure Machine Learning egy GPU-kompatibilis Tensorflow deep learning modell webszolgáltatás.szolgáltatásként, és a következtetési kérelmek pontozására.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 03/05/2020
ms.openlocfilehash: b0fd537d1930e7c9d5f7a33f56ec5d00b1556562
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78398339"
---
# <a name="deploy-a-deep-learning-model-for-inference-with-gpu"></a>Mélytanulási modell üzembe helyezése a GPU-val való következtetéshez
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ez a cikk bemutatja, hogyan használhatja az Azure Machine Learning egy GPU-kompatibilis modell webszolgáltatásként való üzembe helyezéséhez. A cikkben szereplő információk egy modell üzembe helyezésén alapul nak az Azure Kubernetes-szolgáltatás (AKS) használatával. Az AKS-fürt egy GPU-erőforrást biztosít, amelyet a modell a következtetéshez használ.

A következtetés vagy a modell pontozása az a fázis, ahol az üzembe helyezett modell előrejelzéseket készít. Gpu-k használata processzorok helyett teljesítményelőnyöket biztosít a nagymértékben párhuzamosítható számítás.

> [!IMPORTANT]
> A webszolgáltatás-telepítések esetén a GPU-következtetések csak az Azure Kubernetes-szolgáltatás ban támogatott. A __gépi tanulási folyamat__használatával a következtetés, GPU-k csak az Azure Machine Learning Compute támogatott. A pénzmosási folyamatok használatáról a [Kötegelt előrejelzések futtatása című](how-to-use-parallel-run-step.md)témakörben talál további információt. 

> [!TIP]
> Bár a kódrészletek ebben a cikkben egy TensorFlow-modellt használ, az információkat bármely gépi tanulási keretrendszer, amely támogatja a GPU-k.

> [!NOTE]
> A jelen cikkben található információk az [Azure Kubernetes-szolgáltatás](how-to-deploy-azure-kubernetes-service.md) ban a hogyan üzembe helyezés című cikkben található információkra épülnek. Amennyiben ez a cikk általában az AKS-ben való üzembe helyezést ismerteti, ez a cikk a GPU-specifikus üzembe helyezést ismerteti.

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure Machine Learning-munkaterület. További információ: [Create an Azure Machine Learning workspace](how-to-manage-workspace.md).

* Python-fejlesztési környezet az Azure Machine Learning SDK telepítve. További információ: [Azure Machine Learning SDK.](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)  

* Egy regisztrált modell, amely GPU-t használ.

    * A modellek regisztrálásáról a [Modellek telepítése (Deploy Models) (Modellek telepítése) témakörben](how-to-deploy-and-where.md#registermodel)olvashat.

    * A dokumentum létrehozásához használt Tensorflow-modell létrehozásáról és regisztrálásáról a [TensorFlow-modell betanítása](how-to-train-tensorflow.md)című témakörben található.

* A modellek [telepítésének módjáról és helyéről](how-to-deploy-and-where.md)szóló általános ismeretek.

## <a name="connect-to-your-workspace"></a>Csatlakozás a munkaterülethez

Meglévő munkaterülethez való csatlakozáshoz használja a következő kódot:

> [!IMPORTANT]
> Ez a kódrészlet arra számít, hogy a munkaterület konfigurációja az aktuális könyvtárba vagy annak szülőjébe kerül. A munkaterület létrehozásáról az [Azure Machine Learning-munkaterületek létrehozása és kezelése](how-to-manage-workspace.md)című témakörben talál további információt.   A konfiguráció fájlba mentésével kapcsolatos további tudnivalókért olvassa el a [Munkaterület konfigurációs fájljának létrehozása című témakört.](how-to-configure-environment.md#workspace)

```python
from azureml.core import Workspace

# Connect to the workspace
ws = Workspace.from_config()
```

## <a name="create-a-kubernetes-cluster-with-gpus"></a>Kubernetes-fürt létrehozása GPU-kkal

Az Azure Kubernetes-szolgáltatás számos különböző GPU-beállítást kínál. Ezek bármelyikét használhatja a modell következtetés. A képességek és költségek teljes lebontását tekintse meg [az N sorozatú virtuális gépek listájában.](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series)

A következő kód bemutatja, hogyan hozhat létre új AKS-fürtöt a munkaterülethez:

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
> Az Azure mindaddig számláz, amíg az AKS-fürt létezik. Győződjön meg arról, hogy törölje az AKS-fürtöt, ha végzett vele.

Az AKS Azure Machine Learning szolgáltatással való használatáról további információt a [Hogyan telepíthet az Azure Kubernetes szolgáltatásban.](how-to-deploy-azure-kubernetes-service.md)

## <a name="write-the-entry-script"></a>Írja meg a beviteli szkriptet

A bejegyzési parancsfájl megkapja a webszolgáltatásnak küldött adatokat, átadja azokat a modellnek, és visszaadja a pontozási eredményeket. A következő parancsfájl betölti a Tensorflow-modellindításkor, majd a modell segítségével az adatok pontozása.

> [!TIP]
> A bejegyzési parancsfájl a modellre jellemző. Például a szkriptnek ismernie kell a modellhez használni használt keretrendszert, az adatformátumokat stb.

```python
import json
import numpy as np
import os
import tensorflow as tf

from azureml.core.model import Model


def init():
    global X, output, sess
    tf.reset_default_graph()
    model_root = os.getenv('AZUREML_MODEL_DIR')
    # the name of the folder in which to look for tensorflow model files
    tf_model_folder = 'model'
    saver = tf.train.import_meta_graph(
        os.path.join(model_root, tf_model_folder, 'mnist-tf.model.meta'))
    X = tf.get_default_graph().get_tensor_by_name("network/X:0")
    output = tf.get_default_graph().get_tensor_by_name("network/output/MatMul:0")

    sess = tf.Session()
    saver.restore(sess, os.path.join(model_root, tf_model_folder, 'mnist-tf.model'))


def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    out = output.eval(session=sess, feed_dict={X: data})
    y_hat = np.argmax(out, axis=1)
    return y_hat.tolist()
```

A fájl `score.py`neve . A bejegyzési parancsfájlokkal kapcsolatos további információkért [lásd: Hogyan és hol kell telepíteni.](how-to-deploy-and-where.md)

## <a name="define-the-conda-environment"></a>Conda környezet meghatározása

A conda környezeti fájl adja meg a szolgáltatás függőségeit. Ez magában foglalja a függőségek által megkövetelt modell és a bejegyzés parancsfájl. Kérjük, vegye figyelembe, hogy meg kell adnia azureml-defaults a verion >= 1.0.45 pip-függőség, mert tartalmazza a szükséges funkciókat a modell webszolgáltatásként. A következő YAML határozza meg a környezetet egy Tensorflow modell. Meghatározza `tensorflow-gpu`, hogy melyik fogja használni a telepítésben használt GPU-t:

```yaml
name: project_environment
dependencies:
  # The python interpreter version.
  # Currently Azure ML only supports 3.5.2 and later.
- python=3.6.2

- pip:
  # You must list azureml-defaults as a pip dependency
  - azureml-defaults>=1.0.45
- numpy
- tensorflow-gpu=1.12
channels:
- conda-forge
```

Ebben a példában a `myenv.yml`fájl mentése a program szerint történik.

## <a name="define-the-deployment-configuration"></a>A központi telepítési konfiguráció meghatározása

A központi telepítési konfiguráció határozza meg az Azure Kubernetes service környezet futtatásához használt webszolgáltatás:

```python
from azureml.core.webservice import AksWebservice

gpu_aks_config = AksWebservice.deploy_configuration(autoscale_enabled=False,
                                                    num_replicas=3,
                                                    cpu_cores=2,
                                                    memory_gb=4)
```

További információt az [AksService.deploy_configuration referenciadokumentációjában talál.](/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none--compute-target-name-none-)

## <a name="define-the-inference-configuration"></a>A következtetés konfigurációjának meghatározása

A következtetés konfigurációs pontokat a belépési parancsfájl és egy környezeti objektum, amely egy DOcker-rendszerképet GPU-támogatással használ. Vegye figyelembe, hogy a környezetdefinícióhoz használt YAML-fájlnak az azureml-defaults-t pip-függőségként kell felsorolnia a >= 1.0.45 verzióval, mivel tartalmazza a modell webszolgáltatásként való üzemeltetéséhez szükséges funkciókat.

```python
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment, DEFAULT_GPU_IMAGE

myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
myenv.docker.base_image = DEFAULT_GPU_IMAGE
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
```

A környezetekkel kapcsolatos további információkért olvassa el a [Környezetek létrehozása és kezelése a betanításhoz és telepítéshez című témakört.](how-to-use-environments.md)
További információt a [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)referenciadokumentációjában talál.

## <a name="deploy-the-model"></a>A modell üzembe helyezése

Telepítse a modellt az AKS-fürtre, és várja meg, amíg létrehozza a szolgáltatást.

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
> Ha `InferenceConfig` az `enable_gpu=True`objektum rendelkezik , akkor a `deployment_target` paraméternek egy GPU-t tartalmazó fürtre kell hivatkoznia. Ellenkező esetben a központi telepítés sikertelen lesz.

További információt a Model referenciadokumentációjában [talál.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py)

## <a name="issue-a-sample-query-to-your-service"></a>Mintalekérdezés kiadása a szolgáltatásszámára

Tesztlekérdezés küldése az üzembe helyezett modellnek. Amikor jpeg képet küld a modellnek, az megszerzi a képet. A következő kódminta letölti a tesztadatokat, majd kiválasztegy véletlenszerű tesztképet a szolgáltatásnak küldve.

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

Az ügyfélalkalmazások létrehozásáról az [Ügyfél létrehozása az üzembe helyezett webszolgáltatás felhasználásához](how-to-consume-web-service.md)című témakörben talál további információt.

## <a name="clean-up-the-resources"></a>Az erőforrások eltávolítása

Ha az AKS-fürtet kifejezetten erre a példára hozta létre, törölje az erőforrásokat, miután elkészült.

> [!IMPORTANT]
> Az Azure az AKS-fürt üzembe helyezésének ideje alapján számláz. Győződjön meg róla, hogy tisztítsa meg, miután végzett vele.

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>További lépések

* [Modell üzembe helyezése fpga-n](how-to-deploy-fpga-web-service.md)
* [Modell üzembe helyezése ONNX-szel](concept-onnx.md#deploy-onnx-models-in-azure)
* [Tensorflow DNN modellek betanítása](how-to-train-tensorflow.md)
