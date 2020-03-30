---
title: Betanítás azureml-adatkészletekkel
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatja az adatkészleteket a betanítássorán
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 03/09/2020
ms.openlocfilehash: 401383f2d483836bf725051810d78167869f7b22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283497"
---
# <a name="train-with-datasets-in-azure-machine-learning"></a>Betanítás adatkészletekkel az Azure Machine Learningben
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebben a cikkben megtudhatja, hogy az [Azure Machine Learning-adatkészletek](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py) egy távoli kísérletben betanítási folyamat két módon használható anélkül, hogy aggódnia kellene a kapcsolati karakterláncok vagy az adatelérési utak miatt.

- 1. lehetőség: Ha strukturált adatokkal rendelkezik, hozzon létre egy táblázatos adatkészletet, és használja közvetlenül a betanítási parancsfájlban.

- 2. lehetőség: Ha strukturálatlan adatokkal rendelkezik, hozzon létre egy FileDataset-et, és csatlakoztassa vagy töltse le a fájlokat egy távoli számításra a betanításhoz.

Az Azure Machine Learning adatkészletei zökkenőmentes integrációt biztosítanak az Azure Machine Learning képzési termékeivel, például [a ScriptRun,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrun?view=azure-ml-py) [estimator,](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) [HyperDrive](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive?view=azure-ml-py) és [Azure Machine Learning-folyamatokkal.](how-to-create-your-first-pipeline.md)

## <a name="prerequisites"></a>Előfeltételek

Adatkészletek létrehozásához és betanításához a következőkre van szükség:

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy ingyenes fiókot. Próbálja ki még ma [az Azure Machine Learning ingyenes vagy fizetős verzióját.](https://aka.ms/AMLFree)

* Egy [Azure Machine Learning-munkaterület.](how-to-manage-workspace.md)

* Az [Azure Machine Learning SDK python telepítve,](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)amely tartalmazza az azureml-adatkészletek csomag.

> [!Note]
> Egyes adatkészlet-osztályok függőséget az [azureml-dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) csomag. A Linux-felhasználók számára ezek az osztályok csak a következő disztribúciókon támogatottak: Red Hat Enterprise Linux, Ubuntu, Fedora és CentOS.

## <a name="option-1-use-datasets-directly-in-training-scripts"></a>1. lehetőség: Adatkészletek használata közvetlenül a betanítási parancsfájlokban

Ebben a példában hozzon létre egy [táblázatosdataset,](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) és `estimator` használja azt közvetlen bemenetként az objektum betanításához. 

### <a name="create-a-tabulardataset"></a>Táblázatos adatkészlet létrehozása

A következő kód létrehoz egy nem regisztrált tabularDataset egy webes URL-t. Az adattárakban helyi fájlokból vagy elérési utakból is létrehozhat adatkészleteket. További információ [az adatkészletek létrehozásáról.](https://aka.ms/azureml/howto/createdatasets)

```Python
from azureml.core.dataset import Dataset

web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)
```

### <a name="access-the-input-dataset-in-your-training-script"></a>A bemeneti adatkészlet elérése a betanítási parancsfájlban

A TabularDataset objektumok lehetővé teszik az adatok panda vagy szikraDataFrame betöltését, így ismerős adat-előkészítési és -betanítási kódtárakkal dolgozhat. Ezt a funkciót kihasználva átadhat egy tabularDataset-et a betanítási konfiguráció bemeneteként, majd lekérheti azt a parancsfájlban.

Ehhez a bemeneti adatkészlet eléréséhez [`Run`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py) az objektumon keresztül [`to_pandas_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) a betanítási parancsfájlban, és használja a módszert. 

```Python
%%writefile $script_folder/train_titanic.py

from azureml.core import Dataset, Run

run = Run.get_context()
# get the input dataset by name
dataset = run.input_datasets['titanic']
# load the TabularDataset to pandas DataFrame
df = dataset.to_pandas_dataframe()
```

### <a name="configure-the-estimator"></a>A becslés beállítása

A [program egy becslő](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) objektumot használ a kísérletfuttatásának elküldésére. Az Azure Machine Learning előre konfigurált becsléseket a közös gépi tanulási keretrendszerek, valamint egy általános estimator.

Ez a kód egy általános `est`becslő objektumot hoz létre, amely

* Parancsfájlkönyvtár a parancsfájlok számára. Az ebben a könyvtárban található összes fájl fel lesz töltve a fürtcsomópontokra végrehajtás céljából.
* A betanítási parancsfájl, *train_titanic.py*.
* A betanításbemeneti `titanic`adatkészlete , . `as_named_input()`szükséges, hogy a bemeneti adatkészlet a hozzárendelt név vel hivatkozhat a betanítási parancsfájlban. 
* A kísérlet számítási célja.
* A kísérlet környezetdefiníciója.

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


## <a name="option-2--mount-files-to-a-remote-compute-target"></a>2. lehetőség: Fájlok csatlakoztatása távoli számítási célhoz

Ha azt szeretné, hogy az adatfájlok elérhetők legyenek a számítási célon a betanításhoz, a [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) segítségével csatlakoztathatja vagy letöltheti az általa hivatkozott fájlokat.

### <a name="mount-vs-download"></a>Mount vs. Letöltés

Bármilyen formátumú fájlok csatlakoztatása vagy letöltése támogatott az Azure Blob storage, az Azure Files, az Azure Data Lake Storage Gen1, az Azure Data Lake Storage Gen2, az Azure SQL Database és az Azure Database for PostgreSQL által létrehozott adatkészletek esetében. 

Adatkészlet csatlakoztatásakor csatolja az adatkészlet által hivatkozott fájlokat egy könyvtárhoz (csatlakoztatási pont), és elérhetővé teszi a számítási cél. A csatlakoztatás linuxos számítási célokra, például az Azure Machine Learning Compute-ra, a virtuális gépekre és a HDInsightra támogatott. Adatkészlet letöltésekor az adatkészlet által hivatkozott összes fájl letöltődik a számítási célra. A letöltés minden számítási típusesetében támogatott. 

Ha a parancsfájl feldolgozza az adatkészlet által hivatkozott összes fájlt, és a számítási lemez elfér a teljes adatkészletszámára, a letöltés ajánlott, hogy elkerülje a tárolási szolgáltatásokból származó adatfolyam-adatok terhelését. Ha az adatméret meghaladja a számítási lemez méretét, a letöltés nem lehetséges. Ebben a forgatókönyvben azt javasoljuk, szerelés, mivel csak a parancsfájl által használt adatfájlok betöltése a feldolgozás időpontjában.

A következő kód `dataset` az ideiglenes könyvtárhoz`mounted_path`

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

### <a name="create-a-filedataset"></a>Fájladathalmaz létrehozása

A következő példa létrehoz egy nem regisztrált FileDataset webes URL-címek. További információ a más forrásokból származó [adatkészletek létrehozásáról.](https://aka.ms/azureml/howto/createdatasets)

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

### <a name="configure-the-estimator"></a>A becslés beállítása

Az adatkészlet áthaladása `inputs` mellett a paraméter a becslési, is `script_params` átadhatja az adatkészletet, és az adatelérési út (szerelési pont) a betanítási parancsfájl argumentumok on keresztül. Így a betanítási parancsfájl tartjuk a betanítási parancsfájl független azureml-sdk. Más szóval, akkor képes lesz ugyanazt a betanítási parancsfájlt a helyi hibakeresés és a távoli képzés bármely felhőplatformon.

Egy [SKLearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) becslésobjektum a scikit-learn kísérletek futtatásának elküldéséhez használatos. Tudjon meg többet az [SKlearn becsléssel.](how-to-train-scikit-learn.md)

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

### <a name="retrieve-the-data-in-your-training-script"></a>A betanítási parancsfájlban lévő adatok beolvasása

A futtatás elküldése után az `mnist` adatkészlet által hivatkozott adatfájlok a számítási célhoz lesznek csatlakoztatva. A következő kód bemutatja, hogyan lehet beolvasni a parancsfájlban lévő adatokat.

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

## <a name="notebook-examples"></a>Példák jegyzetfüzetre

Az [adatkészlet-jegyzetfüzetek](https://aka.ms/dataset-tutorial) bemutatják és kibontják a jelen cikkben szereplő fogalmakat.

## <a name="next-steps"></a>További lépések

* [Automatikus betanítási gépi tanulási modellek](how-to-auto-train-remote.md) táblázatos adatkészletekkel

* [Képbesorolási modellek betanítása](https://aka.ms/filedataset-samplenotebook) FileDatasets-szel

* [Betanítás adatkészletekkel csővezetékek használatával](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/pipeline-with-datasets/pipeline-for-image-classification.ipynb)
