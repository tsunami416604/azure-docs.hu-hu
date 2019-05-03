---
title: FPGA-kban a modellek üzembe helyezése
titleSuffix: Azure Machine Learning service
description: Megtudhatja, hogyan futtat egy Azure Machine Learning szolgáltatásban a ultramagas közel valós idejű következtetési FPGA modell egy webszolgáltatás üzembe helyezéséhez.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: tedway
author: tedway
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: cfe21d2119b92665c5950d792dec6500257c6316
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65024182"
---
# <a name="deploy-a-model-as-a-web-service-on-an-fpga-with-azure-machine-learning-service"></a>Modell üzembe helyezése az Azure Machine Learning szolgáltatás egy FPGA a webszolgáltatásként

Telepíthet egy modellt webszolgáltatásként, amely a [programmable gate arrays (FPGA) mezőben](concept-accelerate-with-fpgas.md) az Azure Machine Learning hardver gyorsított modellek. FPGA-kban használatával biztosít ultramagas közel valós idejű következtetési egyetlen kötegméret mellett is.

Ezek a modellek jelenleg érhetők el:
  - ResNet 50
  - ResNet 152
  - DenseNet-121
  - VGG-16
  - SSD-VGG

A FPGA-kban ezen Azure-régiókban érhetők el:
  - USA keleti régiója
  - USA nyugati régiója, 2.
  - Nyugat-Európa
  - Délkelet-Ázsia

> [!IMPORTANT]
> Optimalizálható a teljesítmény és a késés, adatok küldését az FPGA modell az ügyfél a fenti (helyezte üzembe a modellt egy) régiók egyikében kell lennie.

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy ingyenes fiókot megkezdése előtt. Próbálja ki a [Azure Machine Learning szolgáltatás ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

- Az Azure Machine Learning szolgáltatás munkaterület és az Azure Machine Learning SDK telepítve van a Pythonhoz készült. Ezekről az előfeltételekről használatával beszerzéséről a [a fejlesztési környezet konfigurálása](how-to-configure-environment.md) dokumentumot.
 
  - Telepítse a Python SDK hardveresen gyorsított modellek:

    ```shell
    pip install --upgrade azureml-accel-models
    ```

## <a name="sample-notebooks"></a>Mintanotebookok

Az Ön kényelme érdekében [notebookok minta](https://aka.ms/aml-notebooks) érhetők el a példa az alábbi, illetve egyéb példák mellett.  Keresse meg a útmutatóval-to-használat – azureml és a telepítési mappák gyorsított modellek.

## <a name="create-and-containerize-your-model"></a>Hozzon létre, és a modell tárolóba

Ez a dokumentum azt ismertetik, hogyan hozhat létre a bemeneti kép előfeldolgozása, adja meg a ResNet-50 használatával egy FPGA featurizer TensorFlow grafikon, és futtassa az funkciók egy osztályozó épít adatkészlet tanított keresztül.

Kövesse az utasításokat:

* A TensorFlow-modell
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
model_def_path = os.path.join(save_path, model_name)
print("Saving model in {}".format(model_def_path))

with tf.Session() as sess:
    model_graph.restore_weights(sess)
    tf.saved_model.simple_save(sess, model_def_path,
                                   inputs={'images': in_images},
                                   outputs={'output_alias': classifier_output})
```

### <a name="register-model"></a>Modell regisztrálása

[Regisztráljon](./concept-model-management-and-deployment.md) a létrehozott modellt.  Címkék és a modellel kapcsolatos más metaadatok hozzáadása segíti is nyomon követheti a betanított modellek.

```python
from azureml.core.model import Model

registered_model = Model.register(workspace = ws
                                  model_path = model_def_path,
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

A TensorFlow-diagram van szüksége, nyissa meg a Neurális hálózat Exchange-formátumra átalakítandó ([ONNX](https://onnx.ai/)).  Meg kell adnia a bemeneti és kimeneti tensors nevét, és ezeket a neveket az ügyfél által használandó, ha a webszolgáltatás felhasznált.

```python
input_tensor = in_images.name
output_tensors = classifier_output.name

print(input_tensor)
print(output_tensors)


from azureml.accel.accel_onnx_converter import AccelOnnxConverter

convert_request = AccelOnnxConverter.convert_tf_model(ws, registered_model, input_tensor, output_tensors)
convert_request.wait_for_completion(show_output=True)

# If the above call succeeded, get the converted model
converted_model = convert_request.result
print(converted_model.name, converted_model.url, converted_model.version, converted_model.id,converted_model.created_time)
```

### <a name="create-docker-image"></a>Docker-rendszerkép létrehozása

A konvertált modell és annak összes függőségét kerülnek egy Docker-rendszerképet.  A Docker-rendszerkép majd telepíthető és a felhőben és a egy támogatott peremhálózati eszköz például példányosítása [az Azure Data Box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview).  A regisztrált Docker-rendszerképet a címkék és leírások is hozzáadhat.

```python
from azureml.core.image import Image
from azureml.accel.accel_container_image import AccelContainerImage

image_config = AccelContainerImage.image_configuration()
image_name = "{}-image".format(model_name)

image = Image.create(name = image_name,
                     models = [converted_model],
                     image_config = image_config, 
                     workspace = ws)


image.wait_for_creation(show_output = True)
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
# Use the default configuration (can also provide parameters to customize)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'my-aks-9' 
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws, 
                                  name = aks_name, 
                                  provisioning_configuration = prov_config)

%%time
aks_target.wait_for_completion(show_output = True)
print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)

#Set the web service configuration (using default here)
aks_config = AksWebservice.deploy_configuration()

%%time
aks_service_name ='aks-service-1'

aks_service = Webservice.deploy_from_image(workspace = ws, 
                                           name = aks_service_name,
                                           image = image,
                                           deployment_config = aks_config,
                                           deployment_target = aks_target)
aks_service.wait_for_deployment(show_output = True)
print(aks_service.state)
print(aks_service.scoring_uri)
```

#### <a name="test-the-cloud-service"></a>A felhőalapú szolgáltatás tesztelése
A Docker-rendszerkép gRPC és a "előrejelzése" API-t TensorFlow-szolgáltató támogatja.  A minta-ügyfél segítségével meghívhatja a Docker-rendszerképet kaphatnak előjelzéseket a modellből.  Mintakód ügyfél érhető el:
- [Python](https://github.com/Azure/aml-real-time-ai/blob/master/pythonlib/amlrealtimeai/client.py)
- [C#](https://github.com/Azure/aml-real-time-ai/blob/master/sample-clients/csharp)

Ha szeretné használni a TensorFlow-szolgáltató, akkor [egy minta-ügyfél letöltése](https://www.tensorflow.org/serving/setup).

```python
import requests
classes_entries = requests.get("https://raw.githubusercontent.com/Lasagne/Recipes/master/examples/resnet50/imagenet_classes.txt").text.splitlines()

# Score image using input and output tensor names
results = client.score_file(path="./snowleopardgaze.jpg", 
                             input_name=input_tensor, 
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
image.delete()
registered_model.delete()
converted_model.delete()
```

## <a name="deploy-to-a-local-edge-server"></a>Üzembe helyezés helyi peremhálózati kiszolgálón

Az összes [Azure Data Box peremhálózati eszközök](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview
) egy FPGA tartalmazza a modell futtatásához.  Csak egy modell futhat a FPGA egyszerre.  Futtatni egy másik modellhez, csak helyezzen üzembe egy új tárolót. Utasítások és a mintakód megtalálható [az Azure-minta](https://github.com/Azure-Samples/aml-hardware-accelerated-models).

## <a name="secure-fpga-web-services"></a>Biztonságos FPGA-webszolgáltatások

FPGA-webszolgáltatások védelmével kapcsolatos további információkért lásd: a [biztonságos webszolgáltatások](how-to-secure-web-service.md) dokumentumot.