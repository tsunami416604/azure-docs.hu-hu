---
title: FPGA-kban a modellek üzembe helyezése
titleSuffix: Azure Machine Learning service
description: Megtudhatja, hogyan futtat egy Azure Machine Learning szolgáltatásban a ultramagas közel valós idejű következtetésekhez FPGA modell egy webszolgáltatás üzembe helyezéséhez.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: tedway
author: tedway
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: 511333342371e18f75b48b60486705571353ee13
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/06/2019
ms.locfileid: "66735055"
---
# <a name="deploy-a-model-as-a-web-service-on-an-fpga-with-azure-machine-learning-service"></a>Modell üzembe helyezése az Azure Machine Learning szolgáltatás egy FPGA a webszolgáltatásként

Telepíthet egy modellt webszolgáltatásként, amely a [programmable gate arrays (FPGA) mezőben](concept-accelerate-with-fpgas.md) az Azure Machine Learning hardver gyorsított modellek. FPGA-kban használatával biztosít ultramagas közel valós idejű következtetésekhez egyetlen kötegméret mellett is. Következtetésekhez vagy a modell pontozása, nem a fázis, az üzembe helyezett modell előrejelzési leggyakrabban a termelési adatok szolgál.

Ezek a modellek jelenleg érhetők el:
  - ResNet 50
  - ResNet 152
  - DenseNet-121
  - VGG-16
  - SSD-VGG

FPGA-kban ezen Azure-régiókban érhetők el:
  - USA keleti régiója
  - Délkelet-Ázsia
  - Nyugat-Európa
  - USA nyugati régiója, 2.

> [!IMPORTANT]
> Optimalizálható a teljesítmény és a késés, adatok küldését az FPGA modell az ügyfél a fenti (helyezte üzembe a modellt egy) régiók egyikében kell lennie.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés.  Ha nem rendelkezik ilyennel, hozzon létre egy ingyenes fiókot, megkezdése előtt. Próbálja ki a [Azure Machine Learning szolgáltatás ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

- FPGA-kvótát.  Az Azure CLI használatával ellenőrizze, hogy rendelkezik-e a kvótát.
    ```shell
    az vm list-usage --location "eastus" -o table
    ```

    Az egyéb helyek ``southeastasia``, ``westeurope``, és ``westus2``.

    A "Name" oszlopban keressen a "Vcpu-k szabványos PB termékcsalád", és ellenőrizze, hogy legalább 6 vcpu-k mellett "CurrentValue."

    Ha nem rendelkezik a kvótát, majd küldje el egy űrlapot [Itt](https://aka.ms/accelerateAI).

- Az Azure Machine Learning szolgáltatás munkaterület és az Azure Machine Learning SDK telepítve van a Pythonhoz készült. Ezekről az előfeltételekről használatával beszerzéséről a [a fejlesztési környezet konfigurálása](how-to-configure-environment.md) dokumentumot.
 
- A Python SDK for hardveresen gyorsított modellek:

    ```shell
    pip install --upgrade azureml-accel-models
    ```

## <a name="sample-notebooks"></a>Mintanotebookok

Az Ön kényelme érdekében [notebookok minta](https://aka.ms/aml-accel-models-notebooks) érhetők el az alábbi példa és más példákat.

## <a name="create-and-containerize-your-model"></a>Hozzon létre, és a modell tárolóba

Ez a dokumentum azt ismertetik, hogyan hozhat létre a bemeneti kép előfeldolgozása, adja meg a ResNet-50 használatával egy FPGA featurizer TensorFlow grafikon, és futtassa az funkciók egy osztályozó épít adatkészlet tanított keresztül.

Kövesse az utasításokat:

* A TensorFlow-modell
* A modell konvertálása
* A modell üzembe helyezése
* A telepített modell feldolgozása
* Telepített szolgáltatások törlése

### <a name="load-azure-ml-workspace"></a>Az Azure ML-munkaterület betöltése

Töltse be az Azure ML-munkaterület.

```python
import os
import tensorflow as tf
 
from azureml.core import Workspace

ws = Workspace.from_config()
print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep = '\n')
```

### <a name="preprocess-image"></a>Kép előfeldolgozása

A webszolgáltatás bemeneti JPEG formátumú kép.  Az első lépés, hogy a JPEG-képek dekódolni, és előfeldolgozása azt.  A JPEG-képek, karakterláncok és az eredmény, amely a bemeneti a ResNet-50 modellbe lesz tensors kell kezelni.

```python
# Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
import azureml.accel.models.utils as utils
tf.reset_default_graph()

in_images = tf.placeholder(tf.string)
image_tensors = utils.preprocess_array(in_images)
print(image_tensors.shape)
```

### <a name="load-featurizer"></a>Featurizer betöltése

A modell inicializálása, és töltse le a featurizer használandó ResNet50 kvantált verziójának TensorFlow ellenőrzőpont.  Más neurálishálózat importálásával előfordulhat, hogy cserélje le az alábbi kódrészletben szereplő "QuantizedResnet50":

- QuantizedResnet152
- QuantizedVgg16
- Densenet121

```python
from azureml.accel.models import QuantizedResnet50
save_path = os.path.expanduser('~/models')
model_graph = QuantizedResnet50(save_path, is_frozen = True)
feature_tensor = model_graph.import_graph_def(image_tensors)
print(model_graph.version)
print(feature_tensor.name)
print(feature_tensor.shape)
```

### <a name="add-classifier"></a>Besorolás hozzáadása

Az osztályozó által igénybe vett rendelkezik betanítva épít adathalmazon.  Példák az átviteli tanulási és képzési a testre szabott súlyok érhetők el a készletét [notebookok minta](https://aka.ms/aml-notebooks).

```python
classifier_output = model_graph.get_default_classifier(feature_tensor)
print(classifier_output)
```

### <a name="save-the-model"></a>A modell mentése

Most, hogy az elő-feldolgozói ResNet-50 featurizer és az osztályozó által igénybe vett lett betöltve, mentse a graph és a kapcsolódó változók modellként.

```python
model_name = "resnet50"
model_save_path = os.path.join(save_path, model_name)
print("Saving model in {}".format(model_save_path))

with tf.Session() as sess:
    model_graph.restore_weights(sess)
    tf.saved_model.simple_save(sess, model_save_path,
                                   inputs={'images': in_images},
                                   outputs={'output_alias': classifier_output})
```

### <a name="save-input-and-output-tensors"></a>Bemeneti és kimeneti tensors mentése
A bemeneti és kimeneti tensors az előfeldolgozási és osztályozó lépések során létrehozott modell átalakítás tanuláshoz és következtetésekhez van szükség.

```python
input_tensors = in_images.name
output_tensors = classifier_output.name

print(input_tensors)
print(output_tensors)
```

> [!IMPORTANT]
> Mentse a bemeneti és kimeneti tensors, mert ezekre szükség lesz a modell átalakítás tanuláshoz és következtetésekhez kéréseket.

Az elérhető modellek és a megfelelő alapértelmezett osztályozó kimeneti tensors szolgálnak, azaz, hogy milyen használna következtetésekhez az alapértelmezett osztályozó használatakor.

+ Resnet50, QuantizedResnet50 ``
output_tensors = "classifier_1/resnet_v1_50/predictions/Softmax:0"
``
+ Resnet152, QuantizedResnet152 ``
output_tensors = "classifier/resnet_v1_152/predictions/Softmax:0"
``
+ Densenet121, QuantizedDensenet121 ``
output_tensors = "classifier/densenet121/predictions/Softmax:0"
``
+ Vgg16, QuantizedVgg16 ``
output_tensors = "classifier/vgg_16/fc8/squeezed:0"
``
+ SsdVgg, QuantizedSsdVgg ``
output_tensors = ['ssd_300_vgg/block4_box/Reshape_1:0', 'ssd_300_vgg/block7_box/Reshape_1:0', 'ssd_300_vgg/block8_box/Reshape_1:0', 'ssd_300_vgg/block9_box/Reshape_1:0', 'ssd_300_vgg/block10_box/Reshape_1:0', 'ssd_300_vgg/block11_box/Reshape_1:0', 'ssd_300_vgg/block4_box/Reshape:0', 'ssd_300_vgg/block7_box/Reshape:0', 'ssd_300_vgg/block8_box/Reshape:0', 'ssd_300_vgg/block9_box/Reshape:0', 'ssd_300_vgg/block10_box/Reshape:0', 'ssd_300_vgg/block11_box/Reshape:0']
``

### <a name="register-model"></a>Modell regisztrálása

[Regisztráljon](./concept-model-management-and-deployment.md) a létrehozott modellt.  Címkék és a modellel kapcsolatos más metaadatok hozzáadása segíti is nyomon követheti a betanított modellek.

```python
from azureml.core.model import Model

registered_model = Model.register(workspace = ws,
                                  model_path = model_save_path,
                                  model_name = model_name)

print("Successfully registered: ", registered_model.name, registered_model.description, registered_model.version, sep = '\t')
```

Ha már regisztrálta egy modellt, és betölti azt, akkor előfordulhat, hogy beolvasni.

```python
from azureml.core.model import Model
model_name = "resnet50"
# By default, the latest version is retrieved. You can specify the version, i.e. version=1
registered_model = Model(ws, name="resnet50")
print(registered_model.name, registered_model.description, registered_model.version, sep = '\t')
```

### <a name="convert-model"></a>Modell konvertálása

Az Open Neurális hálózat Exchange formátumra konvertálni a TensorFlow graph ([ONNX](https://onnx.ai/)).  Meg kell adnia a bemeneti és kimeneti tensors nevét, és ezeket a neveket az ügyfél által használandó, ha a webszolgáltatás felhasznált.

```python
from azureml.accel import AccelOnnxConverter

convert_request = AccelOnnxConverter.convert_tf_model(ws, registered_model, input_tensors, output_tensors)

# If it fails, you can run wait_for_completion again with show_output=True.
convert_request.wait_for_completion(show_output = False)

# If the above call succeeded, get the converted model
converted_model = convert_request.result
print("\nSuccessfully converted: ", converted_model.name, converted_model.url, converted_model.version, 
      converted_model.id, converted_model.created_time, '\n')
```

### <a name="create-docker-image"></a>Docker-rendszerkép létrehozása

A konvertált modell és annak összes függőségét kerülnek egy Docker-rendszerképet.  A Docker-rendszerkép majd telepíthető és példányt.  Támogatott üzembe helyezési célok közé tartozik az AKS a felhőben és a egy edge-eszköz például [az Azure Data Box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview).  A regisztrált Docker-rendszerképet a címkék és leírások is hozzáadhat.

```python
from azureml.core.image import Image
from azureml.accel import AccelContainerImage

image_config = AccelContainerImage.image_configuration()
# Image name must be lowercase
image_name = "{}-image".format(model_name)

image = Image.create(name = image_name,
                     models = [converted_model],
                     image_config = image_config, 
                     workspace = ws)
image.wait_for_creation(show_output = False)
```

Címke szerinti rendszerképek listáját, és bármely hibakereséshez a részletes naplók lekérése.

```python
for i in Image.list(workspace = ws):
    print('{}(v.{} [{}]) stored at {} with build log {}'.format(i.name, i.version, i.creation_state, i.image_location, i.image_build_log_uri))
```

## <a name="model-deployment"></a>Modell-üzembehelyezés

### <a name="deploy-to-the-cloud"></a>A felhőben üzembe helyezni

Az Azure Kubernetes Service (AKS) használatával a modellt webszolgáltatásként, amely nagy méretű éles üzembe helyezéséhez. Létrehozhat egy újat az Azure Machine Learning SDK-t, a CLI vagy az Azure portal használatával.

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Specify the Standard_PB6s Azure VM
prov_config = AksCompute.provisioning_configuration(vm_size = "Standard_PB6s",
                                                    agent_count = 1)

aks_name = 'my-aks-cluster'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws, 
                                  name = aks_name, 
                                  provisioning_configuration = prov_config)
```

Az AKS üzembe helyezés hozzávetőlegesen 15 percet is igénybe vehet.  Ellenőrizze, hogy ha az üzembe helyezés sikeres volt.

```python
aks_target.wait_for_completion(show_output = True)
print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```

A tároló üzembe az AKS-fürtöt.
```python
from azureml.core.webservice import Webservice, AksWebservice

# For this deployment, set the web service configuration without enabling auto-scaling or authentication for testing
aks_config = AksWebservice.deploy_configuration(autoscale_enabled=False,
                                                num_replicas=1,
                                                auth_enabled = False)

aks_service_name ='my-aks-service'

aks_service = Webservice.deploy_from_image(workspace = ws,
                                           name = aks_service_name,
                                           image = image,
                                           deployment_config = aks_config,
                                           deployment_target = aks_target)
aks_service.wait_for_deployment(show_output = True)
```

#### <a name="test-the-cloud-service"></a>A felhőalapú szolgáltatás tesztelése
A Docker-rendszerkép gRPC és a "előrejelzése" API-t TensorFlow-szolgáltató támogatja.  A minta-ügyfél segítségével meghívhatja a Docker-rendszerképet kaphatnak előjelzéseket a modellből.  Mintakód ügyfél érhető el:
- [Python](https://github.com/Azure/aml-real-time-ai/blob/master/pythonlib/amlrealtimeai/client.py)
- [C#](https://github.com/Azure/aml-real-time-ai/blob/master/sample-clients/csharp)

Ha szeretné használni a TensorFlow-szolgáltató, akkor [egy minta-ügyfél letöltése](https://www.tensorflow.org/serving/setup).

```python
# Using the grpc client in Azure ML Accelerated Models SDK package
from azureml.accel import PredictionClient

address = aks_service.scoring_uri
ssl_enabled = address.startswith("https")
address = address[address.find('/')+2:].strip('/')
port = 443 if ssl_enabled else 80

# Initialize AzureML Accelerated Models client
client = PredictionClient(address=address,
                          port=port,
                          use_ssl=ssl_enabled,
                          service_name=aks_service.name)
```

Mivel az osztályozó által igénybe vett tanított volt a [épít](http://www.image-net.org/) adatok beállítása, az osztályok leképezése az emberek számára olvasható címkéket.

```python
import requests
classes_entries = requests.get("https://raw.githubusercontent.com/Lasagne/Recipes/master/examples/resnet50/imagenet_classes.txt").text.splitlines()

# Score image with input and output tensor names
results = client.score_file(path="./snowleopardgaze.jpg", 
                             input_name=input_tensors, 
                             outputs=output_tensors)

# map results [class_id] => [confidence]
results = enumerate(results)
# sort results by confidence
sorted_results = sorted(results, key=lambda x: x[1], reverse=True)
# print top 5 results
for top in sorted_results[:5]:
    print(classes_entries[top[0]], 'confidence:', top[1])
```

### <a name="clean-up-the-service"></a>A szolgáltatás karbantartás
Törli a webszolgáltatás, a lemezkép és a modell (kell elvégezni az alábbi sorrendben, mivel a függőségek).

```python
aks_service.delete()
aks_target.delete()
image.delete()
registered_model.delete()
converted_model.delete()
```

## <a name="deploy-to-a-local-edge-server"></a>Üzembe helyezés helyi peremhálózati kiszolgálón

Az összes [Azure Data Box peremhálózati eszközök](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview
) egy FPGA tartalmazza a modell futtatásához.  Csak egy modell futhat a FPGA egyszerre.  Futtatni egy másik modellhez, csak helyezzen üzembe egy új tárolót. Utasítások és a mintakód megtalálható [az Azure-minta](https://github.com/Azure-Samples/aml-hardware-accelerated-models).

## <a name="secure-fpga-web-services"></a>Biztonságos FPGA-webszolgáltatások

FPGA-webszolgáltatások védelmével kapcsolatos további információkért lásd: a [biztonságos webszolgáltatások](how-to-secure-web-service.md) dokumentumot.

## <a name="pbs-family-vms"></a>PB termékcsalád virtuális gépek

Az Azure-beli virtuális PB termékcsalád Intel Arria 10 FPGA tartalmazza.  Állapotúként jelenik meg "Vcpu-k szabványos PB termékcsalád" az Azure-kvóta foglalási ellenőrzésekor.  A PB6 virtuális gépen, hat virtuális processzorral és a egy FPGA, és azt automatikusan kiépítheti az Azure ml helyezi üzembe a modellt, egy FPGA részeként.  Csak az Azure ML segítségével használja, és tetszőleges bitstreams nem lesz futtatható.  Ha például nem tudja flash az FPGA bitstreams az ehhez a titkosítás, kódolás, stb. 
