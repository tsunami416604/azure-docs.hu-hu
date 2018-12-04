---
title: Modellek Azure Machine Learning szolgáltatás a Kubernetes üzembe helyezése |} A Microsoft Docs
description: Útmutató a modell üzembe helyezése az Azure Machine Learning szolgáltatásból származó Azure Kubernetes Service-ben. A modell üzembe lett helyezve web szolgáltatásként. Az Azure Kubernetes Service jó a nagy léptékű termelési számítási feladatokhoz.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: raymondl
author: raymondlaghaeian
manager: cgronlun
ms.reviewer: larryfr
ms.date: 09/24/2018
ms.openlocfilehash: 5db0efd825a655143828e7ccea73704516893ea2
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52845263"
---
# <a name="how-to-deploy-models-from-azure-machine-learning-service-to-azure-kubernetes-service"></a>Az Azure Kubernetes Service az Azure Machine Learning szolgáltatásból származó modellek üzembe helyezése

Nagy léptékű termelési forgatókönyvekhez helyezheti üzembe a modellt az Azure Kubernetes Service (AKS). Az Azure Machine Learning a meglévő AKS-fürt vagy az üzembe helyezés során létrehoztunk egy új fürtöt. Kérje meg a modellt webszolgáltatásként van telepítve.

Az aks üzembe helyezéséhez biztosít, automatikus méretezést, naplózás, a modelladatok gyűjtésének és a webes szolgáltatás gyors válaszidők.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Ha még nincs előfizetése, hozzon létre egy [ingyenes fiókot](https://aka.ms/AMLfree), mielőtt hozzákezd.

- Egy Azure Machine Learning szolgáltatás munkaterületén, a parancsprogramok, és az Azure Machine Learning SDK-t tartalmazó Python telepítve van egy helyi könyvtárba. Ezekről az előfeltételekről használatával beszerzéséről a [a fejlesztési környezet konfigurálása](how-to-configure-environment.md) dokumentumot.

- Egy betanított gépi tanulási modellt. Ha még nincs fiókja, tekintse meg a [kép osztályozási modell betanításához](tutorial-train-models-with-aml.md) oktatóanyag.

## <a name="initialize-the-workspace"></a>A munkaterület inicializálása

A munkaterület inicializálása, betöltése a `config.json` a munkaterület adatait tartalmazó fájlt.

```python
from azureml.coreazureml import Workspace

# Load existing workspace from the config file info.
ws = Workspace.from_config()
```

## <a name="register-the-model"></a>Regisztrálja a modellt

Egy meglévő modell regisztrálásához adja meg a modell elérési útja, leírás és címkék.

```python
from azureml.core.model import Model

model = Model.register(model_path = "model.pkl", # this points to a local file
                        model_name = "best_model", # this is the name the model is registered as
                        tags = {"data": "diabetes", "type": "regression"},
                        description = "Ridge regression model to predict diabetes",
                        workspace = ws)
```

## <a name="create-a-docker-image"></a>Hozzon létre egy Docker-rendszerképet.

Az Azure Kubernetes Service a Docker-rendszerképek használ. A rendszerkép létrehozásához használja az alábbi lépéseket:

1. Adja meg a kép, létre kell hoznia egy pontozó szkript és a környezet fájlt. Példaként a parancsprogram és a környezet fájl létrehozása tekintse meg a következő szakaszok a lemezkép besorolási példa:

    * [Hozzon létre egy pontozó szkriptre (score.py)](tutorial-deploy-models-with-aml.md#create-scoring-script)

        > [!IMPORTANT]
        > A pontozó szkript az ügyfeleknek elküldött adatokat fogad, és átadja a modell pontozása. A dokumentum a adatszerkezet, amely a parancsfájlt és a modell várható. Ez a dokumentáció kellene, azzal megkönnyítheti létrehozását, hogy az ügyfél használata a web service.

    * [Hozzon létre egy környezetben fájlt (myenv.yml)](tutorial-deploy-models-with-aml.md#create-environment-file) 

   Az alábbi példában ezeket a fájlokat a kép konfigurálása:

    ```python
    from azureml.core.image import ContainerImage

    # Image configuration
    image_config = ContainerImage.image_configuration(execution_script = "score.py",
                                                        runtime = "python",
                                                        conda_file = "myenv.yml",
                                                        description = "Image with ridge regression model",
                                                        tags = {"data": "diabetes", "type": "regression"}
                                                        )
    ```

1. A rendszerkép létrehozásához használja a modell- és képfájlok konfigurációt. A művelet eltarthat körülbelül 5 perc végrehajtásához:

    ```python
    image = ContainerImage.create(name = "myimage1",
                                    # this is the model object
                                    models = [model],
                                    image_config = image_config,
                                    workspace = ws)

    # Wait for the create process to complete
    image.wait_for_creation(show_output = True)
    ```

## <a name="create-the-aks-cluster"></a>Az AKS-fürt létrehozása

A következő kódrészletet bemutatja, hogyan hozhat létre az AKS-fürtöt. Ez a folyamat körülbelül 20 percet vesz igénybe befejezéséhez:

> [!IMPORTANT]
> Az AKS-fürtöt hoz létre a rendszer egy folyamatot, amikor a munkaterülethez. Létrehozása után újból felhasználhatja a fürt több telepítéshez. Ha törli a fürtöt vagy az azt tartalmazó erőforráscsoportot, majd kell létrehoznia egy új fürtöt, üzembe kell helyeznie legközelebb.


```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'aml-aks-1' 
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws, 
                                    name = aks_name, 
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```

### <a name="attach-existing-aks-cluster-optional"></a>Csatolása meglévő AKS fürt (nem kötelező)

Ha rendelkezik meglévő AKS-fürtöt az Azure-előfizetésében, használhatja a rendszerképének üzembe helyezéséhez. A következő kódrészletet bemutatja, hogyan csatlakoztathat egy fürtöt a munkaterülethez mutatja be. 

> [!IMPORTANT]
> Csak az AKS 1.11.3 verziója támogatott.

```python
# Get the resource id from https://porta..azure.com -> Find your resource group -> click on the Kubernetes service -> Properties
resource_id = '/subscriptions/<your subscription id>/resourcegroups/<your resource group>/providers/Microsoft.ContainerService/managedClusters/<your aks service name>'

# Set to the name of the cluster
cluster_name='my-existing-aks' 

# Attatch the cluster to your workgroup
aks_target = AksCompute.attach(workspace=ws, name=cluster_name, resource_id=resource_id)

# Wait for the operation to complete
aks_target.wait_for_completion(True)
```

## <a name="deploy-your-web-service"></a>A web Service szolgáltatásának telepítése

A következő kódrészletet bemutatja, hogyan lehet a rendszerkép az AKS-fürt üzembe helyezése:

```python
from azureml.core.webservice import Webservice, AksWebservice

# Set configuration and service name
aks_config = AksWebservice.deploy_configuration()
aks_service_name ='aks-service-1'
# Deploy from image
aks_service = Webservice.deploy_from_image(workspace = ws, 
                                            name = aks_service_name,
                                            image = image,
                                            deployment_config = aks_config,
                                            deployment_target = aks_target)
# Wait for the deployment to complete
aks_service.wait_for_deployment(show_output = True)
print(aks_service.state)
```

> [!TIP]
> Ha üzembe helyezés során hibák, használja `aks_service.get_logs()` az AKS szolgáltatás a naplók megtekintéséhez. A naplózott információk jelezheti, hogy a hiba okát.

## <a name="test-the-web-service"></a>A webszolgáltatás teszteléséhez

Használat `aks_service.run()` a webszolgáltatás teszteléséhez. Az alábbi kódrészlet adatokat adnak át a szolgáltatást, és megjeleníti az előrejelzést mutatja be:

```python
import json

test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10], 
    [10,9,8,7,6,5,4,3,2,1]
]})
test_sample = bytes(test_sample,encoding = 'utf8')

prediction = aks_service.run(input_data = test_sample)
print(prediction)
```

## <a name="update-the-web-service"></a>A web service frissítése

A web service frissítéséhez használja a `update` metódust. A következő kód bemutatja, hogyan frissíthető egy új rendszerkép használata a web service:

```python
from azureml.core.webservice import Webservice

service_name = 'aci-mnist-3'
# Retrieve existing service
service = Webservice(name = service_name, workspace = ws)
# Update the image used by the service
service.update(image = new-image)
print(service.state)
```

> [!NOTE]
> Amikor frissít egy képet, a webszolgáltatás nem frissül automatikusan. Minden egyes szolgáltatás, amely az új lemezképet használni kívánt manuálisan kell frissítenie.

## <a name="cleanup"></a>Felesleges tartalmak törlése

A szolgáltatás, a lemezkép és a modell törléséhez használja a következő kódrészletet:

```python
aks_service.delete()
image.delete()
model.delete()
```

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [felhasználás a gépi Tanulási modellek webszolgáltatásként üzembe helyezett](how-to-consume-web-service.md).