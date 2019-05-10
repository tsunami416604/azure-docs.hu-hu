---
title: Modell üzembe helyezése deep learning GPU-következtetési hogyan
titleSuffix: Azure Machine Learning service
description: Megtudhatja, hogyan szeretné üzembe helyezni a deep learning-modell, egy webszolgáltatás, amelyet egy GPU következtetési használ. Ebben a cikkben egy Tensorflow modell üzembe lett helyezve az Azure Kubernetes Service-fürthöz. A fürt a gazdagép a webszolgáltatás és a pontszám következtetési érkező GPU-kompatibilis virtuális gép használ.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 05/02/2019
ms.openlocfilehash: 5cc0fe0526937245d3ca913afc477f0259e2afd4
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/09/2019
ms.locfileid: "65515174"
---
# <a name="how-to-do-gpu-inferencing"></a>GPU-következtetési módjáról.

Ismerje meg, egy gépi tanulási modellt webszolgáltatásként üzembe helyezett GPU következtetési használata. Ebből a cikkből elsajátíthatja példát Tensorflow deep learning-modell üzembe helyezése az Azure Machine Learning szolgáltatás használatával. A modell üzembe lett helyezve, GPU-kompatibilis virtuális gépek üzemeltetéséhez a szolgáltatást használó Azure Kubernetes Service (AKS)-fürthöz. Amikor kérések érkeznek a szolgáltatáshoz, a modellt használ a GPU következtetési végrehajtásához.

Gpu-kat kínálnak processzorokat a magas párhuzamosítható számítási teljesítményt nyújtanak. Betanítási vagy következtetési deep learning-modellek (különösen a nagy váró kérelmek) is a GPU-k a kiváló példák.  

Ebben a példában bemutatják, hogyan helyezhet üzembe egy mentett TensorFlow modell Azure Machine Learning. 

## <a name="goals-and-prerequisites"></a>Célok és Előfeltételek

Kövesse az utasításokat:
* Hozzon létre egy gpu-t tartalmazó AKS-fürt
* Modell üzembe helyezése Tensorflow-grafikus processzort tartalmaz

Előfeltételek:
* Az Azure Machine Learning-szolgáltatások munkaterület
* Python
* Tensorflow SavedModel regisztrálva. Megtudhatja, hogyan regisztrálhat modellek lásd [modellek üzembe helyezése](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where#registermodel)

Ez a cikk alapján [Tensorflow-modellek üzembe helyezése az aks](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/production-deploy-to-aks-gpu/production-deploy-to-aks-gpu.ipynb), TensorFlow mentett használó modellek és a egy AKS-fürtöt helyezünk üzembe a. Azonban a kis változtatások a pontozófájl és a környezet fájl bármilyen machine learning-keretrendszerek gpu-kat támogató alkalmazható.  

## <a name="provision-aks-cluster-with-gpus"></a>AKS-fürt üzembe helyezése gpu-kkal
Az Azure rendelkezik több GPU lehetőség, amelyek mindegyike következtetési is használható. Lásd: [a lista N-sorozat](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series) a képességeit és a költségek teljes áttekintését. 

Az AKS használata az Azure Machine Learning szolgáltatás további információkért lásd: a [hogyan helyezheti üzembe, és ahol című cikket.](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where#create-a-new-cluster)

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
> Azure számolunk fel díjat, amíg az AKS-fürt kiépítése. Ügyeljen arra, hogy az AKS-fürt törlése, ha minden kész használja azt.


## <a name="write-entry-script"></a>Bejegyzés a parancsprogram írásához

Mentse a következő a munkakönyvtárban `score.py`. Ez a fájl pontszámot rendelni a lemezképek, a szolgáltatásnak küldött használható. Ez a fájl betölti a modell mentett tensorflow-hoz, és ezután az egyes bejegyzések kérés átadja a bemeneti kép, a TensorFlow-munkamenethez, és az eredményül kapott értékeket ad vissza.
Más következtetési keretrendszerek különböző pontozófájlt lesz szükség.

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

## <a name="define-conda-environment"></a>Conda környezet létrehozása
Hozzon létre egy conda-környezet fájlt `myenv.yml` , a függőségek megadása a szolgáltatás. Fontos, hogy adja meg, hogy használja `tensorflow-gpu` nagyobb teljesítmény elérése érdekében.
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

## <a name="define-gpu-inferenceconfig"></a>GPU-InferenceConfig definiálása

Hozzon létre egy [ `InferenceConfig` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) amely megadja, hogy engedélyezi a grafikus Processzor. Ez biztosítja, hogy CUDA telepítve van-e a rendszerképpel.

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

További információkért lásd: [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) és [AksServiceDeploymentConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py).
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
> Az Azure Machine Learning szolgáltatás egy modell nem telepít egy `InferenceConfig` GPU, amely vár egy fürthöz GPU nélkül.

További információkért lásd: [modell](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

## <a name="issue-sample-query-to-deployed-model"></a>Probléma-mintalekérdezés az üzembe helyezett modell

Mintalekérdezés kiadni az üzembe helyezett modellt. Ez a modell pontozása fog bármely JPEG típusú kép azt egy post kérést küld. 

```python
scoring_url = aks_service.scoring_uri
api_key = aks_service.get_key()(0)
IMAGEURL = "https://upload.wikimedia.org/wikipedia/commons/thumb/6/68/Lynx_lynx_poing.jpg/220px-Lynx_lynx_poing.jpg"

headers = {'Authorization':('Bearer '+ api_key)}
img_data = read_image_from(IMAGEURL).read()
r = requests.post(scoring_url, data = img_data, headers=headers)
```

> [!IMPORTANT]
> Optimalizálható a teljesítmény és a késés, az ügyfél és a végpontot Azure ugyanabban a régióban kell lennie.  Jelenleg az API-k jönnek létre az East US Azure-régióban.

## <a name="cleaning-up-the-resources"></a>Az erőforrások törlése

Miután végzett a bemutató, törölje az erőforrásokat.

> [!IMPORTANT]
> Az Azure a számlát, mennyi ideig az AKS-fürt üzembe lesz helyezve alapján. Ellenőrizze, hogy törölni, azt elvégzése után.

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>További lépések

* [FPGA a modell üzembe helyezése](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-fpga-web-service)
* [Az ONNX-modell üzembe helyezése](https://docs.microsoft.com/azure/machine-learning/service/how-to-build-deploy-onnx#deploy)
* [Tensorflow DNN-modellek betanításához](https://docs.microsoft.com/azure/machine-learning/service/how-to-train-tensorflow)
