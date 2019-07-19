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
ms.date: 06/01/2019
ms.openlocfilehash: eeb1bc35e0438a7e99ea5ed8284f0c8611108da0
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326994"
---
# <a name="deploy-a-deep-learning-model-for-inference-with-gpu"></a>Mély tanulási modell üzembe helyezése a GPU-val való következtetéshez

Ez a cikk bemutatja, hogyan használható a Azure Machine Learning szolgáltatás egy GPU-t támogató Tensorflow mély tanulási modell webszolgáltatásként való üzembe helyezéséhez.

A modell üzembe helyezése egy Azure Kubernetes Service-(ak-) fürtön a GPU-t támogató következtetések elvégzéséhez. A következtetés vagy a modell pontozása az a fázis, ahol a rendszer az üzembe helyezett modellt használja az előrejelzéshez. A GPU-k használata a processzorok helyett teljesítménybeli előnyökkel jár a nagyon párhuzamosítható számításokhoz.

Bár ez a minta egy TensorFlow modellt használ, a következő lépéseket alkalmazhatja bármely, a GPU-t támogató Machine learning-keretrendszerre a pontozási fájl és a környezeti fájl kisméretű módosításával. 

Ebben a cikkben a következő lépéseket hajtja végre:

* GPU-t támogató AK-fürt létrehozása
* Tensorflow GPU-modell üzembe helyezése
* Lekérdezés kiadása az üzembe helyezett modellhez

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure Machine Learning Services-munkaterület.
* Egy Python disztribúció.
* Egy regisztrált Tensorflow mentett modell.
    * A modellek regisztrálásával kapcsolatos további információkért lásd: [modellek üzembe helyezése](../service/how-to-deploy-and-where.md#registermodel).

A szükséges előfeltételek teljesítéséhez elvégezheti a jelen útmutató sorozat egy részét, a [TensorFlow-modell](how-to-train-tensorflow.md)betanítását.

## <a name="provision-an-aks-cluster-with-gpus"></a>AK-fürt kiépítése GPU-val

Az Azure számos különböző GPU-lehetőséggel rendelkezik. Bármelyiket használhatja a következtetésekhez. Tekintse meg [az N sorozatú virtuális gépek listáját](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series) a képességek és költségek teljes körű lebontásához.

További információ az AK Azure Machine Learning szolgáltatással való használatáról: a [telepítés és a hol](../service/how-to-deploy-and-where.md#deploy-aks).

```Python
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
> Az Azure számlázni fogja, amíg az AK-fürt kiépítve van. Ha végzett, mindenképp törölje az AK-fürtöt.

## <a name="write-the-entry-script"></a>A bejegyzés parancsfájljának írása

Mentse a következő kódot a munkakönyvtárba `score.py`. Ez a fájl a szolgáltatásnak eljuttatott képeket is felhasználja. Betölti a TensorFlow mentett modellt, átadja a bemeneti képet a TensorFlow-munkamenetnek az egyes POST-kérelmeknél, majd visszaadja az eredményül kapott pontszámokat. Más következtetési keretrendszerek eltérő pontozási fájlokat igényelnek.

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
    saver = tf.train.import_meta_graph(os.path.join(model_root, 'mnist-tf.model.meta'))
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
## <a name="define-the-conda-environment"></a>A Conda-környezet definiálása

Hozzon létre egy nevű `myenv.yml` Conda-környezeti fájlt a szolgáltatás függőségeinek megadásához. Fontos megadnia, hogy a használatával `tensorflow-gpu` gyorsított teljesítményt érhet el.

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

## <a name="define-the-gpu-inferenceconfig-class"></a>A GPU InferenceConfig osztály megadása

Hozzon `InferenceConfig` létre egy objektumot, amely engedélyezi a GPU-ket, és gondoskodik arról, hogy a CUDA telepítve legyen a Docker-lemezképpel.

```python
from azureml.core.model import Model
from azureml.core.model import InferenceConfig

aks_service_name ='aks-dnn-mnist'
gpu_aks_config = AksWebservice.deploy_configuration(autoscale_enabled = False, 
                                                    num_replicas = 3, 
                                                    cpu_cores=2, 
                                                    memory_gb=4)
model = Model(ws,"tf-dnn-mnist")

inference_config = InferenceConfig(runtime= "python", 
                                   entry_script="score.py",
                                   conda_file="myenv.yml", 
                                   enable_gpu=True)
```

További információkért lásd:

- [InferenceConfig osztály](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)
- [AksServiceDeploymentConfiguration osztály](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py)

## <a name="deploy-the-model"></a>A modell üzembe helyezése

Telepítse a modellt az AK-fürtre, és várja meg, amíg létrehozza a szolgáltatást.

```python
aks_service = Model.deploy(ws,
                           models=[model],
                           inference_config=inference_config, 
                           deployment_config=gpu_aks_config,
                           deployment_target=aks_target,
                           name=aks_service_name)

aks_service.wait_for_deployment(show_output = True)
print(aks_service.state)
```

> [!NOTE]
> Azure Machine learning szolgáltatás nem helyez üzembe olyan modellt, `InferenceConfig` amelynek a GPU-t nem tartalmazó fürtre való engedélyezése várható.

További információ: [Model Class](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

## <a name="issue-a-sample-query-to-your-model"></a>Minta lekérdezés kiadása a modellhez

Tesztelési lekérdezés küldése az üzembe helyezett modellnek. Ha JPEG-képet küld a modellnek, az a képet is leállítja. A következő mintakód egy külső segédprogram-függvényt használ a képek betöltéséhez. A megfelelő kódot a PIR [TensorFlow-minta](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow/utils.py)a githubon találja. 

```python
# Used to test your webservice
from utils import load_data 

# Load test data from model training
X_test = load_data('./data/mnist/test-images.gz', False) / 255.0
y_test = load_data('./data/mnist/test-labels.gz', True).reshape(-1)

# send a random row from the test set to score
random_index = np.random.randint(0, len(X_test)-1)
input_data = "{\"data\": [" + str(list(X_test[random_index])) + "]}"

api_key = aks_service.get_keys()[0]
headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}
resp = requests.post(aks_service.scoring_uri, input_data, headers=headers)

print("POST to url", aks_service.scoring_uri)
#print("input data:", input_data)
print("label:", y_test[random_index])
print("prediction:", resp.text)
```

> [!IMPORTANT]
> A késleltetés minimalizálásához és az átviteli sebesség optimalizálásához győződjön meg arról, hogy az ügyfél ugyanabban az Azure-régióban található, mint a végpont. Ebben a példában az API-k az USA keleti régiójában jönnek létre.

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
