---
title: Modellek üzembe helyezése FPGA-kban webszolgáltatásként
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan webszolgáltatás üzembe helyezése az Azure Machine Learning-FPGA futó modell.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 10/29/2018
ms.custom: seodec18
ms.openlocfilehash: f5252f8de56658ebcf0d3815f6bd6246c495cd75
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53090414"
---
# <a name="deploy-a-model-as-a-web-service-on-an-fpga-with-azure-machine-learning"></a>Modell üzembe helyezése az Azure Machine Learning-FPGA webszolgáltatásként

Telepíthet egy modellt webszolgáltatásként, amely a [programmable gate arrays (FPGA) mezőben](concept-accelerate-with-fpgas.md).  FPGA-kban használatával biztosít ultramagas közel valós idejű következtetési egyetlen kötegméret mellett is.   

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Ha még nincs előfizetése, hozzon létre egy [ingyenes fiókot](https://aka.ms/AMLfree), mielőtt hozzákezd.

- Az Azure Machine Learning szolgáltatás munkaterület és az Azure Machine Learning SDK telepítve van a Pythonhoz készült. Ezekről az előfeltételekről használatával beszerzéséről a [a fejlesztési környezet konfigurálása](how-to-configure-environment.md) dokumentumot.
 
  - A munkaterület kell lennie a *USA keleti RÉGIÓJA 2* régióban.

  - Telepítse a contrib kiegészítő funkciók:

    ```shell
    pip install --upgrade azureml-sdk[contrib]
    ```  

## <a name="create-and-deploy-your-model"></a>Hozzon létre, és a modell üzembe helyezése
A bemeneti kép, a szabadkézi előfeldolgozása ResNet-50 használatával egy FPGA a folyamat létrehozása, és futtassa a funkciók egy épít adatkészlet tanított classifer keresztül.

Kövesse az utasításokat:

* A modell folyamat meghatározása
* A modell üzembe helyezése
* A telepített modell feldolgozása
* Telepített szolgáltatások törlése

> [!IMPORTANT]
> Optimalizálható a teljesítmény és a késés, az ügyfél és a végpontot Azure ugyanabban a régióban kell lennie.  Jelenleg az API-k jönnek létre az East US Azure-régióban.



### <a name="preprocess-image"></a>Kép előfeldolgozása
A folyamat első szakaszában, hogy a képek előfeldolgozása.

```python
import os
import tensorflow as tf

# Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
import azureml.contrib.brainwave.models.utils as utils
in_images = tf.placeholder(tf.string)
image_tensors = utils.preprocess_array(in_images)
print(image_tensors.shape)
```

### <a name="add-featurizer"></a>Featurizer hozzáadása
A modell inicializálása, és töltse le a featurizer használandó ResNet50 kvantált verziójának TensorFlow ellenőrzőpont.

```python
from azureml.contrib.brainwave.models import QuantizedResnet50, Resnet50
model_path = os.path.expanduser('~/models')
model = QuantizedResnet50(model_path, is_frozen = True)
feature_tensor = model.import_graph_def(image_tensors)
print(model.version)
print(feature_tensor.name)
print(feature_tensor.shape)
```

### <a name="add-classifier"></a>Besorolás hozzáadása
Az osztályozó által igénybe vett rendelkezik betanítva épít adathalmazon.

```python
classifier_input, classifier_output = Resnet50.get_default_classifier(feature_tensor, model_path)
```

### <a name="create-service-definition"></a>Szolgáltatás-definíció létrehozása
Most, hogy elvégezhessék a lemezkép előfeldolgozása featurizer és osztályozó, amely a szolgáltatás fut, a szolgáltatás definíciós hozhat létre. A szolgáltatásdefiníció a modellben, amelyre telepítve van a FPGA-szolgáltatás által létrehozott fájlokat. A szolgáltatásdefiníció áll egy folyamat. A folyamat több szakaszra oszlik, amely sorrendben futnak, a rendszer.  TensorFlow-szakaszok Keras-szakaszok és BrainWave szakaszok támogatottak.  A szakasz a a szolgáltatás, amelynek a kimenete a későbbi szakaszában minden egyes fázis a bemenő sorrendben futnak.

Hozzon létre egy TensorFlow szakaszban, adja meg a gráf (ebben az esetben használt alapértelmezett gráf) és a bemeneti tartalmazó munkamenet, és kimeneti tensors erre a szakaszra.  Ez az információ a graph mentéséhez, így futtathatók a szolgáltatásban szolgál.

```python
from azureml.contrib.brainwave.pipeline import ModelDefinition, TensorflowStage, BrainWaveStage

save_path = os.path.expanduser('~/models/save')
model_def_path = os.path.join(save_path, 'service_def.zip')

model_def = ModelDefinition()
with tf.Session() as sess:
    model_def.pipeline.append(TensorflowStage(sess, in_images, image_tensors))
    model_def.pipeline.append(BrainWaveStage(sess, model))
    model_def.pipeline.append(TensorflowStage(sess, classifier_input, classifier_output))
    model_def.save(model_def_path)
    print(model_def_path)
```

### <a name="deploy-model"></a>Modell üzembe helyezése
Létrehoz egy szolgáltatást a szolgáltatás-definícióból.  A munkaterület kell lennie az USA 2. keleti régiója helyen.

```python
from azureml.core import Workspace

ws = Workspace.from_config()
print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep = '\n')

from azureml.core.model import Model
model_name = "resnet-50-rtai"
registered_model = Model.register(ws, model_def_path, model_name)

from azureml.core.webservice import Webservice
from azureml.exceptions import WebserviceException
from azureml.contrib.brainwave import BrainwaveWebservice, BrainwaveImage
service_name = "imagenet-infer"
service = None
try:
    service = Webservice(ws, service_name)
except WebserviceException:
    image_config = BrainwaveImage.image_configuration()
    deployment_config = BrainwaveWebservice.deploy_configuration()
    service = Webservice.deploy_from_model(ws, service_name, [registered_model], image_config, deployment_config)
    service.wait_for_deployment(true)
```

### <a name="test-the-service"></a>A szolgáltatás tesztelése
Kép küldése az API-hoz, és tesztelje a választ, adja hozzá a kimeneti Osztályazonosító leképezés épít osztálynév.

```python
import requests
classes_entries = requests.get("https://raw.githubusercontent.com/Lasagne/Recipes/master/examples/resnet50/imagenet_classes.txt").text.splitlines()
```

Hívják meg a szolgáltatást, és cserélje le a "your image.jpg" fájlnevet alábbi képet, a gép számára. 

```python
with open('your-image.jpg') as f:
    results = service.run(f)
# map results [class_id] => [confidence]
results = enumerate(results)
# sort results by confidence
sorted_results = sorted(results, key=lambda x: x[1], reverse=True)
# print top 5 results
for top in sorted_results[:5]:
    print(classes_entries[top[0]], 'confidence:', top[1])
``` 

### <a name="clean-up-service"></a>Szolgáltatás törlése
Törölje a szolgáltatást.

```python
service.delete()
    
registered_model.delete()
```

## <a name="secure-fpga-web-services"></a>Biztonságos FPGA-webszolgáltatások

FPGA-kban futtat az Azure Machine Learning-modellek adja meg az SSL-támogatás és a kulcs alapú hitelesítés. Ez lehetővé teszi, hogy a szolgáltatás és az ügyfelek által küldött biztonságos adatokat való hozzáférés korlátozása. [Ismerje meg, hogyan védheti meg a webszolgáltatás](how-to-secure-web-service.md).


## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [felhasználás a gépi Tanulási modellek webszolgáltatásként üzembe helyezett](how-to-consume-web-service.md).
