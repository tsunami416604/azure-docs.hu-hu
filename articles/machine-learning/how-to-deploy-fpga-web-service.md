---
title: Mik azok az FPGA - hogyan kell telepíteni
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan helyezhet üzembe egy webszolgáltatást egy FPGA-n futó modellel az Azure Machine Learning segítségével az ultraalacsony késésű következtetés érdekében.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: jordane
author: jpe316
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: 870f7b0ab0f1d7b247435cdbb74e21801b3b052a
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81257181"
---
# <a name="what-are-field-programmable-gate-arrays-fpga-and-how-to-deploy"></a>Mik azok a mezőprogramozható kaputömbök (FPGA), és hogyan kell telepíteni
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ez a cikk bemutatja a mezőprogramozható kaputömbök (FPGA), és bemutatja, hogyan telepítheti a modellek et az Azure Machine Learning használatával egy Azure FPGA.

Az FPGA-k programozható logikai blokkok tömbjét és újrakonfigurálható összekötők hierarchiáját tartalmazzák. Az összeköttetések lehetővé teszik, hogy ezeket a blokkokat a gyártás után különböző módokon konfigurálják. Más chipekhez képest az FPGA-k a programozhatóság és a teljesítmény kombinációját biztosítják.

## <a name="fpgas-vs-cpu-gpu-and-asic"></a>FPGA-k vs. CPU, GPU és ASIC

Az alábbi ábra és táblázat bemutatja, hogyan viszonyulnak az FPGA-k más processzorokhoz.

![Az Azure Machine Learning FPGA-összehasonlításának diagramja](./media/how-to-deploy-fpga-web-service/azure-machine-learning-fpga-comparison.png)

|Processzor||Leírás|
|---|:-------:|------|
|Alkalmazásspecifikus integrált áramkörök|Asics|Az egyéni áramkörök, például a Google TensorFlow processzoregységei (TPU) biztosítják a legnagyobb hatékonyságot. Nem lehet őket újrakonfigurálni, ahogy az igényei változnak.|
|Mező-programozható kaputömbök|FPGA-k|Az FPGA-k, például az Azure-ban elérhetők, az ASIC-khez közeli teljesítményt nyújtanak. Emellett rugalmasak és idővel újrakonfigurálhatók, hogy új logikát valósítsanak meg.|
|Grafikus feldolgozó egységek|GPU-k|Népszerű választás a ai számításokhoz. A GPU-k párhuzamos feldolgozási lehetőségeket kínálnak, így gyorsabb a képrenderelésnél, mint a CPU-k.|
|Központi feldolgozó egységek|CPU-k|Általános célú processzorok, amelyek teljesítménye nem ideális a grafika és a videó feldolgozáshoz.|

Az Azure-beli FPGA-k az Intel FPGA-eszközein alapulnak, amelyeket az adatszakértők és a fejlesztők a valós idejű AI-számítások felgyorsítására használnak. Ez az FPGA-kompatibilis architektúra teljesítményt, rugalmasságot és skálázást kínál, és elérhető az Azure-ban.

FPGA lehetővé teszi, hogy alacsony késés valós idejű következtetés (vagy modell pontozási) kérelmek elérése érdekében. Aszinkron kérelmek (kötegelés) nincs szükség. Kötegelés okozhat késést, mert több adatot kell feldolgozni. Neurális feldolgozó egységek implementációi nem igényelnek kötegelést; ezért a késleltetés lehet sokszor alacsonyabb, mint a CPU és a GPU processzorok.

### <a name="reconfigurable-power"></a>Újrakonfigurálható teljesítmény
FPGA-k at konfigurálhat a különböző típusú gépi tanulási modellekhez. Ez a rugalmasság megkönnyíti az alkalmazások felgyorsítását a legoptimálisabb numerikus pontosság és a használt memóriamodell alapján. Mivel az FPGA-k újrakonfigurálhatók, naprakész maradhat a gyorsan változó AI-algoritmusok követelményeivel.

## <a name="whats-supported-on-azure"></a>Mi támogatott az Azure-ban?
A Microsoft Azure a világ legnagyobb felhőberuházása az FPGA-kba. Az FPGA-kompatibilis hardverarchitektúra használatával a betanított neurális hálózatok gyorsan és alacsonyabb késéssel futnak. Az Azure párhuzamosíthatja az előre betanított mély neurális hálózatok (DNN) az FPGA-k között a szolgáltatás horizontális felskálázása. A DNN-ek előre betaníthatók, mint egy mély featurizer az átviteli tanuláshoz, vagy finomhangolt frissített súlyokkal.

Az Azure-beli FPGA-k a következőket támogatják:

+ Képbesorolási és -felismerési forgatókönyvek
+ TensorFlow-telepítés (Tensorflow 1.x szükséges)
+ Intel FPGA hardver

Ezek a DNN modellek jelenleg elérhetők:
  - ResNet 50
  - ResNet 152
  - Sűrűháló-121
  - VGG-16
  - SSD-VGG

Az FPGA-k a következő Azure-régiókban érhetők el:
  - USA keleti régiója
  - Délkelet-Ázsia
  - Nyugat-Európa
  - USA nyugati régiója, 2.

> [!IMPORTANT]
> A késés és az átviteli képesség optimalizálása érdekében az ügyfél adatokat küld az FPGA-modell nek a fenti régiók egyikében kell lennie (az, amelybe a modellt üzembe helyezte).

Az **Azure-virtuális gépek PBS-családja** Intel Arria 10 FPGA-kat tartalmaz. "Standard PBS-család vCPU-iként" jelenik meg, amikor ellenőrzi az Azure-kvóta-foglalást. A PB6 virtuális gép hat vCPU-val és egy FPGA-val rendelkezik, és az Azure ML automatikusan kiépíti egy modell fpga-ra való üzembe helyezése ként. Csak az Azure ML-ben használható, és nem futtathat tetszőleges bitfolyamokat. Például nem fogja tudni villogni az FPGA-t bitfolyamokkal a titkosításhoz, kódoláshoz stb.

### <a name="scenarios-and-applications"></a>Forgatókönyvek és alkalmazások

Az Azure FPGA-k integrálva vannak az Azure Machine Learningszolgáltatással. A Microsoft fPGA-kat használ a DNN kiértékeléséhez, a Bing keresési rangsorolásához és a szoftveráltal definiált hálózati (SDN) gyorsításhoz a késés csökkentése érdekében, miközben más feladatokhoz felszabadítja a CPU-kat.

A következő forgatókönyvek fpga-kat használnak:
+ [Automatizált optikai ellenőrző rendszer](https://blogs.microsoft.com/ai/build-2018-project-brainwave/)

+ [Földtakaró feltérképezése](https://blogs.technet.microsoft.com/machinelearning/2018/05/29/how-to-use-fpgas-for-deep-learning-inference-to-perform-land-cover-mapping-on-terabytes-of-aerial-images/)

## <a name="example-deploy-models-on-fpgas"></a>Példa: Modellek telepítése FPGA-kon

Egy modellt webszolgáltatásként helyezhet üzembe az Azure Machine Learning hardveres gyorsított modelljeivel. Az FPGA-k használata ultraalacsony késleltetésű következtetést biztosít, még egyetlen kötegméret esetén is. A következtetés vagy a modell pontozása az a fázis, ahol az üzembe helyezett modell előrejelzési, leggyakrabban az éles adatok.

### <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés.  Ha még nincs előfizetése, hozzon létre egy ingyenes fiókot, mielőtt hozzákezd. Próbálja ki még ma [az Azure Machine Learning ingyenes vagy fizetős verzióját.](https://aka.ms/AMLFree)

- FPGA kvóta. Az Azure CLI segítségével ellenőrizze, hogy rendelkezik-e kvótával:

    ```azurecli-interactive
    az vm list-usage --location "eastus" -o table --query "[?localName=='Standard PBS Family vCPUs']"
    ```

    > [!TIP]
    > A többi lehetséges ``southeastasia`` ``westeurope``hely ``westus2``a , és .

    A parancs a következőhöz hasonló szöveget ad vissza:

    ```text
    CurrentValue    Limit    LocalName
    --------------  -------  -------------------------
    0               6        Standard PBS Family vCPUs
    ```

    Győződjön meg arról, hogy legalább 6 vCPU-val rendelkezik az __CurrentValue__csoportban.

    Ha nem rendelkezik kvótával, akkor [https://aka.ms/accelerateAI](https://aka.ms/accelerateAI)küldjön be egy kérelmet a.

- Egy Azure Machine Learning-munkaterület és az Azure Machine Learning SDK python telepítve. További információt a Munkaterület létrehozása című [témakörben talál.](how-to-manage-workspace.md)
 
- A Python SDK hardveresen gyorsított modellekhez:

    ```bash
    pip install --upgrade azureml-accel-models[cpu]
    ```

## <a name="1-create-and-containerize-models"></a>1. Modellek létrehozása és konténerezése

Ez a dokumentum leírja, hogyan hozhat létre egy TensorFlow-grafikont a bemeneti lemezkép előfeldolgozásához, a ResNet 50-et fpga-n használva, majd a funkciók futtatását az ImageNet-adatkészleten betanított osztályozón keresztül.

Kövesse az utasításokat, hogy:

* A TensorFlow modell meghatározása
* A modell konvertálása
* A modell üzembe helyezése
* Az üzembe helyezett modell felhasználása
* Telepített szolgáltatások törlése

Használja az [Azure Machine Learning SDK python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) szolgáltatásdefiníció létrehozásához. A szolgáltatásdefiníció egy olyan fájl, amely a TensorFlow-n alapuló gráfok (bemenet, featurizer és osztályozó) folyamatát írja le. A központi telepítési parancs automatikusan tömöríti a definíciót és a grafikonokat egy ZIP-fájlba, és feltölti a ZIP-t az Azure Blob storage-ba. A DNN már telepítve van az FPGA futtatásához.

### <a name="load-azure-machine-learning-workspace"></a>Az Azure Machine Learning munkaterületének betöltése

Töltse be az Azure Machine Learning-munkaterületet.

```python
import os
import tensorflow as tf

from azureml.core import Workspace

ws = Workspace.from_config()
print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep='\n')
```

### <a name="preprocess-image"></a>Előfeldolgozási kép

A webszolgáltatás bemenete JPEG-lemezkép.  Az első lépés a JPEG-kép dekódolása és feldolgozása.  A JPEG-képek karakterláncként lesznek kezelve, és az eredmény tenszorok, amelyek a ResNet 50 modell bemenetei lesznek.

```python
# Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
import azureml.accel.models.utils as utils
tf.reset_default_graph()

in_images = tf.placeholder(tf.string)
image_tensors = utils.preprocess_array(in_images)
print(image_tensors.shape)
```

### <a name="load-featurizer"></a>Terhelés featurizer

Inicializálja a modellt, és töltse le a ResNet50 kvantált verziójának TensorFlow ellenőrzőpontját, amelyet featurizerként kell használni.  A "QuantizedResnet50" kifejezés helyébe az alábbi kódrészlet más mély neurális hálózatok importálásával kerülhet:

- KvantizedResnet152
- KvantizedVgg16
- Sűrűnet121

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

Ez az osztályozó be van tanítva az ImageNet adatkészleten.  Példák az átviteli tanulásra és a személyre szabott súlyok betanítására a [mintajegyzetfüzetek](https://aka.ms/aml-notebooks)készletében.

```python
classifier_output = model_graph.get_default_classifier(feature_tensor)
print(classifier_output)
```

### <a name="save-the-model"></a>A modell mentése

Most, hogy az előfeldolgozó, a ResNet 50 featurizer és az osztályozó be van töltve, mentse a grafikont és a kapcsolódó változókat modellként.

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

### <a name="save-input-and-output-tensors"></a>A bemeneti és kimeneti tensors mentése
Az előfeldolgozási és osztályozói lépések során létrehozott bemeneti és kimeneti tenszorokra lesz szükség a modellkonvertáláshoz és a következtetéshez.

```python
input_tensors = in_images.name
output_tensors = classifier_output.name

print(input_tensors)
print(output_tensors)
```

> [!IMPORTANT]
> Mentse a bemeneti és kimeneti tenszorokat, mert szüksége lesz rájuk a modellkonverziós és következtetési kérelmekhez.

A rendelkezésre álló modellek és a megfelelő alapértelmezett osztályozó kimeneti tensors alatt vannak, amelyet az alapértelmezett osztályozó használata esetén a következtetéshez használhat.

+ Resnet50, KvantizedResnet50
  ```python
  output_tensors = "classifier_1/resnet_v1_50/predictions/Softmax:0"
  ```
+ Resnet152, KvantizáltResnet152
  ```python
  output_tensors = "classifier/resnet_v1_152/predictions/Softmax:0"
  ```
+ Densenet121, KvantizáltSűrű121
  ```python
  output_tensors = "classifier/densenet121/predictions/Softmax:0"
  ```
+ Vgg16, KonkvantáltVgg16
  ```python
  output_tensors = "classifier/vgg_16/fc8/squeezed:0"
  ```
+ SsdVgg, KonkvandáltSszdVgg
  ```python
  output_tensors = ['ssd_300_vgg/block4_box/Reshape_1:0', 'ssd_300_vgg/block7_box/Reshape_1:0', 'ssd_300_vgg/block8_box/Reshape_1:0', 'ssd_300_vgg/block9_box/Reshape_1:0', 'ssd_300_vgg/block10_box/Reshape_1:0', 'ssd_300_vgg/block11_box/Reshape_1:0', 'ssd_300_vgg/block4_box/Reshape:0', 'ssd_300_vgg/block7_box/Reshape:0', 'ssd_300_vgg/block8_box/Reshape:0', 'ssd_300_vgg/block9_box/Reshape:0', 'ssd_300_vgg/block10_box/Reshape:0', 'ssd_300_vgg/block11_box/Reshape:0']
  ```

### <a name="register-model"></a>Modell regisztrálása

[Regisztrálja](concept-model-management-and-deployment.md) a modellt az SDK használatával az Azure Blob storage-beli ZIP-fájllal. Címkék és egyéb metaadatok hozzáadása a modellről segít nyomon követni a betanított modelleket.

```python
from azureml.core.model import Model

registered_model = Model.register(workspace=ws,
                                  model_path=model_save_path,
                                  model_name=model_name)

print("Successfully registered: ", registered_model.name,
      registered_model.description, registered_model.version, sep='\t')
```

Ha már regisztrált egy modellt, és be szeretné tölteni, lekérheti azt.

```python
from azureml.core.model import Model
model_name = "resnet50"
# By default, the latest version is retrieved. You can specify the version, i.e. version=1
registered_model = Model(ws, name="resnet50")
print(registered_model.name, registered_model.description,
      registered_model.version, sep='\t')
```

### <a name="convert-model"></a>Modell konvertálása

A TensorFlow-diagram konvertálása Open Neural Network Exchange formátumra ([ONNX](https://onnx.ai/)).  Meg kell adnia a bemeneti és kimeneti tensors nevét, és ezeket a neveket az ügyfél fogja használni a webszolgáltatás használatakor.

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

### <a name="create-docker-image"></a>Docker-lemezkép létrehozása

A konvertált modell és az összes függőség hozzáadódik a Docker-rendszerképhez.  Ez a Docker-rendszerkép ezután üzembe helyezhető és példányosítani.  A támogatott telepítési célok közé tartozik az AKS a felhőben vagy egy peremhálózati eszköz, például [az Azure Data Box Edge.](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview)  Címkéket és leírásokat is hozzáadhat a regisztrált Docker-lemezképhez.

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

Sorolja fel a képeket címke szerint, és kapja meg a részletes naplókat a hibakereséshez.

```python
for i in Image.list(workspace=ws):
    print('{}(v.{} [{}]) stored at {} with build log {}'.format(
        i.name, i.version, i.creation_state, i.image_location, i.image_build_log_uri))
```

## <a name="2-deploy-to-cloud-or-edge"></a>2. Üzembe helyezés felhőre vagy peremre

### <a name="deploy-to-the-cloud"></a>Üzembe helyezés a felhőben

A modell üzembe helyezéséhez, mint egy nagy méretű éles webszolgáltatás, használja az Azure Kubernetes-szolgáltatás (AKS). Az Azure Machine Learning SDK, CLI vagy [Azure Machine Learning stúdió](https://ml.azure.com)használatával újat hozhat létre.

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Specify the Standard_PB6s Azure VM and location. Values for location may be "eastus", "southeastasia", "westeurope", or "westus2". If no value is specified, the default is "eastus".
prov_config = AksCompute.provisioning_configuration(vm_size = "Standard_PB6s",
                                                    agent_count = 1,
                                                    location = "eastus")

aks_name = 'my-aks-cluster'
# Create the cluster
aks_target = ComputeTarget.create(workspace=ws,
                                  name=aks_name,
                                  provisioning_configuration=prov_config)
```

Az AKS-telepítés körülbelül 15 percet is igénybe vehet.  Ellenőrizze, hogy a központi telepítés sikeres volt-e.

```python
aks_target.wait_for_completion(show_output=True)
print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```

Telepítse a tárolót az AKS-fürtbe.
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

#### <a name="test-the-cloud-service"></a>A felhőszolgáltatás tesztelése
A Docker-rendszerkép támogatja a gRPC-t és a TensorFlow-kiszolgáló "előre" API-t.  A mintaügyfél segítségével hívja meg a Docker-rendszerképet a modellből származó előrejelzések lehívásához.  Minta ügyfélkód érhető el:
- [Python](https://github.com/Azure/aml-real-time-ai/blob/master/pythonlib/amlrealtimeai/client.py)
- [C #](https://github.com/Azure/aml-real-time-ai/blob/master/sample-clients/csharp)

Ha a TensorFlow-kiszolgálót szeretné használni, [letöltheti a mintaügyfelet.](https://www.tensorflow.org/serving/setup)

```python
# Using the grpc client in Azure ML Accelerated Models SDK package
from azureml.accel import PredictionClient

address = aks_service.scoring_uri
ssl_enabled = address.startswith("https")
address = address[address.find('/')+2:].strip('/')
port = 443 if ssl_enabled else 80

# Initialize Azure ML Accelerated Models client
client = PredictionClient(address=address,
                          port=port,
                          use_ssl=ssl_enabled,
                          service_name=aks_service.name)
```

Mivel ez az osztályozó be van tanítva az [ImageNet](http://www.image-net.org/) adatkészleten, rendelje hozzá az osztályokat az ember által olvasható címkékhez.

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

### <a name="clean-up-the-service"></a>A szolgáltatás karbantartása
Törölje a webszolgáltatást, a lemezképet és a modellt (ebben a sorrendben kell elvégezni, mivel függőségek vannak).

```python
aks_service.delete()
aks_target.delete()
image.delete()
registered_model.delete()
converted_model.delete()
```

### <a name="deploy-to-a-local-edge-server"></a>Telepítés helyi peremhálózati kiszolgálóra

Minden [Azure Data Box Edge-eszköz](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview
) tartalmaz egy FPGA-t a modell futtatásához.  Egyszerre csak egy modell futtatható az FPGA-n.  Egy másik modell futtatásához csak üzembe helyezhet egy új tárolót. Az utasítások és a mintakód ebben az [Azure-mintában](https://github.com/Azure-Samples/aml-hardware-accelerated-models)találhatók.

## <a name="secure-fpga-web-services"></a>Biztonságos FPGA webszolgáltatások

Az FPGA webszolgáltatások védelméről a [Biztonságos webszolgáltatások](how-to-secure-web-service.md) dokumentumban tájékodhat.

## <a name="next-steps"></a>További lépések

Tekintse meg ezeket a jegyzetfüzeteket, videókat és blogokat:

+ Több [mintajegyzetfüzet](https://aka.ms/aml-accel-models-notebooks)

+ [Nagy kapacitású hardver: Az Azure + FPGA: Build 2018 (videó)](https://channel9.msdn.com/events/Build/2018/BRK3202)

+ [A Microsoft FPGA-alapú konfigurálható felhőben (videó)](https://channel9.msdn.com/Events/Build/2017/B8063)

+ [Project Brainwave valós idejű AI: projekt honlapja](https://www.microsoft.com/research/project/project-brainwave/)
