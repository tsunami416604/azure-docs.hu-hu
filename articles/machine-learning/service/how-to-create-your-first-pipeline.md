---
title: Hozzon létre és futtassa a teljes első gépi tanulási folyamat – az Azure Machine Learning szolgáltatás
description: Hozzon létre, és futtassa a machine learning-folyamat, az Azure Machine Learning-SDK Pythonhoz készült.  A folyamatok létrehozásához és kezeléséhez a munkafolyamatok, például az adat-előkészítés, modell betanítása, modell-üzembehelyezés és következtetési varrva együtt machine learning (gépi tanulás) fázisok szolgálnak.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.author: sanpil
author: sanpil
ms.date: 12/04/2018
ms.openlocfilehash: 4fcd981c74c80ee1f9cec5ef579cb40c2fb4d6fa
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52883209"
---
# <a name="create-and-run-a-machine-learning-pipeline-using-azure-machine-learning-sdk"></a>Hozhat létre és futtathat egy machine learning folyamatot az Azure Machine Learning SDK

Ebből a cikkből megismerheti, hogyan hozzon létre, közzétételéhez, futtassa, és nyomon követheti a [machine learning-folyamat](concept-ml-pipelines.md) használatával a [Azure Machine Learning-SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).  Ezek a folyamatok létrehozásához és kezeléséhez a munkafolyamatok, amelyek összefűzheti a machine learning különböző fázisait segítségével. Minden egyes fázisában, például az adat-előkészítési és a modell betanítása és a egy folyamatot egy vagy több lépést is tartalmazhatnak.

A folyamatok létrehozása az Azure Machine Learning szolgáltatás tagjai számára láthatók [munkaterület](how-to-manage-workspace.md). 

A folyamatok távoli számítási célnak kiszámításához és a köztes és végső adatokat, hogy a folyamat társított tárolására használja.  A folyamatok olvasható és írható adat- és támogatott [az Azure storage](https://docs.microsoft.com/azure/storage/) helyeket.


## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://aka.ms/AMLfree) a virtuális gép létrehozásának megkezdése előtt.

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

* Konfigurálja a `DataReference` objektum, amely formában, vagy olyan adattárolón elérhető adatok.

* Állítsa be a [számítási céljainak](concept-azure-machine-learning-architecture.md#compute-target) az a folyamat lépései futtathatók.

### <a name="set-up-a-datastore"></a>Egy adattár beállítása
Egy adattár tárolja az adatokat a folyamat eléréséhez.  Az egyes munkaterületeken tartozik egy alapértelmezett adattárhoz. További adattárainak regisztrálhat. 

A munkaterület létrehozásakor egy [az Azure file storage](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) és a egy [a blob storage-](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) alapértelmezés szerint a munkaterülethez csatlakozik.  Az Azure file storage munkaterület "alapértelmezett"-adattár, de is használhatja a blob storage-adattárhoz.  Tudjon meg többet [az Azure tárolási lehetőségeinek](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks). 

```python
# Default datastore (Azure file storage)
def_data_store = ws.get_default_datastore() 

# The above call is equivalent to this 
def_data_store = Datastore(ws, "workspacefilestore")

# Get blob storage associated with the workspace
def_blob_store = Datastore(ws, "workspaceblobstore")
```

Töltse fel az adatok fájlok vagy könyvtárak az adattárral, hogy a folyamatok a érhető el.  Ebben a példában az adattárhoz blob storage a verzióját használja:

```python
def_blob_store.upload_files(
    ["./data/20news.pkl"],
    target_path="20newsgroups", 
    overwrite=True)
```

Egy folyamatot egy vagy több lépésből áll.  Egy lépésre egy egység egy számítási célnak futhatnak.  Előfordulhat, hogy lépéseket felhasználhat adatforrásokat, és a "köztes" adatok előállításához. Egy lépéssel létrehozhat például egy modellt, a modell és a függő fájlokat egy könyvtárat, és ideiglenes adatokat.  Ezek az adatok ezután a folyamat későbbi részében további lépések érhető el.

### <a name="configure-data-reference"></a>Konfigurálja a data-referencia

Létrehozott egy adatforrás, amely lehet hivatkozni a folyamat egy lépéssel bemenetként. Egy adatforrás egy adott folyamat képviseli egy [DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference) objektum. A `DataReference` él vagy egy adattár elérhető adatok objektumra mutat.

```python
blob_input_data = DataReference(
    datastore=def_blob_store,
    data_reference_name="test_data",
    path_on_datastore="20newsgroups/20news.pkl")
```

Köztes adatok (vagy egy lépés kimenetéből) képviseli egy [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) objektum. `output_data1` a lépések a kimeneteként előállított és a későbbi lépésekben egy vagy több bemeneti használja.  `PipelineData` lépések közötti függőségi adatokat, és a egy implicit végrehajtásának sorrendje a folyamat hoz létre.

```python
output_data1 = PipelineData(
    "output_data1",
    datastore=def_blob_store,
    output_name="output_data1")
```

### <a name="set-up-compute"></a>Számítási beállítása

Az Azure Machine Learning compute (vagy számítási célnak) hivatkozik a gépek vagy fürtök, amelyek végrehajtják a számítási lépéseit a machine learning folyamatban. Például létrehozhat egy Azure Machine Learning Compute futtatásához a lépéseket.

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

Most már készen áll egy folyamat lépés meghatározásához. Nincsenek elérhető az Azure Machine Learning SDK segítségével számos beépített lépéseket. Az alábbi lépéseket a legtöbb alapszint egy `PythonScriptStep` egy Python-szkriptet, amely végrehajtja a megadott számítási célt.

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

A lépéseket, meghatározása után a folyamat egyes lépéseit vagy egészét ezek használatával hozhat létre.

>[!NOTE]
>Nincsenek fájlok vagy az töltenek fel az Azure Machine Learning szolgáltatás határozza meg a lépéseket, vagy a folyamat létrehozását.

```python
# list of steps to run
compareModels = [trainStep, extractStep, compareStep]

# Build the pipeline
pipeline1 = Pipeline(workspace=ws, steps=[compareModels])
```

## <a name="submit-the-pipeline"></a>Küldje el a folyamat

A folyamat elküldésekor a függőségek minden lépésről, illetve az Azure Machine Learning szolgáltatás a forráskönyvtár töltenek fel a megadott mappa pillanatkép ellenőrzi.  Ha nincs forráskönyvtár van megadva, az aktuális helyi könyvtárban van feltöltve.

```python
# Submit the pipeline to be run
pipeline_run1 = Experiment(ws, 'Compare_Models_Exp').submit(pipeline1)
```

Ha először futtatja a folyamatot:

* A projekt-pillanatkép letöltődik a számítási célnak a munkaterülethez társított blob storage-ból.
* A docker-rendszerkép összeállításakor a folyamat minden lépése megfelelő.
* Mindegyik lépéshez a docker-rendszerképet a tárolóregisztrációs adatbázisból letöltődik a számítási célnak.
* Ha egy `DataReference` objektum szerepel az adattár csatlakoztatása után a rendszer egy lépésben. Csatlakoztatási nem támogatott, ha, helyette a számítási célnak az adatokat másolja.
* A lépés a számítási célnak a lépés definíciójában megadott futtatható. 
* Összetevőket, például naplók, az stdout és stderr, metrikák és a lépés által megadott jönnek létre. Ezek az összetevők ezután feltöltött, és a felhasználó alapértelmezett adattárban tárolni.

![egy folyamat, kísérlet futtatása](./media/how-to-create-your-first-pipeline/run_an_experiment_as_a_pipeline.png)

## <a name="publish-a-pipeline"></a>Folyamatok közzététele

Egy folyamat később futtatni a különböző adatbevitelek teheti közzé. A REST-végpont egy már közzétett folyamat paraméterek elfogadásához a folyamat lehet paraméterezni, közzététel előtt. 

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

Az összes közzétett folyamatok egy REST-végpont meghívása a futtatást a folyamat külső rendszerekből, például az ügyfelek nem Python rendelkezik. Ez a végpont lehetővé teszi a kötegelt pontozási és forgatókönyvek átképezési a "felügyelt ismételhetőség".

A fenti folyamat futtatásának elindításához, van szüksége egy Azure Active Directory hitelesítési fejléc tokent leírtak szerint [AzureCliAuthentication osztályban](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py)

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
