---
title: Mik azok a FPGA – üzembe helyezési útmutató
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan helyezhet üzembe egy webszolgáltatást egy olyan FPGA, amelyen fut egy Azure Machine Learning szolgáltatás az alacsony késleltetésű következtetésekhez.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: tedway
author: tedway
ms.date: 07/25/2019
ms.custom: seodec18
ms.openlocfilehash: c3d1d211f85c50a482e1dd9c482cb75477942de7
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70278200"
---
# <a name="what-are-field-programmable-gate-arrays-fpga-and-how-to-deploy"></a>Mik a Field-programozható Gate-tömbök (FPGA) és az üzembe helyezés módja

Ez a cikk bevezetést nyújt a Field-programozható Gate-tömbök (FPGA) számára, és bemutatja, hogyan helyezheti üzembe a modelleket Azure Machine Learning szolgáltatás használatával egy Azure-FPGA. 

A FPGA programozható logikai blokkok tömbjét és az újrakonfigurálható összekapcsolások hierarchiáját tartalmazza. Az összekapcsolások lehetővé teszik, hogy ezeket a blokkokat különböző módokon konfigurálja a gyártás után. A többi zsetonnal összehasonlítva a FPGA a programozhatóság és a teljesítmény kombinációját biztosítja.

## <a name="fpgas-vs-cpu-gpu-and-asic"></a>FPGA-kban vs. A Processzor, grafikus Processzor és ASIC

A következő diagram és táblázat bemutatja, hogyan hasonlítható össze a FPGA más processzorokkal.

![A Azure Machine Learning Service FPGA összehasonlításának ábrája](./media/concept-accelerate-with-fpgas/azure-machine-learning-fpga-comparison.png)

|Processzor||Leírás|
|---|:-------:|------|
|Alkalmazás-specifikus integrált Kapcsolatcsoportok|Az ASICs|Egyéni kapcsolatok, például a Google TensorFlow processzor egységek (TPU), adja meg a lehető legnagyobb hatékonysággal. Az igények változása miatt nem konfigurálhatók újra.|
|A mező-programmable gate arrays|FPGA-kban|A FPGA, például az Azure-on elérhetők, az ASICs-hoz hasonló teljesítményt biztosítanak. Az új logika megvalósításához az idő múlásával rugalmasan és újrakonfigurálható is.|
|Grafikai feldolgozóegységek|A GPU-k|Az AI-számítások népszerű választéka. A GPU-k párhuzamos feldolgozási képességeket biztosítanak, így gyorsabban, mint a CPU-k.|
|Központi egység|CPU-k|Általános célú processzorok, amelyek teljesítménye nem ideális a grafikához és a videók feldolgozásához.|

Az Azure-beli FPGA az Intel FPGA-eszközein alapulnak, amelyeket az adatszakértők és a fejlesztők a valós idejű AI-számítások felgyorsítására használnak. Ez a FPGA-kompatibilis architektúra teljesítményt, rugalmasságot és méretezést kínál, és elérhető az Azure-ban.

A FPGA lehetővé teszi a valós idejű következtetések (vagy modellek pontozási) kérések alacsony késésének elérését. Aszinkron kérelmek (kötegelt feldolgozás) nem szükségesek. A kötegelt feldolgozás késést okozhat, mert több adatfeldolgozást kell feldolgozni. A neurális feldolgozó egységek implementációja nem igényli a kötegelt feldolgozást; Ezért a késés a processzor-és a GPU-processzorokhoz képest sokszor alacsonyabb lehet.

### <a name="reconfigurable-power"></a>Újrakonfigurálható power
A FPGA újrakonfigurálhatja különböző típusú gépi tanulási modellekhez. Ezt a rugalmasságot megkönnyíti az alkalmazások, a legtöbb optimális numerikus pontosság és a használt memória modell alapján gyorsabb. Mivel a FPGA újrakonfigurálható, a rendszer naprakészen tarthatja a gyorsan változó AI-algoritmusok követelményeit.

## <a name="whats-supported-on-azure"></a>Az Azure-ban támogatott műveletek
Microsoft Azure a világ legnagyobb Felhőbeli befektetése a FPGA-ben. Ez az architektúra FPGA-kompatibilis hardveres, betanított Neurális hálózatokkal futtassa gyorsan és kisebb késést biztosítanak. Az Azure-ban integrálással előre képzett mély neurális hálózatokat (DNN) a FPGA között a szolgáltatás skálázása érdekében. A dnn-eket is lehet előre betanított, mint egy részletes featurizer tanulási, vagy a frissített súlyok finomította átvitele.

Az Azure-FPGA a következőket támogatja:

+ Kép besorolási és felismerés forgatókönyvek
+ TensorFlow-telepítés
+ Az Intel FPGA hardver 

Ezek a DNN modellek jelenleg elérhetők:
  - ResNet 50
  - ResNet 152
  - DenseNet-121
  - VGG-16
  - SSD-VGG

A FPGA az alábbi Azure-régiókban érhetők el:
  - East US
  - Délkelet-Ázsia
  - Nyugat-Európa
  - USA nyugati régiója, 2.

> [!IMPORTANT]
> A késés és az átviteli sebesség optimalizálása érdekében a FPGA-modellnek adatokat küldő ügyfélnek a fenti régiók egyikében kell lennie (a modellt a következőre telepítette:).

Az **Azure-beli virtuális gépek PBS-családja** Intel Arria 10 FPGA tartalmaz. Az Azure-kvóta kiosztásának ellenőrzésekor "standard PBS Family vCPU"-ként jelenik meg. A PB6 virtuális gépnek hat vCPU és egy FPGA van, és az Azure ML automatikusan kiépíti a modellnek egy FPGA való üzembe helyezésének részeként. Csak az Azure ML-vel használható, és nem futtathat tetszőleges bitstreams. Például nem fogja tudni megkezdeni a FPGA a bitstreams, hogy titkosítást, kódolást stb.

### <a name="scenarios-and-applications"></a>Forgatókönyvek és alkalmazási

Az Azure FPGA integrálva van Azure Machine Learningokkal. A Microsoft FPGA használ a DNN kiértékeléséhez, a Bing keresési rangsorolásához és a szoftveres hálózatkezelési (SDN) gyorsításhoz a késés csökkentése érdekében, valamint a processzorok más feladatokhoz való felszabadítását.

A következő forgatókönyvek a FPGA-t használják:
+ [Automatikus optikai hálózatvizsgáló rendszer](https://blogs.microsoft.com/ai/build-2018-project-brainwave/)

+ [Föld cover-leképezés](https://blogs.technet.microsoft.com/machinelearning/2018/05/29/how-to-use-fpgas-for-deep-learning-inference-to-perform-land-cover-mapping-on-terabytes-of-aerial-images/)



## <a name="example-deploy-models-on-fpgas"></a>Példa: Modellek üzembe helyezése a FPGA 

A modelleket webszolgáltatásként is üzembe helyezheti a FPGA Azure Machine Learning Hardware Accelerated Models használatával. A FPGA használata rendkívül kis késleltetésű következtetést biztosít, akár egyetlen batch-méret is. A következtetés vagy a modell pontozása az a fázis, ahol az üzembe helyezett modellt az előrejelzéshez használják, leggyakrabban a termelési adatforgalomban.


### <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés.  Ha még nem rendelkezik ilyennel, hozzon létre egy ingyenes fiókot a Kezdés előtt. Próbálja ki a [Azure Machine learning Service ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

- FPGA kvóta. Az Azure CLI használatával győződjön meg arról, hogy rendelkezik-e kvótával:

    ```shell
    az vm list-usage --location "eastus" -o table --query "[?localName=='Standard PBS Family vCPUs']"
    ```

    > [!TIP]
    > A többi lehetséges helye ``southeastasia`` ``westeurope``:, és ``westus2``.

    A parancs a következőhöz hasonló szöveget ad vissza:

    ```text
    CurrentValue    Limit    LocalName
    --------------  -------  -------------------------
    0               6        Standard PBS Family vCPUs
    ```

    Győződjön meg arról, hogy legalább 6 vCPU van a __CurrentValue__alatt.

    Ha nem rendelkezik kvótával, küldje el a kérelmet a következő [https://aka.ms/accelerateAI](https://aka.ms/accelerateAI)címen:.

- Az Azure Machine Learning szolgáltatás munkaterület és az Azure Machine Learning SDK telepítve van a Pythonhoz készült. További információ: [Munkaterület létrehozása](how-to-manage-workspace.md).
 
- A Python SDK a hardveres gyorsítású modellekhez:

    ```shell
    pip install --upgrade azureml-accel-models
    ```


## <a name="1-create-and-containerize-models"></a>1. Modellek létrehozása és tárolóba helyezése

Ebből a dokumentumból megtudhatja, hogyan hozhat létre egy TensorFlow gráfot a bemeneti rendszerkép előfeldolgozásához, hogy a Képtulajdonság a ResNet 50-et használja egy FPGA, majd a szolgáltatásokat a ImageNet-adatkészletben szereplő osztályozó használatával futtassa.

Kövesse az utasításokat:

* A TensorFlow modell megadása
* A modell átalakítása
* A modell üzembe helyezése
* A telepített modell feldolgozása
* Telepített szolgáltatások törlése

A [Pythonhoz készült Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) segítségével hozzon létre egy szolgáltatás-definíciót. A szolgáltatás definíciója egy olyan fájl, amely a diagramok (bemenet, Képtulajdonság és osztályozó) folyamatát írja le a TensorFlow alapján. A telepítési parancs automatikusan tömöríti a definíciót és a diagramokat egy ZIP-fájlba, és feltölti a ZIP-fájlt az Azure Blob Storage-ba. A DNN már telepítve van, hogy a FPGA fusson.

### <a name="load-azure-ml-workspace"></a>Azure ML-munkaterület betöltése

Töltse be az Azure ML-munkaterületét.

```python
import os
import tensorflow as tf

from azureml.core import Workspace

ws = Workspace.from_config()
print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep='\n')
```

### <a name="preprocess-image"></a>Kép előfeldolgozása

A webszolgáltatás bemenete egy JPEG-rendszerkép.  Az első lépés a JPEG-rendszerkép dekódolása és előfeldolgozása.  A JPEG-képek karakterláncként vannak kezelve, és az eredmény a ResNet 50 modell bemenetét eredményező tízes.

```python
# Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
import azureml.accel.models.utils as utils
tf.reset_default_graph()

in_images = tf.placeholder(tf.string)
image_tensors = utils.preprocess_array(in_images)
print(image_tensors.shape)
```

### <a name="load-featurizer"></a>Képtulajdonság betöltése

A modell inicializálása, és töltse le a featurizer használandó ResNet50 kvantált verziójának TensorFlow ellenőrzőpont.  Az alábbi kódrészletben a "QuantizedResnet50" kifejezés helyett más mély neurális hálózatokat is importálhat:

- QuantizedResnet152
- QuantizedVgg16
- Densenet121

```python
from azureml.accel.models import QuantizedResnet50
save_path = os.path.expanduser('~/models')
model_graph = QuantizedResnet50(save_path, is_frozen=True)
feature_tensor = model_graph.import_graph_def(image_tensors)
print(model_graph.version)
print(feature_tensor.name)
print(feature_tensor.shape)
```

### <a name="add-classifier"></a>Osztályozó hozzáadása

Az osztályozó által igénybe vett rendelkezik betanítva épít adathalmazon.  Példák az átadásra és a testreszabott súlyok betanítására a [minta jegyzetfüzetek](https://aka.ms/aml-notebooks)készletében.

```python
classifier_output = model_graph.get_default_classifier(feature_tensor)
print(classifier_output)
```

### <a name="save-the-model"></a>A modell mentése

Most, hogy az előfeldolgozó, a ResNet 50 Képtulajdonság és az osztályozó be lett töltve, mentse a gráfot és a társított változókat modellként.

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

### <a name="save-input-and-output-tensors"></a>Bemeneti és kimeneti adatmennyiségek mentése
Az előfeldolgozás és az osztályozó lépések során létrehozott bemeneti és kimeneti kétlépcsők a modell átalakításához és a következtetésekhez szükségesek.

```python
input_tensors = in_images.name
output_tensors = classifier_output.name

print(input_tensors)
print(output_tensors)
```

> [!IMPORTANT]
> Mentse a bemeneti és kimeneti TEN-ket, mert szüksége lesz rájuk a modell átalakításához és a következtetések megkereséséhez.

Az elérhető modellek és a megfelelő alapértelmezett osztályozó kimeneti TEN-elemek alább láthatók, ami azt eredményezi, hogy az alapértelmezett minősítőt használta.

+ Resnet50, QuantizedResnet50
  ```python
  output_tensors = "classifier_1/resnet_v1_50/predictions/Softmax:0"
  ```
+ Resnet152, QuantizedResnet152
  ```python
  output_tensors = "classifier/resnet_v1_152/predictions/Softmax:0"
  ```
+ Densenet121, QuantizedDensenet121
  ```python
  output_tensors = "classifier/densenet121/predictions/Softmax:0"
  ```
+ Vgg16, QuantizedVgg16
  ```python
  output_tensors = "classifier/vgg_16/fc8/squeezed:0"
  ```
+ SsdVgg, QuantizedSsdVgg
  ```python
  output_tensors = ['ssd_300_vgg/block4_box/Reshape_1:0', 'ssd_300_vgg/block7_box/Reshape_1:0', 'ssd_300_vgg/block8_box/Reshape_1:0', 'ssd_300_vgg/block9_box/Reshape_1:0', 'ssd_300_vgg/block10_box/Reshape_1:0', 'ssd_300_vgg/block11_box/Reshape_1:0', 'ssd_300_vgg/block4_box/Reshape:0', 'ssd_300_vgg/block7_box/Reshape:0', 'ssd_300_vgg/block8_box/Reshape:0', 'ssd_300_vgg/block9_box/Reshape:0', 'ssd_300_vgg/block10_box/Reshape:0', 'ssd_300_vgg/block11_box/Reshape:0']
  ```

### <a name="register-model"></a>Modell regisztrálása

[Regisztrálja](./concept-model-management-and-deployment.md) a MODELLT az SDK használatával az Azure Blob Storage-ban található zip-fájllal. A címkék és a modellel kapcsolatos egyéb metaadatok hozzáadásával nyomon követheti a betanított modelleket.

```python
from azureml.core.model import Model

registered_model = Model.register(workspace=ws,
                                  model_path=model_save_path,
                                  model_name=model_name)

print("Successfully registered: ", registered_model.name,
      registered_model.description, registered_model.version, sep='\t')
```

Ha már regisztrált egy modellt, és be szeretné tölteni, akkor lekérheti.

```python
from azureml.core.model import Model
model_name = "resnet50"
# By default, the latest version is retrieved. You can specify the version, i.e. version=1
registered_model = Model(ws, name="resnet50")
print(registered_model.name, registered_model.description,
      registered_model.version, sep='\t')
```

### <a name="convert-model"></a>Modell konvertálása

Alakítsa át a TensorFlow gráfot a nyílt neurális hálózati Exchange-formátumra ([ONNX](https://onnx.ai/)).  Meg kell adnia a bemeneti és a kimeneti hálózatok nevét, ezeket a neveket pedig az ügyfél fogja használni a webszolgáltatás felhasználása során.

```python
from azureml.accel import AccelOnnxConverter

convert_request = AccelOnnxConverter.convert_tf_model(
    ws, registered_model, input_tensors, output_tensors)

# If it fails, you can run wait_for_completion again with show_output=True.
convert_request.wait_for_completion(show_output=False)

# If the above call succeeded, get the converted model
converted_model = convert_request.result
print("\nSuccessfully converted: ", converted_model.name, converted_model.url, converted_model.version,
      converted_model.id, converted_model.created_time, '\n')
```

### <a name="create-docker-image"></a>Docker-rendszerkép létrehozása

Az átalakított modell és az összes függőség hozzá van adva egy Docker-rendszerképhez.  Ezt a Docker-rendszerképet ezután üzembe helyezheti és létrehozhatja.  A támogatott üzembe helyezési célok közé tartoznak a Felhőbeli AK vagy az Edge-eszközök, például a [Azure Data Box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview).  Címkéket és leírásokat is hozzáadhat a regisztrált Docker-rendszerképhez.

```python
from azureml.core.image import Image
from azureml.accel import AccelContainerImage

image_config = AccelContainerImage.image_configuration()
# Image name must be lowercase
image_name = "{}-image".format(model_name)

image = Image.create(name=image_name,
                     models=[converted_model],
                     image_config=image_config,
                     workspace=ws)
image.wait_for_creation(show_output=False)
```

Sorolja fel a képeket címkével, és szerezze be a hibakereséshez szükséges részletes naplókat.

```python
for i in Image.list(workspace=ws):
    print('{}(v.{} [{}]) stored at {} with build log {}'.format(
        i.name, i.version, i.creation_state, i.image_location, i.image_build_log_uri))
```

## <a name="2-deploy-to-cloud-or-edge"></a>2. Üzembe helyezés a felhőben vagy az Edge-ben

### <a name="deploy-to-the-cloud"></a>Üzembe helyezés a felhőben

Az Azure Kubernetes Service (AKS) használatával a modellt webszolgáltatásként, amely nagy méretű éles üzembe helyezéséhez. Létrehozhat egy újat a Azure Machine Learning SDK, a CLI vagy a Azure Portal használatával.

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Specify the Standard_PB6s Azure VM
prov_config = AksCompute.provisioning_configuration(vm_size="Standard_PB6s",
                                                    agent_count=1)

aks_name = 'my-aks-cluster'
# Create the cluster
aks_target = ComputeTarget.create(workspace=ws,
                                  name=aks_name,
                                  provisioning_configuration=prov_config)
```

Az AK-ban üzemelő példány körülbelül 15 percet vesz igénybe.  Ellenőrizze, hogy az üzemelő példány sikeres volt-e.

```python
aks_target.wait_for_completion(show_output=True)
print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```

Telepítse a tárolót az AK-fürtbe.
```python
from azureml.core.webservice import Webservice, AksWebservice

# For this deployment, set the web service configuration without enabling auto-scaling or authentication for testing
aks_config = AksWebservice.deploy_configuration(autoscale_enabled=False,
                                                num_replicas=1,
                                                auth_enabled=False)

aks_service_name = 'my-aks-service'

aks_service = Webservice.deploy_from_image(workspace=ws,
                                           name=aks_service_name,
                                           image=image,
                                           deployment_config=aks_config,
                                           deployment_target=aks_target)
aks_service.wait_for_deployment(show_output=True)
```

#### <a name="test-the-cloud-service"></a>A Cloud Service tesztelése
A Docker-rendszerkép támogatja a gRPC és a "prediktív" API-t kiszolgáló TensorFlow.  A minta ügyfelet használva hívja meg a Docker-rendszerképet a modellből származó előrejelzések beszerzéséhez.  A minta ügyfél kódja elérhető:
- [Python](https://github.com/Azure/aml-real-time-ai/blob/master/pythonlib/amlrealtimeai/client.py)
- [C#](https://github.com/Azure/aml-real-time-ai/blob/master/sample-clients/csharp)

Ha a TensorFlow-t szeretné használni, [letöltheti a minta-ügyfelet](https://www.tensorflow.org/serving/setup).

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

Mivel ez az osztályozó a [ImageNet](http://www.image-net.org/) -adatkészletre lett betanítva, az osztályokat az emberi olvasásra alkalmas címkékre képezi le.

```python
import requests
classes_entries = requests.get(
    "https://raw.githubusercontent.com/Lasagne/Recipes/master/examples/resnet50/imagenet_classes.txt").text.splitlines()

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

### <a name="clean-up-the-service"></a>A szolgáltatás tisztítása
Törölje a webszolgáltatást, a képet és a modellt (ebben a sorrendben kell elvégezni, mivel függőségek vannak).

```python
aks_service.delete()
aks_target.delete()
image.delete()
registered_model.delete()
converted_model.delete()
```

### <a name="deploy-to-a-local-edge-server"></a>Üzembe helyezés helyi peremhálózati kiszolgálón

Minden [Azure Data Box Edge eszköz](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview
) tartalmaz egy FPGA a modell futtatásához.  Egyszerre csak egy modell futhat a FPGA.  Egy másik modell futtatásához egyszerűen helyezzen üzembe egy új tárolót. [Ebben az Azure-mintában](https://github.com/Azure-Samples/aml-hardware-accelerated-models)az utasítások és a mintakód is megtalálható.

## <a name="secure-fpga-web-services"></a>Biztonságos FPGA-webszolgáltatások

A FPGA webszolgáltatások biztonságossá tételéhez tekintse meg a [biztonságos webszolgáltatások](how-to-secure-web-service.md) dokumentumát.

## <a name="next-steps"></a>További lépések

Tekintse meg ezeket a jegyzetfüzeteket, videókat és blogokat:

+ Több [minta jegyzetfüzet](https://aka.ms/aml-accel-models-notebooks).

+ [Nagy kapacitású hardver: ML az Azure + FPGA-ra épülő skálán: Build 2018 (videó)](https://channel9.msdn.com/events/Build/2018/BRK3202)

+ [A Microsoft FPGA-alapú konfigurálható felhő (videó)](https://channel9.msdn.com/Events/Build/2017/B8063)

+ [Project Brainwave valós idejű mesterséges: Projekt kezdőlapja](https://www.microsoft.com/research/project/project-brainwave/)
