---
title: Modell üzembe helyezése következtetésekhez grafikus processzort tartalmaz
titleSuffix: Azure Machine Learning service
description: Megtudhatja, hogyan szeretné üzembe helyezni a deep learning-modell, egy webszolgáltatás, amelyet használ egy GPU következtetésekhez. Ebben a cikkben egy Tensorflow modell üzembe lett helyezve az Azure Kubernetes Service-fürthöz. A fürt a gazdagép a webszolgáltatás és a pontszám következtetésekhez érkező GPU-kompatibilis virtuális gép használ.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 05/02/2019
ms.openlocfilehash: 64d42b9082895e372bb780d2db023294c1a0a380
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2019
ms.locfileid: "65884737"
---
# <a name="deploy-a-deep-learning-model-for-inference-with-gpu"></a>Modell üzembe helyezése deep learning GPU-következtetésekhez

Ismerje meg, hogyan használja egy gépi tanulási modellt webszolgáltatásként üzembe helyezett GPU következtetésekhez. Következtetésekhez vagy a modell pontozása, nem a fázis, az üzembe helyezett modell előrejelzési leggyakrabban a termelési adatok szolgál.

Ez a cikk bemutatja, hogyan például Tensorflow deep learning-modell segítségével GPU-kompatibilis virtuális gépen (VM) az Azure Kubernetes Service (AKS)-fürt üzembe helyezése az Azure Machine Learning szolgáltatás használatával. Amikor kérések érkeznek a szolgáltatáshoz, a modellt használ a GPU a következtetésekhez számítási feladatok futtatásához.

Gpu-kat kínálnak processzorokat a magas párhuzamosítható számítási teljesítményt nyújtanak. A GPU-kompatibilis virtuális gépek kiváló használati esetek tartalmaznak deep learning eszköz tanuláshoz és következtetésekhez, különösen nagyméretű váró kérelmek a modell.

Ez a példa bemutatja, hogyan helyezhet üzembe egy modell mentése az Azure Machine Learning tensorflow-hoz. Tegye a következőket:

* A GPU-kompatibilis AKS-fürt létrehozása
* Modell üzembe helyezése GPU tensorflow-hoz

## <a name="prerequisites"></a>Előfeltételek

* Az Azure Machine Learning-szolgáltatások munkaterület
* Egy Python-disztribúció
* Modell mentése regisztrált Tensorflow. Modellek regisztrálása kapcsolatban lásd: [modellek üzembe helyezése](../service/how-to-deploy-and-where.md#registermodel).

Ez a cikk a Jupyter notebook alapján [Tensorflow-modellek üzembe helyezése az aks](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/production-deploy-to-aks-gpu/production-deploy-to-aks-gpu.ipynb). A Jupyter notebook modellek mentett TensorFlow használ, és telepíti őket egy AKS-fürtöt. A notebook bármely machine learning-keretrendszer, amely támogatja a GPU-k végezhet kisebb változtatásokat, így a pontozófájl és a környezet fájlt is alkalmazhat.  

## <a name="provision-an-aks-cluster-with-gpus"></a>A GPU-k az AKS-fürt kiépítése

Az Azure számos különböző GPU lehetőség van. Bármelyiket használhatja a következtetési. Lásd: [N-sorozatú virtuális gépek listáját](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series) a képességeit és a költségek teljes áttekintését.

Az AKS használata az Azure Machine Learning szolgáltatás további információkért lásd: [üzembe helyezése és hol](../service/how-to-deploy-and-where.md#deploy-aks).

```python
# Provision AKS cluster with GPU machine
prov_config = AksCompute.provisioning_configuration(vm_size="Standard_NC6")

# Create the cluster
aks_target = ComputeTarget.create(
    workspace=ws, name=aks_name, provisioning_configuration=prov_config
)

aks_target.wait_for_deployment()
```

> [!IMPORTANT]
> Azure számolunk fel díjat, amíg az AKS-fürt kiépítése. Ellenőrizze, hogy az AKS-fürt törlése, ha elkészült, az azt.

## <a name="write-the-entry-script"></a>A bejegyzés parancsfájl írásához

Mentse a következő kódot a munkakönyvtárban `score.py`. Ez a fájl pontszámmodell a képeket, akkor a rendszer a szolgáltatás. Betölti a mentett TensorFlow-modell, minden POST-kérelmet továbbítja a bemeneti kép, a TensorFlow-munkamenethez, és az eredményül kapott pontszámok adja vissza. Más következtetési keretrendszerek különböző pontozófájlt van szükség.

```python
import tensorflow as tf
import numpy as np
import ujson
from azureml.core.model import Model
from azureml.contrib.services.aml_request import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse

def init():
    global session
    global input_name
    global output_name
    
    session = tf.Session()

    model_path = Model.get_model_path('resnet50')
    model = tf.saved_model.loader.load(session, ['serve'], model_path)
    if len(model.signature_def['serving_default'].inputs) > 1:
        raise ValueError("This score.py only supports one input")
    input_name = [tensor.name for tensor in model.signature_def['serving_default'].inputs.values()][0]
    output_name = [tensor.name for tensor in model.signature_def['serving_default'].outputs.values()]
    

@rawhttp
def run(request):
    if request.method == 'POST':
        reqBody = request.get_data(False)
        resp = score(reqBody)
        return AMLResponse(resp, 200)
    if request.method == 'GET':
        respBody = str.encode("GET is not supported")
        return AMLResponse(respBody, 405)
    return AMLResponse("bad request", 500)

def score(data):
    result = session.run(output_name, {input_name: [data]})
    return ujson.dumps(result[1])

if __name__ == "__main__":
    init()
    with open("lynx.jpg", 'rb') as f: #load file for testing locally
        content = f.read()
        print(score(content))

```

## <a name="define-the-conda-environment"></a>Adja meg a conda-környezet

Hozzon létre egy conda-környezet fájlt `myenv.yml` , a függőségek megadása a szolgáltatás. Fontos, hogy adja meg, hogy használ `tensorflow-gpu` nagyobb teljesítmény elérése érdekében.

```yaml
name: aml-accel-perf
channels:
  - defaults
dependencies:
  - tensorflow-gpu = 1.12
  - numpy
  - ujson
  - pip:
    - azureml-core
    - azureml-contrib-services
```

## <a name="define-the-gpu-inferenceconfig-class"></a>A GPU InferenceConfig osztály meghatározása

Hozzon létre egy `InferenceConfig` objektum, amely lehetővé teszi a GPU-kat, és biztosítja, hogy a CUDA telepítve van a Docker-rendszerképet a.

```python
from azureml.core.model import Model
from azureml.core.model import InferenceConfig

aks_service_name ='gpu-rn'
gpu_aks_config = AksWebservice.deploy_configuration(autoscale_enabled = False, 
                                                    num_replicas = 3, 
                                                    cpu_cores=2, 
                                                    memory_gb=4)
model = Model(ws,"resnet50")

inference_config = InferenceConfig(runtime= "python", 
                                   entry_script="score.py",
                                   conda_file="myenv.yml", 
                                   gpu_enabled=True)
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
                           deployment_config=aks_config,
                           deployment_target=aks_target,
                           name=aks_service_name)

aks_service.wait_for_deployment(show_output = True)
print(aks_service.state)
```

> [!NOTE]
> Az Azure Machine Learning szolgáltatás nem telepíthető, modell- `InferenceConfig` objektum, amely egy fürtöt, amely nem rendelkezik egy GPU engedélyezni GPU vár.

További információkért lásd: [Model class](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

## <a name="issue-a-sample-query-to-your-deployed-model"></a>Mintalekérdezés kiadni az üzembe helyezett modell

Egy teszt lekérdezést küldenek az üzembe helyezett modellt. Jpeg-képek küld a modell, amikor a lemezkép pontszámmodell azt.

```python
scoring_url = aks_service.scoring_uri
api_key = aks_service.get_key()(0)
IMAGEURL = "https://upload.wikimedia.org/wikipedia/commons/thumb/6/68/Lynx_lynx_poing.jpg/220px-Lynx_lynx_poing.jpg"

headers = {'Authorization':('Bearer '+ api_key)}
img_data = read_image_from(IMAGEURL).read()
r = requests.post(scoring_url, data = img_data, headers=headers)
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
