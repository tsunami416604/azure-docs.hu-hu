---
title: ML-folyamatok létrehozása, futtatása, & nyomon követése
titleSuffix: Azure Machine Learning
description: Gépi tanulási folyamat létrehozása és futtatása a Pythonhoz készült Azure Machine Learning SDK-val. A gépi tanulási (ML) fázisokat összevarró munkafolyamatok létrehozásához és kezeléséhez használjon ML-es folyamatokat. Ezek a fázisok közé tartozik az adatelőkészítés, a modell betanítása, a modell üzembe helyezése, valamint a következtetések/pontozás.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.author: sanpil
author: sanpil
ms.date: 12/05/2019
ms.custom: seodec18
ms.openlocfilehash: b1b0facbb7cdd0dd51c53077c21afab427facf3b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82133584"
---
# <a name="create-and-run-machine-learning-pipelines-with-azure-machine-learning-sdk"></a>Gépi tanulási folyamatokat hozhat létre és futtathat Azure Machine Learning SDK-val

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebből a cikkből megtudhatja, hogyan hozhat létre, tehet közzé, futtathat és követhet nyomon egy [gépi tanulási folyamatot](concept-ml-pipelines.md) a [Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)használatával.  A **ml** -folyamatok segítségével hozzon létre egy olyan munkafolyamatot, amely különböző ml fázisokat egyesít, majd tegye közzé ezt a folyamatot a Azure Machine learning munkaterületen a későbbi eléréshez vagy másokkal való megosztáshoz.  A ML-folyamatok ideálisak kötegelt pontozási forgatókönyvekhez, különböző számítások használatával, az újbóli Futtatás helyett a lépéseket, valamint az ML-munkafolyamatok másokkal való megosztását.

Habár az [Azure-folyamatokat](https://docs.microsoft.com/azure/devops/pipelines/targets/azure-machine-learning?context=azure%2Fmachine-learning%2Fservice%2Fcontext%2Fml-context&view=azure-devops&tabs=yaml) más típusú folyamatokkal is használhatja, amelyek az ml-feladatok CI/CD-automatizálására szolgálnak, az adott típusú folyamat soha nem tárolódik a munkaterületen. [Hasonlítsa össze ezeket a különböző folyamatokat](concept-ml-pipelines.md#which-azure-pipeline-technology-should-i-use).

Egy ML-folyamat minden fázisa (például az adatelőkészítés és a modell betanítása) egy vagy több lépést is tartalmazhat.

A létrehozott ML-folyamatok a Azure Machine Learning [munkaterület](how-to-manage-workspace.md)tagjai számára láthatók. 

A ML-folyamatok távoli számítási célokat használnak a számításhoz és a folyamathoz társított közbenső és végső adatok tárolásához. A támogatott [Azure Storage](https://docs.microsoft.com/azure/storage/) -helyekről olvashatnak és írhatnak az adatokról.

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy ingyenes fiókot. Próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree).

## <a name="prerequisites"></a>Előfeltételek

* Hozzon létre egy [Azure Machine learning munkaterületet](how-to-manage-workspace.md) az összes folyamat erőforrásának tárolásához.

* [Konfigurálja a fejlesztési környezetet](how-to-configure-environment.md) az Azure Machine learning SDK telepítéséhez, vagy használjon egy [Azure Machine learning számítási példányt (előzetes verzió)](concept-compute-instance.md) a már telepített SDK-val.

Első lépésként csatolja a munkaterületet:

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```

## <a name="set-up-machine-learning-resources"></a>Gépi tanulási erőforrások beállítása

Hozzon létre egy ML-folyamat futtatásához szükséges erőforrásokat:

* Állítson be egy adattárolót, amely a folyamat lépéseiben szükséges információ elérésére szolgál.

* Konfiguráljon `Dataset` egy objektumot úgy, hogy olyan állandó adatértékre mutasson, amely egy adattárban él vagy elérhető. Konfiguráljon `PipelineData` egy objektumot a folyamat lépései között átadott ideiglenes adatmennyiséghez. 

* Állítsa be azokat a [számítási célokat](concept-azure-machine-learning-architecture.md#compute-targets) , amelyeken a folyamat lépései futni fognak.

### <a name="set-up-a-datastore"></a>Adattár beállítása

Az adattár tárolja a folyamathoz való hozzáférést. Az egyes munkaterületek alapértelmezett adattárral rendelkeznek. További adattárolókat is regisztrálhat. 

A munkaterület létrehozásakor a [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) és az [Azure Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) a munkaterülethez van csatolva. Az Azure Blob Storage-hoz való kapcsolódáshoz egy alapértelmezett adattár van regisztrálva. További információ: [a Azure Files, az Azure-blobok és az Azure-lemezek használatának](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks)meghatározása. 

```python
# Default datastore 
def_data_store = ws.get_default_datastore()

# Get the blob storage associated with the workspace
def_blob_store = Datastore(ws, "workspaceblobstore")

# Get file storage associated with the workspace
def_file_store = Datastore(ws, "workspacefilestore")

```

Töltse fel az adatfájlokat vagy címtárakat az adattárba, hogy azok elérhetők legyenek a folyamatokból. Ez a példa a blob Storage-t használja adattárként:

```python
def_blob_store.upload_files(
    ["iris.csv"],
    target_path="train-dataset",
    overwrite=True)
```

Egy folyamat egy vagy több lépésből áll. A lépés egy számítási célra futtatott egység. A lépések felhasználhatják az adatforrásokat, és "köztes" adatforrásokat hozhatnak létre. A lépések olyan adattípusokat hozhatnak létre, mint például a modell, a modellekkel és a függő fájlokkal rendelkező könyvtárak vagy az ideiglenes adatmennyiség. Ezek az adatkészletek a folyamat későbbi szakaszaiban is elérhetők.

Ha többet szeretne megtudni a folyamat adataihoz való csatlakoztatásáról, tekintse meg a cikkek [elérését](how-to-access-data.md) és az [adatkészletek regisztrálását](how-to-create-register-datasets.md)ismertető cikket. 

### <a name="configure-data-using-dataset-and-pipelinedata-objects"></a>Az `Dataset` adatkészletek `PipelineData` és az objektumok konfigurálása

Az imént létrehozott egy olyan adatforrást, amely egy adott lépés bemenetének megfelelően hivatkozhat egy folyamatra. Az adatfolyamatok adatátvitelének előnyben részesített módja egy [adatkészlet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.Dataset) -objektum. Az `Dataset` objektum olyan adatokra mutat, amelyek vagy egy adattárból vagy egy webes URL-címről érhetők el. Az `Dataset` osztály absztrakt, ezért létre kell hoznia egy `FileDataset` (egy vagy több fájlra hivatkozó) vagy egy olyan példányát, `TabularDataset` amelyet egy vagy több, tagolt oszlopokkal rendelkező fájlból hozott létre.

`Dataset`az objektumok támogatják a verziószámozást, a különbségeket és az összegző statisztikát. `Dataset`a (z) (például a Python-generátorok) kiértékelése és hatékony kiértékelése a (z) segítségével, felosztással vagy szűréssel. 

Hozzon létre `Dataset` egy olyan metódust, mint például a [from_file](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-) vagy a [from_delimited_files](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header-true--partition-format-none--support-multi-line-false-).

```python
from azureml.core import Dataset

iris_tabular_dataset = Dataset.Tabular.from_delimited_files([(def_blob_store, 'train-dataset/iris.csv')])
```

A közbenső adatokat (vagy egy lépés kimenetét) egy [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) objektum képviseli. `output_data1`egy lépés kimenete jön létre, és egy vagy több jövőbeli lépés bemenetéhez használható. `PipelineData`bevezet egy, a lépések közötti függőséget, és egy implicit végrehajtási sorrendet hoz létre a folyamatban. Ezt az objektumot később a folyamat lépéseinek létrehozásakor fogjuk használni.

```python
from azureml.pipeline.core import PipelineData

output_data1 = PipelineData(
    "output_data1",
    datastore=def_blob_store,
    output_name="output_data1")
```

Az adatkészletek és a folyamat adatainak kezelésével kapcsolatos további részletek és mintakód az adatok átvitele a következőre: [ml folyamat lépései (Python)](how-to-move-data-in-out-of-pipelines.md).

## <a name="set-up-a-compute-target"></a>Számítási cél beállítása

Azure Machine Learning a __számítási__ (vagy __számítási cél__) kifejezés a gépi tanulási folyamat számítási lépéseit végrehajtó gépekre vagy fürtökre vonatkozik.   A számítási célok teljes listájáért, valamint a munkaterülethez való létrehozásához és csatolásához lásd: [számítási célok a modell betanításához](how-to-set-up-training-targets.md) .  A számítási cél létrehozásához és csatolásához szükséges folyamat ugyanaz, függetlenül attól, hogy a modell betanítása vagy a folyamat lépései futnak-e. A számítási cél létrehozása és csatolása után használja az `ComputeTarget` objektumot a [folyamat lépéseiben](#steps).

> [!IMPORTANT]
> A számítási célokat szolgáló felügyeleti műveletek végrehajtása távoli feladatokon belül nem támogatott. Mivel a gépi tanulási folyamatokat távoli feladatokként küldi el a rendszer, ne használja a számítási célok felügyeleti műveleteit a folyamaton belül.

Az alábbi példák a számítási célok létrehozásához és csatolásához szükségesek a következőkhöz:

* Azure Machine Learning Compute
* Azure Databricks 
* Azure Data Lake Analytics

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning számítás

A lépések futtatásához létrehozhat egy Azure Machine Learning számítást.

```python
from azureml.core.compute import ComputeTarget, AmlCompute

compute_name = "aml-compute"
vm_size = "STANDARD_NC6"
if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('Found compute target: ' + compute_name)
else:
    print('Creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size=vm_size,  # STANDARD_NC6 is GPU-enabled
                                                                min_nodes=0,
                                                                max_nodes=4)
    # create the compute target
    compute_target = ComputeTarget.create(
        ws, compute_name, provisioning_config)

    # Can poll for a minimum number of nodes and for a specific timeout.
    # If no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(
        show_output=True, min_node_count=None, timeout_in_minutes=20)

    # For a more detailed view of current cluster status, use the 'status' property
    print(compute_target.status.serialize())
```

### <a name="azure-databricks"></a><a id="databricks"></a>Azure Databricks

A Azure Databricks egy Apache Spark-alapú környezet az Azure-felhőben. A számítási célként Azure Machine Learning folyamattal is használható.

Használat előtt hozzon létre egy Azure Databricks munkaterületet. Munkaterület-erőforrás létrehozásához tekintse [meg a Spark-feladatok futtatása Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) dokumentumon című témakört.

Azure Databricks számítási célként való csatolásához adja meg a következő információkat:

* __Databricks számítási név__: a számítási erőforráshoz hozzárendelni kívánt név.
* __Databricks-munkaterület neve__: az Azure Databricks munkaterület neve.
* __Databricks hozzáférési jogkivonat__: a Azure Databricks hitelesítéséhez használt hozzáférési jogkivonat. Hozzáférési jogkivonat létrehozásához tekintse meg a [hitelesítési](https://docs.azuredatabricks.net/dev-tools/api/latest/authentication.html) dokumentumot.

Az alábbi kód azt mutatja be, hogyan csatolható Azure Databricks számítási célként a Azure Machine Learning SDK-val (__az Databricks-munkaterületnek ugyanabban az előfizetésben kell lennie, mint a pénzmosás-munkaterület__):

```python
import os
from azureml.core.compute import ComputeTarget, DatabricksCompute
from azureml.exceptions import ComputeTargetException

databricks_compute_name = os.environ.get(
    "AML_DATABRICKS_COMPUTE_NAME", "<databricks_compute_name>")
databricks_workspace_name = os.environ.get(
    "AML_DATABRICKS_WORKSPACE", "<databricks_workspace_name>")
databricks_resource_group = os.environ.get(
    "AML_DATABRICKS_RESOURCE_GROUP", "<databricks_resource_group>")
databricks_access_token = os.environ.get(
    "AML_DATABRICKS_ACCESS_TOKEN", "<databricks_access_token>")

try:
    databricks_compute = ComputeTarget(
        workspace=ws, name=databricks_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('databricks_compute_name {}'.format(databricks_compute_name))
    print('databricks_workspace_name {}'.format(databricks_workspace_name))
    print('databricks_access_token {}'.format(databricks_access_token))

    # Create attach config
    attach_config = DatabricksCompute.attach_configuration(resource_group=databricks_resource_group,
                                                           workspace_name=databricks_workspace_name,
                                                           access_token=databricks_access_token)
    databricks_compute = ComputeTarget.attach(
        ws,
        databricks_compute_name,
        attach_config
    )

    databricks_compute.wait_for_completion(True)
```

Részletesebb példaként tekintse meg a GitHubon egy [példát a notebookra](https://aka.ms/pl-databricks) .

### <a name="azure-data-lake-analytics"></a><a id="adla"></a>Azure Data Lake Analytics

A Azure Data Lake Analytics egy big data elemzési platform az Azure-felhőben. A számítási célként Azure Machine Learning folyamattal is használható.

Használat előtt hozzon létre egy Azure Data Lake Analytics fiókot. Az erőforrás létrehozásához tekintse meg az [Ismerkedés a Azure Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal) dokumentummal című témakört.

Data Lake Analytics számítási célként való csatolásához a Azure Machine Learning SDK-t kell használnia, és meg kell adnia a következő információkat:

* __Számítási név__: a számítási erőforráshoz hozzárendelni kívánt név.
* __Erőforráscsoport__: az Data Lake Analytics fiókot tartalmazó erőforráscsoport.
* __Fiók neve__: a Data Lake Analytics fiók neve.

A következő kód bemutatja, hogyan csatolhatja Data Lake Analytics számítási célként:

```python
import os
from azureml.core.compute import ComputeTarget, AdlaCompute
from azureml.exceptions import ComputeTargetException


adla_compute_name = os.environ.get(
    "AML_ADLA_COMPUTE_NAME", "<adla_compute_name>")
adla_resource_group = os.environ.get(
    "AML_ADLA_RESOURCE_GROUP", "<adla_resource_group>")
adla_account_name = os.environ.get(
    "AML_ADLA_ACCOUNT_NAME", "<adla_account_name>")

try:
    adla_compute = ComputeTarget(workspace=ws, name=adla_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('adla_compute_name {}'.format(adla_compute_name))
    print('adla_resource_id {}'.format(adla_resource_group))
    print('adla_account_name {}'.format(adla_account_name))
    # create attach config
    attach_config = AdlaCompute.attach_configuration(resource_group=adla_resource_group,
                                                     account_name=adla_account_name)
    # Attach ADLA
    adla_compute = ComputeTarget.attach(
        ws,
        adla_compute_name,
        attach_config
    )

    adla_compute.wait_for_completion(True)
```

Részletesebb példaként tekintse meg a GitHubon egy [példát a notebookra](https://aka.ms/pl-adla) .

> [!TIP]
> Azure Machine Learning folyamatok csak az Data Lake Analytics-fiók alapértelmezett adattárában tárolt adatmennyiségek esetében használhatók. Ha a működéséhez szükséges adatmennyiség nem alapértelmezett tárolóban található, akkor az a [`DataTransferStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) használatával másolhatja az Adatmásolást a betanítás előtt.

## <a name="construct-your-pipeline-steps"></a><a id="steps"></a>A folyamat lépéseinek megépítése

Miután létrehozta és csatol egy számítási célt a munkaterülethez, készen áll a folyamat lépésének megadására. A Azure Machine Learning SDK-n keresztül számos beépített lépés érhető el. Ezeknek a lépéseknek a legalapvetőbb lépései egy [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py), amely egy Python-szkriptet futtat egy adott számítási célra:

```python
from azureml.pipeline.steps import PythonScriptStep

ds_input = my_dataset.as_named_input('input1')

trainStep = PythonScriptStep(
    script_name="train.py",
    arguments=["--input", ds_input.as_download(), "--output", output_data1],
    inputs=[ds_input],
    outputs=[output_data1],
    compute_target=compute_target,
    source_directory=project_folder,
    allow_reuse=True
)
```

A korábbi eredmények (`allow_reuse`) használata kulcsfontosságú, ha a folyamatokat együttműködésen alapuló környezetben használják, mivel a szükségtelen ismétlések kiküszöbölése nem biztosít rugalmasságot. Az újrafelhasználás az alapértelmezett viselkedés, ha a script_name, a bemenetek és a lépés paramétereinek változatlanok maradnak. A lépés kimenetének újrafelhasználásakor a rendszer nem küldi el a feladatot a számításhoz, hanem az előző Futtatás eredményei azonnal elérhetők lesznek a következő lépés futtatásához. Ha `allow_reuse` a értéke false (hamis), akkor a folyamat végrehajtása során a rendszer mindig új futtatást generál ehhez a lépéshez. 

A lépések meghatározása után a folyamatokat a fenti lépések némelyikével vagy mindegyikével kell felépíteni.

> [!NOTE]
> A lépések megadása vagy a folyamat összeállítása során a rendszer nem tölt fel fájlt vagy Azure Machine Learning.

```python
# list of steps to run
compareModels = [trainStep, extractStep, compareStep]

from azureml.pipeline.core import Pipeline

# Build the pipeline
pipeline1 = Pipeline(workspace=ws, steps=[compareModels])
```

A következő példa a korábban létrehozott Azure Databricks számítási célt használja: 

```python
from azureml.pipeline.steps import DatabricksStep

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

### <a name="use-a-dataset"></a>Adatkészlet használata 

Az Azure Blob Storage-ból, Azure Filesból, Azure Data Lake Storage Gen1ból, Azure Data Lake Storage Gen2ból, Azure SQL Databaseból és Azure Database for PostgreSQLból létrehozott adatkészletek a folyamat bármely lépéséhez bemenetként is használhatók. Ha a kimenetet egy [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py) vagy [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py)szeretné írni, a kimeneti adatokat ([PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)) csak az Azure blobba és az Azure file share adattárba lehet írni.

```python
dataset_consuming_step = PythonScriptStep(
    script_name="iris_train.py",
    inputs=[iris_tabular_dataset.as_named_input("iris_data")],
    compute_target=compute_target,
    source_directory=project_folder
)
```

Ezután lekéri a folyamat adatkészletét a [Run. input_datasets](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#input-datasets) szótár használatával.

```python
# iris_train.py
from azureml.core import Run, Dataset

run_context = Run.get_context()
iris_dataset = run_context.input_datasets['iris_data']
dataframe = iris_dataset.to_pandas_dataframe()
```

A sort `Run.get_context()` érdemes kiemelni. Ez a függvény az aktuális `Run` kísérleti futtatást jelképező értéket kérdezi le. A fenti példában egy regisztrált adatkészlet beolvasására használjuk. Az `Run` objektum egy másik gyakori használata a kísérlet és a kísérlet alapjául szolgáló munkaterület beolvasása: 

```python
# Within a PythonScriptStep

ws = Run.get_context().experiment.workspace
```

További részletekért, beleértve az adatok továbbításának és elérésének alternatív módjait is, lásd: adatok áthelyezése a következőre és az adatátviteli [folyamat lépései (Python)](how-to-move-data-in-out-of-pipelines.md).

## <a name="submit-the-pipeline"></a>A folyamat elküldése

A folyamat elküldésekor Azure Machine Learning ellenőrzi az egyes lépésekhez tartozó függőségeket, és feltölti a megadott forrás-könyvtár pillanatképét. Ha nincs megadva a forrás könyvtára, a rendszer feltölti az aktuális helyi könyvtárat. A pillanatkép a munkaterületen a kísérlet részeként is tárolódik.

> [!IMPORTANT]
> Ha meg szeretné akadályozni, hogy a fájlok szerepeljenek a pillanatképben, `.amlignore` hozzon létre egy [. gitignore](https://git-scm.com/docs/gitignore) vagy fájlt a címtárban, és adja hozzá a fájlokat. A `.amlignore` fájl ugyanazt a szintaxist és mintázatot használja, mint a [. gitignore](https://git-scm.com/docs/gitignore) fájl. Ha mindkét fájl létezik, a `.amlignore` fájl elsőbbséget élvez.
>
> További információ: [Pillanatképek](concept-azure-machine-learning-architecture.md#snapshots).

```python
from azureml.core import Experiment

# Submit the pipeline to be run
pipeline_run1 = Experiment(ws, 'Compare_Models_Exp').submit(pipeline1)
pipeline_run1.wait_for_completion()
```

A folyamat első futtatásakor Azure Machine Learning:

* Letölti a projekt pillanatképét a számítási célra a munkaterülethez társított blob Storage-ból.
* Létrehoz egy Docker-rendszerképet a folyamat egyes lépéseinek megfelelően.
* Letölti az egyes lépésekhez tartozó Docker-rendszerképet a számítási célra a tároló-beállításjegyzékből.
* A és `Dataset` `PipelineData` az objektumok elérését konfigurálja. A as `as_mount()` hozzáférési mód használata esetén a rendszer biztosítékot biztosít a virtuális hozzáférés biztosításához. Ha a csatlakoztatás nem támogatott `as_download()`, vagy ha a felhasználó a hozzáférési jogosultságot adja meg, az adatok a számítási célra lesznek másolva.
* Futtatja a lépést a lépés definíciójában megadott számítási célként. 
* Olyan összetevőket hoz létre, mint például a naplók, az stdout és az stderr, a metrikák és a lépés által megadott kimenet. Ezeket az összetevőket a rendszer feltölti és megőrzi a felhasználó alapértelmezett adattárában.

![Kísérlet folyamatként való futtatásának ábrája](./media/how-to-create-your-first-pipeline/run_an_experiment_as_a_pipeline.png)

További információ: [kísérlet osztály](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py) referenciája.

### <a name="view-results-of-a-pipeline"></a>Folyamat eredményeinek megtekintése

Tekintse meg az összes folyamat listáját és a futtatásuk részleteit a Studióban:

1. Jelentkezzen be [Azure Machine learning studióba](https://ml.azure.com).

1. [Megtekintheti a munkaterületet](how-to-manage-workspace.md#view).

1. A bal oldalon válassza a **folyamatok** lehetőséget az összes folyamat futtatásának megtekintéséhez.
 ![Machine learning-folyamatok listája](./media/how-to-create-your-first-pipeline/pipelines.png)
 
1. Válasszon ki egy adott folyamatot a Futtatás eredményeinek megtekintéséhez.

## <a name="git-tracking-and-integration"></a>Git-követés és-integráció

Ha olyan képzést indít el, ahol a forrás könyvtára helyi git-tárház, a rendszer a tárház adatait a futtatási előzményekben tárolja. További információ: git- [integráció Azure Machine Learninghoz](concept-train-model-git-integration.md).

## <a name="publish-a-pipeline"></a>Folyamat közzététele

A folyamatokat közzéteheti úgy, hogy később különböző bemenetekkel fusson. Egy már közzétett folyamat REST-végpontján a paraméterek elfogadásához el kell parametrizálja a folyamatot a közzététel előtt.

1. A folyamat paramétereinek létrehozásához használjon egy alapértelmezett értéket használó [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py) objektumot.

   ```python
   from azureml.pipeline.core.graph import PipelineParameter
   
   pipeline_param = PipelineParameter(
     name="pipeline_arg",
     default_value=10)
   ```

2. Adja hozzá `PipelineParameter` ezt az objektumot paraméterként a folyamat lépéseihez a következőképpen:

   ```python
   compareStep = PythonScriptStep(
     script_name="compare.py",
     arguments=["--comp_data1", comp_data1, "--comp_data2", comp_data2, "--output_data", out_data3, "--param1", pipeline_param],
     inputs=[ comp_data1, comp_data2],
     outputs=[out_data3],
     compute_target=compute_target,
     source_directory=project_folder)
   ```

3. Tegye közzé ezt a folyamatot, amely fogadja a paramétert a meghívásakor.

   ```python
   published_pipeline1 = pipeline_run1.publish_pipeline(
        name="My_Published_Pipeline",
        description="My Published Pipeline Description",
        version="1.0")
   ```

### <a name="run-a-published-pipeline"></a>Közzétett folyamat futtatása

Minden közzétett folyamat REST-végponttal rendelkezik. Ez a végpont meghívja a folyamat futtatását a külső rendszerekből, például a nem Python-ügyfelekből. Ez a végpont lehetővé teszi a "felügyelt ismételhetőség" használatát a Batch-pontozási és-átképzési forgatókönyvekben.

Az előző folyamat futtatásának megkezdéséhez szüksége lesz egy Azure Active Directory hitelesítési fejléc-tokenre, amelyet a [AzureCliAuthentication osztály](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py) -referenciában talál, vagy további részleteket talál a [hitelesítés Azure Machine learning](https://aka.ms/pl-restep-auth) jegyzetfüzetben című cikkben leírtak szerint.

```python
from azureml.pipeline.core import PublishedPipeline
import requests

response = requests.post(published_pipeline1.endpoint,
                         headers=aad_token,
                         json={"ExperimentName": "My_Pipeline",
                               "ParameterAssignments": {"pipeline_arg": 20}})
```

## <a name="create-a-versioned-pipeline-endpoint"></a>Verzióval ellátott folyamat végpontjának létrehozása

A folyamat végpontja több közzétett folyamattal is létrehozható. Ez egy közzétett folyamathoz is használható, de egy rögzített REST-végpontot biztosít, amellyel megismétli és frissítheti a ML-folyamatokat.

```python
from azureml.pipeline.core import PipelineEndpoint

published_pipeline = PublishedPipeline.get(workspace="ws", name="My_Published_Pipeline")
pipeline_endpoint = PipelineEndpoint.publish(workspace=ws, name="PipelineEndpointTest",
                                            pipeline=published_pipeline, description="Test description Notebook")
```

### <a name="submit-a-job-to-a-pipeline-endpoint"></a>Feladatok elküldése egy folyamat végpontjának

Elküldheti a feladatokat egy folyamat-végpont alapértelmezett verziójára:

```python
pipeline_endpoint_by_name = PipelineEndpoint.get(workspace=ws, name="PipelineEndpointTest")
run_id = pipeline_endpoint_by_name.submit("PipelineEndpointExperiment")
print(run_id)
```

A feladatokat egy adott verzióra is elküldheti:

```python
run_id = pipeline_endpoint_by_name.submit("PipelineEndpointExperiment", pipeline_version="0")
print(run_id)
```

Ugyanezt a REST API használatával is elvégezheti:

```python
rest_endpoint = pipeline_endpoint_by_name.endpoint
response = requests.post(rest_endpoint, 
                         headers=aad_token, 
                         json={"ExperimentName": "PipelineEndpointExperiment",
                               "RunSource": "API",
                               "ParameterAssignments": {"1": "united", "2":"city"}})
```

### <a name="use-published-pipelines-in-the-studio"></a>Közzétett folyamatok használata a Studióban

A közzétett folyamatokat a studióból is futtathatja:

1. Jelentkezzen be [Azure Machine learning studióba](https://ml.azure.com).

1. [Megtekintheti a munkaterületet](how-to-manage-workspace.md#view).

1. A bal oldalon válassza a **végpontok**lehetőséget.

1. A felső részen válassza a **folyamat-végpontok**lehetőséget.
 ![a Machine learning által közzétett folyamatok listája](./media/how-to-create-your-first-pipeline/pipeline-endpoints.png)

1. Válasszon ki egy adott folyamatot a folyamat végpontjának korábbi futtatásainak futtatásához, felhasználásához vagy áttekintéséhez.

### <a name="disable-a-published-pipeline"></a>Közzétett folyamat letiltása

Ha el szeretné rejteni egy folyamatot a közzétett folyamatok listájáról, tiltsa le azt a Studióban vagy az SDK-ban:

```python
# Get the pipeline by using its ID from Azure Machine Learning studio
p = PublishedPipeline.get(ws, id="068f4885-7088-424b-8ce2-eeb9ba5381a6")
p.disable()
```

A `p.enable()`használatával újra engedélyezheti. További információ: PublishedPipeline- [osztály](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.publishedpipeline?view=azure-ml-py) referenciája.

## <a name="caching--reuse"></a>Gyorsítótárazási & újrafelhasználása  

A folyamatok működésének optimalizálásához és testreszabásához hajtson végre néhány dolgot a gyorsítótárazás és az újbóli használat érdekében. Például a következőket teheti:
+ **Állítsa le a kimenet futtatásának alapértelmezett újrafelhasználását** a `allow_reuse=False` [lépés definíciója](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py)szerint. Az újrafelhasználás a kulcs, ha a folyamatokat együttműködésen alapuló környezetben használja, mivel a szükségtelen futtatások kiiktatása rugalmasságot biztosít. Azonban letilthatja az ismételt használatot.
+ **A kimenet újragenerálásának kényszerítése a futtatási folyamat összes lépése esetében**`pipeline_run = exp.submit(pipeline, regenerate_outputs=False)`

Alapértelmezés szerint `allow_reuse` a lépések engedélyezve vannak, és a `source_directory` lépés definíciójában megadott érték kivonatolásra kerül. Tehát ha egy adott lépés parancsfájlja ugyanazokat (`script_name`, bemeneteket és paramétereket)` source_directory` tartalmaz, és semmi más nem módosult, az előző lépés futtatásának kimenete újra felhasználható, a feladatot a rendszer nem küldi el a számításba, és az előző Futtatás eredményei azonnal elérhetővé válnak a következő lépéshez.

```python
step = PythonScriptStep(name="Hello World",
                        script_name="hello_world.py",
                        compute_target=aml_compute,
                        source_directory=source_directory,
                        allow_reuse=False,
                        hash_paths=['hello_world.ipynb'])
```

## <a name="next-steps"></a>További lépések

- [Ezeket a Jupyter-jegyzetfüzeteket a githubon](https://aka.ms/aml-pipeline-readme) a gépi tanulási folyamatok további megismeréséhez használhatja.
- Tekintse meg a [azureml-pipeline-Core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) csomag és a azureml-folyamatok – [STEPs](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) csomag SDK-referenciáját.
- A folyamatokkal kapcsolatos hibakeresési és hibaelhárítási tippekért lásd: [útmutató](how-to-debug-pipelines.md) .

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]
