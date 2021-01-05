---
title: ML-kísérletek üzembe helyezése a MLflow
titleSuffix: Azure Machine Learning
description: Állítsa be az MLflow-t a Azure Machine Learning segítségével, és telepítse az ML-modelleket webszolgáltatásként.
services: machine-learning
author: shivp950
ms.author: shipatel
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.date: 12/23/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 1159a6cfda6b877f04573c85fa437ce3bff81af1
ms.sourcegitcommit: 6cca6698e98e61c1eea2afea681442bd306487a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/24/2020
ms.locfileid: "97761744"
---
# <a name="deploy-mlflow-models-with-azure-machine-learning-preview"></a>MLflow-modellek üzembe helyezése Azure Machine Learningsal (előzetes verzió)

Ebből a cikkből megtudhatja, hogyan helyezheti üzembe a MLflow-modellt Azure Machine Learning webszolgáltatásként, így kihasználhatja és alkalmazhatja Azure Machine Learning modell-kezelési és adateltolódás-észlelési képességeit az éles modellekben.

Azure Machine Learning a következő telepítési konfigurációkat kínálja:
* Az Azure Container instance (ACI), amely egy megfelelő választás egy gyors fejlesztési-tesztelési üzembe helyezéshez.
* Az Azure Kubernetes szolgáltatás (ak), amely méretezhető éles környezetekben való üzembe helyezéshez ajánlott.

A [MLflow](https://www.mlflow.org) egy nyílt forráskódú kódtár a gépi tanulási kísérletek életciklusának kezeléséhez. A Azure Machine Learning való integrációja lehetővé teszi, hogy ezt a kezelést a modell betanítási fázisán túlmenően az éles modell üzembe helyezési fázisában is kiterjessze.

>[!NOTE]
> A nyílt forráskódú kódtár MLflow gyakran változnak. Ennek megfelelően a Azure Machine Learning és a MLflow-integráción keresztül elérhető funkciókat előzetes verziónak kell tekinteni, és a Microsoft nem támogatja teljes mértékben.

A következő ábra azt mutatja be, hogy a MLflow üzembe helyezési API-val a meglévő MLflow-modelljét üzembe helyezheti Azure Machine Learning webszolgáltatásként, a keretrendszerük, a PyTorch, a Tensorflow, a scikit-Learn, a ONNX stb., valamint az üzemi modellek a munkaterületen való kezeléséhez.

![ mlflow-modellek üzembe helyezése az Azure Machine learning szolgáltatással](./media/how-to-use-mlflow/mlflow-diagram-deploy.png)

> [!TIP]
> Az ebben a dokumentumban szereplő információk elsősorban olyan adatszakértők és fejlesztők számára készültek, akik Azure Machine Learning webszolgáltatási végpontra szeretnék telepíteni a MLflow-modellt. Ha Ön olyan rendszergazda, aki a Azure Machine Learning erőforrás-használat és-események figyelését érdekli (például kvóták, befejezett betanítási futtatások vagy befejezett modellek üzembe helyezése), tekintse meg a [figyelés Azure Machine learning](monitor-azure-machine-learning.md).

## <a name="prerequisites"></a>Előfeltételek

* [Állítsa be a MLflow követési URI-t a Azure Machine learning összekapcsolásához](how-to-use-mlflow.md).
* Telepítse az `azureml-mlflow` csomagot. 
    * Ez a csomag automatikusan bevezeti a `azureml-core` [Azure Machine learning Python SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py)-t, amely biztosítja a kapcsolatot a MLflow a munkaterület eléréséhez.

## <a name="deploy-to-aci"></a>Üzembe helyezés az ACI-ban

Ha a MLflow modellt egy Azure Machine Learning webszolgáltatásba szeretné telepíteni, a modellt a [MLflow követési URI-val kell beállítani a Azure Machine Learninghoz való kapcsolódáshoz](how-to-use-mlflow.md). 

Állítsa be a telepítési konfigurációt a [deploy_configuration ()](/python/api/azureml-core/azureml.core.webservice.aciwebservice?preserve-view=true&view=azure-ml-py#&preserve-view=truedeploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) metódussal. A webszolgáltatások nyomon követéséhez címkéket és leírásokat is hozzáadhat.

```python
from azureml.core.webservice import AciWebservice, Webservice

# Set the model path to the model folder created by your run
model_path = "model"

# Configure 
aci_config = AciWebservice.deploy_configuration(cpu_cores=1, 
                                                memory_gb=1, 
                                                tags={'method' : 'sklearn'}, 
                                                description='Diabetes model',
                                                location='eastus2')
```

Ezután regisztrálja és telepítse a modellt egy lépésben a MLflow [üzembe helyezési](https://www.mlflow.org/docs/latest/python_api/mlflow.azureml.html#mlflow.azureml.deploy) módszerével a Azure Machine Learninghoz. 

```python
(webservice,model) = mlflow.azureml.deploy( model_uri='runs:/{}/{}'.format(run.id, model_path),
                      workspace=ws,
                      model_name='sklearn-model', 
                      service_name='diabetes-model-1', 
                      deployment_config=aci_config, 
                      tags=None, mlflow_home=None, synchronous=True)

webservice.wait_for_deployment(show_output=True)
```

## <a name="deploy-to-aks"></a>Üzembe helyezés az AKS-ben

Ha a MLflow modellt egy Azure Machine Learning webszolgáltatásba szeretné telepíteni, a modellt a [MLflow követési URI-val kell beállítani a Azure Machine Learninghoz való kapcsolódáshoz](how-to-use-mlflow.md). 

Az AK-ra való üzembe helyezéshez először hozzon létre egy AK-fürtöt. Hozzon létre egy AK-fürtöt a [ComputeTarget. Create ()](/python/api/azureml-core/azureml.core.computetarget?preserve-view=true&view=azure-ml-py#&preserve-view=truecreate-workspace--name--provisioning-configuration-) metódus használatával. Egy új fürt létrehozása 20-25 percet is igénybe vehet.

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (can also provide parameters to customize)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'aks-mlflow'

# Create the cluster
aks_target = ComputeTarget.create(workspace=ws, 
                                  name=aks_name, 
                                  provisioning_configuration=prov_config)

aks_target.wait_for_completion(show_output = True)

print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```
Állítsa be a telepítési konfigurációt a [deploy_configuration ()](/python/api/azureml-core/azureml.core.webservice.aciwebservice?preserve-view=true&view=azure-ml-py#&preserve-view=truedeploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) metódussal. A webszolgáltatások nyomon követéséhez címkéket és leírásokat is hozzáadhat.

```python
from azureml.core.webservice import Webservice, AksWebservice

# Set the web service configuration (using default here with app insights)
aks_config = AksWebservice.deploy_configuration(enable_app_insights=True, compute_target_name='aks-mlflow')

```

Ezután regisztrálja és telepítse a modellt egy lépésben a MLflow [üzembe helyezési](https://www.mlflow.org/docs/latest/python_api/mlflow.azureml.html#mlflow.azureml.deploy) módszerével a Azure Machine Learninghoz. 

```python

# Webservice creation using single command
from azureml.core.webservice import AksWebservice, Webservice

# set the model path 
model_path = "model"

(webservice, model) = mlflow.azureml.deploy( model_uri='runs:/{}/{}'.format(run.id, model_path),
                      workspace=ws,
                      model_name='sklearn-model', 
                      service_name='my-aks', 
                      deployment_config=aks_config, 
                      tags=None, mlflow_home=None, synchronous=True)


webservice.wait_for_deployment()
```

A szolgáltatás üzembe helyezése több percet is igénybe vehet.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem tervezi az üzembe helyezett webszolgáltatás használatát, `service.delete()` törölje azt a jegyzetfüzetből.  További információ: a [webszolgáltatások dokumentációja. Delete ()](/python/api/azureml-core/azureml.core.webservice%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truedelete--).

## <a name="example-notebooks"></a>Példajegyzetfüzetek

Az [Azure ml jegyzetfüzetekkel rendelkező MLflow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/track-and-monitor-experiments/using-mlflow) bemutatják és kibővítik a jelen cikkben ismertetett fogalmakat.

> [!NOTE]
> A mlflow-t használó példák Közösség által vezérelt tárháza a következő címen érhető el: https://github.com/Azure/azureml-examples .

## <a name="next-steps"></a>További lépések

* [A modellek kezelése](concept-model-management-and-deployment.md).
* Figyelje az [adateltolódáshoz](./how-to-enable-data-collection.md)használt üzemi modelleket.
* [Nyomon követheti Azure Databricks futtatását a MLflow](how-to-use-mlflow-azure-databricks.md).
