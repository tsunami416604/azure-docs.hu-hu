---
title: Ml-folyamatok létrehozása, futtatása & nyomon követése
titleSuffix: Azure Machine Learning
description: Hozzon létre és futtasson egy gépi tanulási folyamatot az Azure Machine Learning SDK pythonhoz. A gépi tanulási fázisokat összefűző munkafolyamatok létrehozásához és kezeléséhez gépi tanulási fázisokat hozhat létre és kezelhet. Ezek a fázisok közé tartozik az adatok előkészítése, modell betanítása, modell üzembe helyezése és következtetés/pontozási.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.author: sanpil
author: sanpil
ms.date: 12/05/2019
ms.custom: seodec18
ms.openlocfilehash: fa0a5bfe921687ad964e9321e3874de37ccf9b98
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80549300"
---
# <a name="create-and-run-machine-learning-pipelines-with-azure-machine-learning-sdk"></a>Gépi tanulási folyamatok létrehozása és futtatása az Azure Machine Learning SDK-val
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebben a cikkben megtudhatja, hogyan hozhat létre, tehet közzé, futtathat és követhet nyomon egy [gépi tanulási folyamatot](concept-ml-pipelines.md) az [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)használatával.  Az **ML-folyamatok** segítségével hozzon létre egy munkafolyamatot, amely összefűzi a különböző gépi tanulási fázisokat, majd tegye közzé, hogy a folyamat az Azure Machine Learning-munkaterületen későbbi eléréséhez vagy másokkal való megosztáshoz.  A gépi tanulási folyamatok ideálisak a kötegelt pontozási forgatókönyvekhez, különböző számítási műveletekhez, a lépések újrafelhasználásához azok újrafuttatása helyett, valamint az ML-munkafolyamatok másokkal való megosztásához.

Bár egy másfajta folyamat, az [Úgynevezett Azure Pipeline](https://docs.microsoft.com/azure/devops/pipelines/targets/azure-machine-learning?context=azure%2Fmachine-learning%2Fservice%2Fcontext%2Fml-context&view=azure-devops&tabs=yaml) a CI/CD automatizálása a gépi tanulási feladatok, az ilyen típusú folyamat soha nem tárolja a munkaterületen belül. [Hasonlítsa össze ezeket a különböző csővezetékeket](concept-ml-pipelines.md#which-azure-pipeline-technology-should-i-use).

A rendszermegtalálható klúfzolés-előkészítő és modellbetanítási folyamatok minden fázisa egy vagy több lépést tartalmazhat.

A létrehozott GÉP-folyamatok az Azure Machine Learning-munkaterület tagjai számára [láthatók.](how-to-manage-workspace.md) 

A rendszer-összeszolgáltatási folyamatok távoli számítási célokat használnak a számításhoz és a folyamathoz kapcsolódó köztes és végső adatok tárolásához. A támogatott [Azure Storage-helyekre](https://docs.microsoft.com/azure/storage/) és azokból adatokat olvashatnak és írhatnak.

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy ingyenes fiókot. Próbálja ki az [Azure Machine Learning ingyenes vagy fizetős verzióját.](https://aka.ms/AMLFree)

## <a name="prerequisites"></a>Előfeltételek

* Hozzon létre egy [Azure Machine Learning-munkaterületet](how-to-manage-workspace.md) az összes folyamaterőforrás tárolására.

* [Konfigurálja a fejlesztői környezetet](how-to-configure-environment.md) az Azure Machine Learning SDK telepítéséhez, vagy használjon [egy Azure Machine Learning-számítási példányt (előzetes verziót)](concept-compute-instance.md) az SDK már telepítve.

Először csatolja a munkaterületet:

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```


## <a name="set-up-machine-learning-resources"></a>Gépi tanulási erőforrások beállítása

A hibajelző folyamat futtatásához szükséges erőforrások létrehozása:

* Állítsa be a folyamatlépésekben szükséges adatok eléréséhez használt adattár.

* Konfiguráljon `DataReference` úgy egy objektumot, hogy olyan adatokra mutasson, amelyek egy adattárban élnek vagy azokban érhetők el.

* Állítsa be azokat a [számítási célokat,](concept-azure-machine-learning-architecture.md#compute-targets) amelyeken a folyamat lépései futni fognak.

### <a name="set-up-a-datastore"></a>Adattár beállítása

Az adattár tárolja a folyamat hoz való hozzáférés hez vezető adatokat. Minden munkaterület rendelkezik egy alapértelmezett adattár. További adattárak regisztrálhatók. 

A munkaterület létrehozásakor az [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) és az Azure [Blob storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) a munkaterülethez csatlakozik. Az azure blob storage-hoz való csatlakozáshoz egy alapértelmezett adattár van regisztrálva. További információ: [Az Azure Files, az Azure Blobs vagy az Azure Disks használatának eldöntése.](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks) 

```python
# Default datastore 
def_data_store = ws.get_default_datastore()

# Get the blob storage associated with the workspace
def_blob_store = Datastore(ws, "workspaceblobstore")

# Get file storage associated with the workspace
def_file_store = Datastore(ws, "workspacefilestore")

```

Töltsön fel adatfájlokat vagy könyvtárakat az adattárba, hogy azok elérhetők legyenek a folyamatokból. Ez a példa a Blob storage-ot használja adattárként:

```python
def_blob_store.upload_files(
    ["./data/20news.pkl"],
    target_path="20newsgroups",
    overwrite=True)
```

A folyamat egy vagy több lépésből áll. A lépés egy számítási célon futó egység. A lépések adatforrásokat vehetnek fel, és "köztes" adatokat eredményezhetnek. Egy lépés hozhat létre adatokat, például egy modell, egy könyvtár modell és a függő fájlokat, vagy ideiglenes adatokat. Ezek az adatok ezután a folyamat későbbi lépéseihez érhetők el.

Ha többet szeretne tudni a folyamat adatokhoz való csatlakoztatásáról, olvassa el az [Adatok elérése](how-to-access-data.md) és az [adatkészletek regisztrálása című cikkeket.](how-to-create-register-datasets.md) 

### <a name="configure-data-reference"></a>Adathivatkozás konfigurálása

Most létrehozott egy adatforrást, amely egy folyamatban egy lépés bemeneteként hivatkozhat. A folyamat adatforrását [datareference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference) objektum jelöli. Az `DataReference` objektum olyan adatokra mutat, amelyek egy adattárban élnek vagy érhetők el.

```python
from azureml.data.data_reference import DataReference

blob_input_data = DataReference(
    datastore=def_blob_store,
    data_reference_name="test_data",
    path_on_datastore="20newsgroups/20news.pkl")
```

A köztes adatokat (vagy egy lépés kimenetét) egy [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) objektum képviseli. `output_data1`egy lépés kimeneteként készül, és egy vagy több jövőbeli lépés bemeneteként használható. `PipelineData`adatfüggőséget vezet be a lépések között, és implicit végrehajtási rendelést hoz létre a folyamatban. Ez az objektum később lesz használva a folyamatlépések létrehozásakor.

```python
from azureml.pipeline.core import PipelineData

output_data1 = PipelineData(
    "output_data1",
    datastore=def_blob_store,
    output_name="output_data1")
```

### <a name="configure-data-using-datasets"></a>Adatok konfigurálása adatkészletek használatával

Ha táblázatos adatokat tárol egy fájlban vagy fájlhalmazban, a [táblázatos adatkészlet](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) `DataReference`hatékony alternatívája a . `TabularDataset`objektumok támogatják a verziószámozást, a különbözetet és az összesítő statisztikákat. `TabularDataset`s lustán értékelik (mint a Python generátorok), és ez hatékony, hogy részre bontási vagy szűrés. Az `FileDataset` osztály hasonló lustán kiértékelt adatokat biztosít, amelyek egy vagy több fájlt jelölnek. 

Hozzon `TabularDataset` létre egy olyan módszereket, mint [a from_delimited_files](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header-true--partition-format-none--support-multi-line-false-).

```python
from azureml.data import TabularDataset

iris_tabular_dataset = Dataset.Tabular.from_delimited_files([(def_blob_store, 'train-dataset/tabular/iris.csv')])
```

 A `FileDataset` [from_files](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-)használatával hoz létre.

 Az adatkészletek [munkálatairól az & regiszteradatkészletek hozzáadása](how-to-create-register-datasets.md) vagy [ez a mintajegyzetfüzet](https://aka.ms/train-datasets)ismerteti.

## <a name="set-up-compute-target"></a>Számítási cél beállítása

Az Azure Machine Learningben a __számítási__ kifejezés (vagy __számítási cél)__ azokra a gépekre vagy fürtökre utal, amelyek a gépi tanulási folyamat számítási lépéseit hajtják végre.   Tekintse meg [a számítási célok modell képzés](how-to-set-up-training-targets.md) teljes listáját számítási célok és hogyan lehet létrehozni, és csatolja őket a munkaterülethez.  A számítási cél létrehozásának és csatolásának folyamata ugyanaz, függetlenül attól, hogy modellt bead-e vagy folyamatlépést futtat. Miután létrehozta és csatlakoztatta a `ComputeTarget` számítási célt, használja a [folyamatlépésben lévő objektumot.](#steps)

> [!IMPORTANT]
> A számítási célokon végzett felügyeleti műveletek nem támogatottak a távoli feladatokon belülről. Mivel a gépi tanulási folyamatok távoli feladatként kerülnek elküldésre, ne használja a felügyeleti műveleteket a folyamaton belülről számított célokon.

Az alábbiakban példákat mutatunk be számítási célok létrehozására és csatolására:

* Azure Machine Learning Compute
* Azure Databricks 
* Azure Data Lake Analytics

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning-számítás

Létrehozhat egy Azure Machine Learning-számítást a lépések futtatásához.

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

Az Azure Databricks egy Apache Spark-alapú környezet az Azure-felhőben. Számítási célként használható egy Azure Machine Learning-folyamattal.

Hozzon létre egy Azure Databricks-munkaterületet használat előtt. Munkaterületi erőforrás létrehozásához tekintse meg a [Spark-feladat futtatása az Azure Databricks-dokumentumban.](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)

Az Azure Databricks számítási célként való csatolásához adja meg a következő információkat:

* __Databricks számítási név:__ A számítási erőforráshoz rendelni kívánt név.
* __Databricks munkaterület neve:__ Az Azure Databricks munkaterület neve.
* __Databricks hozzáférési jogkivonat:__ Az Azure Databricks hitelesítéséhez használt hozzáférési jogkivonat. Hozzáférési jogkivonat létrehozásához tekintse meg a [hitelesítési](https://docs.azuredatabricks.net/dev-tools/api/latest/authentication.html) dokumentumot.

A következő kód bemutatja, hogyan csatolhatja az Azure Databricks számítási célként az Azure Machine Learning SDK __(A Databricks munkaterület jelen kell lennie ugyanabban az előfizetésben, mint az AML-munkaterület):__

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

Részletesebb példa: egy [példa jegyzetfüzet a](https://aka.ms/pl-databricks) GitHubon.

### <a name="azure-data-lake-analytics"></a><a id="adla"></a>Azure Data Lake Analytics

Az Azure Data Lake Analytics egy big data-elemzési platform az Azure-felhőben. Számítási célként használható egy Azure Machine Learning-folyamattal.

Hozzon létre egy Azure Data Lake Analytics-fiókot, mielőtt használná. Az erőforrás létrehozásához tekintse meg az [Azure Data Lake Analytics –első lépések](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal) című dokumentumot.

A Data Lake Analytics számítási célként való csatolásához az Azure Machine Learning SDK-t kell használnia, és a következő információkat kell megadnia:

* __Számítási név:__ A számítási erőforráshoz rendelni kívánt név.
* __Erőforráscsoport__: A Data Lake Analytics-fiókot tartalmazó erőforráscsoport.
* __Fióknév__: A Data Lake Analytics-fiók neve.

A következő kód bemutatja, hogyan csatolható a Data Lake Analytics számítási célként:

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

Részletesebb példa: egy [példa jegyzetfüzet a](https://aka.ms/pl-adla) GitHubon.

> [!TIP]
> Az Azure Machine Learning-folyamatok csak a Data Lake Analytics-fiók alapértelmezett adattárában tárolt adatokkal működhetnek. Ha a dolgozni kívánt adatok nem alapértelmezett tárolóban vannak, [`DataTransferStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) a segítségével átmásolhatja az adatokat a betanítás előtt.

## <a name="construct-your-pipeline-steps"></a><a id="steps"></a>A folyamat lépéseinek megépítése

Miután létrehozott és csatolt egy számítási célt a munkaterülethez, készen áll egy folyamatlépés definiálására. Az Azure Machine Learning SDK-n keresztül számos beépített lépés érhető el. A lépések legalapvetőbb része a [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py), amely egy Python-parancsfájlt futtat egy megadott számítási célban:

```python
from azureml.pipeline.steps import PythonScriptStep

trainStep = PythonScriptStep(
    script_name="train.py",
    arguments=["--input", blob_input_data, "--output", output_data1],
    inputs=[blob_input_data],
    outputs=[output_data1],
    compute_target=compute_target,
    source_directory=project_folder
)
```

A korábbi eredmények`allow_reuse`( ) újrafelhasználása kulcsfontosságú a folyamatok együttműködési környezetben való használatakor, mivel a szükségtelen ismétlések kiküszöbölése agilitást kínál. Az újrafelhasználás az alapértelmezett viselkedés, ha a script_name, bemenetek és a lépés paraméterei változatlanok maradnak. A lépés kimenetének újbóli felszámításakor a feladat nem kerül elküldésre a számítási, hanem az előző futtatás eredményei azonnal elérhetők a következő lépés futtatása. Ha `allow_reuse` hamis, egy új futtatás mindig jön létre ehhez a lépéshez a folyamat végrehajtása során. 

Miután definiálja a lépéseket, néhány vagy az összes lépéssel hozhatja létre a folyamatot.

> [!NOTE]
> A lépések definiálásakor vagy a folyamat létrehozásakor a rendszer nem tölt fel fájlt vagy adatokat az Azure Machine Learningbe.

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

Ha `TabularDataset` a-t `FileDataset` vagy a folyamatot szeretné használni, a [as_named_input(name)](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset?view=azure-ml-py#as-named-input-name-)hívásával [datasetConsumptionconfig](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_consumption_config.datasetconsumptionconfig?view=azure-ml-py) objektummá kell alakítania. Ezt az `DatasetConsumptionConfig` objektumot a `inputs` folyamatlépés egyiklépéseként adja át. 

Az Azure Blob storage, az Azure Files, az Azure Data Lake Storage Gen1, az Azure Data Lake Storage Gen2, az Azure SQL Database és az Azure Database for PostgreSQL által létrehozott adatkészletek bármely folyamatlépés beviteleként használhatók. A [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py) vagy [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py)kimenetírásának kivételével a kimeneti adatok ([PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)) csak az Azure Blob és az Azure File share datastores-ba írhatók.

```python
dataset_consuming_step = PythonScriptStep(
    script_name="iris_train.py",
    inputs=[iris_tabular_dataset.as_named_input("iris_data")],
    compute_target=compute_target,
    source_directory=project_folder
)
```

Ezután a [Run.input_datasets](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#input-datasets) szótár használatával beolvassa a folyamat adatkészletét.

```python
# iris_train.py
from azureml.core import Run, Dataset

run_context = Run.get_context()
iris_dataset = run_context.input_datasets['iris_data']
dataframe = iris_dataset.to_pandas_dataframe()
```

További információkért tekintse meg az [azure-pipeline-steps csomag](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) és [a pipeline osztály](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py) referencia.

## <a name="submit-the-pipeline"></a>A folyamat beküldése

A folyamat elküldésekén az Azure Machine Learning ellenőrzi az egyes lépésekkel kapcsolatos függőségeket, és feltölti a megadott forráskönyvtár pillanatképét. Ha nincs megadva forráskönyvtár, a rendszer feltölti az aktuális helyi könyvtárat. A pillanatkép is tárolja a kísérlet részeként a munkaterületen.

> [!IMPORTANT]
> Ha meg szeretné akadályozni, hogy a fájlok bekerüljenek a pillanatképbe, hozzon létre egy [.gitignore](https://git-scm.com/docs/gitignore) vagy `.amlignore` fájlt a könyvtárban, és adja hozzá a fájlokat. A `.amlignore` fájl ugyanazt a szintaxist és mintázatot használja, mint a [.gitignore](https://git-scm.com/docs/gitignore) fájl. Ha mindkét fájl `.amlignore` létezik, a fájl élvez elsőbbséget.
>
> További információ: [Pillanatképek](concept-azure-machine-learning-architecture.md#snapshots).

```python
from azureml.core import Experiment

# Submit the pipeline to be run
pipeline_run1 = Experiment(ws, 'Compare_Models_Exp').submit(pipeline1)
pipeline_run1.wait_for_completion()
```

Folyamat első futtatásakor az Azure Machine Learning:

* Letölti a projekt pillanatképét a számítási célra a munkaterülethez társított Blob-tárterületről.
* A folyamat minden lépésének megfelelő Docker-rendszerképet hoz létre.
* Letölti a Docker-rendszerképet a számítási cél minden egyes lépéséhez a tároló beállításjegyzékből.
* Az adattárolót akkor `DataReference` csatlakoztatja, ha egy objektum meg van adva egy lépésben. Ha a csatlakoztatás nem támogatott, az adatok at a számítási célba másolja.
* Futtatja a lépés a lépésdefinícióban megadott számítási cél. 
* A lépés által megadott összetevőket hoz létre, például naplókat, stdout és stderr, metrikákat és kimeneteket. Ezeket az összetevőket ezután feltölti és tárolja a felhasználó alapértelmezett adattárában.

![Kísérlet folyamatként való futtatásának diagramja](./media/how-to-create-your-first-pipeline/run_an_experiment_as_a_pipeline.png)

További információt a [Kísérlet osztály](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py) hivatkozási számban talál.

### <a name="view-results-of-a-pipeline"></a>Folyamat eredményeinek megtekintése

Tekintse meg az összes csővezeték listáját és azok futtatásának részleteit a stúdióban:

1. Jelentkezzen be az [Azure Machine Learning stúdióba.](https://ml.azure.com)

1. [Tekintse meg a munkaterületet](how-to-manage-workspace.md#view).

1. A bal oldalon válassza a folyamatok az összes folyamat **futtatása.**
 ![Gépi tanulási folyamatok listája](./media/how-to-create-your-first-pipeline/pipelines.png)
 
1. Válasszon ki egy adott folyamatot a futtatási eredmények megtekintéséhez.

## <a name="git-tracking-and-integration"></a>Git-követés és integráció

Amikor elindítja a betanítási futtatás, ahol a forráskönyvtár egy helyi Git-tárház, a tárház adatait a futtatási előzmények tárolja. További információ: [Git integration for Azure Machine Learning.](concept-train-model-git-integration.md)

## <a name="publish-a-pipeline"></a>Folyamat közzététele

Közzétehet egy folyamatot, hogy később különböző bemenetekkel futtassa. Ahhoz, hogy egy már közzétett folyamat REST-végpontja paramétereket fogadjon el, a közzététel előtt paramétereznie kell a folyamatot.

1. Folyamatparaméter létrehozásához használjon egy alapértelmezett értékű [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py) objektumot.

   ```python
   from azureml.pipeline.core.graph import PipelineParameter
   
   pipeline_param = PipelineParameter(
     name="pipeline_arg",
     default_value=10)
   ```

2. Adja `PipelineParameter` hozzá ezt az objektumot paraméterként a folyamat bármely lépéséhez az alábbiak szerint:

   ```python
   compareStep = PythonScriptStep(
     script_name="compare.py",
     arguments=["--comp_data1", comp_data1, "--comp_data2", comp_data2, "--output_data", out_data3, "--param1", pipeline_param],
     inputs=[ comp_data1, comp_data2],
     outputs=[out_data3],
     compute_target=compute_target,
     source_directory=project_folder)
   ```

3. A folyamat közzététele, amely meghíváskor elfogadja a paramétert.

   ```python
   published_pipeline1 = pipeline_run1.publish_pipeline(
        name="My_Published_Pipeline",
        description="My Published Pipeline Description",
        version="1.0")
   ```

### <a name="run-a-published-pipeline"></a>Közzétett folyamat futtatása

Minden közzétett folyamat rendelkezik EGY REST-végpont. Ez a végpont meghívja a folyamat futtatását külső rendszerekről, például nem Python-ügyfelekről. Ez a végpont lehetővé teszi a "felügyelt ismételhetőséget" a kötegelt pontozási és átképzési forgatókönyvekben.

Az előző folyamat futtatásához egy Azure Active Directory-hitelesítési fejléctokenre van szüksége, az [AzureCliAuthentication osztályhivatkozásban](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py) leírtak szerint, vagy további részletekre van szüksége az [Azure Machine Learning-jegyzetfüzethitelesítésben.](https://aka.ms/pl-restep-auth)

```python
from azureml.pipeline.core import PublishedPipeline
import requests

response = requests.post(published_pipeline1.endpoint,
                         headers=aad_token,
                         json={"ExperimentName": "My_Pipeline",
                               "ParameterAssignments": {"pipeline_arg": 20}})
```

## <a name="create-a-versioned-pipeline-endpoint"></a>Verziós folyamatvégpont létrehozása
Létrehozhat egy folyamatvégpontot több közzétett folyamattal a háta mögött. Ez használható, mint egy közzétett folyamat, de ad egy rögzített REST-végpont, ahogy iterálni, és frissíti a rendszermegtalálható folyamatok.

```python
from azureml.pipeline.core import PipelineEndpoint

published_pipeline = PublishedPipeline.get(workspace="ws", name="My_Published_Pipeline")
pipeline_endpoint = PipelineEndpoint.publish(workspace=ws, name="PipelineEndpointTest",
                                            pipeline=published_pipeline, description="Test description Notebook")
```

### <a name="submit-a-job-to-a-pipeline-endpoint"></a>Feladat elküldése folyamatvégpontba
Elküldheti a feladatot a folyamatvégpont alapértelmezett verziójához:
```python
pipeline_endpoint_by_name = PipelineEndpoint.get(workspace=ws, name="PipelineEndpointTest")
run_id = pipeline_endpoint_by_name.submit("PipelineEndpointExperiment")
print(run_id)
```
Egy feladatot egy adott verzióra is beküldhet:
```python
run_id = pipeline_endpoint_by_name.submit("PipelineEndpointExperiment", pipeline_version="0")
print(run_id)
```

Ugyanez a REST API használatával valósítható meg:
```python
rest_endpoint = pipeline_endpoint_by_name.endpoint
response = requests.post(rest_endpoint, 
                         headers=aad_token, 
                         json={"ExperimentName": "PipelineEndpointExperiment",
                               "RunSource": "API",
                               "ParameterAssignments": {"1": "united", "2":"city"}})
```

### <a name="use-published-pipelines-in-the-studio"></a>Közzétett folyamatok használata a stúdióban

A közzétett folyamatot a stúdióból is futtathatja:

1. Jelentkezzen be az [Azure Machine Learning stúdióba.](https://ml.azure.com)

1. [Tekintse meg a munkaterületet](how-to-manage-workspace.md#view).

1. A bal oldalon válassza a **Végpontok**lehetőséget.

1. A tetején válassza a **Folyamat végpontja lehetőséget.**
 ![machine learning közzétett folyamatainak listája](./media/how-to-create-your-first-pipeline/pipeline-endpoints.png)

1. Válassza ki a folyamatvégpont korábbi futtatásainak eredményeit futtatni, felhasználni vagy áttekinteni egy adott folyamatot.


### <a name="disable-a-published-pipeline"></a>Közzétett folyamat letiltása

Ha el szeretne rejteni egy folyamatot a közzétett folyamatok listájáról, tiltsa le azt a stúdióban vagy az SDK-ból:

```
# Get the pipeline by using its ID from Azure Machine Learning studio
p = PublishedPipeline.get(ws, id="068f4885-7088-424b-8ce2-eeb9ba5381a6")
p.disable()
```

A segítségével újra `p.enable()`engedélyezheti. További információ: [PublishedPipeline osztály](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.publishedpipeline?view=azure-ml-py) referencia.


## <a name="caching--reuse"></a>Gyorsítótárazás & újrafelhasználás  

A folyamatok viselkedésének optimalizálása és testreszabása érdekében néhány dolgot megtehet a gyorsítótárazás és az újrafelhasználás körül. Például a következőket választhatja:
+ **Kapcsolja ki a lépésfuttatási kimenet alapértelmezett újrafelhasználását a** `allow_reuse=False` [lépésdefiníció](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py)során történő beállítással. Az újrafelhasználás kulcsfontosságú a folyamatok együttműködési környezetben való használatakor, mivel a szükségtelen futtatások kiküszöbölése agilitást biztosít. Az újrafelhasználást azonban letilthatja.
+ **A kimenetregenerálás kényszerítése az összes lépésnél**`pipeline_run = exp.submit(pipeline, regenerate_outputs=False)`

Alapértelmezés szerint `allow_reuse` a lépések engedélyezve vannak, és a `source_directory` megadott lépésdefiníció kivonat. Így ha egy adott lépés parancsfájlja`script_name`ugyanaz marad ( , bemenetek és a` source_directory` paraméterek), és semmi más nem változott, az előző lépésfuttatás kimenetét újra felhasználja, a feladat nem kerül elküldésre a számítási, és az eredmények az előző futtatás azonnal elérhető a következő lépés helyett.

```python
step = PythonScriptStep(name="Hello World",
                        script_name="hello_world.py",
                        compute_target=aml_compute,
                        source_directory=source_directory,
                        allow_reuse=False,
                        hash_paths=['hello_world.ipynb'])
```

## <a name="next-steps"></a>További lépések

- Használja [ezeket a Jupyter-jegyzetfüzeteket a GitHubon a](https://aka.ms/aml-pipeline-readme) gépi tanulási folyamatok további megismeréséhez.
- Tekintse meg az SDK referencia súgóját az [azureml-pipelines-core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) csomaghoz és az [azureml-pipelines-steps](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) csomaghoz.
- Tekintse meg a [folyamatokkal](how-to-debug-pipelines.md) kapcsolatos tippeket a hibakereséshez és a hibaelhárításhoz.

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]
