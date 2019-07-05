---
title: Modell üzembe helyezése következtetésekhez grafikus processzort tartalmaz
titleSuffix: Azure Machine Learning service
description: Ez a cikk bemutatja, hogyan használhatja az Azure Machine Learning szolgáltatás üzembe helyezése a deep learning-modellt használja, mint egy webes service.service és pontszám következtetésekhez kérések GPU-kompatibilis Tensorflow.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 06/01/2019
ms.openlocfilehash: 8086d059913cc61bff0bca31681368bea6d76777
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543804"
---
# <a name="deploy-a-deep-learning-model-for-inference-with-gpu"></a>Modell üzembe helyezése deep learning GPU-következtetésekhez

Ez a cikk bemutatja, hogyan használhatja az Azure Machine Learning szolgáltatást a GPU-kompatibilis Tensorflow deep learning-webszolgáltatásként, amely a modell üzembe helyezése.

A modell rendszerbe állítása GPU-kompatibilis következtetési ehhez az Azure Kubernetes Service (AKS)-fürthöz. Következtetési vagy a modell pontozása, nem a fázis, az üzembe helyezett modell előrejelzési szolgál. Gpu-k használata helyett a processzorok ajánlat teljesítményt nyújtanak a magas párhuzamosítható számítási.

Bár ez a minta TensorFlow modellt használ, az alábbi lépéseket alkalmazhatja az bármely machine learning-keretrendszer, amely támogatja a GPU-k végezhet kisebb változtatásokat, így a pontozófájl és a környezet fájlt. 

Ebben a cikkben hajtsa végre a következő lépéseket:

* A GPU-kompatibilis AKS-fürt létrehozása
* Modell üzembe helyezése GPU tensorflow-hoz
* Mintalekérdezés kiadni az üzembe helyezett modell

## <a name="prerequisites"></a>Előfeltételek

* Az Azure Machine Learning-szolgáltatások munkaterület.
* Egy Python-disztribúció.
* Modell mentése regisztrált Tensorflow.
    * Modellek regisztrálása kapcsolatban lásd: [modellek üzembe helyezése](../service/how-to-deploy-and-where.md#registermodel).

A bemutató útmutató-sorozat része hajthatja végre [TensorFlow modell betanításához hogyan](how-to-train-tensorflow.md), a szükséges előfeltételek teljesítése.

## <a name="provision-an-aks-cluster-with-gpus"></a>A GPU-k az AKS-fürt kiépítése

Az Azure számos különböző GPU lehetőség van. Bármelyiket használhatja a következtetési. Lásd: [N-sorozatú virtuális gépek listáját](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series) a képességeit és a költségek teljes áttekintését.

Az AKS használata az Azure Machine Learning szolgáltatás további információkért lásd: [üzembe helyezése és hol](../service/how-to-deploy-and-where.md#deploy-aks).

```Python
# Choose a name for your cluster
aks_name = "aks-gpu"

# Check to see if the cluster already exists
try:
    compute_target = ComputeTarget(workspace=ws, name=aks_name)
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
> Azure számolunk fel díjat, amíg az AKS-fürt kiépítése. Ellenőrizze, hogy az AKS-fürt törlése, ha elkészült, az azt.

## <a name="write-the-entry-script"></a>A bejegyzés parancsfájl írásához

Mentse a következő kódot a munkakönyvtárban `score.py`. Ez a fájl pontszámmodell a képeket, akkor a rendszer a szolgáltatás. Betölti a mentett TensorFlow-modell, minden POST-kérelmet továbbítja a bemeneti kép, a TensorFlow-munkamenethez, és az eredményül kapott pontszámok adja vissza. Más következtetési keretrendszerek különböző pontozófájlt van szükség.

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
## <a name="define-the-conda-environment"></a>Adja meg a conda-környezet

Hozzon létre egy conda-környezet fájlt `myenv.yml` , a függőségek megadása a szolgáltatás. Fontos, hogy adja meg, hogy használ `tensorflow-gpu` nagyobb teljesítmény elérése érdekében.

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

## <a name="define-the-gpu-inferenceconfig-class"></a>A GPU InferenceConfig osztály meghatározása

Hozzon létre egy `InferenceConfig` objektum, amely lehetővé teszi a GPU-kat, és biztosítja, hogy a CUDA telepítve van a Docker-rendszerképet a.

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

- [InferenceConfig osztályban](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)
- [AksServiceDeploymentConfiguration osztályban](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py)

## <a name="deploy-the-model"></a>A modell üzembe helyezése

Az AKS-fürt üzembe helyezése a modellt, és várjon, amíg a szolgáltatás létrehozásához.

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
> Az Azure Machine Learning szolgáltatás nem telepíthető, modell- `InferenceConfig` objektum, amely egy fürtöt, amely nem rendelkezik egy GPU engedélyezni GPU vár.

További információkért lásd: [Model class](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

## <a name="issue-a-sample-query-to-your-model"></a>Mintalekérdezés kiadni a modell

Egy teszt lekérdezést küldenek az üzembe helyezett modellt. Jpeg-képek küld a modell, amikor a lemezkép pontszámmodell azt. A következő mintakód egy külső segédprogram függvény segítségével képeket tölthet be. Annak a megfelelő kódot a pir [TensorFlow-minta a Githubon](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow/utils.py). 

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
> A késés minimalizálása és a teljesítmény optimalizálása, ellenőrizze, hogy az ügyfél ugyanabban a régióban az Azure és a végpontnak. Ebben a példában az API-k jönnek létre az East US Azure-régióban.

## <a name="clean-up-the-resources"></a>Az erőforrások törlése

Miután végzett az ebben a példában az erőforrások törlése

> [!IMPORTANT]
> Az Azure számlák alapján mennyi az AKS-fürt üzembe lesz helyezve. Ellenőrizze, hogy törölni, azt elvégzése után.

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>További lépések

* [FPGA a modell üzembe helyezése](../service/how-to-deploy-fpga-web-service.md)
* [Az ONNX-modell üzembe helyezése](../service/concept-onnx.md#deploy-onnx-models-in-azure)
* [Tensorflow DNN-modellek betanításához](../service/how-to-train-tensorflow.md)
