---
title: MLflow követése ML-kísérleteknél
titleSuffix: Azure Machine Learning
description: A Databricks-fürtökben, a helyi környezetekben vagy a virtuálisgép-környezetekben létrehozott ML-modellek MLflow és összetevőinek naplózása Azure Machine Learning használatával.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.topic: conceptual
ms.date: 01/27/2020
ms.custom: seodec18
ms.openlocfilehash: a1263ecacc2af0559c726fb12c799d0e6d2f1014
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76543341"
---
# <a name="track-models-metrics-with-mlflow-and-azure-machine-learning-preview"></a>Modellek metrikáinak nyomon követése a MLflow és a Azure Machine Learning (előzetes verzió)

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ez a cikk bemutatja, hogyan engedélyezheti a MLflow követési URI-és naplózási API-JÁT, a [MLflow-követést](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api)együttesen, a MLflow-kísérletek és a Azure Machine learning összekapcsolásához. Így nyomon követheti és naplózhatja a kísérlet mérőszámait és összetevőit a [Azure Machine learning munkaterületen](https://docs.microsoft.com/azure/machine-learning/concept-azure-machine-learning-architecture#workspaces). Ha már használja a kísérletek MLflow követését, a munkaterület központosított, biztonságos és skálázható helyet biztosít a képzési mérőszámok és modellek tárolásához.

<!--
+ Deploy your MLflow experiments as an Azure Machine Learning web service. By deploying as a web service, you can apply the Azure Machine Learning monitoring and data drift detection functionalities to your production models. 
-->

A [MLflow](https://www.mlflow.org) egy nyílt forráskódú kódtár a gépi tanulási kísérletek életciklusának kezeléséhez. A MLFlow Tracking a MLflow egy olyan összetevője, amely naplózza és nyomon követi a képzési műveletek mérőszámait és modelljeit, függetlenül a kísérlet környezetében – helyileg a számítógépén, egy távoli számítási célponton, egy virtuális gépen vagy egy Azure Databricks fürtön. 

Az alábbi ábra azt szemlélteti, hogy a MLflow nyomon követésével nyomon követheti a kísérlet futtatási metrikáit, és az Azure Machine Learning munkaterületen tárolhatja a modell összetevőit.

![mlflow az Azure Machine learning-diagrammal](./media/how-to-use-mlflow/mlflow-diagram-track.png)

> [!TIP]
> A jelen dokumentumban található információk elsősorban olyan adatszakértők és fejlesztők számára készültek, akik figyelni szeretnék a modell betanítási folyamatát. Ha Ön olyan rendszergazda, aki a Azure Machine Learning erőforrás-használat és-események figyelését érdekli (például kvóták, befejezett betanítási futtatások vagy befejezett modellek üzembe helyezése), tekintse meg a [figyelés Azure Machine learning](monitor-azure-machine-learning.md).

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>MLflow és Azure Machine Learning ügyfelek összehasonlítása

 Az alábbi táblázat összefoglalja azokat a különböző ügyfeleket, amelyek használhatják a Azure Machine Learningt, valamint a hozzájuk tartozó funkciókra vonatkozó képességeiket.

 A MLflow követése olyan metrikai naplózási és összetevő-tárolási funkciókat kínál, amelyek csak a [Azure Machine learning PYTHON SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)-n keresztül érhetők el.


| | MLflow&nbsp;követése <!--& Deployment--> | Python SDK Azure Machine Learning |  Azure Machine Learning parancssori felület | Azure Machine Learning Studio|
|---|---|---|---|---|
| Munkaterület kezelése |   | ✓ | ✓ | ✓ |
| Adattárak használata  |   | ✓ | ✓ | |
| Naplózási metrikák      | ✓ | ✓ |   | |
| Összetevők feltöltése | ✓ | ✓ |   | |
| Metrikák megtekintése     | ✓ | ✓ | ✓ | ✓ |
| Számítások kezelése   |   | ✓ | ✓ | ✓ |

<!--| Deploy models    | ✓ | ✓ | ✓ | ✓ |
|Monitor model performance||✓|  |   |
| Detect data drift |   | ✓ |   | ✓ |
-->
## <a name="prerequisites"></a>Előfeltételek

* [Telepítse a MLflow.](https://mlflow.org/docs/latest/quickstart.html)
* [Telepítse a Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) -t a helyi számítógépre, az SDK biztosítja a kapcsolatot a MLflow a munkaterület eléréséhez.
* [Hozzon létre egy Azure Machine learning-munkaterület](how-to-manage-workspace.md).

## <a name="track-local-runs"></a>Helyi futtatások nyomon követése

A MLflow követése Azure Machine Learning lehetővé teszi a naplózott metrikák és összetevők tárolását a helyi futtatásokból a Azure Machine Learning-munkaterületre.

Telepítse a `azureml-mlflow` csomagot a MLflow-követés használatára a kísérletekben helyileg futtatott Azure Machine Learning a Jupyter Notebook vagy a Kódszerkesztő használatával.

```shell
pip install azureml-mlflow
```

>[!NOTE]
>A azureml. a névterek gyakran változnak, ahogy dolgozunk a szolgáltatás tökéletesítésén. Ennek megfelelően az ebben a névtérben található bármit előzetes verziónak kell tekinteni, és a Microsoft nem támogatja teljes mértékben.

Importálja a `mlflow` és [`Workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) osztályokat a MLflow követési URI-ja eléréséhez és a munkaterület konfigurálásához.

A következő kódban a `get_mlflow_tracking_uri()` metódus egyedi nyomkövetési URI-címeket rendel hozzá a munkaterülethez, `ws`és `set_tracking_uri()` a MLflow követési URI-t az adott címhez.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
>A nyomkövetési URI-azonosító legfeljebb egy órán belül érvényes. Ha némi üresjárati idő után újraindítja a szkriptet, a get_mlflow_tracking_uri API-val új URI-t kap.

Állítsa be a MLflow-kísérlet nevét `set_experiment()` és indítsa el a betanítást a `start_run()`. Ezután a `log_metric()` használatával aktiválja a MLflow naplózási API-t, és megkezdheti a betanítási futtatási metrikák naplózását.

```Python
experiment_name = 'experiment_with_mlflow'
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    mlflow.log_metric('alpha', 0.03)
```

## <a name="track-remote-runs"></a>Távoli futtatások nyomon követése

A MLflow követése Azure Machine Learning lehetővé teszi a naplózott metrikák és összetevők tárolását a távoli futtatásokból a Azure Machine Learning-munkaterületre.

A távoli futtatások lehetővé teszik, hogy a modelleket nagyobb teljesítményű számításokra, például GPU-t használó virtuális gépekre vagy Machine Learning Compute fürtökre tanítsa. A különböző számítási lehetőségek megismeréséhez lásd: [számítási célok beállítása a modell betanításához](how-to-set-up-training-targets.md) .

Konfigurálja a számítási és képzési környezetét a [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) osztállyal. `mlflow` és `azure-contrib-run` pip-csomagok belefoglalása a környezeti [`CondaDependencies`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) szakaszba. Ezután hozza létre [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) a távoli számítási célként.

```Python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core import ScriptRunConfig

exp = Experiment(workspace = 'my_workspace',
                 name='my_experiment')

mlflow_env = Environment(name='mlflow-env')

cd = CondaDependencies.create(pip_packages=['mlflow', 'azureml-contrib-run'])

mlflow_env.python.conda_dependencies = cd

src = ScriptRunConfig(source_directory='./my_script_location', script='my_training_script.py')

src.run_config.target = 'my-remote-compute-compute'
src.run_config.environment = mlflow_env
```

A betanítási szkriptben importálja `mlflow` a MLflow-naplózási API-k használatára, és kezdje meg a futtatási metrikák naplózását.

```Python
import mlflow

with mlflow.start_run():
    mlflow.log_metric('example', 1.23)
```

Ezzel a számítási és betanítási futtatási konfigurációval a `Experiment.submit('train.py')` metódus használatával küldhet el egy futtatást. Ez a metódus automatikusan beállítja a MLflow követési URI-t, és a naplózást a MLflow a munkaterületre irányítja.

```Python
run = exp.submit(src)
```

## <a name="track-azure-databricks-runs"></a>Azure Databricks futtatások nyomon követése

A MLflow követése Azure Machine Learning lehetővé teszi a naplózott metrikák és összetevők tárolását a Azure Databricks a Azure Machine Learning munkaterületen.

Ha a Mlflow-kísérleteket Azure Databricks segítségével szeretné futtatni, először létre kell hoznia egy [Azure Databricks-munkaterületet és-fürtöt](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal). A fürtben ügyeljen arra, hogy a *azureml-mlflow* függvénytárat a PyPi-ből telepítse, hogy a fürt hozzáférhessen a szükséges függvényekhez és osztályokhoz.

Innen importálja a kísérleti jegyzetfüzetet, csatolja a Azure Databricks-fürthöz, és futtassa a kísérletet. 

### <a name="install-libraries"></a>Tárak telepítése

Ha a fürtön szeretné telepíteni a kódtárakat, navigáljon a **tárak** lapra, és kattintson az **új telepítése** parancsra.

 ![mlflow az Azure Machine learning-diagrammal](./media/how-to-use-mlflow/azure-databricks-cluster-libraries.png)

A **csomag** mezőbe írja be a következőt: azureml-mlflow, majd kattintson a telepítés gombra. Szükség szerint ismételje meg ezt a lépést, hogy a kísérlethez további csomagokat telepítsen a fürtön.

 ![mlflow az Azure Machine learning-diagrammal](./media/how-to-use-mlflow/install-libraries.png)

### <a name="set-up-your-notebook-and-workspace"></a>A jegyzetfüzet és a munkaterület beállítása

A fürt beállítása után importálja a kísérleti jegyzetfüzetet, nyissa meg, és csatolja hozzá a fürtöt.

A következő kódnak a kísérleti jegyzetfüzetben kell szerepelnie. Ez a kód beolvassa az Azure-előfizetésének részleteit a munkaterület létrehozásához. Ez a kód feltételezi, hogy rendelkezik egy meglévő erőforráscsoporthoz és Azure Machine Learning munkaterülettel, különben [létrehozhatja őket](how-to-manage-workspace.md). 

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

#### <a name="connect-your-azure-databricks-and-azure-machine-learning-workspaces"></a>Azure Databricks és Azure Machine Learning munkaterületek összekötése

A [Azure Portal](https://ms.portal.azure.com)a Azure DATABRICKS (ADB) munkaterületet egy új vagy egy meglévő Azure Machine learning munkaterülethez csatolhatja. Ehhez navigáljon az ADB munkaterületére, és válassza a jobb alsó sarokban található **Azure Machine learning munkaterület csatolása** gombot. A munkaterületek összekapcsolása lehetővé teszi, hogy nyomon kövesse a kísérlet adatait a Azure Machine Learning munkaterületen. 

### <a name="link-mlflow-tracking-to-your-workspace"></a>A MLflow követésének összekapcsolása a munkaterülettel

Miután létrehozta a munkaterületet, állítsa be a MLflow követési URI-t. Ezzel összekapcsolja a MLflow nyomon követését Azure Machine Learning munkaterületre. A csatolást követően minden kísérlet a felügyelt Azure Machine Learning követési szolgáltatásban fog megjelenni.

#### <a name="directly-set-mlflow-tracking-in-your-notebook"></a>Közvetlenül a MLflow-követés beállítása a jegyzetfüzetben

```python
uri = ws.get_mlflow_tracking_uri()
mlflow.set_tracking_uri(uri)
```

A betanítási szkriptben importálja a mlflow-t a MLflow-naplózási API-k használatára, és kezdje meg a futtatási metrikák naplózását. A következő példa naplózza az alapkorszak elvesztésének mérőszámát. 

```python
import mlflow 
mlflow.log_metric('epoch_loss', loss.item()) 
```

#### <a name="automate-setting-mlflow-tracking"></a>MLflow-követési beállítások automatizálása

Ahelyett, hogy manuálisan állítja be a nyomkövetési URI-t a fürtökön lévő minden további kísérlet jegyzetfüzet-munkamenetben, ezt a [Azure Machine learning a fürt inicializálási parancsfájljának](https://github.com/Azure/MachineLearningNotebooks/blob/3ce779063b000e0670bdd1acc6bc3a4ee707ec13/how-to-use-azureml/azure-databricks/linking/README.md)használatával automatikusan végrehajtja.

Ha megfelelően van konfigurálva, a MLflow-követési adatait megtekintheti a Azure Machine Learning REST API és az összes ügyfelet, illetve Azure Databricks a MLflow felhasználói felületén vagy a MLflow-ügyfél használatával.

## <a name="view-metrics-and-artifacts-in-your-workspace"></a>Metrikák és összetevők megtekintése a munkaterületen

A MLflow-naplózás metrikái és összetevői a munkaterületen maradnak. Ha bármikor meg szeretné tekinteni őket, keresse meg a munkaterületet, és keresse meg a kísérletet név szerint a munkaterületen a [Azure Machine learning Studióban](https://ml.azure.com).  Vagy futtassa az alábbi kódot. 

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

 ## <a name="example-notebooks"></a>Jegyzetfüzetek – példa

Az [Azure ml jegyzetfüzetekkel rendelkező MLflow](https://aka.ms/azureml-mlflow-examples) bemutatják és kibővítik a jelen cikkben ismertetett fogalmakat.

## <a name="next-steps"></a>Következő lépések
* [A modellek kezelése](concept-model-management-and-deployment.md).
* Figyelje az [adateltolódáshoz](how-to-monitor-data-drift.md)használt üzemi modelleket.
