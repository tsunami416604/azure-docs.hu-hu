---
title: Az Azure Machine Learning hardveres gyorsítás FPGA-csomagja
description: További információ az Azure Machine Learning-felhasználók számára, a python-csomagokat.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: reference
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 05/07/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 83c5a788f85fcc47c221f5c8f9e6944c4448fbf9
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2018
ms.locfileid: "51712126"
---
# <a name="azure-machine-learning-hardware-acceleration-package"></a>Az Azure Machine Learning hardveres gyorsítás csomag

>[!Note]
>**Ez a cikk elavult.** Ez a csomag FPGA kivezettük. Ez a funkció támogatása az Azure Machine Learning SDK lett hozzáadva. A csomag támogatja a növekményes lejár. [A támogatási idősor megtekintése](overview-what-happened-to-workbench.md#timeline). Ismerje meg, készül frissíteni [FPGA támogatási](concept-accelerate-with-fpgas.md).

Az Azure Machine Learning hardveres gyorsítás csomag egy Python pip telepíthető bővítmény az Azure Machine Learning, amely lehetővé teszi az adatszakértők és a Mesterségesintelligencia-fejlesztőknek, hogy gyorsan el:

+ Szabadkézi lemezképek ResNet-50 kvantált verziójával

+ Ezek a funkciók alapuló osztályozó eszközökkel betanítása

+ A modellek üzembe helyezése [programmable gate arrays (FPGA) mezőben](concept-accelerate-with-fpgas.md) ultramagas közel valós idejű következtetési az Azure-ban

## <a name="prerequisites"></a>Előfeltételek

1. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://aka.ms/AMLfree) a virtuális gép létrehozásának megkezdése előtt.

1. Egy Azure Machine Learning Modellkezelés-fiók. A fiók létrehozásával kapcsolatos további információkért lásd: a [Azure Machine Learning gyors üzembe helyezés és a Workbench telepítési](../desktop-workbench/quickstart-installation.md) dokumentumot. 

1. A csomag telepítve kell lennie. 

 
## <a name="how-to-install-the-package"></a>A csomag telepítése

1. Töltse le és telepítse a legújabb [Git](https://git-scm.com/downloads).

2. Telepítés [Anaconda (Python 3.6-os)](https://conda.io/miniconda.html)

   Egy előre konfigurált Anaconda-környezet letöltéséhez használja a következő parancsot a Git használatával:

    ```
    git clone https://aka.ms/aml-real-time-ai
    ```
1. A környezet létrehozásához nyissa meg egy **Anaconda Rákérdezés** és használja a következő parancsot:

    ```
    conda env create -f aml-real-time-ai/environment.yml
    ```

1. Az aktiválás a környezetben, használja a következő parancsot:

    ```
    conda activate amlrealtimeai
    ```

## <a name="sample-code"></a>Mintakód

A mintakód végigvezeti a modell üzembe helyezése FPGA-, az SDK-val.

1. A csomag importálása:
   ```python
   import amlrealtimeai
   from amlrealtimeai import resnet50
   ```

1. A lemezkép előzetes feldolgozása:
   ```python 
   from amlrealtimeai.resnet50.model import LocalQuantizedResNet50
   model_path = os.path.expanduser('~/models')
   model = LocalQuantizedResNet50(model_path)
   print(model.version)
   ```

1. Szabadkézi a lemezképek:
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

1. A szolgáltatásdefiníció létrehozása:
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
 
1. Készítse elő a modell egy FPGA futtathatók:
   ```python
   from amlrealtimeai import DeploymentClient

   subscription_id = "<Your Azure Subscription ID>"
   resource_group = "<Your Azure Resource Group Name>"
   model_management_account = "<Your AzureML Model Management Account Name>"

   model_name = "resnet50-model"
   service_name = "quickstart-service"

   deployment_client = DeploymentClient(subscription_id, resource_group, model_management_account)
   ```

1. Egy FPGA futtathatók a modell üzembe helyezése:
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

1. Az API meghívása:
   ```python
   image_file = R'C:\path_to_file\image.jpg'
   results = client.score_image(image_file)
   ```

## <a name="reporting-issues"></a>Jelentéskészítési problémái

Használja a [fórum](https://aka.ms/aml-forum-service) a csomag tapasztal, jelentse a problémákat.

## <a name="next-steps"></a>További lépések

[Modell üzembe helyezése FPGA-webszolgáltatásként](how-to-deploy-fpga-web-service.md)