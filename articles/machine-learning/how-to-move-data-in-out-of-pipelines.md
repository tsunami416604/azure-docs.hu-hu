---
title: Az adatmozgatás ML-folyamatokban
titleSuffix: Azure Machine Learning
description: Ismerkedjen meg Azure Machine Learning folyamatokban lévő adatok bemeneti & kimenetével.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 08/20/2020
ms.topic: conceptual
ms.custom: how-to, contperfq4, devx-track-python
ms.openlocfilehash: 1b6b5af2e6533c13165ae8253813a52b2c7ad261
ms.sourcegitcommit: afa1411c3fb2084cccc4262860aab4f0b5c994ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2020
ms.locfileid: "88756962"
---
# <a name="moving-data-into-and-between-ml-pipeline-steps-python"></a>Adatok áthelyezése gép tanulási folyamatok lépéseibe és azok között (Python)

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ez a cikk egy Azure Machine Learning folyamat lépései közötti adatimportálási,-átalakítási és-áthelyezési kódot tartalmaz. Az adatAzure Machine Learningi működésének áttekintését lásd: az [Azure Storage-szolgáltatásokban tárolt adathozzáférés](how-to-access-data.md). Azure Machine Learning folyamatok előnyeiről és struktúrájáról a [Mi a Azure Machine learning-folyamat?](concept-ml-pipelines.md)című témakörben olvashat.

Ez a cikk bemutatja, hogyan végezheti el a következőket:

- `Dataset`Objektumok használata már meglévő adatértékekhez
- A lépésein belüli hozzáférés adatai
- Az `Dataset` adat felosztása részhalmazokra, például betanítási és érvényesítési részhalmazokra
- Objektumok létrehozása az `OutputFileDatasetConfig` adatok átviteléhez a következő folyamat lépéséhez
- `OutputFileDatasetConfig`Objektumok használata bemenetként a folyamat lépéseihez
- Hozzon létre új `Dataset` objektumokat, `OutputFileDatasetConfig` amelyeket meg szeretne őrizni

> [!NOTE]
>A `OutputFileDatasetConfig` és az `OutputTabularDatasetConfig` osztályok kísérleti előzetes funkciók, és bármikor változhatnak.
>
>További információ: https://aka.ms/azuremlexperimental.

## <a name="prerequisites"></a>Előfeltételek

A következők szükségesek:

- Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy ingyenes fiókot, mielőtt hozzákezd. Próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree).

- A [Pythonhoz készült Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py), vagy a [Azure Machine learning studióhoz](https://ml.azure.com/)való hozzáférés.

- Egy Azure Machine Learning-munkaterület.
  
  [Hozzon létre egy Azure Machine learning munkaterületet](how-to-manage-workspace.md) , vagy használjon egy meglévőt a Python SDK-n keresztül. Importálja a `Workspace` és az `Datastore` osztályt, és töltse be az előfizetési adatokat a fájlból `config.json` a függvény használatával `from_config()` . Ez a függvény alapértelmezés szerint az aktuális könyvtárban keresi a JSON-fájlt, de megadhat egy Path paramétert is, amely a fájlra mutat `from_config(path="your/file/path")` .

   ```python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

- Néhány már meglévő adattal. Ez a cikk röviden bemutatja az [Azure Blob-tárolók](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)használatát.

- Nem kötelező: egy meglévő gépi tanulási folyamat, például egy, a [Machine learning-folyamatok létrehozása és futtatása Azure Machine learning SDK-val](how-to-create-your-first-pipeline.md)című témakörben leírtak szerint.

## <a name="use-dataset-objects-for-pre-existing-data"></a>`Dataset`Objektumok használata már meglévő adatértékekhez 

Az adatfolyamatok adatfeldolgozásának előnyben részesített módja egy [adatkészlet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py) -objektum használata. `Dataset` az objektumok az egész munkaterületen elérhető állandó adatértékeket jelölik.

Számos módon hozhatók létre és regisztrálhatók `Dataset` objektumok. A táblázatos adatkészletek egy vagy több fájlban elérhető tagolt adatokat foglalnak magukban. A fájl adatkészletei bináris adatokat (például képeket) vagy az elemezni kívánt adatokat használják. Az objektumok létrehozásának legegyszerűbb programozási módjai a `Dataset` meglévő Blobok használata a munkaterület-tárolóban vagy a nyilvános URL-címekben:

```python
datastore = Datastore.get(workspace, 'training_data')
iris_dataset = Dataset.Tabular.from_delimited_files(DataPath(datastore, 'iris.csv'))

cats_dogs_dataset = Dataset.File.from_files(
    paths='https://download.microsoft.com/download/3/E/1/3E1C3F21-ECDB-4869-8368-6DEBA77B919F/kagglecatsanddogs_3367a.zip',
    archive_options=ArchiveOptions(archive_type=ArchiveType.ZIP, entry_glob='**/*.jpg')
)
```

Az adatkészletek különböző lehetőségekkel és különböző forrásokból való létrehozásával, a regisztrálással és a Azure Machine Learning felhasználói felületen való áttekintésével, valamint az adatméretnek a számítási kapacitással való interakciójának és verziószámozásának megismerésével kapcsolatban lásd: [Azure Machine learning adatkészletek létrehozása](how-to-create-register-datasets.md). 

### <a name="pass-datasets-to-your-script"></a>Adatkészletek továbbítása a parancsfájlba

Ha át szeretné adni az adatkészlet elérési útját a parancsfájlhoz, használja az `Dataset` objektum `as_named_input()` metódusát. Átadhatja az eredményül kapott `DatasetConsumptionConfig` objektumot a parancsfájlnak argumentumként, vagy a `inputs` folyamat parancsfájljának argumentumának használatával lekérheti az adatkészletet a használatával `Run.get_context().input_datasets[]` .

Miután létrehozott egy elnevezett bemenetet, kiválaszthatja a hozzáférési módját: `as_mount()` vagy `as_download()` . Ha a szkript feldolgozza az adatkészlet összes fájlját, és a számítási erőforrás lemeze elég nagy az adatkészlet számára, a letöltési hozzáférési mód a jobb választás. A letöltési hozzáférési mód elkerüli az adatátviteli terhelést a futásidőben. Ha a parancsfájl az adatkészlet egy részhalmazára fér hozzá, vagy túl nagy a számítási feladatokhoz, használja a csatlakoztatási hozzáférési módot. További információért olvassa el a [Csatlakoztatás és letöltés](https://docs.microsoft.com/azure/machine-learning/how-to-train-with-datasets#mount-vs-download) című témakört.

Adatkészlet átadása a folyamat lépéseire:

1. `TabularDataset.as_named_inputs()` `FileDataset.as_named_input()` Objektum létrehozásához használja a (z) vagy a (nem) lehetőséget. `DatasetConsumptionConfig`
1. `as_mount()` `as_download()` Hozzáférési mód beállítása vagy használata
1. Adja át az adatkészleteket a folyamat lépéseire a `arguments` vagy a `inputs` argumentum használatával.

A következő kódrészlet a fenti lépések a konstruktorban való összekapcsolásának általános mintáját mutatja be `PythonScriptStep` : 

```python

train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    inputs=[iris_dataset.as_named_inputs('iris').as_mount()]
)
```

Olyan metódusokat is használhat, mint `random_split()` például `take_sample()` a és a több bemenet létrehozása vagy a folyamat lépéséhez átadott adatok mennyiségének csökkentése:

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

### <a name="access-datasets-within-your-script"></a>A parancsfájlban lévő adatkészletek elérése

A folyamat lépéseit tartalmazó parancsfájl elnevezett bemenetei szótárként érhetők el az `Run` objektumon belül. Kérje le az aktív `Run` objektumot a használatával `Run.get_context()` , majd kérje le az elnevezett bemenetek szótárát a használatával `input_datasets` . Ha az argumentum helyett az argumentumot adta meg, akkor a `DatasetConsumptionConfig` `arguments` `inputs` kód használatával férhet hozzá az adataihoz `ArgParser` . Mindkét módszert az alábbi kódrészlet mutatja be.

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

Az átadott érték lesz az adatkészlet-fájl (ok) elérési útja.

Közvetlenül is elérhető a regisztráció `Dataset` . Mivel a regisztrált adatkészletek állandók és megosztottak a munkaterületen, közvetlenül is lekérheti őket:

```python
run = Run.get_context()
ws = run.experiment.workspace
ds = Dataset.get_by_name(workspace=ws, name='mnist_opendataset')
```

## <a name="use-outputfiledatasetconfig-for-intermediate-data"></a>`OutputFileDatasetConfig`A köztes adatkezeléshez használatos

Míg `Dataset` az objektumok csak állandó adatokat képviselnek, [`OutputFileDatasetConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.outputfiledatasetconfig?view=azure-ml-py) az objektum (ok) a folyamat lépései **és** az állandó kimeneti adatok ideiglenes adatkimenetéhez használható. 

 `OutputFileDatasetConfig` az objektum alapértelmezett viselkedése a munkaterület alapértelmezett adattárára való írás. Adja át az `OutputFileDatasetConfig` objektumait a `PythonScriptStep` `arguments` paraméterrel.

```python
from azureml.data import OutputFileDatasetConfig
dataprep_output = OutputFileDatasetConfig()
input_dataset = Dataset.get_by_name(workspace, 'raw_data')

dataprep_step = PythonScriptStep(
    name="prep_data",
    script_name="dataprep.py",
    compute_target=cluster,
    arguments=[input_dataset.as_named_input('raw_data').as_mount(), dataprep_output]
    )
```

Dönthet úgy, hogy a Futtatás végén feltölti az `OutputFileDatasetConfig` objektum tartalmát. Ebben az esetben használja a `as_upload()` függvényt az `OutputFileDatasetConfig` objektummal együtt, és adja meg, hogy felülírja-e a meglévő fájlokat a célhelyen. 

```python
#get blob datastore already registered with the workspace
blob_store= ws.datastores['my_blob_store']
OutputFileDatasetConfig(name="clean_data", destination=blob_store).as_upload(overwrite=False)
```

### <a name="use-outputfiledatasetconfig-as-outputs-of-a-training-step"></a>Használat `OutputFileDatasetConfig` egy képzési lépés kimenete

A folyamaton belül a `PythonScriptStep` program argumentumai segítségével kérheti le a rendelkezésre álló kimeneti útvonalakat. Ha ez a lépés az első, és inicializálja a kimeneti adatokat, létre kell hoznia a könyvtárat a megadott elérési úton. Ezután megírhatja, hogy milyen fájlokat szeretne foglalni a alkalmazásban `OutputFileDatasetConfig` .

```python
parser = argparse.ArgumentParser()
parser.add_argument('--output_path', dest='output_path', required=True)
args = parser.parse_args()

# Make directory for file
os.makedirs(os.path.dirname(args.output_path), exist_ok=True)
with open(args.output_path, 'w') as f:
    f.write("Step 1's output")
```

### <a name="read-outputfiledatasetconfig-as-inputs-to-non-initial-steps"></a>Olvasási `OutputFileDatasetConfig` bemenetként a nem kezdeti lépésekhez

Miután a kezdeti folyamat lépése adatokat ír az `OutputFileDatasetConfig` elérési útra, és ennek a kezdeti lépésnek a kimenete lesz, egy későbbi lépéshez bemenetként is használható. 

A következő kódban 

* `step1_output_data` azt jelzi, hogy a PythonScriptStep kimenete `step1` a ADLS Gen 2 adattárba íródik a `my_adlsgen2` feltöltési hozzáférési módban. További információ a szerepkör- [engedélyek beállításáról](how-to-access-data.md#azure-data-lake-storage-generation-2) : az adat visszaírása ADLS Gen 2 adattárba. 

* `step1`A befejezést követően a kimenet a által jelzett célhelyre íródik `step1_output_data` , majd a 2. lépés készen áll a `step1_output_data` bemenet használatára. 

```python
# get adls gen 2 datastore already registered with the workspace
datastore = workspace.datastores['my_adlsgen2']
step1_output_data = OutputFileDatasetConfig(name="processed_data", destination=datastore).as_upload()

step1 = PythonScriptStep(
    name="generate_data",
    script_name="step1.py",
    runconfig = aml_run_config,
    arguments = ["--output_path", step1_output_data]
)

step2 = PythonScriptStep(
    name="read_pipeline_data",
    script_name="step2.py",
    compute_target=compute,
    runconfig = aml_run_config,
    arguments = ["--pd", step1_output_data.as_input]

)

pipeline = Pipeline(workspace=ws, steps=[step1, step2])
```

## <a name="register-outputfiledatasetconfig-objects-for-reuse"></a>Objektumok regisztrálása `OutputFileDatasetConfig` újbóli használatra

Ha a `OutputFileDatasetConfig` kísérlet időtartama alatt továbbra is elérhetővé szeretné tenni a rendelkezésre állást, regisztrálja azt a munkaterületen a kísérletek közötti megosztáshoz és újrafelhasználáshoz.

```python
step1_output_ds = step1_output_data.register_on_complete(name='processed_data', 
                                                         description = 'files from step1`)
```

## <a name="next-steps"></a>További lépések

* [Azure Machine learning-adatkészlet létrehozása](how-to-create-register-datasets.md)
* [Gépi tanulási folyamatokat hozhat létre és futtathat Azure Machine Learning SDK-val](how-to-create-your-first-pipeline.md)
