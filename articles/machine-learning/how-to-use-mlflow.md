---
title: ML-folyamatkövetés pénzmosási kísérletekhez
titleSuffix: Azure Machine Learning
description: Állítsa be az ML-folyamatot az Azure Machine Learningdel a Databricks-fürtökben, a helyi környezetben vagy a virtuális gép környezetében létrehozott ml-modellek metrikáinak és összetevőinek naplózásához.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.topic: conceptual
ms.date: 02/03/2020
ms.custom: seodec18
ms.openlocfilehash: dce7db9fc508c70d79be62a7e97b3bf52a316b22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76983698"
---
# <a name="track-models-metrics-with-mlflow-and-azure-machine-learning-preview"></a>Modellek metrikáinak nyomon követése az MLflow-val és az Azure Machine Learningdel (előzetes verzió)

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ez a cikk bemutatja, hogyan engedélyezheti az MLflow nyomon követési URI- és naplózási API-ját, amely együttesen [MLflow-követés](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api)néven ismert az MLflow-kísérletek és az Azure Machine Learning csatlakoztatásához. Ezzel lehetővé teszi a kísérletmetrikák és összetevők nyomon követését és naplózását az [Azure Machine Learning-munkaterületen.](https://docs.microsoft.com/azure/machine-learning/concept-azure-machine-learning-architecture#workspaces) Ha már használja az MLflow Tracking-et a kísérletekhez, a munkaterület egy központosított, biztonságos és méretezhető helyet biztosít a betanítási metrikák és modellek tárolásához.

<!--
+ Deploy your MLflow experiments as an Azure Machine Learning web service. By deploying as a web service, you can apply the Azure Machine Learning monitoring and data drift detection functionalities to your production models. 
-->

[Az MLflow](https://www.mlflow.org) egy nyílt forráskódú könyvtár a gépi tanulási kísérletek életciklusának kezeléséhez. Az MLFlow-követés az ML-folyamat egyik összetevője, amely naplózza és nyomon követi a betanítási futtatási metrikákat és a modellösszetevőket, függetlenül a kísérlet környezetétől – helyileg a számítógépen, egy távoli számítási célon, egy virtuális gépen vagy egy Azure Databricks-fürtön. 

Az alábbi ábra azt mutatja be, hogy az ML-folyamatkövetés segítségével nyomon követheti a kísérlet futtatási metrikáit, és modellösszetevőket tárol az Azure Machine Learning-munkaterületen.

![mlflow azazure gépi tanulási diagrammal](./media/how-to-use-mlflow/mlflow-diagram-track.png)

> [!TIP]
> A jelen dokumentumban szereplő információk elsősorban adatszakértők és fejlesztők számára készült, akik a modell betanítási folyamatát szeretnék figyelni. Ha ön az Azure Machine Learning erőforrás-használatának és eseményeinek figyelése iránt érdeklődő rendszergazda, például kvóták, befejezett képzési futtatások vagy befejezett modelltelepítések, olvassa el [az Azure Machine Learning figyelése.](monitor-azure-machine-learning.md)

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>Az MLflow és az Azure Machine Learning-ügyfelek összehasonlítása

 Az alábbi táblázat összefoglalja az Azure Machine Learninget használó különböző ügyfeleket és a megfelelő funkcióképességeket.

 Az MLflow Tracking metrikus naplózást és műtermék-tárolási funkciókat kínál, amelyek egyébként csak az [Azure Machine Learning Python SDK-n](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)keresztül érhetők el.


| | ML-folyamatkövetés&nbsp; <!--& Deployment--> | Azure Machine Learning Python SDK |  Azure Machine Learning CLI | Azure Machine Learning Studio|
|---|---|---|---|---|
| Munkaterület kezelése |   | ✓ | ✓ | ✓ |
| Adattárak használata  |   | ✓ | ✓ | |
| Naplómutatók      | ✓ | ✓ |   | |
| Összetevők feltöltése | ✓ | ✓ |   | |
| Metrikák megtekintése     | ✓ | ✓ | ✓ | ✓ |
| Számítások kezelése   |   | ✓ | ✓ | ✓ |

<!--| Deploy models    | ✓ | ✓ | ✓ | ✓ |
|Monitor model performance||✓|  |   |
| Detect data drift |   | ✓ |   | ✓ |
-->
## <a name="prerequisites"></a>Előfeltételek

* [Telepítse az MLflow-t.](https://mlflow.org/docs/latest/quickstart.html)
* [Telepítse az Azure Machine Learning SDK-t](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) a helyi számítógépre Az SDK biztosítja a kapcsolatot az MLflow-hoz a munkaterület eléréséhez.
* [Hozzon létre egy Azure Machine Learning-munkaterületet.](how-to-manage-workspace.md)

## <a name="track-local-runs"></a>Helyi futások nyomon követése

Az MLflow-követés az Azure Machine Learning segítségével tárolhatja a helyi adatokból származó naplózott metrikákat és összetevőket az Azure Machine Learning-munkaterületre.

Telepítse `azureml-mlflow` a csomagot az MLflow Tracking és az Azure Machine Learning használatához a jupyter-jegyzetfüzetben vagy kódszerkesztőben helyileg futtatott kísérletekhez.

```shell
pip install azureml-mlflow
```

>[!NOTE]
>Az azureml.contrib névtér gyakran változik, miközben a szolgáltatás fejlesztésén dolgozunk. Mint ilyen, a névtérben lévő minden előnézetnek tekintendő, és a Microsoft nem támogatja teljes mértékben.

Importálja `mlflow` [`Workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) a és az osztályokat az MLflow nyomon követési URI-jának eléréséhez és a munkaterület konfigurálásához.

A következő kódban `get_mlflow_tracking_uri()` a metódus egyedi követési URI-címet `ws`rendel `set_tracking_uri()` a munkaterülethez, és az MLflow-követési URI-t erre a címre sorolja.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
>A követési URI legfeljebb egy óra vagy annál kevesebb érvényes. Ha némi tétlen idő után újraindítja a parancsfájlt, használja a get_mlflow_tracking_uri API-t egy új URI-be.

Állítsa be az MLflow-kísérlet `set_experiment()` nevét, `start_run()`és kezdje el a betanítást a segítségével. Ezután `log_metric()` aktiválja az MLflow naplózási API-t, és kezdje meg a betanítási futtatási mérőszámok naplózását.

```Python
experiment_name = 'experiment_with_mlflow'
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    mlflow.log_metric('alpha', 0.03)
```

## <a name="track-remote-runs"></a>Távoli futtatások nyomon követése

Az MLflow-követés az Azure Machine Learning segítségével tárolhatja a távoli adatokból származó naplózott metrikákat és összetevőket az Azure Machine Learning-munkaterületre.

A távoli futtatások lehetővé teszik a modellek betanítását a hatékonyabb számítási műveletekre, például a GPU-kompatibilis virtuális gépekre vagy a Machine Learning Compute-fürtökre. Tekintse [meg a számítási célok beállítása a modellbetanításhoz](how-to-set-up-training-targets.md) a különböző számítási lehetőségek megismeréséhez.

Konfigurálja a számítási és a [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) betanítási futtatási környezetet az osztállyal. A `mlflow` `azureml-mlflow` környezet [`CondaDependencies`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) szakaszában szerepeljen a pip csomagok és pip csomagok. Ezután [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) a távoli számítási célként a távoli számítással hozhat létre.

```Python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core import ScriptRunConfig

exp = Experiment(workspace = 'my_workspace',
                 name='my_experiment')

mlflow_env = Environment(name='mlflow-env')

cd = CondaDependencies.create(pip_packages=['mlflow', 'azureml-mlflow'])

mlflow_env.python.conda_dependencies = cd

src = ScriptRunConfig(source_directory='./my_script_location', script='my_training_script.py')

src.run_config.target = 'my-remote-compute-compute'
src.run_config.environment = mlflow_env
```

A betanítási `mlflow` parancsfájlban importálja az MLflow naplózási API-k at, és kezdje el naplózni a futtatási metrikákat.

```Python
import mlflow

with mlflow.start_run():
    mlflow.log_metric('example', 1.23)
```

Ezzel a számítási és betanítási `Experiment.submit('train.py')` futtatási konfigurációval a módszerrel küldjön be egy futtatást. Ez a módszer automatikusan beállítja az ML flow-követési URI-t, és az MLflow-ból a munkaterületre irányítja a naplózást.

```Python
run = exp.submit(src)
```

## <a name="track-azure-databricks-runs"></a>Az Azure Databricks futásának nyomon követése

Az MLflow-követés az Azure Machine Learning segítségével tárolhatja az Azure Databricks naplózott metrikáit és összetevőit az Azure Machine Learning-munkaterületen.

Az Azure Databricks-szel végzett mlflow-kísérletek futtatásához először létre kell hoznia egy [Azure Databricks-munkaterületet és fürtöt.](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) A fürtben győződjön meg arról, hogy telepítse az *azureml-mlflow-függvénytárat* a PyPi-ből, hogy a fürt hozzáférjen a szükséges függvényekhez és osztályokhoz.

Innen importálhatja a kísérleti jegyzetfüzetet, csatolhatja az Azure Databricks-fürthöz, és futtathatja a kísérletet. 

### <a name="install-libraries"></a>Tárak telepítése

Ha tárakat szeretne telepíteni a fürtre, keresse meg a **Könyvtárak** lapot, és kattintson az **Új telepítése gombra.**

 ![mlflow azazure gépi tanulási diagrammal](./media/how-to-use-mlflow/azure-databricks-cluster-libraries.png)

A **Csomag** mezőbe írja be az azureml-ml flow-t, majd kattintson a telepítés gombra. Ismételje meg ezt a lépést szükség szerint további csomagok telepítéséhez a fürtbe a kísérlethez.

 ![mlflow azazure gépi tanulási diagrammal](./media/how-to-use-mlflow/install-libraries.png)

### <a name="set-up-your-notebook-and-workspace"></a>Jegyzetfüzet és munkaterület beállítása

A fürt beállítása után importálja a kísérleti jegyzetfüzetet, nyissa meg, és csatolja hozzá a fürtöt.

A következő kódnak a kísérletjegyzetfüzetben kell lennie. Ez a kód leadja az Azure-előfizetés részleteit a munkaterület példányossá. Ez a kód feltételezi, hogy rendelkezik egy meglévő erőforráscsoporttal és az Azure Machine Learning-munkaterülettel, ellenkező esetben [létrehozhatja őket.](how-to-manage-workspace.md) 

```python
import mlflow
import mlflow.azureml
import azureml.mlflow
import azureml.core

from azureml.core import Workspace
from azureml.mlflow import get_portal_url

subscription_id = 'subscription_id'

# Azure Machine Learning resource group NOT the managed resource group
resource_group = 'resource_group_name' 

#Azure Machine Learning workspace name, NOT Azure Databricks workspace
workspace_name = 'workspace_name'  

# Instantiate Azure Machine Learning workspace
ws = Workspace.get(name=workspace_name,
                   subscription_id=subscription_id,
                   resource_group=resource_group)
```

#### <a name="connect-your-azure-databricks-and-azure-machine-learning-workspaces"></a>Az Azure Databricks és az Azure Machine Learning-munkaterületek összekapcsolása

Az [Azure Portalon](https://ms.portal.azure.com)összekapcsolhatja az Azure Databricks (ADB) munkaterületet egy új vagy meglévő Azure Machine Learning-munkaterülettel. Ehhez keresse meg az ADB-munkaterületet, és kattintson a **Link Azure Machine Learning munkaterület** gombra a jobb alsó sarokban. A munkaterületek összekapcsolása lehetővé teszi a kísérletadatok nyomon követését az Azure Machine Learning-munkaterületen. 

### <a name="link-mlflow-tracking-to-your-workspace"></a>Az ML-folyamat követés összekapcsolása a munkaterülethez

A munkaterület példányosítása után állítsa be az MLflow-követési URI-t. Ezzel összekapcsolhatja az ML-folyamat követést az Azure Machine Learning-munkaterülettel. Összekapcsolás után az összes kísérlet a felügyelt Azure Machine Learning-követési szolgáltatásban fog landolni.

#### <a name="directly-set-mlflow-tracking-in-your-notebook"></a>Az MLflow Tracking közvetlen beállítása a noteszgépben

```python
uri = ws.get_mlflow_tracking_uri()
mlflow.set_tracking_uri(uri)
```

A betanítási parancsfájlban importálja mlflow-az MLflow naplózási API-k at, és indítsa el a futtatási metrikák naplózását. A következő példa naplózza a korszak veszteség metrikáját. 

```python
import mlflow 
mlflow.log_metric('epoch_loss', loss.item()) 
```

#### <a name="automate-setting-mlflow-tracking"></a>Az ML flow tracking beállításának automatizálása

Ahelyett, hogy manuálisan állítana be a követési URI-t a fürtök minden további kísérleti jegyzetfüzet-munkamenetében, ezt automatikusan tegye meg ezzel az [Azure Machine Learning Tracking Cluster Init parancsfájllal.](https://github.com/Azure/MachineLearningNotebooks/blob/3ce779063b000e0670bdd1acc6bc3a4ee707ec13/how-to-use-azureml/azure-databricks/linking/README.md)

Ha megfelelően konfigurált, láthatja az MLflow-követési adatokat az Azure Machine Learning REST API-ban és az összes ügyfélben, valamint az Azure Databricks-ben az MLflow felhasználói felületen keresztül vagy az MLflow-ügyfél használatával.

## <a name="view-metrics-and-artifacts-in-your-workspace"></a>Metrikák és összetevők megtekintése a munkaterületen

Az ML flow naplózásmeszmei és összetevői a munkaterületen maradnak. Ha bármikor meg szeretné tekinteni őket, keresse meg a munkaterületet, és keresse meg név szerint a kísérletet a munkaterületen az [Azure Machine Learning stúdióban.](https://ml.azure.com)  Vagy futtassa az alábbi kódot. 

```python
run.get_metrics()
ws.get_details()
```

<!-- ## Deploy MLflow models as a web service

Deploying your MLflow experiments as an Azure Machine Learning web service allows you to leverage the Azure Machine Learning model management and data drift detection capabilities and apply them to your production models.

The following diagram demonstrates that with the MLflow deploy API you can deploy your existing MLflow models as an Azure Machine Learning web service, despite their frameworks--PyTorch, Tensorflow, scikit-learn, ONNX, etc., and manage your production models in your workspace.

![mlflow with azure machine learning diagram](./media/how-to-use-mlflow/mlflow-diagram-deploy.png)

### Log your model

Before you can deploy, be sure that your model is saved so you can reference it and its path location for deployment. In your training script, there should be code similar to the following [mlflow.sklearn.log_model()](https://www.mlflow.org/docs/latest/python_api/mlflow.sklearn.html) method, that saves your model to the specified outputs directory. 

```python
# change sklearn to pytorch, tensorflow, etc. based on your experiment's framework 
import mlflow.sklearn

# Save the model to the outputs directory for capture
mlflow.sklearn.log_model(regression_model, model_save_path)
```
>[!NOTE]
> Include the `conda_env` parameter to pass a dictionary representation of the dependencies and environment this model should be run in.

### Retrieve model from previous run

To retrieve the run, you need the run ID and the path in run history of where the model was saved. 

```python
# gets the list of runs for your experiment as an array
experiment_name = 'experiment-with-mlflow'
exp = ws.experiments[experiment_name]
runs = list(exp.get_runs())

# get the run ID and the path in run history
runid = runs[0].id
model_save_path = 'model'
```

### Create Docker image

The `mlflow.azureml.build_image()` function builds a Docker image from the saved model in a framework-aware manner. It automatically creates the framework-specific inferencing wrapper code and specifies package dependencies for you. Specify the model path, your workspace, run ID and other parameters.

The following code builds a docker image using *runs:/<run.id>/model* as the model_uri path for a Scikit-learn experiment.

```python
import mlflow.azureml

azure_image, azure_model = mlflow.azureml.build_image(model_uri='runs:/{}/{}'.format(runid, model_save_path),
                                                      workspace=ws,
                                                      model_name='sklearn-model',
                                                      image_name='sklearn-image',
                                                      synchronous=True)
```
The creation of the Docker image can take several minutes. 

### Deploy the Docker image 

After the image is created, use the Azure Machine Learning SDK to deploy the image as a web service.

First, specify the deployment configuration. Azure Container Instance (ACI) is a suitable choice for a quick dev-test deployment, while Azure Kubernetes Service (AKS) is suitable for scalable production deployments.

#### Deploy to ACI

Set up your deployment configuration with the [deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) method. You can also add tags and descriptions to help keep track of your web service.

```python
from azureml.core.webservice import AciWebservice, Webservice

# Configure 
aci_config = AciWebservice.deploy_configuration(cpu_cores=1, 
                                                memory_gb=1, 
                                                tags={'method' : 'sklearn'}, 
                                                description='Diabetes model',
                                                location='eastus2')
```

Then, deploy the image by using the Azure Machine Learning SDK [deploy_from_image()](/python/api/azureml-core/azureml.core.webservice.webservice(class)?view=azure-ml-py#deploy-from-image-workspace--name--image--deployment-config-none--deployment-target-none--overwrite-false-) method. 

```python
webservice = Webservice.deploy_from_image( image=azure_image, 
                                           workspace=ws, 
                                           name='diabetes-model-1', 
                                           deployment_config=aci_config)

webservice.wait_for_deployment(show_output=True)
```
#### Deploy to AKS

To deploy to AKS, first create an AKS cluster and bring over the Docker image you want to deploy. For this example, bring over the previously created image from the ACI deployment.

To get the image from the previous ACI deployment use the [Image](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.image.image?view=azure-ml-py) class. 

```python
from azureml.core.image import Image

# Get the image by name, you can change this based on the image you want to deploy
myimage = Image(workspace=ws, name='sklearn-image') 
```

Create an AKS cluster using the [ComputeTarget.create()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py#create-workspace--name--provisioning-configuration-) method. It may take 20-25 minutes to create a new cluster.

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
Set up your deployment configuration with the [deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) method. You can also add tags and descriptions to help keep track of your web service.

```python
from azureml.core.webservice import Webservice, AksWebservice
from azureml.core.image import ContainerImage

# Set the web service configuration (using default here with app insights)
aks_config = AksWebservice.deploy_configuration(enable_app_insights=True)

# Unique service name
service_name ='aks-service'
```

Then, deploy the image by using the Azure Machine Learning SDK [deploy_from_image()](/python/api/azureml-core/azureml.core.webservice.webservice(class)?view=azure-ml-py#deploy-from-image-workspace--name--image--deployment-config-none--deployment-target-none--overwrite-false-) method. 

```python
# Webservice creation using single command
aks_service = Webservice.deploy_from_image( workspace=ws, 
                                            name=service_name,
                                            deployment_config = aks_config
                                            image = myimage,
                                            deployment_target = aks_target)

aks_service.wait_for_deployment(show_output=True)
```

The service deployment can take several minutes.

## Clean up resources

If you don't plan to use the logged metrics and artifacts in your workspace, the ability to delete them individually is currently unavailable. Instead, delete the resource group that contains the storage account and workspace, so you don't incur any charges:

1. In the Azure portal, select **Resource groups** on the far left.

   ![Delete in the Azure portal](./media/how-to-use-mlflow/delete-resources.png)

1. From the list, select the resource group you created.

1. Select **Delete resource group**.

1. Enter the resource group name. Then select **Delete**.

 -->

 ## <a name="example-notebooks"></a>Példa jegyzetfüzetekre

Az [MLflow az Azure ML-jegyzetfüzetek](https://aka.ms/azureml-mlflow-examples) bemutatják, és bontsa ki a jelen cikkben bemutatott fogalmakat.

## <a name="next-steps"></a>További lépések
* [A modellek kezelése](concept-model-management-and-deployment.md).
* Figyelje a termelési modellek et az [adatok eltolódására.](how-to-monitor-data-drift.md)
