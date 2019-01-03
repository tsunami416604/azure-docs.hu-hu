---
title: Nyomon követheti a gépi Tanulási folyamatok létrehozása & futtatása
titleSuffix: Azure Machine Learning service
description: Hozzon létre, és futtassa a machine learning-folyamat, az Azure Machine Learning-SDK Pythonhoz készült. A folyamatok létrehozásához és kezeléséhez a munkafolyamatok a varrva együtt machine learning (gépi tanulás) fázisok használhatja. Ezeket a fázisokat tartalmazza az adat-előkészítés, modell betanítása, modell-üzembehelyezés és következtetési.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.author: sanpil
author: sanpil
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 6c6472b824eefdd1954f3645c69090d1fb5455de
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/22/2018
ms.locfileid: "53754458"
---
# <a name="create-and-run-a-machine-learning-pipeline-by-using-azure-machine-learning-sdk"></a>Hozzon létre, és a egy machine learning-folyamat futtatása az Azure Machine Learning-SDK használatával

Ebből a cikkből megismerheti, hogyan hozzon létre, közzétételéhez, futtassa, és nyomon követheti a [machine learning-folyamat](concept-ml-pipelines.md) használatával a [Azure Machine Learning-SDK](https://aka.ms/aml-sdk).  Ezek a folyamatok létrehozásához és kezeléséhez a munkafolyamatok, amelyek összefűzheti a machine learning különböző fázisait segítségével. Minden egyes fázisában, például az adat-előkészítési és a modell betanítása és a egy folyamatot egy vagy több lépést is tartalmazhatnak.

A folyamatok létrehozása az Azure Machine Learning szolgáltatás tagjai számára láthatók [munkaterület](how-to-manage-workspace.md). 

A folyamatok távoli számítási célnak kiszámításához és a köztes és végső adatokat, hogy a folyamat társított tárolására használja. A folyamatok olvasható és írható adat- és támogatott [Azure Storage](https://docs.microsoft.com/azure/storage/) helyeket.

>[!Note]
>Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy ingyenes fiókot megkezdése előtt. Próbálja ki a [Azure Machine Learning szolgáltatás ingyenes vagy fizetős verzióját](http://aka.ms/AMLFree).

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

### <a name="set-up-compute"></a>Számítási beállítása

Az Azure Machine Learning az előfizetési időszak *számítási* (vagy *számítási célt*) a gépek vagy fürtök, amelyek a machine learning folyamatban, a számítási lépésekkel vonatkozik. Létrehozhat például egy Azure Machine Learning compute futtatásához a lépéseket.

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

## <a name="construct-your-pipeline-steps"></a>Hozza létre a folyamat lépései

Most már készen áll egy folyamat lépés meghatározásához. Nincsenek elérhető az Azure Machine Learning SDK segítségével számos beépített lépéseket. Az alábbi lépéseket a legtöbb alapszint egy `PythonScriptStep`, amely egy Python-szkriptet futtat egy adott számítási célnak.

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

## <a name="submit-the-pipeline"></a>Küldje el a folyamat

A folyamat elküldésekor Azure Machine Learning szolgáltatás ellenőrzi az egyes lépések a függőségeket, és feltölti a megadott forráskönyvtár pillanatképét. Ha nincs forráskönyvtár van megadva, az aktuális helyi könyvtárban van feltöltve.

```python
# Submit the pipeline to be run
pipeline_run1 = Experiment(ws, 'Compare_Models_Exp').submit(pipeline1)
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
