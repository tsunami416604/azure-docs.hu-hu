---
title: A hardveres gyorsítás az Azure Machine Learning FPGA csomag
description: További tudnivalók az Azure Machine Learning felhasználók a python-csomagokat.
ms.service: machine-learning
ms.component: studio
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: routlaw
author: rloutlaw
ms.date: 05/07/2018
ms.openlocfilehash: e680ef34be1d5dae2942c432de5e81fe620bbdc4
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34832978"
---
# <a name="azure-machine-learning-hardware-acceleration-package"></a>Az Azure Machine Learning hardveres gyorsítás csomag

Az Azure Machine Learning hardveres gyorsítás csomag a Python, amely lehetővé teszi, hogy adatelemzők és fejlesztők AI gyorsan Azure Machine Learning pip telepíthető bővítmény:

+ Featurize képek ResNet 50 quantized verziója

+ Ezekről a szolgáltatásokról alapján vonat osztályozó

+ A modellek telepítése [programozható kapu tömbök (FPGA) mezőben](concept-accelerate-with-fpgas.md) az egészen kis késleltetésű inferencing Azure

## <a name="prerequisites"></a>Előfeltételek

1. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

1. Létre kell hoznia egy Azure Machine Learning modell kezelése fiókot. A fiók létrehozásával kapcsolatos további információkért lásd: a [Azure Machine Learning gyors üzembe helyezés és a munkaterületet üzemeltető telepítési](../service/quickstart-installation.md) dokumentum. 

1. A csomag telepítve kell lennie. 

 
## <a name="how-to-install-the-package"></a>A csomag telepítése

1. Töltse le és telepítse a legújabb [Git](https://git-scm.com/downloads).

2. Telepítés [Anaconda (Python 3.6)](https://conda.io/miniconda.html)

3. Töltse le a előre konfigurált Anaconda-környezetekben, használja a következő parancsot a Git parancssorból:

    ```
    git clone https://aka.ms/aml-real-time-ai
    ```
5. A környezet létrehozásához nyissa meg egy **Anaconda Rákérdezés** és használja a következő parancsot:

    ```
    conda env create -f aml-real-time-ai/environment.yml
    ```

6. A környezet aktiválásához a következő paranccsal:

    ```
    conda activate amlrealtimeai
    ```

## <a name="sample-code"></a>Mintakód

A mintakód végigvezeti a modell rendszerbe állítása egy FPGA az SDK segítségével.

1. A csomag importálása:
   ```python
   import amlrealtimeai
   from amlrealtimeai import resnet50
   ```

1. A lemezkép folyamat előtti:
   ```python 
   from amlrealtimeai.resnet50.model import LocalQuantizedResNet50
   model_path = os.path.expanduser('~/models')
   model = LocalQuantizedResNet50(model_path)
   print(model.version)
   ```

1. Featurize a lemezképek:
   ```python 
   from amlrealtimeai.resnet50.model import LocalQuantizedResNet50
   model_path = os.path.expanduser('~/models')
   model = LocalQuantizedResNet50(model_path)
   print(model.version)
   ```

1. Hozzon létre egy osztályozó:
   ```python
   model.import_graph_def(include_featurizer=False)
   print(model.classifier_input)
   print(model.classifier_output)
   ```

1. A szolgáltatás-definíció létrehozása:
   ```python
   from amlrealtimeai.pipeline import ServiceDefinition, TensorflowStage, BrainWaveStage
   save_path = os.path.expanduser('~/models/save')
   service_def_path = os.path.join(save_path, 'service_def.zip')

   service_def = ServiceDefinition()
   service_def.pipeline.append(TensorflowStage(tf.Session(), in_images, image_tensors))
   service_def.pipeline.append(BrainWaveStage(model))
   service_def.pipeline.append(TensorflowStage(tf.Session(), model.classifier_input, model.classifier_output))
   service_def.save(service_def_path)
   print(service_def_path)
   ```
 
1. Készítse elő a modell egy FPGA futtatni:
   ```python
   from amlrealtimeai import DeploymentClient

   subscription_id = "<Your Azure Subscription ID>"
   resource_group = "<Your Azure Resource Group Name>"
   model_management_account = "<Your AzureML Model Management Account Name>"

   model_name = "resnet50-model"
   service_name = "quickstart-service"

   deployment_client = DeploymentClient(subscription_id, resource_group, model_management_account)
   ```

1. Egy FPGA futtatni a modell rendszerbe állítása:
   ```python
   service = deployment_client.get_service_by_name(service_name)
   model_id = deployment_client.register_model(model_name, service_def_path)

   if(service is None):
      service = deployment_client.create_service(service_name, model_id)    
   else:
      service = deployment_client.update_service(service.id, model_id)
   ```

1. Az ügyfél létrehozása:
    ```python
   from amlrealtimeai import PredictionClient
   client = PredictionClient(service.ipAddress, service.port)  
   ```

1. Az API hívása:
   ```python
   image_file = R'C:\path_to_file\image.jpg'
   results = client.score_image(image_file)
   ```

## <a name="reporting-issues"></a>Jelentéskészítési problémák

Használja a [fórum](https://aka.ms/aml-forum) hogy jelentse az esetleges problémákat tapasztal a csomag.

## <a name="next-steps"></a>További lépések

[A modell rendszerbe állítása a egy FPGA webszolgáltatásként](how-to-deploy-fpga-web-service.md)