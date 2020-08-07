---
title: Betanítás azureml-adatkészletekkel
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatók az adatkészletek a képzésben
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 07/31/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: acd030d8108ef3983be29fe85de6d7b3caf620af
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87849335"
---
# <a name="train-with-datasets-in-azure-machine-learning"></a>Betanítás Azure Machine Learning-adatkészletekkel
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebből a cikkből megtudhatja, hogyan dolgozhat [Azure Machine learning adatkészletekkel](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py) a betanítási kísérletekben.  Az adatkészleteket a helyi vagy távoli számítási célhelyen is használhatja, és nem kell aggódnia a kapcsolatok karakterláncai vagy az adatelérési utak miatt.

Azure Machine Learning adatkészletek zökkenőmentes integrációt biztosítanak Azure Machine Learning képzési termékekkel, például a [ScriptRun](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrun?view=azure-ml-py), a [kalkulátorsal](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py), a [HyperDrive](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive?view=azure-ml-py) és a [Azure Machine learning folyamatokkal](how-to-create-your-first-pipeline.md).

## <a name="prerequisites"></a>Előfeltételek

Az adatkészletek létrehozásához és betanításához a következők szükségesek:

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy ingyenes fiókot. Próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

* Egy [Azure Machine learning munkaterület](how-to-manage-workspace.md).

* A [Azure Machine learning SDK for Python telepítve](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), amely tartalmazza a azureml-adatkészletek csomagot.

> [!Note]
> Egyes adatkészlet-osztályok függőségei vannak a [azureml-adatelőkészítés](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) csomagon. A Linux-felhasználók esetében ezek az osztályok csak a következő disztribúciókban támogatottak: Red Hat Enterprise Linux, Ubuntu, Fedora és CentOS.

## <a name="access-and-explore-input-datasets"></a>Bemeneti adatkészletek elérése és megismerése

Egy meglévő TabularDataset a munkaterületen található kísérlet betanítási parancsfájljában férhet hozzá, és betöltheti az adatkészletet egy Panda dataframe a helyi környezet további feltárása érdekében.

A következő kód a [`get_context()`]() osztály metódusát használja a [`Run`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py) meglévő bemeneti TabularDataset eléréséhez `titanic` a betanítási parancsfájlban. Ezután a [`to_pandas_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) metódus használatával tölti be az adatkészletet egy Panda dataframe, hogy a képzés előtt további adatfeltárást és előkészítést lehessen készíteni.

> [!Note]
> Ha az eredeti adatforrás NaN, üres karakterláncokat vagy üres értékeket tartalmaz, akkor a to_pandas_dataframe () használatakor ezek az értékek *Null* értékként lesznek lecserélve. 

```Python
%%writefile $script_folder/train_titanic.py

from azureml.core import Dataset, Run

run = Run.get_context()
# get the input dataset by name
dataset = run.input_datasets['titanic']

# load the TabularDataset to pandas DataFrame
df = dataset.to_pandas_dataframe()
```

Ha be kell töltenie az előkészített adatokat egy új adatkészletbe a memóriában lévő pandák dataframe, írja be az adatokat egy helyi fájlba, például egy parkettát, és hozzon létre egy új adatkészletet a fájlból. Létrehozhat adatkészleteket helyi fájlokból vagy adattárolók elérési útjaiból is. További információ az [adatkészletek létrehozásáról](how-to-create-register-datasets.md).

## <a name="use-datasets-directly-in-training-scripts"></a>Adatkészletek közvetlen használata a betanítási szkriptekben

Ha strukturált adatokat még nem regisztrált adatkészletként, hozzon létre egy TabularDataset, és közvetlenül a helyi vagy távoli kísérlethez használja a betanítási parancsfájlban.

Ebben a példában létre kell hoznia egy nem regisztrált [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) , és közvetlenül bemenetként kell használni az `estimator` objektum számára a képzéshez. Ha a munkaterületen más kísérletekkel szeretné használni ezt a TabularDataset, tekintse meg az [adatkészletek munkaterületre való regisztrálásával](how-to-create-register-datasets.md#register-datasets)foglalkozó témakört.

### <a name="create-a-tabulardataset"></a>TabularDataset létrehozása

A következő kód létrehoz egy nem regisztrált TabularDataset egy webes URL-címről.  

```Python
from azureml.core.dataset import Dataset

web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)
```

A TabularDataset-objektumok lehetővé teszik a TabularDataset lévő adat betöltését egy pandák vagy Spark-DataFrame, hogy az ismerős adat-előkészítési és-betanítási könyvtárakkal is működjön, anélkül, hogy el kellene hagyni a notebookot. A képesség kihasználása érdekében tekintse meg a [bemeneti adatkészletek elérését és megismerését](#access-and-explore-input-datasets)ismertető témakört.

### <a name="configure-the-estimator"></a>A kalkulátor konfigurálása

A kísérlet futtatásának elküldéséhez egy [kalkulátor](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) objektumot kell használni. A Azure Machine Learning előre konfigurált becslések rendelkezik a gyakori gépi tanulási keretrendszerekhez, valamint egy általános becsléshez is.

Ez a kód egy általános kalkulátor-objektumot hoz létre, `est` amely megadja a

* A parancsfájlok parancsfájl-könyvtára. Az ebben a könyvtárban található összes fájl fel lesz töltve a fürtcsomópontokra végrehajtás céljából.
* A betanítási parancsfájl, *train_titanic.*
* A betanításhoz használt bemeneti adatkészlet `titanic_ds` . `as_named_input()`azért van szükség, hogy a bemeneti adatkészletet a betanítási parancsfájlban lévő hozzárendelt név is hivatkozhat `titanic` . 
* A kísérlet számítási célja.
* A kísérlet környezeti definíciója.

```Python
est = Estimator(source_directory=script_folder,
                entry_script='train_titanic.py',
                # pass dataset object as an input with name 'titanic'
                inputs=[titanic_ds.as_named_input('titanic')],
                compute_target=compute_target,
                environment_definition= conda_env)

# Submit the estimator as part of your experiment run
experiment_run = experiment.submit(est)
experiment_run.wait_for_completion(show_output=True)
```

## <a name="mount-files-to-remote-compute-targets"></a>Fájlok csatlakoztatása távoli számítási célokhoz

Ha strukturálatlan adatokkal rendelkezik, hozzon létre egy [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset?view=azure-ml-py) , és csatlakoztassa vagy töltse le az adatfájlokat, hogy azok elérhetők legyenek a távoli számítási cél számára a képzéshez. Ismerje meg, hogy mikor kell használni a [csatlakoztatást és a letöltést](#mount-vs-download) a távoli betanítási kísérletekhez. 

A következő példa létrehoz egy FileDataset, és csatlakoztatja az adatkészletet a számítási célhoz úgy, hogy a kiképzéshez a kalkulátorban argumentumként adja át. 

### <a name="create-a-filedataset"></a>FileDataset létrehozása

A következő példa egy nem regisztrált FileDataset hoz létre a webes URL-címekről. További információ az [adathalmazok](https://aka.ms/azureml/howto/createdatasets) más forrásokból való létrehozásáról.

```Python
from azureml.core.dataset import Dataset

web_paths = [
            'http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz'
            ]
mnist_ds = Dataset.File.from_files(path = web_paths)
```

### <a name="configure-the-estimator"></a>A kalkulátor konfigurálása

Javasoljuk, hogy az adatkészletet argumentumként adja át a csatlakoztatáskor. Az adatkészletnek a kalkulátorban található paraméterrel való átadása mellett az adatkészletet átadhatja a `inputs` `script_params` (z) és az adatelérési utat (beszerelési pont) a betanítási szkriptben argumentumokkal. Így ugyanazt a betanítási szkriptet fogja használni a helyi hibakereséshez és a távoli oktatáshoz bármilyen felhőalapú platformon.

A [SKLearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) -kalkulátor objektum a scikit-Learn kísérletek futtatásának elküldésére szolgál. Miután elküldte a futtatást, az adatkészlet által hivatkozott adatfájlok a `mnist` számítási célhoz lesznek csatlakoztatva. További információ a [SKlearn-kalkulátor](how-to-train-scikit-learn.md)betanításáról.

```Python
from azureml.train.sklearn import SKLearn

script_params = {
    # mount the dataset on the remote compute and pass the mounted path as an argument to the training script
    '--data-folder': mnist_ds.as_named_input('mnist').as_mount(),
    '--regularization': 0.5
}

est = SKLearn(source_directory=script_folder,
              script_params=script_params,
              compute_target=compute_target,
              environment_definition=env,
              entry_script='train_mnist.py')

# Run the experiment
run = experiment.submit(est)
run.wait_for_completion(show_output=True)
```

### <a name="retrieve-the-data-in-your-training-script"></a>A betanítási parancsfájlban szereplő adatlekérdezés

A következő kód bemutatja, hogyan kérheti le az adatait a parancsfájlban.

```Python
%%writefile $script_folder/train_mnist.py

import argparse
import os
import numpy as np
import glob

from utils import load_data

# retrieve the 2 arguments configured through script_params in estimator
parser = argparse.ArgumentParser()
parser.add_argument('--data-folder', type=str, dest='data_folder', help='data folder mounting point')
parser.add_argument('--regularization', type=float, dest='reg', default=0.01, help='regularization rate')
args = parser.parse_args()

data_folder = args.data_folder
print('Data folder:', data_folder)

# get the file paths on the compute
X_train_path = glob.glob(os.path.join(data_folder, '**/train-images-idx3-ubyte.gz'), recursive=True)[0]
X_test_path = glob.glob(os.path.join(data_folder, '**/t10k-images-idx3-ubyte.gz'), recursive=True)[0]
y_train_path = glob.glob(os.path.join(data_folder, '**/train-labels-idx1-ubyte.gz'), recursive=True)[0]
y_test = glob.glob(os.path.join(data_folder, '**/t10k-labels-idx1-ubyte.gz'), recursive=True)[0]

# load train and test set into numpy arrays
X_train = load_data(X_train_path, False) / 255.0
X_test = load_data(X_test_path, False) / 255.0
y_train = load_data(y_train_path, True).reshape(-1)
y_test = load_data(y_test, True).reshape(-1)
```

## <a name="mount-vs-download"></a>Csatlakoztatás vs Letöltés

Az Azure Blob Storage, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database és Azure Database for PostgreSQL által létrehozott adatkészletek esetében bármilyen formátumú fájlok csatlakoztatása vagy letöltése támogatott. 

Adatkészlet **csatlakoztatásakor** az adatkészlet által hivatkozott fájlokat csatolja egy könyvtárhoz (csatlakoztatási ponthoz), és elérhetővé teszi azt a számítási célra. A csatlakoztatás Linux-alapú számításokhoz, többek között Azure Machine Learning számításokhoz, virtuális gépekhez és HDInsight támogatott. 

Adatkészlet letöltésekor a rendszer az adatkészlet által hivatkozott összes fájlt **letölti** a számítási célra. A letöltés minden számítási típus esetében támogatott. 

Ha a szkript feldolgozza az adatkészlet által hivatkozott összes fájlt, és a számítási lemez elfér a teljes adatkészletben, a letöltés javasolt a tárolási szolgáltatásokból származó adatok átvitelének elkerülése érdekében. Ha az adatok mérete meghaladja a számítási lemez méretét, a letöltés nem lehetséges. Ebben a forgatókönyvben javasoljuk a csatlakoztatást, mivel csak a parancsfájl által használt adatfájlok töltődnek be a feldolgozás során.

A következő kód csatlakoztatja `dataset` a TEMP könyvtárhoz:`mounted_path`

```python
import tempfile
mounted_path = tempfile.mkdtemp()

# mount dataset onto the mounted_path of a Linux-based compute
mount_context = dataset.mount(mounted_path)

mount_context.start()

import os
print(os.listdir(mounted_path))
print (mounted_path)
```

## <a name="access-datasets-in-your-script"></a>Adatkészletek elérése a parancsfájlban

A regisztrált adatkészletek helyileg és távolról is elérhetők a számítási fürtökön, például az Azure Machine Learning számítási feladatokhoz. A regisztrált adatkészlet kísérletek közötti eléréséhez használja a következő kódot a munkaterület és a regisztrált adatkészlet név szerinti eléréséhez. Alapértelmezés szerint az [`get_by_name()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--) osztályban lévő metódus a `Dataset` munkaterülethez regisztrált adatkészlet legújabb verzióját adja vissza.

```Python
%%writefile $script_folder/train.py

from azureml.core import Dataset, Run

run = Run.get_context()
workspace = run.experiment.workspace

dataset_name = 'titanic_ds'

# Get a dataset by name
titanic_ds = Dataset.get_by_name(workspace=workspace, name=dataset_name)

# Load a TabularDataset into pandas DataFrame
df = titanic_ds.to_pandas_dataframe()
```

## <a name="accessing-source-code-during-training"></a>Forráskód elérése a betanítás során

Az Azure Blob Storage nagyobb átviteli sebességgel rendelkezik, mint egy Azure-fájlmegosztás, és nagy mennyiségű, párhuzamosan elindított feladatra fog méretezni. Ezért javasoljuk, hogy a futtatások konfigurálásával blob Storage-t használjon a forráskód-fájlok átviteléhez.

A következő kódrészlet a futtatási konfigurációt adja meg, amely a blob-adatforgalomhoz használandó blob-adattár.

```python 
# workspaceblobstore is the default blob storage
run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="notebook-examples"></a>Jegyzetfüzet-példák

Az [adatkészlet jegyzetfüzetei](https://aka.ms/dataset-tutorial) bemutatják és kibővítik az ebben a cikkben szereplő fogalmakat.

## <a name="next-steps"></a>Következő lépések

* [Gépi tanulási modellek automatikus tanítása](how-to-auto-train-remote.md) a TabularDatasets.

* [Képosztályozási modellek betanítása](https://aka.ms/filedataset-samplenotebook) a FileDatasets.

* [Adatkészletek betanítása a folyamatok használatával](how-to-create-your-first-pipeline.md).
