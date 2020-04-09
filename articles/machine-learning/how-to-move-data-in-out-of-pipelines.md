---
title: Bemeneti és kimeneti adatok a pénzmosási folyamatokból
titleSuffix: Azure Machine Learning
description: Adatok előkészítése, felhasználása és létrehozása az Azure Machine Learning-folyamatokban
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 04/01/2020
ms.openlocfilehash: 3dd1a82bf7fad1f201f5c0f52af944ef44a3fdf9
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879764"
---
# <a name="moving-data-into-and-between-ml-pipeline-steps-python"></a>Adatok áthelyezése az ML-folyamat lépéseibe és között (Python)

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Az adatok központi szerepet játszanak a gépi tanulási folyamatokban. Ez a cikk az Azure Machine Learning-folyamat lépései közötti adatok importálására, átalakítására és áthelyezésére szolgáló kódot tartalmaz. Az Azure Machine Learning ben az adatok működésének áttekintése az [Access-adatok az Azure storage-szolgáltatásokban című témakörben található.](how-to-access-data.md) Az Azure Machine Learning-folyamatok előnyeiről és struktúrájáról a [Mik azok az Azure Machine Learning-folyamatok.](concept-ml-pipelines.md)

Ez a cikk megmutatja, hogyan:

- Objektumok `Dataset` használata már meglévő adatokhoz
- Adatok elérése a lépéseken belül
- Adatok `Dataset` felosztása részhalmazokra, például betanítási és érvényesítési részhalmazokra
- Objektumok `PipelineData` létrehozása az adatok átviteléhez a következő folyamatlépésbe
- Objektumok `PipelineData` használata folyamatlépések bemeneteként
- Új `Dataset` objektumok `PipelineData` létrehozása a megmaradni kívánt objektumokból

## <a name="prerequisites"></a>Előfeltételek

A következők szükségesek:

- Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy ingyenes fiókot. Próbálja ki az [Azure Machine Learning ingyenes vagy fizetős verzióját.](https://aka.ms/AMLFree)

- Az [Azure Machine Learning SDK pythonhoz](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py), vagy az Azure Machine Learning [stúdióhoz](https://ml.azure.com/)való hozzáférés.

- Egy Azure Machine Learning-munkaterület.
  
  [Hozzon létre egy Azure Machine Learning-munkaterületet,](how-to-manage-workspace.md) vagy használjon egy meglévőt a Python SDK-n keresztül. Importálja `Workspace` `Datastore` az osztályt és az osztályt, és töltse be az előfizetésadatait a fájlból `config.json` a funkció használatával. `from_config()` Ez a függvény alapértelmezés szerint az aktuális könyvtárban keresi a JSON-fájlt, de `from_config(path="your/file/path")`megadhat egy elérési utat is, amely a használatával a fájlra mutat.

   ```python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

- Néhány már létező adat. Ez a cikk röviden bemutatja egy [Azure blob tároló](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)használatát.

- Nem kötelező: Egy meglévő gépi tanulási folyamat, például az [Azure Machine Learning SDK-val gépi tanulási folyamatok létrehozása és futtatása](how-to-create-your-first-pipeline.md)című dokumentumban ismertetett.

## <a name="use-dataset-objects-for-pre-existing-data"></a>Objektumok `Dataset` használata már meglévő adatokhoz 

Az adatok folyamatba történő betöltésének előnyben részesített módja egy [Adatkészlet-objektum](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py) használata. `Dataset`objektumok a munkaterület egészterületén elérhető állandó adatokat jelölik.

Az objektumok létrehozásának és `Dataset` regisztrálásának számos módja van. A táblázatos adatkészletek egy vagy több fájlban elérhető, tagolt adatokra szolgálnak. A fájladatkészletek bináris adatokhoz (például képekhez) vagy az elemzésre szánt adatokhoz tartoznak. Az objektumok létrehozásának `Dataset` legegyszerűbb programozott módja a meglévő blobok használata a munkaterületi tárolóban vagy nyilvános URL-címekben:

```python
datastore = Datastore.get(workspace, 'training_data')
iris_dataset = Dataset.Tabular.from_delimited_files(DataPath(datastore, 'iris.csv'))

cats_dogs_dataset = Dataset.File.from_files(
    paths='https://download.microsoft.com/download/3/E/1/3E1C3F21-ECDB-4869-8368-6DEBA77B919F/kagglecatsanddogs_3367a.zip',
    archive_options=ArchiveOptions(archive_type=ArchiveType.ZIP, entry_glob='**/*.jpg')
)
```

A különböző beállításokkal és különböző forrásból származó adatkészletek létrehozásával, regisztrációjukkal és az Azure Machine Learning felhasználói felületén való áttekintésükkel, az adatok méretének a számítási kapacitással való kölcsönhatásának megértésével és verziószámozásával kapcsolatos további lehetőségekről az [Azure Machine Learning-adatkészletek létrehozása](how-to-create-register-datasets.md)című témakörben található. 

### <a name="pass-datasets-to-your-script"></a>Adatkészletek áttöltése a parancsfájlhoz

Az adatkészlet elérési útjának a parancsfájlba `Dataset` való `as_named_input()` átadásához használja az objektum metódusát. Az eredményül kapott `DatasetConsumptionConfig` objektumot argumentumként átadhatja `inputs` a parancsfájlnak, vagy az argumentumot `Run.get_context().input_datasets[]`a folyamatparancsfájlhoz használva a használatával bekeresheti az adatkészletet a használatával.

Miután létrehozott egy elnevezett bemenetet, kiválaszthatja `as_mount()` `as_download()`annak hozzáférési módját: vagy . Ha a parancsfájl feldolgozza az adatkészlet összes fájlját, és a számítási erőforráson lévő lemez elég nagy az adatkészlethez, a letöltési hozzáférési mód a jobb választás. A letöltési hozzáférési mód elkerüli az adatok futásidőben való streamelésének többletterhelését. Ha a parancsfájl hozzáfér az adatkészlet egy részhalmazához, vagy túl nagy a számítási, használja a csatlakoztatási hozzáférési módot. További információ: [Mount vs. Download](https://docs.microsoft.com/azure/machine-learning/how-to-train-with-datasets#mount-vs-download)

Adatkészlet átadása a folyamatlépésnek:

1. Objektum létrehozásához `TabularDataset.as_named_inputs()` használja vagy `FileDataset.as_named_input()` (nincs 's) `DatasetConsumptionConfig`
1. A `as_mount()` `as_download()` hozzáférési mód használata vagy beállítása
1. Adja át az adatkészleteket a `arguments` folyamatlépéseknek az argumentum vagy a `inputs` argumentum használatával

A következő kódrészlet a `PythonScriptStep` lépések konstruktoron belüli egyesítésének közös mintáját mutatja be: 

```python

train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    inputs=[iris_dataset.as_named_inputs('iris').as_mount()]
)
```

Olyan módszereket is `random_split()` használhat, mint például több bemenet létrehozása, `take_sample()` vagy csökkentheti a folyamatlépésnek átadott adatok mennyiségét:

```python
seed = 42 # PRNG seed
smaller_dataset = iris_dataset.take_sample(0.1, seed=seed) # 10%
train, test = smaller_dataset.random_split(percentage=0.8, seed=seed)

train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    inputs=[train.as_named_inputs('train').as_download(), test.as_named_inputs('test').as_download()]
)
```

### <a name="access-datasets-within-your-script"></a>Adatkészletek elérése a parancsfájlon belül

A folyamatlépés-parancsfájl névvel ellátott bemenetei `Run` szótárként érhetők el az objektumon belül. Az aktív `Run` objektum `Run.get_context()` beolvasása a használatával, majd `input_datasets`az elnevezett bemenetek szótárának beolvasása a használatával. Ha az `DatasetConsumptionConfig` objektumot `arguments` argumentum helyett `inputs` argumentummal adta `ArgParser` át, akkor az adatokat kód használatával érheti el. Mindkét technikát a következő kódrészlet mutatja be.

```python
# In pipeline definition script:
# Code for demonstration only: It would be very confusing to split datasets between `arguments` and `inputs`
train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    arguments=['--training-folder', train.as_named_inputs('train').as_download()]
    inputs=[test.as_named_inputs('test').as_download()]
)

# In pipeline script
parser = argparse.ArgumentParser()
parser.add_argument('--training-folder', type=str, dest='train_folder', help='training data folder mounting point')
args = parser.parse_args()
training_data_folder = args.train_folder

testing_data_folder = Run.get_context().input_datasets['test']
```

Az átadott érték az adatkészletfájl(ok) elérési útja lesz.

Az is lehetséges, hogy `Dataset` hozzáférjen a regisztrált közvetlenül. Mivel a regisztrált adatkészletek állandóak és megosztottak egy munkaterületen, közvetlenül lekérheti őket:

```python
run = Run.get_context()
ws = run.experiment.workspace
ds = Dataset.get_by_name(workspace=ws, name='mnist_opendataset')
```

## <a name="use-pipelinedata-for-intermediate-data"></a>Köztes `PipelineData` adatok használata

Míg `Dataset` az objektumok állandó adatokat jelölnek, [a PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) objektumok ideiglenes adatokhoz használatosak, amelyek a folyamatlépésekből kerülnek ki. Mivel egy `PipelineData` objektum élettartama hosszabb, mint egy folyamatlépés, definiálja őket a folyamatdefiníciós parancsfájlban. Objektum létrehozásakor `PipelineData` meg kell adnia egy nevet és egy adattárolót, ahol az adatok elfognak kerülni. Adja `PipelineData` át az objektum(oka)t `outputs` a `arguments` `PythonScriptStep` _következők_ használatával:

```python
default_datastore = workspace.get_default_datastore()
dataprep_output = PipelineData("clean_data", datastore=default_datastore)

dataprep_step = PythonScriptStep(
    name="prep_data",
    script_name="dataprep.py",
    compute_target=cluster,
    arguments=["--output-path", dataprep_output]
    inputs=[Dataset.get_by_name(workspace, 'raw_data')],
    outputs=[dataprep_output]
)
```

Dönthet úgy, hogy `PipelineData` az objektumot azonnali feltöltést lehetővé tőeszközként hozza létre. Ebben az esetben `PipelineData`a létrehozásakor `upload_mode` állítsa `"upload"` be `output_path_on_compute` a kívánt értéket, és használja az argumentumot az adatok írási útvonalának megadásához:

```python
PipelineData("clean_data", datastore=def_blob_store, output_mode="upload", output_path_on_compute="clean_data_output/")
```

### <a name="use-pipelinedata-as-outputs-of-a-training-step"></a>Betanítási lépés kimeneteként való használat `PipelineData`

A folyamaton `PythonScriptStep`belül a program argumentumai alapján bekeresheti a rendelkezésre álló kimeneti útvonalakat. Ha ez a lépés az első, és inicializálja a kimeneti adatokat, létre kell hoznia a könyvtárat a megadott elérési úton. Ezután bármilyen fájlt írhat a rendszerbe, `PipelineData`amelyet szeretne.

```python
parser = argparse.ArgumentParser()
parser.add_argument('--output_path', dest='output_path', required=True)
args = parser.parse_args()

# Make directory for file
os.makedirs(os.path.dirname(args.output_path), exist_ok=True)
with open(args.output_path, 'w') as f:
    f.write("Step 1's output")
```

Ha létrehozta `PipelineData` a `is_directory` az argumentumot, hogy `True`, elég `os.makedirs()` lenne, hogy csak végre a hívást, és akkor lenne szabad írni bármilyen fájlt szeretne az utat. További részletek [értelése: PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) referencia dokumentáció.

### <a name="read-pipelinedata-as-inputs-to-non-initial-steps"></a>Olvasás `PipelineData` nem kezdeti lépések bemeneteként

Miután a kezdeti folyamatlépés bizonyos `PipelineData` adatokat ír az elérési útra, és a kezdeti lépés kimeneté válik, egy későbbi lépés bemeneteként használható:

```python
step1_output_data = PipelineData("processed_data", datastore=def_blob_store, output_mode="upload")

step1 = PythonScriptStep(
    name="generate_data",
    script_name="step1.py",
    runconfig = aml_run_config,
    arguments = ["--output_path", step1_output_data],
    inputs=[],
    outputs=[step1_output_data]
)

step2 = PythonScriptStep(
    name="read_pipeline_data",
    script_name="step2.py",
    compute_target=compute,
    runconfig = aml_run_config,
    arguments = ["--pd", step1_output_data],
    inputs=[step1_output_data]
)

pipeline = Pipeline(workspace=ws, steps=[step1, step2])
```

A `PipelineData` bemenet értéke az előző kimenet elérési útja. Ha, ahogy korábban látható, az első lépés egyetlen fájlt írt, a fogyasztása így nézhet ki: 

```python
parser = argparse.ArgumentParser()
parser.add_argument('--pd', dest='pd', required=True)
args = parser.parse_args()

with open(args.pd) as f:
    print(f.read())
```

## <a name="convert-pipelinedata-objects-to-datasets"></a>Objektumok `PipelineData` átalakítása `Dataset`s

Ha a futtatás időtartamánál `PipelineData` hosszabb ideig szeretné elérhetővé tenni `as_dataset()` a készüléket, `Dataset`a funkciójával konvertálhatja azt . Ezután regisztrálhatja `Dataset`a , így első osztályú állampolgár a munkaterületen. Mivel `PipelineData` az objektum nak minden alkalommal más elérési útja lesz, amikor `create_new_version` `True` a folyamat `Dataset` fut, `PipelineData` erősen ajánlott, hogy az objektumból létrehozott egy létrehozott eszköz regisztrálásakor állítsa be.

```python
step1_output_ds = step1_output_data.as_dataset()
step1_output_ds.register(name="processed_data", create_new_version=True)
```

## <a name="next-steps"></a>További lépések

* [Azure-beli gépi tanulási adatkészlet létrehozása](how-to-create-register-datasets.md)
* [Gépi tanulási folyamatok létrehozása és futtatása az Azure Machine Learning SDK-val](how-to-create-your-first-pipeline.md)
