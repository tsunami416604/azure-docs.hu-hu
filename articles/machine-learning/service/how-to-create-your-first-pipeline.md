---
title: Nyomon követheti a gépi Tanulási folyamatok létrehozása & futtatása
titleSuffix: Azure Machine Learning service
description: Hozzon létre, és futtassa a machine learning-folyamat, az Azure Machine Learning-SDK Pythonhoz készült. A folyamatok létrehozásához és kezeléséhez a munkafolyamatok a varrva együtt machine learning (gépi tanulás) fázisok használhatja. Ezeket a fázisokat tartalmazza az adat-előkészítés, modell betanítása, modell-üzembehelyezés és következtetési.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.author: sanpil
author: sanpil
ms.date: 01/08/2019
ms.custom: seodec18
ms.openlocfilehash: 4698268d8e7798d0664d9eb6c33862d9641ff9fc
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2019
ms.locfileid: "55895584"
---
# <a name="create-and-run-a-machine-learning-pipeline-by-using-azure-machine-learning-sdk"></a>Hozzon létre, és a egy machine learning-folyamat futtatása az Azure Machine Learning-SDK használatával

Ebből a cikkből megismerheti, hogyan hozzon létre, közzétételéhez, futtassa, és nyomon követheti a [machine learning-folyamat](concept-ml-pipelines.md) használatával a [Azure Machine Learning-SDK](https://aka.ms/aml-sdk).  Ezek a folyamatok létrehozásához és kezeléséhez a munkafolyamatok, amelyek összefűzheti a machine learning különböző fázisait segítségével. Minden egyes fázisában, például az adat-előkészítési és a modell betanítása és a egy folyamatot egy vagy több lépést is tartalmazhatnak.

A folyamatok létrehozása az Azure Machine Learning szolgáltatás tagjai számára láthatók [munkaterület](how-to-manage-workspace.md). 

A folyamatok távoli számítási célnak kiszámításához és a köztes és végső adatokat, hogy a folyamat társított tárolására használja. A folyamatok olvasható és írható adat- és támogatott [Azure Storage](https://docs.microsoft.com/azure/storage/) helyeket.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy ingyenes fiókot megkezdése előtt. Próbálja ki a [Azure Machine Learning szolgáltatás ingyenes vagy fizetős verzióját](http://aka.ms/AMLFree).

## <a name="prerequisites"></a>Előfeltételek

* [A fejlesztési környezet konfigurálása](how-to-configure-environment.md) az Azure Machine Learning SDK telepítése.

* Hozzon létre egy [Azure Machine Learning-munkaterületet](how-to-configure-environment.md#workspace) , amely tárolja az folyamat-erőforrások. 

 ```python
 ws = Workspace.create(
     name = '<workspace-name>',
     subscription_id = '<subscription-id>',
     resource_group = '<resource-group>',
     location = '<workspace_region>',
     exist_ok = True)
 ```

## <a name="set-up-machine-learning-resources"></a>Állítsa be a machine learning-erőforrások

A folyamat futtatásához szükséges erőforrások létrehozásához:

* Állítsa be a folyamat lépések során szükség az adatok eléréséhez használt adattárolót.

* Konfigurálja a `DataReference` él, vagy elérhető adattárolót az adatok az objektum.

* Állítsa be a [számítási céljainak](concept-azure-machine-learning-architecture.md#compute-target) az a folyamat lépései futtathatók.

### <a name="set-up-a-datastore"></a>Egy adattár beállítása
Egy adattár tárolja az adatokat a folyamat eléréséhez. Az egyes munkaterületeken tartozik egy alapértelmezett adattárhoz. További adattárainak regisztrálhat. 

A munkaterület létrehozásakor [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) és [Azure Blob storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) alapértelmezés szerint a munkaterülethez csatlakozik. Az Azure Files a munkaterülethez tartozó alapértelmezett datastore, de is használhatja a Blob storage-adattárhoz. További tudnivalókért lásd: [való használata az Azure Files, Azure-Blobok és Azure-lemezek](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks). 

```python
# Default datastore (Azure file storage)
def_data_store = ws.get_default_datastore() 

# The above call is equivalent to this 
def_data_store = Datastore(ws, "workspacefilestore")

# Get blob storage associated with the workspace
def_blob_store = Datastore(ws, "workspaceblobstore")
```

Töltse fel az adatok fájlok vagy könyvtárak az adattárral, hogy a folyamatok a érhető el. Ebben a példában az adattárhoz Blob storage a verzióját használja:

```python
def_blob_store.upload_files(
    ["./data/20news.pkl"],
    target_path="20newsgroups", 
    overwrite=True)
```

Egy folyamatot egy vagy több lépésből áll. Egy lépésre egy egység egy számítási célnak futhatnak. Előfordulhat, hogy lépéseket felhasználhat adatforrásokat, és a "köztes" adatok előállításához. Egy lépéssel létrehozhat például egy modellt, a modell és a függő fájlokat egy könyvtárat, és ideiglenes adatokat. Ezek az adatok ezután a folyamat későbbi részében további lépések érhető el.

### <a name="configure-data-reference"></a>Konfigurálja a data-referencia

Létrehozott egy adatforrás, amely lehet hivatkozni a folyamat egy lépéssel bemenetként. Egy adatforrás egy adott folyamat képviseli egy [DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference) objektum. A `DataReference` él vagy egy adattár elérhető adatok objektumra mutat.

```python
blob_input_data = DataReference(
    datastore=def_blob_store,
    data_reference_name="test_data",
    path_on_datastore="20newsgroups/20news.pkl")
```

Köztes adatok (vagy egy lépés kimenetéből) képviseli egy [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) objektum. `output_data1` a lépések a kimeneteként előállított, és a későbbi lépésekben egy vagy több bemeneti szolgálnak. `PipelineData` lépések közötti függőségi adatokat, és a folyamat létrehoz egy implicit végrehajtásának sorrendje.

```python
output_data1 = PipelineData(
    "output_data1",
    datastore=def_blob_store,
    output_name="output_data1")
```

## <a name="set-up-compute-target"></a>Állítsa be a számítási célnak

Az Azure Machine Learning az előfizetési időszak __számítási__ (vagy __számítási célt__) a gépek vagy fürtök, amelyek a machine learning folyamatban, a számítási lépésekkel vonatkozik.   Lásd: [számítási céljainak modell betanítása](how-to-set-up-training-targets.md) számítási célnak, és hogyan hozhat létre, és csatolja őket a munkaterület teljes listáját.  A létrehozásához, és vagy egy számítási célnak csatolása ugyanúgy történik, függetlenül attól, hogy a modell tanítása vagy egy folyamat lépés futtatása. Miután hoz létre, és csatolja a számítási célnak, a `ComputeTarget` objektumának a [folyamat lépés](#steps).

> [!IMPORTANT]
> A számítási célokhoz felügyeleti műveleteket nem támogatott a távoli feladatokban. Mivel a machine learning-folyamatokat, távoli feladatokat az elküldésüket ne használja a műveletek a számítási célokhoz, a folyamat belül.

Az alábbiakban példákat létrehozása és csatlakoztatása a számítási célokhoz:

* Az Azure Machine Learning Compute
* Azure Databricks 
* Azure Data Lake Analytics

### <a name="azure-machine-learning-compute"></a>Az Azure Machine Learning Compute

Létrehozhat egy Azure Machine Learning compute futtatásához a lépéseket.

```python
compute_name = "aml-compute"
 if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('Found compute target: ' + compute_name)
else:
    print('Creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = vm_size, # NC6 is GPU-enabled
                                                                min_nodes = 1, 
                                                                max_nodes = 4)
     # create the compute target
    compute_target = ComputeTarget.create(ws, compute_name, provisioning_config)
    
    # Can poll for a minimum number of nodes and for a specific timeout. 
    # If no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current cluster status, use the 'status' property    
    print(compute_target.status.serialize())
```

### <a id="databricks"></a>Az Azure Databricks

Az Azure Databricks egy Apache Spark-alapú környezetben az Azure-felhőben. Az Azure Machine Learning-folyamat számítási célként használható.

Az Azure Databricks-munkaterület létrehozása a használat előtt. Ezek az erőforrás létrehozásához tekintse meg a [Spark-feladatok futtatása Azure Databricksen](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) dokumentumot.

Csatlakoztassa az Azure Databricks, egy számítási célnak, a következő információkat biztosítja:

* __Databricks számítási neve__: A számítási erőforrás hozzárendelni kívánt nevét.
* __Databricks-munkaterület neve__: Az Azure Databricks-munkaterület neve.
* __Databricks-hozzáférési jogkivonat__: A hozzáférési jogkivonatot az Azure databricks hitelesítéséhez. Hozzáférési jogkivonat létrehozásához, tekintse meg a [hitelesítési](https://docs.azuredatabricks.net/api/latest/authentication.html) dokumentumot.

A következő kód bemutatja, hogyan csatlakoztathat Azure Databricks, egy számítási célnak, az Azure Machine Learning SDK mutat be:

```python
import os
from azureml.core.compute import ComputeTarget, DatabricksCompute
from azureml.exceptions import ComputeTargetException

databricks_compute_name = os.environ.get("AML_DATABRICKS_COMPUTE_NAME", "<databricks_compute_name>")
databricks_workspace_name = os.environ.get("AML_DATABRICKS_WORKSPACE", "<databricks_workspace_name>")
databricks_resource_group = os.environ.get("AML_DATABRICKS_RESOURCE_GROUP", "<databricks_resource_group>")
databricks_access_token = os.environ.get("AML_DATABRICKS_ACCESS_TOKEN", "<databricks_access_token>")

try:
    databricks_compute = ComputeTarget(workspace=ws, name=databricks_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('databricks_compute_name {}'.format(databricks_compute_name))
    print('databricks_workspace_name {}'.format(databricks_workspace_name))
    print('databricks_access_token {}'.format(databricks_access_token))

    # Create attach config
    attach_config = DatabricksCompute.attach_configuration(resource_group = databricks_resource_group,
                                                           workspace_name = databricks_workspace_name,
                                                           access_token = databricks_access_token)
    databricks_compute = ComputeTarget.attach(
             ws,
             databricks_compute_name,
             attach_config
         )
    
    databricks_compute.wait_for_completion(True)
```
### <a id="adla"></a>Az Azure Data Lake Analytics

Az Azure Data Lake Analytics egy big data-elemzési platform az Azure-felhőben. Az Azure Machine Learning-folyamat számítási célként használható.

Hozzon létre egy Azure Data Lake Analytics-fiók, használat előtt. Ez az erőforrás létrehozásához tekintse meg a [Ismerkedés az Azure Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal) dokumentumot.

Csatlakoztassa a Data Lake Analytics számítási célként, az Azure Machine Learning SDK, és adja meg a következő információkat:

* __Számítási neve__: A számítási erőforrás hozzárendelni kívánt nevét.
* __Erőforráscsoport__: A Data Lake Analytics-fiókot tartalmazó erőforráscsoportot.
* __Fiók neve__: A Data Lake Analytics-fiók nevét.

A következő kód bemutatja, hogyan csatlakoztathat a Data Lake Analytics számítási célként mutat be:

```python
import os
from azureml.core.compute import ComputeTarget, AdlaCompute
from azureml.exceptions import ComputeTargetException


adla_compute_name = os.environ.get("AML_ADLA_COMPUTE_NAME", "<adla_compute_name>")
adla_resource_group = os.environ.get("AML_ADLA_RESOURCE_GROUP", "<adla_resource_group>")
adla_account_name = os.environ.get("AML_ADLA_ACCOUNT_NAME", "<adla_account_name>")

try:
    adla_compute = ComputeTarget(workspace=ws, name=adla_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('adla_compute_name {}'.format(adla_compute_name))
    print('adla_resource_id {}'.format(adla_resource_group))
    print('adla_account_name {}'.format(adla_account_name))
    # create attach config
    attach_config = AdlaCompute.attach_configuration(resource_group = adla_resource_group,
                                                     account_name = adla_account_name)
    # Attach ADLA
    adla_compute = ComputeTarget.attach(
             ws,
             adla_compute_name,
             attach_config
         )
    
    adla_compute.wait_for_completion(True)
```

> [!TIP]
> Az Azure Machine Learning-folyamatokat a Data Lake Analytics-fiók az alapértelmezett data store-ban tárolt adatokkal folytatott csak működik. Ha adatokat kell a munkahelyi egy nem alapértelmezett tároló, használhat egy [ `DataTransferStep` ](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) képzési előtt az adatok másolásához.

## <a id="steps"></a>Hozza létre a folyamat lépései

Hozzon létre, és a egy számítási célnak a munkaterülethez csatolja, készen áll egy folyamat lépés meghatározásához. Nincsenek elérhető az Azure Machine Learning SDK segítségével számos beépített lépéseket. Az alábbi lépéseket a legtöbb alapszint egy [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py), amely egy Python-szkriptet futtat egy adott számítási célnak.

```python
trainStep = PythonScriptStep(
    script_name="train.py",
    arguments=["--input", blob_input_data, "--output", processed_data1],
    inputs=[blob_input_data],
    outputs=[processed_data1],
    compute_target=compute_target,
    source_directory=project_folder
)
```

A lépéseket, meghatározása után hozhat létre a folyamat használatával, vagy azok egy részét az ezeket a lépéseket.

>[!NOTE]
>Egyetlen fájl vagy adatokat töltenek fel az Azure Machine Learning szolgáltatás határozza meg a lépéseket, vagy a folyamat létrehozását.

```python
# list of steps to run
compareModels = [trainStep, extractStep, compareStep]

# Build the pipeline
pipeline1 = Pipeline(workspace=ws, steps=[compareModels])
```

Az alábbi példa a korábban létrehozott Azure Databricks számítási célnak használja: 

```python
dbStep = DatabricksStep(
    name="databricksmodule",
    inputs=[step_1_input],
    outputs=[step_1_output],
    num_workers=1,
    notebook_path=notebook_path,
    notebook_params={'myparam': 'testparam'},
    run_name='demo run name',
    compute_target=databricks_compute,
    allow_reuse=False
)
# List of steps to run
steps = [dbStep]

# Build the pipeline
pipeline1 = Pipeline(workspace=ws, steps=steps)
```

## <a name="submit-the-pipeline"></a>Küldje el a folyamat

A folyamat elküldésekor Azure Machine Learning szolgáltatás ellenőrzi az egyes lépések a függőségeket, és feltölti a megadott forráskönyvtár pillanatképét. Ha nincs forráskönyvtár van megadva, az aktuális helyi könyvtárban van feltöltve.


```python
# Submit the pipeline to be run
pipeline_run1 = Experiment(ws, 'Compare_Models_Exp').submit(pipeline1)
pipeline_run1.wait_for_completion()
```

Ha először futtatja a folyamatot, az Azure Machine Learning:

* A projekt-pillanatkép letölti a számítási célnak a a munkaterülethez társított Blob storage-ból.
* Összeállít egy Docker-rendszerképet, a folyamat minden lépése megfelelő.
* Letölti a számítási célnak mindegyik lépéshez a docker-rendszerképet a tárolóregisztrációs adatbázisból.
* Csatlakoztatja az adattárhoz, ha egy `DataReference` objektum van megadva egy lépésben. Csatlakoztatási nem támogatott, ha, helyette a számítási célnak az adatokat másolja.
* A lépés fut a számítási célnak a lépés definíciójában megadott. 
* Összetevők, például naplók, az stdout és stderr, metrikák és a lépés által megadott hoz létre. Ezek az összetevők ezután feltöltött, és a felhasználó alapértelmezett adattárolóban tárolni.

![Egy kísérlet futtató folyamat ábrája](./media/how-to-create-your-first-pipeline/run_an_experiment_as_a_pipeline.png)

## <a name="publish-a-pipeline"></a>Folyamatok közzététele

Egy folyamat később futtatni a különböző adatbevitelek teheti közzé. A REST-végpont egy már közzétett folyamat paraméterek elfogadásához meg kell parametrizálja a folyamat a közzététel előtt. 

1. Egy folyamat paramétere létrehozásához használja a [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py) objektum alapértelmezett értékkel.

 ```python
 pipeline_param = PipelineParameter(
     name="pipeline_arg", 
     default_value=10)
 ```

2. Adja hozzá ezt a `PipelineParameter` paramétert a folyamat lépései valamelyik a következő objektum:

 ```python
 compareStep = PythonScriptStep(
     script_name="compare.py",
     arguments=["--comp_data1", comp_data1, "--comp_data2", comp_data2, "--output_data", out_data3, "--param1", pipeline_param],
     inputs=[ comp_data1, comp_data2],
     outputs=[out_data3],    
     target=compute_target, 
     source_directory=project_folder)
 ```

3. Ez a folyamat, amely elfogadja a paraméter meghívásakor közzététele.

   ```python
   published_pipeline1 = pipeline1.publish(
       name="My_Published_Pipeline", 
       description="My Published Pipeline Description")
   ```

## <a name="run-a-published-pipeline"></a>Egy közzétett folyamat futtatása

Az összes közzétett folyamatok REST-végponton kell. A végpont hívja meg a futtatást a folyamat külső rendszerekből, például az ügyfelek nem Python. Ez a végpont lehetővé teszi a "felügyelt ismételhetőség" a kötegelt pontozási és forgatókönyvek átképezési.

A fenti folyamat futtatásának elindításához, van szüksége egy Azure Active Directory hitelesítési fejléc tokent leírtak szerint [AzureCliAuthentication osztály](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py).

```python
response = requests.post(published_pipeline1.endpoint, 
    headers=aad_token, 
    json={"ExperimentName": "My_Pipeline",
        "ParameterAssignments": {"pipeline_arg": 20}})
```
## <a name="view-results"></a>Eredmények megtekintése

A folyamatok minden és azok részleteinek listáját lásd:
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).  

1. [A munkaterület megjelenítése](how-to-manage-workspace.md#view-a-workspace) folyamatok listája található.
 ![gépi tanulási folyamatok listája](./media/how-to-create-your-first-pipeline/list_of_pipelines.png)
 
1. Válassza ki az adott folyamatot a futtatási eredmények megtekintéséhez.

## <a name="next-steps"></a>További lépések
- Használat [ezek Jupyter notebookok a Githubon](https://aka.ms/aml-pipeline-readme) való ismerkedés a machine learning-további folyamatokat.
- Olvassa el az SDK-referencia súgó a a [azureml-folyamatok-core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) csomag és a [azureml-folyamatok-lépéseket](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) csomagot.

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]
