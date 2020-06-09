---
title: MLflow követése ML-kísérleteknél
titleSuffix: Azure Machine Learning
description: Állítsa be a MLflow-t a Azure Machine Learning használatával a mérőszámok és az összetevők a ML modellekből való naplózásához, és telepítse az ML-modelleket webszolgáltatásként.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.topic: how-to
ms.date: 06/04/2020
ms.custom: tracking-python
ms.openlocfilehash: 5d6e4bfb3c99d2fb570fea7aaebc7150088f1036
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/09/2020
ms.locfileid: "84559376"
---
# <a name="track-model-metrics-and-deploy-ml-models-with-mlflow-and-azure-machine-learning-preview"></a>Modell metrikáinak nyomon követése és ML modellek üzembe helyezése a MLflow és a Azure Machine Learning (előzetes verzió)

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ez a cikk bemutatja, hogyan engedélyezheti a MLflow követési URI-és naplózási API-JÁT, a [MLflow-követést](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api)együttesen, a MLflow-kísérletek és a Azure Machine learning összekapcsolásához. Ezzel lehetővé teszi, hogy

+ A kísérlet metrikáinak és összetevőinek nyomon követése és naplózása a [Azure Machine learning munkaterületen](https://docs.microsoft.com/azure/machine-learning/concept-azure-machine-learning-architecture#workspaces). Ha már használja a kísérletek MLflow követését, a munkaterület központosított, biztonságos és skálázható helyet biztosít a képzési mérőszámok és modellek tárolásához.

+ A MLflow-kísérletek Azure Machine Learning webszolgáltatásként való üzembe helyezése. Webszolgáltatásként való üzembe helyezéssel az éles modelleken alkalmazhatja a Azure Machine Learning monitorozási és adateltolódás-észlelési funkcióit. 

A [MLflow](https://www.mlflow.org) egy nyílt forráskódú kódtár a gépi tanulási kísérletek életciklusának kezeléséhez. A MLFlow Tracking a MLflow egy olyan összetevője, amely naplózza és nyomon követi a képzési műveletek mérőszámait és modelljeit, függetlenül a kísérlet környezetében – helyileg a számítógépén, egy távoli számítási célponton, egy virtuális gépen vagy egy Azure Databricks fürtön. 

Az alábbi ábra azt szemlélteti, hogy a MLflow nyomon követésével nyomon követheti a kísérlet futtatási metrikáit, és az Azure Machine Learning munkaterületen tárolhatja a modell összetevőit.

![mlflow az Azure Machine learning-diagrammal](./media/how-to-use-mlflow/mlflow-diagram-track.png)

> [!TIP]
> A jelen dokumentumban található információk elsősorban olyan adatszakértők és fejlesztők számára készültek, akik figyelni szeretnék a modell betanítási folyamatát. Ha Ön olyan rendszergazda, aki a Azure Machine Learning erőforrás-használat és-események figyelését érdekli (például kvóták, befejezett betanítási futtatások vagy befejezett modellek üzembe helyezése), tekintse meg a [figyelés Azure Machine learning](monitor-azure-machine-learning.md).

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>MLflow és Azure Machine Learning ügyfelek összehasonlítása

 Az alábbi táblázat összefoglalja azokat a különböző ügyfeleket, amelyek használhatják a Azure Machine Learningt, valamint a hozzájuk tartozó funkciókra vonatkozó képességeiket.

 A MLflow követése olyan metrikai naplózási és összetevő-tárolási funkciókat kínál, amelyek csak a [Azure Machine learning PYTHON SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)-n keresztül érhetők el.


| | MLflow &nbsp; követése <!--& Deployment--> | Python SDK Azure Machine Learning |  Azure Machine Learning parancssori felület | Azure Machine Learning Studio|
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

Telepítse a `azureml-mlflow` csomagot a MLflow-követés használatára Azure Machine learning a kísérleteken helyileg futtatott Jupyter notebook vagy Kódszerkesztő használatával.

```shell
pip install azureml-mlflow
```

>[!NOTE]
>A azureml. a névterek gyakran változnak, ahogy dolgozunk a szolgáltatás tökéletesítésén. Ennek megfelelően az ebben a névtérben található bármit előzetes verziónak kell tekinteni, és a Microsoft nem támogatja teljes mértékben.

Importálja a `mlflow` és [`Workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) osztályokat a MLflow követési URI-ja eléréséhez és a munkaterület konfigurálásához.

A következő kódban a `get_mlflow_tracking_uri()` metódus egyedi nyomkövetési URI-címeket rendel a munkaterülethez, `ws` és `set_tracking_uri()` a MLflow nyomon követi az adott címnek megfelelő URI-t.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
>A nyomkövetési URI-azonosító legfeljebb egy órán belül érvényes. Ha némi üresjárati idő után újraindítja a szkriptet, a get_mlflow_tracking_uri API-val új URI-t kap.

Állítsa be a MLflow-kísérlet nevét a (z) értékre, `set_experiment()` és indítsa el a képzést a alkalmazással `start_run()` . Ezután `log_metric()` aktiválja a MLflow naplózási API-t, és kezdje meg a betanítási futtatási metrikák naplózását.

```Python
experiment_name = 'experiment_with_mlflow'
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    mlflow.log_metric('alpha', 0.03)
```

## <a name="track-remote-runs"></a>Távoli futtatások nyomon követése

A MLflow követése Azure Machine Learning lehetővé teszi a naplózott metrikák és összetevők tárolását a távoli futtatásokból a Azure Machine Learning-munkaterületre.

A távoli futtatások lehetővé teszik, hogy a modelleket nagyobb teljesítményű számításokra, például GPU-t használó virtuális gépekre vagy Machine Learning Compute fürtökre tanítsa. A különböző számítási lehetőségek megismeréséhez lásd: [számítási célok beállítása a modell betanításához](how-to-set-up-training-targets.md) .

Állítsa be a számítási és képzési környezetét a [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) osztállyal. Belefoglalási `mlflow` és `azureml-mlflow` pip-csomagok a környezet [`CondaDependencies`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) szakaszában. Ezután hozza létre a számítási [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) célként a távoli számítási célt.

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

A betanítási szkriptben importálja `mlflow` a MLflow-naplózási API-k használatára, és kezdje meg a futtatási metrikák naplózását.

```Python
import mlflow

with mlflow.start_run():
    mlflow.log_metric('example', 1.23)
```

Ennek a számítási és betanítási futtatási konfigurációnak a használatával `Experiment.submit('train.py')` elküldheti a futtatást a metódussal. Ez a metódus automatikusan beállítja a MLflow követési URI-t, és a naplózást a MLflow a munkaterületre irányítja.

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

## <a name="deploy-mlflow-models-as-a-web-service"></a>MLflow-modellek üzembe helyezése webszolgáltatásként

A MLflow-kísérletek Azure Machine Learning webszolgáltatásként való üzembe helyezése lehetővé teszi, hogy kihasználja a Azure Machine Learning modell-felügyeleti és adateltolódás-észlelési képességeket, és alkalmazza azokat az éles modelleken.

A következő ábra azt mutatja be, hogy a MLflow üzembe helyezési API-val a meglévő MLflow-modelleket üzembe helyezheti Azure Machine Learning webszolgáltatásként, a keretrendszerük, a PyTorch, a Tensorflow, a scikit-Learn, a ONNX stb., valamint a munkaterületen lévő üzemi modellek kezeléséhez.

![mlflow az Azure Machine learning-diagrammal](./media/how-to-use-mlflow/mlflow-diagram-deploy.png)

### <a name="log-your-model"></a>A modell naplózása

A telepítés előtt győződjön meg róla, hogy a modell el lett mentve, hogy hivatkozhat rá, és annak elérési útját a központi telepítéshez. A betanítási szkriptben a következő [mlflow. sklearn. log_model ()](https://www.mlflow.org/docs/latest/python_api/mlflow.sklearn.html) metódushoz hasonló kódot kell megadni, amely a modellt a megadott kimeneti könyvtárba menti. 

```python
# change sklearn to pytorch, tensorflow, etc. based on your experiment's framework 
import mlflow.sklearn

# Save the model to the outputs directory for capture
mlflow.sklearn.log_model(regression_model, model_save_path)
```
>[!NOTE]
> Adja meg a `conda_env` paramétert annak a függőségeknek és környezetnek az átadásához, amelynek a modellnek futnia kell.

### <a name="retrieve-model-from-previous-run"></a>Modell beolvasása az előző futtatásból

A Futtatás lekéréséhez szüksége lesz a futtatási AZONOSÍTÓra és az elérési útra a modell mentési helyétől függően. 

```python
# gets the list of runs for your experiment as an array
experiment_name = 'experiment-with-mlflow'
exp = ws.experiments[experiment_name]
runs = list(exp.get_runs())

# get the run ID and the path in run history
runid = runs[0].id
model_save_path = 'model'
```

### <a name="deploy-the-model"></a>A modell üzembe helyezése

A modell webszolgáltatásként való üzembe helyezéséhez használja a Azure Machine Learning SDK-t.

Először határozza meg a telepítési konfigurációt. Az Azure Container instance (ACI) egy megfelelő választás a gyors fejlesztési és tesztelési célú üzembe helyezéshez, az Azure Kubernetes Service (ak) pedig méretezhető üzemi környezetekhez alkalmas.

#### <a name="deploy-to-aci"></a>Üzembe helyezés az ACI-ban

Állítsa be a telepítési konfigurációt a [deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) metódussal. A webszolgáltatások nyomon követéséhez címkéket és leírásokat is hozzáadhat.

```python
from azureml.core.webservice import AciWebservice, Webservice

# Configure 
aci_config = AciWebservice.deploy_configuration(cpu_cores=1, 
                                                memory_gb=1, 
                                                tags={'method' : 'sklearn'}, 
                                                description='Diabetes model',
                                                location='eastus2')
```

Ezután regisztrálja és telepítse a modellt a Azure Machine Learning SDK [üzembe helyezési](/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) módszer használatával. 

```python
(webservice,model) = mlflow.azureml.deploy( model_uri='runs:/{}/{}'.format(run.id, model_path),
                      workspace=ws,
                      model_name='sklearn-model', 
                      service_name='diabetes-model-1', 
                      deployment_config=aci_config, 
                      tags=None, mlflow_home=None, synchronous=True)

webservice.wait_for_deployment(show_output=True)
```
#### <a name="deploy-to-aks"></a>Üzembe helyezés az AKS-ben

Az AK-ra való üzembe helyezéshez először hozzon létre egy AK-fürtöt. Hozzon létre egy AK-fürtöt a [ComputeTarget. Create ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py#create-workspace--name--provisioning-configuration-) metódus használatával. Egy új fürt létrehozása 20-25 percet is igénybe vehet.

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
Állítsa be a telepítési konfigurációt a [deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) metódussal. A webszolgáltatások nyomon követéséhez címkéket és leírásokat is hozzáadhat.

```python
from azureml.core.webservice import Webservice, AksWebservice

# Set the web service configuration (using default here with app insights)
aks_config = AksWebservice.deploy_configuration(enable_app_insights=True, compute_target_name='aks-mlflow')


Then, deploy the image by using the Azure Machine Learning SDK [deploy()](Then, register and deploy the model by using the Azure Machine Learning SDK [deploy](/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) method. 

```python
# Webservice creation using single command
from azureml.core.webservice import AksWebservice, Webservice
(webservice, model) = mlflow.azureml.deploy( model_uri='runs:/{}/{}'.format(run.id, model_path),
                      workspace=ws,
                      model_name='sklearn-model', 
                      service_name='my-aks', 
                      deployment_config=aks_config, 
                      tags=None, mlflow_home=None, synchronous=True)


webservice.wait_for_deployment()
```

A szolgáltatás üzembe helyezése több percet is igénybe vehet.

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Ha nem tervezi a naplózott metrikák és összetevők használatát a munkaterületen, akkor a törlési képesség jelenleg nem érhető el. Ehelyett törölje a Storage-fiókot és-munkaterületet tartalmazó erőforráscsoportot, így nem számítunk fel díjat:

1. Az Azure Portalon válassza az **Erőforráscsoportok** lehetőséget a bal szélen.

   ![Törlés a Azure Portal](./media/how-to-use-mlflow/delete-resources.png)

1. Válassza ki a listában az Ön által létrehozott erőforráscsoportot.

1. Válassza az **Erőforráscsoport törlése** elemet.

1. Adja meg az erőforráscsoport nevét. Ezután válassza a **Törlés** elemet.

## <a name="example-notebooks"></a>Jegyzetfüzetek – példa

Az [Azure ml jegyzetfüzetekkel rendelkező MLflow](https://aka.ms/azureml-mlflow-examples) bemutatják és kibővítik a jelen cikkben ismertetett fogalmakat.

## <a name="next-steps"></a>Következő lépések
* [A modellek kezelése](concept-model-management-and-deployment.md).
* Figyelje az [adateltolódáshoz](how-to-monitor-data-drift.md)használt üzemi modelleket.
